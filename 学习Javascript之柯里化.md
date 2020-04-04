---
title: 理解Javascript的柯里化
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文1454字，阅读大约需要4分钟。

**总括:**  本文以初学者的角度来阐述Javascript中柯里化的概念以及如何在工作中进行使用。

- 原文地址：[理解Javascript的柯里化](https://blog.damonare.cn/2020/02/02/理解Javascript的柯里化/#more)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**事亲以敬，美过三牲。**

<!-- more -->

## 正文

函数式编程是一种如今比较流行的编程范式，它主张将函数作为参数进行传递，然后返回一个没有副作用的函数，说白了，就是希望一个函数只做一件事情。

像Javascript，Haskell，Clojure等编程语言都支持函数式编程。

这种编程思想涵盖了三个重要的概念：

- 纯函数；
- 柯里化；
- 高阶函数；

而这篇文章主要是想向大家讲清楚`柯里化`这个概念。

### 什么是柯里化

首先我们先来看一个例子：

```js
function sum(a, b, c) {
  return a + b + c;
}
// 调用
sum(1, 2, 3); // 6
```

上述函数实现的是将a,b,c三个参数相加，改写为柯里化函数如下：

```js
function sum(a) {
  return function (b) {
    return function(c) {
      return a + b + c;
    } 
  }
}
// 调用
let sum1 = sum(1);
let sum2 = sum1(2);
sum2(3); // 6
```

**所谓柯里化就是把具有较多参数的函数转换成具有较少参数的函数的过程。**

我们来一步步看上面那个柯里化函数做了什么，首先第一步调用了sum(1)，此时变量sum1相当于：

```js
sum1 = function(b) {
   return function(c) {
    // 注意此时变量a存在于闭包中，可以调用，a = 1
    return a + b + c;
  }
}
```

然后调用sum1(2)，此时赋值给变量sum2相当于：

```js
sum2 = function(c) {
  // 变量a,b皆在闭包中, a = 1, b = 2
  return a + b + c;
}
```

最后调用sum2(3)，返回1 + 2 + 3的结果6；

这就是一个最简单的柯里化函数，是不是很简单呢？

### 柯里化函数的作用

那么问题来了，上面改写后的柯里化函数和原函数比起来代码多了不少，而且也不如原函数好理解，柯里化函数到底有什么用呢？

确实，柯里化函数在这里看起来的确是很臃肿，不实用，但在很多场景下他的作用是很大的，甚至很多人在不经意间已经在使用柯里化函数了。举一个简单的例子：

假设我们有一批的长方体，我们需要计算这些长方体的体积，实现一个如下函数：

```js
function volume(length, width, height) {
  return length * width * height;
}
volume(200, 100, 200);
volume(200, 150, 100);
volume(200, 50, 80);
volume(100, 50, 60);
```

如上计算长方体的体积函数会发现存在很多相同长度的长方体，我们再用柯里化函数实现一下：

```js
function volume(length, width, height) {
  return function(width) {
      return function(height) {
         return length * width * height;
      }
  }
}
let len200 = volume(200);
len200(100)(200);
len200(150)(100);
len200(50)(80);
volume(100)(50)(60);
```

如上，通过实现一个len200函数我们统一处理长度为200的长方体的体积，这就实现了**参数复用**。

我们再举一个只执行一次函数的例子：

```js
function execOnce(fun) {
   let flag = true;
  return function() {
    if (flag) {
      fun && fun();
      flag = false;
    }
  }
}
let onceConsole = execOnce(function() {
  console.log('只打印一次');
});
onceConsole();
onceConsole();
```

如上，我们实现了一个execOnce函数，该函数接受一个函数参数，然后返回一个函数，变量flag存在闭包中，用来判断返回的函数是否执行过，onceConsole相当于：

```js
let onceConsole = function() {
  if (flag) {
       (function() {
        console.log('只打印一次');
      })()
      flag = false;
    }
}
```

这也是柯里化函数的一个简单应用。

### 通用柯里化函数的实现

既然柯里化函数这么实用，那么我们能不能实现一个通用的柯里化函数呢？所谓通用，就是说该函数可以把函数参数转换为柯里化函数，看下第一版实现的代码：

```js
 // 第一版
var curry = function (fn) {
  var args = [].slice.call(arguments, 1);
  return function() {
    var newArgs = args.concat([].slice.call(arguments));
    return fn.apply(null, newArgs);
  };
};
 function add(a, b) {
   return a + b;
 }

var addFun = curry(add, 1, 2);
addFun() // 3
//或者
var addOne = curry(add, 1);
```

如上代码，我们接受一个函数作为参数，然后收集其它的参数，将这些参数传给这个函数参数去执行。但上面的代码有个问题，参数不够自由，比如我们想这么调用就会报错：

```js
var addFun = curry(function(a, b,c) {
  return a + b + c;
}, 1);
addFun(2)(3); // 报错 addFun(...) is not a function
```

这好像违背了我们参数复用的原则，改进如下：

```js
function curry(fn, ...args) {
  var length = fn.length;
  args = args || [];
  return function(...rest) {
    var _args = [...args, ...rest];
    return _args.length < length
      ? curry(fn, _args)
    : fn.apply(this, _args);
  }
}
var fn = curry(function(a, b, c) {
  console.log(a + b + c);
});
fn('a', 'b', 'c'); // abc
fn('a', 'b')('c'); // abc
fn('a')('b')('c'); // abc
```

如上实现就很完善，该工具函数的实现总结起来就一句话：

**利用闭包将函数的参数储存起来，等参数达到一定数量时执行函数。**

## 后记

柯里化是以闭包为基础的，不理解闭包可能对柯里化的理解有所阻碍，希望通过这篇文章能让各位了解和理解Javascript的柯里化。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)