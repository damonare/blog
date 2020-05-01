---
title:  JS设计模式之外观模式
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

> 本文500字，阅读大约需要3分钟。

**总括:** 在Javascript中实现23种经典设计模式的外观模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**吾生也有涯，而知也无涯**。

<!-- more -->

## 正文

外观模式(Facade pattern):   **为一组复杂的子系统接口提供一个更高级的统一接口，通过这个接口使得对子系统接口访问更容易**。

外观模式在JS中比较经典的应用是对浏览器中绑定事件的实现。在`chrome`中给一个元素绑定事件是这样做的：

```js
document.addEventListener('click', function(e) {
  e.preventDefault();
  console.log('click');    
})
```

但在老版IE中(低于9)是没有`addEventListener`这个`API`的，低版本IE中绑定事件要这样做：

```js
document.attachEvent('onclick', function(e) {
  e.preventDefault();
  console.log('click');    
})
```

我们需要兼容一下两个不同平台的实现：

```js
var addEvent = function (el, ev, fn) {
    if (el.addEventListener) {
        el.addEventListener(ev, fn, false);
    } else if (el.attachEvent) {
        el.attachEvent('on' + ev, fn);
    } else {
        el['on' + ev] = fn;
    }
};
```

如上`addEvent`函数就是外观模式的一个经典实现，它磨平了不同平台绑定事件`API`不同的问题，让使用者直接调用`addEvent`方法即可而不用在意兼容平台的问题。

jQuery中其实就有很多外观模式的应用，jQuery的经典之处很重要的一点就在于它使得开发者不用去在乎诸多的平台API上的区别。比如对于`.css`方法，`.html()`方法等都是外观模式的一种实现。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)