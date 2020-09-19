# 第3章：Todo API

在接下来的两章中，我们将构建一个Todo API后端，然后将其与React前端连接。 我们已经制作了第一个API，并回顾了HTTP和REST的抽象工作原理，但是您仍然可能还没有“完全”了解它们如何结合在一起。 在这两章的最后，您将学到。

由于我们要制作专用的后端和前端，因此我们会将代码分成相似的结构。 在现有代码目录中，我们将创建一个todo目录，其中包含我们的后端Django Python代码和我们的前端React JavaScript代码。

最终的布局将如下所示。

```python
todo
|  ├──frontend
|      ├──React... 
|  ├──backend
|      ├──Django...
```

本章重点介绍后端，而第4章介绍前端。



## 初始化创建

任何Django API的第一步始终是安装Django，然后在其之上添加Django REST Framework。 首先在桌面上的代码目录中创建一个专用的todo目录。

打开一个新的命令行控制台，然后输入以下命令：

```shell
$ cd ~/Desktop
$ cd code
$ mkdir todo && cd todo
```

**注意：**确保已从上一章中停用了虚拟环境。 您可以通过键入退出来执行此操作。 命令行前面是否没有括号？ 好。 那么您就不在现有的虚拟环境中。

在此todo文件夹中将是我们的后端和前端目录。 让我们创建一个后端文件夹，安装Django，然后激活一个新的虚拟环境。

```shell
$ mkdir backend && cd backend 
$ pipenv install django==2.2.6 
$ pipenv shell
```

您应该在命令行上看到括号，以确认虚拟环境（后端）已激活。

现在已经安装了Django，我们应该首先创建一个传统的Django项目todo_project，在其中添加第一个应用程序todo，然后迁移初始数据库。

```shell
(backend) $ django-admin startproject todo_project . (backend) $ python manage.py startapp todos 
(backend) $ python manage.py migrate
```

在Django中，我们始终需要将新应用添加到INSTALLED_APPS设置中，所以现在就这样做。 在文本编辑器中打开todo_project / settings.py。 在文件底部，添加`todos.apps.TodosConfig`。

```python
# todo_project/settings.py
INSTALLED_APPS = [ 
    'django.contrib.admin', 
    'django.contrib.auth', 
    'django.contrib.contenttypes',
    'django.contrib.sessions', 
    'django.contrib.messages',
    'django.contrib.staticfiles',
  
    # Local
    'todos.apps.TodosConfig', # new 
]
```

如果您现在在命令行上运行`python manage.py runserver`并导航至http://127.0.0.1:8000/，则可以看到我们的项目已成功安装。

![image-20200916015409343](https://i.loli.net/2020/09/16/w4sOEM3aZfydhq9.png)

准备开始吧！



## Models

接下来是在todos应用程序中定义我们的Todo数据库模型。 我们将保持基本状态，只有两个字段：title和body。

```python
# todos/models.py
from django.db import models


class Todo(models.Model):
    title = models.CharField(max_length=200) 
    body = models.TextField()
    
    def __str__(self): 
        return self.title
```

我们在顶部导入模型，然后将其子类化以创建自己的Todo模型。 我们还添加了__str__方法，以为每个将来的模型实例提供易于理解的名称。

由于我们已经更新了模型，现在该是Django进行两步操作的时候了：制作一个新的迁移文件，然后每次将数据库与更改同步。 在命令行上，键入Control + c以停止我们的本地服务器。 然后运行以下两个命令：

```shell
(backend) $ python manage.py makemigrations todos 
(backend) $ python manage.py migrate
```

可以选择添加我们要为其创建迁移文件的特定应用程序（我们可以只键入`python manage.py makemigrations`），但这是采用的最佳做法。 迁移文件是调试应用程序的一种绝妙方法，您应该努力为每个小的更改创建一个迁移文件。 如果我们在两个不同的应用程序中更新了模型，然后运行`python manage.py makemigrations`，则生成的单个迁移文件将包含两个应用程序中的数据。 这只会增加调试难度。 尝试使您的迁移尽可能小。

现在，我们可以使用内置的Django管理应用程序与我们的数据库进行交互。 如果我们立即进入管理员，我们的Todos应用程序将不会出现。 我们需要通过todos / admin.py文件显式添加它，如下所示。

```python
# todos/admin.py
from django.contrib import admin

from .models import Todo

admin.site.register(Todo)
```

而已！ 现在，我们可以创建一个超级用户帐户来登录管理员。

```shell
(backend) $ python manage.py createsuperuser
```

然后再次启动本地服务器：

```shell
(backend) $ python manage.py runserver
```

现在，如果您浏览至http://127.0.0.1:8000/admin/，则可以登录。



![image-20200916121310810](https://i.loli.net/2020/09/16/gouv2NwUVBmLlph.png)

单击Todos旁边的“ +Add”，并创建3个新的待办事项，并确保为两者添加标题和正文。 这是我的样子：

![image-20200916121429636](https://i.loli.net/2020/09/16/vOCFowAfJG5rIYL.png)

至此，我们实际上已经完成了Todo API的传统Django部分。 由于我们不必为该项目建立网页，因此不需要网站URL，视图或模板。 我们需要的只是一个模型，而Django REST Framework将负责其余的工作。



## Dajngo REST 框架

停止本地服务器Control + c，然后通过pipenv安装Django REST Framework。

```shell
(backend) $ pipenv install djangorestframework==3.10.3
```

然后像其他任何第三方应用程序一样，将rest_framework添加到我们的INSTALLED_APPS设置中。 我们还希望开始配置所有REST_FRAMEWORK下存在的Django REST Framework特定设置。 首先，我们将权限明确设置为AllowAny。 此行位于文件的底部。

```python
# todo_project/settings.py
INSTALLED_APPS = [
    'django.contrib.admin', 
    'django.contrib.auth', 
    'django.contrib.contenttypes',
    'django.contrib.sessions', 
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # 3rd party 
    'rest_framework',  # new
  
    # Local
    'todos.apps.TodosConfig', 
]

# new
REST_FRAMEWORK = { 
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny', 
    ]
}
```

Django REST Framework包含冗长的隐式设置默认设置列表。 您可以在此处查看完整列表。 AllowAny是其中之一，这意味着当我们像上面所做的那样显式设置它时，其效果与没有设置DEFAULT_PERMISSION_CLASSES的配置完全相同。

学习默认设置需要花费一些时间。 在本书学习过程中，我们将对其中的一些熟悉。 要记住的主要内容是，隐式默认设置的设计旨在使开发人员可以进入并开始在本地开发环境中快速工作。 但是，默认设置不适用于生产。 因此，通常我们会在项目过程中对它们进行一些更改。

好的，这样就安装了Django REST Framework。 接下来是什么？
与上一章中我们同时构建网页和API的Library项目不同，在这里我们仅构建API。 因此，我们不需要创建任何模板文件或传统的Django视图。

相反，我们将更新三个特定于Django REST框架的文件，以将数据库模型转换为Web API：urls.py，views.py和serializers.py。



## URLs

我喜欢先从URL开始，因为它们是我们API端点的入口点。 就像在传统的Django项目中一样，urls.py文件使我们可以配置路由。

从Django项目级文件todo_project / urls.py开始。 我们在第二行导入include，并在api /为我们的todos应用添加一条路线。

```python
# todo_project/urls.py
from django.contrib import admin
from django.urls import include, path  # new

urlpatterns = [
    path('admin/', admin.site.urls),  
    path('api/', include('todos.urls')), # new
]
```

接下来，创建我们的应用程序级别的todos / urls.py文件。

```shell
(backend) $ touch todos/urls.py
```

并使用以下代码对其进行更新。

```python
# todos/urls.py
from django.urls import path
from .views import ListTodo, DetailTodo

urlpatterns = [
    path('<int:pk>/', DetailTodo.as_view()), 
    path('', ListTodo.as_view()),
]
```

请注意，我们引用的还有两个尚未创建的视图：ListTodo和DetailTodo。 但是，路由现已完成。 `api/`有所有待办事项的列表位于空字符串 `''`，即。 每个待办事项都将在其主键上可用，这是Django在每个数据库表中自动设置的值。 第一个条目是1，第二个条目是2，依此类推。 因此，我们的第一个待办事项最终将位于API端点`api/1/`。



##  Serializers

让我们回顾一下到目前为止。 我们从一个传统的Django项目和应用程序开始，我们创建了数据库模型并添加了数据。 然后，我们安装了Django REST Framework并配置了URL。 现在，我们需要将模型中的数据转换为将在URL输出的JSON。 因此，我们需要一个序列化器。

Django REST Framework附带了一个强大的内置序列化程序类，我们可以使用少量代码快速扩展它们。 这就是我们在这里要做的。

首先在todos应用中创建一个新的serializers.py文件。

```shell
(backend) $ touch todos/serializers.py
```

然后更新代码，如下所示：

```python
# todos/serializers.py
from rest_framework import serializers 
from .models import Todo


class TodoSerializer(serializers.ModelSerializer): 
    class Meta:
        model = Todo
        fields = ('id', 'title', 'body',)
```

在顶部，我们从Django REST Framework以及我们的models.py文件导入了序列化器。 接下来，我们创建一个类TodoSerializer。 这里的格式与我们在Django本身中创建模型类或表单的方式非常相似。 我们正在指定要使用的模型以及我们要公开的特定字段。 请记住，id是Django自动创建的，因此我们不必在Todo模型中定义它，但是我们将在细节视图中使用它。

就是这样。 Django REST Framework现在将神奇地将我们的数据转换为JSON，从而公开来自Todo模型的id，title和body字段。

我们需要做的最后一件事是配置我们的views.py文件。



## Views

在传统的Django中，视图用于自定义要发送到模板的数据。 在Django REST Framework中，视图执行相同的操作，但对序列化的数据而言。

Django REST Framework视图的语法故意与常规Django视图非常相似，就像常规Django一样，Django REST Framework随附了通用视图以用于常见用例。 这就是我们在这里使用的。

更新todos / views.py文件，如下所示：

```python
# todos/views.py
from rest_framework import generics

from .models import Todo
from .serializers import TodoSerializer


class ListTodo(generics.ListAPIView): 
    queryset = Todo.objects.all() 
    serializer_class = TodoSerializer

    
class DetailTodo(generics.RetrieveAPIView): 
    queryset = Todo.objects.all() 
    serializer_class = TodoSerializer
```

在顶部，我们导入Django REST Framework的泛型视图以及我们的models.py和serializers.py文件。

从我们的todos / urls.py文件中调用，我们有两条路线，因此有两个不同的视图。 我们将使用ListAPIView显示所有待办事项，并使用RetrieveAPIView显示单个模型实例。

精明的读者会注意到这里的代码有些冗余。 即使扩展的通用视图有所不同，我们实质上还是为每个视图重复使用queryset和serializer_class。 在本书的后面，我们将学习有关解决此问题的视图集和路由器，并允许我们使用更少的代码来创建相同的API视图和URL。

但是现在我们完成了！ 我们的API已准备就绪，可以使用。 如您所见，Django REST Framework和Django之间的唯一真正区别是，使用Django REST Framework，我们需要添加serializers.py文件，而无需模板文件。 否则，urls.py和views.py文件的行为类似。



##  Consuming the API

传统上使用API是一个挑战。 对于给定的HTTP响应或请求的正文和标头中包含的所有信息，根本没有很好的可视化效果。

取而代之的是，大多数开发人员使用命令行HTTP客户端（例如cURL）（如上一章所述）或HTTPie。

2012年，第三方软件产品Postman投放市场，现在全球有数百万希望通过可视化，功能丰富的方式与API交互的开发人员使用。

但是Django REST框架最令人惊奇的事情之一是，它附带了功能强大的可浏览API，我们可以立即使用它。 如果您发现需要使用API进行更多自定义，则可以使用Postman之类的工具。 但是通常内置的可浏览API绰绰有余。



##  Browsable API

现在让我们使用可浏览的API与我们的数据进行交互。 确保本地服务器正在运行。

```shell
(backend) $ python manage.py runserver
```

然后导航到http://127.0.0.1:8000/api/以查看我们的工作API列表视图端点。

![image-20200916123533730](https://i.loli.net/2020/09/16/o9LuZFfsak3rTbI.png)

该页面显示了我们先前在数据库模型中创建的三个待办事项。 API终结点称为集合，因为它显示多个项目。

我们的可浏览API可以做很多事情。 首先，让我们看一下原始的JSON视图，即实际通过互联网传输的视图。 单击右上角的“ GET”按钮，然后选择JSON。

![image-20200916123638726](https://i.loli.net/2020/09/16/cHMsDFLPCwtrmOE.png)

如果您返回http://127.0.0.1:8000/api/的列表视图页面，我们可以看到其他信息。 回想一下，HTTP动词GET用于读取数据，而POST用于更新或创建数据。

在“列表待办事项”下显示GET / api /，它告诉我们我们在此端点上执行了GET。 下方显示的是HTTP 200 OK，这是我们的状态代码，一切正常。 最重要的是，它下面显示允许：GET，HEAD，OPTIONS。 请注意，由于这是一个只读端点，因此不包括POST，我们只能执行GET。

我们还为每个模型制作了一个DetailTodo视图。 这称为实例，在http://127.0.0.1:8000/api/1/上可见。

![image-20200916123754421](https://i.loli.net/2020/09/16/4b8qT3z7x6vdRHV.png)

您也可以导航至以下端点：

- http://127.0.0.1:8000/api/2
- http://127.0.0.1:8000/api/3



## **CORS**

我们需要做的最后一步是跨源资源共享（CORS）。 每当客户端与不同域（mysite.com与yoursite.com）或端口（localhost：3000与localhost：8000）上托管的API进行交互时，都会存在潜在的安全问题。

具体来说，CORS要求服务器包含特定的HTTP标头，以允许客户端确定是否以及何时应允许跨域请求。

我们的Django API后端将与专用前端通信，该前端位于用于本地开发的不同端口上，并在部署后位于另一个域上。

处理此问题的最简单方法（以及Django REST框架建议的一种方法）是使用中间件，该中间件将根据我们的设置自动包括适当的HTTP标头。

我们将使用的软件包是django-cors-header，可以轻松将其添加到我们现有的项目中。

首先使用Control + c退出我们的服务器，然后使用Pipenv安装django-cors-headers。

```shell
(backend) $ pipenv install django-cors-headers==3.1.1
```

接下来，在三个位置更新我们的settings.py文件：

- 将`corsheaders`添加到INSTALLED_APPS
- 在MIDDLEWARE中把 `CorsMiddleware` 添加到 `CommonMiddleWare` 的上方
- 创建一个 `CORS_ORIGIN_WHITELIST`

```python
# todo_project/settings.py
INSTALLED_APPS = [ 
    'django.contrib.admin', 
    'django.contrib.auth', 
    'django.contrib.contenttypes',
    'django.contrib.sessions', 
    'django.contrib.messages', 
    'django.contrib.staticfiles',
  
    # 3rd party
    'rest_framework', 
    'corsheaders', # new
    # Local
    'todos.apps.TodosConfig', 
]

MIDDLEWARE = [    
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware', # new   
    'django.middleware.common.CommonMiddleware', 
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

# new
CORS_ORIGIN_WHITELIST = (
    'http://localhost:3000', 
    'http://localhost:8000',
)

```

将corsheaders.middleware.CorsMiddleware出现在正确的位置非常重要。 由于中间件是从上到下加载的，因此它位于MIDDLEWARE设置中的django.middleware.common.CommonMiddleware之上。 另外请注意，我们已将两个域列入白名单：localhost：3000和localhost：8000。 前者是React的默认端口，下一章将在前端使用它。 后者是默认的Django端口。



## 测试

您应该始终为Django项目编写测试。 前期花费的少量时间将为您节省大量的时间和以后的调试错误。 让我们添加两个基本测试，以确认标题和正文内容的行为符合预期。

打开todos / tests.py文件，并添加以下内容：

```python
# todos/tests.py
from django.test import TestCase 
from .models import Todo


class TodoModelTest(TestCase):
    @classmethod
    def setUpTestData(cls):
        Todo.objects.create(title='first todo', body='a body here')

    def test_title_content(self):
        todo = Todo.objects.get(id=1)
        expected_object_name = f'{todo.title}'
        self.assertEquals(expected_object_name, 'first todo')
    
    def test_body_content(self):
        todo = Todo.objects.get(id=1)
        expected_object_name = f'{todo.body}'
        self.assertEquals(expected_object_name, 'a body here')
```

它使用Django的内置TestCase类。 首先，我们在setUpTestData中设置数据，然后编写两个新测试。 然后使用python manage.py test命令运行测试。

```shell
(backend) $ python manage.py test 
...
Ran 2 tests in 0.002s


OK
```

就是这样！ 我们的后端现已完成。 确保服务器正在运行，因为我们将在下一章中使用它。

```shell
(backend) $ python manage.py runserver
```

## 总结

只需最少的代码，Django REST Framework便允许我们从头开始创建Django API。 我们从传统Django需要的唯一组件是models.py文件和urls.py路由。 views.py和serializers.py文件完全是Django REST Framework特有的。

与上一示例不同，我们没有为该项目构建任何网页，因为我们的目标只是创建一个API。 但是，在将来的任何时候，我们都可以轻松实现！ 只需添加一个新视图，URL和一个模板即可公开我们现有的数据库模型。

在此示例中，重要的一点是我们添加了CORS标头，并且仅将域localhost：3000和localhost：8000明确设置为可以访问我们的API。 第一次开始构建API时，很容易混淆正确设置CORS标头。

我们可以做更多的配置，以后再做，但最终，创建Django API的过程是建立模型，编写一些URL路由，然后添加Django REST Framework的序列化程序和视图所提供的一些魔术。

在下一章中，我们将构建一个React前端并将其连接到我们的Todo API后端。