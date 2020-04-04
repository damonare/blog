---
title: 学习Javascript数据结构之栈和队列
categories:
    - 原创
tags:
    - Javascript
    - 数据结构
    - 算法
comments: true
---

## 前言

**总括：** 本文使用Javascript数组的API定义了栈和队列并较为详细的说明了栈和队列的概念。

- 原文地址：[学习javascript数据结构(一)—栈和队列](http://blog.damonare.cn/2017/01/16/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%B8%80%EF%BC%89%E2%80%94%E2%80%94%E6%A0%88%E5%92%8C%E9%98%9F%E5%88%97/#more)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)


**只要你不计较得失，人生还有什么不能想法子克服的。**

<!-- more-->

## 正文

几乎所有的编程语言都原生支持数组类型，因为数组是最简单的内存数据结构。javascript也有数组类型，而数组呢，其实就是一种特殊的栈或是队列，利用javascript&nbsp;Array所内置的API可以很方便的模拟栈和队列。

我想对于数组每一个学过编程语言的都不会陌生吧，我们知道，我们可以在数组的任意位置添加或是删除元素，然而，有时候我们还需要一种在添加或是删除元素的时候有更多控制的数据结构。有两种数据结构类似于数组。但在添加或是删除元素的时候更为的可控。他们就是栈和队列。

### 栈

>  栈是一种遵从后进先出(LIFO)原则的有序集合。新添加的或是待删除的元素都保存在栈的末尾。我们称作栈顶，而另一端我们称作栈底。

在现实生活中就有很多栈的例子，比如下图的书本，这一摞书如果要取肯定是先去最上面的那一本，但它是最后一个放上去的，也就是栈顶的元素都是待添加或是待删除的。这就是后进先出的实际例子。

![栈](http://img.blog.csdn.net/20161102145616341)

#### 栈的创建

首先我们先声明一个函数：

```javascript
function Stack() {
    //各种属性和方法的声明
}
```

然后我们需要一种数据结构来保存栈里面的数据：

```javascript
var items = [];
```

接下来，我们需要给栈声明一些方法：

- push(element): 添加一个或是几个新元素到栈顶。
- pop(): 移除栈顶的元素，同时返回被移除元素。
- peek(): 返回栈顶的元素，但并不对栈顶的元素做出任何的修改。
- isEmpty(): 检查栈内是否有元素，如果有返回true，没有返回false。
- clear(): 清除栈里的元素。
- size(): 返回栈的元素个数。
- print(): 打印栈里的元素。

#### 栈的完整代码

我们通过Javascript提供的API，实现栈如下：

```javascript
function Stack() {
    var items = [];
    this.push = function(element) {
        items.push(element);
    };
    this.pop = function() {
        return items.pop();
    };
    this.peek = function() {
        return items[items.length-1];
    };
    this.isEmpty = function(){
        return items.length == 0;
    };
    this.size = function() {
        return items.length;
    };
    this.clear = function() {
        items = [];
    };
    this.print = function() {
        console.log(items.toString());
    };
    this.toString = function() {
        return items.toString();
    };
}
```
ES6版本：

```javascript
let _items = Symbol();
class Stack2 {
    constructor () {
        this[_items] = [];
    }
    push(element){
        this[_items].push(element);
    }
    pop(){
        return this[_items].pop();
    }
    peek(){
        return this[_items][this[_items].length-1];
    }
    isEmpty(){
        return this[_items].length == 0;
    }
    size(){
        return this[_items].length;
    }
    clear(){
        this[_items] = [];
    }
    print(){
        console.log(this.toString());
    }
    toString(){
        return this[_items].toString();
    }
}
```

#### 使用栈

创建完了栈，也给他了方法，然后我们来实例化一个对象：

```javascript
var stack=new Stack();
console.log(stack.isEmpty());
//true
stack.push(1);
stack.push(3);
//添加元素
console.log(stack.peek());
//输出栈顶元素3
console.log(stack.size());
//2
```
其余方法调用读者可自行尝试。

### 队列

我们已经接触了栈，接下来要说的队列和栈十分相似，他们都是线性表，元素都是有序的
。队列和栈不同的是，队列遵循的是FIFO，也就是先进先出的原则。队列从尾部添加新元素，从顶部移除元素，最新添加的元素必须排列在队列的末尾。<br><br>
在现实生活中，最常见的队列就是排队，如下图，先进入队列的先接受服务，后进入队列的必须排在队列末尾。

![队列](http://img.blog.csdn.net/20161102145601043)

#### 队列的创建

首先我们声明一个类：

```javascript
function Queue() {
    //这里是队列的属性和方法
}
```

然后我们同样创建一个保存元素的数组：

```javascript
var items = [];
```

接下来声明一些队列可用的方法：

- enqueue(element): 向队列尾部添加一个（或是多个）元素。
- dequeue(): 移除队列的第一个元素，并返回被移除的元素。
- front(): 返回队列的第一个元素——最先被添加的也是最先被移除的元素。队列不做任何变动。
- isEmpty(): 检查队列内是否有元素，如果有返回true，没有返回false。
- size(): 返回队列的长度。
- print(): 打印队列的元素。

#### 队列的完整代码

我们通过Javascript提供的API，实现队列如下：

```javascript
function Queue() {
    var items = [];
    this.enqueue = function(element) {
        items.push(element);
    };
    this.dequeue = function() {
        return items.shift();
    };
    this.front = function() {
        return items[0];
    };
    this.isEmpty = function() {
        return items.length == 0;
    };
    this.clear = function() {
        items = [];
    };
    this.size = function() {
        return items.length;
    };
    this.print = function() {
        console.log(items.toString());
    };
}
```
ES6版本:

```javascript
let Queue2 = (function () {
    const items = new WeakMap();
    class Queue2 {
        constructor () {
            items.set(this, []);
        }
        enqueue(element) {
            let q = items.get(this);
            q.push(element);
        }
        dequeue() {
            let q = items.get(this);
            let r = q.shift();
            return r;
        }
        front() {
            let q = items.get(this);
            return q[0];
        }
        isEmpty(){
            return items.get(this).length == 0;
        }
        size(){
            let q = items.get(this);
            return q.length;
        }
        clear(){
            items.set(this, []);
        }
        print(){
            console.log(this.toString());
        }
        toString(){
            return items.get(this).toString();
        }
    }
    return Queue2;
})();
```

#### 使用队列

创建完了队列，也给他了方法，然后我们来实例化一个对象：

```javascript
var queue = new Queue();
console.log(queue.isEmpty());
//true
queue.enqueue(1);
queue.enqueue(3);
//添加元素
console.log(queue.front());
//返回队列的第一个元素1
console.log(queue.size());
//2
//输出元素个数
```

## 后记

> 这篇博客使用javascript实现了栈和队列这两种数据结构。关于具体的应用的有机会补上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)