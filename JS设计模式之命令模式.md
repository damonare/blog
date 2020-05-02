---
title:  JS设计模式之命令模式
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

**总括:** 在Javascript中实现23种经典设计模式的命令模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**非学无以广才，非志无以成学**。

<!-- more -->

## 正文

命令模式(Command pattern):   **将参数封装在一个对象里进行请求**。

所谓的命令模式主要的思想是将参数封装在一个对象中进行请求，看一个购买产品的伪代码，具体如下：

```js
var ProductManager = {
    // 请求信息
    requestInfo: function (model, id) {
        return '请求远程信息' + model + ' with ID ' + id;
    },
    // 购买汽车
    buyProduct: function (model, id) {
        return '成功购买了 ' + id + ', a ' + model;
    },
    // 组织view
    changeView: function (model, id) {
        return '创建UI' + model + ' ( ' + id + ' ) ';
    }
};
ProductManager.execute = function (command) {
    return ProductManager[command.request](command.model, command.carID);
};
console.log(ProductManager.execute({ request: "changeView", model: '哈哈哈', carID: '145523' }));
// 创建UI哈哈哈 ( 145523 ) 
console.log(ProductManager.execute({ request: "requestInfo", model: '接口', carID: '543434' }));
// 请求远程信息接口 with ID 543434
```

如上直接把请求信息封装在一个对象中进行请求，这种模式也在业务中经常用来请求远程接口。通过将接口参数，请求的接口命令，请求ID等信息等封装在一个对象中进行请求。

命令模式也比较容易实现一个命令队列，做到一个命令执行结束后才会接着执行下一个命令，这在实现动画或是队列请求中比较有用。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)