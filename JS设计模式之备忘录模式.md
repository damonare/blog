---
title:  JS设计模式之备忘录模式
categories:
  - 原创
tags:
  - JavaScript
  - 设计模式
  - 行为型模式
toc: true
comments: true
---

## 前言

> 本文423字，阅读大约需要2分钟。

**总括:** 在Javascript中实现23种经典设计模式的备忘录模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**博观而约取，厚积而薄发**。

<!-- more -->

## 正文

备忘录模式(Memento pattern):   **在不破坏对象的封装性的情况下，在对象之外捕获并保存该对象内部的状态以便日后对象使用或是恢复对象的某个状态**。

这个模式说白了就是实现内存缓存，以实现下次访问某个数据直接返回而不用计算。比如实现缓存计算值：

```js
function sum() {
  var a = 0;
  for (var  i = 0; i < arguments.length; i++) {
    a += arguments[i];
  }
  console.log(a);
  return a;
}
const cacheSum = (function() {
  var cache = {};
  return function() {
    var key = [].join.call(arguments, ',');
    if (!isNaN(cache[key])) {
       console.log('该计算结果已缓存:', cache[key]);
      return;
    }
    return cache[key] = sum.apply(this, arguments);
  }
})();
cacheSum(1, 2, 3, 4); // 10
cacheSum(1, 2, 3, 4); // 该计算结果已缓存: 10
```

这个例子之前在代理模式的时候有写过，实际上这既是一个代理模式也是一个备忘录模式的实现，就看从哪个角度去看他。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)