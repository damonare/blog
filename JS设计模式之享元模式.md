---
title:  JS设计模式之享元模式
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

> 本文1235字，阅读大约需要6分钟。

**总括:** 在Javascript中实现23种经典设计模式的享元模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**岂能尽如人意，但求无愧我心**！

<!-- more -->

## 正文

享元模式(Flyweight pattern):   **通过共享技术支持大量细粒度的对象，避免对象间拥有相同内容造成多余的开销**。

### 简单的例子

享元模式往往适用于处理某些应用很多相似对象造成的内存开销问题。使用享元模式往往是使用时间换空间的一种策略。我们以搜索一个东西的搜索结果为例，简单看下：

```html
<div id="container"></div>
<div id="next">下一页</div>
```

初步实现的js逻辑如下：

```js
// 当前页面显示的搜索结果数
var curLen = 5;
// 总的搜索结果
var searchList = Array(1000).fill('').map((_, index) => `<span>搜索结果${index}</span>`);
// 当前页数
var curPages = 0;
for (var i = 0; i < searchList.length; i++) {
    // 创建dom元素
    let dom = document.createElement('div');
    dom.innerHTML = `<span>搜索结果${i}</span>`;
    if (i >= curLen) {
        dom.style.display = 'none';
    }
    document.getElementById('container').appendChild(dom);
}
// 给下一页按钮绑定事件
document.getElementById('next').onclick = function(e) {
    // 查找dom元素
    var divs = document.getElementById('container').getElementsByTagName('div');
    var n = 0;
    n = ++curPages % Math.ceil(searchList.length / curLen) * curLen;
    for(let j = 0; j < searchList.length; j++) {
        divs[j].style.display = 'none';
    }
    for(let k = 0; k < curLen; k++) {
        if (divs[n + k]) {
            divs[n + k].style.display = 'block';
        }
    }
}
```

上面的代码中我们假设搜索结果有1000条，然后代码中创建了1000个DOM元素，插入到html标签中。当点击下一页的时候对这1000个DOM元素进行显示隐藏的样式修改从而达到页面切换的目的。此时只是实现了一个1000条DOM元素，但平时使用搜索引擎的搜索结果岂止百万，几亿条搜索结果也是有可能的，如果我们还像上面那样实现，真的插入几亿条DOM，浏览器很容易就垮掉了。因此真实环境中的代码一定不能这样实现，我们看下利用享元模式如何来优化上面的代码，首先可以看到上面代码中每一个DOM元素其实是相似的，不同的只是每一条DOM中的内容，因此我们可以尝试把这些DOM的公共部分提取出来。另外插入DOM元素过多的问题，也完全可以通过更改固定结构的内容来实现。看下代码：

```js
var Flyweight = (function() {
  var doms = [];
  function createDom() {
    let dom = document.createElement('div');
    document.getElementById('container').appendChild(dom);
    doms.push(dom);
    return dom;
  }
  return {
      getDom() {
          if (doms.length < curLen) {
              return createDom();
          } else {
              let dom = doms.shift();
              doms.push(dom);
              return dom;
          }
      }
  }
})();
```

然后具体的业务逻辑代改写如下：

```js
for (var i = 0; i < curLen; i++) {
  if (searchList[i]) {
    Flyweight.getDom().innerHTML = `<span>搜索结果${i}</span>`;
  }
}
// 给下一页按钮绑定事件
document.getElementById('next').onclick = function(e) {
  if (searchList.length < curLen) return;
  var n = 0;
  n = ++curPages % Math.ceil(searchList.length / curLen) * curLen;
  for (let j = 0; j < curLen; j++) {
      // 如果存在n +j 条则插入内容
      if (searchList[n + j]) {
          Flyweight.getDom().innerHTML = searchList[n + j];
        / 否则插入起始位置的第n + j - searchList.length条
      } else if (searchList[n + j - searchList.length]) {
        Flyweight.getDom().innerHTML = searchList[n + j - searchList.length];
       // 如果不存则插入空字符串
      } else {
        Flyweight.getDom().innerHTML = '';
      }
  }
}
```

如上此时的实现需要用到的DOM就只有5条，即当前页数所显示的最多的搜索结果数。此时如果查看DOM结构的话会发现Elements中只有5条DOM元素，切换页数只是会更改这5条DOM元素的内容。

#### 应用

享元模式的核心思想在于减少共享对象的数量。

-   内部状态存储于对象内部；
-   内部状态可以被一些对象共享；
-   内部状态独立于具体的场景，通常不会改变 ；
-   外部状态通常取决于具体的业务，根据业务的变化而更改；

上面的说法比较抽象，但代入上面的例子基本也可以理解的。

如果一个业务满足下面的几个场景就可以使用享元模式来处理：

-   一个业务中存在大量相似对象；
-   使用相似对象造成了内存的浪费；
-   对象的大部分状态可以变为外部状态；
-   剥离出对象的外部状态后，可以用较少的共享对象来取代大量的相似对象；

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)