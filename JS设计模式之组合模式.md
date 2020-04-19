---
title:  JS设计模式之组合模式
categories:
  - 原创
tags:
  - JavaScript
  - 设计模式
  - 结构型模式
toc: true
comments: true
---

## 前言

> 本文659字，阅读大约需要5分钟。

**总括:** 在Javascript中实现23种经典设计模式的组合模式。

- 公众号：「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

**业精于勤，荒于嬉；行成于思，毁于随**。

<!-- more -->


## 正文

组合模式(Composite pattern):   **将对象组合成树形结构以表示“部分整体”的层次结构**。

组合模式的核心在于“组合”，最终的对象结构是一个树形结构。我们以创建一个商店的菜单为例去应用组合模式。因为不同商家售卖的产品不同，每个产品的特色不同，所以最终每个商店的菜单都是不一样的，这个场景就是组合模式的用武之地。我们分析下一个简单的商店菜单的大概构成：

```bash
├── 菜单
|   ├── 主食类
|   |   ├── 米饭
|   |   ├── 面
|   ├── 菜类
|   |   ├── 酸辣土豆丝
|   |   ├── 西红柿炒鸡蛋
|   ├── 汤类
|   |   ├── 紫菜蛋花汤
|   |   ├── 猪蹄莲藕汤
```

好的，第一步先创建一个菜单类：

```js
function Menu(name, type) {
  this.name = name;
  this.type = type;
  this.child = [];
}
Menu.prototype.add = function(item) {
  this.child.push(item);
  return this;
}
Menu.prototype.show = function() {
  console.log(this.child);
}
```

然后创建一个主食，菜，汤类：

```js
function MainFood() {
  this.child = [];
}
MainFood.prototype.add = function(food) {
  this.child.push(food);
  return this;
}
function Entres() {
  this.child = [];
}
Entres.prototype.add = function(food) {
  this.child.push(food);
  return this;
}
function Soup() {
  this.child = [];
}
Soup.prototype.add = function(food) {
  this.child.push(food);
  return this;
}
```

最后是一个具体食物类：

```js
function FoodComponent() {
}
FoodComponent.prototype.getName = function () {
    throw new Error("该方法必须重写!");
};
FoodComponent.prototype.getPrice = function () {
    throw new Error("该方法必须重写!");
};
function Food(name, price) {
  this.name = name;
  this.price = price;
}
Food.prototype = Object.create(FoodComponent.prototype);
Food.prototype.getName = function() {
    return this.name;
}
Food.prototype.getPrice = function() {
    return this.price;
}
```

好的，工作做完了我们来创建一个菜单：

```js
var menuA = new Menu('快餐店', '快餐');
menuA.add(
    new MainFood().add(new Food('米饭', '1元')).add(new Food('面', '10元'))
).add(
    new Entres().add(new Food('酸辣土豆丝', '7元')).add(new Food('西红柿炒鸡蛋', '8元'))
).add(
    new Soup().add(new Food('紫菜蛋花汤', '15元')).add(new Food('猪蹄莲藕汤', '20元'))
).show();
// (3) [MainFood, Entres, Soup]
console.log(menuA);
// Menu {name: "快餐店", type: "快餐", child: Array(3)}
```

如上就是一个组合模式的应用，通过这种形式很容易去创建不同的菜单，而不必去关乎具体的实现，更不用写一堆的if/else去分别处理。

以上。

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)