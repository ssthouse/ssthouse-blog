# 用 D3.js 画一个手机专利关系图, 看看苹果,三星,微软间的专利纠葛

## 前言

本文灵感来源于[Mike Bostock](https://bost.ocks.org/mike/) 的一个 [demo 页面](http://bl.ocks.org/mbostock/1153292)

原 demo 基于 D3.js v3 开发, 笔者将其使用 D3.js v5 进行重写, 并改为使用新版的 ES6 语法.

源码: [github](https://github.com/ssthouse/github-visualization)

在线演示 : [demo](https://ssthouse.github.io/visual-explain/#/list/patent-suit)

## 效果

![demo](https://raw.githubusercontent.com/ssthouse/d3-blog/master/mobile-patent-suit/img/demo.gif)

可以看到, 上图左上角为图例, 中间为各个手机公司之间的专利关系图.

图例中有三种线段:

- **红色实线**: 正在进行专利诉讼 (箭头指向方为被诉讼方)
- **蓝色虚线**: 诉讼已经结束
- **绿色实线**: 专利已经授权

## 实现

下面让我们看看如何一步步实现上图的效果.

### 分析数据

```javascript
 [
      { source: 'Microsoft', target: 'Amazon', type: 'licensing' },
      { source: 'Microsoft', target: 'HTC', type: 'licensing' },
      { source: 'Samsung', target: 'Apple', type: 'suit' },
      { source: 'Motorola', target: 'Apple', type: 'suit' },
      { source: 'Nokia', target: 'Apple', type: 'resolved' },
      { source: 'HTC', target: 'Apple', type: 'suit' },
      { source: 'Kodak', target: 'Apple', type: 'suit' },
      { source: 'Microsoft', target: 'Barnes & Noble', type: 'suit' },
      { source: 'Microsoft', target: 'Foxconn', type: 'suit' },
      { source: 'Oracle', target: 'Google', type: 'suit' },
      { source: 'Apple', target: 'HTC', type: 'suit' },
      { source: 'Microsoft', target: 'Inventec', type: 'suit' },
      { source: 'Samsung', target: 'Kodak', type: 'resolved' },
      { source: 'LG', target: 'Kodak', type: 'resolved' },
      { source: 'RIM', target: 'Kodak', type: 'suit' },
      { source: 'Sony', target: 'LG', type: 'suit' },
      { source: 'Kodak', target: 'LG', type: 'resolved' },
      { source: 'Apple', target: 'Nokia', type: 'resolved' },
      { source: 'Qualcomm', target: 'Nokia', type: 'resolved' },
      { source: 'Apple', target: 'Motorola', type: 'suit' },
      { source: 'Microsoft', target: 'Motorola', type: 'suit' },
      { source: 'Motorola', target: 'Microsoft', type: 'suit' },
      { source: 'Huawei', target: 'ZTE', type: 'suit' },
      { source: 'Ericsson', target: 'ZTE', type: 'suit' },
      { source: 'Kodak', target: 'Samsung', type: 'resolved' },
      { source: 'Apple', target: 'Samsung', type: 'suit' },
      { source: 'Kodak', target: 'RIM', type: 'suit' },
      { source: 'Nokia', target: 'Qualcomm', type: 'suit' }
    ]
```

每一条数据都是由以下几部分组成:

- `source`  :  诉讼方的公司名称
- `target` : 被诉讼方的公司名称
- `type` : 当前诉讼状态

需要注意的是: 有一些公司 (如 **Apple**, **Microsoft** ) 同时参与了多起诉讼案件, 但我们在数据可视化时只会为每一个公司分配一个节点, 然后用连线表示各个公司之间的关系.

数据可视化最重要的就是数据和图像之间的映射关系, 本例中我们的可视化的逻辑为: 

- 将每一个公司作为图中的一个**圆形节点**
- 将每一条诉讼关系表示为两个圆形节点之间的**连线**



