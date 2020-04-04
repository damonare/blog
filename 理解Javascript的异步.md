---
title: 理解Javascript的异步
categories:
  - 原创
  - 译文
tags:
  - JavaScript
toc: true
comments: true
---

### 前言

> 本文2925字，阅读大约需要10分钟。

**总括:**  本文梳理了异步代码和同步代码执行的区别，Javascript的事件循环，任务队列微任务队列等概念。

- 原文地址：[Understanding Asynchronous JavaScript](https://blog.bitsrc.io/understanding-asynchronous-javascript-the-event-loop-74cd408419ff)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**未曾失败的人恐怕也未曾成功过。**

<!-- more -->

Javascript是单线程的编程语言，单线程就是说同一时间只能干一件事。放到编程语言上来说，就是说Javascript引擎(执行Javascript代码的虚拟机)同一时间只能执行一条语句。

单线程语言的好处是你只管写不用担心并发问题。但这也意味着无法在不阻塞主线程的情况下去执行一些诸如网络请求的长时间操作。

设想下如果我们从某个接口请求一些数据，然后服务器需要一些时间才能将数据返回，此时就会阻塞主线程页面处于无响应的状态。

这里就是Javascript异步的用武之地了，我们可以通过异步操作(比如回调函数，promise和async/await)来执行长时间的网络请求而不阻塞主线程。

虽然说了解这些所有的概念不一定让你立刻成为一名出色的Javascript开发者，但了解异步会对你很有帮助。

话不多说，正文开始:)

#### 同步的代码是怎么执行的

在深入研究Javascript的异步之前，我们先来看下同步的代码是如何在Javascript引擎中执行的。看例子：

```js
const second = () => {
  console.log('Hello there!');
}
const first = () => {
  console.log('Hi there!');
  second();
  console.log('The End');
}
first();
```

要想理解上面的代码是如何在Javascript引擎中被执行的，我们必须要去[理解Javascript的执行上下文和执行栈](https://blog.damonare.cn/2020/02/09/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84%E6%89%A7%E8%A1%8C%E4%B8%8A%E4%B8%8B%E6%96%87%E5%92%8C%E6%89%A7%E8%A1%8C%E6%A0%88/#more)。

#### 执行上下文

所谓的执行上下文是Javascript代码执行环境中的一个抽象的概念。Javascript任何代码都是在执行上下文中执行的。

函数内部的代码会在函数执行上下文中执行，全局的代码会在全局执行上下文中执行，每一个函数都有自己的执行上下文。

#### 执行栈

顾名思义执行栈是一种后进先出(LIFO)的栈结构，它用来存储在代码执行阶段创建的所有的执行上下文。

基于单线程的原因，Javascript只有一个执行栈，因为是基于栈结构所以只能从栈的顶层添加或是删除执行上下文。

让我们回到上面的代码，尝试理解Javascript引擎是如何去执行它们的。

```js
const second = () => {
  console.log('Hello there!');
}
const first = () => {
  console.log('Hi there!');
  second();
  console.log('The End');
}
first();
```

![上述代码的执行栈](https://image.damonare.cn/1_DkG1a8f7rdl0GxM0ly4P7w.png)

<div align="center">上述代码的执行栈</div>

**所以这里发生了什么呢？**

当代码被执行时，首先一个全局执行上下文（这里用`main()`表示）被创建然后压到执行栈的顶端。当执行到`first()`这一行代码，它的执行上下文被压到执行栈的顶端。

紧接着，`console.log('Hi there!');`的函数执行上下文被压到执行栈的顶端，执行结束后该执行上下文从执行栈弹出。然后调用`second()`函数，该函数的执行上下文被压到执行栈的顶端。

然后执行`console.log('Hello there!');`，对应的函数执行上下文被压入执行栈，执行结束被弹出，然后`second()`函数执行结束，执行上下文被弹出。

`console.log(‘The End’)`执行，函数执行上下文被压入执行栈，执行结束被弹出，此时`first()`函数执行结束，对应执行上下文被弹出。

整个程序执行结束，全局执行上下文(main())被弹出。

#### 异步代码是怎么执行的

现在我们已经对同步代码的执行有了一个基本的认知，下面让我们看下异步代码是如何执行的：

##### 阻塞

假设我们用同步的方式去发起一个图片请求或是一个普通的网络请求，例子如下：

```js
const processImage = (image) => {
  /**
  * doing some operations on image
  **/
  console.log('Image processed');
}
const networkRequest = (url) => {
  /**
  * requesting network resource
  **/
  return someData;
}
const greeting = () => {
  console.log('Hello World');
}
processImage(logo.jpg);
networkRequest('www.somerandomurl.com');
greeting();
```

请求图片或是网络请求是需要花费时间的，因此当我们调用`processImage()`的时候，花费的时间取决于图片的大小。

当`processImage()`函数执行结束，响应的执行上下文从执行栈中弹出，然后调用`networkRequest()`函数，对应执行上下文被压入执行栈，该函数同样需要花费一些时间才能结束。

`networkRequest()`函数执行结束，调用`greeting()`，然后里面只有一行`console.log('Hello World')`，``console.log()`函数通常执行会很快，因此`greeting()`会很快执行完然后返回结果。

可以发现，我们必须等函数(比如processImage，networkRequest函数)执行结束才能调用下一个函数。这意味着这些函数调用的时候会阻塞主线程，造成主线程不能执行其他代码，这是我们所不希望的。

**所以怎么解决这个问题呢？**

最简单的解决办法就是使用异步的回调函数，有了异步的回调函数就不会阻塞主线程，看例子：

```js
const networkRequest = () => {
  setTimeout(() => {
    console.log('Async Code');
  }, 2000);
};
console.log('Hello World');
networkRequest();
```

这里我们使用了`setTimeout`方法去模拟网络请求函数。

**请注意**：`setTimeout`不是Javascript引擎提供的，而是web API(浏览器中)和C/C++ API(nodejs中)的一部分。

![Javascript运行环境概述](https://image.damonare.cn/1_O_H6XRaDX9FaC4Q9viiRAA.png)

<div align="center">Javascript运行环境概述</div>

**事件循环**、**Web API**和**消息队列/任务队列**并不是Javascript引擎的一部分而是浏览器的Javascript运行环境或是Nodejs的Javascript运行环境的一部分，在Nodejs中，Web API被C/C++ API替代。

回到上面的代码，看看异步的代码是如何执行的：

```js
const networkRequest = () => {
  setTimeout(() => {
    console.log('Async Code');
  }, 2000);
};
console.log('Hello World');
networkRequest();
console.log('The End');
```

![事件循环](https://image.damonare.cn/1_sOz5cj-_Jjv23njWg_-uGA.gif)

<div align="center">事件循环</div>

代码开始执行，`console.log(‘Hello World’)`函数的执行上下文首先被压入执行栈，执行结束后被弹出，然后调用`networkRequest()`，对应的函数执行上下文被压入执行栈。

紧接着 `setTimeout()` 函数被调用，对应的函数执行上下文被压入执行栈。

`setTimeout`有两个参数：1. 回调函数；2. 时间(以毫秒ms为单位);3. 附加参数(会被传到回调函数里面)

`setTimeout()` 函数会在web API运行环境中进行一个`2s`的倒计时，这个时候 `setTimeout()` 函数就已经执行完了，执行上下文从执行栈中弹出。再然后`console.log('The End')`函数被执行，进入执行栈，结束后弹出执行栈。

这时候倒计时到期，`setTimeout()`的回调函数被推到**消息队列**中，但回调函数不会立即执行，这是事件循环开始的地方。

#### 事件循环

事件循环的工作就是去查看执行栈，确定执行栈是否为空，如果执行栈为空，那么就去检查消息队列，看看消息队列中是否有待执行的回调函数。它按照类似如下的方式来被实现：

```js
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

在这里，执行栈已经为空，消息队列包含一个`setTimeout`函数的回调函数，因此事件循环把回调函数的执行上下文压入执行栈的顶端。

然后`console.log(‘Async Code’)`函数的执行上下文被压入执行栈，结束后从执行栈弹出。这时候回调函数执行结束，对应的执行上下文也从执行栈中弹出。

##### DOM事件

**消息队列(也叫任务队列)**中也会包含来自DOM事件(比如点击事件，键盘事件等)，看例子：

```js
document.querySelector('.btn').addEventListener('click',(event) => {
  console.log('Button Clicked');
});
```

对于DOM事件来说，web API中会有一个事件侦听器坚挺某个事件被触发(在这里是click事件)，当某个事件被触发时，就会把相应的回调函数放入消息队列中执行。

事件循环再次检查执行栈，如果执行栈为空，就把事件的回调函数推入执行栈。

我们已经了解了异步回调和事件回调是如何执行的，这些回调函数被存储在消息队列中等待被执行。

#### ES6任务队列和微任务队列

ES6中为`promise`函数引入了**微任务队列(也叫作业队列)**的概念。**微任务队列**和**消息队列**的区别就是优先级上的区别，**微任务队列**的优先级要高于**消息队列**。也就是说在**微任务队列**的`promise`回调函数会比在**消息队列**中的回调函数更先执行。

比如：

```js
console.log('Script start');
setTimeout(() => {
  console.log('setTimeout');
}, 0);
new Promise((resolve, reject) => {
  resolve('Promise resolved');
}).then(res => console.log(res))
  .catch(err => console.log(err));
console.log('Script End');
```

输出：

```txt
Script start
Script End
Promise resolved
setTimeout
```

可以看到`promise`是在`setTimeout`之前执行的，因为`promise`的response被存储在**微任务队列**中，有比**消息队列**更高的优先级。

再看另一个例子，有两个`promise`函数，两个`setTimeout`函数:

```js
console.log('Script start');
setTimeout(() => {
  console.log('setTimeout 1');
}, 0);
setTimeout(() => {
  console.log('setTimeout 2');
}, 0);
new Promise((resolve, reject) => {
  resolve('Promise 1 resolved');
}).then(res => console.log(res))
  .catch(err => console.log(err));
new Promise((resolve, reject) => {
  resolve('Promise 2 resolved');
}).then(res => console.log(res))
  .catch(err => console.log(err));
console.log('Script End');
```

输出：

```bash
Script start
Script End
Promise 1 resolved
Promise 2 resolved
setTimeout 1
setTimeout 2
```

可以看到两个`promise`的回调函数都在`setTimeout`的回调函数之前运行，因为相比**消息队列**事件循环会优先处理**微任务队列**中的回调函数。

当事件循环处理**微任务队列**中的回调函数的时候另一个`promise`被resolved了，然后这个`promise`的回调函数会被添加到**微任务队列**中。并且它会被优先执行，无论**消息队列**中的回调函数的执行会花费多长时间，都要排队。

比如：

```js
console.log('Script start');
setTimeout(() => {
  console.log('setTimeout');
}, 0);
new Promise((resolve, reject) => {
  resolve('Promise 1 resolved');
}).then(res => console.log(res));

new Promise((resolve, reject) => {
  resolve('Promise 2 resolved');
}).then(res => {
  console.log(res);
  return new Promise((resolve, reject) => {
    resolve('Promise 3 resolved');
  })
}).then(res => console.log(res));
console.log('Script End');
```

打印：

```js
Script start
Script End
Promise 1 resolved
Promise 2 resolved
Promise 3 resolved
setTimeout
```

因此所有在**微任务队列**的回调函数都会在**消息队列**的回调函数之前被执行。也就是说，事件循环会先清空**微任务队列**的回调函数才会去执行**消息队列**中的回调函数。

### 结论

我们了解了Javascript中同步和异步代码是怎么执行，以及一些其它的概念(包括执行栈，事件循环，微任务队列，消息队列等)。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)