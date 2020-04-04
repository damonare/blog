---
title: 学习Javascript之高阶函数
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文986字，阅读大约需要4分钟。

**总括:**  本文以初学者的角度来阐述Javascript中高阶函数的概念以及在工作中使用的一些例子。

- 原文地址：[理解Javascript的高阶函数](https://blog.damonare.cn/2020/02/06/%E7%90%86%E8%A7%A3Javascript%E7%9A%84%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0/#more)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**读书必专精不二，方见义理**

<!-- more -->

## 正文

函数式编程是一种如今比较流行的编程范式，它主张将函数作为参数进行传递，然后返回一个没有副作用的函数，说白了，就是希望一个函数只做一件事情。

像Javascript，Haskell，Clojure等编程语言都支持函数式编程。

这种编程思想涵盖了三个重要的概念：

- 纯函数；
- 柯里化；
- 高阶函数；

而这篇文章主要是想向大家讲清楚`高阶函数`这个概念。

#### 什么是高阶函数

高阶函数至少满足以下一个条件：

- 接受一个或多个函数作为输入；
- 输出一个函数；

在Javascript中，函数也是变量，既可以赋值给另一个变量，也可以作为参数传递给另一个函数：

```js
var foo = (a) => console.log(a);
var bar = foo;
bar(1); // 1
var addOne = (n) => console.log(n + 1);
var execFun = (fun, x) => fun(x);
execFun(addOne, 2); // 3
```

而所谓**高阶函数就是那些接受另一个函数作为参数的函数。**以及那些**返回一个函数的函数**。

### 函数作为参数

Javascript有很多内置的高阶函数。比如`Array.prototype.map`、`Array.prototype.reduce`、`Array.prototype.forEach`、`Array.prototype.filter` 、`Array.prototype.sort`、`Array.prototype.find`、`Array.prototype.every`等都是内置的高阶函数。挑几个说明一下：

#### Array.prototype.map

> `map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

**map**函数接受两个参数：**callback**(回调函数)和**thisArg**(处理callback时的this)

**callback**函数接受三个参数：**element**(数组元素)，**index**(数组下标) 和 **array**(当前数组)。

看一个**map**函数应用实例。

**不使用高阶函数的实现：**

```js
const arr1 = [1, 2, 3];
const arr2 = [];
for(let i = 0; i < arr1.length; i++) {
  arr2.push(arr1[i] * 2);
}
// prints [ 2, 4, 6 ]
console.log(arr2);
```

**使用高阶函数的实现：**

```js
const arr1 = [1, 2, 3];
const arr2 = arr1.map(function(item) {
  return item * 2;
});
console.log(arr2);
```

**利用箭头函数再次缩减上述代码**：

```js
const arr1 = [1, 2, 3];
const arr2 = arr1.map(item => item * 2);
console.log(arr2);
```

#### Array.prototype.reduce

> `reduce()` 方法对数组中的每个元素执行一个由您提供的**reducer**函数(升序执行)，将其结果汇总为单个返回值。

**reducer**函数接受两个参数：**callback**(回调函数)和**initialValue**(调用 `callback`函数时的第一个参数的值)

**callback**函数接收4个参数:

1. Accumulator (acc) (累计器)；
2. Current Value (cur) (当前值)；
3. Current Index (idx) (当前索引)；
4. Source Array (src) (源数组)；

**不使用高阶函数的实现：**

```js
const arr = [5, 7, 1, 8, 4];
let sum = 0;
for(let i = 0; i < arr.length; i++) {
  sum = sum + arr[i];
}
// prints 25
console.log(sum);
```

**使用高阶函数的实现：**

```js
const arr = [5, 7, 1, 8, 4];
let sum = arr.reduce((a, b) => a + b, 0);
// prints 25
console.log(sum);
```

观察map和reduce上面两个实例可以发现，应用高阶函数的实现代码更加优雅并且易读，代码量也大幅度缩减，好处远远大于常规实现。

### 输出一个函数

如果一个函数返回一个函数，同样也是高阶函数，举例实现一个类型检查函数：

```js
function checkType(type) {
  return function(obj) {
    return Object.prototype.toString.call(obj) === `[object ${type}]`
  }
}
const isObject = checkType('Object');
const isFunction = checkType('Function');
const isString = checkType('String');
```

如上实现一个checkType函数，该函数返回一个函数来判断具体的数据类型。该函数也是一个高阶函数。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)