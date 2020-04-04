---
title: 学习Javascript之模拟实现new
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文1021字，阅读大约需要5分钟。

**总括:**  本文对new进行了一个简单介绍，然后使用一个函数模拟实现了new操作符做的事情。

- 参考文档：[new 运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**人生是没有毕业的学校。**

<!-- more -->

## 正文

`new`是JS中的一个关键字，用来将构造函数实例化的一个运算符。例子：

```js
function Animal(name) {
  this.name = name;
}
Animal.prototype.sayName = function() {
  console.log("I'm " + this.name);
}
var cat = new Animal('Tom');
console.log(cat.name); // Tom
console.log(cat.__proto__ === Animal.prototype); // true
cat.sayName(); // I'm Tom
```

从上面的例子可以得出两点结论：

1. `new`操作符实例化了一个对象；
2. 这个对象可以访问构造函数的属性；
3. 这个对象可以访问构造函数原型上的属性；
4. 对象的**\__proto__**属性指向了构造函数的原型；

由于`new`是关键字，我们只能去声明一个函数去实现`new`的功能，首先实现上面的三个特性，**第一版代码**如下：

附：对原型原型链不熟悉的可以先看[理解Javascript的原型和原型链](https://blog.damonare.cn/2020/02/11/%E7%90%86%E8%A7%A3Javascript%E7%9A%84%E5%8E%9F%E5%9E%8B%E5%92%8C%E5%8E%9F%E5%9E%8B%E9%93%BE/#more)。

```js
// construct: 构造函数
function newFunction() {
  var res = {};
  // 排除第一个构造函数参数
  var construct = Array.prototype.shift.call(arguments);
  res.__proto__ = construct.prototype;
  // 使用apply执行构造函数，将构造函数的属性挂载在res上面
  construct.apply(res, arguments);
  return res;
}
```

我们测试下：

```js
function newFunction() {
  var res = {};
  var construct = Array.prototype.shift.call(arguments);
  res.__proto__ = construct.prototype;
  construct.apply(res, arguments);
  return res;
}
function Animal(name) {
  this.name = name;
}
Animal.prototype.sayName = function() {
  console.log("I'm " + this.name);
}
var cat = newFunction(Animal, 'Tom');
console.log(cat.name); // Tom
console.log(cat.__proto__ === Animal.prototype); // true
cat.sayName(); // I'm Tom
```

一切正常。`new`的特性实现已经80%，但`new`还有一个特性：

```js
function Animal(name) {
    this.name = name;
    return {
        prop: 'test'
    };
}
var cat = new Animal('Tom');
console.log(cat.prop); // test
console.log(cat.name); // undefined
console.log(cat.__proto__ === Object.prototype); // true
console.log(cat.__proto__ === Animal.prototype); // false
```

如上，如果构造函数`return`了一个对象，那么`new`操作后返回的是构造函数`return`的对象。让我们来实现下这个特性，**最终版代码**如下：

```js
// construct: 构造函数
function newFunction() {
  var res = {};
  // 排除第一个构造函数参数
  var construct = Array.prototype.shift.call(arguments);
  res.__proto__ = construct.prototype;
  // 使用apply执行构造函数，将构造函数的属性挂载在res上面
  var conRes = construct.apply(res, arguments);
  // 判断返回类型
  return conRes instanceof Object ? conRes : res;
}
```

测试下：

```js
function Animal(name) {
  this.name = name;
  return {
    prop: 'test'
  };
}
var cat = newFunction(Animal, 'Tom');
console.log(cat.prop); // test
console.log(cat.name); // undefined
console.log(cat.__proto__ === Object.prototype); // true
console.log(cat.__proto__ === Animal.prototype); // false
```

以上代码就是我们最终对`new`操作符的模拟实现。我们再来看下官方对`new`的解释

引用MDN对`new`运算符的定义：

> **`new` 运算符**创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。

`new`操作符会干下面这些事：

1. 创建一个空的简单JavaScript对象（即`{}`）；
2. 链接该对象（即设置该对象的构造函数）到另一个对象 ；
3. 将步骤1新创建的对象作为`this`的上下文 ；
4. 如果该函数没有返回对象，则返回`this`。

4条都已经实现。还有一个更好的实现，就是通过`Object.create`去创建一个空的对象:

```js
// construct: 构造函数
function newFunction() {
  // 通过Object.create创建一个空对象；
  var res = Object.create(null);
  // 排除第一个构造函数参数
  var construct = Array.prototype.shift.call(arguments);
  res.__proto__ = construct.prototype;
  // 使用apply执行构造函数，将构造函数的属性挂载在res上面
  var conRes = construct.apply(res, arguments);
  // 判断返回类型
  return conRes instanceof Object ? conRes : res;
}
```

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)