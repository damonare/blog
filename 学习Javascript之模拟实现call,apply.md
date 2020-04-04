---
title: 学习Javascript之模拟实现call,apply
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文1630字，阅读大约需要8分钟。

**总括:**  本文从零开始通过提出问题然后解决问题的方式模拟实现了比较完善的call和apply方法

- 参考文档：[Function.prototype.call()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)，[Function.prototype.apply()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**每一个不曾起舞的日子，都是对生命的辜负。**

<!-- more -->

## 正文

### call,apply简介

首先介绍下call和apply两个方法，这两个方法都是挂载在函数的原型上的，所以所有的函数都可以调用这两个方法。

> **注意：**call()方法的作用和 apply() 方法类似，区别就是`call()`方法接受的是**参数列表**，而`apply()`方法接受的是**一个参数数组**。

例子：

```js
function foo(b = 0) {
  console.log(this.a + b);
}
const obj1 = {
  a: 1
};
const obj2 = {
  a: 2
};
foo.call(obj1, 1); // 2
foo.call(obj2, 2); // 4
foo.apply(obj1, [1]); // 2
foo.apply(obj2, [2]); // 4
```

对于`this`不熟悉的同学可以先异步：[理解Javascript的this](https://blog.damonare.cn/2017/07/22/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84this/#more)。总结起来一句话：Javascript函数的`this`指向调用方，谁调用`this`就指向谁，如果没人谁调用这个函数，严格模式下指向`undefined`，非严格模式指向`window`。

所以本质上**call和apply就是用来更改被调用函数的this值的**。如上，call和apply只有参数的不同，模拟实现了call，那么apply就只是参数处理上的区别。也就是说，call和apply干了两件事：

1. 改变被调用函数的`this`值；
2. 传参调用；

###更改this 

现在模拟实现call和apply的问题转移到另一个问题上，即如何去更改一个函数的`this`值，很简单：

```js
function foo(b = 0) {
  console.log(this.a + b);
}
const obj1 = {
  a: 1,
  foo: foo
};
const obj2 = {
  a: 2,
  foo: foo
};
obj1.foo(1);
obj2.foo(2);
```

也就是说我们把这个方法赋值给对象，然后对象调用这个函数就可以了。改变一个函数的this步骤很简单，首先将这个函数赋值给this要指向的对象，然后对象调用这个函数，执行完从对象上删除掉这个函数就好了。步骤如下：

```js
obj.foo = foo;
obj.foo();
delete obj.foo;
```

有了思路我们**实现第一版call方法**：

```js
Function.prototype.call2 = function(context) {
  context = context || {};
  context[this.name] = this;
  context[this.name]();
  delete context[this.name];
}
```

`this.name`是函数声明的名称，但其实是没必要一定对应函数名称的，我们随便用一个key都可以：

```js
Function.prototype.call2 = function(context) {
  context = context || {};
  context.func = this;
  context.func();
  delete context.func;
}
```

使用新的call调用上面的函数：

```js
foo.call2(obj1); // 1
foo.call2(obj2); // 2
```

OK，this的问题解决了，接下来就是传参的问题：

### 传参

函数中的参数保存在一个类数组对象`arguments`中。因此我们可以从arguments里面去拿从传到call2里面的参数：

```js
Function.prototype.call2 = function(context) {
  context = context || {};
  var params = [];
  for (var i = 1; i < arguments.length; i++) {
    params[i - 1] = arguments[i];
  }
  context.func = this;
  context.func();
  delete context.func;
}
```

此时问题来了,如何把参数`params`传递到`func`中呢？比较容易想到的办法是**利用ES6的扩展运算符**：

```js
Function.prototype.call2 = function(context) {
  context = context || {};
  var params = [];
  for (var i = 1; i < arguments.length; i++) {
    params[i - 1] = arguments[i];
  }
  context.func = this;
  context.func(...params);
  delete context.func;
}
```

看下我们的例子：

```js
foo.call2(obj1, 1); // 2
foo.call2(obj2, 2); // 4
```

还有一个实现，是利用不常用的eval函数，即我们把参数拼接成一个字符串，传给eval函数去执行，

> eval() 函数可计算某个字符串，并执行其中的的 JavaScript 代码。

看下我们的第二版实现：

```js
Function.prototype.call2 = function(context) {
  context = context || {};
  var params = [];
  for (var i = 1; i < arguments.length; i++) {
    params[i - 1] = arguments[i];
  }
  // 注意，此处的this是指的被调用的函数
  context.func = this;
  eval('context.func(' + params.join(",") + ')');
  delete context.func;
}
```

### 其它

`call`和`apply`还有另外两个重要的特性，可以正常返回函数执行结果，接受`null`或`undefined`为参数的时候将`this`指向`window`，然后我们来实现下这两个特性，然后加上必要的判断提示，这是我们的**第三版实现**：

```js
Function.prototype.call2 = function(context) {
  context = context || window;
  var params = [];
  // 此处将i初始化为1，是为了跳过context参数
   for (var i = 1; i < arguments.length; i++) {
    params[i - 1] = arguments[i];
  }
  // 注意，此处的this是指的被调用的函数
  context.func = this;
  var res = eval('context.func(' + params.join(",") + ')');
  delete context.func;
  return res;
}
```

然后我们调用测试下：

```js
foo.call2(obj1, 1); // 2

foo.call(2, 1); // NaN
foo.call2(2, 1); // context.func is not a function
```

如上我们发现将对象改成数字2后原始call返回了NaN，我们的call2却报错了，说明一个问题，我们直接`context = context || window`是有问题的。内部还有一个类型判断，解决这个问题后，我们的**第四版实现**如下：

```js
Function.prototype.call2 = function(context) {  
  if (context === null || context === undefined) {
    context = window;
  } else {
    context = Object(context) || context;
  }
  var params = [];
  // 此处将i初始化为1，是为了跳过context参数
   for (var i = 1; i < arguments.length; i++) {
    params[i - 1] = arguments[i];
  }
  // 注意，此处的this是指的被调用的函数
  context.func = this;
  var res = eval('context.func(' + params.join(",") + ')');
  delete context.func;
  return res;
}
```

这就是我们的最终代码，这个代码可以从ES3一直兼容到ES6，此时：

```js
foo.call(2, 1); // NaN
foo.call2(2, 1); // NaN
```

### 模拟实现apply

apply和call只是参数上的区别，将call2改写就好了：

```js
Function.prototype.apply2 = function(context, arr) {
  if (context === null || context === undefined) {
    context = window;
  } else {
    context = Object(context) || context;
  }
  // 注意，此处的this是指的被调用的函数
  context.func = this;
  arr =  arr || [];
  var res = eval('context.func(' + arr.join(",") + ')');
  delete context.func;
  return res;
}
```

以上就是我们最终的实现，目前还有一个问题就是`context.func`的问题，这样一来我们传进来的`context`就不能使用`func`字符串作为方法名了。

### 结论

我们实现过程都解决了以下问题：

1. 更改被调用函数的`this`；
2. 将参数传递给被调用函数；
3. 将被调用函数结果返回，第一个参数为`null`或`undefined`的时候被调用函数的`this`指向`window`;
4. 解决类型判断的问题；

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)