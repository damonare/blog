---

title:  JS设计模式之工厂模式
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

> 本文867字，阅读大约需要5分钟。

**总括:**  在Javascript中实现23种经典设计模式的工厂模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**与有肝胆人共事，从无字句处读书。**

<!-- more -->

## 正文

工厂模式(Factory Method pattern)：**不同输入返回不同类的实例对象**。

### 简单工厂模式

假设有一个创建各种小动物的需求，我们需要声明猫类(Cat)，狗类(Dog)等。可以这样做：

```js
function Cat(name) {
  this.name = name;
  this.type = 'cat';
}
Cat.prototype.getName = function() {
  return this.name;
}
function Dog(name) {
  this.name = name;
  this.type = 'dog';
}
Dog.prototype.getName = function() {
  return this.name;
}
```

这个时候我们发现每次创建某个动物的时候需要找到某个动物的类，然后看完代码再去按照要求创建，当然我们这里的代码十分的简单，但平时业务中的代码可能会很复杂。思考下他们的共性，可以抽象出一个工厂方法，也就是简单工厂模式的一种实现：

```js
function AnimalFactory(type, name) {
  switch(type) {
    case 'Cat':
      return new Cat(name);
    case 'Dog':
      return new Dog(name);
  }
}
```

经过这样的一层封装我们调用`Cat`和`Dog`类就可以这样做了：

```js
var tom = AnimalFactory('Cat', 'Tom');
var wangcai = AnimalFactory('Dog', '旺财');
console.log(tom.name); // Tom
console.log(wangcai.name); // 旺财
```

如上通过一次简单的封装我们实现了不必在乎类具体的实现，按需去获取实例对象的目的。

### 工厂模式

假设现在我们又多了一个`Mouse`类需要新增进来，我们面临的问题是啥？1. 新增一个`Mouse`类；2. 修改`AnimalFactory`函数；增加类需要修改两处地方，这不是我们的追求。而工厂方法模式就是解决这个问题，工厂模式的核心思想就是要将对象的实际创建工作tuichu我们用工厂模式重写上面的`AnimalFactory`工厂函数：

```js
var Factory = function(type, name) {
  // 避免该类被直接形如Factory()调用产生无法预期的bug
  if (this instanceof Factory) {
    return new this[type](name);
  } else {
    return new Factory(type, name);
  }
}
Factory.prototype =  {
  Dog: (function() {
    function Dog(name) {
      this.name = name;
      this.type = 'dog';
    }
    Dog.prototype.getName = function() {
      return this.name;
    }
    return Dog;
  })(),
  Cat: (function() {
    function Cat(name) {
      this.name = name;
      this.type = 'cat';
    }
    Cat.prototype.getName = function() {
      return this.name;
    }
    return Cat;
  })()
};
```

此时我们再创建对象：

```js
var tom = Factory('Cat', 'Tom');
var wangcai = Factory('Dog', '旺财');
console.log(tom.name); // Tom
console.log(wangcai.name); // 旺财
```

然后新增Mouse类：

```js
var Factory = function(type, name) {
  if (this instanceof Factory) {
    return new this[type](name);
  } else {
    return new Factory(type, name);
  }
}
Factory.prototype =  {
  Dog: (function() {
    function Dog(name) {
      this.name = name;
      this.type = 'dog';
    }
    Dog.prototype.getName = function() {
      return this.name;
    }
    return Dog;
  })(),
  Cat: (function() {
    function Cat(name) {
      this.name = name;
      this.type = 'cat';
    }
    Cat.prototype.getName = function() {
      return this.name;
    }
    return Cat;
  })(),
  Mouse: (function() {
    function Mouse(name) {
      this.name = name;
      this.type = 'mouse';
    }
    Mouse.prototype.getName = function() {
      return this.name;
    }
    return Mouse;
  })()
};
```

如上我们十分轻松的就新增了一个`Mouse`类，只需要关注`Mouse`类的实现，而不用再去兼顾工厂函数的逻辑。调用一下：

```js
var tom = Factory('Cat', 'Tom');
var jerry = Factory('Mouse', 'Jerry');
var wangcai = Factory('Dog', '旺财');
console.log(tom.name); // Tom
console.log(wangcai.name); // 旺财
console.log(jerry.type); // mouse
```

非常的nice~

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)