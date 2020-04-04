---
title: Javascript闭包总结
categories:
    - 原创
tags:
    - JavaScript
toc: true
comments: true
---

## 前言

**总括：** 本文总结了闭包的概念，并搜集了一些经典闭包的案例习题供给各位学习参考。

- 原文博客地址：[Javascript闭包小结](http://blog.damonare.cn/2016/12/23/Javascript%E9%97%AD%E5%8C%85%E6%B5%85%E8%B0%88/)

- 知乎专栏&amp;&amp;简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&amp;&amp;[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**良辰难再，人生中大好时刻，不要再去旧梦重圆。**

<!-- more -->

## 正文

其实关于闭包各个论坛社区里都有很多的文章来讲它，毕竟闭包是JavaScript中一个特色，也正因为这个雨中不同的特色也让闭包理解起来有一些吃力。笔者在这里不仅仅是想介绍闭包，也向列举一些笔者所见过的一些闭包，如果有读者还有一些比较经典的闭包例子，希望可以在评论区里留一下，谢谢。


**说了半天，究竟什么是闭包呢？**

 - 闭包就是函数的局部变量集合，只是这些局部变量在函数返回后会继续存在。

 - 闭包就是就是函数的“堆栈”在函数返回后并不释放，我们也可以理解为这些函数堆栈并不在栈上分配而是在堆上分配。

 - 当在一个函数内定义另外一个函数就会产生闭包。

**为了便于理解，我们可以简单的将闭包理解为：**

 - 闭包：是指有权访问另外一个函数作用域中的变量的函数。


### JavaScript中的作用域

&nbsp;&nbsp;**JavaScript中是没有块级作用域的。不过关于块级作用域我们在这里不做深入探究，笔者在[JavaScript的作用域和块级作用域概念理解](https://segmentfault.com/a/1190000004092842)中有对块级作用域较为详细的解释，不懂的读者可以去看看。**

> 变量的作用域无非就是两种：全局变量和局部变量。

> Javascript语言的特殊之处，就在于函数内部可以直接读取全局变量。



```javascript
var n=999;
function f1(){
    alert(n);
}
f1(); // 999
```

**如上函数，f1可调用全局变量n**

另一方面，在函数外部自然无法读取函数内的局部变量。

```javascript
function f1(){
    var n=999;
}
alert(n); // error
```

**这里有一个地方需要注意，函数内部声明变量的时候，一定要使用var命令。如果不用的话，你实际上声明了一个全局变量。**

```javascript
function f1(){
    n=999;
}
f1();
alert(n); // 999
```

###闭包

**1.理解闭包**

**我们已经理解了什么是作用域，什么是块级作用域，那又该如何去访问函数内部的变量呢？**

出于种种原因，我们有时候需要得到函数内的局部变量。但是，前面已经说过了，正常情况下，这是办不到的，只有通过变通方法才能实现。

```javascript
function f1(){
var n=999;
function f2(){
    alert(n);
}
    return f2;
}
var result=f1();
result();// 弹出999
```

**上面函数中的f2函数就是闭包，就是通过建立函数来访问函数内部的局部变量。**

**2.闭包的用途**

&nbsp;&nbsp;闭包可以用在许多地方。它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。

```javascript
function f1(){
    var n=999;
    nAdd=function(){n+=1}
    function f2(){
    alert(n);
    }
    return f2;
}
var result=f1();
result(); // 999
nAdd();
result(); // 1000
```

**在这段代码中，result实际上就是闭包f2函数。它一共运行了两次，第一次的值是999，第二次的值是1000。这证明了，函数f1中的局部变量n一直保存在内存中，并没有在f1调用后被自动清除。**

**为什么会这样呢？原因就在于f1是f2的父函数，而f2被赋给了一个全局变量，这导致f2始终在内存中，而f2的存在依赖于f1，因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。**

**这段代码中另一个值得注意的地方，就是"nAdd=function(){n+=1}"这一行，首先在nAdd前面没有使用var关键字，因此nAdd是一个全局变量，而不是局部变量。其次，nAdd的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以nAdd相当于是一个setter，可以在函数外部对函数内部的局部变量进行操作。**


**3.闭包的注意点**

> 1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。



> 2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。



**4.经典闭包小案例**

**如果你能理解下面全部的案例，那你的闭包就算是真正掌握了。**


```javascript
var name = "The Window";
var object = {
name : "My Object",
getNameFunc : function(){
    return function(){
        return this.name;
        };
    }
};　　alert(object.getNameFunc()());//The Window
```

```javascript
var name = "The Window";
var object = {
name : "My Object",
getNameFunc : function(){
    var that = this;
    return function(){
        return that.name;
        };
    }
  };
  alert(object.getNameFunc()());//My Object
```

```javascript
function fun(n,o) {
  console.log(o)
  return {
    fun:function(m){
      return fun(m,n);
    }
  };
}
var a = fun(0);  a.fun(1);  a.fun(2);  a.fun(3);//undefined,?,?,?
var b = fun(0).fun(1).fun(2).fun(3);//undefined,?,?,?
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);//undefined,?,?,?
```

​

> //问:三行a,b,c的输出分别是什么？



**这是一道非常典型的JS闭包问题。其中嵌套了三层fun函数，搞清楚每层fun的函数是那个fun函数尤为重要。**

> //答案：

//a: undefined,0,0,0

//b: undefined,0,1,2

//c: undefined,0,1,1

**都答对了么？如果都答对了恭喜你在js闭包问题当中几乎没什么可以难住你了。**

#### 参考文章

- [学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)