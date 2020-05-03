---
title:  JS设计模式之观察者模式
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

> 本文831字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的观察者模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**与有肝胆人共事，从无字句处读书**。

<!-- more -->

## 正文

观察者模式(Observer pattern):   **定义了一种一对多的关系，观察者向订阅者发布更新信息**。

观察者模式可能是理解起来难度最大的一个设计模式，观察者模式又叫做发布-订阅模式，其实本质就是定义了两种概念，一个叫发布者，一个叫订阅者。实现一个观察者对象：

```js
const observer = (function() {
    var message = {};
    return {
        // 订阅某一条消息
        subscribe() {},
        // 发布某个消息
        publish() {},
        // 取消订阅
        unsubscribe() {}
    }
})();
```

下面实现这三个方法：

```js
const observer = (function() {
    const message = {};
    return {
        // 订阅某一条消息
        subscribe(type, callback) {
            if (message[type]) {
                message[type].push(callback);
            } else {
                message[type] = [callback];
            }
            // 返回取消订阅的方法
            return {
                unsubscribe: this.unsubscribe.bind(this, type, callback)
            }
        },
        // 发布某个消息
        publish(type, ...rest) {
            if (!message[type]) return;
            const params  = {
                type,
                rest
            };
            for (var i = 0; i < message[type].length; i++) {
              	// 将参数传递给回调函数
                message[type][i] && message[type][i].call(this, params);
            }
        },
        // 取消订阅
        unsubscribe(type, callback) {
            if (message[type] instanceof Array) {
                for (var i = 0; i < message[type].length; i++) {
                    if (message[type][i] === callback) {
                        message[type].splice(i, 1);
                    }
                }
            }
        }
    }
})();
```

简单使用下：

```js
var sub1 = observer.subscribe('demo', function(a) {
    console.log(1, a);
});
var sub2 = observer.subscribe('demo', function(a) {
    console.log(2, a);
});
observer.publish('demo', 'ceshi'); 
// 1 {type: "demo", rest: Array(1)}
// 2 {type: "demo", rest: Array(1)}
sub1.unsubscribe();
observer.publish('demo');
// 2 {type: "demo", rest: Array(1)}
```

再举一个更实际的例子，学生听课，老师讲课，学生可以订阅问题，学生实际就是个订阅的角色，老师来发布问题，老师是发布者这个角色，订阅了某个问题的学生才可以回答这个问题，首先实现学生类和老师类：

```js
function Student(name) {
    this.name = name;
    this.say = () => {
        console.log(this.name, '回答问题');
    }
}
Student.prototype.answer = function(question) {
    observer.subscribe(question, this.say);
}
Student.prototype.sleep = function(question) {
    console.log(this.name + '已经不想回答' + question + '这个问题了' )
    observer.unsubscribe(question, this.say);
}

function Teacher() {}
Teacher.prototype.ask = function(question) {
    console.log('老师发布问题：', question);
    observer.publish(question);
}
```

然后应用：

```js
var student1 = new Student('小明');
var student2 = new Student('李华');
var student3 = new Student('小红');
student1.answer('什么是观察者模式?');
student1.answer('什么是命令模式?');
student2.answer('什么是中介者模式?');
student2.answer('什么是观察者模式?');
student3.answer('什么是备忘录模式?');
student3.answer('什么是建造者模式?');
student3.sleep('什么是建造者模式?');
// 小红已经不想回答什么是建造者模式?这个问题了
var teacher = new Teacher();
teacher.ask('什么是观察者模式?');
// 老师发布问题： 什么是观察者模式?
// 小明 回答问题
// 李华 回答问题
teacher.ask('什么是备忘录模式?');
// 老师发布问题： 什么是备忘录模式?
// 小红 回答问题
```

如上的代码就是观察者模式的一个简单应用。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)