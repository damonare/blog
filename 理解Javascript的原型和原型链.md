---
title: 理解Javascript的原型和原型链
categories:
  - 原创
  - 译文
tags:
  - JavaScript
toc: true
comments: true

---

### 前言

> 本文2088字，阅读大约需要13分钟。

**总括:**  结合实例阐述了原型和原型链的概念并总结了几种创建对象的方法，扩展原型链的方法。

- 参考文章：[The Secret Life of Objects](https://eloquentjavascript.net/06_object.html)，[继承与原型链](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**禄无常家，福无家门。**

<!-- more -->

### 正文

#### 原型

Javascript中有一句话，叫一切皆是对象，当然这句话也不严谨，比如`null`和`undefined`就不是对象，除了这俩完全可以说Javascript一切皆是对象。而Javascript对象都有一个叫做**原型**的公共属性，属性名是**\__proto__**。这个**原型**属性是对另一个对象的引用，通过这个**原型**属性我们就可以访问另一个对象所有的属性和方法。比如：

```js
let numArray = [1, 2, -8, 3, -4, 7];
```

**Array**对象就有一个原型属性指向**Array.prototype**,变量`numArray`继承了**Array.prototype**对象所有的属性和方法。

![](https://image.damonare.cn/1_0SEuvD3JycoeKThov-urSQ.png)

这就是为什么可以直接调用像**sort()**这种方法：

```js
console.log(numArray.sort()); // -> [-4, -8, 1, 2, 3, 7]
```

也就是说：

```js
numArray.__proto__ === Array.prototype // true
```

对于其他对象(函数)也是一样(比如**Date(),Function(), String(),Number()**等)；

当一个构造函数被创建后，实例对象会继承构造函数的原型属性，这是构造函数的一个非常重要的特性。在Javascript中使用`new`关键字来对构造函数进行实例化。看下面的例子：

```js
const Car = function(color, model, dateManufactured) {
  this.color = color;
  this.model = model;
  this.dateManufactured = dateManufactured;
};
Car.prototype.getColor = function() {
	return this.color;
};
Car.prototype.getModel = function() {
	return this.model;
};
Car.prototype.carDate = function() {
	return `This ${this.model} was manufactured in the year ${this.dateManufactured}`
}
let firstCar = new Car('red', 'Ferrari', '1985');
console.log(firstCar);
console.log(firstCar.carDate());
```

![](https://image.damonare.cn/1_LFtUIQbLqX0vtRPRwFtLFg.png)

上面的例子中，方法**getColor,carDate,getModel**都是对象(函数)**Car**的方法，而**Car**的实例对象**firstCar**可以继承**Car**原型上的一切方法和属性。

**结论：**每一个实例对象都有一个私有属性**\__proto__**,指向它的构造函数的原型对象(**prototype**)。

#### 原型链

在Javascript中如果访问一个对象本身不存在的属性或是方法，就首先在它的原型对象上去寻找，如果原型对象上也不存在，就继续在原型对象的原型对象上去寻找，直到找到为止。那么原型对象有尽头么？所有对象的原型尽头是**Object.prototype**,那么**Object.prototype**这个对象的**\__proto__**指向啥呢？答案是`null`。我们日常开发中用到的绝大多数对象的**\__proto__**基本不会直接指向**Object.prototype**,基本都是指向另一个对象。比如所有的函数的**\__proto__**都会指向**Function.prototype**,所有数组的**\__proto__**都会指向**Array.prototype**。

```js
let protoRabbit = {
  color: 'grey',
  speak(line) {
		console.log(`The ${this.type} rabbit says ${line}`);
  }
};
let killerRabbit = Object.create(protoRabbit);
killerRabbit.type = "assassin";
killerRabbit.speak("SKREEEE!");
```

上面代码中变量**protoRabbit**设置为所有兔子对象的公有属性对象集，**killerRabbit**这只兔子通过**Object.create**方法继承了**protoRabbit**的所有属性和方法，然后给**killerRabbit**赋值了一个**type**属性，再看下面的代码：

```js
let mainObject = {
	bar: 2
};
// create an object linked to `anotherObject`
let myObject = Object.create( mainObject );
for (let k in myObject) {
  console.log("found: " + k);
}
// found: bar
("bar" in myObject); 
```

如上变量**myObject**本身并没有**bar**属性，但这里会循着原型链一层一层往上找，直到找到或者原型链结束为止。如果到原型链尽头还是没找到该属性，那么访问该属性的时候就会返回`undefined`了。

使用**for...in**关键字对对象进行迭代的过程，和上面访问某个属性循着原型链查找类似,会去遍历所有原型链上的属性(不论属性是否可枚举)。

```js
let protoRabbit = {
  color: 'grey',
  speak(line) {
  	console.log(`The ${this.type} rabbit says ${line}`);
  }
};
let killerRabbit = Object.create(protoRabbit);
killerRabbit.type = "assassin";
killerRabbit.speak("SKREEEE!");
```

上面的代码中访问**speak**的效率很高，但如果我们想创建很多个**Rabbit**对象，就必须要重复写很多代码。而这正是原型和构造函数的真正用武之地。

```js
let protoRabbit = function(color, word, type) {
  this.color = color;
  this.word = word;
  this.type = type;
};
protoRabbit.prototype.getColor = function() {
	return this.color;
}
protoRabbit.prototype.speak = function() {
	console.log(`The ${this.type} rabbit says ${this.word}`);
}
let killerRabbit = new protoRabbit('grey', 'SKREEEEE!', 'assassin');
killerRabbit.speak();
```

如上代码，使用构造函数的方式就可以节省很多的代码。

**结论：**每一个实例对象都有一个私有属性**\__proto__**,指向它的构造函数的原型对象(**prototype**)。原型对象也有自己的**\__proto__**，层层向上直到一个对象的原型对象为`null`。这一层层原型就是原型链。

附赠一张原型链的图：

![](https://image.damonare.cn/912272379-5b2f5308780fb_articlex.png)

#### 创建对象的四种方法

##### 字面量对象

这是比较常用的一种方式：

```js
let obj = {};
```

##### 构造函数创建

构造函数创建的方式更多用来在Javascript中实现继承，多态，封装等特性。

```js
function Animal(name) {
	this.name = name;
}
let cat = new Animal('Tom');
```

##### class创建

`class`关键字是ES6新引入的一个特性，它其实是基于原型和原型链实现的一个语法糖。

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
}
let cat = new Animal('Tom');
```

#### 扩展原型链的四种方法

##### 构造函数创建

上面例子有用到使用构造函数创建对象的例子，我们再来看一个实际的例子：

```js
function Animal(name) {
	this.name = name;
}
Animal.prototype = {
	run() {
    console.log('跑步');
	}
}
let cat = new Animal('Tom');
cat.__proto__ === Animal.prototype; // true
Animal.prototype.__proto__ === Object.prototype; // true
```

> **优点：**支持目前以及所有可想象到的浏览器(IE5.5都可以使用). 这种方法非常快，非常符合标准，并且充分利用JIST优化。
>
> **缺点：**为使用此方法，这个问题中的函数必须要被初始化。另外构造函数的初始化，可能会给生成对象带来并不想要的方法和属性。

##### Object.create

ECMAScript 5 中引入了一个新方法: `Object.create()`。可以调用这个方法来创建一个新对象。新对象的原型就是调用 create 方法时传入的第一个参数：

```js
var a = {a: 1}; 
// a ---> Object.prototype ---> null
var b = Object.create(a);
b.__proto__ === a; // true
```

> **优点：** 支持当前所有非微软版本或者 IE9 以上版本的浏览器。允许一次性地直接设置 `__proto__` 属性，以便浏览器能更好地优化对象。同时允许通过 `Object.create(null) `来创建一个没有原型的对象。
>
> **缺点：**不支持 IE8 以下的版本；这个慢对象初始化在使用第二个参数的时候有可能成为一个性能黑洞，因为每个对象的描述符属性都有自己的描述对象。当以对象的格式处理成百上千的对象描述的时候，可能会造成严重的性能问题。

##### Object.setPrototypeOf

语法：

```js
Object.setPrototypeOf(obj, prototype)
```
参数：

| 参数名    | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| obj       | 要设置其原型的对象。                                         |
| prototype | 该对象的新原型(一个对象 或 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)). |

```js
var a = { n: 1 };
var b = { m : 2 };
Object.setPrototypeOf(a, b);
a.__proto__ === b; // true
```

> **优点：**支持所有现代浏览器和微软IE9+浏览器。允许动态操作对象的原型，甚至能强制给通过 `Object.create(null) `创建出来的没有原型的对象添加一个原型。
>
> **缺点：**这个方式表现并不好，应该被弃用；动态设置原型会干扰浏览器对原型的优化；不支持 IE8 及以下的浏览器版本。

##### \__proto__

```js
var a = { n: 1 };
var b = { m : 2 };
a.__proto__ = b;
a.__proto__ === b; // true
```

使用**\__proto__**也可以动态设置对象的原型。

> **优点：**支持所有现代非微软版本以及 IE11 以上版本的浏览器。将 `__proto__` 设置为非对象的值会静默失败，并不会抛出错误。
>
> **缺点：**应该完全将其抛弃因为这个行为完全不具备性能可言；干扰浏览器对原型的优化；不支持 IE10 及以下的浏览器版本。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)