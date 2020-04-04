---
title: 理解Javascript的作用域和作用域链
categories:
  - 原创
  - 译文
tags:
  - JavaScript
toc: true
comments: true

---

## 前言

> 本文2771字，阅读大约需要8分钟。

**总括:**  本文讲解了Javascript的作用域，作用域类型，作用域链等概念以及Javascript是如何去建立作用域链并寻找变量的。

- 原文地址：[Understanding Scope and Scope Chain in JavaScript](https://blog.bitsrc.io/understanding-scope-and-scope-chain-in-javascript-f6637978cf53)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**一花凋零，荒芜不了整个春天。**

<!-- more -->

## 正文

作用域和作用域链在Javascript和很多其它的编程语言中都是一种基础概念。但很多Javascript开发者并不真正理解它们，但这些概念对掌握Javascript至关重要。

正确的去理解这个概念有利于你去写更好，更高效和更简洁的代码，让你成为一个更优秀的Javascript开发者。

因此，在本文中，我将会向大家解释清楚什么是作用域和作用域链，以及Javascript引擎在内部是如何通过它们操作和查找变量的。

事不宜迟，正文开始 :)

### 什么是作用域

Javascript中的作用域说的是变量的可访问性和可见性。也就是说整个程序中哪些部分可以访问这个变量，或者说这个变量都在哪些地方可见。

#### 为什么作用域很重要

1. **作用域最为重要的一点是安全。**变量只能在特定的区域内才能被访问，有了作用域我们就可以避免在程序其它位置意外对某个变量做出修改。
2. **作用域也会减轻命名的压力。**我们可以在不同的作用域下面定义相同的变量名。

### 作用域的类型

Javascript中有三种作用域：

1. 全局作用域；
2. 函数作用域；
3. 块级作用域；

#### 1. 全局作用域

任何不在函数中或是大括号中声明的变量，都是在全局作用域下，全局作用域下声明的变量可以在程序的任意位置访问。例如：

```js
// 全局变量
var greeting = 'Hello World!';
function greet() {
  console.log(greeting);
}
// 打印 'Hello World!'
greet();
```

#### 2. 函数作用域

函数作用域也叫局部作用域，如果一个变量是在函数内部声明的它就在一个函数作用域下面。这些变量只能在函数内部访问，不能在函数以外去访问。例如：

```js
function greet() {
  var greeting = 'Hello World!';
  console.log(greeting);
}
// 打印 'Hello World!'
greet();
// 报错： Uncaught ReferenceError: greeting is not defined
console.log(greeting);
```

#### 3. 块级作用域

ES6引入了`let`和`const`关键字,和`var`关键字不同，在大括号中使用`let`和`const`声明的变量存在于块级作用域中。在大括号之外不能访问这些变量。看例子：

```js
{
  // 块级作用域中的变量
  let greeting = 'Hello World!';
  var lang = 'English';
  console.log(greeting); // Prints 'Hello World!'
}
// 变量 'English'
console.log(lang);
// 报错：Uncaught ReferenceError: greeting is not defined
console.log(greeting);
```

上面代码中可以看出，在大括号内使用`var`声明的变量lang是可以在大括号之外访问的。使用`var`声明的变量不存在块级作用域中。

###  作用域嵌套

像Javascript中函数可以在一个函数内部声明另一个函数一样，作用域也可以嵌套在另一个作用域中。请看例子：

```js
var name = 'Peter';
function greet() {
  var greeting = 'Hello';
  {
    let lang = 'English';
    console.log(`${lang}: ${greeting} ${name}`);
  }
}
greet();
```

这里我们有三层作用域嵌套，首先第一层是一个块级作用域（`let`声明的），被嵌套在一个函数作用域(`greet`函数)中，最外层作用域是全局作用域。

### 词法作用域

词法作用域（也叫静态作用域）从字面意义上看是说作用域在词法化阶段（通常是编译阶段）确定而非执行阶段确定的。看例子：

```js
let number = 42;
function printNumber() {
  console.log(number);
}
function log() {
  let number = 54;
  printNumber();
}
// Prints 42
log();
```

上面代码可以看出无论`printNumber()`在哪里调用`console.log(number)`都会打印`42`。**动态作用域**不同，`console.log(number)`这行代码打印什么取决于函数`printNumber()`在哪里调用。

如果是**动态作用域**，上面`console.log(number)`这行代码就会打印`54`。

使用词法作用域，我们可以仅仅看源代码就可以确定一个变量的作用范围，但如果是动态作用域，代码执行之前我们没法确定变量的作用范围。

像C，C++，Java，Javascript等大多数编程语言都支持静态作用域。Perl 既支持动态作用域也支持静态作用域。

###  作用域链

当在Javascript中使用一个变量的时候，首先Javascript引擎会尝试在当前作用域下去寻找该变量，如果没找到，再到它的上层作用域寻找，以此类推直到找到该变量或是已经到了全局作用域。

如果在全局作用域里仍然找不到该变量，它就会在全局范围内隐式声明该变量(非严格模式下)或是直接报错。

例如：

```js
let foo = 'foo';
function bar() {
  let baz = 'baz';
  // 打印 'baz'
  console.log(baz);
  // 打印 'foo'
  console.log(foo);
  number = 42;
  console.log(number);  // 打印 42
}
bar();
```

当函数`bar()`被调用，Javascript引擎首先在当前作用域下寻找变量`baz`，然后寻找foo变量但发现在当前作用域下找不到，然后继续在外部作用域寻找找到了它(这里是在全局作用域找到的)。

然后将`42`赋值给变量`number`。Javascript引擎会在当前作用域以及外部作用域下一步步寻找number变量(没找到)。

如果是在非严格模式下，引擎会创建一个`number`的全局变量并把`42`赋值给它。但如果是严格模式下就会报错了。

**结论：**当使用一个变量的时候，Javascript引擎会循着作用域链一层一层往上找该变量，直到找到该变量为止。

###  作用域和作用域链是如何工作的

以上内容已经讲解了作用域，作用域的类型，现在让我们看下Javascript引擎是如何确定变量的作用域链和如何去查找变量的。

要想理解Javascript是如何进行变量查找的，必须要了解Javascript中词法环境这个概念(请参考：[理解Javascript中的执行上下文和执行栈](https://blog.damonare.cn/2020/02/09/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84%E6%89%A7%E8%A1%8C%E4%B8%8A%E4%B8%8B%E6%96%87%E5%92%8C%E6%89%A7%E8%A1%8C%E6%A0%88/#more))。

#### 什么是词法环境

所谓**词法环境**就是一种**标识符—变量**映射的结构(这里的**标识符**指的是变量/函数的名字，**变量**是对实际对象[包含函数和数组类型的对象]或基础数据类型的引用)。

简单地说，词法环境是Javascript引擎用来存储变量和对象引用的地方。

**注意：**不要混淆了词法环境和词法作用域，词法作用域是在**代码编译阶段**确定的作用域(**译者注**：一个抽象的概念)，而词法环境是Javascript引擎用来存储变量和对象引用的地方(**译者注**：一个具象的概念)。

一个词法环境就像下面这样：

```js
lexicalEnvironment = {
  a: 25,
  obj: <ref. to the object>
}
```

只有当该作用域的代码被执行的时候，引擎才会为那个作用域创建一个新的词法环境。词法环境还会记录所引用的外部词法环境(即外部作用域)。例：

```js
lexicalEnvironment = {
  a: 25,
  obj: <ref. to the object>
  outer: <outer lexical environemt>
}
```

###  Javascript引擎是如何进行变量查找的

现在我们已经知道了作用域，作用域链和词法环境的概念，现在让我们看下Javascript引擎是如何利用词法环境来确定作用域和作用域链的。

结合例子我们来理解上面的这些概念：

```js
let greeting = 'Hello';
function greet() {
  let name = 'Peter';
  console.log(`${greeting} ${name}`); // Hello Peter
}
greet();
{
  let greeting = 'Hello World!'
  console.log(greeting); // Hello World!
}
```

上述代码加载后，首先会创建一个全局词法环境，其中包含在全局范围内声明的变量和函数。像下面这样：

```js
globalLexicalEnvironment = {
  greeting: 'Hello'
  greet: <ref. to greet function>
  outer: <null>
}
```

这里的`outer`字段(也就是外部词法环境)被设置为了`null`，是因为全局词法环境已经是最顶层的词法环境了。

然后，我们调用了`greet()`函数，然后一个新的词法环境会被被创建：

```js
functionLexicalEnvironment = {
  name: 'Peter'
  outer: <globalLexicalEnvironment>
}
```

这里的`outer`字段被设置为了`globalLexicalEnvironment`，是因为他的外部作用域就是全局作用域。

然后，执行console.log(\`\${greeting} ${name}\`)这行代码，Javascript引擎首先在当前函数的词法环境中寻找变量`greeting`和`name`，但只找到了`name`，没找到`greeting`。然后继续在上层的词法环境中找`greeting`（这里是全局作词法环境）。最后在全局词法环境中找到了`greeting`。

紧接着执行那段在大括号里的代码，为这个块级创建一个新的词法环境。如下：

```js
blockLexicalEnvironment = {
  greeting: 'Hello World',
  outer: <globalLexicalEnvironment>
}
```

然后执行`console.log(greeting)`这行代码，首先在本层词法环境中找`greeting`，OK，找到，结束。此时就不会再去外部作用域(这里是全局作用域)寻找该变量了。

**注意：**只有`let`和`const`声明变量才会创建一个新的词法环境存储，使用`var`声明的变量会被存储在当前块（大括号）所在的词法环境中（全局词法环境或是函数词法环境中）。

**结论：**当一个变量被使用时，Javascript引擎会首先在当前的词法环境中进行寻找，如果找不到就找上层词法环境中寻找，直到找到为止。

### 结论

作用域就是一个变量可访问和可见的区域，和函数一样，Javascript的作用域也可以嵌套，Javascript引擎会层层遍历作用域来寻找用到的变量。

Javascript使用词法作用域，这意味着变量的作用在编译阶段就会被确定。

Javascript引擎在程序执行期间使用词法环境来存储变量和函数。

作用域和作用域链是Javascript中的基础概念，理解作用域和作用域链能让你成为一个更优秀的Javascript开发者。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)