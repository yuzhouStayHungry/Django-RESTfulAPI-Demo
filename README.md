# Django for API: Build web APIs With Python & Django

Django API开发: 使用Python和Django构建web APIs

<img src="https://i.loli.net/2020/09/19/L1Tl2MDCmovERJk.png" width="150px">

Django for api是一个基于项目的指南，指导您使用Django和Django REST框架构建现代api。

它适合从未构建过API的初学者，以及希望快速介绍Django基础知识和最佳实践的专业程序员。

## 前言

译者：最近正在学习Django for API这本入门书，此仓库是针对该书的一些开源章节翻译，可以快速入门Django，如果大家有什么问题可以提出来。

希望之后通过学习更多的Django API开发相关知识，然后建立一个自己的学习仓库。

> “I always advice people - Don't wait ! Do something when you are young, when you have no responsibilities. Invest time in yourself to have great Experiences that are going to enrich you, then you can't possibly lose.”
— Steve Jobs

## 目录

- [介绍](#介绍)
	- [为什么需要APIs](#为什需要apis)
	- [Django REST 框架](#Django-rest-框架)
	- [为什么写这本书](#为什么写这本书)
	- [总结](#总结)
- [第一章：Web API介绍](https://github.com/yuzhouStayHungry/Django-RESTfulAPI-Demo/blob/master/docs/%E7%AC%AC%E4%B8%80%E7%AB%A0%EF%BC%9AWeb%20APIs%E4%BB%8B%E7%BB%8D.md)
- [第二章：网络和API](https://github.com/yuzhouStayHungry/Django-RESTfulAPI-Demo/blob/master/docs/%E7%AC%AC%E4%BA%8C%E7%AB%A0%EF%BC%9A%E7%BD%91%E7%AB%99%E5%92%8CAPI.md)
- [第三章：Todo待办事项应用API](https://github.com/yuzhouStayHungry/Django-RESTfulAPI-Demo/blob/master/docs/%E7%AC%AC%E4%B8%89%E7%AB%A0%EF%BC%9ATodo%E5%BA%94%E7%94%A8API.md)
- 待续

### 介绍

互联网由RESTful API提供支持。在后台，即使最简单的在线任务也涉及到多台计算机相互交互。

API（Application Programming Interface）是描述两台计算机互相通信的一种方式。尽管有多种构建API的方式，但Web APIs--允许在全球范围内传输数据--以RESTful（REp resentaional State Transfer）方式构成了压倒性的结构。

接下来学习如何使用[Django](https://www.djangoproject.com/)和[Django REST Framework](http://www.django-rest-framework.org/)来构建不同的RESTful web APIs。

Django和Django REST Framework的结合是构建Web API的最流行和可自定义的方法之一，被世界上许多大型科技公司（包括Instagram，Mozilla，Pinterest和Bitbucket）使用。 它还非常适合初学者，因为Django的“含电池”方法掩盖了创建任何Web API所涉及的许多潜在的复杂性和安全风险。



### 为什么需要APIs？

Django于2005年首次发布，当时大多数网站由一个大型整体代码库组成。 “后端”由数据库模型，URL和视图组成，这些模型与HTML，CSS和JavaScript的“前端”模板交互，而HTML，CSS和JavaScript则控制着每个网页的显示布局。

但是，近年来，“ API优先”方法已经成为Web开发中的主要范例。 此方法涉及将后端与前端正式分离。 这意味着Django变成了功能强大的数据库和API，而不仅仅是网站框架。

如今，可以说，在大型公司中，Django被更多地用作后端API而不是完整的整体网站解决方案！

此时一个明显的问题是：“为什么要打扰？” 传统的Django本身可以很好地工作，将Django站点转换为Web API似乎需要做很多工作。 另外，作为开发人员，您必须用另一种编程语言编写专用的前端。

顺便说一下，这种将服务分为不同组件的方法被广泛称为**面向服务的体系结构**。



然而，事实证明，将前端与后端分离有多个优点。 首先，它可以说是“面向未来的”，因为任何JavaScript前端都可以使用后端API。 鉴于前端库中的更改速度非常快-React仅在2013年发布，而Vue在2014年发布！-这非常有价值。 如果在未来几年内最终将当前的前端框架替换为更新的框架，则后端API可以保持不变。 不需要大的重写。

其次，API可以支持以不同语言和框架编写的多个前端。 考虑到JavaScript用于Web前端，而Android应用程序需要Java编程语言，而iOS应用程序需要Swift编程语言。 使用传统的整体方法，Django网站无法支持这些各种前端。 但是使用内部API，这三个组件都可以与相同的基础数据库后端通信！

第三，可以在系统内部和外部都使用API-first方法。 

API-first的主要缺点是，与传统的Django应用程序相比，它需要更多的配置。 但是，正如我们将在本书中看到的那样，出色的Django REST Framework库消除了许多这种复杂性。



### Django REST 框架

有成百上千的第三方应用程序可为Django添加更多功能。 （您可以在[Django Packages](https://djangopackages.org/)上看到完整的可搜索列表。）但是Django REST Framework可以说是Django的杀手级应用。

它是成熟的，功能齐全，可自定义，可测试的，并且文档齐全。 它还有目的地模仿了Django的许多传统约定，从而使学习速度更快。 它是用Python编程语言编写的，这是一种很棒，流行且易于访问的语言。

如果您已经了解Django，那么下一步就是学习Django REST Framework。 只需最少的代码，它就可以将任何现有的Django应用程序转换为Web API。



### 为什么写这本书

我写这本书的原因是，对于Django REST框架新手来说，显然缺乏良好的资源。 假设似乎每个人都已经了解API，HTTP，REST等。 我自己学习如何构建Web API的旅程令人沮丧...而且我已经对Django足够了解，因此可以写一本书！

本书是我希望以Django REST Framework开头的指南。

第1章首先简要介绍Web API和HTTP协议。

在第2章中，我们将建立一个图书馆书网站，然后向其中添加一个API，以回顾传统Django和Django REST Framework之间的区别。 

然后在第3-4章中，我们将构建一个Todo API并将其连接到React前端。 可以使用相同的过程将任何专用的前端（Web，iOS，Android，台式机或其他）连接到Web API后端。

在第5-9章中，我们构建了一个生产就绪的Blog API，其中包含完整的CRUD功能。 我们还将介绍深度权限，用户身份验证，视图集，路由器，文档等。

可以在[Github上](https://github.com/wsvincent/restapiswithdjango)在线找到所有章节的完整源代码。



### 总结

Django和Django REST Framework是一种强大且易于访问的构建Web API的方法。 到本书结尾，您将能够使用现代最佳实践从头开始正确构建自己的Web API。 您将能够以最少的代码将任何现有的Django网站扩展到网络API。
让我们开始！