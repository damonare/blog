---
title:  JS设计模式之策略模式
categories:
  - 原创
tags:
  - JavaScript
  - 设计模式
  - 结构型模式
toc: true
comments: true
---

## 前言

> 本文759字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的装饰者模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**与有肝胆人共事，从无字句处读书**。

<!-- more -->

## 正文

装饰者模式(Decorator pattern):   **在不改变原对象的基础上，通过对其进行包装拓展使原对象能满足更为复杂的需求**。

#### 简单的例子

在Javascript中这是一种比较常见的设计模式，主要的使用场景是针对某个函数或是对象增加某些特性或是功能。比如：

```js
var a = function() {
  console.log(1);
  // 其它代码
}
var _a = a;
a = function() {
  _a();
  console.log(2);
  // 其它代码
}
a();
// 打印 1
// 打印 2
```

比较现实点的实现比如`window.onload`函数：

```js
window.onload = function() {
  console.log(1);
}
var _onload = window.onload;
window.onload = function() {
  _onload();
  console.log(2);
}
// 打印 1  
// 打印 2
```

为了避免覆盖其它同事写的`onload`函数，往往采用如上的实现，但这样的实现有个弊端在于如果装饰链很长会导致诸如`_onload`这样的中间变量非常多；另外还存在this丢失的问题，直接调用`_onload`会导致内部`this`指向`window`。

```js
var _getElementById = document.getElementById;
document.getElementById = function(id) {
    console.log(1);
    return _getElementById(id);
}
var div = document.getElementById('test');
```

如上代码会报错：

```js
Uncaught TypeError: Illegal invocation
```

原因就在于此时``_getElementById`函数`this`指向`window`而不是`document`。解决方案也很简单，可以把`this`手动传进去。

```js
var _getElementById = document.getElementById;
document.getElementById = function(id) {
    console.log(1);
    return _getElementById.call(document, id);
}
var div = document.getElementById('test');
```

上面的代码就正常了。

#### ES7的装饰器

装饰者模式的好用和广泛使用使得ES7中直接引入了这个模式的实现。其实它是个语法糖，支持对类和方法的装饰，实际的应用代码类似于：

```js
@decorator
class A {}

// 等同于

class A {}
A = decorator(A) || A;
```

#### 和代理模式的对比

代理模式和装饰者模式都保留了原对象或是方法的引用，然后对原对象做处理。区别在于他们两者的目的。

-   代理模式的目的是为了提供一个替代者，当不能直接访问原对象或是不符合需要的时候，为原对象提供一个替代者，在访问真正的原对象之前也可以做一些额外的事情；
-   装饰者模式的目的是为了添加一种行为，为原对象添加一种特性。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)