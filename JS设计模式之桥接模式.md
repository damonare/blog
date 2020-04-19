---
title:  JS设计模式之桥接模式
categories:
  - 原创
tags:
  - JavaScript
  - 设计模式
  - 结构型模式
toc: true
comments: true
---

## 前言

> 本文495字，阅读大约需要3分钟。

**总括:** 在Javascript中实现23种经典设计模式的桥接模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**熟读唐诗三百首，不会作诗也会吟**。

<!-- more -->

## 正文

桥接模式(Bridge pattern):   **将抽象部分与实现部分分离，使它们都可以独立地变化**。

比如我们实现一个常见的鼠标移入移出的效果：

html代码：

```html
 <span>123</span>
 <span>456</span>
```

JS实现鼠标移出移入的颜色背景更改：

```js
var spanList = document.querySelectorAll('span');
spanList[0].onmouseover = function() {
    this.style.color = '#fff';
    this.style.backgroundColor = '#000';
}
spanList[0].onmouseleave = function() {
    this.style.color = '#000';
    this.style.backgroundColor = '#fff';
}
spanList[1].onmouseover = function() {
    this.style.color = 'red';
    this.style.backgroundColor = 'blue';
}
spanList[1].onmouseleave = function() {
    this.style.color = 'blue';
    this.style.backgroundColor = 'red';
}
```

很明上面的代码实现很不优雅，存在着大量重复代码，颜色背景的设置完全可以抽象出来：

```js
function changeColor(color, bg) {
  this.style.color = color;
  this.style.backgroundColor = bg;
}
```

接下来就是要把元素绑定该函数：

```js
var spanList = document.querySelectorAll('span');
spanList[0].onmouseover = function() {
    changeColor.call(this, '#fff', '#000');
}
spanList[0].onmouseleave = function() {
    changeColor.call(this, '#000', '#fff');
}
spanList[1].onmouseover = function() {
    changeColor.call(this, 'red', 'blue');
}
spanList[1].onmouseleave = function() {
    changeColor.call(this, 'blue', 'red');
}
```

如上实现，代码量减少了很多的同时，我们也把这个功能抽象(changeColor函数)部分和实现(元素绑定事件)部分分离，之后即使有更改我们也可以去灵活的更改抽象实现部分或是具体实现部分，代码可维护性大大增加。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)