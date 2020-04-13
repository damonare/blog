---
title:  JS设计模式之抽象工厂模式
categories:
  - 原创
tags:
  - JavaScript
  - 设计模式
  - 创建型模式
toc: true
comments: true
---

## 前言

> 本文735字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的抽象工厂模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**一人花开，一人花落，这些年从头到尾，无人问询。**

<!-- more -->

## 正文

抽象工厂模式(Abstract Factory): **围绕一个超级工厂创建其他工厂**。

在Javascript中`abstract`仍然是个保留字，因此在目前的Javascript还无法直接去创建一个“抽象类”。但像Typescript中这种JS超集编程语言中已经有了相关的概念。

###  抽象类

在目前的Javascript中也可以模拟一个抽象类的实现：

```js
function Cat() {
  if (this instanceof Cat) {
    return new Error('抽象类不能被实例化');
  }
}
Cat.prototype.getName = function() {
  return new Error('抽象方法不能调用');
}
```

如上通过抛出错误的方式来给出提示。抽象类的主要作用就是**约束实例对象必须具有的某些方法**。如果这些必要的方法从父类(这里的Cat)继承过来而没有具体的去实现，那么实例化对象便会调用父类的方法，此时有一个友好的提示就很有帮助了。比如：

```js
function Cat() {
  if (this instanceof Cat) {
    return new Error('抽象类不能被实例化');
  }
}
Cat.prototype.getName = function() {
  return new Error('抽象方法不能调用');
}
function Tom() {}
Tom.prototype = Object.create(Cat.prototype);
var tom = new Tom();
tom.getName(); // Error: 抽象方法不能调用
```

如上，子类Tom通过继承抽象类Cat来约束Tom类需要重写的一些方法 。

### 抽象工厂

前面说过工厂模式，将工厂模式应用到抽象类，就是本文所说的抽象工厂模式。

```js
var Factory = function(type) {
  // 判断抽象工厂中是否有该抽象类
  if (typeof Factory[type] === 'function') {
    // 缓存类
    function F() {}
    F.prototype = Object.create(Factory[type].prototype);
    // 实例化
    return new F();
  }
}
Factory.Cat = function() {
  if (this instanceof Factory.Cat) {
    return new Error('抽象类不能被实例化');
  }
}
Factory.Cat.prototype.getName = function() {
  return new Error('抽象方法不能调用');
}
Factory.Dog = function() {
  if (this instanceof Factory.Dog) {
    return new Error('抽象类不能被实例化');
  }
}
Factory.Dog.prototype.getName = function() {
  return new Error('抽象方法不能调用');
}
Factory.Mouse = function() {
  if (this instanceof Factory.Mouse) {
    return new Error('抽象类不能被实例化');
  }
}
Factory.Mouse.prototype.getName = function() {
  return new Error('抽象方法不能调用');
}
```

如上，就是一个抽象工厂模式的实现。我们写个单例测试下：

```js
var tom = Factory('Cat');
tom.getName(); // Error: 抽象方法不能调用
```

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)