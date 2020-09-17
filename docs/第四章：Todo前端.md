# 第 4 章：Todo应用的React前端

API的宫内在于与其他程序进行通信。 在本章中，我们将通过[React](https://reactjs.org/)前端使用上一章中的Todo API，这样您就可以了解实际中一切如何协同工作。

我选择使用React，因为它是目前最流行的JavaScript前端库，但是此处描述的技术也可以与其他任何流行的前端框架一起使用，包括[Vue](https://vuejs.org/)，[Angular](https://angular.io/)或[Ember](https://emberjs.com/)。 他们甚至可以与适用于iOS或Android的移动应用，桌面应用或其他任何应用一起使用。 连接到后端API的过程非常相似。

如果您陷入困境或想了解有关React实际发生的事情的更多信息，请查看很好的[官方教程](https://reactjs.org/tutorial/tutorial.html)。



## 安装Node

首先，将React应用程序配置为前端。 首先打开一个新的命令行控制台，以便现在有**两个控制台打开**。 这个很重要。 我们需要上一章中现有的Todo后端才能在本地服务器上运行。 我们将使用第二个控制台来创建，然后在单独的本地端口上运行我们的React前端。 这就是我们在本地模拟专用和已部署的**前端**/**后端**的生产环境的样子的方式。

在新的第二个命令行控制台中，安装[NodeJS](https://nodejs.org/en/)，它是一个JavaScript运行时引擎。 它使我们可以在Web浏览器之外运行JavaScript。

在Mac计算机上，我们可以使用Homebrew，如果您按照[Django入门指南](https://djangoforbeginners.com/initial-setup/)中的说明配置本地计算机，则应该已经安装了Homebrew。

```shell
$ brew install node
```

在Windows计算机上，有多种方法，但一种流行的方法是使用
[nvm-windows](https://github.com/coreybutler/nvm-windows)。 其存储库包含详细的最新安装说明。 如果您使用的是Linux，请使用[nvm](https://github.com/creationix/nvm)。 在撰写本文时，可以使用以下任一命令来完成，要么使用cURL命令：

```shell
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/\ install.sh | bash
```

或者使用Wget：

```shell
$ wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/\ install.sh | bash
```

然后运行：

```shell
$ command -v nvm
```

关闭当前的命令行控制台，然后再次打开以完成安装。



## 安装 React

我们将使用出色的[create-react-app](https://github.com/facebookincubator/create-react-app)包来快速启动一个新的React项目。 这将生成我们的项目样板并通过一个命令安装所需的依赖项！

要安装React，我们将依赖[npm](https://www.npmjs.com/)，这是一个JavaScript包管理器。 像用于Python的pipenv一样，npm使管理和安装多个软件包变得非常简单。 npm的最新版本还包括[npx](https://medium.com/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)，这是一种改进的方法，可以在本地安装软件包而不污染全局名称空间。 这是安装React的推荐方法，也是我们在此使用的方法！

```shell
$ cd ~/Desktop
$ cd todo
```

创建一个名为前端的新React应用。

```shell
$ npx create-react-app frontend
```

您的目录结构现在应如下所示：

```shell
todo
|   ├──frontend
|       ├──React... 
|   ├──backend
|       ├──Django...
```

转到我们的前端项目，并使用命令`npm start`运行React应用。

```shell
$ cd frontend
$ npm start
```

如果导航到 `http://localhost:3000 /` ，则将看到create-react-app默认主页。

![image-20200917230941089](https://i.loli.net/2020/09/17/jqvSkmM2KAh5Y7N.png)

## 模拟数据

如果您返回到我们的API端点，则可以在以下位置的浏览器中看到原始JSON：
http://127.0.0.1:8000/api/?format=json



```json
[ 
  {
    "id":1,
    "title":"1st todo", 
    "body":"Learn Django properly."
  }, 
  {
    "id":2, 
    "title":"Second item", 
    "body":"Learn Python."
  }, 
  {
    "id":3,
    "title":"Learn HTTP", 
    "body":"It's important."
  } 
]
```

每当向API端点发出GET请求时，就会返回此值。 最终，我们将直接使用该API，但是一个好的初始步骤是首先模拟数据，然后配置我们的API调用。

我们需要在React应用程序中更新的唯一文件是`src/App.js`。 首先，在一个名为list的变量中模拟API数据，该变量实际上是一个具有三个值的数组。

```js
// src/App.js
import react, { Component } from 'react';

const list = [
  {
    "id":1,
    "title":"1st todo",
    "body":"Learn Django properly."
  },
  {
    "id":2,
    "title":"Second item",
    "body":"Learn Python."
  },
  {
    "id":3,
    "title":"Learn HTTP",
    "body": "It's important."
  }
]
```

接下来，我们将列表加载到组件的状态，然后使用JavaScript数组方法[map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)显示所有项目。

我故意在这里快速移动，如果您以前从未使用过React，请复制代码，以便您了解如何将React前端连接到我们的Django后端。

这是现在包含在 `src/App.js` 文件中的完整代码。