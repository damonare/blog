---
title: 学习Javascript之深浅拷贝
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文2895字，阅读大约需要12分钟。

**总括:**  本文介绍了JS中的深浅拷贝方法。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**事业常成于坚忍，毁于急躁。**

<!-- more -->

## 正文

Javascript中不同数据类型存储有两种方式，一种是基础数据类型的存储，一种是引用数据类型(Object)的存储。基础数据类型的存储在**栈**中，引用数据类型存储在**堆**中。举个例子：

```js
var a = null;
var b = undefined;
var c = 123;
var d = '123';
var e = true;
var f = { prop: 1 };
var g = f;
```

上面代码中的变量在内存中的存储方式如下图所示：

![堆栈内存](https://image.damonare.cn/%E5%A0%86%E6%A0%88.png)

堆内存和栈内存大概就长这个样子，在引擎内部，对象的值存储方式是多种多样的，一般不会直接存储在对象内部。对象内部只是存储这种属性的名称，其实就是一个指针，指向这些值存储的真正位置，他们共同构成了图中Object区域。

总结来说，**栈内存是一对一的关系，堆内存往往是一对多的关系**。

看一个实例：

```js
var obj = {
  a: 1
};
var o = obj;
o.a = 2;
console.log(obj.a); // 2
console.log(obj.a === o.a); // true
var str = '123';
var string = str;
str = '456';
console.log(string); // '123'
console.log(str === string); // false
```

如上代码，变量`obj`赋值给了变量`o`，更改变量`o`的属性发现同样更改了`obj`，这是因为不管是变量`obj`还是变量`o`都指向同一片内存空间，变量`o`和变量`obj`保存的只是一个指针。虽然这样节省了内存空间，但可能也会导致一些bug。因为他们是一对多的关系，导致如果两个变量指向同一片内存地址，一个更改了，可能会导致另一个变量的使用场景产生bug。

本文所说的深浅拷贝就是针对这个问题来进行讲解的。

**深拷贝**：将B对象拷贝到A对象中，包括B里面的子对象；

**浅拷贝**：将B对象拷贝到A对象中，不包括B里面的子对象；

#### 浅拷贝

浅拷贝的实现比较简单，将对象遍历一遍就OK了：

```js
function shallowCopy(obj) {
  var res = {};
  for(var prop in obj) {
    if (obj.hasOwnProperty(prop)) {
      res[prop] = obj[prop];
    }
  }
  return res;
}
```

看下浅拷贝可能会导致的问题：

```js
var obj1 = {
  a: 1,
  b: [0, 1]
};
var obj2 = shallowCopy(obj1);
obj2.b.push(2);
console.log(obj1 === obj2); // false
console.log(obj1.b); // [0, 1, 2]
console.log(obj1.b === obj2.b); // true
```

如上，更改`obj2.b`这个属性，`obj1.b`也更改了，就是因为`obj1.b`和`obj2.b`指向了同一个内存地址，完全相等。而深拷贝应用场景最核心的就是为了解决这个问题。

### 深拷贝

接下来是本文的重点，总结实现深拷贝的几种方法。

#### 1. JSON API

深拷贝最简单的方式就是使用`JSON.parse`和`JSON.stringify`来进行转换：

```js
function deepCopy(obj) {
  return JSON.parse(JSON.stringify(obj));
}
```

看下例子：

```js
var obj1 = {
  a: 1,
  b: [0, 1]
};
console.time('JSON耗时：');
var obj2 = deepCopy(obj1);
console.timeEnd('JSON耗时：');
obj2.b.push(2);
console.log(obj1 === obj2); // false
console.log(obj1.b); // [0, 1]
console.log(obj1.b === obj2.b); // false
// JSON耗时：: 0.057373046875ms
```

如上，`obj1`和`obj2`完全独立，分别指向一个独立的内存地址，更改`obj1`不会对`obj2`产生影响。这种方法简单便捷，代码量少，为很多场景青睐。但这种方式存在一个问题就是被复制对象的原型都丢失了。看例子：

```js
function Foo(name) {
  this.name = name;
}
var obj1 = new Foo('damonare');
var obj2 = deepCopy(obj1);
console.log(obj1.__proto__ === Foo.prototype); // true
console.log(obj2.__proto__ === Foo.prototype); // false
console.log(obj2.__proto__ === Object.prototype); // true
```

如上复制后的`obj2`原型已经指向了`Object`，丢失了原型。当然可能很多的业务场景并不需要如此的严格。

#### 2. 递归

递归的实现方式如下：

```js
function deepCopy(obj){
    var newobj = obj.constructor === Array ? [] : {};
    for (var i in obj) {
      newobj[i] = typeof obj[i] === 'object' ? 
        deepCopy(obj[i]) : obj[i]; 
    }
    return newobj;
};
```

上面方法只针对数组和对象进行深复制：

```js
function Foo(name) {
  this.name = name;
}
var obj1 = new Foo('damonare');
console.time('递归耗时：');
var obj2 = deepCopy(obj1);
console.timeEnd('递归耗时：');
// 递归耗时：: 0.067138671875ms
console.log(obj1.__proto__ === Foo.prototype); // true
console.log(obj2.__proto__ === Foo.prototype); // false
console.log(obj2.__proto__ === Object.prototype); // true
```

#### 循环引用问题

所谓循环引用即下面这种情况：

```js
var o = {};
o.a = o;
```

o引用a，a引用o，这是最典型的循环引用案例，循环引用带来的问题就是两个变量始终无法被释放，从而导致内存泄露。这里的解决方案也很easy，就是在外部声明一个变量去保存当前的引用：

```js
var map = new WeakMap();
function deepCopy(obj){
    if (map.has(obj)) return obj;
    map.set(obj, 1);
    var newobj = obj.constructor === Array ? [] : {};
    for (var i in obj) {
        newobj[i] = typeof obj[i] === 'object' ? 
        deepCopy(obj[i]) : obj[i]; 
    }
    return newobj;
};
```

### 总结

归根结底，不管是JSON还是递归方法去深拷贝对象，都不是严格意义上的深拷贝，因为对象的原型都没有进行一层深拷贝。但简单的实现往往可以满足80%以上的使用场景，如果有针对对象和数组之外的深拷贝实现场景，推荐因为`loadash` 的`cloneDeep`方法，这是一个面向未来的深拷贝实现，对ES6一些新的内置对象都有很好支持和实现。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)