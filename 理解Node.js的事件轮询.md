---
title:  理解Node.js的事件轮询
categories:  
    - 原创
tags:
    - JavaScript
    - Node.js
toc: true
comments: true
---

## 前言

**总括** ：本文主要是对Node.js事件轮询的一个详细讲述。

* 原文地址：[理解Node.js的事件轮询](http://blog.damonare.cn/2017/02/08/%E7%90%86%E8%A7%A3Node.js%E7%9A%84%E4%BA%8B%E4%BB%B6%E8%BD%AE%E8%AF%A2/#more)
* Node小应用：[Node-sample](https://github.com/damonare/node-sample)

**智者阅读群书，亦阅历人生**

<!-- more -->

## 正文

#### Node.js的两个基本概念

Node.js的第一个基本概念就是I/O操作开销是巨大的：



![](https://cdn.damonare.cn/io-cost.png)

所以，当前变成技术中最大的浪费来自于等待I/O操作的完成。有几种方法可以解决性能的影响：

- **同步方式**：按次序一个一个的处理请求。*利*：简单；*弊*：任何一个请求都可以阻塞其他所有请求。
- **开启新进程**：每个请求都开启一个新进程。*利*：简单；*弊*：大量的链接意味着大量的进程。
- **开启新线程**：每个请求都开启一个新线程。*利*：简单，而且跟进程比，对系统内核更加友好，因为线程比进程轻的多;*弊*:不是所有的机器都支持线程，而且对于要处理共享资源的情况，多线程编程会很快变得太过于复杂。

第二个基本概念是每个连接都创建一个新线程是很消耗内存的（例如：你可以对比Nginx回想一下Apache内存耗尽的情景）。

Apache是多线程的：它为每个请求开启一个新的线程（或者是进程，这取决于你的配置），当并发连接增多时，你可以看看它是怎么一点一点耗尽内存的。Nginx和Node.js不是多线程的，因为线程的消耗太“重”了。它们两个是单线程、基于事件的，这就把处理众多连接所产生的线程/进程消耗给消除了。

#### 单线程

确实只有一个线程：你不能并行执行任何代码，比如：下面的“sleep”将会阻塞sever1秒钟：

```javascript
function sleep() {
   var now = new Data().getTime();
   while (new Date().getTime() < now + 1000) {
         // do nothing
   }
}
sleep();
```

但就我目前学习阶段而言，我觉得好多人对于所谓的node单线程是有误解的。实际上官方给出的“单线程”是具有误导性的。所谓的单线程是指你的代码只运行在一个线程上(好多地方都叫它主线程，实际上Javascript的浏览器运行环境不也是这么处理我们写的Javascript代码的嘛)，而诸多任务的并行处理，就需要多线程了，如下图：

![](https://cdn.damonare.cn/104032-20150917140900539-1845886135.png)

如上图，Node.js中的单线程之说指的就是这个主线程，这个主线程有一个循环结构，保持着整个程序(你写的代码)的运转。

#### 事件轮询

其实上面我们所说的**维持主线程运行的循环**这部分就是"事件轮询"，它存在于主线程中，负责不停地调用开发者编写的代码。但对开发者是不可见的。so...开发者编写的代码是怎样被调用的呢？看下图：

![](https://cdn.damonare.cn/104032-20150917141055570-1948801510.png)



如上图，异步函数在执行结束后，会在事件队列中添加一个事件(遵循先进先出原则)，主线程中的代码执行完毕后(即一次循环结束)，下一次循环开始就在事件队列中"读取"事件，然后调用它所对应的回调函数(所以回调函数的执行顺序是不一定的)。如果开发者在回调函数中调用了阻塞方法(比如上文中的sleep函数)，那么整个事件轮询就会阻塞，事件队列中的事件得不到及时处理。正因为这样，nodejs中的一些库方法均是异步的，也提倡用户调用异步方法。

```javascript
var fs = require('fs');
fs.readFile('hello.txt', function (err, data) {  //异步读取文件
　　console.log("read file end");
});
while(1)
{
    console.log("call readFile over");
}
```

如上代码，我们虽然使用了异步方法readfile读取文件，但`read file end`永远不会输出，因为代码始终在while循环中，下一次事件轮询始终没法开始，也就没法'读取'事件队列调用相应的回调函数了。

最后有一个[Node-sample](https://github.com/damonare/node-sample)是博主平时积累的一些代码，包含注释，汇总成了一个小应用，还是可以看到学习的蛛丝马迹的。感兴趣的您可以看看。

## 后记

参考文章：

- [Understanding the node.js event loop](http://blog.mixu.net/2011/02/01/understanding-the-node-js-event-loop)
- [nodejs事件轮询详述](http://www.cnblogs.com/xiaozhi_5638/p/4816265.html)

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)