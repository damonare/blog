---
title: Javascript本地存储小结
categories:
    - 原创
tags:
    - JavaScript
    - HTML
    - 缓存
toc: true
comments: true
---

## 前言

**总括:** 详细讲述`Cookie`,`LocalStorge`,`SesstionStorge`的区别和用法。


- 原文博客地址：[Javascript本地存储小结](http://blog.damonare.cn/2016/11/26/Javascript%E6%9C%AC%E5%9C%B0%E5%AD%98%E5%82%A8%E5%B0%8F%E7%BB%93/)

- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)

- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人生如画，岁月如歌。**

<!-- more -->****。**。**

## 正文

### 1. 各种存储方案的简单对比

- Cookies：浏览器均支持，容量为4KB；
- UserData：仅IE支持，容量为64KB；
- Flash：100KB，非HTML原生，需要插件支持；
- Google Gears SQLite ：需要插件支持，容量无限制；
- LocalStorage：HTML5，容量为5M；
- SesstionStorage：HTML5，容量为5M；
- globalStorage：Firefox独有的，Firefox13开始就不再支持这个方法；
> UserData仅IE支持， Google Gears SQLite需要插件，Flash已经伴随着HTML5的出现渐渐退出了历史舞台，因此今天我们的主角只有他们三个：`cookie`,`localStorge`,`sesstionStorge`;

测

### 2. Cookie

作为一个前端和Cookie打交道的次数肯定不会少了，Cookie算是比较古老的技术了
1993 年，网景公司雇员 Lou Montulli 为了让用户在访问某网站时，进一步提高访问速度，同时也为了进一步实现个人化网络，发明了今天广泛使用的 Cookie。

#### 2.1 Cookie的特点

**我们先来看下Cookie的特点：**

- 1）cookie的大小受限制，cookie大小被限制在4KB，不能接受像大文件或邮件那样的大数据;

- 2）只要有请求涉及cookie，cookie就要在服务器和浏览器之间来回传送（这解释为什么本地文件不能测试cookie）。而且cookie数据始终在同源的http请求中携带（即使不需要），这也是Cookie不能太大的重要原因。正统的cookie分发是通过扩展HTTP协议来实现的，服务器通过在HTTP的响应头中加上一行特殊的指示以提示浏览器按照指示生成相应的cookie;

- 3）用户每请求一次服务器数据，cookie则会随着这些请求发送到服务器，服务器脚本语言如PHP等能够处理cookie发送的数据，可以说是非常方便的。当然前端也是可以生成Cookie的，用js对cookie的操作相当的繁琐，浏览器只提供document.cookie这样一个对象，对cookie的赋值，获取都比较麻烦。而在PHP中，我们可以通过setcookie()来设置cookie，通过$_COOKIE这个超全局数组来获取cookie。

> cookie的内容主要包括：名字，值，过期时间，路径和域。路径与域一起构成cookie的作用范围。若不设置过期时间，则表示这个cookie的生命期为浏览器会话期间，关闭浏览器窗口，cookie就消失。这种生命期为浏览器会话期的cookie被称为会话cookie。会话cookie一般不存储在硬盘上而是保存在内存里，当然这种行为并不是规范规定的。若设置了过期时间，浏览器就会把cookie保存到硬盘上，关闭后再次打开浏览器，这些cookie仍然有效直到超过设定的过期时间。存储在硬盘上的cookie可以在不同的浏览器进程间共享，比如两个IE窗口。而对于保存在内存里的cookie，不同的浏览器有不同的处理方式。

#### 2.2 Session

说到Cookie就不能不说Session。

Session机制。session机制是一种服务器端的机制，服务器使用一种类似于散列表的结构（也可能就是使用散列表）来保存信息。当程序需要为某个客户端的请求创建一个session时，服务器首先检查这个客户端的请求里是否已包含了一个session标识（称为session id），如果已包含则说明以前已经为此客户端创建过session，服务器就按照session id把这个session检索出来使用（检索不到，会新建一个），如果客户端请求不包含session id，则为此客户端创建一个session并且生成一个与此session相关联的session id，session id的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串，这个session id将被在本次响应中返回给客户端保存。保存这个session id的方式可以采用cookie，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。一般这个cookie的名字都是类似于SEEESIONID。但cookie可以被人为的禁止，则必须有其他机制以便在cookie被禁止时仍然能够把session id传递回服务器。经常被使用的一种技术叫做URL重写，就是把session id直接附加在URL路径的后面。比如：`http://damonare.cn?sessionid=123456`还有一种技术叫做表单隐藏字段。就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把session id传递回服务器。比如：

```html
<form name="testform" action="/xxx">
    <input type="hidden" name="sessionid" value="123456">
    <input type="text">
</form>
```

实际上这种技术可以简单的用对action应用URL重写来代替。

#### 2.3 Cookie和Session简单对比

**Cookie和Session 的区别：**

- 1）cookie数据存放在客户的浏览器上，session数据放在服务器上；

- 2）cookie不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到安全应当使用session；

- 3）session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能考虑到减轻服务器性能方面，应当使用cookie；

- 4）单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie；

   **建议：**

   - 将登陆信息等重要信息存放为seesion
   - 其他信息如果需要保留，可以放在cookie中

#### 2.4 document.cookie的属性

**expires属性**

指定了cookie的生存期，默认情况下cookie是暂时存在的，他们存储的值只在浏览器会话期间存在，当用户推出浏览器后这些值也会丢失，如果想让cookie存在一段时间，就要为expires属性设置为未来的一个过期日期。现在已经被max-age属性所取代，max-age用秒来设置cookie的生存期。

**path属性**

它指定与cookie关联在一起的网页。在默认的情况下cookie会与创建它的网页，该网页处于同一目录下的网页以及与这个网页所在目录下的子目录下的网页关联。

**domain属性**

domain属性可以使多个web服务器共享cookie。domain属性的默认值是创建cookie的网页所在服务器的主机名。不能将一个cookie的域设置成服务器所在的域之外的域。例如让位于order.damonare.cn的服务器能够读取catalog.damonare.cn设置的cookie值。如果catalog.damonare.cn的页面创建的cookie把自己的path属性设置为“/”，把domain属性设置成“.damonare.cn”，那么所有位于catalog.damonare.cn的网页和所有位于orlders.damonare.cn的网页，以及位于damonare.cn域的其他服务器上的网页都可以访问这个cookie。

**secure属性**

它是一个布尔值，指定在网络上如何传输cookie，默认是不安全的，通过一个普通的http连接传输

#### 2.5 cookie实战

这里我们使用javascript来写一段cookie,借用w3cschool的demo:

```Javascript
function getCookie(c_name){
    if (document.cookie.length>0){
        c_start=document.cookie.indexOf(c_name + "=")
        if (c_start!=-1){
            c_start=c_start + c_name.length+1
            c_end=document.cookie.indexOf(";",c_start)
            if (c_end==-1) c_end=document.cookie.length
            return unescape(document.cookie.substring(c_start,c_end))
        }
    }
    return "";
}

function setCookie(c_name,value,expiredays){
    var exdate=new Date()
    exdate.setDate(exdate.getDate()+expiredays)
    document.cookie=c_name+ "=" +escape(value)+
            ((expiredays==null) ? "" : "; expires="+exdate.toUTCString())
}
function checkCookie(){
    username=getCookie('username')
    if(username!=null && username!=""){alert('Welcome again '+username+'!')}
    else{
        username=prompt('Please enter your name:',"")
        if (username!=null && username!=""){
            setCookie('username',username,355)
        }
    }
}
```

> 注意这里对Cookie的生存期进行了定义，也就是355天

### 3. localStorage

这是一种持久化的存储方式，也就是说如果不手动清除，数据就永远不会过期。
它也是采用Key - Value的方式存储数据，底层数据接口是sqlite，按域名将数据分别保存到对应数据库文件里。它能保存更大的数据（IE8上是10MB，Chrome是5MB），同时保存的数据不会再发送给服务器，避免带宽浪费。

#### 3.1 localStorage的属性方法

下表是localStorge的一些属性和方法

| 属性方法                             | 说明                           |
| -------------------------------- | ---------------------------- |
| localStorage.length              | 获得storage中的个数                |
| localStorage.key(n)              | 获得storage中第n个元素对的键值（第一个元素是0） |
| localStorage.getItem(key)        | 获取键值key对应的值                  |
| localStorage.key                 | 获取键值key对应的值                  |
| localStorage.setItem(key, value) | 添加数据，键值为key，值为value          |
| localStorage.removeItem(key)     | 移除键值为key的数据                  |
| localStorage.clear()             | 清除所有数据                       |

#### 3.2 localStorage的缺点

① localStorage大小限制在500万字符左右，各个浏览器不一致

② localStorage在隐私模式下不可读取

③ localStorage本质是在读写文件，数据多的话会比较卡（firefox会一次性将数据导入内存，想想就觉得吓人啊）

④ localStorage不能被爬虫爬取，不要用它完全取代URL传参

### 4. sessionStorage

和服务器端使用的session类似，是一种会话级别的缓存，关闭浏览器会数据会被清除。不过有点特别的是它的作用域是窗口级别的，也就是说不同窗口间的sessionStorage数据不能共享的。使用方法（和localStorage完全相同）：

| 属性方法                               | 说明                           |
| ---------------------------------- | ---------------------------- |
| sessionStorage.length              | 获得storage中的个数                |
| sessionStorage.key(n)              | 获得storage中第n个元素对的键值（第一个元素是0） |
| sessionStorage.getItem(key)        | 获取键值key对应的值                  |
| sessionStorage.key                 | 获取键值key对应的值                  |
| sessionStorage.setItem(key, value) | 添加数据，键值为key，值为value          |
| sessionStorage.removeItem(key)     | 移除键值为key的数据                  |
| sessionStorage.clear()             | 清除所有数据                       |

### 5. sessionStorage和localStorage的区别

- sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。当用户关闭浏览器窗口后，数据立马会被删除。

- localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。第二天、第二周或下一年之后，数据依然可用。

#### 5.1 测试

**sessionStorage:**

```javascript
if (sessionStorage.pagecount) {
    sessionStorage.pagecount = Number(sessionStorage.pagecount) + 1;
} else {
    sessionStorage.pagecount = 1;
}
console.log("Visits "+ sessionStorage.pagecount + " time(s).");
```

测试过程：我们在控制台输入上述代码查看打印结果

控制台首次输入代码：

![sessionStorage测试结果](http://img.blog.csdn.net/20161116225014652)

关闭窗口，控制台再次输入代码：

![sessionStorage测试结果](http://img.blog.csdn.net/20161116225014652)

所谓的关闭窗口即销毁，就是这样，关闭窗口重新打开输入代码输出结果还是上面图片的样子，也就是说关闭窗口后sessionStorage.pagecount即被销毁，除非重心创建。或者从历史记录进入才会相关数据才会存在。好的，我们再来看下localStorge表现：

```javascript
if (localStorage.pagecount) {
    localStorage.pagecount = Number(localStorage.pagecount) + 1;
} else {
    localStorage.pagecount = 1;
}
console.log("Visits "+ localStorage.pagecount + " time(s).");
```

控制台首次输入代码：

![localStorage测试结果1](http://img.blog.csdn.net/20161116225028996)

关闭窗口，控制台再次输入代码：

![localStorage测试结果2](http://img.blog.csdn.net/20161116225040575)


### 6. web Storage和cookie的区别

Web Storage(localStorage和sessionStorage)的概念和cookie相似，区别是它是为了更大容量存储设计的。Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。

除此之外，Web Storage拥有setItem,getItem,removeItem,clear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。

但是Cookie也是不可以或缺的：Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生

## 后记

> 博主尽可能思路清晰的理了一遍cookie，session，localStorage，sessionStorage之间的区别和联系，希望可以帮到大家。
> 参考文章：

- [cookie 和session 的区别详解](http://www.cnblogs.com/shiyangxt/archive/2008/10/07/1305506.html)

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)