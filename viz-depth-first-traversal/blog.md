# 可视化讲解 深度优先遍历(DFT)

深度优先遍历, 刷过题的朋友应该都很熟悉了,难是不难,但是理解起来还是要费一些功夫的.
深度优先遍历的实现方法有递归和非递归两种, 这里我们用可视化的角度,讲解前一种: 递归的深度优先遍历

为什么要以可视化的方式来讲解呢? 因为人是视觉的动物, 如果和你说 `二叉树` 或 `栈` , 相信大家脑中出现的都是下面的图形:

![binary tree](https://raw.githubusercontent.com/ssthouse/d3-blog/master/viz-depth-first-traversal/img/binary-tree.png)

![stack](https://raw.githubusercontent.com/ssthouse/d3-blog/master/viz-depth-first-traversal/img/stack.png)

而不是下面的代码:

```javascript
// binary tree
class Node {
  constructor(value, leftChild, rightChild) {
    this.value = value
    this.leftChild = leftChild
    this.rightChild = rightChild
  }
}

// stack
const stack = new Array()
```

所以说, 人是视觉动物, 以图形可视化的方式来讲解问题往往能讲解的更清楚, 这也就是我写本文的缘由.

## 效果展示

为了可视化的讲解 `深度优先遍历算法`, 笔者写了一个简单的网页, 实现的功能有:

- 用户可编辑进行深度遍历的二叉树
- 网页上给出了 JavaScript 版本的实现
- 点击 `Start DFT` 按钮, 用户将看到算法中用到的 **二叉树** 和 **栈** 都将动态 的展示再页面中,可以直观的看到代码运行过程中,数据的变化

## 深度搜索简介

## 可视化思路

## 代码讲解
