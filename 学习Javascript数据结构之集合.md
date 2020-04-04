---
title: 学习Javascript数据结构之集合
categories:
    - 原创
tags:
    - Javascript
    - 数据结构
    - 算法
comments: true
---

## 前言

**总括：** 本文讲解了数据结构中的[集合]概念，并使用Javascript实现了集合。

- 原文博客地址：[学习javascript数据结构(三)——集合](http://blog.damonare.cn/2017/01/16/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%B8%89%EF%BC%89%E2%80%94%E2%80%94%E9%9B%86%E5%90%88/)
- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)
- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生多风雨，何处无险阻。**


<!-- more -->

## 正文

### 集合简介

在上一篇[学习javascript数据结构(二)——链表](http://damonare.github.io/2016/11/09/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%BA%8C%EF%BC%89%E2%80%94%E2%80%94%E9%93%BE%E8%A1%A8/#more)中我们说了链表这种数据结构，但归根结底，不论是栈，队列亦或是链表都是线性结构。他们都是一种很规矩的数据结构，就像幼儿园的小朋友排队乖乖的站在那不会动一样。

---

![幼儿园小朋友排队](http://img.blog.csdn.net/20161129224745616)

然而纷杂的数据并不会总是排队站在那里，幼儿园小朋友一旦下了课那可就撒欢了，乱糟糟一团。可我们的幼儿园老师却能分辨出这些小朋友，因为啥？因为每个小朋友都在一个班里，而且每一个小朋友都有自己的名字。老师自然很容易就找到小朋友了。

![幼儿园小朋友下课](http://img.blog.csdn.net/20161129224928510)

---

而本篇博文要说的集合正是一堆`乱糟糟的数据`，唯一的共同点是这些数据隶属于同一个`集合`,看下百科给出的解释：

> 由一个或多个元素所构成的叫做集合。

此处的元素就是小朋友了，他们所在的集合就是他们的班级。其实我们在高中的时候也接触过集合的概念。那时候还没有套路这个名词，单纯的岁月，那个年代对于集合是这么解释的：

> 集合是指具有某种特定性质的具体的或抽象的对象汇总成的集体，这些对象称为该集合的元素。

然后又是这么分类的：

- 空集：{}
- 有限集：{a,b,4}
- 无限集：{1,2,3,4...}

不过，数据结构中集合是没有无限集这个概念的。再然后那时候的集合还有这么几个特性：

- 确定性：给定一个集合，任给一个元素，该元素或者属于或者不属于该集合，二者必居其一，不允许有模棱两可的情况出现。
- 互异性：一个集合中，任何两个元素都认为是不相同的，即每个元素只能出现一次。有时需要对同一元素出现多次的情形进行刻画，可以使用多重集，其中的元素允许出现多次。
- 无序性：一个集合中，每个元素的地位都是相同的，元素之间是无序的。集合上可以定义序关系，定义了序关系后，元素之间就可以按照序关系排序。但就集合本身的特性而言，元素之间没有必然的序。

想当年哥还是个数学学霸，如今却沦落为了一个码农......真是让人唏嘘啊。咳咳！接着说：

集合还有这几种常见的基本操作：

- 并集
- 交集
- 差集

而且我们数据结构中的集合基本是也符合高中时候的数学中的概念的。接下来我们是用ES5来实现集合，为啥子这么说呢......因为在ES6中已经新给出了Set，Map等几个集合类，更加方便快捷的锁定键值对。

### 集合的创建

首先我们先声明一个集合类：

```javascript
function Set() {
    var items = {};
}
```

接下来，我们需要给链表声明一些方法：

- add(value): 向集合添加一个新的项；
- remove(value): 从集合移除一个值；
- has(value): 如果值在集合中，返回true,否则返回false；
- clear(): 移除集合中的所有项；
- size(): 返回集合所包含的元素数量，与数组的length属性相似；；
- values(): 返回一个集合中所有值的数组；
- union(setName): 并集，返回包含两个集合所有元素的新集合(元素不重复)；；
- intersection(setName): 交集，返回包含两个集合中共有的元素的集合；；
- difference(setName): 差集，返回包含所有存在本集合而不存在setName集合的元素的新集合；；
- subset(setName): 子集，验证setName是否是本集合的子集；；

下面是Set类的完整代码：

```javascript
function Set() {
    let items = {};
    this.add = function(value){
        if (!this.has(value)){
            items[value] = value;
            return true;
        }
        return false;
    };
    this.delete = function(value){
        if (this.has(value)){
            delete items[value];
            return true;
        }
        return false;
    };
    this.has = function(value){
        return items.hasOwnProperty(value);
        //return value in items;
    };

    this.clear = function(){
        items = {};
    };
    /**
     * Modern browsers function
     * IE9+, FF4+, Chrome5+, Opera12+, Safari5+
     * @returns {Number}
     */
    this.size = function(){
        return Object.keys(items).length;
    };

    /**
     * cross browser compatibility - legacy browsers
     * for modern browsers use size function
     * @returns {number}
     */
    this.sizeLegacy = function(){
        let count = 0;
        for(let key in items) {
            if(items.hasOwnProperty(key))
                ++count;
        }
        return count;
    };
    /**
     * Modern browsers function
     * IE9+, FF4+, Chrome5+, Opera12+, Safari5+
     * @returns {Array}
     */
    this.values = function(){
        let values = [];
        for (let i=0, keys=Object.keys(items); i<keys.length; i++) {
            values.push(items[keys[i]]);
        }
        return values;
    };
    this.valuesLegacy = function(){
        let values = [];
        for(let key in items) {
            if(items.hasOwnProperty(key)) {
                values.push(items[key]);
            }
        }
        return values;
    };
    this.getItems = function(){
      return items;
    };
    this.union = function(otherSet){
        let unionSet = new Set();
        let values = this.values();
        for (let i=0; i<values.length; i++){
            unionSet.add(values[i]);
        }
        values = otherSet.values();
        for (let i=0; i<values.length; i++){
            unionSet.add(values[i]);
        }
        return unionSet;
    };
    this.intersection = function(otherSet){
        let intersectionSet = new Set();
        let values = this.values();
        for (let i=0; i<values.length; i++){
            if (otherSet.has(values[i])){
                intersectionSet.add(values[i]);
            }
        }
        return intersectionSet;
    };
    this.difference = function(otherSet){
        let differenceSet = new Set();
        let values = this.values();
        for (let i=0; i<values.length; i++){
            if (!otherSet.has(values[i])){    
                differenceSet.add(values[i]);
            }
        }
        return differenceSet;
    };
    this.subset = function(otherSet){
        if (this.size() > otherSet.size()){
            return false;
        } else {
            let values = this.values();
            for (let i=0; i<values.length; i++){
                if (!otherSet.has(values[i])){  
                    return false;
                }
            }
            return true;
        }
    };
}
```

下面是ES6版本代码：

```javascript
let Set2 = (function () {
    const items = new WeakMap();
    class Set2 {
        constructor () {
            items.set(this, {});
        }
        add(value){
            if (!this.has(value)){
                let items_ = items.get(this);
                items_[value] = value;
                return true;
            }
            return false;
        }
        delete(value){
            if (this.has(value)){
                let items_ = items.get(this);
                delete items_[value];
                return true;
            }
            return false;
        }
        has(value){
            let items_ = items.get(this);
            return items_.hasOwnProperty(value);
        }
        clear(){
            items.set(this, {});
        }
        size(){
            let items_ = items.get(this);
            return Object.keys(items_).length;
        }
        values(){
            let values = [];
            let items_ = items.get(this);
            for (let i=0, keys=Object.keys(items_); i<keys.length; i++) {
                values.push(items_[keys[i]]);
            }
            return values;
        }
        getItems(){
            return items.get(this);
        }
        union(otherSet){
            let unionSet = new Set();
            let values = this.values();
            for (let i=0; i<values.length; i++){
                unionSet.add(values[i]);
            }
            values = otherSet.values();
            for (let i=0; i<values.length; i++){
                unionSet.add(values[i]);
            }
            return unionSet;
        }
        intersection(otherSet){
            let intersectionSet = new Set();
            let values = this.values();
            for (let i=0; i<values.length; i++){
                if (otherSet.has(values[i])){
                    intersectionSet.add(values[i]);
                }
            }
            return intersectionSet;
        }
        difference(otherSet){
            let differenceSet = new Set();
            let values = this.values();
            for (let i=0; i<values.length; i++){
                if (!otherSet.has(values[i])){
                    differenceSet.add(values[i]);
                }
            }
            return differenceSet;
        };
        subset(otherSet){
            if (this.size() > otherSet.size()){
                return false;
            } else {
                let values = this.values();
                for (let i=0; i<values.length; i++){
                    if (!otherSet.has(values[i])){
                        return false;
                    }
                }
                return true;
            }
        };
    }
    return Set2;
})();
```

## 后记

集合是一种比较常见的数据结构，在JS中我们已经有了一种类似哈希表的东西：Object(对象)。但现在我们所说的集合只是以[value,value]形式存储数据.敬请期待：[学习javascript数据结构(四)——树](https://damonare.github.io/2017/01/14/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E5%9B%9B%EF%BC%89%E2%80%94%E2%80%94%E6%A0%91/#more)

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)