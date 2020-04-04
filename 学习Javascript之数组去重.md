---
title: 学习Javascript之数组去重
categories:
  - 原创
tags:
  - JavaScript
toc: true
comments: true
---

## 前言

> 本文2895字，阅读大约需要12分钟。

**总括:**  本文总结了10种常见的数组去重方法，并将各种方法进行了对比。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**如烟往事俱忘却，心底无私天地宽**。

<!-- more -->

## 正文

数组去重对于前端来说不是一个常见的需求，一般后端都给做了，但这却是一个有意思的问题，而且经常出现在面试中来考察面试者对JS的掌握程度。本文从数据类型的角度去思考数组去重这个问题，首先解决的是数组中只有基础数据类型的情况，然后是对象的去重。首先是我们的测试数据：

```js
var meta = [
    0,
    '0', 
    true,
    false,
    'true',
    'false',
    null,
    undefined,
    Infinity,
    {},
    [],
    function(){},
    { a: 1, b: 2 },
    { b: 2, a: 1 },
];
var meta2 = [
    NaN,
      NaN,
    Infinity,
    {},
    [],
    function(){},
    { a: 1, b: 2 },
    { b: 2, a: 1 },
];
var sourceArr = [...meta, ... Array(1000000)
    .fill({})
    .map(() => meta[Math.floor(Math.random() * meta.length)]),
    ...meta2];
```

下文中引用的所有`sourceArr`都是上面的变量。`sourceArr`中包含了`1000008`条数据。需要注意的是`NaN`，它是JS中唯一一个和自身严格不相等的值。

然后我们的目标是将上面的`sourceArr`数组去重得到:

```js
// 长度为14的数组
[false, "true", Infinity, true, 0, [], {}, "false", "0", null, undefined, {a: 1, b: 2}, NaN, function(){}]
```

### 基础数据类型

#### 1. ES6中Set

这是在ES6中很常用的一种方法，对于简单的基础数据类型去重，完全可以直接使用这种方法，`扩展运算符 + Set`：

```js
console.time('ES6中Set耗时：');
var res = [...new Set(sourceArr)];
console.timeEnd('ES6中Set耗时：');
// ES6中Set耗时：: 28.736328125ms
console.log(res);
// 打印数组长度20： [false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, NaN, function(){}, function(){}]
```

或是使用`Array.from + Set`：

```js
console.time('ES6中Set耗时：');
var res = Array.from(new Set(sourceArr));
console.timeEnd('ES6中Set耗时：');
// ES6中Set耗时：: 28.538818359375ms
console.log(res);
// 打印数组长度20：[false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, NaN, function(){}, function(){}]
```

**优点：**简洁方便，可以区分`NaN`；

**缺点：**无法识别相同对象和数组；

简单的场景建议使用该方法进行去重。

#### 2. 使用indexOf

使用内置的indexOf方法进行查找：

```js
function unique(arr) {
    if (!Array.isArray(arr)) return;
    var result = [];
    for (var i = 0; i < arr.length; i++) {
        if (array.indexOf(arr[i]) === -1) {
            result.push(arr[i])
        }
    }
    return result;
}
console.time('indexOf方法耗时：');
var res = unique(sourceArr);
console.timeEnd('indexOf方法耗时：');
// indexOf方法耗时：: 23.376953125ms
console.log(res);
// 打印数组长度21: [false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, NaN,NaN, function(){}, function(){}]
```

**优点**：ES5以下常用方法，兼容性高，易于理解；

**缺点**：无法区分`NaN`;需要特殊处理；

可以在ES6以下环境使用。

#### 3. 使用inculdes方法

和`indexOf`类似，但`inculdes`是ES7(ES2016)新增API：

```js
function unique(arr) {
    if (!Array.isArray(arr)) return;
    var result = [];
    for (var i = 0; i < arr.length; i++) {
        if (!result.includes(arr[i])) {
            result.push(arr[i])
        }
    }
    return result;
}
console.time('includes方法耗时：');
var res = unique(sourceArr);
console.timeEnd('includes方法耗时：');
// includes方法耗时：: 32.412841796875ms
console.log(res);
// 打印数组长度20：[false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, NaN, function(){}, function(){}]
```

**优点**：可以区分`NaN`；

**缺点**：ES版本要求高，和`indexOf`方法相比耗时较长；

#### 4. 使用filter和indexOf方法

这种方法比较巧妙，通过判断当前的index值和查找到的index是否相等来决定是否过滤元素：

```js
function unique(arr) {
       if (!Array.isArray(arr)) return;
    return arr.filter(function(item, index, arr) {
        //当前元素，在原始数组中的第一个索引==当前索引值，否则返回当前元素
        return arr.indexOf(item, 0) === index;
    });
}
console.time('filter和indexOf方法耗时：');
var res = unique(sourceArr);
console.timeEnd('filter和indexOf方法耗时：');
// includes方法耗时：: 24.135009765625ms
console.log(res);
// 打印数组长度19：[false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, function(){}, function(){}]
```

**优点**：利用高阶函数代码大大缩短；

**缺点**：由于`indexOf`无法查找到`NaN`，因此`NaN`被忽略。

这种方法很优雅，代码量也很少，但和使用Set结构去重相比还是美中不足。

#### 5. 利用reduce+includes

同样是两个高阶函数的巧妙使用：

```js
var unique = (arr) =>  {
   if (!Array.isArray(arr)) return;
   return arr.reduce((prev,cur) => prev.includes(cur) ? prev : [...prev,cur],[]);
}
var res = unique(sourceArr);
console.time('reduce和includes方法耗时：');
var res = unique(sourceArr);
console.timeEnd('reduce和includes方法耗时：');
// reduce和includes方法耗时：: 100.47802734375ms
console.log(res);
// 打印数组长度20：[false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, NaN, function(){}, function(){}]
```

**优点**：利用高阶函数代码大大缩短；

**缺点**：ES版本要求高，速度较慢；

同样很优雅，但如果这种方法能用，同样也能用Set结构去重。

#### 6. 利用Map结构

使用map实现：

```js
function unique(arr) {
  if (!Array.isArray(arr)) return;
  let map = new Map();
  let result = [];
  for (let i = 0; i < arr.length; i++) {
    if(map .has(arr[i])) {
      map.set(arr[i], true); 
    } else { 
      map.set(arr[i], false);
      result.push(arr[i]);
    }
  } 
  return result;
}
console.time('Map结构耗时：');
var res = unique(sourceArr);
console.timeEnd('Map结构耗时：');
// Map结构耗时：: 41.483154296875ms
console.log(res);
// 打印数组长度20：[false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, NaN, function(){}, function(){}]
```

相比Set结构去重消耗时间较长，不推荐使用。

#### 7. 双层嵌套，使用splice删除重复元素

这个也比较常用，对数组进行双层遍历，挑出重复元素：

```js
function unique(arr){    
    if (!Array.isArray(arr)) return;        
    for(var i = 0; i < arr.length; i++) {
        for(var j = i + 1; j<  arr.length; j++) {
            if(Object.is(arr[i], arr[j])) {// 第一个等同于第二个，splice方法删除第二个
                arr.splice(j,1);
                j--;
            }
        }
    }
    return arr;
}
console.time('双层嵌套方法耗时：');
var res = unique(sourceArr);
console.timeEnd('双层嵌套方法耗时：');
// 双层嵌套方法耗时：: 41500.452880859375ms
console.log(res);
// 打印数组长度20: [false, "true", Infinity, true, 0, [],  [], {b: 2, a: 1}, {b: 2, a: 1}, {}, {}, "false", "0", null, undefined, {a: 1, b: 2}, {a: 1, b: 2}, NaN, function(){}, function(){}]
```

**优点**：兼容性高。

**缺点**：性能低，时间复杂度高。

不推荐使用。

#### 8. 利用sort方法

这个思路也很简单，就是利用`sort`方法先对数组进行排序，然后再遍历数组，将和相邻元素不相同的元素挑出来：

```js
 function unique(arr) {
   if (!Array.isArray(arr)) return;
   arr = arr.sort((a, b) => a - b);
   var result = [arr[0]];
   for (var i = 1; i < arr.length; i++) {
     if (arr[i] !== arr[i-1]) {
       result.push(arr[i]);
     }
   }
   return result;
 }
console.time('sort方法耗时：');
var res = unique(sourceArr);
console.timeEnd('sort方法耗时：');
// sort方法耗时：: 936.071044921875ms
console.log(res);
// 数组长度357770，剩余部分省略
// 打印：(357770) [Array(0), Array(0), 0...]
```

**优点**：无；

**缺点**：耗时长，排序后数据不可控；

不推荐使用，因为使用sort方法排序无法对数字类型`0`和字符串类型`'0'`进行排序导致大量的冗余数据存在。

上面的方法只是针对基础数据类型，对于对象数组函数不考虑，下面再看下如何去重相同的对象。

### Object

下面的这种实现和利用Map结构相似，这里使用对象的key不重复的特性来实现

#### 9. 利用hasOwnProperty和filter

使用`filter`和`hasOwnProperty`方法：

```js
function unique(arr) {
      if (!Array.isArray(arr)) return;
    var obj = {};
    return arr.filter(function(item, index, arr) {
        return obj.hasOwnProperty(typeof item + item) ? false : (obj[typeof item + item] = true)
    })
}
console.time('hasOwnProperty方法耗时：');
var res = unique(sourceArr);
console.timeEnd('hasOwnProperty方法耗时：');
// hasOwnProperty方法耗时：: 258.528076171875ms
console.log(res);
// 打印数组长度13: [false, "true", Infinity, true, 0, [], {}, "false", "0", null, undefined, NaN, function(){}]
```

**优点**：代码简洁，可以区分相同对象数组函数；

**缺点**：版本要求高，因为要查找整个原型链因此性能较低；

该方法利用对象key不重复的特性来实现区分对象和数组，但上面是通过`类型+值`做key的方式，所以`{a: 1, b: 2}`和`{}`被当做了相同的数据。因此该方法也有不足。

#### 10. 利用对象key不重复的特性

这种方法和使用`Map`结构类似，但`key`的组成有所不同:

```js
function unique(arr) {
    if (!Array.isArray(arr)) return;
    var result = [];
     var  obj = {};
    for (var i = 0; i < arr.length; i++) {
        var key = typeof arr[i] + JSON.stringify(arr[i]) + arr[i];
        if (!obj[key]) {
            result.push(arr[i]);
            obj[key] = 1;
        } else {
            obj[key]++;
        }
    }
    return result;
}
console.time('对象方法耗时：');
var res = unique(sourceArr);
console.timeEnd('对象方法耗时：');
// 对象方法耗时：: 585.744873046875ms
console.log(res);
// 打印数组长度15: [false, "true", Infinity, true, 0, [], {b: 2, a: 1}, {}, "false", "0", null, undefined, {a: 1, b: 2}, NaN, function(){}]
```

这种方法是比较成熟的，去除了重复数组和重复对象，但对于像`{a: 1, b: 2}`和`{b: 2, a: 1}`这种就无法区分，原因在于将这两个对象进行`JSON.stringify()`之后得到的字符串分别是`{"a":1,"b":2}`和`{"b":2,"a":1}`, 因此两个值算出的key不同。加一个判断对象是否相等的方法就好了，改写如下：

```js
function isObject(obj) {
    return Object.prototype.toString.call(obj) === '[object Object]';
}
function unique(arr) {
    if (!Array.isArray(arr)) return;
    var result = [];
     var  obj = {};
    for (var i = 0; i < arr.length; i++) {
          // 此处加入对象和数组的判断
        if (Array.isArray(arr[i])) {
            arr[i] = arr[i].sort((a, b) => a - b);
        }
        if (isObject(arr[i])) {
            let newObj = {}
            Object.keys(arr[i]).sort().map(key => {
                newObj[key]= arr[i][key];
            });
            arr[i] = newObj;
        }
        var key = typeof arr[i] + JSON.stringify(arr[i]) + arr[i];
        if (!obj[key]) {
            result.push(arr[i]);
            obj[key] = 1;
        } else {
            obj[key]++;
        }
    }
    return result;
}
console.time('对象方法耗时：');
var res = unique(sourceArr);
console.timeEnd('对象方法耗时：');
// 对象方法耗时：: 793.142822265625ms
console.log(res);
// 打印数组长度14: [false, "true", Infinity, true, 0, [], {b: 2, a: 1}, {}, "false", "0", null, undefined, NaN, function(){}]
```

### 结论

| 方法                             | 优点                                 | 缺点                                                         |
| -------------------------------- | ------------------------------------ | ------------------------------------------------------------ |
| ES6中Set                         | 简单优雅，速度快                     | **基础类型推荐使用**。版本要求高，不支持对象数组和`NaN`      |
| 使用indexOf                      | ES5以下常用方法，兼容性高，易于理解  | 无法区分`NaN`;需要特殊处理                                   |
| 使用inculdes方法                 | 可以区分`NaN`                        | ES版本要求高，和`indexOf`方法相比耗时较长                    |
| 使用filter和indexOf方法          | 利用高阶函数代码大大缩短；           | 由于`indexOf`无法查找到`NaN`，因此`NaN`被忽略。              |
| 利用reduce+includes              | 利用高阶函数代码大大缩短；           | ES7以上才能使用，速度较慢；                                  |
| 利用Map结构                      | 无明显优点                           | ES6以上，                                                    |
| 双层嵌套，使用splice删除重复元素 | 兼容性高                             | 性能低，时间复杂度高，如果不使用`Object.is`来判断则需要对`NaN`特殊处理，速度极慢。 |
| 利用sort方法                     | 无                                   | 耗时长，排序后数据不可控；                                   |
| 利用hasOwnProperty和filter       | ：代码简洁，可以区分相同对象数组函数 | 版本要求高，因为要查找整个原型链因此性能较低；               |
| 利用对象key不重复的特性          | 优雅，数据范围广                     | **Object推荐使用**。代码比较复杂。                           |

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)