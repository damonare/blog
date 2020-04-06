---
title:  JS设计模式之原型模式
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

> 本文2584字，阅读大约需要10分钟。

**总括:**  本文讲解了在Javascript实现继承的几种方式，在Javascript中实现23种经典设计模式的原型模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**居安思危，思则有备，有备无患**。

<!-- more -->

## 正文

《Javascript高级程序设计》一书中对Javascript基于原型链实现继承的实现方式进行了非常经典的阐述和讲解。本文以此书为基础，借鉴其中思路对Javascript中的“原型继承模式”进行总结讲解。Javascript中的继承就是基于原型模式(Prototype pattern)的一种实现。

### 类式继承

类式继承是最基本的实现，它的原理就是通过将子类的原型指向父类的实例对象，从而达到继承父类属性和方法的目的：

```js
// 类式继承
// 父类Animal
function Animal(kind) {
  this.kind = kind;
  this.value = 'Animal';
  this.prop = ['name', 'age', 'color'];
}
// 为父类添加原型方法
Animal.prototype.getValue = function() {
  return this.value;
}
// 声明子类
function Cat(name) {
  this.name = name;
}
// 子类通过原型继承父类
Cat.prototype = new Animal('cat');
// 为子类添加原型方法
Cat.prototype.getName = function() {
  return this.getName;
}
```

但这种方法存在一个很大的缺陷：**子类的所有实例对象都共享同一个父类实例对象。**

```js
var cat1 = new Cat('diandian');
var cat2 = new Cat('nengneng');
cat1.prop.push('language');
console.log(cat2.prop); // ["name", "age", "color", "language"]
console.log(cat1.getValue()); // Animal
```

如上代码，有一个很大的优点：**代码复用**。所有的子类实例对象都引用同一份父类实例对象。但也因此存在问题，即更改`cat1`从父类继承的属性`prop`，另一个实例对象`cat2`也被更改了，根源就是他们的原型是同一个实例对象。请记住此时我们亟待解决的问题是:

**子类的实例对象共享同一个父类实例对象导致子类的实例对象之间彼此之间容易造成污染。**

2

> 类式继承总结：
>
> - **优点**： 代码复用；
> - **缺点**：子类实例对象容易彼此污染。

### 构造函数继承

好的，基于上面的问题，我们换一个思路去实现继承，Javascript中不光原型可以使用，我们把父类函数当做普通函数在子类函数中运行一遍，尝试把父类属性绑定到子类上面不一样实现了继承么？看下代码：

```js
// 构造函数继承
// 父类Animal
function Animal(kind) {
  this.kind = kind;
  this.value = 'Animal';
  this.prop = ['name', 'age', 'color'];
}
// 为父类添加原型方法
Animal.prototype.getValue = function() {
  return this.value;
}
// 声明子类
function Cat(name) {
  this.name = name;
  // 核心代码,执行父类函数
  Animal.call(this, 'cat');
}
// 为子类添加原型方法
Cat.prototype.getName = function() {
  return this.name;
}
```

解释下上面代码，核心在于`Animal.call(this, 'cat')`,对于`call`不熟悉的同学可以参考笔者[学习Javascript之模拟实现call,apply](https://blog.damonare.cn/2020/02/17/%E5%AD%A6%E4%B9%A0Javascript%E4%B9%8B%E6%A8%A1%E6%8B%9F%E5%AE%9E%E7%8E%B0call,apply/#more)这篇文章。上面同样的代码我们再看下运行结果：

```js
var cat1 = new Cat('diandian');
var cat2 = new Cat('nengneng');
cat1.prop.push('language');
console.log(cat2.prop); // ["name", "age", "color"]
console.log(cat1.getValue()); // 错误：Uncaught TypeError: cat1.getValue is not a function
```

可以看到，子类的实例对象之间不再互相影响，他们都有一份自己独立的父类属性和方法，因此**类式继承**的问题已经被解决了，但此时引入了新的问题：**代码复用**。因为所有继承自父类属性和方法都是直接绑定在子类的实例对象上，导致父类代码无法被复用，在子类的实例对象声明时父类的每一个属性和方法都会被重新声明，这种继承方式很粗暴的违背了代码复用的原则。而且通过这种方式实现的继承**无法继承父类函数原型上的方法和属性**。因此调用父类原型上的方法`getValue`时报错了。

>  构造函数继承总结：
>
> - **优点**：子类实例对象不会彼此污染。
> - **缺点**：代码无法复用，且无法继承父类原型上的方法和属性。

### 组合继承

看了上面两种继承方法，我们发现，他们是可以彼此弥补不足的，因此第三种实现继承的方式就是把上面两种方法组合起来：

```js
// 组合继承
// 父类Animal
function Animal(kind) {
  this.kind = kind;
  this.value = 'Animal';
  this.prop = ['name', 'age', 'color'];
}
// 为父类添加原型方法
Animal.prototype.getValue = function() {
  return this.value;
}
// 声明子类
function Cat(name) {
  this.name = name;
  // 构造函数继承的核心代码
  Animal.call(this, 'cat');
}
// 类式继承核心代码
Cat.prototype = new Animal('cat');
// 为子类添加原型方法
Cat.prototype.getName = function() {
  return this.name;
}
```

如上，通过将类式继承和构造函数继承结合，我们完美的解决了那两种方法的缺点：

```js
var cat1 = new Cat('diandian');
var cat2 = new Cat('nengneng');
cat1.prop.push('language');
console.log(cat2.prop); // ["name", "age", "color"]
console.log(cat1.getValue()); // Animal
```

组合继承是一种比较完美实现继承的方法，也是比较常用的一种方法，但这种方法还存在一个问题：就是父类函数被调用了两次。一次是在子类函数中调用父类函数` Animal.call(this, 'cat');`。一次是将声明父类实例对象`new Animal('cat')`。因此该方法并不完美。

> 组合继承总结：
>
> - **优点**： 代码复用，子类实例对象不会彼此污染；
> - **缺点**：父类函数被调用了两次；

### 原型式继承

2006年道格拉斯提出了一种新的继承方式(没错就是那个创造JSON，写了JSLint的大牛)，他的实现代码如下：

```js
// 原型继承
function inheritObject(o) {
  // 声明一个过渡函数对象
  function F() {}
  // 过渡对象的原型继承父对象
  F.prototype = o;
  // 返回实例，该实例的原型继承了父对象
  return new F();
}
```

看上面代码是不是很熟悉，他其实是对类式继承的一种封装。随着这种思想的深入，ES已经新增了`Object.create`方法来实现上面`inheritObject`的功能，使用方式如下：

```js
var book = {
  name: 'Javascript语言精粹',
  alikeBook: ['Javascript权威指南', '你不知道的Javascript']
}
var newBook = inheritObject(book);
var otherBook = inheritObject(book);
newBook.alikeBook.push('Javascript高级程序设计');
console.log(otherBook.alikeBook); //  ["Javascript权威指南", "你不知道的Javascript", "Javascript高级程序设计"]
```

如上示例代码，我们发现类式继承的优点和缺点该方法都有，不同的只是抽离为一个单独的方法，更通用了一点。因此这种方法并不完美，它其实就是类式继承的一个变种。

>原型式继承总结：
>
>- **优点**： 代码复用；
>- **缺点**：子类实例对象容易彼此污染。

### 寄生式继承

道格拉斯推广的继承并不只有原型式继承一种方法：

```js
// 寄生式继承
var book = {
  name: 'Javascript语言精粹',
  alikeBook: ['Javascript权威指南', '你不知道的Javascript']
}
function createBook(obj) {
  // 通过原型继承方式创建新对象
  var o = inheritObject(obj);
  o.getName = function() {
    console.log(this.name);
  }
  return o;
}
```

如上代码其实是对原型继承的又一层的封装，但在此基础上对继承的对象进行了拓展，这样新创建的对象不仅有父类的属性和方法还有新的属性和方法。

```js
var newBook = createBook(book);
var otherBook = createBook(book);
newBook.alikeBook.push('Javascript高级程序设计');
console.log(otherBook.alikeBook); //  ["Javascript权威指南", "你不知道的Javascript", "Javascript高级程序设计"]
```

如上示例，原型式继承存在的问题依然存在，只是多了可以扩展对象的一个优点。这种方式其实只是为了我们的终极继承方法——**寄生组合式继承**的一个过渡实现方法，下面来看下寄生组合式继承。

> 寄生式继承总结：
>
> - **优点**： 代码复用，扩展子对象；
> - **缺点**：子类实例对象容易彼此污染。

### 寄生组合式继承

上面我们说过**组合继承**已经比较完美，唯一美中不足的是父类函数调用了两次。而寄生组合式继承就可以既兼顾**组合继承**的优点，又可以解决这个缺点。我们再来看下道格拉斯对于寄生式继承的一个改造：

```js
/**
 * 寄生式继承 继承原型
 * @param subClass 子类
 * @param superClass 父类
 */
function inheritPrototype(subClass, superClass) {
  // 复制一份父类的原型副本保存在变量中
  var p = inheritObject(superClass.prototype);
  // 修正因为重写子类原型导致子类的constructor被修改
  p.constructor = subClass;
  // 设置子类的原型
  subClass.prototype = p;
}
```

我们还是拿我们前面的例子来实现：

```js
// 寄生组合式继承
// 父类Animal
function Animal(kind) {
  this.kind = kind;
  this.value = 'Animal';
  this.prop = ['name', 'age', 'color'];
}
// 为父类添加原型方法
Animal.prototype.getValue = function() {
  return this.value;
}
// 声明子类
function Cat(name) {
  this.name = name;
  // 构造函数继承的核心代码
  Animal.call(this, 'cat');
}
// 寄生组合式继承继承核心代码
inheritPrototype(Cat, Animal);
// 或是
// Cat.prototype = Object.create(Animal.prototype);
// 为子类添加原型方法
Cat.prototype.getName = function() {
  return this.name;
}
```

如上实现，父类函数只调用了一次，测试下：

```js
var cat1 = new Cat('diandian');
var cat2 = new Cat('nengneng');
cat1.prop.push('language');
console.log(cat2.prop); // ["name", "age", "color"]
console.log(cat1.getValue()); // Animal
```

这种方法是目前最常用的方法，但也存在一个小小的缺点，就是如果想给子类添加原型方法，那么只能通过**prototype.**对象，通过点语法一个一个去添加，要不然会直接覆盖掉从父类继承的对象，当然**组合继承**也存在这个问题。

> 寄生组合式继承总结：
>
> - **优点**： 代码复用，子类实例对象不会彼此污染；
> - **缺点**：无伤大雅；

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)