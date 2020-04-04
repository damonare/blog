---
title: 理解Javascript的变量提升
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true

---

### 前言

> 本文2922字，阅读大约需要8分钟。

**总括:**  什么是变量提升，使用var,let,const,function,class声明的变量函数类在变量提升的时候都有什么区别。

- 参考文章：[Hoisting in Modern JavaScript — let, const, and var](https://blog.bitsrc.io/hoisting-in-modern-javascript-let-const-and-var-b290405adfda)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**要么庸俗，要么孤独。**

<!-- more -->

### 正文

Javascript中的变量提升说的是在程序中可以在变量声明之前就进行使用：

```js
console.log(a); // undefined
var a = 1;
```

可以看到，在变量a声明之前我们可以正常调用a，代码的实际的表现更像是这样的：

```js
var a;
console.log(a); // undefined
a = 1;
```

但实际上，代码并没有被改变，上面的代码只是我们猜测的，其实Javascript引擎在执行这几行代码的时候并没有移动或是改变代码的结果。到底发生了什么呢？

#### 变量提升

在代码的编译期间，即代码真正执行的瞬息之间，引擎会将代码块中所有的变量声明和函数声明都记录下来。这些函数声明和变量声明都会被记录在一个名为**词法环境**的数据结构中。词法环境是Javascript引擎中一种记录变量和函数声明的数据结构，它会被直接保存在内存中。所以，上面的`console.log(a)`可以正常执行。

##### 什么是词法环境

所谓**词法环境**就是一种**标识符—变量**映射的结构(这里的**标识符**指的是变量/函数的名字，**变量**是对实际对象[包含函数和数组类型的对象]或基础数据类型的引用)。

简单地说，词法环境是Javascript引擎用来存储变量和对象引用的地方。

词法环境的结构用伪代码表示如下：

```js
LexicalEnvironment = {
  Identifier:  <value>,
  Identifier:  <function object>
}
```

关于词法环境更多的了解可以看博主之前的译文：[理解Javascript中的执行上下文和执行栈](https://blog.damonare.cn/2020/02/09/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84%E6%89%A7%E8%A1%8C%E4%B8%8A%E4%B8%8B%E6%96%87%E5%92%8C%E6%89%A7%E8%A1%8C%E6%A0%88/#more)。

了解了词法环境接下来让我依次看下使用`var`,`const`,`let`,`function`,`class`声明的变量或函数的情况。

#### function声明提升

```js
helloWorld();  // 打印 'Hello World!'
function helloWorld(){
  console.log('Hello World!');
}
```

我们已经知道了，函数声明会在编译阶段就会被记录在词法环境中并且保存在内存中，因此我们可以在函数进行实际声明之前对该函数进行访问。

上面函数声明保存在词法环境中像下面这样：

```js
lexicalEnvironment = {
  helloWorld: < func >
}
```

所以在代码执行阶段，当Javascript引擎碰到`helloWorld()`这行代码，会在词法环境中寻找，然后找到这个函数并执行它。

####函数表达式

注意，只有函数声明才会被直接提升，使用函数表达式声明的函数不会被提升，看下面代码：

```js
helloWorld();  // TypeError: helloWorld is not a function
var helloWorld = function(){
  console.log('Hello World!');
}
```

如上，代码报错了。使用`var`声明的helloWorld是个变量，并不是函数，Javascript引擎只会把它当成普通的变量来处理，而不会在词法环境中给它赋值。

保存在词法环境中像下面这样：

```js
lexicalEnvironment = {
  helloWorld: undefined
}
```

上面的代码要想可以正常运行改写如下即可：

```js
var helloWorld = function(){
  console.log('Hello World!');
}
helloWorld();  // 打印 'Hello World!'
```

#### var变量提升

看一个使用`var`声明变量的例子：

```js
console.log(a); // 打印 'undefined'
var a = 3;
```

如果按上面`function`函数声明的方式去理解，这里应该打印3，但实际上打印了`undefined`。

**请记住：**所谓的声明提升只是在编译阶段Javascript引擎将函数声明和变量声明存储在词法环境中，但不会给它们赋值。等到了执行阶段，真正执行到赋值那一行的时候，词法环境才会更新。

但上面的代码为什么打印了`undefined`呢？

Javascript引擎会在编译阶段将使用`var`声明的变量保存在词法环境中，并将它初始化为`undefined`。到了执行阶段，等执行到赋值那一行代码的时候，词法环境中变量的值才会被更新。

所以上面代码的词法环境初始化像下面这样：

```js
lexicalEnvironment = {
  a: undefined
}
```

这也解释了为什么前面使用函数表达式声明的函数执行会报错，为什么上面的代码会打印`undefined`。当代码执行到`var a = 3;`这行代码的时候，词法环境中a的值就会被更新，此时词法环境会被更新如下：

```js
lexicalEnvironment = {
  a: 3
}
```

#### let和const变量提升

看一个使用`let`声明变量的例子:

```js
console.log(a);
let a = 3;
```

输出：

```bash
Uncaught ReferenceError: Cannot access 'a' before initialization
```

再看一个使用const声明变量的例子：

```js
console.log(b);
const b = 1;
```

输出：

```bash
Uncaught ReferenceError: Cannot access 'b' before initialization
```

和`var`不同，相同结构的代码换成`let`或是`const`都直接报错了。

难道使用`let`和`const`声明的变量不存在变量提升的情况么？

实际上，在Javascript中所有声明的变量(`var`,`const`,`let`,`function`,`class`)都存在变量提升的情况。使用`var`声明的变量，在词法环境中会被初始化为`undefined`，但用`let`和`const`声明的变量并不会被初始化。

使用`let`和`const`声明的变量只有在执行到赋值那行代码的时候才会真正给他赋值，这也意味着在执行到变量声明的那行代码之前访问那个变量都会报错，这就是我们常说的**暂时性死区(TDZ)**。即在变量声明之前都不能对变量进行访问。

当执行到变量声明的那一行的时候，但是仍然没有赋值，那么使用`let`声明的变量就会被初始化为`undefined`;使用`const`声明的变量就会报错; 看实际的例子：

```js
let a;
console.log(a); // 输出 undefined
a = 5;
```

在代码编译阶段，Javascript引擎会把变量a存储在词法环境中，并把a保持在未初始化的状态。此时词法环境像下面这样：

```js
lexicalEnvironment = {
  a: <uninitialized>
}
```

此时如果尝试访问变量a或是b，Javascript引擎会在词法环境中找到该变量，但此时变量处于未初始化的状态，因此会抛出一个引用错误。

然后在执行阶段，Javascript引擎执行到赋值(专业点叫词法绑定)那一行的时候，会评估被赋值的值，如果没有被赋值，只是简单的声明，此时就会给`let`声明的变量赋值为`undefined`；此时词法环境像下面这样：

```js
lexicalEnvironment = {
  a: undefined
}
```

当执行到`a = 5`这一行的时候，词法环境再次更新：

```js
lexicalEnvironment = {
  a: 5
}
```

再看下使用`const`声明代码的情况:

```js
let a;
console.log(a);
a = 5;
const b;
console.log(b);
```

输出：

```js
Uncaught SyntaxError: Missing initializer in const declaration
```

上面代码直接报错，a的值也没有打印，直接报错，其实是代码在编译阶段就已经报错了，压根没执行到`console.log(a);`这一行代码。

**注意：**在函数中，只要是能在变量声明之后引用该变量就不会报错。

什么意思呢？看如下代码：

```js
function foo () {
  console.log(a);
}
let a = 20;
foo(); // 打印 20
```

但下面代码就会报错：

```js
function foo () {
  console.log(a);
}
foo();
let a = 20; // 报错： Uncaught ReferenceError: Cannot access 'a' before initialization
```

这里报错的原因需要结合[Javascript中的执行上下文和执行栈](https://blog.damonare.cn/2020/02/09/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84%E6%89%A7%E8%A1%8C%E4%B8%8A%E4%B8%8B%E6%96%87%E5%92%8C%E6%89%A7%E8%A1%8C%E6%A0%88/#more)才能理解，因为此时全局执行上下文中词法环境中保存的变量a处于未初始化的状态，调用foo函数，创建了一个函数执行上下文，然后函数foo执行过程对全局执行上下文的变量a进行访问，但a还处于未初始化的状态(此时`let a = 20`还没有执行)。因此报错。

这里需要纠正一个误区，就是`let`和`const`声明的变量只有暂时性死区，不存在变量提升，其实是不对的，举个例子证明理解一下：

```js
let a = 1;
{
  console.log(a);
  let a = 2;
}
```

上面的代码会被报错：

```js
Uncaught ReferenceError: Cannot access 'a' before initialization
```

如果不存在变量提升，理论上不会报错才对。

#### class声明提升

与`let`、`const`类似，使用`class`声明的类也会被提升，然后这个类声明会被保存在词法环境中但处于未初始化的状态，直到执行到变量赋值那一行代码，才会被初始化。另外，`class`声明的类一样存在**暂时性死区(TDZ)**。看例子：

```js
let peter = new Person('Peter', 25); 
console.log(peter);
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
```

打印：

```bash
Uncaught ReferenceError: Cannot access 'Person' before initialization
```

改写如下就可以正常运行了：

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
let peter = new Person('Peter', 25); 
console.log(peter);
// Person { name: 'Peter', age: 25 }
```

上面代码在编译阶段，词法环境像这样：

```js
lexicalEnvironment = {
  Person: <uninitialized>
}
```

然后执行到`class`声明的那一行代码，此时词法环境像下面这样：

```js
lexicalEnvironment = {
  Person: <Person object>
}
```

**注意：**使用构造函数实例化对象并不会报错：

```js
let peter = new Person('Peter', 25);
console.log(peter);
function Person(name, age) {

    this.name = name;
    this.age = age;
}
// Person { name: 'Peter', age: 25 }
```

上面代码正常运行。

#### 类表达式

和函数表达式一样，类表达式也一样会被提升，比如：

```js
let peter = new Person('Peter', 25);
console.log(peter);
let Person = class {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
```

报错：

```js
Uncaught ReferenceError: Cannot access 'Person' before initialization
```

要想正常运行，改写如下即可：

```js
let Person = class {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}
let peter = new Person('Peter', 25); 
console.log(peter);
// Person { name: 'Peter', age: 25 }
```

也就是说不管是函数表达式还是类表达式遵循的规则和变量声明是一样的。

#### 结论

不管是`var`,`const`,`let`,`function`,`class`声明的变量还是函数都存在变量提升的情况。正确理解变量提升有助于我们写更好的代码。整个变量提升的情况总结如下：

- `var`：存在变量提升，在编译阶段会被初始化为`undefined`；
- `let`:   存在变量提升，存在暂时性死区（TDZ），执行阶段，如果没赋值，则初始化为`undefined`；
- `const`:  存在变量提升，存在暂时性死区（TDZ），如果没有赋值，编译阶段就会报错；
- `function`：存在变量提升，在变量声明之前可以访问并执行；
- `class`： 存在变量提升，存在暂时性死区（TDZ）；

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)