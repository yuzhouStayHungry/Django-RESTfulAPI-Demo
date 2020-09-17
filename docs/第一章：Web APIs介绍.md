# 第一章：Web APIs

在开始构建自己的网络API之前，请务必先回顾网络的实际是怎样运行的。 毕竟，“ Web API”实际上位于万维网的现有体系结构之上，并且依赖于包括HTTP，IP / TCP等在内的多种技术。
在本章中，我们将回顾Web API的基本术语：终端，资源，HTTP动词，HTTP状态码和REST。 即使您已经对这些术语感到熟悉，我还是鼓励您完整阅读本章。



## 万维网

互联网是至少从1960年代就已经存在的互连计算机网络系统。 但是，互联网的早期使用仅限于少数几个隔离的网络，这些网络主要是政府，军事或科学性质的，可以通过电子方式交换信息。 到1980年代，许多研究机构和大学都在使用Internet共享数据。 在欧洲，最大的互联网节点位于瑞士日内瓦的CERN（欧洲核研究组织），该实验室经营着世界上最大的粒子物理实验室。 这些实验产生大量数据，需要与世界各地的科学家远程共享。

但是，与今天相比，1980年代的整体互联网使用量很小。 大多数人无法使用它，甚至无法理解它为什么重要。 少数Internet节点为所有流量提供动力，而使用它的计算机主要位于同一小型网络中。

1989年，CERN的研究科学家蒂姆·伯纳斯·李（Tim Berners-Lee）发明了HTTP并引入了现代的万维网，这一切都改变了。 他的远见卓识是，可以将现有的超文本系统（其在计算机屏幕上显示的文本包含指向其他文档的链接（超链接））移动到Internet上。

他的发明--超文本传输协议（HTTP）是第一个标准的，全球通过Internet共享文档的方式。 它引入了网页的概念：带有URL，链接和资源（例如图像，音频或视频等）的分散文档。

如今，当大多数人想到“互联网”时，他们想到的是万维网（World Wide Web），这是数十亿人和计算机在线通信的主要方式。



## URLs

URL（统一资源定位符）是互联网上资源的地址。 例如，Google主页位于https://www.google.com。

当您要转到Google主页时，请在网络浏览器中键入完整的URL地址。 然后，您的浏览器通过Internet发送请求，并与服务器建立了神奇的连接（我们将介绍实际发生的情况），该服务器使用在浏览器中呈现Google主页所需的数据进行响应。

此**请求**和**响应**模式是所有Web通信的基础。 **客户端**（通常是Web浏览器，但也有本机应用程序或实际上任何与Internet连接的设备）请求信息，而**服务器**则以响应进行响应。

由于网络通信是通过HTTP进行的，因此这些形式更正式地称为HTTP请求和HTTP响应。

在给定的URL中，还有几个离散的组件。 例如，再次考虑https://www.google.com。 第一部分，https，指的是使用的scheme。 它告诉Web浏览器如何访问该位置的资源。 对于网站，通常是http或https，但是也可以是ftp（用于文件），smtp（用于电子邮件）等等。 下一部分www.google.com是网站的主机名或实际名称。 每个URL都包含一个方案和一个主机。

许多网页也包含可选**路径**。 如果您访问Python官网https://www.python.org,Python，然后单击“关于”页面的链接，您将被重定向到 https://www.python.org/about/。`/about` /是路径。

总而言之，每个 https://python.org/about/ 之类的URL都有三个潜在部分：

- 模式/协议-https

- 主机/服务器名-www.python.org

- 和（可选）路径-/ about /


## 互联网协议套件

一旦我们知道了资源的实际URL，其他所有技术的全部集合就必须正常工作（一起）以将客户端与服务器连接并加载实际的网页。 这被广泛称为Internet procotol套件，并且整本书都围绕该主题编写。 但是，出于我们的目的，我们可以坚持广泛的基础知识。

当用户在其网络浏览器中输入https://www.google.com并点击Enter时，会发生一些事情。 首先，浏览器需要在广阔的互联网上的某个地方找到所需的服务器。 它使用域名服务（DNS）将域名“ google.com”转换为IP地址，该IP地址是代表互联网上每个已连接设备的唯一数字序列。 使用域名是因为，与“ 172.217.164.68”这样的IP地址相比，人类更容易记住“ google.com”这样的域名。

浏览器具有给定域名的IP地址后，它需要一种方法来与所需服务器建立一致的连接。 这是通过传输控制协议（TCP）进行的，该协议可在两个应用程序之间提供可靠，有序和经过错误检查的字节传输。

为了在两台计算机之间建立TCP连接，客户端和服务器之间会发生三次“握手”：

1. 客户端发送`SYN`给服务端，请求建立连接

2. 服务端响应一个`SYN-ACK`确认这次请求，传递一个连接参数
3. 客户端发送`ACK`回给服务器以确认连接

一旦建立TCP连接，两台计算机就可以开始通过HTTP通信。



## HTTP 动词

每个网页都包含一个地址（URL）以及一系列被批准的动作，称为HTTP动词。 到目前为止，我们主要讨论了获取网页的问题，但是也可以创建，编辑和删除内容。

考虑一下Facebook网站。 登录后，您可以阅读时间轴，创建新帖子或编辑/删除现有帖子。 创建，读取，更新，删除这四个动作俗称CRUD功能，它们代表了绝大多数在线采取的动作。

HTTP协议包含许多从服务器请求信息时可以使用的**请求方法**。 四个最常见的CRUD：它们是POST，GET，PUT和DELETE。

![image-20200916010024544](https://i.loli.net/2020/09/16/kyiYvKaFGPLMu4t.png)

若要创建内容，请使用POST，读取内容GET，对其进行更新，然后使用DELETE进行删除。



## 终端

网站由包含HTML，CSS，图像，JavaScript等的网页组成。 但是，Web API具有终结点，而终结点是带有公开数据的可用操作（HTTP动词）列表的URL（通常为JSON，这是当今最常见的数据格式，并且是Django REST Framework的默认格式）。
例如，我们可以为一个名为mysite的新网站创建以下API端点。

```html
https://www.mysite/api/users      # Get returns all users
https://www.mysite/api/users/<id> # Get returns a single user
```

在第一个端点/ api / users中，可用的GET请求返回所有可用用户的列表。 这种返回多个数据资源的端点称为集合。

第二个端点/ api / users / <id>代表一个用户。 GET请求仅返回有关该用户的信息。



如果将POST添加到第一个端点，则可以创建一个新用户，而将DELETE添加到第二个端点，则可以删除单个用户。

在本书的学习过程中，我们将更加熟悉API端点，但是最终创建API涉及到一系列端点：带有关联HTTP动词的URL。

网页包含HTML，CSS，图像等。 但是端点只是通过可用的HTTP动词访问数据的一种方式。



## HTTP

在本章中，我们已经讨论了很多HTTP，但是在这里我们将描述HTTP的实际含义和工作方式。
HTTP是具有现有TCP连接的两台计算机之间的请求-响应协议。 发出请求的计算机称为客户端，而响应的计算机称为服务器。 通常，客户端是Web浏览器，但也可以是iOS应用或任何与互联网连接的设备。 服务器是经过优化可通过Internet工作的任何计算机的名字。 我们需要将一台基本笔记本电脑转变为一台服务器所需的一切，就是一些特殊的软件和持久的Internet连接。

每个HTTP消息均包含一个请求/状态行，请求头和可选的正文数据。 例如，这是一个示例HTTP消息，浏览器可能会发送该HTTP消息来请求位于https://www.google.com的Google主页。

```http
GET / HTTP/1.1
Host: google.com
Accept_Language: en-US
```

第一行称为请求行，它指定要使用的HTTP方法（GET），路径（/）以及要使用的特定HTTP版本（HTTP / 1.1）。

随后的两行是HTTP标头：Host是域名，

Accept_- Language是要使用的语言，在这种情况下是美国英语。 有许多HTTP标头可用。

HTTP消息还有一个可选的第三部分，称为主体。 但是，我们只会看到带有HTTP响应的正文消息，其中包含数据。

为简单起见，假设Google主页仅包含HTML“ Hello，World！” 这就是来自Google服务器的HTTP响应消息。

```http
HTTP/1.1 200 OK
Date: Wed, 28 Oct 2019 23:26:07 GMT
Server: gws
Accept-Ranges: bytes
Content-Length: 13
Content-Type: text/html; charset=UTF-8

Hello, world!
```

第一行是响应行，它指定我们正在使用HTTP / 1.1。 状态码200 OK指示客户端的请求已成功（稍后会更多关于状态码的信息）。

接下来的八行是HTTP标头。 最后，在换行后，我们的实际正文内容为“ Hello，world！”。

因此，每个HTTP消息（无论是请求还是响应）都具有以下格式：

```http
Response/request line
Headers...

(optional) Body
```

大多数网页包含多个资源，这些资源需要多个HTTP请求/响应周期。 如果一个网页具有HTML，一个CSS文件和一个图像，则在浏览器中呈现完整的网页之前，需要在客户端和服务器之间来回往返三趟。



## 状态码

一旦您的Web浏览器在URL上执行了HTTP请求，就不能保证一切都会真正生效！ 因此，有很长的HTTP状态代码列表可用于伴随每个HTTP响应。

您可以根据以下系统判断状态码的一般类型：

- 2xx 成功- 客户请求的操作已收到，理解并接受
- 3xx 重定向 - 所请求的网址已移动
- 4xx 客户端错误 - 发生错误，通常是客户端的URL请求错误
- 5xx 服务端错误 - 服务器无法解决请求

无需记住所有可用的状态代码。 通过练习，您将熟悉最常见的设置，例如200（确定），201（创建），301（永久移动），404（未找到）和500（服务器错误）。

要记住的重要一点是，一般而言，任何给定的HTTP请求只有四个潜在结果：它起作用（2xx），它以某种方式重定向（3xx），客户端出错（4xx）或服务器发出错误（5xx）。

这些状态代码会自动放置在每条HTTP消息顶部的请求/响应行中。



## 无状态

关于HTTP的最后一个重要点是，它是一个无状态协议。 这意味着每个请求/响应对都完全独立于前一个。 过去的交互没有存储的内存，在计算机科学中称为状态。

无状态为HTTP带来了很多好处。 由于所有电子通信系统都会随着时间流逝而丢失信号，因此，如果我们没有无状态协议，那么如果不经历一个请求/响应周期，事情就会不断中断。 结果，HTTP被称为非常有弹性的分布式协议。

但是缺点是，在Web应用程序中，状态管理非常重要。 说明网站是如何记住您已登录的，以及电子商务网站如何管理您的购物车。 这是我们使用现代网站的基础，但HTTP本身不支持它。

历史上状态是在服务器上维护的，但是在诸如React，Angular和Vue之类的现代前端框架中，它已越来越多地转移到客户端，Web浏览器。 当我们介绍用户身份验证时，我们将详细了解状态，但是请记住，HTTP是无状态的。 这非常有利于在两台计算机之间可靠地发送信息，但不利于记住每个单独的请求/响应对之外的任何内容。



## **REST**

代表性状态转移（REST）是Roy Fielding在其论文中于2000年首次提出的体系结构。 它是一种在Web之上（即在HTTP协议之上）构建API的方法。

关于使API实际上是否为RESTful的原因，已经撰写了整本书。 但是出于我们的目的，我们将在此处重点关注**三个**主要特征。 每个RESTful API：

- 无状态，像HTTP
- 支持常见的HTTP动词（GET，POST，PUT，DELETE等）
- 以JSON或XML格式返回数据

任何RESTful API至少必须具有这三个原则。 该标准很重要，因为它提供了设计和使用Web API的一致方法。



## 总结

尽管现代互联网具有很多技术，但作为开发人员，我们不必从头开始实施所有技术。 Django和Django REST Framework的完美结合可以正确处理Web API涉及的大多数复杂性。 但是，重要的是要至少对所有部件如何组合在一起有一个广泛的了解。

最终，Web API是端点的集合，这些端点公开了基础数据库的某些部分。 作为开发人员，我们控制每个端点的URL，可用的基础数据以及可以通过HTTP动词执行的操作。 通过使用HTTP标头，我们还可以设置各种级别的身份验证和权限，这将在本书的后面部分中介绍。



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