---
title: 聊聊clip-path
tags:
    - CSS
categories:
    - 原创
toc: true
comments: true
---

### 前言

**总括：**  图片是一个网站必不可少的元素，而呈现出绚丽多彩的图片效果在很多情况下不仅仅是设计师的工作，通过代码来修饰图片也是一个前端工程师必备的技能。因为兼容性的问题，实际项目中可能用的比较少，包括博主自己也只是用过几次剪切，很多情况下都交给设计师去做了。但作为一个hacker怎么能满足于此呢，必须深入探究！

- 原文博客地址：[聊聊clip-path](http://blog.damonare.cn/2016/09/09/%E8%81%8A%E8%81%8Aclip-path/)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生如画，岁月如歌。**

<!-- more -->

## 正文


### Flilter

filter有十种特效来处理图片，博主只放几种特效的样例给大家看一下：

**照片反色效果**：

![照片反色](http://img.blog.csdn.net/20160910104748479)

**照片褐色效果**：

![照片褐色](http://img.blog.csdn.net/20160910104803642)

**照片阴影效果**：

![阴影](http://img.blog.csdn.net/20160910104818298)

**十种特效源码:**

```css
-webkit-filter:opacity(.6);//透明度
filter:opacity(.6);
-webkit-filter:blur(10px);//照片模糊
filter:blur(10px);
-webkit-filter:invert(1);
filter:invert(1);
-webkit-filter:saturate(3);//照片饱和度
filter:saturate(3);
-webkit-filter:grayscale(1);//照片灰度
filter:grayscale(1);
-webkit-filter:sepia(1);//照片褐色
filter:sepia(1);
-webkit-filter:hue-rotate(90deg);//色相旋转
filter:hue-rotate(90deg);
-webkit-filter:brightness(.5);//亮度
filter:brightness(.5);
-webkit-filter:contrast(2);//对比度
filter:contrast(2);
-webkit-filter:drop-shadow(10px 10px 10px #ccc);//阴影
filter:drop-shadow(10px 10px 10px #ccc);
```

> 但实际上这个属性兼容性很低：
>
> ![can I use](http://img.blog.csdn.net/20160910105340845)

**截止博主发文日期，Filter的兼容性如上图，我们可以看到IE是完全不支持的，Edge也是部分支持。这可能也是Filter没法用在项目中的原因之一了。感兴趣的读者可以Copy博主代码本地测试一下，或是参照[MDN|Filter](https://developer.mozilla.org/en-US/docs/Web/CSS/filter#blur%28%29_2)了解。博主不在这里做过多的说明了。**

###  clip&clip-path

这两个属性正是今天的重头戏，博主曾在[从隐藏元素谈起](http://damonare.github.io/2016/09/05/%E4%BB%8E%E9%9A%90%E8%97%8F%E5%85%83%E7%B4%A0%E8%B0%88%E8%B5%B7/#more)提起过，但并没做深入解释。是的，它可以用来隐藏元素，当然也就能处理图片了。

- clip

clip这个属性我相信会有很大一部分人不知道，因为这个属性使用率非常的低，因为很多情况下我们会直接重新切一张新图出来代替而不会去剪裁已有的图片，但实际上这个属性用在CSS sprite简直就如同神器一般，因为在很多情况下background-position并不符合我们的需求，比如,有时我们希望Sprite图片可以延迟滚动加载，或者是可以很轻松的右键图片另存为...或是其它background-position没法满足的情景。
废话不多说，看样例：
![这里写图片描述](http://img.blog.csdn.net/20160910144129604)

```css
position:absolute;
clip:rect(50px 250px 250px 50px); /* IE6, IE7 */
clip:rect(50px,250px,250px,50px);
```

![这里写图片描述](http://img.blog.csdn.net/20160910182933393)

**注意，元素定位position必须是absolute或是fixed的，兼容IE6，IE7需要将值之间的逗号去掉。另外，react（top,right,bottom,left）；四个值分别是相对于图片左上角为原点的坐标值。Clip基本所有的浏览器都支持，可以放心使用。**

让人放弃它的原因无外乎：
> - clip 只对绝对定位的元素有效对于position:relative和position:static无效
> - clip 只能用于矩形，即rect()函数

- clip-path

其实clip在HTML5中已经被废弃了(依然可用)，取而代之的是clip-path。本来clip还有一个circle(圆)，但基本没有浏览器实现这个属性值，只有rect()可是使用，可能W3C也是等不下去了吧，直接推出了一个更牛逼的属性——clip-path,这个属性起初是SVG里面的然后被挪用到了CSS里面。关于SVG博主有时间会再另外介绍，这里按下不表。效果图：
![这里写图片描述](http://img.blog.csdn.net/20160910165910381)
![这里写图片描述](http://img.blog.csdn.net/20160910165924100)

**读者可以在[这里自行体验](http://bennettfeely.com/clippy/)**

兼容：现在为止IE 和 Edge 不支持这个属性，Firefox 仅部分支持 clip-path ，
Chrome、Safari 和 Opera 需要使用 -webkit- 前缀支持此属性。
![这里写图片描述](http://img.blog.csdn.net/20160910172318567)
**clip-path兼容性甚至比前面说到的filter还差，所以很难真正使用起来。更多使用效果[戳这里](http://codepen.io/wenbin5243/pen/iheHF)和[这里](http://species-in-pieces.com/#)**

**说一下它的四个属性值：**

- clip-source: 可以是内、外部的SVG的clipPath元素的URL引用;


- basic-shape: 使用一些基本的形状函数创建的一个形状。主要包括circle()、ellipse()、inset()和polygon()。

- geometry-box: 是可选参数。此参数和basic-shape函数一起使用时，可以为basic-shape的裁剪工作提供参考盒子。如果geometry-box由自身指定，那么它会使用指定盒子形状作为裁剪的路径，包括任何(由border-radius提供的)的角的形状。

**开始使用clip-path**

在开始使用clip-path绘制图形，或者说裁剪图形之前，有两点需要大家注意：

- 使用clip-path要从同一个方向绘制，如果顺时针绘制就一律顺时针，逆时针就一律逆时针，因为polygon是一个连续线段，若线段彼此有交集，裁剪区域就会有相减的情况发生，当然如果你特意需要这样的效果除外。

- 如果绘制时采用比例的方式绘制，长宽就必须要先行设定，不然有可能绘制出来的长宽和我们想像的就会有差距，使用像素绘制就不会有这样的现象。

我们就拿上面途中的六边形作为polygon()函数示例：

```CSS
-webkit-clip-path: polygon(0% 50%, 25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%);
clip-path: polygon(0% 50%, 25% 0%, 75% 0%, 100% 50%, 75% 100%, 25% 100%);
```

**效果图:**

![效果图](http://img.blog.csdn.net/20160910174638529)

**讲解：**

![这里写图片描述](http://img.blog.csdn.net/20160910174656822)

每个点的第一个坐标值决定了它在 x 轴上的位置，第二个坐标值指定了它在 y 轴的位置，所有点是顺时针绘制的。其实一个 polygon（）就能满足所有的形状需要了，有自定义的API用更加方便不是么。

**注意：inset()这个真的坑，按说同样裁剪成方形应该是和clip的rect一样用法，可不一样！**

```css
//Clip的rect
position:absolute;
clip: rect(50px 250px 250px 50px);
//clip-path
clip-path: inset(50px 50px 50px 50px);
-webkit-clip-path: inset(50px 50px 50px 50px);
```
本文使用图片是300*300的。

很明显：

 ```css
clip: rect(50px 250px 250px 50px);
=clip-path: inset(50px 50px 50px 50px);
 ```
## 结束语

相信随着时代发展，clip-path会慢慢被浏览器厂商接受的。
本文有任何错误，欢迎评论留言。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)