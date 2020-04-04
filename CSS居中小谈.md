---
title: CSS居中小谈
categories:
  - 原创
tags:
  - CSS
toc: true
comments: true
---

## 前言

**总括：**  CSS居中一直是一个比较敏感的话题，为了以后开发的方便，楼主觉得确实需要总结一下了，总的来说，居中问题分为垂直居中和水平居中，实际上水平居中是很简单的，但垂直居中的方式和方法就千奇百怪了。

- 原文地址：[CSS居中小谈](http://blog.damonare.cn/2016/11/30/CSS%E5%B1%85%E4%B8%AD%E5%B0%8F%E8%B0%88/)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生用物，各有天限；夏涝太多，必有秋旱。**

<!-- more -->

## 正文

### 内联元素居中方案

**水平居中设置：**

1. 行内元素 设置 text-align:center；

2. Flex布局 设置父元素display:flex;justify-content:center;(灵活运用)

**垂直居中设置：**

1. 父元素高度确定的单行文本（内联元素） 设置 height = line-height；
2. 父元素高度确定的多行文本（内联元素）
  a:插入 table （插入方法和水平居中一样），然后设置vertical-align:middle；
  b:先设置 display:table-cell 再设置 vertical-align:middle；

### 块级元素居中方案

 **水平居中设置：**

1. 定宽块状元素 设置 左右 margin 值为 auto；

2. 不定宽块状元素
  a:在元素外加入 table 标签（完整的，包括 table、tbody、tr、td），该元素写在 td 内，然后设置 margin 的值为 auto；
  b:给该元素设置 display:inine 方法；
  c:父元素设置 position:relative 和 left:50%，子元素设置 position:relative 和 left:50%；

**垂直居中设置：**

垂直居中是最复杂的，下面详细看下块级元素垂直居中的几种方法：

1. **元素定高**：使用position:absolute（fixed）,设置left、top、margin-left、margin-top的属性;

 ```css
.box {
    width: 200px;
    height: 200px;
    background: red;
    position: absolute;/*或fixed*/
    top: 50%;
    left: 50%;
    margin-top: -100px;
    margin-left: -100px;
}
 ```

2. **不定高不定宽：**利用position:fixed（absolute）属性，margin:auto这个必须不要忘记了(不定高不定宽);

```css
.box{
    width: 100px;
    height: 100px;
    background: red;
    position: absolute;/*或fixed*/
    top:0;
    right:0;
    bottom:0;
    left:0;
    margin: auto;
}
```

3. 利用display:table-cell属性使内容垂直居中,这个方法在多行文字居中的时候用的比较多;

**HTML代码：**

```html
<div class="box">
    <span>多行文字，此处居中设置</span>
</div>
```

**CSS代码：**

```css
.box{
    display: table-cell;
    vertical-align: middle;
    text-align: center;
    width: 100px;
    height: 120px;
    background: purple;
}
.box span{
    display: inline-block;
    vertical-align: middle;
}
```

4. 使用css3的新属性transform:translate(x,y)属性(不定高，不定宽);

**HTML代码：**

```html
<div class='box'>
    垂直居中
</div>
```
**CSS代码:**

```css
.box{
    position: absolute;
    top:50%;
    left:50%;
    transform: translate(-50%,-50%);
}
```

5. 使用before，after伪元素(定高不定宽);

**HTML代码：**

```html
<div class='box'>
    <div class='content'>
        垂直居中
    </div>
</div>
```

**CSS代码：**

```css
.box{
    display: block;
    background: rgba(0,0,0,.5);
    height: 100px;
}
.content::before{
    content: '';
    display: block;
    vertical-align: middle;
    height: 100%;
}
.content::after{
    content: '';
    display: block;
    vertical-align: middle;
    height: 100%;
}
.box .content{
    height: 33px;
    line-height: 33px;
    text-align: center;
}
```

6. Flex布局(不定高，不定宽);

**HTML代码：**

```html
<div class='box'>
    <div class='content'>
        垂直居中
    </div>
</div>
```

**CSS代码:**

```css
.box{
    background-color: red;
    height: 200px;
    display: flex;
    /*水平居中*/
    justify-content: center;
    /*垂直居中*/
    align-items: center;
}
```

> 博主暂时掌握了这些居中方法，读者如果还有好方法或是觉得那个地方不对，欢迎评论，不吝感谢。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)