---
title:  JS设计模式之代理模式
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

> 本文745字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的代理模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**满招损，谦受益**。

<!-- more -->

## 正文

代理模式(Proxy pattern):   **为一个对象提供一个代理，以此来控制对该对象的访问**。

### 简单的例子

现在的明星都有经纪人，想要和明星有商业合作都得联系他的经纪人，这里的经纪人就好比我们的代理对象，明星是我们的目标对象。

```js
// 明星蔡徐坤
let Caixukun = function(amount) {
  this.amount = amount;
	this.playBasketBall = function() {
     console.log('想要我打篮球要花' + this.amount + '元');
  }
}
// 经纪人
let Jingjiren = function (amount) {
  this.sendMessage = function() {
    new Caixukun(amount).playBasketBall();
  }
};
let test = new Jingjiren(1000);
test.sendMessage();
// 想要我打篮球要花 1000元
```

如上代码要想让蔡徐坤打篮球得经过经纪人，初看这里经纪人多此一举，但实际上在经纪人这里我们可以处理很多逻辑，比如：

```js
// 明星蔡徐坤
let Caixukun = function(amount) {
  this.amount = amount;
	this.playBasketBall = function() {
     console.log('想要我打篮球要花' + this.amount + '元');
  }
}
// 经纪人
let Jingjiren = function (amount) {
  this.sendMessage = function() {
    if (amount <= 1000) {
      console.log('想要我坤打篮球' + amount + '元是不够的的');
    } else {
       new Caixukun(amount).playBasketBall();
    }
  }
};
let amount_1000 = new Jingjiren(1000);
amount_1000.sendMessage();
let amount_10000 = new Jingjiren(10000);
amount_10000.sendMessage();
// 想要我坤打篮球1000元是不够的的
// 想要我打篮球要花10000元
```

如上经纪人可以给明星做出很多的逻辑判断，从而阻挡对原对象的很多无效访问。

### 缓存代理

代理模式比较有用的一个场景就是在缓存上面,看一个计算数字之和的函数：

```js
function sum() {
  var a = 0;
  for (var  i = 0; i < arguments.length; i++) {
    a += arguments[i];
  }
  console.log(a);
  return a;
}
sum(1, 2); // 3
sum(1, 2, 3); // 6
```

加入代理：

```js
var proxySum = (function() {
  var cache = {};
  return function() {
    var key = [].join.call(arguments, ',');
    if (!isNaN(cache[key])) {
       console.log('该计算结果已缓存:', cache[key]);
      return;
    }
    return cache[key] = sum.apply(this, arguments);
  }
})();
proxySum(1, 2, 3, 4); // 10
proxySum(1, 2, 3, 4); // 该计算结果已缓存: 10
```

如上当第二次调用`proxySum(1, 2, 3, 4)`的时候就会返回缓存结果；

### ES6中的Proxy

在ES6中已经原生的支持了这种模式，通过提供一个名为`Proxy`构造函数的方式去实现对象的代理，`Proxy`实现了对对象访问的一切控制，vue3也使用了Proxy对对象的方法或属性进行监听。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)