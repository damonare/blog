---
title:  JS设计模式之建造者模式
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

> 本文575字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的建造者模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**愿每次回忆，对生活都不感到负疚**。

<!-- more -->

## 正文

建造者模式(Builder Pattern):**使用多个简单的对象一步一步构建成一个复杂的对象**。

建造者模式是比较简单的一种设计模式，和工厂模式比较相似，但也有些许的区别。工厂模式侧重于创建一个实例对象，不会关心对象创建的过程，而建造者模式更为复杂点，建造者模式侧重于对象的创建过程，比如创建一个人的时候，这个人名字叫什么，多大年纪都是建造者模式需要考虑的事情，这个人也可以有一个工作属性，可能会很复杂单独提取为一个工作类，最终我们的人是由一个个类拼接起来，我们使用的时候直接调用即可。

### 简单的例子

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}
Person.prototype = {
  getName() { return this.name },
  setName(name) { this.name = name; },
  getAge() { return this.age; },
  setAge(age) { this.age = age; },
}
var person = new Person('旺财', 2);
person.getAge(); // 2
```

如上就是一个建造者模式的应用，给了这个动物类需要的名字和年龄属性。

#### 建造者模式

```js
function Work(type) {
  this.type = type;
}
Work.prototype.getWork = function() {
  return this.type;
}
```

组合到上面的Person类中：

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.work = new Work('工程师');
}
Person.prototype = {
  getName() { return this.name },
  setName(name) { this.name = name; },
  getAge() { return this.age; },
  setAge(age) { this.age = age; },
}
var person = new Person('旺财', 2);
person.getAge(); // 2
person.work.getWork(); // 工程师
```

一个较为完整的Person类就像积木一样被"建造"出来了。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)