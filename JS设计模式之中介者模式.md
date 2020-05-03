---
title:  JS设计模式之中介者模式
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

> 本文821字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的中介者模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**与有肝胆人共事，从无字句处读书**。

<!-- more -->

## 正文

中介者模式(Mediator pattern):   **通过一个中介者对象封装一系列对象之间的交互，使得对象和对象之间不再互相应用，降低他们之间的耦合**。

### 例子

《中国机长》中除了飞机上的发生的事情外，其它的事情基本都是在机场控制系统里面发生的，这个机场控制系统就是一个中介，负责传递不同飞机之间的信息，控制着飞机的起飞和降落。这个机场控制系统就是整个系统的关键，其实就是我们这里所说的中介者角色。我们实现这样一个例子：

```js
function Animal(type, name) {
    this.type = type;
    this.name = name;
    this.point = 0;
}

Animal.prototype.play = function () {
    this.point += 1;
    mediator.played();
};

const scoreboard = {
    // 打印点击次数
    update: function (score) {
        // 当前分数
        console.log(score);
    }
};

const  mediator = {

    // 所有的animal
    animals: {},

    // 初始化
    setup: function () {
        var animals = this.animals;
        animals.cat = new Animal('cat', 'Tom');
        animals.mouse = new Animal('mouse', 'jerry');
    },
    // play以后，更新分数
    played: function () {
        const animals = this.animals;
        const score = {
            cat: animals.cat.point,
            mouse: animals.mouse.point
        };
        scoreboard.update(score);
    },
    // 处理用户按键交互
    keypress: function (e) {
        if (e.which === 49) { // 数字键 "1"
            mediator.animals.cat.play();
            return;
        }
        if (e.which === 48) { // 数字键 "0"
            mediator.animals.mouse.play();
            return;
        }
    }
};
// go!
mediator.setup();
window.onkeypress = mediator.keypress;
```

如上代码`mediator`就是中介者对象，通过在这个对象里面处理不同对象的交互关系，因此中介者对象往往是最复杂的那个对象。中介者对象同样也可以是个消息系统：

```js

const Mediator = (function() {
    var _msg = {};
    return {
        register(type, action) {
            if (_msg[type]) {
                _msg[type].push(action);
            } else {
                _msg[type] = [action];
            }
        },
        send(type) {
            if (_msg[type]) {
                for (var i = 0; i < _msg[type].length; i++) {
                    _msg[type][i] && _msg[type][i]();
                }
            }
        }
    }
})();
Mediator.register('demo', function() {
    console.log(1);
});
Mediator.register('demo', function() {
    console.log(2);
});
Mediator.send('demo');
```

如上是一个消息系统的实现，同样也是个中介者模式。

### 对比

-   观察者模式： 和观察者模式一样，中介者模式也是为了解决不同对象间的耦合问题，不同的是中介者模式还包含了对象之间的交互，观察者模式中的订阅者是双向的，既可以是消息的发布者，也可以是订阅者。而中介者模式中订阅是单向的，只能是订阅者，消息统一由中介者对象发布。
-   外观模式，中介者模式的封装在于不同对象的交互关系的封装，而外观模式则是为了提供一个更为简单易用的接口并不会添加其他功能。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)