---
title:  JS设计模式之单例模式
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

> 本文818字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的单例模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**及时宜自勉，岁月不待人。**

<!-- more -->

## 正文

单例模式(Singleton pattern)：**限制一个类在全局范围内只有一个实例**。

顾名思义，单例模式就是要保证一个类只有一个实例，应用场景很多，比如jQuery中的`$`和`jQuery`对象，浏览器中的`window`对象以及全局缓存等都是单例模式的一种实现。单例模式实现起来并不复杂，下面我们就来看下几种单例模式的实现方式。

###  简单实现

保障一个类只有一个实例：

```js
function Singleton(name) {
  // 判断是否存在实例
  if (typeof Singleton.instance === 'object') {
    return Singleton.instance;
  }
  // 其它内容
  this.name = name;
  // 缓存
  Singleton.instance = this;
  // 隐式返回this
}
```

通过将实例对象缓存在Singleton上面来判断该类是否已经实例化过。测试下：

```js
// 测试
var single = new Singleton('测试');
var single2 = new Singleton('测试2');
console.log(single === single2); // true
```

如上，两个实例完全相等，因为实际上他们是同一个实例的引用，单例模式通过这种方式保证了一个类在全局只会有一个实例的特性。

### 惰性单例

所谓的惰性单例就是说在单例需要的时候才会创建：

```js
var LazySingle = function() {
  function Singleton(args = {}) {
    this.name = args.name;
  }
  Singleton.prototype.getName = function() {
    return this.name;
  }
  var _instance = null;
  return {
    getInstance: function() {
      if (_instance === undefined) {
        instance = new Singleton(args);
      }
      return _instance;
    }
  }
}
```


### 命名空间

了解`Typescript`的童鞋知道，命名空间是`Typescript`早期的一个特性，也叫`namespace`，目前已被弃用。它解决的问题是**避免命名冲突**，比如小明写的代码都挂载在`xiaoming`下面，访问小明写的方法或是属性需要通过`xiaoming.xx`(xx为变量名)来调用。简单实现：

```js
var xiaoming = {
  name: '小明',
  css: function() {},
  js: function() {}
}
```

命名空间是模块化的基础，也正是因为`ES6`引入了`import`和`export`，`Typescript`弃用了`namespace`。但它的存在仍旧有意义。结合命名空间和惰性单例我们看下单例模式的最佳实现：

```js
var SingletonModule = (function () {
    //参数：传递给单例的一个参数集合
    function Singleton(args = {}) {
        this.name = args.name;
    }
    Singleton.prototype.getName = function() {
      return this.name;
    }
    //实例容器
    var instance;
    // 私有变量
    var _prop = 'SingletonModule';
    var _static = {
        prop: _prop,
        //获取实例的方法
        //返回Singleton的实例
        getInstance: function (args) {
            if (instance === undefined) {
                instance = new Singleton(args);
            }
            return instance;
        }
    };
    return _static;
})();
```

解释下上面代码，我们通过一个自执行函数将代码包裹在一个函数作用域中，避免了函数中变量对外部造成污染，然后定义了一个`Singleton`类和变量`instance`，利用闭包将`instance`存储，在`_static`的`getInstance`方法中进行逻辑判断，保证类`Singleton`只有一个实例。测试下：

```js
var single = SingletonModule.getInstance({ name: 'xiaoming' });
var single2 = SingletonModule.getInstance({ name: 'xiaohua' });
console.log(single === single2); // 输出 true
console.log(single.name); // xiaoming
console.log(single2.name); // xiaoming
```

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)