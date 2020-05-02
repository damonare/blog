---
title:  JS设计模式之解释器模式
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

> 本文818字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的解释器模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**与有肝胆人共事，从无字句处读书**。

<!-- more -->

## 正文

解释器模式(Interpreter pattern):   **对于一种语言，给出其文法表示形式，并定义一种解释器，通过这个解释器来解释语言中定义的句子**。

所谓解释器是实现了一种文法，一种对应关系，这有点像Typescript中枚举类型数据的实现：

```js
var colorMap = {};
(function() {
  colorMap[colorMap[1] = 'Red'] =  1;
  colorMap[colorMap[2] = 'yellow'] =  2;
  colorMap[colorMap[3] = 'blue'] =  3;
})(colorMap);
```

如上解释了一个颜色和数字的对应关系，这是个非常简单的实现。解释器的实质是对业务的需求，经过抽象提取成一个解释程序，而一个解释程序能否应用的关键是能否根据需求整理出一套可使用的语法规则。再看更简单的例子：

```js
function getReverseString(str) {
  return str.split('').reverse().join('');
}
console.log(getReverseString('123')); // 321
```

如上也是一个解释器，是对反转字符串的一个解释器。实际上JS内置了很多的解释器函数，可以直接去调用。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)