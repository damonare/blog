---
title: 理解Javascript的闭包
categories: 
  - 原创
  - 译文
tags:
    - JavaScript
    - 译文
toc: true
comments: true
---

## 前言

**总括** ：这篇文章使用有效的Javascript代码向程序员们解释了闭包，大牛和功能型程序员请自行忽略。

**译者** ：文章写在2006年，可直到翻译的21小时之前作者还在完善这篇文章，在Stackoverflow的[How do JavaScript closures work?](http://stackoverflow.com/questions/111102/how-do-javascript-closures-work)这个问题里更是得到了4000+的赞同，文章内容质量自然不必多说。

* 原文地址：[JavaScript Closures for Beginners](http://web.archive.org/web/20080209105120/http:/blog.morrisjohns.com/javascript_closures_for_dummies)
* 原文作者：Morris 
* 译者：Damonare
* 译者博客地址：[Damonare的个人博客](http://damonare.cn)

**本文属于译文** 

<!-- more -->

## 正文

### 闭包并不是魔法

这篇文章使用有效的Javascript代码向程序员们解释了闭包，大牛和功能型程序员请自行忽略。

实际上一旦你对闭包的核心概念心领神会了，闭包就不难理解了，但如果你想通过读那些学术性文章或是学院派的论文来理解闭包那基本是不可能的。

本文主要是面向那些有主流程序语言开发经验或是能看懂下面这段代码的程序员：

```javascript
function sayHello(name) {
  var text = 'Hello ' + name;
  var say = function() { console.log(text); }
  say();
}
sayHello('Joe');
```

### 一个闭包小案例

**两种方式概括：**

- 闭包是javascript支持[头等函数](https://zh.wikipedia.org/wiki/%E5%A4%B4%E7%AD%89%E5%87%BD%E6%95%B0)的一种方式，它是一个能够引用其内部作用域变量(在本作用域第一次声明的变量)的表达式，这个表达式可以赋值给某个变量，可以作为参数传递给函数，也可以作为一个函数返回值返回。

或是

- 闭包是函数开始执行的时候被分配的一个[栈帧](http://baike.baidu.com/link?url=x9za8fl-K8Gsdc0IFBbC5fTininX3H8qVBuSPsChIJd8bmzTRXvd8scDL1uCYKLS26m6GMbXgHFC5K8yXz7nZ3eImibufpfwiBWzlBDAyT_)，在函数执行结束返回后仍不会被释放(就好像一个栈帧被分配在堆里而不是栈里！)

下面这段代码返回了一个指向这个函数的引用：

```javascript
function sayHello2(name) {
  var text = 'Hello ' + name; // 局部变量text
  var say = function() { console.log(text); }
  return say;
}
var say2 = sayHello2('Bob');
say2(); // 打印日志： "Hello Bob"
```

绝大部分Javascript程序员能够理解上面代码中的一个函数引用是如何返回赋值给变量`say2`的，如果你不理解，那么你需要理解之后再来学习闭包。C语言程序员会认为这个函数返回一个指向某函数的指针，变量`say`和`say2`都是指向某个函数的指针。

Javascript的函数引用和C语言指针相比还有一个关键性的不同之处，在Javascript中，一个引用函数的变量可以看做是有两个指针，一个是指向函数的指针，一个是指向闭包的隐藏指针。

上面代码中就有一个闭包，为什么呢？因为匿名函数` function() { console.log(text); }`是在另一个函数(在本例中就是`sayHello2()`函数)声明的。在Javascript中，如果你在另一个函数中使用了`function`关键字，那么你就创建了一个闭包。

在C语言和大多数常用程序语言中，当一个函数返回后，函数内声明的局部变量就不能再被访问了，因为该函数对应的栈帧已经被销毁了。

在Javscript中，如果你在一个函数中声明了另一个函数，那么在你调用这个函数返回后里面的局部变量仍然是可以访问的。这个已经在上面的代码中演示过了，即我们在函数`sayHello()`返回后仍然可以调用函数`say2()`。**注意：我们在代码中引用的变量`text`是我们在函数`sayHello2()`中声明的局部变量。**

```javascript
function() { console.log(text); } // 输出say2.toString();
```

观察`say2.toString()`的输出，我们可以看到确实引用了`text`变量。匿名函数之所以可以引用包含`'Hello Bob'`的`text`变量就是因为`sayhello2()`的局部变量被保存在了闭包中。

神奇的是，在JavaScript中，函数引用还有一个对于它所创建的闭包的秘密引用，类似于事件委托是一个方法指针加上对于某个对象的秘密引用。

### 更多例子

出于某种不得而知的原因，当你去阅读一些关于闭包的文章的时候，闭包看起来真的是难以理解的。但如果你看到一些你能够去操作的闭包小案例(这花费了我一段时间)，闭包就容易理解了。推荐好好推敲下这几个小案例直到你彻底理解了它们到底是如何工作的。如果你没完全弄明白闭包是如何工作的就去盲目使用闭包，会搞出很多神奇的bug的！

#### 例3

局部变量虽然没有被复制，但可以通过被引用而被保留下来。这就好像外部函数退出后，但栈帧依旧保存在内存中一样。

```javascript
function say667() {
  // 局部变量num最后会保存在闭包中
  var num = 42;
  var say = function() { console.log(num); }
  num++;
  return say;
}
var sayNumber = say667();
sayNumber(); // 输出 43
```

#### 例4

下面三个全局函数对同一个闭包有一个共同的引用，因为他们都是在调用函数`setupSomeGlobals()`时声明的。

```javascript
var gLogNumber, gIncreaseNumber, gSetNumber;
function setupSomeGlobals() {
  // 局部变量num最后会保存在闭包中
  var num = 42;
  // 将一些对于函数的引用存储为全局变量
  gLogNumber = function() { console.log(num); }
  gIncreaseNumber = function() { num++; }
  gSetNumber = function(x) { num = x; }
}
setupSomeGlobals();
gIncreaseNumber();
gLogNumber(); // 43
gSetNumber(5);
gLogNumber(); // 5
var oldLog = gLogNumber;
setupSomeGlobals();
gLogNumber(); // 42
oldLog() // 5
```

这三个函数具有对同一个闭包的共享访问权限——这个闭包是指当三个函数定义时`setupSomeGlobals()`的局部变量。

**注意：在上述示例中，当你再次调用`setupSomeGlobals()`时，一个新的闭包(栈帧)就被创建了。**旧变量`gLogNumber`, `gIncreaseNumber`, `gSetNumber` 被有新闭包的函数覆盖(在JavaScript中，如果你在一个函数中声明了一个新的函数，那么当外部函数被调用时，内部函数会被重新创建)。

#### 例5

 这个示例对于很多人来说都是一个挑战，所以希望你能弄懂它。**注意：当你在一个循环里面定义一个函数的时候，闭包里的局部变量可能不会像你想的那样。**

```javascript
function buildList(list) {
    var result = [];
    for (var i = 0; i < list.length; i++) {
        var item = 'item' + i;
        result.push( function() {console.log(item + ' ' + list[i])} );
    }
    return result;
}
function testList() {
    var fnlist = buildList([1,2,3]);
    // 使用j是为了防止搞混---可以使用i
    for (var j = 0; j < fnlist.length; j++) {
        fnlist[j]();
    }
}
 testList() //输出 "item2 undefined" 3 次
```

`result.push( function() {console.log(item + ' ' + list[i])}`这一行给`result`数组添加了三次函数匿名引用。如果你不熟悉匿名函数可以想象成下面代码：

```javascript
pointer = function() {console.log(item + ' ' + list[i])};
result.push(pointer);
```

注意，当你运行上述代码的时候会打印`"item2 undefined"`三次！和前面的示例一样，和`buildList`的局部变量对应的闭包只有一个。当匿名函数在`fnlist[j]()`这一行调用的时候，他们使用同一个闭包，而且是使用的这个闭包里`i`和`item`现在的值(循环结束后`i`的值为3，`item`的值为`'item2'`)。**注意：我们从索引`0`开始，所以`item`最后的值为`item2'`，`i`的值会被`i++`增加到`3` 。**

#### 例6

这个例子表明了闭包会保存函数退出之前内部定义的所有的局部变量。**注意：变量`alice`是在匿名函数之前创建的。** 匿名函数先被声明，然后当它被调用的时候之所以能够访问`alice`是因为他们在同一个作用域内(JavaScript做了[变量提升](http://stackoverflow.com/questions/3725546/variable-hoisting/3725763#3725763))，`sayAlice()()`直接调用了从`sayAlice()`中返回的函数引用——这个和前面的完全一样，只是少了临时的变量【译者注：存储sayAlice()返回的函数引用的变量】

```javascript
function sayAlice() {
    var say = function() { console.log(alice); }
    // 局部变量最后保存在闭包中
    var alice = 'Hello Alice';
    return say;
}
sayAlice()();// 输出"Hello Alice"
```

**技巧：需要注意变量`say`也是在闭包内部，也能被在`sayAlice()`内部声明的其它函数访问，或者也可以在函数内部递归访问它。**

#### 例7

最后一个例子说明了每次调用函数都会为局部变量创建一个闭包。实际上每次函数声明并不会创建一个单独的闭包，但每次调用函数都会创建一个独立的闭包。

```javascript
function newClosure(someNum, someRef) {
    // 局部变量最终保存在闭包中
    var num = someNum;
    var anArray = [1,2,3];
    var ref = someRef;
    return function(x) {
        num += x;
        anArray.push(num);
        console.log('num: ' + num +
            '\nanArray ' + anArray.toString() +
            '\nref.someVar ' + ref.someVar);
      }
}
obj = {someVar: 4};
fn1 = newClosure(4, obj);
fn2 = newClosure(5, obj);
fn1(1); // num: 5; anArray: 1,2,3,5; ref.someVar: 4;
fn2(1); // num: 6; anArray: 1,2,3,6; ref.someVar: 4;
obj.someVar++;
fn1(2); // num: 7; anArray: 1,2,3,5,7; ref.someVar: 5;
fn2(2); // num: 8; anArray: 1,2,3,6,8; ref.someVar: 5;
```

#### 总结

如果任何不太明白的地方最好的方式就是把玩这几个例子，去机械地阅读一些文章远比去做这些实例难得多。我关于闭包的说明、栈框体(stack-frame)的说明等等，严格理论上讲并不是完全正确的——它们只是为了理解而简化处理过的。当基础的概念心领神会之后，就可以轻松地理解这些细节了。

### 最终总结

- 每当你在另一个函数里使用了关键字`function`，一个闭包就被创建了；
- 每当你在一个函数内部使用了`eval()`，一个闭包就被创建了。在`eval`内部你可以引用外部函数定义的局部变量，同样的，在`eval`内部也可以通过`eval('var foo = …')`来创建新的局部变量；
- 当你在一个函数内部使用`new function(...)`(即[构造函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function))时，它不会创建闭包(新函数不能引用外部函数的局部变量）；
- JavaScript中的闭包，就像一个副本，将某函数在退出时候的所有局部变量复制保存其中；
- 也许最好的理解是闭包总是在进入某个函数的时候被创建，而局部变量是被加入到这个闭包中；
- 闭包函数每次被调用的时候都会创建一组新的局部变量存储。(前提是这个函数包含一个内部的函数声明，并且这个函数的引用被返回或者用某种方法被存储到一个外部的引用中)；
- 两个函数或许从源代码文本上看起来一样，但因为隐藏闭包的存在会让两个函数具有不同的行为。我认为Javascript代码实际上并不能找出一个函数引用是否有闭包；
- 如果你正尝试做一些动态源代码的修改(例如：`myFunction = Function(myFunction.toString().replace(/Hello/,'Hola'));`)，如果`myFunction`是一个闭包的话，那么这并不会生效(当然，你甚至可能从来都没有在运行的时候考虑过修改源代码字符串，但是。。。)；
- 在函数内部的函数的内部声明函数是可以的——可以获得不止一个层级的闭包；
- 通常我认为闭包是一个同时包含函数和被捕捉的变量的术语，但是请注意我并没有在本文中使用这个定义；
- 我觉得JavaScript中的闭包跟其它函数式编程语言中的闭包是有不同之处的。

### 感谢

如果你正好在学习闭包(在这里或是其他地方)，期待您对本文的任何反馈，您的任何建议都可能会使本文更加清晰易懂。请联系<a href="mailto:jztan1996@gmail.com">jztan1996@gmail.com 【译者注：这是译者的邮箱，欢迎交流学习】</a>

## 后记

这是译者翻译的第一篇文章，收获良多，感觉上并不比自己写一篇文章省事，相反熟悉内容了解代码的同时还得去揣摩作者表达的意图，难度的确要比自己单独写一篇高。能力有限，水平一般，有翻译不到位的地方，欢迎批评指正。感谢！

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)