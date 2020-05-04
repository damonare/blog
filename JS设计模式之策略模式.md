---
title:  JS设计模式之策略模式
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

**总括:** 在Javascript中实现23种经典设计模式的策略模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**人生在勤，不索何获**。

<!-- more -->

## 正文

策略模式(Strategy pattern):   **将定义的一组算法封装起来，使其互相之间可以替换**。

策略模式和状态模式很像，在表单提交中取校验输入值是常见的需求，比如：

-   用户名不能为空；
-   是否是数字；
-   手机号码的校验；
-   邮箱的校验；

```js
var inputStrategy = function() {
  var strategy = {
    // 不能为空
    notNull(value) {
      return /\s+/.test(value) ? '内容不能为空' : '';
    },
    // 是否是数字
    number(value) {
      return /^[0-9]+(\.[0-9]+)?$/.test(value) ? '' : '请输入数字';
    },
    // 手机号校验
    phone(value) {
      return /^[19]\d{10}$/.test(phone) ? '' : '请输入正确格式电话号码';
    },
    // 邮箱校验
    email(value) {
      return /^[\w+-]+(\.[\w+-]+)*@[a-z\d-]+(\.[a-z\d-]+)*\.([a-z]{2,4})$/i.test(value) ? '' : '请输入正确邮箱';
    }
  }
  return {
      check(type, value) {
          return strategy[type] ? strategy[type](value) : '没有该类型的算法';
      },
      add(type, fn) {
        if (strategy[type]) return '该类型算法已存在'
        strategy[type] = fn;
      }
  }
}();
```

如上就是一个策略模式的应用，他看起来和状态模式很相似，但其实有很大的不同，状态模式说的是状态和行为(UI 或是 业务逻辑)的一种对应关系，使用状态模式可以将不同状态下对应的行为从复杂的`if/else`中逻辑判断中抽离出来。

### 状态模式和策略模式

相同点：

-   两者都有一个状态对应关系，一个策略或是一个状态类，上下文通过请求某个状态来执行逻辑；

不同点：

-   状态模式是状态和行为(UI 或是 业务逻辑)的对应关系，策略模式则是状态和算法的映射关系，可以互相替代，甚至可以去切换算法，但状态模式则不能根据状态去互相替代，否则就产生不同的行为后果。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)