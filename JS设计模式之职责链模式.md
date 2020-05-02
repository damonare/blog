---
title:  JS设计模式之职责链模式
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

> 本文757字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的职责链模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**读书有三到，谓心到，眼到，口到**。

<!-- more -->

## 正文

职责链模式(Chain of Responsibility pattern):   **避免将一个请求的发送者与接受者耦合在一起,让多个对象都有机会处理请求**。

所谓的职责链顾名思义就是一条任务链条。比较经典的是一个ajax请求，分为以下部分：发起ajax请求任务 => 响应数据适配任务 => 根据数据呈现组件任务。伪代码如下：

```js
const sendData = function(data, dealType, dom) {
  console.log('发起ajax请求');
  // 假设存在request方法发起ajax请求
  const res = request(data);
  if (res.status >= 200) {
    dealData(res.data, dealType, dom);
  }
}
const dealData = function(data, dealType, dom) {
  if (dealType === '[object Array]') {
    createUI(data, dom);
  }
}
const createUI = function(data, dom) {
  var i = 0;
  var html = '';
  for (; i < data.length; i++) {
    html += `<li>${data[i]}</li>`
  }
  dom.innerHTML = html;
}
```

如上通过以上这种模式我们可以直接发起一个请求，而不必在乎接受者如何去处理这个请求，也就达到了请求发送者和请求的接受者解耦的目的。

另外一个比较好的优化在于业务中复杂的逻辑判断，比如实现一个购买系统，老的实现：

```js
const order = function(orderType, pay) {
  if (orderType === 'alipay') {
     console.log('使用支付宝支付' + pay);
    // 处理后续逻辑
  } else if (orderType === 'wechatpay') {
     console.log('使用微信支付' + pay);
    // 处理后续逻辑
  } else if (orderType === 'cash') {
    console.log('使用现金支付' + pay);
    // 处理后续逻辑
  } else {
    console.log('使用未知方式支付' + pay);
    // 处理后续逻辑
  }
}
order('alipay', '1000元'); // 使用支付宝支付1000元
```

每一种支付方式的处理逻辑可能非常的重，上面省略了很多代码，如果这样实现代码会非常不好维护，使用职责链模式优化：

```js
const order = function(orderType, pay) {
  if (orderType === 'alipay') {
    alipay(pay);
  } else if (orderType === 'wechatpay') {
    wechatpay(pay);
  } else if (orderType === 'cash') {
    cash(pay);
  } else {
    unknow(pay);
  }
}
const alipay = function(pay) {
    console.log('使用支付宝支付' + pay);
    // 处理后续逻辑
}
const wechatpay = function(pay) {
    console.log('使用微信支付' + pay);
    // 处理后续逻辑
}
const cash = function(pay) {
    console.log('使用现金支付' + pay);
    // 处理后续逻辑
}
const unknow = function(pay) {
    console.log('使用未知方式支付' + pay);
    // 处理后续逻辑
}
order('alipay', '1000元');
```

代码虽然增多了一丢丢，但和实际带来的效果相比实际上是微乎其微的。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)