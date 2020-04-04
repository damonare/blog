---
title: forEach循环中你不知道的3件事
categories:
  - 原创
  - 译文
tags:
  - JavaScript
toc: true
comments: true

---

## 前言

> 本文925字，阅读大约需要3分钟。

**总括:**  forEach无法被return,continue,break;

- 原文地址：[3 things you didn’t know about the forEach loop in JS](https://medium.com/front-end-weekly/3-things-you-didnt-know-about-the-foreach-loop-in-js-ff02cec465b1)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**自弃者扶不起，自强者击不倒。**

<!-- more -->

### 正文

你觉得你真的学会用`forEach`了么？

这是我之前对`forEach`循环的理解：就是一个普通语义化之后的`for`循环，可以被`break`,`continue`,`return`。

这篇文章将向你展示`forEach`中你可能不了解的3件事。

#### 1.  return不会停止循环

你觉得下面的代码在打印`1`和`2`之后会停止么？

```js
array = [1, 2, 3, 4];
array.forEach(function (element) {
  console.log(element);

  if (element === 2) 
    return;
  
});
// Output: 1 2 3 4
```

答案是不会，上述代码会正常打印1,2,3,4。如果你有`Java`背景，你也许会很诧异，这怎么可能呢？

原因是我们在`forEach`函数中传了一个回调函数，该回调函数的行为和普通函数一样，我们`return`操作其实就是普通函数中`return`。所以并不符合我们预期将`forEach`循环打断。

**[MDN官方文档：](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)**

> **注意：** 除了抛出异常以外，没有办法中止或跳出 `forEach()` 循环。如果你需要中止或跳出循环，`forEach()` 方法不是应当使用的工具。

我们将上述代码改写：

```js
const array = [1, 2, 3, 4];
const callback = function(element) {
    console.log(element);
    
    if (element === 2) 
      return; // would this make a difference? no.
}
for (let i = 0; i < array.length; i++) {
    callback(array[i]);
}
// Output: 1 2 3 4
```

这就是上述代码实际的执行思路，`return`操作只作用于当前的函数，自然不会对`for`循环产生影响

#### 2. 不能break

下面的代码你觉得会被`break`掉么？

```js
const array = [1, 2, 3, 4];
array.forEach(function(element) {
  console.log(element);
  
  if (element === 2) 
    break;
});
// Output: Uncaught SyntaxError: Illegal break statement
```

不会，甚至这行代码都不会运行，直接报错了。

那么这段代码如何达到我们原本想达到的效果呢？

用普通`for`循环就好了:

```js
const array = [1, 2, 3, 4];
for (let i = 0; i < array.length; i++) {
  console.log(array[i]);
  
  if (array[i] === 2) 
    break;
}
// Output: 1 2
```

#### 3. 不能continue

下面代码会是跳过2只打印1、3、4吗？

```js
const array = [1, 2, 3, 4];
array.forEach(function (element) {
  if (element === 2) 
    continue;
  
  console.log(element);
});
// Output: Uncaught SyntaxError: Illegal continue statement: no surrounding iteration statement
```

同样不会，和`break`一样，报错，这行代码之后甚至都不会运行。

怎么达到预期呢?

还是使用普通的`for`循环来解决：

```js
for (let i = 0; i < array.length; i++) {
  if (array[i] === 2) 
    continue;
  console.log(array[i]);
}
// Output: 1 3 4
```

#### 译者补充

`forEach`函数的实际运行原理其实是这样的，伪代码如下：

```js
let arr = [1, 2];
arr.forEach(function(ele) {
	console.log(ele);
}); 
// output: 1, 2
// 上面代码等同于
function func(ele) {
  console.log(ele);
}
for (let i = 0; i < arr.length; i++) {
	func(arr[i])
}
// output: 1, 2
```

实际上`forEach`的polyfill实现也是这样的，在`forEach`函数中执行一个`for`循环，在`for`循环里调用回调函数。

因此，像下面代码自然不会符合预期：

```js
let arr = [1, 2];
let sum = 0;
function add(a) {
	return a;
}
arr.forEach(async function(ele) {
  sum += await add(ele);
});
console.log(sum);
// Output：0
```

改写如下：

```js
let arr = [1, 2];
let sum = 0;
function add(a) {
	return a;
}
for (let i = 0; i < arr.length; i++) {
	sum += add(arr[i]);
}
console.log(sum);
// Output：3
```

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)