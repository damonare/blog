---
title: Javascript填坑史
categories: 
  - 参考
tags:
  - JavaScript
toc: true
comments: true
---

# 前言

**总括：** 这是笔者平时积累的一些觉得比较有意思或是比较有难度的JavaScript题目理解和心得，会保持长期更新。

- 原文地址：[Javascript填坑史](http://blog.damonare.cn/2016/12/23/Javascript%E5%A1%AB%E5%9D%91%E5%8F%B2/)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生莫作妇人身，百年苦乐由他人。**

<!-- more -->

 # 正文

## 1.setTimeout和setInterval深入理解

```javascript
console.log("1");
setTimeout(function(){
    console.log("3")
},0);
console.log("2");
```

结果：控制台依次输出1,2,3;

```javascript
function fn() {
    setTimeout(function(){
        alert('can you see me?');
    },1000);
    while(true) {}
}
```

你觉得这段代码的执行结果是什么呢？答案是，alert永远不会出现。 这是为什么呢？因为，while这段代码没有执行完，所以插入在后面的代码便永远不会执行。 综上所述，其实JS终归是单线程产物。无论如何"异步"都不可能突破单线程这个障碍。所以许多的"异步调用"（包括Ajax）事实上也只是"伪异步"而已。只要理解了这么一个概念，也许理解setTimeout和setInterval也就不难了。

## 2\. 闭包初探小题

有几个小题个人觉得还是比较有意思的:

```javascript
var name = "The Window";
var object = {
    name : "My Object",
    getNameFunc : function(){
        return function(){
            return this.name;
        };
    }
};
alert(object.getNameFunc()());//The Window
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

> //问:三行a,b,c的输出分别是什么？

**这是一道非常典型的JS闭包问题。其中嵌套了三层fun函数，搞清楚每层fun的函数是那个fun函数尤为重要。**

> //答案： //a: undefined,0,0,0 //b: undefined,0,1,2 //c: undefined,0,1,1

## 3\. Array/map,Number/parseInt

```javascript
["1", "2", "3"].map(parseInt)//求输出结果
```

首先, map接受两个参数, 一个回调函数 callback, 一个回调函数的this值 其中回调函数接受三个参数 currentValue, index, arrary;而题目中, map只传入了回调函数--parseInt.其次, parseInt 只接受两个两个参数 string, radix(基数). radix的合法区间是2-36\. 0或是默认是10.所以本题即问

```javascript
parseInt('1', 0);
parseInt('2', 1);
parseInt('3', 2);
```

后两者参数不合法.所以答案是：[1, NaN, NaN]；

## 4. 0.1+0.2!=0.3和9999999999999999 == 10000000000000000;

根据语言规范，JavaScript 采用"IEEE 754 标准定义的双精度64位格式"（"double-precision 64-bit format IEEE 754 values"）表示数字。据此我们能得到一个有趣的结论，和其他编程语言（如 C 和 Java）不同，JavaScript 不区分整数值和浮点数值，所有数字在 JavaScript 中均用浮点数值表示，所以在进行数字运算的时候要特别注意。[精度丢失][3]看看下面的例子:

```javascript
0.1 + 0.2 = 0.30000000000000004
```

在具体实现时，整数值通常被视为32位整型变量，在个别实现（如某些浏览器）中也以32位整型变量的形式进行存储，直到它被用于执行某些32位整型不支持的操作，这是为了便于进行位操作。大整数精度在2的53次方以内是不会丢失的，也就是说浏览器能精确计算Math.pow(2,53)以内所有的数，小数精度，当十进制小数的二进制表示的有限数字不超过 52 位时，在 JavaScript 里也是可以精确存储的。 解决办法：Math.round( (.1+.2)*100)/100;

## 5\. [1 < 2 < 3, 3 < 2 < 1]

此题会让人误以为是2>1&&2<3,其实不是的，这个题等价于

```javascript
1<2=>true;
true<3=>1<3=>true;
3<2=>true;
false<1=>0<1=>true;
```

**答案：[true,true]** 这个题的重点是对于运算符的理解，一是javascript对于不同类型数值的比较规则，详见[js比较表][4],[javascript相等性判断][5]；二是对于[比较操作符][6]和[赋值运算符][7]的理解，即一个自左向右一个自右向左~

## 6\. 浏览器懵逼史（1）

```javascript
3.toString;
3..toString;
3...toString;
```

这个题感觉脑洞很大啊~先说答案：error,'3',error; 可如果是

```javascript
var a=3;
a.toString;
```

却又合法了答案就是'3'; 为啥呢？ 因为在JS中1.1,1.,.1都是合法数字啊！那么在解析3.toString的时候到底是这是个数字呢，还是方法调用呢？浏览器就懵逼了呗，只能抛出一个error,所以说感觉此题就是在戏耍浏览器......

## 7\. 声明提升

```javascript
var name = 'World!';
(function () {
    if (typeof name === 'undefined') {
        var name = 'Jack';
        console.log('Goodbye ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
```

答案是什么呢...笔者第一次做的时候傻傻的觉得是Hello,world...实则不然，正确答案是:Goodbye Jack; 为什么呢，声明提升...上述代码相当于下面的代码：

```javascript
var name = 'World!';
(function () {
    var name;
    if (typeof name === 'undefined') {
        name = 'Jack';
        console.log('Goodbye ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
```

## 8\. 坑爹史（1）

```javascript
var a = [0];
if ([0]) {
  console.log(a == true);
} else {
  console.log("wut");
}
```

读者们你们觉得此题答案是什么呢？true?因为[0]被看做Boolean是被认为是true，理所当然的推出来[0]==true,控制台输出true...看似没错，然而并不是这样滴~[0]这个玩意儿在单独使用的时候是被认为是true的，但用作比较的时候它是false...所以正确答案是false；不信的话，F12控制台输出[0]==false；看是不是true......

## 9\. 坑爹史（2）

```javascript
1 + - + + + - + 1
```

这题应该是等同于：（倒着看）

```javascript
1 + (a)  => 2
a = - (b) => 1
b = + (c) => -1
c = + (d) => -1
d = + (e) => -1
e = + (f) => -1
f = - (g) => -1
g = + 1   => 1
```

答案是2

## 10\. 坑爹史（3）

```javascript
function sidEffecting(ary) {
  ary[0] = ary[2];
}
function bar(a,b,c) {
  c = 10
  sidEffecting(arguments);
  return a + b + c;
}
bar(1,1,1)
```

此题涉及ES6语法，实在坑的不行...[arguments][8] 首先 The arguments object is an Array-like object corresponding to the arguments passed to a function.也就是说 arguments 是一个 object, c 就是 arguments[2], 所以对于 c 的修改就是对 arguments[2] 的修改. 所以答案是 21. 然而!!!!!! 当函数参数涉及到 any rest parameters, any default parameters or any destructured parameters 的时候, 这个 arguments 就不在是一个 mapped arguments object 了.....请看:

```javascript
function sidEffecting(ary) {
  ary[0] = ary[2];
}
function bar(a,b,c = 3) {
  c = 10
  sidEffecting(arguments);
  return a + b + c;
}
bar(1,1,1)
```

答案是12... 请读者细细体会!!

## 11\. 坑爹史（4）

```javascript
[,,,].join(", ")
```

```javascript
[,,,] => [undefined × 3]
```

因为javascript 在定义数组的时候允许最后一个元素后跟一个,, 所以这是个长度为三的稀疏数组(这是长度为三, 并没有 0, 1, 2三个属性哦) 答案: ", , "

## 12\. 浏览器懵逼史(2)

```javascript
var a = {class: "Animal", name: 'Fido'};
a.class
```

这个题比较流氓.. 因为是浏览器相关, class是个保留字(现在是个关键字了);Fuck! 所以答案不重要, 重要的是自己在取属性名称的时候尽量避免保留字. 如果使用的话请加引号 a['class']

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)