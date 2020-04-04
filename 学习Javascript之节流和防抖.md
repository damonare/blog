---
title: 学习Javascript之节流和防抖
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文1012字，阅读大约需要4分钟。

**总括:**  本文通过实例介绍了什么是节流函数，什么是防抖函数。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**社会犹如一条船，每个人都要有掌舵的准备。**

<!-- more -->

## 正文

节流函数和防抖函数相信很多人都在日常业务开发中使用过，其实不管是节流函数还是防抖函数都是一种简单的高阶函数，他们都是通过将一个关键的外部变量保存在外层作用域，通过对这个变量的判断和操作来决定是否调用回调函数。

### 节流函数

函数节流让指函数有规律的进行调用，应用场景:

1. 游戏中发射子弹的频率(比如1秒发射一颗);
2. 滚动事件；

所谓节流即让回调函数在一定时间内只能调用一次，因此我们的节流函数需要的参数有两个：

1. 回调函数；
2. 延迟执行的时间；

实现如下：

```js
/**
 * @param {function} fun 调用函数
 * @param {number} delay 延迟调用时间
 * @param {array} args 剩余参数
 */
const throttle = function(fun, delay, ...args) {
  let last = null;
  return function(...rest) {
      const now = + new Date();
      let _args = [...args, ...rest];
      if (now - last > delay) {
          fun.apply(this, _args);
          last = now;
      }
  }
}
```

解释下上面的代码，我们通过在外层函数声明一个`last`变量，然后返回一个函数，在该函数中获取当前的时间，拼接外层函数的剩余参数和内层函数的参数，如果当前的时间戳和上一次调用的时间戳差大于延迟时间，那么就执行回调函数。我们测试下：

```js
var obj = { a: 1 };
var num = 0;
//实例
var throttleExample = throttle(function(...rest) {
  console.log(rest, this.a);
  num++;
}.bind(obj), 1000);
//调用
throttleExample(num);
throttleExample(num);
throttleExample(num);
// 
```

如上代码，我们调用了三次`throttleExample`但只打印了一次`[0] 1`。**节流函数顾名思义就像是控制水流的函数，让水流匀速流动的函数。**也就是说在一定时间内，函数不管调用多少次，实际只执行**第一次调用**。

### 防抖函数

函数防抖让函数在”调用’’之后的一段时间后生效，应用场景:

1. 输入框输入事件；
2. window.resize事件，窗口大小调整事件；
3. 滚动事件；

和节流函数相似防抖函数同样需要两个参数：

1. 回调函数；
2. 延迟调用时间；

实现如下：

```js
/**
 * @param {function} fun 调用函数
 * @param {number} delay 延迟调用时间
 * @param {array} args 剩余参数
 */
const debouce = function(fun, delay, ...args) {
  let timer = null;
  return function(...rest) {
    clearTimeout(timer);
    timer = setTimeout(() => {
        fun.apply(this, [...args, ...rest]);
    }, delay);
  }
}
```

解释下上面代码，在外层函数声明一个timer变量，内层函数设置定时器，在delay时间后执行该函数，并将定时器返回的标志保存在timer里面，在此期间一旦有调用，就取消定时器，重新开始定时。我们测试下：

```js
var obj = { a: 1 };
var num = 0;
//实例
var debouceExample = debouce(function(...rest) {
  console.log(rest, this.a);
  num++;
}.bind(obj), 1000);
//调用
debouceExample(num);
debouceExample(num);
debouceExample(num);
```

如上代码调用三次`debouceExample`函数，**1秒后**打印一次`[0] 1`。防抖函数顾名思义防止函数抖动，在一定时间内不管调用多少次，执行的都会是**最后一次的调用**。而且最后一次的调用时间是在一定时间之后。

### 结论

不管是节流函数还是防抖函数目的都是为了防止某个函数多次调用造成的bug或是空间的浪费。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)