---
title: 学习Javascript数据结构之树
categories:
    - 原创
tags:
    - Javascript
    - 数据结构
    - 算法
comments: true
---

## 前言

**总括：** 本文讲解了数据结构中的[树]的概念，尽可能通俗易懂的解释树这种数据结构的概念，使用javascript实现了树，如有纰漏，欢迎批评指正。

- 原文博客地址：[学习javascript数据结构(四)——树](http://blog.damonare.cn/2017/01/16/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E5%9B%9B%EF%BC%89%E2%80%94%E2%80%94%E6%A0%91/#more)
- 知乎专栏&&简书专题：[前端进击者（知乎）](https://zhuanlan.zhihu.com/damonare)&&[前端进击者（简书）](http://www.jianshu.com/collection/bbaa63e264f5)
- 博主博客地址：[Damonare的个人博客](http://damonare.cn)

**人之所能，不能兼备，弃其所短，取其所长。**

<!-- more -->

## 正文

### 树简介

在上一篇[学习javascript数据结构(三)——集合](http://damonare.github.io/2016/11/26/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%B8%89%EF%BC%89%E2%80%94%E2%80%94%E9%9B%86%E5%90%88/#more)中我们说了集合这种数据结构，在[学习javascript数据结构(一)——栈和队列](http://damonare.github.io/2016/11/01/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%B8%80%EF%BC%89%E2%80%94%E2%80%94%E6%A0%88%E5%92%8C%E9%98%9F%E5%88%97/#more)和[学习javascript数据结构(二)——链表](http://damonare.github.io/2016/11/09/%E5%AD%A6%E4%B9%A0javascript%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%BA%8C%EF%BC%89%E2%80%94%E2%80%94%E9%93%BE%E8%A1%A8/#more)说了栈和队列以及链表这类线性表数据结构。接下来这一篇说的是`树`这种数据结构。首先想让大家明白的是数据结构是个什么玩意儿，数据结构可以分为数据的逻辑结构和数据的物理结构，所谓的数据逻辑结构在我理解就是计算机对于数据的组织方式的研究。也就是说研究的是数据和数据之间的关系。而数据的物理结构是数据的逻辑结构在计算机中的具体实现，也就是说一种逻辑结构可能会有多种存储结构与之相对应。

那么我们这一篇所说的`树`就是一种数据逻辑结构，即研究的是数据和数据之间的关系。之前所说的`栈`、`队列`、`链表`都是一种线性结构，相信大家也能发现这种线性结构的数据关系有一个共同点，就是数据都是一对一的，而上一篇说到的集合这种数据结构，数据是散乱的，他们之间的关系就是隶属于同一个集合，如上一篇例子所说，这些小孩子都是同一个幼儿园的，但是这些小孩子之间的关系我们并不知道。线性表(栈、队列、链表)就是对这些小孩子关系的一种表达(一对一)。而集合也是对于这些小孩子关系的一种表达。和线性表不同的是，树这种数据结构是一对多的，也就是说他所描述的是某个小孩子和其它小孩子之间的关系。

树这种结构实际上我们平时也有见到，比如下图这种简单的思维导图：

![思维导图](https://wlqcev.coding-pages.com/4e4a20a4462309f7b9c58b78720e0cf3d7cad635.jpg)

如下也是一棵树：

![树](https://wlqcev.coding-pages.com/2009072216345191.jpg)

关于树概念总结如下:

 1）树形结构是一对多的非线性结构。
 2）树形结构有树和二叉树两种，树的操作实现比较复杂，但树可以转换为二叉树进行处理。
 3）树的定义：树(Tree)是 n(n≥0)个相同类型的数据元素的有限集合。
 4）树中的数据元素叫节点(Node)。
 5）n=0 的树称为空树(Empty Tree)；
 6）对于 n＞0 的任意非空树 T 有： 
     （1）有且仅有一个特殊的节点称为树的根(Root)节点，根没有前驱节点； 
     （2）若n＞1，则除根节点外，其余节点被分成了m(m＞0)个互不相交的集合
           T1，T2，。。。，Tm，其中每一个集合Ti(1≤i≤m)本身又是一棵树。树T1，T2，。。。，Tm称为这棵树的子树(Subtree)。 
 7）树的定义是递归的，用树来定义树。因此，树（以及二叉树）的许多算法都使用了递归。 

参看维基百科对于`树`的定义：

> 在计算机科学中，**树**（英语：tree）是一种[抽象数据类型](https://zh.wikipedia.org/wiki/%E6%8A%BD%E8%B1%A1%E8%B3%87%E6%96%99%E5%9E%8B%E5%88%A5)（ADT）或是实作这种抽象数据类型的[数据结构](https://zh.wikipedia.org/wiki/%E8%B3%87%E6%96%99%E7%B5%90%E6%A7%8B)，用来模拟具[有树状结构](https://zh.wikipedia.org/wiki/%E6%A8%B9%E7%8B%80%E7%B5%90%E6%A7%8B)性质的数据集合。它是由n（n>=1）个有限节点组成一个具有层次关系的[集合](https://zh.wikipedia.org/wiki/%E9%9B%86%E5%90%88)。把它叫做“树”是因为它看起来像一棵倒挂的树，也就是说它是根朝上，而叶朝下的。它具有以下的特点：
>
> - 每个节点有零个或多个子节点；
> - 没有父节点的节点称为根节点；
> - 每一个非根节点有且只有一个父节点；
> - 除了根节点外，每个子节点可以分为多个不相交的子树；

树的种类：

> **无序树：**树中任意节点的子节点之间没有顺序关系，这种树称为无序树，也称为[自由树](https://zh.wikipedia.org/wiki/%E8%87%AA%E7%94%B1%E6%A0%91)；
>
> **有序树：**树中任意节点的子节点之间有顺序关系，这种树称为有序树；
>
> - [二叉树](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%8F%89%E6%A0%91)：每个节点最多含有两个子树的树称为二叉树；
> - [完全二叉树](https://zh.wikipedia.org/wiki/%E5%AE%8C%E5%85%A8%E4%BA%8C%E5%8F%89%E6%A0%91)：对于一颗二叉树，假设其深度为d（d>1）。除了第d层外，其它各层的节点数目均已达最大值，且第d层所有节点从左向右连续地紧密排列，这样的二叉树被称为完全二叉树；
> - [满二叉树](https://zh.wikipedia.org/w/index.php?title=%E6%BB%A1%E4%BA%8C%E5%8F%89%E6%A0%91&action=edit&redlink=1)：所有叶节点都在最底层的完全二叉树；
> - [平衡二叉树](https://zh.wikipedia.org/wiki/%E5%B9%B3%E8%A1%A1%E4%BA%8C%E5%8F%89%E6%A0%91)（[AVL树](https://zh.wikipedia.org/wiki/AVL%E6%A0%91)）：当且仅当任何节点的两棵子树的高度差不大于1的二叉树；
> - [排序二叉树](https://zh.wikipedia.org/w/index.php?title=%E6%8E%92%E5%BA%8F%E4%BA%8C%E5%8F%89%E6%A0%91&action=edit&redlink=1)(二叉查找树（英语：Binary Search Tree），也称二叉搜索树、有序二叉树)；
> - [霍夫曼树](https://zh.wikipedia.org/wiki/%E9%9C%8D%E5%A4%AB%E6%9B%BC%E6%A0%91)：[带权路径](https://zh.wikipedia.org/w/index.php?title=%E5%B8%A6%E6%9D%83%E8%B7%AF%E5%BE%84&action=edit&redlink=1)最短的二叉树称为哈夫曼树或最优二叉树；
> - [B树](https://zh.wikipedia.org/wiki/B%E6%A0%91)：一种对读写操作进行优化的自平衡的二叉查找树，能够保持数据有序，拥有多余两个子树。

有关树的术语:

> 1. **节点的度**：一个节点含有的子树的个数称为该节点的度；
> 2. **树的度**：一棵树中，最大的节点的度称为树的度；
> 3. **叶节点**或**终端节点**：度为零的节点；
> 4. **非终端节点**或**分支节点**：度不为零的节点；
> 5. **父亲节点**或**父节点**：若一个节点含有子节点，则这个节点称为其子节点的父节点；
> 6. **孩子节点**或**子节点**：一个节点含有的子树的根节点称为该节点的子节点；
> 7. **兄弟节点**：具有相同父节点的节点互称为兄弟节点；
> 8. 节点的**层次**：从根开始定义起，根为第1层，根的子节点为第2层，以此类推；
> 9. 树的**高度**或**深度**：树中节点的最大层次；
> 10. **堂兄弟节点**：父节点在同一层的节点互为堂兄弟；
> 11. **节点的祖先**：从根到该节点所经分支上的所有节点；
> 12. **子孙**：以某节点为根的子树中任一节点都称为该节点的子孙。
> 13. **森林**：由m（m>=0）棵互不相交的树的集合称为森林；

（我是维基百科搬运工，哈哈哈）

### 二叉树

>  **二叉树**（英语：Binary tree）是每个节点最多有两个子树的[树结构](https://zh.wikipedia.org/wiki/%E6%A0%91%E7%BB%93%E6%9E%84)。通常子树被称作“左子树”（*left subtree*）和“右子树”（*right subtree*）。[二叉树](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%85%83%E6%A8%B9)常被用于实现[二叉查找树](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%85%83%E6%90%9C%E5%B0%8B%E6%A8%B9)和[二元堆积](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%85%83%E5%A0%86%E7%A9%8D)。

我们主要研究的就是二叉树，也就是数据为一对二的关系。那么在二叉树中又有些分类；

![二叉树](https://wlqcev.coding-pages.com/2329205432280906653.jpg)

二叉树分类：

- 一棵深度为k，且有![{\displaystyle 2^{\begin{aligned}k+1\end{aligned}}-1}](https://wikimedia.org/api/rest_v1/media/math/render/svg/f24729d4eae59094b7ed114e09dcbf142f32cde8)个节点称之为**满二叉树**；
- 深度为k，有n个节点的[二叉树](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%85%83%E6%A8%B9)，当且仅当其每一个节点都与深度为k的[满二叉树](https://zh.wikipedia.org/w/index.php?title=%E6%BB%BF%E4%BA%8C%E5%85%83%E6%A8%B9&action=edit&redlink=1)中，序号为1至n的节点对应时，称之为**完全二叉树**。
- 平衡二叉树又被称为AVL树（区别于AVL算法），它是一棵二叉排序树，且具有以下性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵**平衡二叉树**。

### 二叉树的遍历

1）一棵二叉树由根结点、左子树和右子树三部分组成；
2） D、L、R 分别代表遍历根结点、遍历左子树、遍历右子树，则二叉树的；
3） 遍历方式有6 种：DLR、DRL、LDR、LRD、RDL、RLD。先左或先右算法基本一样，所以就剩下三种DLR（先序或是前序）、LDR（中序）、LRD（后序）。

-  **前序遍历**：首先访问根节点，然后遍历左子树，最后遍历右子树，可记录为根—左—右；


-  **中序遍历**：首先访问左子树，然后访问根节点，最后遍历右子树，可记录为左—根—右；


-  **后序遍历**：首先遍历左子树，然后遍历右子树，最后遍历根节点，可记录为左—右—根。

![二叉树的遍历](https://wlqcev.coding-pages.com/BINTREE2.jpg)

![二叉树遍历](https://wlqcev.coding-pages.com/2017-01-16_173441.png)

以上图1为例解释前序遍历：

首先访问根节点`a`=>然后遍历左子树`b`=>左子树`b`的左子树`d`=>`d`的右孩子`e`=>此时`b`的左子树遍历完，遍历`b`的右子树`f`=>`f`的左孩子`g`=>左子树`b`遍历完，遍历根节点的右孩子`c`，完成=>`abdefgc`

中序遍历，后序遍历就不多说了，不同的只是访问的顺序。

注意：

(1)已知前序、后序遍历结果，不能推导出一棵确定的树；

(2)已知前序、中序遍历结果，能够推导出后序遍历结果；

(3)已知后序、中序遍历结果，能够推导出前序遍历结果；

### 二叉搜索树的创建

二叉查找树（BinarySearch Tree，也叫二叉搜索树，或称二叉排序树Binary Sort Tree）或者是一棵空树，或者是具有下列性质的二叉树：

    （1）若它的左子树不为空，则左子树上所有结点的值均小于它的根结点的值；

    （2）若它的右子树不为空，则右子树上所有结点的值均大于它的根结点的值；

    （3）它的左、右子树也分别为二叉查找树。

首先我们声明一个BinarySearchTree类：

```js
function BinarySearchTree() {
    var Node = function(key){
        this.key = key;
        this.left = null;
        this.right = null;
    };
    var root=null;
}
```

![二叉树](https://wlqcev.coding-pages.com/binary6.gif)

和链表一样，二叉树也通过指针来表示节点之间的关系。在双向链表中，每一个节点有两个指针，一个指向下一个节点，一个指向上一个节点。对于树，使用同样的方式，只不过一个指向左孩子，一个指向右孩子。现在我们给这棵树弄一些方法：

- insert(key): 向树中插入一个新的键(节点);
- search(key): 在书中查找一个键，如果节点存在，返回true;如果不存在，返回false;
- inOrdertraverse(): 通过中序遍历方式遍历所有节点；
- preorderTraverse(): 通过先序遍历方式遍历所有的节点；
- postOrdertraverse(): 通过后序遍历的方式遍历所有的节点；
- min(): 返回树中的最小值；
- max(): 返回树中的最大值；
- remove(key): 从树中移除某个键；

BinarySearchTree类的完整代码（充分添加注释）：

```javascript
function BinarySearchTree() {
    var Node = function(key){
        this.key = key;
        this.left = null;
        this.right = null;
    };
    var root = null;
    this.insert = function(key){

        var newNode = new Node(key);

        //判断是否是第一个节点，如果是作为根节点保存。不是调用inserNode方法
        if (root === null){
            root = newNode;
        } else {
            insertNode(root,newNode);
        }
    };
    var insertNode = function(node, newNode){
      //判断两个节点的大小，根据二叉搜索树的特点左子树上所有结点的值均小于它的根结点的值，右子树上所有结点的值均大于它的根结点的值
        if (newNode.key < node.key){
            if (node.left === null){
                node.left = newNode;
            } else {
                insertNode(node.left, newNode);
            }
        } else {
            if (node.right === null){
                node.right = newNode;
            } else {
                insertNode(node.right, newNode);
            }
        }
    };
    this.getRoot = function(){
        return root;
    };
    this.search = function(key){
        return searchNode(root, key);
    };

    var searchNode = function(node, key){
        if (node === null){
            return false;
        }
        if (key < node.key){
            return searchNode(node.left, key);
        } else if (key > node.key){
            return searchNode(node.right, key);
        } else { //element is equal to node.item
            return true;
        }
    };
    this.inOrderTraverse = function(callback){
        inOrderTraverseNode(root, callback);
    };
    var inOrderTraverseNode = function (node, callback) {
        if (node !== null) {
            inOrderTraverseNode(node.left, callback);
            callback(node.key);
            inOrderTraverseNode(node.right, callback);
        }
    };
    this.preOrderTraverse = function(callback){
        preOrderTraverseNode(root, callback);
    };
    var preOrderTraverseNode = function (node, callback) {
        if (node !== null) {
            callback(node.key);
            preOrderTraverseNode(node.left, callback);
            preOrderTraverseNode(node.right, callback);
        }
    };
    this.postOrderTraverse = function(callback){
        postOrderTraverseNode(root, callback);
    };
    var postOrderTraverseNode = function (node, callback) {
        if (node !== null) {
            postOrderTraverseNode(node.left, callback);
            postOrderTraverseNode(node.right, callback);
            callback(node.key);
        }
    };
    this.min = function() {
        return minNode(root);
    };
    var minNode = function (node) {
        if (node){
            while (node && node.left !== null) {
                node = node.left;
            }

            return node.key;
        }
        return null;
    };
    this.max = function() {
        return maxNode(root);
    };
    var maxNode = function (node) {
        if (node){
            while (node && node.right !== null) {
                node = node.right;
            }

            return node.key;
        }
        return null;
    };
    this.remove = function(element){
        root = removeNode(root, element);
    };
    var findMinNode = function(node){
        while (node && node.left !== null) {
            node = node.left;
        }
        return node;
    };
    var removeNode = function(node, element){
        if (node === null){
            return null;
        }
        if (element < node.key){
            node.left = removeNode(node.left, element);
            return node;
        } else if (element > node.key){
            node.right = removeNode(node.right, element);
            return node;
        } else { 
            //处理三种特殊情况
            //1 - 叶子节点
            //2 - 只有一个孩子的节点
            //3 - 有两个孩子的节点
            //case 1
            if (node.left === null && node.right === null){
                node = null;
                return node;
            }
            //case 2
            if (node.left === null){
                node = node.right;
                return node;
            } else if (node.right === null){
                node = node.left;
                return node;
            }
            //case 3
            var aux = findMinNode(node.right);
            node.key = aux.key;
            node.right = removeNode(node.right, aux.key);
            return node;
        }
    };
}
```

## 后记

树是一种比较常见的数据结构，不管是考试还是日常编码或是面试都是没法避免的一个知识点，此篇总结不甚完善，纰漏之处还望指出方便之后更改。敬请期待数据结构篇最后一篇文章：[学习javascript数据结构（五）——图]

### 参考文章

- [树（数据结构）](https://zh.wikipedia.org/wiki/树_(数据结构))
- [二叉树](https://zh.wikipedia.org/wiki/%E4%BA%8C%E5%8F%89%E6%A0%91#.E4.BA.8C.E5.8F.89.E6.A0.91.E7.9A.84.E7.B1.BB.E5.9E.8B)

---

能力有限，水平一般，欢迎勘误，不胜感激。

转载请获本人授权，并注明作者和出处。

订阅更多文章可关注公众号「前端进阶学习」，回复「666」，获取一揽子前端技术书籍

![前端进阶学习](https://image.damonare.cn/qianduanjinjie.png)