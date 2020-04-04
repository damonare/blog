---
title: 学习Javascript之尾调用
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文2433字，阅读大约需要10分钟。

**总括:**  本文介绍了尾调用，尾递归的概念，结合实例解释了什么是尾调用优化，并阐述了尾调用优化如今的现状。

- 参考文章：[尾递归的后续探究](https://imweb.io/topic/5a244260a192c3b460fce275)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**事亲以敬，美过三牲。**

<!-- more -->

## 正文

尾调用是函数式编程的一个重要的概念，本篇文章就来学习下尾调用相关的知识。

### 尾调用

在之前的文章[理解Javascript的高阶函数](https://blog.damonare.cn/2020/02/06/%E5%AD%A6%E4%B9%A0Javascript%E4%B9%8B%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0/)中，有说过在一个函数中输出一个函数，则这个函数可以被成为高阶函数。本文的主角尾调用和它类似，**如果一个函数返回的是另一个函数的调用结果，那么就被称为尾调用**。例子：

```js
function add(x, y) {
  return x + y;
}
function sum() {
  return add(1, 2);
}
```

如上就是一个尾调用的例子sum函数返回了add的调用结果。但下面的例子就不是尾调用：

```js
function add(x, y) {
  return x + y;
}
// 情况1
function sum() {
  return add(1, 2) + 1;
}
// 情况2
function sum2() {
  let a = add(1, 2);
  return a;
}
```

上例中情况1和情况2都不是尾调用，情况1在调用add函数后还有一个+1的操作，情况2在调用add函数后还有赋值给a的操作，因此上面的情况都不是尾调用。

### 尾递归

递归相信大家都知道，就是函数自己调用自己的一种操作。那么，**如果一个函数返回的是自己的调用结果就被称为尾递归**。也就是说尾递归一定是尾调用，但尾调用不一定是尾递归。先看一个常规递归的例子：

```js
function sum(n) {
  if (n <= 1) return 1;
  return sum(n - 1) + n;
}
sum(10000); // 50005000
```

如上sum函数就是一个递归函数，但他不符合我们上面对尾调用的定义，因此它不是一个尾调用函数，更不是一个尾递归函数。改写为尾递归函数：

```js
function sum(n, result = 1) {
  if (n <= 1) return result;
  return sum(n - 1, result + n);
}
sum(10000); //  Maximum call stack size exceeded
```

我们依然调用sum(10000)但这里却报错了，就是比较常见的堆栈溢出(stack overflow)。关于执行栈(也被称为调用栈)不了解的可以参考之前的博文：[理解Javascript中的执行上下文和执行栈](https://blog.damonare.cn/2020/02/09/%E7%90%86%E8%A7%A3Javascript%E4%B8%AD%E7%9A%84%E6%89%A7%E8%A1%8C%E4%B8%8A%E4%B8%8B%E6%96%87%E5%92%8C%E6%89%A7%E8%A1%8C%E6%A0%88/)。

### 尾调用优化

现在假设函数A是一个返回了函数B调用结果的函数。函数B是一个返回了函数C结果的函数。类似这样：

```js
function C() {}
function B() { return C(); }
function A() { return B(); }
A();
```

当函数A被调用的时候会有一个A的函数执行上下文被压入执行栈中，B调用的时候会有一个B的执行上下文被压入执行栈中，直到函数A和函数B都执行结束，对应的执行上下文才会被推出执行栈。如果函数B还返回了一个函数C的调用结果，也会重复这个过程，以此类推，如果这个执行栈内执行上下文的数量超过了最大值那么就会报出堆栈溢出的错误，这是前面的那个例子报错的缘由。看下图，上面函数的执行栈：

![](https://image.damonare.cn/tail-call-stack.png)

如果函数B中有对函数A中变量的引用，那么函数A即使执行结束对应的执行上下文也无法从执行栈中被推出，也就是我们常说的闭包。但如果函数B中没有对函数A的引用，执行结束后直接推出函数A的执行上下文多好。

上面的想法如果成真，执行栈中只需要保存上一个函数(最内层函数)的执行上下文就好了，这就是**尾调用优化。**

**尾调用优化：**对符合要求的尾调用函数，只在执行栈中保存最内层函数的执行上下文的一种实现。

如果我们优化生效，理想中的执行栈应该是这样的：

![](https://image.damonare.cn/tail-call-stack-more.png)

要知道一个执行上下文中保存的信息是很多的，尾调用优化如果生效，执行栈中的执行上下文只会存在一条，因此可以极大地节约内存。这就是**尾调用优化的意义**。

但尾调用优化仅仅是普通开发者去写可以被优化的函数是做不到的，这个特性一般都需要借助编译器或是运行环境来支持才可以。Javascript原来是不支持尾递归调用优化的**，ES6中才开始规定程序引擎应在严格模式下使用尾调用优化**。而且ECMAScript 6限定了尾位置不含闭包的尾调用才能进行优化。这和我们前面说的不谋而合。

但实际笔者经过测试，Chrome( 79.0.3945.130)、Safari( 13.0.3 )都还不支持，也就是说前面那个报堆栈溢出的错误依然会报。经过查资料，发现只有低版本的node才曾经支持过尾递归调用优化，node(6.0.0)是可以开启尾递归调用优化的。还是前面的例子，但开启了严格模式：

```js
'use strict';
function sum(n, result = 1) {
  if (n <= 1) return result;
  return sum(n - 1, result + n);
}
sum(10000);
```

我们看下使用node(6.0.0)调用上面代码的结果：

````js
RangeError: Maximum call stack size exceeded
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:4:13)
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:6:10)
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:6:10)
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:6:10)
````

如上还是报错了，堆栈溢出。不管是node还是浏览器对于尾递归调用优化默认都是关闭的，在node中需要加一个参数`--harmony_tailcalls`才能开启尾递归调用优化。再看下：

```js
$ node --harmony_tailcalls tail-call.js                        
5000050000
```

正常返回了结果。修改下代码我们看下实际的调用栈：

```js
'use strict';
function sum(n, result = 1) {
    console.trace();
    if (n <= 1) return result;
    return sum(n - 1, result + n);
}
const result = sum(3);
console.log(result);
```

未开启尾调用优化之前：

```js
Trace
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:3:13)
    at Object.<anonymous> (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:7:16)
Trace
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:3:13)
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:5:10)
    at Object.<anonymous> (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:7:16)
Trace
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:3:13)
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:5:10)
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:5:10)
    at Object.<anonymous> (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:7:16)
```

如上打印，无用的信息都被我删除掉了，我们再看下开启尾调用优化之后的：

```js
Trace
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:3:13)
    at Object.<anonymous> (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:7:16)
Trace
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:3:13)
    at Object.<anonymous> (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:7:16)
Trace
    at sum (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:3:13)
    at Object.<anonymous> (/Users/mac/Desktop/demo/html-css-js-demo/tail-call.js:7:16)
```

可以看到和我们预期的是一样的，执行栈中一直只有一个执行上下文。空间复杂度从O(n)被降到了O(1)。大大的节约了内存空间。

这里留给我们两个问题，一个是不开启尾递归调用优化的情况下堆栈溢出的报错如何解决，一个是尾递归调用既然好处这么大为啥要默认关闭呢？。先看第一个问题：

### 解决堆栈溢出报错

1. `for`循环。根本原因是执行上下文太多导致的爆栈，那么不调用函数自然可以解决这个问题：

```js
'use strict';
function sum(n) {
    let res = 0;
    for (var i = 0; i < n; i++) {
        res += i;
    }
    return res;
}
const result = sum(3);
console.log(result);
```

2. 某些情况下确实无法使用`for`循环，还是要调用函数，此时可以利用**弹跳床函数**，所谓弹跳床函数，相当于函数的一个中转站。

```js
// 弹跳床函数，执行函数，如果函数返回类型还是函数则继续执行，直到执行结束
function trampoline(f) {
  while (f && f instanceof Function) {
    f = f();
  }
  return f;
}
```

相应的我们的原函数需要改写如下：

```js
function sum(n, result = 1) {
  if (n <= 1) return result;
    return sum.bind(null, n - 1, result + n);
}
```

  此时调用：

```js
trampoline(sum(100000));
```

  就不会报错堆栈溢出了。

### 尾调用优化默认关闭

看到这想必一定很好奇，既然尾调用优化如此高效，为何都默认关闭了这个特性呢？答案分为两方面：

1. **隐式优化问题**。由于引擎消除尾递归是隐式的，函数是否符合尾调用而被消除了尾递归很难被程序员自己辨别；
2. **调用栈丢失问题**。尾调用优化要求除掉尾调用执行时的调用堆栈，这将导致执行流中的堆栈信息丢失。

Chrome下使用尾递归写法的方法依旧出现调用栈溢出的原因在于：

- `直接原因`： 各大浏览器（除了safari）根本就没部署尾调用优化；
- `根本原因`： 尾调用优化依旧有隐式优化和调用栈丢失的问题；

既然尾调用优化是默认关闭的，是不是说尾调用没什么用了呢？其实不然，尾调用是函数式编程一个重要的概念，合理的应用尾调用可以大大提高我们代码的可读性和可维护性，相比带来的一点性能损失，写更优雅更易读的代码更为的重要。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)