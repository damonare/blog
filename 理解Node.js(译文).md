---
title: 理解Node.js(译文)
categories: 
    - 译文
    - 原创
tags:
    - JavaScript
    - 译文
    - Node.js
toc: true
comments: true
---

## 前言

**总括** ：这篇文章十分生动形象的的介绍了Node，满足了读者想去了解Node的需求。作者是Node的第一批贡献者之一，德国前端大神。译者觉得作者的比喻很适合初学者理解Node，特此翻译。

**译者** ：原文网址里有只小蚂蚁的效果很有意思(多次鼠标悬浮会有惊喜)，哈哈哈，可以去看一下哦。

* 原文地址：[Understanding node.js](http://debuggable.com/posts/understanding-node-js:4bd98440-45e4-4a9a-8ef7-0f7ecbdd56cb)
* 原文作者：[Felix Geisendörfer](http://felixge.de/)
* Node小应用：[Node-sample](https://github.com/damonare/node-sample)
* 译者：[Damonare](http://damonare.cn)

**本文属于译文** 

<!-- more -->

## 正文

当我向别人介绍Node.js的时候一般会有两种反应，要么是立马就弄明白它是个什么玩意儿，要么是被它搞的很糊涂。

如果你现在还处于后者，下面就是我对于`node`的解释：

- 它是一个命令行工具，你可以下载一个tarball文件，编译然后安装源文件；
- 它可以让你在你的终端输入`node my_app.js `来运行Javascript程序；
- Node的JS代码是由 [V8 javascript 引擎](http://code.google.com/p/v8/)（就是那个使得Chrome如此之快的东西）所执行的；
- Node提供了诸如访问网络或是操作文件系统的`Javascript API`

### “但我也可以用 Ruby, Python, Php,Java, ...等语言来做我想要做的事啊”

我听到你说的话了，你是对的。`Node`不是狡猾的独角兽，这点很抱歉，它不会帮你做你该做的事。它仅仅是一个工具，而且他也不会替代你现在所常用的一些工具，至少现在不会。

### "说重点！！！"

好的，我会的，当你需要同时做好几件事的时候`Node`会表现的十分优秀。你有写了一段代码然后对他说"我想你可以并行运行！"的体验吗？哈哈哈，在Node中除了你的代码所有的东西都是并行运行的。

### "啊？！"

是的，没错，除了你的代码之外所有的代码都是并行运行的。为了理解这一点，你可以把你自己的代码想象成一个国王，而`Node`就是他的仆人军队。

新的一天是这样开始的：某个仆人叫醒了国王，然后问他是否需要什么。国王给了这个仆人一个任务清单然后就回去继续睡觉了。然后这个仆人就把任务清单上的任务分发下去，仆人们开始工作了。

当一个仆人完成了他的任务的时候，他就跑到国王寝宫外面排队等候报告。国王一次只能听取一个仆人报告任务，有的时候国王会在仆人报告结束的时候给他更多的任务。(看你代码咋写咯)

生活是美好的，因为国王的诸多仆人同时执行多个任务，但报告结果的时候是一个一个来的，所以国王能够很专注。

### "那确实很美好，但你能结束这个愚蠢的比喻用更加geek的方式来告诉我吗？"

好的，一个`node`程序或许是下面这样的：

```javascript
var fs = require('fs')
  , sys = require('sys');
//译者注：sys is deprecated. Use util instead.这里我们直接用console.log即可
fs.readFile('treasure-chamber-report.txt', function(report) {
  //sys.puts("oh, look at all my money: "+report);
  console.log("oh, look at all my money: "+report)
});

fs.writeFile('letter-to-princess.txt', '...', function() {
  //sys.puts("can't wait to hear back from her!");
  console.log("can't wait to hear back from her!")
});
```

你的代码(国王)给了`node`(仆人)两个任务即读(readFile)和写(writeFile)文件，然后就去睡大觉了。一旦node完成了某个任务，跟这个任务对应的回调就会触发。但同一时间只能有一个回调被触发，在那个回调执行完成之前，所有其它的回调都得排队等待。进一步说，回调触发的顺序是不能被保证的。

### "所以我不必担心代码在同一时间访问同一个数据结构？"

你确实理解了，这就是JavaScript的单进程/事件循环设计美丽的地方。

### "好棒，但我为什么应该用它呢？"

一个原因是效率。在一个web应用中，响应时间主要是花在了执行数据库查询上面，而用`node`,你可以一次性执行所有的数据库查询。将响应时间减少到了执行最慢的数据库查询所用的时间。

另一个原因是`Javascript`。你可以使用`Node`让你的浏览器和后端共享代码。Javascript也在渐渐成为一门真正的通用语言。不管你在过去是用Python, Ruby, Java, PHP, ...等等，你都或多或少的使用过Javasctipt，对吗？

最后一个原因是原生速度。V8正在不断的推进作为地球上最快的动态语言编译器之一的边界，我也想不到有任何其它的语言在速度上能够像Javascript一样不断的高歌猛进。再进一步说，`node`的I/O设备真的十分的轻量，能够让你尽可能最大程度的利用系统的I/O容量。

### "所以你是说从现在开始我应该用Node写我所有的应用么？"

是也不是，一旦你开始舞弄`node`这柄锤子，所有的东西都会开始变得像钉子。但如果你当前的工作有一个deadline，你可以参考下面的几点来做决定用不用`node`:

- 低响应时间/高并发是否重要？Node真的很擅长处理这俩问题；
- 项目有多大？小项目没问题，如果是大项目就应该认真评估了(可用的库，修复一个bug所需的资源或者two upstream等等)

### "我能在Node中访问DOM吗？"

这是一个好问题！答案是不行，DOM是浏览器的东西吗，不过幸好node的JS引擎（V8）跟那些混乱的东西是完全分离的。不过，有人在以node模块的形式来实现DOM，或许带来令人兴奋的可能性比如对客户端代码进行单元测试。(译者注：现在已经有人实现了这个模块，详情查看[Node-dom](https://www.npmjs.com/package/node-dom))。

### "难道事件驱动编程真的很难吗？"

这取决于你自己，如果你已经学会了如何在浏览器里调用Ajax或是调用某个事件，那么学习node对你不会是什么难题。

同时，测试驱动开发能够真正的帮助你从做一个可维护的设计开始学习node。

### "我应该从哪里学到更多？"

Tim Caswell正在运作优秀的[How To Node](http://howtonode.org/)博客。在twitter上Follow [nodejs](https://twitter.com/search?q=node.js&src=typd)。订阅[邮件列表](http://groups.google.com/group/nodejs)。(译者注：也可以结合[Node.js 6.9.5 文档](http://nodejs.cn/api/)进行学习，另外，译者写了一个node的小应用[node-sample](https://github.com/damonare/node-sample)可以clone下来看下)

## 后记

本篇文章的比如讲真是有些简单了，但从现实事物中找到真正相对应的也是在太难。，另外，由于时间原因，本文一些不妥之处或是当时还处在实验性阶段的东西译者或删或改。能力有限，水平一般，翻译不妥之处，还望指正。感谢。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)