---
title: 学习Javascript数据结构之链表
categories:
    - 原创
tags:
    - Javascript
    - 数据结构
    - 算法
comments: true
---

## 前言

**总括：** 本文详细解释了数据结构中的链表的概念并阐述了如何在Javascript中如何去创建一个链表。

- 原文地址：[学习javascript数据结构(二)——链表](http://blog.damonare.cn/2016/11/26/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%BA%8C%EF%BC%89%E2%80%94%E2%80%94%E9%93%BE%E8%A1%A8/)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生总是直向前行走，从不留下什么。**

<!-- more-->

## 正文

### 链表简介

&nbsp;&nbsp;&nbsp;&nbsp;上一篇博客-[学习javascript数据结构(一)——栈和队列](http://damonare.github.io/2016/11/01/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%B8%80%EF%BC%89%E2%80%94%E2%80%94%E6%A0%88%E5%92%8C%E9%98%9F%E5%88%97/#more)说了栈和队列在javascript中的实现,我们运用javascript提供的API很容易的实现了栈和队列，但这种数据结构有一个很明显的缺点，因为数组大小是固定的所以我们在移除或是添加一项数据的时候成本很高，基本都需要吧数据重排一次。(javascript的Array类方法虽然很方便但背后的原理同样是这样的)

&nbsp;&nbsp;&nbsp;&nbsp;相比数组我们今天主角——链表就要来的随性的多，简单的理解可以是这样：在内存中，栈和队列（数组）的存在就是一个整体，如果想要对她内部某一个元素进行移除或是添加一个新元素就要动她内部所有的元素，所谓牵一发而动全身；而链表则不一样，每一个元素都是由元素本身数据和指向下一个元素的指针构成，所以添加或是移除某一个元素不需要对链表整体进行操作，只需要改变相关元素的指针指向就可以了。

&nbsp;&nbsp;&nbsp;&nbsp;链表在实际生活中的例子也有很多，比如自行车的链条，环环相扣，但添加或是移除某一个环节只需要对症下药，对相关环节进行操作就OK。再比如：火车，火车就是一个链表，每一节车厢就是元素，想要移除或是添加某一节车厢，只需要把连接车厢的链条改变一下就好了。那么，在javascript中又该怎么去实现链表结构呢？

### 链表的创建

首先我们要创建一个链表类：

```javascript
function LinkedList() {
    //各种属性和方法的声明
}
```

然后我们需要一种数据结构来保存链表里面的数据：

```javascript
var Node=function(element){
    this.element = element;
    this.next = null;
}
//Node类表示要添加的元素，他有两个属性，一个是element，表示添加到链表中的具体的值；另一个是next,表示要指向链表中下一个元素的指针。
```

接下来，我们需要给链表声明一些方法：

- append(element): 向链表尾部添加一个新的元素；
- insert(position,element): 向链表特定位置插入元素；
- remove(element): 从链表移除一项；
- indexOf(element): 返回链表中某元素的索引，如果没有返回-1；
- removeAt(position): 从特定位置移除一项；
- isEmpty(): 判断链表是否为空，如果为空返回true,否则返回false;
- size(): 返回链表包含的元素个数；
- toString(): 重写继承自Object类的toString()方法，因为我们使用了Node类；

### 链表的完整代码：

```javascript
function LinkedList() {
    //Node类声明
    let Node = function(element){
        this.element = element;
        this.next = null;
    };
    //初始化链表长度
    let length = 0;
    //初始化第一个元素
    let head = null;
    this.append = function(element){
        //初始化添加的Node实例
        let node = new Node(element),
            current;
        if (head === null){
            //第一个Node实例进入链表，之后在这个LinkedList实例中head就不再是null了
            head = node;
        } else {
            current = head;
            //循环链表知道找到最后一项，循环结束current指向链表最后一项元素
            while(current.next){
                current = current.next;
            }
            //找到最后一项元素后，将他的next属性指向新元素node,j建立链接
            current.next = node;
        }
        //更新链表长度
        length++;
    };
    this.insert = function(position, element){
        //检查是否越界，超过链表长度或是小于0肯定不符合逻辑的
        if (position >= 0 && position <= length){
            let node = new Node(element),
                current = head,
                previous,
                index = 0;
            if (position === 0){
                //在第一个位置添加
                node.next = current;
                head = node;
            } else {
                //循环链表，找到正确位置，循环完毕，previous，current分别是被添加元素的前一个和后一个元素
                while (index++ < position){
                    previous = current;
                    current = current.next;
                }
                node.next = current;
                previous.next = node;
            }
            //更新链表长度
            length++;
            return true;
        } else {
            return false;
        }
    };
    this.removeAt = function(position){
        //检查是否越界，超过链表长度或是小于0肯定不符合逻辑的
        if (position > -1 && position < length){
            let current = head,
                previous,
                index = 0;
            //移除第一个元素
            if (position === 0){
                //移除第一项，相当于head=null;
                head = current.next;
            } else {
                //循环链表，找到正确位置，循环完毕，previous，current分别是被添加元素的前一个和后一个元素
                while (index++ < position){
                    previous = current;
                    current = current.next;
                }
                //链接previous和current的下一个元素，也就是把current移除了
                previous.next = current.next;
            }
            length--;
            return current.element;
        } else {
            return null;
        }
    };
    this.indexOf = function(element){
        let current = head,
            index = 0;
        //循环链表找到元素位置
        while (current) {
            if (element === current.element) {
                return index;
            }
            index++;
            current = current.next;
        }
        return -1;
    };
    this.remove = function(element){
        //调用已经声明过的indexOf和removeAt方法
        let index = this.indexOf(element);
        return this.removeAt(index);
    };
    this.isEmpty = function() {
        return length === 0;
    };
    this.size = function() {
        return length;
    };
    this.getHead = function(){
        return head;
    };
    this.toString = function(){
        let current = head,
            string = '';
        while (current) {
            string += current.element + (current.next ? ', ' : '');
            current = current.next;
        }
        return string;
    };
    this.print = function(){
        console.log(this.toString());
    };
}
//一个实例化后的链表，里面是添加的数个Node类的实例
```
ES6版本:

```javascript
let LinkedList2 = (function () {
    class Node {
        constructor(element){
            this.element = element;
            this.next = null;
        }
    }
    //这里我们使用WeakMap对象来记录长度状态
    const length = new WeakMap();
    const head = new WeakMap();
    class LinkedList2 {
        constructor () {
            length.set(this, 0);
            head.set(this, null);
        }
        append(element) {
            let node = new Node(element),
                current;
            if (this.getHead() === null) {
                head.set(this, node);
            } else {
                current = this.getHead();
                while (current.next) {
                    current = current.next;
                }
                current.next = node;
            }
            let l = this.size();
            l++;
            length.set(this, l);
        }
        insert(position, element) {
            if (position >= 0 && position <= this.size()) {

                let node = new Node(element),
                    current = this.getHead(),
                    previous,
                    index = 0;
                if (position === 0) {
                    node.next = current;
                    head.set(this, node);
                } else {
                    while (index++ < position) {
                        previous = current;
                        current = current.next;
                    }
                    node.next = current;
                    previous.next = node;
                }
                let l = this.size();
                l++;
                length.set(this, l);
                return true;
            } else {
                return false;
            }
        }
        removeAt(position) {
            if (position > -1 && position < this.size()) {
                let current = this.getHead(),
                    previous,
                    index = 0;
                if (position === 0) {
                    head.set(this, current.next);
                } else {
                    while (index++ < position) {
                        previous = current;
                        current = current.next;
                    }
                    previous.next = current.next;
                }
                let l = this.size();
                l--;
                length.set(this, l);
                return current.element;
            } else {
                return null;
            }
        }
        remove(element) {
            let index = this.indexOf(element);
            return this.removeAt(index);
        }
        indexOf(element) {
            let current = this.getHead(),
                index = 0;
            while (current) {
                if (element === current.element) {
                    return index;
                }
                index++;
                current = current.next;
            }
            return -1;
        }
        isEmpty() {
            return this.size() === 0;
        }
        size() {
            return length.get(this);
        }
        getHead() {
            return head.get(this);
        }
        toString() {
            let current = this.getHead(),
                string = '';
            while (current) {
                string += current.element + (current.next ? ', ' : '');
                current = current.next;
            }
            return string;

        }
        print() {
            console.log(this.toString());
        }
    }
    return LinkedList2;
})();
```

代码比较长，但实现链表的核心就是利用了对象属性来链接链表的每一个节点。

### 双向链表

```javascript
function DoublyLinkedList() {
    let Node = function(element){
        this.element = element;
        this.next = null;
        this.prev = null; //NEW
    };
    let length = 0;
    let head = null;
    let tail = null; //NEW
    this.append = function(element){
        let node = new Node(element),
            current;
        if (head === null){
            head = node;
            tail = node; //NEW
        } else {
            //NEW
            tail.next = node;
            node.prev = tail;
            tail = node;
        }
        length++;
    };
    this.insert = function(position, element){
        if (position >= 0 && position <= length){
            let node = new Node(element),
                current = head,
                previous,
                index = 0;
            if (position === 0){
                if (!head){       //NEW
                    head = node;
                    tail = node;
                } else {
                    node.next = current;
                    current.prev = node; //NEW
                    head = node;
                }
            } else  if (position === length) { ////NEW
                current = tail;   
                current.next = node;
                node.prev = current;
                tail = node;
            } else {
                while (index++ < position){
                    previous = current;
                    current = current.next;
                }
                node.next = current;
                previous.next = node;
                current.prev = node; //NEW
                node.prev = previous; //NEW
            }
            length++;
            return true;
        } else {
            return false;
        }
    };
    this.removeAt = function(position){
        if (position > -1 && position < length){
            let current = head,
                previous,
                index = 0;
            if (position === 0){ //NEW
                if (length === 1){ //
                    tail = null;
                } else {
                    head.prev = null;
                }
            } else if (position === length-1){  //NEW
                current = tail;
                tail = current.prev;
                tail.next = null;
            } else {
                while (index++ < position){
                    previous = current;
                    current = current.next;
                }
                previous.next = current.next;
                current.next.prev = previous; //NEW
            }
            length--;
            return current.element;
        } else {
            return null;
        }
    };
    this.remove = function(element){
        let index = this.indexOf(element);
        return this.removeAt(index);
    };
    this.indexOf = function(element){
        let current = head,
            index = -1;
        if (element == current.element){
            return 0;
        }
        index++;
        while(current.next){
            if (element == current.element){
                return index;
            }
            current = current.next;
            index++;
        }
        //check last item
        if (element == current.element){
            return index;
        }
        return -1;
    };
    this.isEmpty = function() {
        return length === 0;
    };
    this. size = function() {
        return length;
    };
    this.toString = function(){
        let current = head,
            s = current ? current.element : '';
        while(current && current.next){
            current = current.next;
            s += ', ' + current.element;
        }
        return s;
    };
    this.inverseToString = function() {
        let current = tail,
            s = current ? current.element : '';
        while(current && current.prev){
            current = current.prev;
            s += ', ' + current.element;
        }
        return s;
    };
    this.print = function(){
        console.log(this.toString());
    };
    this.printInverse = function(){
        console.log(this.inverseToString());
    };
    this.getHead = function(){
        return head;
    };
    this.getTail = function(){
        return tail;
    }
}
```

&nbsp;&nbsp;&nbsp;&nbsp;双向链表和单项比起来就是Node类多了一个prev属性，也就是每一个node不仅仅有一个指向它后面元素的指针也有一个指向它前面的指针。

### 循环链表

&nbsp;&nbsp;&nbsp;&nbsp;明白了前面的基础链表和双向链表之后这个肯定不在话下了，循环，其实就是整个链表实例变成了一个圈，在单项链表中最后一个元素的next属性为null,现在让它指向第一个元素也就是head，那么他就成了单向循环链表。在双向链表中最后一个元素的next属性为null,现在让它指向第一个元素也就是head，那么他就成了双向循环链表。就那么回事...

## 后记

说到现在一直都是线性表，就是顺序数据结构，他们都是有顺序的，数据都是一条绳子上的蚂蚱。那么，如果数据是没有顺序的呢？那又该使用哪种数据结构呢？这个放到[学习javascript数据结构(三)——集合]中学习。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)