---
title:  JS设计模式之访问者模式
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

> 本文514字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的访问者模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**读书给人以快乐、给人以光彩、给人以才干**。

<!-- more -->

## 正文

访问者模式(Visitor pattern):   **针对对象中的元素，定义在不改变对象的前提下访问结构中元素的新方法**。

Javascript中的源生对象构造器其实就是一个访问者，比如在判断一个变量的数据类型的时候，可以通过`Object.prototype.toString.call`的方式，来判断变量的类型。我们来以创建一个类数组对象为例实现一个访问者模式。

```js
const Visitor = (function() {
  return {
    splice() {
      // 调用splice方法，从第二个参数开始算起
      var args = [].splice.call(arguments, 1);
      // 对第一个参数执行
      return [].splice.apply(arguments[0], args);
    },
    push() {
      // 当前第一个元素的长度
      var len = arguments[0].length || 0;
      // 添加的数据从第二个元素开始算起
      var args = this.splice(arguments, 1);
      // 校正length长度
      arguments[0].length = len + arguments.length - 1;
      return [].push.apply(arguments[0], args);
    },
    pop() {
      return [].pop.apply(arguments[0]);
    }
  }
})();
```

如上就是一个简单的类数组对象的实现，实践下：

```js
var o = {};
console.log(o.length); // undefined
Visitor.push(o, 1, 2);
console.log(o.length); // 2
Visitor.push(o, 3, 4);
console.log(o.length); // 4
Visitor.pop(o);
console.log(o.length); // 3
```

访问者模式解决了数据和数据的操作方法之间的耦合，将数据的操作方法和数据独立出来使得操作方法更为方便的访问和拓展。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)