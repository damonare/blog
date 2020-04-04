---
title: 学习Javascript之模拟实现Promise
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文1683字，阅读大约需要5分钟。

**总括:**  本文使用低版本Javascript模拟实现了Promise对象。

- 参考文档：[Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)，[Promises/A+](https://promisesaplus.com/)
- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**盛年不重来，一日难再晨。**

<!-- more -->

## 正文

不熟悉`Promise`语法的同学可以去MDN的[Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)看下语法。本文旨在使用ES6版本以下的语法去模拟实现一个`Promise`，规范参考[Promises/A+](https://promisesaplus.com/),[中文版Promises/A+](https://www.ituring.com.cn/article/66566)。

### 初始化构造函数

实现基本的`Promise`构造函数，首先要具有以下属性和方法：

1. 当前`Promise`状态；
2. 当前的传递值；
3. 原型方法`then`;
4. 原型方法`catch`；

初始化后**第一版代码**如下：

```js
/**
 * @param {function} executor 
 * executor 形如 function(resolve, reject) {}
 */
function Promise(executor) {
  // 当前promise值
  this.value = null;
  // 存储回调函数
  this.callbacks = [];
  // 当前promise状态例
  this.status = 'pending';
  var _this = this;
  function resolve(initValue) {
    _this.value = initValue;
    _this.status = 'resolved';
  }
  function reject(initValue) {
    _this.value = initValue;
    _this.status = 'rejected';
  }
  executor(resolve);
}

Promise.prototype.then = function(onFulfilled, onRejected) {
  if(this.status === 'resolved') return this;
  return this;
}
Promise.prototype.catch = function(onRejected) {
  if(this.status === 'rejected') return this;
  return this.then(null, onRejected);
}
```

解释下上面代码，我们声明了一个`Promise`构造函数。然后在内部声明了`resolve`函数和`reject`函数，在函数内更改状态，然后将其传给`Promise`的参数executor。`this.value`储存当前传给回调函数的值，**`this.callbacks`数组存储的是回调函数，因为一个同一个`promise`实例可能会多次调用`then`方法**。`this.status`存储当前的`promise`状态，一旦状态更改再调用方法立即返回，状态不可再更改。方法`then`返回`this`从而支持链式调用。我们要实现的第一个特性就是异步，即`Promise`实例的`then`方法中的回调函数要异步执行。

### 异步

ES6新引入了微任务队列的概念，比如`Promise`实例中`then`的回调函数就是在微任务队列中等待执行，在低版本JS中我们可以使用`setTimeout`来模拟微任务队列。另外`then`和`catch`是可以链式调用的，而且`resolve`的参数要传到`then`的回调函数中，`reject`的参数要传到`catch`的回调函数中。而这里出现一个问题，`then`和`catch`方法都是同步调用的，而回调函数需要异步调用，这就意味着我们不能直接在`then`方法中直接将回调调用执行。所以我们需要将回调函数保存起来，延迟调用。另外标准里`then`方法返回的是一个`promise`实例，并不是`this`。综合以上叙述，我们需要实现的特性是：

1. `then`返回一个`promise`实例,该实例处于resolved状态(标准要求)；

```js
Promise.prototype.then = function(onFulfilled, onRejected) {
  var _this = this;
  return new Promise(function(resolve, reject) {
    if (_this.status === 'pending') {
      var defer = {
        // 当前promise实例的回调存储
        onFulfilled: onFulfilled || null,
        onRejected: onRejected || null,
        // 下一个promise实例的resolve和reject函数
        resolve: resolve || null,
        reject: reject || null
      };
      _this.defers.push(defer);
      return;
    }
    // 状态一旦更改直接返回将上一个then的回调函数返回值保存，传给下一个实例
    var ret = onFulfilled(_this.value);
    resolve(ret);
  });
}
```

解释下上面的代码，`then`方法返回了一个`promise`实例，通过在`defer`中保存当前`then`的回调函数和下一个实例的`resolve，reject`函数，将当前的`promise`实例和下一个`promise`实例连接在一起。根本原因在于我们的`then`方法，`catch`方法都是同步调用，但回调函数是异步调用，所以我们需要将当前实例的回调函数和下一个实例的`resolve，reject`函数保存起来，从而保证链式调用，值能传到下一个`promise`实例。


2. 回调函数延迟异步调用；

```js
function asyncExec(self) {
  setTimeout(function() {
    // 将所有回调执行
    this.defers.forEach(function(defer) {
      var callback = self.status === 'resolved' ? defer.onFulfilled : defer.onRejected;
      // 如果then回调函数为空，则执行实例的resolve或reject函数
      if (callback === null) {
        callback = self.status === 'resolved' ? defer.resolve : defer.reject;
        callback(self.value);
        return;
      }
      // 异步执行then回调函数
      var ret = callback(self.value);
      // 将值传给下一个promise实例
      defer.resolve(ret);
    });
  }.bind(self));
}
var _this = this;
function resolve(initValue) {
    _this.value = initValue;
    _this.status = 'resolved';
    asyncExec(_this);
}
function reject(initValue) {
  _this.value = initValue;
  _this.status = 'rejected';
  asyncExec(_this);
}
```

统一封装asyncExec方法，将所有的回调函数进行执行，此时promise的状态一定已经更改，通过判断状态来决定执行成功回调还是失败的回调。如果then方法没有传入参数则执行then返回的promise实例中的resolve或是reject函数。

### 异常处理

在JS中回调函数执行的异常可以使用`try/catch`捕获错误。

```js
try {
  // 异步执行then回调函数
  var ret = callback(self.value);
  // 将值传给下一个promise实例
  defer.resolve(ret);
} catch(e) {
  defer.reject(e);
}
```

上面代码通过`try/catch`捕获执行中的错误，然后将错误传到下一个实例(then方法返回的实例)的reject函数中。

### 静态方法

Promise构造函数除了原型方法之外，在Promise函数上改挂在着resolve方法和reject方法，方便调用：

```js
PromiseNew.resolve = function(value) {
  return new PromiseNew(function(resolve) {
    resolve(value);
  });
}
PromiseNew.reject = function(value) {
  return new PromiseNew(function(_, reject) {
    reject(value);
  });
}
```

### 完整实现

```js
/**
 * @param {function} executor 
 * 形如 function(resolve, reject) {}
 */
function Promise(executor) {
  // 当前promise值
  this.value = null;
  // 存储回调函数
  this.defers = [];
  // 当前promise状态例
  this.status = 'pending';
  var _this = this;
  function resolve(initValue) {
    _this.value = initValue;
    _this.status = 'resolved';
    asyncExec(_this);
  }
  function reject(initValue) {
    _this.value = initValue;
    _this.status = 'rejected';
    asyncExec(_this);
  }
  function asyncExec(self) {
    setTimeout(function() {
      // 将所有回调执行
      // console.warn(this.defers);
      this.defers.forEach(function(defer) {
        // 一旦状态更改，直接返回回调函数
        var callback = self.status === 'resolved' ? defer.onFulfilled : defer.onRejected;
        if (callback === null) {
            callback = self.status === 'resolved' ? defer.resolve : defer.reject;
            callback(self.value);
            return;
        }
        try {
          // 异步执行then回调函数
          var ret = callback(self.value);
          // 将值传给下一个promise实例
          defer.resolve(ret);
        } catch(e) {
          defer.reject(e);
        }
      });
    }.bind(self));
  }
  executor(resolve, reject);
}

Promise.prototype.then = function(onFulfilled, onRejected) {
  var _this = this;
  return new Promise(function(resolve, reject) {
    if (_this.status === 'pending') {
      _this.defers.push({
        // 当前promise实例的回调存储
        onFulfilled: onFulfilled || null,
        onRejected: onRejected || null,
        // 下一个promise实例的resolve和reject函数
        resolve: resolve || null,
        reject: reject || null
      });
      return;
    }
    // 状态一旦更改直接返回新的promise状态
    try {
      // 异步执行then回调函数
      var ret = onFulfilled(self.value);
      // 将值传给下一个promise实例
      resolve(ret);
    } catch(e) {
      reject(e);
    }
  });
}

Promise.prototype.catch = function(onRejected) {
  return this.then(null, onRejected);
}

Promise.resolve = function(value) {
  return new Promise(function(resolve) {
    resolve(value);
  });
}
Promise.reject = function(value) {
  return new Promise(function(_, reject) {
    reject(value);
  });
}
```

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)