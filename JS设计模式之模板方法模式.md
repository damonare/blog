---
title:  JS设计模式之模板方法模式
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

> 本文718字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的模板方法模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**知之者不如好之者，好之者不如乐之者**。

<!-- more -->

## 正文

模板方法模式(Template method pattern):   **抽象父类实现一些方法，子类具体的去实现这些方法的一种模式**。

在抽象工厂模式中有提过Javascript中实现抽象类的方法，以《Header first 设计模式》中煮茶和煮咖啡为例，看下模板方法模式的应用：

```js
// 煮茶类的实现
function Tea() {}
Tea.prototype.boilWater = function() {
    console.log('煮开水');
}
Tea.prototype.bubbleTea = function() {
    console.log('泡茶');
}
Tea.prototype.pourTea = function() {
    console.log('倒茶');
}
Tea.prototype.addSugar = function() {
    console.log('加糖');
}
Tea.prototype.init = function() {
    this.boilWater();
    this.bubbleTea();
    this.pourTea();
    this.addSugar();
}
var tea = new Tea();
tea.init();
// 煮咖啡类的实现
function Coffee() {}
Coffee.prototype.boilWater = function() {
    console.log('煮开水');
}
Coffee.prototype.bubbleCoffee = function() {
    console.log('泡咖啡');
}
Coffee.prototype.pourCoffee = function() {
    console.log('倒咖啡');
}
Coffee.prototype.addSugar = function() {
    console.log('加糖，加牛奶');
}
Coffee.prototype.init = function() {
    this.boilWater();
    this.bubbleCoffee();
    this.pourCoffee();
    this.addSugar();
}
var coffee = new Coffee();
coffee.init();
```

对比下两者：

| 泡茶   | 泡咖啡       |
| ------ | ------------ |
| 煮开水 | 煮开水       |
| 泡茶   | 泡咖啡       |
| 倒茶   | 倒咖啡       |
| 加糖   | 加糖，加牛奶 |

如上的实现，完全可以抽象出来很多东西：

-   煮开水是相同的操作；
-   泡茶，泡咖啡，可以抽象为泡饮料；
-   倒茶，倒咖啡，可以抽象为倒饮料；
-   加糖，加牛奶，可以抽象为添加调料；

于是这两个类可以抽象为一个父类：

```js
function Beverage() {}
Beverage.prototype.boilWater = function() {
    console.log('煮开水');
}
Beverage.prototype.bubble = function() {
    return new Error('抽象方法，需要子类重写');
}
Beverage.prototype.pour = function() {
    return new Error('抽象方法，需要子类重写');
}
Beverage.prototype.addSome = function() {
    return new Error('抽象方法，需要子类重写');
}
Beverage.prototype.init = function() {
    this.boilWater();
    this.bubble();
    this.pour();
    this.addSome();
}
```

然后煮茶和煮咖啡去继承这个抽象类：

```js
// 煮茶类的实现
function Tea() {}
Tea.prototype = new Beverage();
Tea.prototype.bubble = function() {
    console.log('泡茶');
}
Tea.prototype.pour = function() {
    console.log('倒茶');
}
Tea.prototype.addSome = function() {
    console.log('加糖');
}

var tea = new Tea();
tea.init();
// 煮咖啡类的实现
function Coffee() {}
Coffee.prototype = new Beverage();
Coffee.prototype.bubble = function() {
    console.log('泡咖啡');
}
Coffee.prototype.pour = function() {
    console.log('倒咖啡');
}
Coffee.prototype.addSome = function() {
    console.log('加糖，加牛奶');
}
var coffee = new Coffee();
coffee.init();
```

模板方法模式的核心在于不同类之间的方法复用，实际上也是抽象能力的一种应用，这既是对子类一种行为上的约束，也保证了子类的方法复用和可扩展。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)