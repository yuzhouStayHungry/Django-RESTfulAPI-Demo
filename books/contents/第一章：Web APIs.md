# 第二章：网站库和API

Django REST Framework与Django Web框架协同工作以创建Web API。 我们不能仅使用Django Rest Framework来构建Web API。 在安装和配置Django本身之后，必须始终将其添加到项目中。

在本章中，我们将回顾传统Django和Django REST Framework之间的异同。 最重要的一点是，Django创建的网站包含网页，而Django REST Framework创建的Web API是URL端点的集合，这些URL端点包含返回JSON的可用HTTP动词。

为了说明这些概念，我们将使用传统的Django建立一个基本的Library网站，然后使用Django REST Framework将其扩展为Web API。

确保您已经在计算机上安装了Python 3和Pipenv。 如果需要帮助，可以在这里找到完整的说明。



##  典型Django开发

首先，我们需要在计算机上有一个专用目录来存储代码。 它可以放置在任何地方，但为了方便起见，如果您使用的是Mac，我们可以将其放在“桌面”文件夹中。 位置真的没关系； 它只需要易于访问。

```shell
$ cd ~/Desktop
$ mkdir code && cd code
```

该代码文件夹将成为本书所有代码的位置。 下一步是为我们的库站点创建一个专用目录，通过Pipenv安装Django，然后使用shell命令进入虚拟环境。 您应该始终为每个新的Python项目使用专用的虚拟环境。

```shell
$ mkdir library && cd library
$ pipenv install django==2.2.6
$ pipenv shell
(library) $
```

Pipenv在当前目录中创建一个Pipfile和一个Pipfile.lock。 命令行前括号中的（library）表明我们的虚拟环境处于活动状态。

传统的Django网站由一个项目`project`和一个（或多个）代表不同功能的应用`apps`组成。 让我们使用startproject命令创建一个新项目。 别忘了加上句号。 最后将代码安装在当前目录中。 如果不包括句点，则Django默认会创建一个附加目录。

```shell
(library)$ django-admin startproject library_project .
```

Django会自动为我们生成一个新项目，我们可以使用tree命令看到它。 （注意：如果在Mac上无法使用tree，请使用Homebrew：`brew install tree`安装。）

```shell
(library) $ tree .
├── Pipfile
├── Pipfile.lock
├── library_project
│ ├── __init__.py
│ ├── settings.py
│ ├── urls.py
│ └── wsgi.py
└── manage.py
```

这些文件具有以下定义：

- `__init__.py`是将目录视为软件包的Python方法； 它是空的
- `settings.py`包含我们项目的所有配置
- `urls.py`控制顶级URL路由
- `wsgi.py`代表Web服务器网关界面，可帮助Django服务于
	最终网页
- `manage.py`执行各种Django命令，例如运行本地Web
	服务器或创建新应用。

运行`migrate`将数据库与Django的默认设置同步，然后启动本地Django Web服务器。

```shell
(library) $ python manage.py migrate
(library) $ python manage.py runserver
```

打开Web浏览器，访问http://127.0.0.1:8000/以确认我们的项目已成功安装。

![image-20200916015409343](https://i.loli.net/2020/09/16/w4sOEM3aZfydhq9.png)



## 第一个app

典型的下一步是开始添加代表功能不同区域的应用程序。 一个Django项目可以支持多个应用程序。

通过键入Control + c停止本地服务器，然后创建一个books应用程序。

```shell
(library) $ python manage.py startapp books
```

现在再查看一下Django生成了什么文件。

```shell
.
├── Pipfile
├── Pipfile.lock
├── books
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── library_project
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```

每个应用程序都有一个`__init__.py`文件，将其标识为Python软件包。 创建了6个新文件：

- `admin.py`是内置Django Admin应用程序的配置文件
- `apps.py`是应用程序本身的配置文件
- `migrations /`目录存储用于数据库更改的迁移文件
- `models.py`是我们定义数据库模型的地方
- `tests.py`用于我们的应用程序特定测试
- `views.py`是我们处理Web应用程序的请求/响应逻辑的地方

通常，开发人员还会在每个应用程序内创建一个urls.py文件进行路由。

让我们构建文件，以便我们的图书馆项目列出首页上的所有书籍。 将您选择的文本编辑器打开到settings.py文件。 第一步是将新应用添加到我们的INSTALLED_APPS配置中。 我们总是在底部添加新应用，因为Django会按顺序读取它们，并且我们希望内置的核心Django应用（例如admin和auth）在加载我们的应用之前已经被加载。

```python
# library_project/settings.py
INSTALLED_APPS = [ 
    'django.contrib.admin', 
    'django.contrib.auth', 
    'django.contrib.contenttypes', 
    'django.contrib.sessions', 
    'django.contrib.messages', 
    'django.contrib.staticfiles',
    
    # Local
    'books.apps.BooksConfig', # new
]
```

然后运行迁移以使我们的数据库与更改同步。

```shell
(library) $ python manage.py migrate
```

传统Django中的每个网页都需要多个文件：视图，URL和模板。 但是首先我们需要一个数据库模型，所以让我们从这里开始。



## Models

在您的文本编辑器中，打开文件book / models.py并进行如下更新：

```python
# books/models.py
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=250)
    subtitle = models.CharField(max_length=250) 
    author = models.CharField(max_length=100) 
    isbn = models.CharField(max_length=13)
    
    def __str__(self): 
        return self.title
```

这是一个基本的Django模型，我们在最上面一行从Django导入模型，然后创建一个扩展它的Book类。 有四个字段：标题，副标题，作者和isbn。 我们还包括__str__方法，以便稍后在管理员中显示书名。

请注意，ISBN是分配给每本出版书籍的唯一的13个字符的标识符。

由于我们创建了一个新的数据库模型，因此我们需要创建一个迁移文件来进行处理。 指定应用名称是可选的，但建议在此处使用。 我们可以只键入python manage.py makemigrations，但是如果有多个应用程序进行了数据库更改，那么这两个应用程序都将被添加到迁移文件中，这使得将来的调试更加困难。 保持您的迁移文件尽可能具体。

然后运行迁移以更新我们的数据库。

```shell
(library) $ python manage.py makemigrations books 
(library) $ python manage.py migrate
```

到目前为止，一切都很好。 



## Admin

我们可以开始通过内置的Django应用将数据输入到我们的新模型中。 但是我们必须首先做两件事：

创建一个超级用户帐户并更新admin.py，以便显示books应用程序。
从超级用户帐户开始。 在命令行上运行以下命令：

```shell
(library) $ python manage.py createsuperuser
```

按照提示输入用户名，电子邮件和密码。 请注意，出于安全原因，输入密码时屏幕上不会显示文本。

现在更新我们的图书应用的admin.py文件。

```python
# books/admin.py
from django.contrib import admin 
from .models import Book


admin.site.register(Book)
```

这就是我们所需要的！ 再次启动本地服务器。

```shell
(library) $ python manage.py runserver
```

导航到http://127.0.0.1:8000/admin并登录。

![image-20200916020819935](https://i.loli.net/2020/09/16/96ivdpkUtrKbwGV.png)

您将被重定向到管理员主页。

![image-20200916020903737](https://i.loli.net/2020/09/16/dlqmJ2MOgsL3TrQ.png)

单击书籍的链接。

![image-20200916020942554](https://i.loli.net/2020/09/16/ESy1Ls2CkX4vVpc.png)

然后点击右上角的“添加图书+”按钮。

![image-20200916021033382](https://i.loli.net/2020/09/16/DOIFNRKYxc1Zbwg.png)



我已经输入了Django初学者书籍的详细信息。 您可以在此处输入任何文本。 纯粹是出于演示目的。 单击“保存”按钮后，我们将重定向到列出所有当前条目的“书籍”页面。

![image-20200916021124065](https://i.loli.net/2020/09/16/g56DuVLamGvx1U9.png)

我们传统的Django项目现在有数据，但是我们需要一种将其公开为网页的方法。 这意味着创建视图，URL和模板文件。 现在开始吧。



## Views

views.py文件控制如何显示数据库模型内容。 由于我们要列出所有书籍，因此可以使用内置的通用类ListView。
更新books / views.py文件。

```python
# books/views.py
from django.views.generic import ListView

from .models import Book

class BookListView(ListView): 
    model = Book
    template_name = 'book_list.html'
```

首先，我们导入了ListView和Book模型。 然后，我们创建一个BookListView类，该类指定要使用的模型和模板（尚未创建）。

在拥有一个正常工作的网页之前，需要执行两个步骤：制作模板并配置URL。 让我们从URL开始。



## URLs

我们需要同时设置项目级别的urls.py文件，然后在books应用程序中设置一个。 用户访问我们的网站时，他们将首先与library_project / urls.py文件进行交互，因此，请先对其进行配置。

```python
# library_project/urls.py
from django.contrib import admin
from django.urls import path, include # new

urlpatterns = [
    path('admin/', admin.site.urls), 
    path('', include('books.urls')), # new
]
```

前两行会导入内置的管理应用程序，路线的路径，并包括将与我们的图书应用程序一起使用的内容。 如果用户转到/ admin /，他们将被重定向到admin应用。 我们在图书应用路由中使用空字符串''，这意味着首页上的用户将直接重定向到图书应用。

现在，我们可以配置我们的books / urls.py文件。 但是，糟糕！ Django由于某种原因在应用程序中默认不包含urls.py文件，因此我们需要自己创建它。

```shell
(library) $ touch books/urls.py
```

现在，在文本编辑器中更新新文件。

```python
# books/urls.py
from django.urls import path
from .views import BookListView

urlpatterns = [
    path('', BookListView.as_view(), name='home'),
]
```

我们导入视图文件，在空字符串''处配置BookListView，并添加命名URL主页作为最佳实践。

Django的工作方式，现在，当用户转到我们网站的主页时，他们将首先点击library_project / urls.py文件，然后将其重定向到使用BookListView指定的books / urls.py。 在此视图文件中，Book模型与ListView一起使用以列出所有书籍。

最后一步是创建我们的模板文件，以控制实际网页上的布局。 我们已经在视图中将其名称指定为book_list.html。 其位置有两个选项：默认情况下，Django模板加载器将在以下位置的books应用程序内查找模板：books / templates / books / book_list.html。 我们也可以改为创建一个单独的项目级模板目录，然后更新settings.py文件以指向该目录。

您最终会在自己的项目中使用哪一个是个人喜好。 我们将在此处使用默认结构。 如果您对第二种方法感到好奇，请阅读《 Django For Beginners》一书。

首先在books应用中创建一个新的模板文件夹，然后在其中创建一个books文件夹，最后是一个book_list.html文件。

```shell
(library) $ mkdir books/templates
(library) $ mkdir books/templates/books
(library) $ touch books/templates/books/book_list.html
```

然后更新template文件，

```html
<!-- books/templates/books/book_list.html -->
<h1>All books</h1>
{% for book in object_list %}
    <ul>
        <li>Title: {{ book.title }}</li>
        <li>Subtitle: {{ book.subtitle }}</li>
        <li>Author: {{ book.author }}</li>
        <li>ISBN: {{ book.isbn }}</li>
    </ul>
{% endfor %}
```

Django内置了允许基本逻辑的模板语言。 在这里，我们使用for标签来遍历所有可用的书。 模板标签必须包含在左/右括号和括号内。 因此，格式始终为`{％for ...％}`，然后我们必须稍后使用`{％endfor％}`关闭循环。

我们要遍历的是对象，其中包含ListView提供的所有可用书籍。 该对象的名称为object_list。 因此，为了遍历每本书，我们在`{% for book in object_list ％}`。 然后显示模型中的每个字段。



## 网页

现在，我们可以启动本地Django服务器并查看我们的网页。

```shell
(library) $ python manage.py runserver
```

导航至位于http://127.0.0.1:8000/的主页。

![image-20200916022359047](https://i.loli.net/2020/09/16/sjukaA6wrTOncJb.png)

如果我们在管理员中添加其他图书，则它们也都将出现在此处。

这是对传统Django网站的快速浏览。 现在，向其中添加一个API！



## Django REST 框架

就像其他任何第三方应用程序一样，添加了Django REST Framework。 如果本地服务器Control + c仍在运行，请确保退出它。 然后在命令行上键入以下内容。

```shell
(library) $ pipenv install djangorestframework==3.10.3
```

在我们的settings.py文件中，将rest_framework添加到INSTALLED_APPS配置中。 我喜欢在第三方应用程序和本地应用程序之间做出如下区分，因为在大多数项目中，应用程序的数量迅速增长。

最终，我们的API将公开一个端点，该端点列出JSON中的所有书籍。 因此，我们将需要一个新的URL路由，一个新的视图以及一个新的序列化器文件（稍后将对此进行更多介绍）。

我们可以用多种方法来组织这些文件，但是我的首选方法是创建一个专用的api应用程序。 这样，即使将来我们增加更多应用程序，每个应用程序都可以包含专用网页所需的模型，视图，模板和url，但是整个项目的所有API专用文件都将驻留在专用api应用程序中。

让我们新建一个`api`应用程序。

```shell
(library) $ python manage.py startapp api
```

然后增加到 INSTALLED_APPS

```python
# library_project/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # 3rd party
    'rest_framework',

    # Local Apps
    'books.apps.BooksConfig',
    'api.apps.ApiConfig', # new
]
```

api应用程序将没有自己的数据库模型，因此无需像通常那样创建迁移文件和更新数据库。



## URLs

让我们从我们的URL配置开始。 添加API端点就像配置传统Django应用的路由一样。 首先，在项目级别，我们需要包括api应用程序并配置其URL路由，即api /。

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('books.urls')),
    path('api/', include('api.urls')), # new
]
```

然后在api应用程序中创建`urls.py`文件。



```shell
(library) $ touch api/urls.py
```

然后更新这个文件，如下：

```python
# api/urls.py
from django.urls import path


from .views import BookAPIView


urlpatterns = [
    path('', BookAPIView.as_view()),
]
```

一切准备就绪。



## Views

接下来是我们的views.py文件，该文件依赖Django REST Framework的内置通用类视图。 这些是故意模仿传统Django基于类的通用视图的格式，但它们不是一回事。

为避免混淆，某些开发人员将调用API视图文件apiviews.py或api.py。 就个人而言，在专用的api应用程序中工作时，我发现仅调用Django REST框架视图文件views.py并不会造成混淆，但是在这一点上意见不一。

在我们的views.py文件中，将其更新为如下所示：

```python
# api/views.py

from rest_framework import generics

from books.models import Book
from .serializers import BookSerializer


class BookAPIView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

在最上面的几行中，我们导入Django REST Framework的通用类视图，我们的Books应用中的模型以及api应用中的序列化器（我们将在下一个序列化器中导入）。

然后，我们创建一个BookAPIView，它使用ListAPIView为所有书籍实例创建一个只读端点。 有许多通用的视图，我们将在后面的章节中进一步探讨它们。

在我们的视图中，仅需执行两个步骤，即指定所有可用书籍的queryset，然后指定将成为BookSerializer的serializer_class。



## Serializers

序列化器将数据转换为易于在Internet上使用的格式（通常为JSON），并显示在API端点上。 在接下来的章节中，我们还将更深入地介绍序列化器和JSON。 现在，我想演示使用Django REST Framework创建序列化器以将Django模型转换为JSON是多么容易。

在api应用中创建一个`serializers.py`文件:

```shell
(library) $ touch api/serializers.py
```

然后在文本编辑器中更新为如下所示：

```python
# api/serializers.py
from rest_framework import serializers

from books.models import Book


class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ('title', 'subtitle', 'author', 'isbn')
```

首先，我们从Books应用程序中导入Django REST Framework的序列化器类和Book模型。 我们将Django REST Framework的ModelSerializer扩展到BookSerializer类中，该类指定我们的数据库模型Book和我们希望公开的数据库字段：标题，副标题，作者和isbn。

大概就是这样！ 完成了



## cURL

我们想看看我们的API端点是什么样子。 我们知道它应该在URL http://127.0.0.1:8000/api/返回JSON。 确保我们的本地Django服务器正在运行：

```shell
(library) $ python manage.py runserver
```

现在打开一个新的第二个命令行控制台。 我们将使用它来访问在现有命令行控制台中运行的API。

我们可以使用流行的cURL程序通过命令行执行HTTP请求。 我们需要一个基本的GET请求，以指定`curl`和我们要调用的URL。

```shell

$ curl http://127.0.0.1:8000/api/ 
[
  {
    "title":"Django for Beginners",
    "subtitle":"Build websites with Python and Django",       
    "author":"William S. Vincent", 
    "isbn":"978-198317266"
  } 
]
```

数据全部以JSON格式存在，但格式不正确且难以理解。 幸运的是，Django REST Framework给我们带来了另一个惊喜：API端点的强大可视模式。



## Browsable API

在本地服务器仍在第一个命令行控制台中运行的情况下，在Web浏览器中的http://127.0.0.1:8000/api/上导航到我们的API端点。

![image-20200916112842031](https://i.loli.net/2020/09/16/aDn5iGkTeEVqlvK.png)

哇，看看！ Django REST Framework默认情况下提供此可视化。 此页面中内置了许多功能，我们将在整本书中进行探讨。 现在，我希望您将此页面与原始JSON端点进行比较。 单击“获取”按钮，然后从下拉菜单中选择“ json”。

![image-20200916112949042](https://i.loli.net/2020/09/16/2XZjx6TCq3t8OVm.png)

这就是来自API端点的原始JSON的样子。 我认为我们可以同意Django REST Framework版本更具吸引力。



## 总结

在本章中，我们讨论了很多内容，因此请不要担心现在是否会感到困惑。 首先，我们创建了一个传统的Django图书馆网站。 然后，我们添加了Django REST Framework，并能够以最少的代码添加API端点。

在接下来的两章中，我们将构建自己的Todo API后端，并将其与React驱动的前端连接，以演示一个完整的工作示例，这将有助于巩固所有这些理论在实践中的融合！