---
title:  JS设计模式之策略模式
categories:
  - 原创
tags:
  - JavaScript
  - 设计模式
  - 行为型模式
toc: true
comments: true
---

## 前言

> 本文969字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的迭代器模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**读过一本好书，像交了一个益友**。

<!-- more -->

## 正文

迭代器模式(Iterator pattern):   **在不暴露对象内部结构的同时，可以顺序的访问聚合对象内部的元素**。

迭代器分为两种，一种是内部迭代器，一种是外部迭代器。它的目的是为了**优化循环代码**，基本每一种语言中都有循环这种结构，这种结构虽然便利，但一旦重复性循环代码过多就会导致代码臃肿不堪难以维护。

### 内部迭代器

所谓内部迭代器，在Javascript中有很多现成的实现，比如我们常用的`Array.prototype.forEach`就是一种内部迭代器的实现，这个方法内部实际上也是一种循环：

```js
const arr = [1, 2, 3];
// 常规实现遍历
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}
// 使用forEach
arr.forEach((a) => console.log(a));
```

`forEach`的polyfill实现实际上是这样的(代码来自MDN)：

```js
if (!Array.prototype.forEach) {

  Array.prototype.forEach = function(callback, thisArg) {
    var T, k;
    if (this == null) {
      throw new TypeError(' this is null or not defined');
    }
    // 1. 调用Object方法传入this值生成当前对象
    var O = Object(this);
    // 2. Let lenValue be the result of calling the Get() internal
    // method of O with the argument "length".
    // 3. Let len be toUint32(lenValue).
    var len = O.length >>> 0;
    // 4. If isCallable(callback) is false, throw a TypeError exception. 
    // See: http://es5.github.com/#x9.11
    if (typeof callback !== "function") {
      throw new TypeError(callback + ' is not a function');
    }
    // 5. If thisArg was supplied, let T be thisArg; else let
    // T be undefined.
    if (arguments.length > 1) {
      T = thisArg;
    }

    // 6. Let k be 0
    k = 0;

    // 7. Repeat, while k < len
    while (k < len) {

      var kValue;

      // a. Let Pk be ToString(k).
      //    This is implicit for LHS operands of the in operator
      // b. Let kPresent be the result of calling the HasProperty
      //    internal method of O with argument Pk.
      //    This step can be combined with c
      // c. If kPresent is true, then
      if (k in O) {

        // i. Let kValue be the result of calling the Get internal
        // method of O with argument Pk.
        kValue = O[k];

        // ii. Call the Call internal method of callback with T as
        // the this value and argument list containing kValue, k, and O.
        callback.call(T, kValue, k, O);
      }
      // d. Increase k by 1.
      k++;
    }
    // 8. return undefined
  };
}
```

可以看到forEach通过封装，回调函数，在保证内部对象不被暴露的同时可以访问内部元素。

### 外部迭代器

外部迭代器调用方式相比内部迭代器更为复杂，但它的适用面更广，也能满足更多的需求。看下一个外部迭代器的简单实现：

```js
const Iterator = function(arr = []) {
  let nextIndex = 0;
  const next = function() {
    return nextIndex < arr.length ? {
      value: arr[nextIndex++],
      done: false
    } : {
      value: null,
      done: true
    }
  }
  return {
    next,
    first: function() {
      return arr[0];
    }
    last: function() {
      return arr[arr.length - 1];
    },
    current: function() {
      return arr[nextIndex];
    }
  }
}
```

然后迭代一个数组：

```js
const arr = [1, 2, 3];
var iteratorArr = Iterator(arr);
console.log(iteratorArr.next()); // {value: 1, done: false}
console.log(iteratorArr.next()); // {value: 2, done: false}
console.log(iteratorArr.next()); // {value: 3, done: false}
console.log(iteratorArr.next()); // {value: null, done: true}
console.log(iteratorArr.first()); // 1
console.log(iteratorArr.current()); // undefined
console.log(iteratorArr.last()); // 3
```

如上实现，访问一个数组的元素时，就做到了不会暴露内部属性和方法，而且代码优雅容易理解。ES6中已经原生支持了迭代器，ES6 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)