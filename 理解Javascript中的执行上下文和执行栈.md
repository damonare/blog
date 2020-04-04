---
title: 理解Javascript中的执行上下文和执行栈
categories:
  - 原创
  - 译文
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文3356字，阅读大约需要9分钟。

**总括:**  本文深入的讲解了Javascript中的执行上下文和执行栈。

- 原文地址：[Understanding Execution Context and Execution Stack in Javascript](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**流水在碰到底处时才会释放活力。**

<!-- more -->

## 正文

如果你是或者想成为一名Javascript开发者，那就必须要知道Javascript内部是如何执行的。正确的理解Javascript中的执行上下文和执行栈对于理解其它Javascript概念(比如变量提升，作用域，闭包等)至关重要。

正确的去理解Javascript执行上下文和执行栈将会是你成为一名更好的Javascript开发者。

不多废话，我们现在就开始:)

### 什么是执行上下文

简单的来说，执行上下文是一种对Javascript代码执行环境的一种抽象概念，也就是说只要有Javascript代码运行，那么它就一定是运行在执行上下文中。

### 执行上下文的类型

Javascript一共有三种执行上下文：

- **全局执行上下文**。这是一个默认的或者说基础的执行上下文，所有不在函数中的代码都会在全局执行上下文中执行。它会做两件事：创建一个全局的`window`对象(浏览器环境下)，并将`this`的值设置为该全局对象，另外一个程序中只能有一个全局上下文。
- **函数执行上下文**。每次调用函数时，都会为该函数创建一个执行上下文，每一个函数都有自己的一个执行上下文，但注意是该执行上下文是在函数被调用的时候才会被创建。函数执行上下文会有很多个，每当一个执行上下文被创建的时候，都会按照他们定义的顺序去执行相关代码（这会在后面会说到）。
- **Eval函数执行上下文**。在`eval`函数中执行的代码也会有自己的执行上下文，但由于`eval`函数不会被经常用到，这里就不做讨论了。(**译者注**：`eval`函数容易导致恶意攻击，并且运行代码的速度比相应的替代方法慢，因为不推荐使用)。

### 执行栈

执行栈，在其他编程语言中也被称为“调用栈”，这是一种后进先出(LIFO)的数据结构，被用来储存在代码运行阶段创建的所有的执行上下文。

当Javascript引擎（**译者注**：Javascript引擎是执行Javascript代码的解释器，一般被内嵌在浏览器中）开始执行你第一行Javascript脚本代码的时候，它就会创建一个全局执行上下文然后将它压到执行栈中。每当引擎碰到一个函数的时候，它就会创建一个函数执行上下文，然后将这个执行上下文压到执行栈中。（**译者注**：这种结构类似弹夹，执行栈就是弹夹，执行上下文是子弹，子弹被一个个压入弹夹，当子弹发射的时候，最后一个进弹夹的子弹会被最先射出）。

引擎会执行位于执行栈栈顶的执行上下文(一般是函数执行上下文)，当该函数执行结束后，对应的执行上下文就会被弹出，然后控制流程到达执行栈的下一个执行上下文。

结合下面的代码来理解下：

```js
let a = 'Hello World!';
function first() {
  console.log('Inside first function');
  second();
  console.log('Again inside first function');
}
function second() {
  console.log('Inside second function');
}
first();
console.log('Inside Global Execution Context');
```

![上述代码的执行栈](https://image.damonare.cn/stack.png)

<div align="center">上述代码的执行栈</div>

当上述代码在浏览器中加载时，Javascript引擎首先创建一个全局执行上下文并将其压入执行栈中，然后碰到`first()`函数被调用，此时再创建一个函数执行上下文压入执行栈中。

当`second()`函数在`first()`函数中被调用时，引擎再针对这个函数创建一个函数执行上下文将其压入执行栈中，`second`函数执行完毕后，对应的函数执行上下文被推出执行栈销毁，然后控制流程到下一个执行上下文也就是`first`函数。

当`first`函数执行结束，first函数执行上下文也被推出，引擎控制流程到全局执行上下文，直到所有的代码执行完毕，全局执行上下文也会被推出执行栈销毁，然后程序结束。

### 执行上下文是如何创建的？

现在我们已经了解了Javascript引擎是如何去处理执行上下文的，那么，执行上下文是如何创建的呢？

执行上下文的创建分为两个阶段：

1. 创建阶段；
2. 执行阶段；

### 创建阶段

执行上下文是在创建阶段被创建的，创建阶段包括以下几个方面：

1. 创建词法环境；
2. 创建变量环境；

因此执行上下文可以抽象为下面的形式：

```js
ExecutionContext = {
  LexicalEnvironment = <ref. to LexicalEnvironment in memory>,
  VariableEnvironment = <ref. to VariableEnvironment in  memory>,
}
```

#### 词法环境

[ES6的官方文档](http://ecma-international.org/ecma-262/6.0/) 把词法环境定义为：

> **词法环境**（Lexical Environments）是一种规范类型，用于根据ECMAScript代码的词法嵌套结构来定义标识符与特定变量和函数的关联。词法环境由一个环境记录（Environment Record）和一个可能为空的外部词法环境（outer Lexical Environment）引用组成。

简单来说，*词法环境*就是一种**标识符—变量**映射的结构(这里的**标识符**指的是变量/函数的名字，**变量**是对实际对象[包含函数和数组类型的对象]或基础数据类型的引用)。

举个例子，看看下面的代码：

```js
var a = 20;
var b = 40;
function foo() {
  console.log('bar');
}
```

上面代码的词法环境类似这样：

```js
lexicalEnvironment = {
  a: 20,
  b: 40,
  foo: <ref. to foo function>
}
```

每一个词法环境由下面三部分组成：

1. 环境记录；
2. 外部环境引用；
3. 绑定`this`;

##### 环境记录

所谓的环境记录就是词法环境中记录变量和函数声明的地方。

环境记录也有两种类型：

1. **声明类环境记录。**顾名思义，它存储的是变量和函数声明，函数的词法环境内部就包含着一个声明类环境记录。
2. **对象环境记录。**全局环境中的词法环境中就包含的就是一个对象环境记录。除了变量和函数声明外，对象环境记录还包括全局对象（浏览器的`window`对象）。因此，对于对象的每一个新增属性（对浏览器来说，它包含浏览器提供给`window`对象的所有属性和方法），都会在该记录中创建一个新条目。

**注意：**对函数而言，环境记录还包含一个`arguments`对象，该对象是个类数组对象，包含参数索引和参数的映射以及一个传入函数的参数的长度属性。举个例子，一个`arguments`对象像下面这样：

```js
function foo(a, b) {
  var c = a + b;
}
foo(2, 3);
// argument 对象类似下面这样
Arguments: { 0: 2, 1: 3, length: 2 }
```

（**译者注**：环境记录对象在创建阶段也被称为**变量对象(VO)**，在执行阶段被称为**活动对象(AO)**。之所以被称为**变量对象**是因为此时该对象只是存储执行上下文中变量和函数声明，之后代码开始执行，变量会逐渐被初始化或是修改，然后这个对象就被称为**活动对象**）

##### 外部环境引用

对于外部环境的引用意味着在当前执行上下文中可以访问外部词法环境。也就是说，如果在当前的词法环境中找不到某个变量，那么Javascript引擎会试图在上层的词法环境中寻找。（**译者注：**Javascript引擎会根据这个属性来构成我们常说的作用域链）

##### 绑定`this`

在词法环境创建阶段中，会确定`this`的值。

在全局执行上下文中，`this`值会被映射到全局对象中（在浏览器中，也就是`window`对象）。

在函数执行上下文中，`this`值取决于谁调用了该函数，如果是对象调用了它，那么就将`this`值设置为该对象，否则将`this`值设置为全局对象或是`undefined`(严格模式下)。例如：

```js
const person = {
  name: 'peter',
  birthYear: 1994,
  calcAge: function() {
    console.log(2018 - this.birthYear);
  }
}
person.calcAge(); 
// 上面calcAge的 'this' 就是 'person',因为calcAge是被person对象调用的
const calculateAge = person.calcAge;
calculateAge();
// 上面的'this' 指向全局对象(window)，因为没有对象调用它，或者说是window调用了它(window省略不写)
```

词法环境抽象出来类似下面的伪代码：

```js
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
     	// 标识符在这里绑定
    }
    outer: <null>,
    this: <global object>
  }
}
FunctionExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 标识符在这里绑定
    }
    outer: <Global or outer function environment reference>,
    this: <depends on how function is called>
   }
}
```

#### 变量环境

其实变量环境也是词法环境的一种，它的环境记录包含了**变量声明语句**在执行上下文中创建的变量和具体值的绑定关系。

如上所述，变量环境也是词法环境的一种，因此它具有词法环境所有的属性。

在ES6中，**词法环境**和**变量环境**的不同就是前者用来存储函数声明和变量声明（`let`和`const`）绑定关系，后者只用来存储`var`声明的变量绑定关系。

### 执行阶段

在这个阶段，将完成所有变量的赋值操作，然后执行代码。

##### 例子

我们看几个例子来理解上面的概念：

```js
let a = 20;
const b = 30;
var c;
function multiply(e, f) {
 var g = 20;
 return e * f * g;
}
c = multiply(20, 30);
```

当上面的代码被执行的时候，Javascript引擎会创建一个全局执行上下文去执行全局的代码。所以全局执行上下文在创建阶段看起来会像下面这样：

```js
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 标识符在这里绑定
      a: < uninitialized >,
      b: < uninitialized >,
      multiply: < func >
    }
    outer: <null>,
    ThisBinding: <Global Object>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 标识符在这里绑定
      c: undefined,
    }
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
```

在执行阶段，将完成变量的赋值操作，因此在执行阶段全局执行上下文看起来会像下面这样：

```js
GlobalExectionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 标识符在这里绑定
      a: 20,
      b: 30,
      multiply: < func >
    }
    outer: <null>,
    ThisBinding: <Global Object>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // 标识符在这里绑定
      c: undefined,
    }
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
```

当调用`multiply(20, 30)`时，将为该函数创建一个函数执行上下文，该函数执行上下文在创建阶段像下面这样：

```js
FunctionExectionContext = {
	LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 标识符在这里绑定
      Arguments: { 0: 20, 1: 30, length: 2 },
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
	VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
     	// 标识符在这里绑定
      g: undefined
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
```

然后，执行上下文进入执行阶段，这时候已经完成了变量的赋值操作。该函数上下文在执行阶段像下面这样：

```js
FunctionExectionContext = {
	LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 标识符在这里绑定
      Arguments: { 0: 20, 1: 30, length: 2 },
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
	VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // 标识符在这里绑定
      g: 20
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
```

函数执行完成后，返回值存储在变量`c`中，此时全局词法环境被更新。之后，全局代码执行完成，程序结束。

**注意：**你可能已经注意到上面代码，`let`和`const`定义的变量`a`和`b`在创建阶段没有被赋值，但`var`声明的变量从在创建阶段被赋值为`undefined`。

这是因为，在创建阶段，会在代码中扫描变量和函数声明，然后将函数声明存储在环境中，但变量会被初始化为`undefined`(`var`声明的情况下)和保持`uninitialized`(未初始化状态)(使用`let`和`const`声明的情况下)。

这就是为什么使用`var`声明的变量可以在变量声明之前调用的原因，但在变量声明之前访问使用`let`和`const`声明的变量会报错(TDZ)的原因。

这其实就是我们经常听到的**变量声明提升。**

**注意：**在**执行阶段**，如果Javascript引擎找不到`let`和`const`声明的变量的值，也会被赋值为`undefined`。

### 结论

如上，我们讲解了Javascript代码是如何执行的，虽然说成为一名优秀的Javascript开发者并不需要完全搞懂这些概念，但对上面的概念有深入的理解有助于我们去学习和理解其它概念，比如：变量声明提升，闭包，作用域链等。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)