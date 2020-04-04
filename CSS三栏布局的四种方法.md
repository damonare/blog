---
title: CSS三栏布局的四种方法
categories:
    - 原创
tags:
    - CSS
toc: true
comments: true
---

## 前言

**总括：** 不管是三栏布局还是两栏布局都是我们在平时项目里经常使用的，也许你不知道什么事三栏布局什么是两栏布局但实际已经在用，或许你知道三栏布局的一种或两种方法，但实际操作中也只会依赖那某一种方法，本文具体的介绍了三栏布局的四种方法，并介绍了它的使用场景。

- 原文地址：[CSS三栏布局的四种方法](http://blog.damonare.cn/2016/12/11/CSS%E4%B8%89%E6%A0%8F%E5%B8%83%E5%B1%80%E7%9A%84%E5%9B%9B%E7%A7%8D%E6%96%B9%E6%B3%95/)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)


**年华一去不复返，事业放弃再难成。**

<!-- more -->

## 正文

所谓三栏布局就是指页面分为左中右三部分然后对中间一部分做自适应的一种布局方式。

### 1.绝对定位法

HTML代码如下:

```html
<div class="left">Left</div>
<div class="main">Main</div>
<div class="right">Right</div>
```

CSS代码如下：

```css
//简单的进行CSS reset
body,html {
    height: 100%;
    padding: 0px;
    margin: 0px;
}
//左右绝对定位
.left,.right {
    position: absolute;
    top: 0px;
    background: red;
    height: 100%;
}
.left {
    left: 0;
    width: 100px;
}
.right {
    right: 0px;
    width: 200px;
}
// 中间使用margin空出左右元素所占据的空间
.main {
    margin: 0px 200px 0px 100px;
    height: 100%;
    background: blue;
}
```

该方法有个明显的缺点，就是如果中间栏含有最小宽度限制，或是含有宽度的内部元素，当浏览器宽度小到一定程度，会发生层重叠的情况。

### 2. 圣杯布局

HTML代码如下：

```html
//注意元素次序
<div class="main">Main</div>
<div class="left">Left</div>
<div class="right">Right</div>
```

CSS代码如下：

```css
//习惯性的CSS reset
body,html {
    height: 100%;
    padding: 0;
    margin: 0;
}
//父元素body空出左右栏位
body {
    padding-left: 100px;
    padding-right: 200px;
}
//左边元素更改
.left {
    background: red;
    width: 100px;
    float: left;
    margin-left: -100%;
    position: relative;
    left: -100px;
    height: 100%;
}
//中间部分
.main {
    background: blue;
    width: 100%;
    height: 100%;
    float: left;
}
//右边元素定义
.right {
    background: red;
    width: 200px;
    height: 100%;
    float: left;
    margin-left: -200px;
    position: relative;
    right: -200px;
}
```

相关解释如下：
- (1)中间部分需要根据浏览器宽度的变化而变化，所以要用100%，这里设左中右向左浮动，因为中间100%，左层和右层根本没有位置上去
- (2)把左层margin负100后，发现left上去了，因为负到出窗口没位置了，只能往上挪
- (3)按第二步这个方法，可以得出它只要挪动窗口宽度那么宽就能到最左边了，利用负边距，把左右栏定位
- (4)但由于左右栏遮挡住了中间部分，于是采用相对定位方法，各自相对于自己把自己挪出去，得到最终结果

### 3. 双飞翼布局

HTML代码如下：

```html
<div class="main">
    <div class="inner">
        Main
    </div>
</div>
<div class="left">Left</div>
<div class="right">Right</div>
```

CSS代码如下：

```css
//CSS reset
body,html {
    height: 100%;
    padding: 0;
    margin: 0;
}
body {
    /*padding-left:100px;*/
    /*padding-right:200px;*/
}
.left {
    background: red;
    width: 100px;
    float: left;
    margin-left: -100%;
    height: 100%;
    /*position: relative;*/
    /*left:-100px;*/
}
.main {
    background: blue;
    width: 100%;
    float: left;
    height: 100%;
}
.right {
    background: red;
    width: 200px;
    float: left;
    margin-left: -200px;
    height: 100%;
    /*position:relative;*/
    /*right:-200px;*/
}
//新增inner元素
.inner {
    margin-left: 100px;
    margin-right: 200px;
}
```

圣杯布局实际看起来是复杂的后期维护性也不是很高，在淘宝UED的探讨下，出来了一种新的布局方式就是双飞翼布局，代码如上。增加多一个div就可以不用相对布局了，只用到了浮动和负边距。和圣杯布局差异的地方已经被注释。

### 4. 浮动

HTML代码如下：

```html
//注意元素次序
<div class="left">Left</div>
<div class="right">Right</div>
<div class="main">Main</div>
```

CSS代码如下：

```css
//CSS reset
body,html {
    height:100%;
    padding: 0;
    margin: 0
}
//左栏左浮动
.left {
    background: red;
    width: 100px;
    float: left;
    height: 100%;
}
//中间自适应
.main {
    background: blue;
    height: 100%;
    margin:0px 200px 0px 100px;
}
//右栏右浮动
.right {
    background: red;
    width: 200px;
    float: right;
    height: 100%;
}
```

这种方式代码足够简洁与高效，也容易理解

## 后记

四种方法其实只有圣杯布局和双飞翼布局较难理解，但实际上理解了圣杯布局，双飞翼布局自然就理解了。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)