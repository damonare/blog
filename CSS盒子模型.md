---
title: 从CSS盒子模型说起
categories:
    - 原创
tags:
    - CSS
    - HTML
toc: true
comments: true
---

## 前言

**总括：** 对于盒子模型，BFC，IFC和外边距合并等概念和问题的总结

- 原文地址：[从CSS盒子模型说起](http://blog.damonare.cn/2017/07/08/CSS%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B/#more)

- 知乎专栏：[前端进击者](https://zhuanlan.zhihu.com/damonare)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)


**为学之道，莫先于穷理；穷理之要，必先于读书。**

<!-- more -->

## 正文


​	CSS盒子模型想来都不陌生，但还是想先介绍一下，以保证文章的完整性。😳

### 盒子模型

​	CSS盒子模型：

![盒子模型](https://www.w3.org/TR/CSS2/images/boxdim.png)

在一个文档中，每一个元素都被抽象成一个盒子，每一个盒子又包括四部分(从内到外):内容(content)，内填充(padding)，边框(border)，外边距(margin)。见上图，这是从二维的角度分析，来张三维立体图：😜

![网上找的图片](https://cdn.damonare.cn/2081525038-56ea01986c0f9_articlex%20%281%29.jpeg)

此图很形象的解释了CSS盒子的构成：

1. content box：立体盒子的核心
2. padding box：内边距区域padding area 延伸到包围padding的边框。如果内容区域content area设置了背景、颜色或者图片，这些样式将会延伸到padding上(当然我们可以通过background-clip设置作用区域)
3. border box：由border和4条border edge组成。若border宽度设置为0，则border edge与padding edage重叠；
4. margin box：由margin和4条margin edge组成。若margin宽度设置为0，则margin edge与border edage重叠。

😨看起来很复杂的样子...

拿PS图层的概念更好理解这块，最上面的就是content box往下一次是padding box,border box,margin box。

那么盒子模型一般分为两种：

#### IE盒子模型

所谓IE盒子模型，就是之前IE浏览器实现的一种怪异的盒子模型，怎么怪异呢？当我们这样设置的时候:

```css
div {
  width: 100px;
  height: 100px;
}
```

理论上我们想要设置的就是content box的宽高嘛，但是IE在解析的时候会按照这个规则解析：

> width = content-width + padding-width + border-width
> height = content-height + padding-height + border-height

这就导致了这种尴尬的境地:[下面无内容的话直接戳这里](https://jsfiddle.net/Damonare/f7vh400t/)😓

<script async src="https://jsfiddle.net/Damonare/f7vh400t/embed/html,css,result//"></script>
#### 标准盒子模型

标准就比较符合常人的思维了，设置的width,height就是content的width和height

规则就是:

> width = content-width
>
> height = content-height 

实例如下：[无内容戳这](https://jsfiddle.net/Damonare/a6oogyyz)😓

<script async src="https://jsfiddle.net/Damonare/a6oogyyz/embed/html,css,result/"></script>
可能秉着宽大为怀的准则，CSS3加了个box-sizing属性，变相承认了这两种盒子都对(好吧，可能一个人有一个人的看法吧)，不过box-sizing默认属性就是content-box，即标准盒子模式，IE盒子模型呢，是属性border-box。刚刚查MDN发现还有一个属性padding-box(width=content-width+padding-width)，不过并没有浏览器实现它(真可怜)，并无卵用🤣

### 行内元素的思考

刚刚说的是以块级元素为例说的，那么行内元素呢？好吧，其实你知道，行内盒是没法设置width和height的，那么之前我就有了这样的思维定势：行内盒没有padding，margin，然后发现，哦！行内盒是有padding-left,padding-right,margin-left,margin-right的！WOC!，然后又发现，行内盒是实际上身怀八甲...😷

[以下无内容戳这里](https://jsfiddle.net/Damonare/826gyrrh)

<script async src="https://jsfiddle.net/Damonare/826gyrrh/embed/html,css,result/"></script>
行内盒子的高由font-size决定的；
行内盒子的宽等于其子行级盒子的外宽度(margin+border+padding+content width)之和。

是有padding-top和padding-bottom,margin-left,margin-bottom的但并不占据空间…这就符合盒子模型了嘛，既都是盒子，自然应该是一样的。行内盒的margin-top, margin-bottom不占空间，由此联想到了另一个问题——😑

### 外边距合并

所谓外边距合并呢，就是margin合并嘛，看下MDN的定义：

>  块的顶部外边距和底部外边距有时被组合(折叠)为单个外边距，其大小是组合到其中的最大外边距，这种行为称为**外边距合并**。

😱注意只是上下，没有说左右。而且是针对块级元素说的。

外边距合并有这几种情况：

#### 相邻兄弟元素

**HTML：**

```html
<div class="up">我在上面</div>
<div class="down">我在下面</div>
```

**CSS：**

```css
.up {
  width: 100px;
  height: 100px;
  border: 1px solid blue;
  margin: 100px;
}
.down {
  width: 100px;
  height: 100px;
  border: 1px solid red;
  margin: 100px;
}
```

我们感性上觉得上下两个元素应该是相差200px距离，然而并不是。

#### 父子元素

如果块级父元素中，不存在上边框、上内补、inline content、清除浮动这四条属性（对于上边框和上内补，也可以说，当上边距及上内补宽度为0时），那么这个块级元素和其第一个子元素的上边距就可以说”挨到了一起“。此时这个块级父元素和其第一个子元素就会发生 上外边距合并 现象，换句话说，此时这个父元素对外展现出来的外边距将直接变成这个父元素和其第一个子元素的margin-top的较大者。😊

**HTML:** 

```html
<div class="parent">
  <div class="child">我是儿子</div>
</div>
```

**CSS:**

```css
.parent {
  width: 100px;
  height: 200px;
  background: red;
  margin-left: 100px;
}
.child {
  width: 50px;
  height: 50px;
  margin-top: 100px;
  border: 1px solid blue;
}
```

上面代码感性上可能会觉得，父元素没有上边距，然而并不是。

MDN给了三种情况，但第三种空块元素，我觉得可以包含在这两种之内，就没举🌰

那么这种外边距合并的情况咋解决呢？看下一个概念...

### BFC

😯定义：

> 一个**块格式化上下文（block formatting context）** 是Web页面的可视化CSS渲染的一部分。它是块盒子的布局发生，浮动互相交互的区域。

那么触发BFC的情况有哪些呢？

看MDN：

😱一个**块格式化上下文**由以下之一创建：

- 根元素或其它包含它的元素
- 浮动 (元素的[`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float) 不是 `none`)
- 绝对定位的元素 (元素具有 [`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position) 为 `absolute` 或 `fixed`)
- 内联块 inline-blocks (元素具有 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)`: inline-block`)
- 表格单元格 (元素具有 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)`: table-cell，HTML表格单元格默认属性`)
- 表格标题 (元素具有 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)`: table-caption`, HTML表格标题默认属性)
- 块元素具有[`overflow`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow) ，且值不是 `visible`
- [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)`: flow-root`

>  **注意，根元素就创建了一个BFC**

那么BFC又有一下特性：

1. 内部块级盒子垂直方向排列
2. 盒子垂直距离由margin决定，同一个BFC的盒子外边距会合并
3. BFC就是一个隔离的容器，内部子元素不会影响到外部元素
4. 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。
5. BFC的区域不会与float box叠加。

好，上面外边距合并的两种情况，利用BFC如何解决呢？[下面没内容的话请戳这里](https://jsfiddle.net/Damonare/ejntapom/)😓

<script async src="https://jsfiddle.net/Damonare/ejntapom/embed/html,css,result/"></script>
关于第四五条特性，请看上面的示例。

**BFC用途：**

1. 清除浮动;
2. 解决外边距合并;
3. 布局;

#### 块级盒子的概念

关于这块有好多个概念...首先是块级元素和块级盒子：**每个块级元素至少生成一个块级盒**，称为主要块级盒。一些元素，比如li，生成额外的盒来放置项目符号，不过多数元素只生成一个主要块级盒。 

主要块级盒将包含后代元素生成的盒以及生成的内容。它也是可以使用([定位方案 positioning scheme](https://developer.mozilla.org/en-US/docs/CSS/Positioning_scheme))的盒。

块容器盒(*block container box)* 只包含其它块级盒，或生成一个行内格式化上下文(inline formatting context)

>  **注意块级盒与块容器盒概念不同。 前者描述元素跟它的父元素与兄弟元素之间的表现，后者描述元素跟它的后代之间的影响。**

**同时是块容器盒的块级盒称为块盒(block boxes)。**（注意块盒和块级盒并不是全等）

还有一个特殊的块盒——匿名块盒

```html
<div>
  Some inline text 
  <p>followed by a paragraph</p>
  followed by more inline text.
</div>
//将创建两个匿名块盒，一个包含 <p> 前面的文本 (Some inline text)， 一个包含 <p> 后面的文本(followed by more inline text), 
```

块级元素触发BFC，行内元素会触发啥么❓

### IFC

IFC 只有在一个块级元素中**仅包含**内联级别元素时才会生成。

#### 行内盒子的概念

当元素的 CSS 属性 display的计算值为 `inline`, `inline-block` 或 `inline-table `时，称它为行内级元素。视觉上它将内容与其它行内级元素排列为多行。典型的如段落内容，有文本(可以有多种格式譬如着重)，或图片，都是行内级元素。

行内级元素生成行内级盒(*inline-level boxes)*，参与行内格式化上下文(inline formatting context)。同时参与生成行内格式化上下文的行内级盒称为行内盒(*Inline boxes)。*所有display:inline 的非替换元素生成的盒是行内盒。而不参与生成行内格式化上下文的行内级盒称为原子行内级盒(*atomic inline-level boxes)。这些盒由可替换行内元素，或 display 值为 `inline-block` 或 `inline-table `的元素生成，不能拆分成多个盒。

另外CSS3还新增了两种格式上下文：GFC（Grid Formatting Contexts）栅格格式化上下文和FFC（Flex Formatting Contexts）Flex格式化上下文，即分别在元素display为grid和flex、 inline-flex 时触发

### 定位

#### 常规流

分清了这些盒子的概念，具体怎么排列呢？以下来自MDN：

> 在常规流中，盒一个接着一个排列。在块级格式化上下文里面， 它们竖着排列；在行内格式化上下文里面， 它们横着排列。 当 position为 `static` 或 `relative`，并且 float 为 `none` 时会触发常规流。

#### 浮动(Floats)

对于浮动定位方案(*float positioning scheme)*, 盒称为浮动盒(*floating boxes)*。它位于当前行的开头或末尾。这导致常规流环绕在它的周边，除非设置 clear 属性。

要使用浮动定位方案，元素 CSS 属性position 为 `static` 或 `relative`，然后`  float`不为` none` 。如果 `float`设为 `left`, 浮动由行盒的开头开始定位。如果设为 `right`, 浮动定位在行盒的末尾。

#### 绝对定位(Absolute positioning)

对于绝对定位方案， 盒从常规流中被移除，不影响常规流的布局。 它的定位相对于它的包含块，相关CSS属性：top, bottom, left及right 。

如果元素的属性position为 `absolute` 或 `fixed`， 它是绝对定位元素。

固定定位元素(*fixed positioned element)*也是绝对定位元素，它的包含块是视口。当页面滚动时它固定在屏幕上，因为视口没有移动。

以上。

## 后记

### 参考文章

[外边距塌陷](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

[Box model](https://www.w3.org/TR/CSS21/box.html#box-dimensions)

[Block formatting context](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Block_formatting_context)

[视觉格式化模型](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Visual_formatting_model)

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)