---
title: 理解Javascript的this
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

总括: 详解JavaScript中的this的一篇总结，不懂this这个难点，很多时候会造成一些困扰，写出一些bug不知如何收场，所以一起来写bug吧，不对，一起来写代码吧。

- 原文地址：[理解Javascript的this](https://blog.damonare.cn/2017/07/22/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84this/#more)

- 知乎专栏: [前端进击者](https://zhuanlan.zhihu.com/damonare)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生得意须尽欢，莫使金樽空对月**

<!-- more -->

 ## 正文

​  `JavaScript`中的`this`格外的不一样，比如`Java`语言中的`this`是在代码的执行阶段是不可更改，而JavaScript的`this`是在**调用阶段**进行绑定。👌因为这一性质所以给了`this`很大的发挥空间。但其在严格模式和非严格模式下又有些不一样，在函数的不同调用方式也导致this有些区别。🌹

### What's this?

😎首先对`this`的下个定义：**this是在执行上下文创建时确定的一个在执行过程中不可更改的变量。**

所谓**执行上下文**，就是JavaScript引擎在执行一段代码之前将代码内部会用到的一些**变量**、**函数**、**this**提前声明然后保存在变量对象中的过程。这个'代码片段'包括：**全局代码**(script标签内部的代码)、**函数内部代码**、**eval内部代码**。而我们所熟知的作用域链也会在保存在这里，以一个类数组的形式存储在对应函数的[[Scopes]]属性中。

`this`只在函数调用阶段确定，也就是执行上下文创建的阶段进行赋值，保存在变量对象中。这个特性也导致了`this`的多变性:🙂即当函数在不同的调用方式下都可能会导致`this`的值不同。

👆👆上面我们说过了在严格模式下和非严格模式下this表现不同：

```javascript
var a = 1;
function fun() {
   'use strict';
  var a = 2;
    return this.a;
}
fun();//😨报错 Cannot read property 'a' of undefined
```

👆严格模式下，this指向undefined;

```javascript
var a = 1;
function fun() {
  var a = 2;
    return this.a;
}
fun();//1
```

👆非严格模式下this指向window; 

上面同一段代码，在不同模式下之所以有不同表现，就是因为this在严格模式，非严格模式下的不同。

> 结论：**当函数独立调用的时候，在严格模式下它的this指向undefined，在非严格模式下，当this指向undefined的时候，自动指向全局对象(浏览器中就是window)**  

多提一句，在全局环境下，this就是指向自己，再看🌰：

```javascript
this.a = 1;
var b = 1;
c = 1;
console.log(this === window)//true
//这三种都能得到想要的结果，全局上下文的变量对象中存在这三个变量
```

再多提一句，当this不在函数中用的时候会怎样?看🌰：

```javascript
var a = 1000;
var obj = {
  a: 1,
    b: this.a + 1
}
function fun() {
  var obj = {
        a: 1,
  c: this.a + 2 //严格模式下这块报错 Cannot read property 'a' of undefined
    }
    return obj.c;
}
console.log(fun());//1002
console.log(obj.b);//1001
```

这种情况下this还是指向了window。那么我们可以单独下个结论：

**当obj在全局声明的时候，obj内部属性中的this指向全局对象，当obj在一个函数中声明的时候，严格模式下this会指向undefined，非严格模式自动转为指向全局对象。**

👌好了，刚刚小试牛刀下，知道了严格模式和非严格模式下`this`的区别，然而我们日常应用最多的还是在函数中用`this`，上面也说过了`this`在函数的不同调用方式还有区别，那么函数的调用方式都有哪些呢？四种：

- 在全局环境或是普通函数中直接调用；
- 作为对象的方法；
- 使用apply和call；
- 作为构造函数；

下面分别就四种情况展开：

### 直接调用

上面的🌰其实就是直接调用的，不过我决定再写☝️🌰：

```javascript
var a = 1;
var obj  =  {
  a: 2,
    b: function () {
      function fun() {
        return this.a
      }
      console.log(fun());
    }
} 
obj.b();//1
```

fun函数虽然在obj.b方法中定义，但它还是一个普通函数，直接调用在非严格模式下指向undefined，又自动指向了全局对象，正如预料，严格模式会报错undefined.a不成立，a未定义。

重要的事情再说一遍：**当函数独立调用的时候，在严格模式下它的this指向undefined，在非严格模式下，当this指向undefined的时候，自动指向全局对象(浏览器中就是window)。**😯

### 作为对象的方法

```javascript
var a = 1;
var obj = {
  a: 2,
  b: function() {
    return this.a;
  }
}
console.log(obj.b())//2
```

👆b所引用的匿名函数作为obj的一个方法调用，这时候`this`指向调用它的对象。这里也就是obj。那么如果b方法不作为对象方法调用呢？啥意思呢，就是这样👇：

```javascript
var a = 1;
var obj = {
  a: 2,
  b: function() {
    return this.a;
  }
}
var t = obj.b;
console.log(t());//1
```

如上，t函数执行结果竟然是全局变量1，为啥呢？这就涉及Javascript的内存空间了，就是说，obj对象的b属性存储的是对该匿名函数的一个引用，可以理解为一个指针。当赋值给t的时候，并没有单独开辟内存空间存储新的函数，而是让t存储了一个指针，该指针指向这个函数。相当于执行了这么一段伪代码：

```javascript
var a = 1;
function fun() {//此函数存储在堆中
  return this.a;
}
var obj = {
  a: 2,
  b: fun //b指向fun函数
}
var t = fun;//变量t指向fun函数
console.log(t());//1
```

此时的t就是一个指向fun函数的指针，调用t，相当于直接调用fun，套用以上规则，打印出来1自然很好理解了。

### 使用apply,call

关于apply和call是干什么的怎么用本文不涉及，请移驾：[apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)，[call](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

这是个万能公式，实际上上面直接调用的代码，我们可以看成这样的：

```javascript
function fun() {
    return this.a;
}
fun();//1
//严格模式
fun.call(undefined)
//非严格模式
fun.call(window)
```

这时候我们就可以解释下，为啥说在非严格模式下，当函数this指向undefined的时候，会自动指向全局对象，如上，在非严格模式下，当调用fun.call(undefined)的时候打印出来的依旧是1，就是最好的证据。

为啥说是万能公式呢？再看函数作为对象的方法调用:

```javascript
var a = 1;
var obj = {
  a: 2,
  b: function() {
    return this.a;
  }
}
obj.b()
obj.b.call(obj)
```

如上，是不是很强大，可以理解为其它两种都是这个方法的语法糖罢了，那么apply和call是不是真的万能的呢？并不是，ES6的箭头函数就是特例，因为箭头函数的`this`不是在调用时候确定的，这也就是为啥说箭头函数好用的原因之一，因为它的`this`固定不会变来变去的了。关于箭头函数的`this`我们稍后再说。

### 作为构造函数

何为构造函数？所谓构造函数就是用来new对象的函数，像`Function`、`Object`、`Array`、`Date`等都是全局定义的构造函数。其实每一个函数都可以new对象，那些批量生产我们需要的对象的函数就叫它构造函数罢了。注意，构造函数首字母记得大写。

```javascript
function Fun() {
  this.name = 'Damonre';
  this.age = 21;
  this.sex = 'man';
  this.run = function () {
    return this.name + '正在跑步';
  }
}
Fun.prototype = {
  contructor: Fun,
  say: function () {
    return this.name + '正在说话';
  }
}
var f = new Fun();
f.run();//Damonare正在跑步
f.say();//Damonare正在说话
```

如上，**如果函数作为构造函数用，那么其中的this就代表它即将new出来的对象。**为啥呢？new做了啥呢？

伪代码如下：

```javascript
function Fun() {
  //new做的事情
  var obj = {};
  obj.__proto__ = Fun.prototype;//Base为构造函数
  obj.name = 'Damonare';
  ...//一系列赋值以及更多的事
  return obj
}
```

也就是说new做了下面这些事:

- 创建一个临时对象；
- 给临时对象绑定原型；
- 给临时对象对应属性赋值；
- 将临时对象return；

也就是说new其实就是个语法糖，this之所以指向临时对象还是没逃脱上面说的几种情况。

当然如果直接调用Fun(),如下：

```javascript
function Fun() {
  this.name = 'Damonre';
  this.age = 21;
  this.sex = 'man';
  this.run = function () {
    return this.name + '正在跑步';
  }
}
Fun();
console.log(window)
```

其实就是直接调用一个函数，this在非严格模式下指向window，你可以在window对象找到所有的变量。

另外还有一点，prototype对象的方法的this指向实例对象，因为实例对象的`__proto__`已经指向了原型函数的prototype。这就涉及原型链的知识了，即方法会沿着对象的原型链进行查找。实际上不仅仅是构造函数的prototype，即便是在整个原型链中，this代表的也都是当前对象的值。

### 箭头函数

刚刚提到了箭头函数是一个不可以用call和apply改变this的典型。

我们看下面这个🌰：

```javascript
var a = 1;
var obj = {
  a: 2
};
var fun = () => console.log(this.a);
fun();//1
fun.call(obj)//1
```

以上，两次调用都是1。

那么箭头函数的this是怎么确定的呢？**箭头函数会捕获其所在上下文的  `this` 值，作为自己的 `this` 值**，也就是说箭头函数的this在词法层面就完成了绑定。apply，call方法只是传入参数，却改不了this。

```javascript
var a = 1;
var obj = {
  a: 2
};
function fun() {
    var a = 3;
    let f = () => console.log(this.a);
    f();
};
fun();//1
fun.call(obj);//2
```

如上，fun直接调用，fun的上下文中的this值为window，注意，这个地方有点绕。fun的上下文就是此箭头函数所在的上下文，因此此时f的this为fun的this也就是window。当fun.call(obj)再次调用的时候，新的上下文创建，fun此时的this为obj，也就是箭头函数的this值。

再来一个🌰：

```javascript
function Fun() {
  this.name = 'Damonare';
}
Fun.prototype.say = () => {
  console.log(this);
}
var f = new Fun();
f.say();//window
```

有的同学看到这个🌰会很懵逼，感觉上this应该指向f这个实例对象啊。不是的，此时的箭头函数所在的上下文是`__proto__`所在的上下文也就是Object函数的上下文，而Object的this值就是全局对象。

那么再来一个🌰:

```javascript
function Fun() {
  this.name = 'Damonare';
    this.say = () => {
  console.log(this);
  }
}
var f = new Fun();
f.say();//Fun的实例对象
```

如上，this.say所在的上下文，此时箭头函数所在的上下文就变成了Fun的上下文环境，而因为上面说过当函数作为构造函数调用的时候(也就是new的作用)上下文环境的`this`指向实例对象。

## 后记

文中定义均为个人总结，不妥之处还请雅正。

转载请注明出处。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)