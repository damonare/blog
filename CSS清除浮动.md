---
title: CSS清除浮动
categories:
    - 原创
tags:
    - CSS
toc: true
comments: true
---

## 前言

**总括：** 在非IE浏览器（如Firefox）下，当容器的高度为auto，且容器的内容中有浮动（float为left或right）的元素，在这种情况下，容器的高度不能自动伸长以适应内容的高度，使得内容溢出到容器外面而影响（甚至破坏）布局的现象。这个现象叫浮动溢出，为了防止这个现象的出现而进行的CSS处理，就叫CSS清除浮动。

- 原文地址：[CSS清除浮动](http://blog.damonare.cn/2016/11/26/CSS%E6%B8%85%E9%99%A4%E6%B5%AE%E5%8A%A8/)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生用物，各有天限；夏涝太多，必有秋旱。**

<!-- more -->

## 正文

首先看下，浮动导致的问题，引用一个W3C的例子，下面代码中的news容器没有包围浮动的元素，导致news容器表现形式是空白的，这种现象就是子元素浮动导致的。在很多情况下我们需要去清除浮动来让元素`img`和`p`撑满`div`。浮动产生的根本原因在于元素脱离了文档流，由于设置了`float`为`none`以外的属性，元素`img`和`p`形成了BFC（格式化上下文），说白了就是它自己变成了一个完整的容器，不再受外部父元素的控制了。总结起来，清除浮动的方法一共有两类：

- 触发父元素的BFC(格式化上下文)；
- 使用`clear: both`

**CSS:**

```css
.news {
  background-color: gray;
  border: solid 1px black;
}
.news img {
  float: left;
}
.news p {
  float: right;
}
```

**HTML:** 

```html
<div class="news">
  <img src="http://damonare.cn/logo.png" />
  <p>这是一段文案</p>
</div>
```



![浮动示例](http://images.cnitblog.com/blog/349636/201310/23224343-9668661a8f63445699e0a8c24a64662b.jpg);



### 1. 使用带clear属性的空元素

在浮动元素后使用一个空元素如

```html
<div class="clear"></div>
```

并在CSS中赋予

```css
.clear{
  clear:both;
}
```

属性即可清理浮动。亦可使用

```html
<br class="clear" />或<hr class="clear" />
```

来进行清理。

**CSS:**

```css
.news {
  background-color: gray;
  border: solid 1px black;
}
.news img {
  float: left;
}
.news p {
  float: right;
}
.clear {
  clear: both;
}
```

**HTML:**

```html
<div class="news">
  <img src="http://damonare.cn/logo.png" />
  <p>这是一段文案</p>
  <div class="clear"></div>
</div>
```

优点：简单，代码少，浏览器兼容性好。
缺点：需要添加大量无语义的html元素，代码不够优雅，后期不容易维护。

### 2. 使用CSS的:after伪元素

结合`:after`伪元素（注意这不是伪类，而是伪元素，代表一个元素之后最近的元素）和 IEhack ，可以完美兼容当前主流的各大浏览器，这里的 IEhack 指的是触发 hasLayout。
给浮动元素的容器添加一个clearfix的class，然后给这个class添加一个`:after`伪元素实现元素末尾添加一个看不见的块元素（Block element）清理浮动。

**CSS:**

```css
.news {
  background-color: gray;
  border: solid 1px black;
}
.news img {
  float: left;
}
.news p {
  float: right;
}
.clearfix:after{
  content: "020";
  display: block;
  height: 0;
  clear: both;
  visibility: hidden;  
}
.clearfix {
  /* 触发 hasLayout */
  zoom: 1;
}
```

**HTML:**

```html
<div class="news clearfix">
  <img src="news-pic.jpg" />
  <p>some text</p>
</div>
```

通过CSS伪元素在容器的内部元素最后添加了一个看不见的空格"020"或点"."，并且赋予clear属性来清除浮动。需要注意的是为了IE6和IE7浏览器，要给clearfix这个class添加一条zoom:1;触发haslayout。

### 3. 使用overflow

给浮动元素的容器添加

```css
overflow:hidden;
```

或

```css
overflow:auto;
```

可以清除浮动，另外在 IE6 中还需要触发 hasLayout ，例如为父元素设置容器宽高或设置 zoom:1。在添加overflow属性后，浮动元素又回到了容器层，把容器高度撑起，达到了清理浮动的效果。

**CSS:**

```css
.news {
  background-color: gray;
  border: solid 1px black;
  overflow: hidden;
  *zoom: 1;
}
.news img {
  float: left;
}
.news p {
  float: right;
}
```

**HTML:**

```html	
<div class="news">
  <img src="http://damonare.cn/logo.png" />
  <p>这是一段文案</p>
</div>
```

### 4. 使用float

给浮动元素的容器也添加上浮动属性即可清除内部浮动，但是这样会使其整体浮动，影响布局，也可以酌情使用；

**CSS:**

```css
.news {
  background-color: gray;
  border: solid 1px black;
  float: left;
}
.news img {
  float: left;
}
.news p {
  float: right;
}
```

**HTML:**

```html
<div class="news">
  <img src="http://damonare.cn/logo.png" />
  <p>这是一段文案</p>
</div>
```

### 5. 使用position

使用position也可以清除浮动，需要使用绝对定位，在某些场景下也可以使用。

**CSS:**

```css
.news {
  background-color: gray;
  border: solid 1px black;
  position: absolute; /** 或fixed**/
}
.news img {
  float: left;
}
.news p {
  float: right;
}
```

**HTML:**

```html	
<div class="news">
  <img src="http://damonare.cn/logo.png" />
  <p>这是一段文案</p>
</div>
```

### 6. 使用display

使用display，将其设置为inline-block也可以清除浮动，该方法是比较通用，也比较简单的一种：

**CSS:**

```css
.news {
  background-color: gray;
  border: solid 1px black;
  display: inline-block;
}
.news img {
  float: left;
}
.news p {
  float: right;
}
```

**HTML:**

```html	
<div class="news">
  <img src="http://damonare.cn/logo.png" />
  <p>这是一段文案</p>
</div>
```

## 后记

通过上面的例子，我们不难发现清除浮动的方法可以分成两类：

- 方法1,2是利用 clear 属性，包括在浮动元素末尾添加一个带有 clear: both 属性的空 div 来闭合元素，其实利用 :after 伪元素的方法也是在元素末尾添加一个内容为一个点并带有 clear: both 属性的元素实现的；

- 方法3,4,5,6是触发浮动元素父元素的 BFC (Block Formatting Contexts, 块级格式化上下文)，使到该父元素可以包含浮动元素。这种方式其实还有很多，只要是能触发父元素的BFC就可以清除浮动。

最后建议使用相对完美的:after伪元素方法清理浮动，文档结构更加清晰。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)