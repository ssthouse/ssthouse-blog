# [译] D3.js 之 d3-shap 简介

## 译者注

> 原文: 来自 D3.js 作者 Mike Bostock 的 [Introducing d3-shape](https://medium.com/@mbostock/introducing-d3-shape-73f8367e6d12)

> 译者: [ssthouse](https://github.com/ssthouse)
> 联系译者: 邮箱(ssthouse@163.com) & 微信(wssst123456789)

## 译文

假设你现在想创建一个用于学习特定数据集的工具, 你最容易想到的呈现方式是什么呢? 一个可以自定义的 chart? 一个抽象的坐标系统? 将数据编码成图像来表示?

每一种方法都有它的好处. 比如你想做探索性的数据可视化, 你可能会更偏向于快速(高效)的方法, 因为这样你可以快速的测试各种不同的可视化效果.
如果是做讲解性质的数据可视化, 你可能会选择能精细控制的方法, 以让你的观众能跟精准的理解你的意图.

不管你选择的哪种方法, 你都最终需要将实际的图形画在屏幕上, 也就是说你需要用你的数据生成一些能代表这些数据的图形.

那么, 你打算如何画出图形呢?

一些简单的图形, 比如柱状图, 通过 canvas的api就可以轻松的画出: [fillRect](https://www.w3.org/TR/2dcontext/#dom-context-2d-fillrect)

