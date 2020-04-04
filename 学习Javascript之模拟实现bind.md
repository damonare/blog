---
title: 学习Javascript之模拟实现bind
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文1703字，阅读大约需要5分钟。

**总括:**  本文模拟实现了bind方法的更改this，传参和绑定函数作为构造函数调用时this失效的特性。

- 参考文档：[Function.prototype.bind()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**愿每次回忆，对生活都不感到负疚。**

<!-- more -->

## 正文

`bind`和`call`，`apply`的作用类似，都是用来更改函数的this值的，不同的是，`call`和`apply会`直接把函数执行，但`bind`会返回一个函数，我们称之为绑定函数:

```js
function foo(b = 0) {
	console.log(this.a + b);
}
var obj1  = {
  a: 1
};
foo.call(obj1, 1); // 2
foo.apply(obj1, [1]); // 2
var bar = foo.bind(obj1, 1);
bar(); // 2
```

看下`bind()`函数最重要的两个特性：

1. 更改this；
2. 传参；

### 更改this&传参

更改`this`我们可以借助之前[模拟实现过的call和apply](https://blog.damonare.cn/2020/02/17/%E5%AD%A6%E4%B9%A0Javascript%E4%B9%8B%E6%A8%A1%E6%8B%9F%E5%AE%9E%E7%8E%B0call,apply/#more)的方式来实现，传参就必要我们借助[闭包](https://blog.damonare.cn/2017/01/21/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84%E9%97%AD%E5%8C%85/#more)来实现了，我们看下我们实现的**第一版代码**：

```js
Function.prototype.bind2 = function(context) {
  var _this = this;
  return function() {
		context.func = _this;
    context.func();
    delete context.func;
  }
}
```

传参需要将外层函数(bind里面的参数)和传到绑定函数中的参数全部拼接到一起，这就需要借助闭包来实现，更改`this`我们可以直接使用`apply`来实现，将参数放到一个数组中传到绑定函数中，我们的**第二版代码**：

```js
Function.prototype.bind2 = function(context) {
  // 保存上层函数this值
  var _this = this;
  // 保存上层函数的参数
  var args = [].slice.call(arguments, 1);
  return function() {
    // 将参数拼接
		var _args = args.concat([].slice.call(arguments));
    // 利用apply更改this，并把拼接的参数传到函数中
    _this.apply(context, _args);
  }
}
```

现在我们再来测试下：

```js
function foo(b = 0) {
	console.log(this.a + b);
}
var obj1  = {
  a: 1
};
// 我们成为绑定函数
var bar1 = foo.bind2(obj1, 1);
bar1(); // 2
var bar2 = foo.bind2(obj1);
bar2(); // 1
```

两个特性成功实现，完美。 然后重头戏在下面：

### this失效

目前更改`this`和传递参数两个特性已经实现，如果截止到这就结束了，就不会单独为模拟实现`bind()`写一篇博客了，`bind`还有一个特性，即当绑定函数作为构造函数使用的时候里面的`this`就会失效。例子：

```js
function Animal(name) {
  this.name = name;
}
var obj = {
	name: 'test'
};
var cat = new Animal('Tom');
var Animal2 = Animal.bind(obj);
var cat2 = new Animal2('Tom');
console.log(cat); // {name: "Tom"}
console.log(cat2); // {name: "Tom"}
console.log(obj); // {name: "test"}
```

我们解释下上面的代码，我们首先使用构造函数Animal实例化了一个cat对象，cat对象的内容如上打印，然后我们声明了一个Animal2来保存对象obj的绑定函数Animal.bind(obj)。实例化Animal2后发现cat2内容和cat是一样的，此时我们发现使用`bind`绑定的`this`失效了，因为我们传进去obj对象的内容并没有发生改变。我们再来看下我们目前的bind2的表现：

```js
Function.prototype.bind2 = function(context) {
  // 保存上层函数this值
  var _this = this;
  // 保存上层函数的参数
  var args = [].slice.call(arguments, 1);
  return function() {
    // 将参数拼接
    var _args = args.concat([].slice.call(arguments));
    // 利用apply更改this，并把拼接的参数传到函数中
    _this.apply(context, _args);
  }
}

function Animal(name) {
  this.name = name;
}
var obj = {
  name: 'test'
};
var mouse = new Animal('jerry');
var Animal3 = Animal.bind2(obj);
var mouse2 = new Animal3('jerry');
console.log(mouse); // {name: "jerry"}
console.log(mouse2); // {}
console.log(obj); // {name: 'jerry'}
```

我们先看下这里的Animal3实际的返回函数，它是bind2方法的这一部分：

```js
function() {
    // 将参数拼接
    args.concat([].slice.call(arguments));
    // 利用apply更改this，并把拼接的参数传到函数中
    _this.apply(context, args);
 }
```

如上，代码中我们`new Animal3('jerry')`实际上就是对上面的这个函数的实例化，这就是为什么mouse2是个空对象的原因。然后由于前面bind2绑定的是obj，`_this.apply(context, args)`这行代码就把obj对象的name属性给更改了，context指向obj，_this指向Animal函数。而我们的目标是希望当绑定函数被当做构造函数使用的时候，context不会指向被传进来的上下文对象(比如这里的obj)而是指向绑定函数的`this`。我们的问题转移到这上面上了：如何在一个函数中去判断这个函数是被正常调用还是被当做构造函数调用的。答案是通过原型。不熟悉原型的同学可以移步:[理解Javascript的原型和原型链](https://blog.damonare.cn/2020/02/11/%E7%90%86%E8%A7%A3Javascript%E7%9A%84%E5%8E%9F%E5%9E%8B%E5%92%8C%E5%8E%9F%E5%9E%8B%E9%93%BE/#more)。例子：

```js
function Animal() {
  console.log(this.__proto__ === Animal.prototype);
}
new Animal(); // true
Animal(); // false
```

因此可以把我们可以在我们返回的函数里面进行这样的判断，这是我们**第三版代码**：

```js
Function.prototype.bind2 = function(context) {
  // 保存上层函数this值
  var _this = this;
  // 保存上层函数的参数
  var args = [].slice.call(arguments, 1);
  function Func() {
    // 将参数拼接
    var _args = args.concat([].slice.call(arguments));
    _this.apply(this.__proto__ === Func.prototype ? this : context, _args);
  }
  return Func;
}

// 测试代码
function Animal(name) {
  this.name = name;
}
var obj = {
  name: 'test'
};
var mouse = new Animal('jerry');
var Animal3 = Animal.bind2(obj);
var mouse2 = new Animal3('jerry');
console.log(mouse); // {name: "jerry"}
console.log(mouse2); //{name: "jerry"}
console.log(obj); // {name: 'test'}
```

如上例子，我们的mouse2和obj都是正常的返回了。但这样的实现有一个问题，就是我们没法拿到Animal的原型，此时`mouse2.__proto__ === Func.prototype`；

因此需要再改写下，当实例对象能够链接到构造函数的原型，**第四版代码如下**：

```js
Function.prototype.bind2 = function(context) {
  // 保存上层函数this值
  var _this = this;
  // 保存上层函数的参数
  var args = [].slice.call(arguments, 1);
  function Func() {
    // 将参数拼接
    var _args = args.concat([].slice.call(arguments));
    _this.apply(this.__proto__ === Func.prototype ? this : context, _args);
  }
  Func.prototype = this.prototype;
  return Func;
}
```

这个时候我们再去实例化mouse2，就可以做到`mouse2.__proto__ === Animal.prototype`了。

还有一个问题，因为我们是直接`Func.prototype = this.prototype`, 所以我们在修改Func.prototype的时候，也会直接修改函数的prototype，我们看下我们的**最终代码**：

```js
Function.prototype.bind2 = function(context) {
  // 保存上层函数this值
  var _this = this;
  // 保存上层函数的参数
  var args = [].slice.call(arguments, 1);
  function Transfer() {}
  function Func() {
    // 将参数拼接
    var _args = args.concat([].slice.call(arguments));
    _this.apply(this.__proto__ === Func.prototype ? this : context, _args);
  }
  Transfer.prototype = this.prototype;
  Func.prototype = new Transfer();
  return Func;
}
```

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)