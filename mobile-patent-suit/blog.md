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
  ...
]
```

可以看到, 每一条数据都是由以下几部分组成:

- `source` : 诉讼方的公司名称
- `target` : 被诉讼方的公司名称
- `type` : 当前诉讼状态

需要注意的是: 有一些公司 (如 **Apple**, **Microsoft** ) 同时参与了多起诉讼案件, 但我们在数据可视化时只会为每一个公司分配一个节点, 然后用连线表示各个公司之间的关系.

数据可视化最重要的就是数据和图像之间的映射关系, 本例中我们的可视化的逻辑为:

- 将每一个**公司**作为图中的一个**圆形节点**
- 将每一条**诉讼关系**表示为两个圆形节点之间的**连线**

**公司 ==> 圆形节点**

![公司  ==>  圆形节点](https://raw.githubusercontent.com/ssthouse/d3-blog/master/mobile-patent-suit/img/company_as_dot.png)

**诉讼关系 ==> 连线**

![公司  ==>  圆形节点](https://raw.githubusercontent.com/ssthouse/d3-blog/master/mobile-patent-suit/img/line_as_suit.png)

### 技术分析

要实现可以拖动, 自动分散开的网络结构图, 本 demo 用到了 **D3.js** 中的 **d3-force** 和 **d3-drag** , 当然还有最基础的 **d3-selection**.

为了方便搭建用户界面, 使用了 **Vue** 作为前端框架. 但 Vue 并不对数据可视化逻辑产生影响, 不使用也不会对我们的实现造成影响.

### 代码实现

现在让我们进入代码部分, 首先我们画出每个公司代表的圆形节点:

上面说到了, 原始数据中, 有部分公司多次出现在不同的诉讼关系中, 而我们要为每个公司画出唯一的节点, 所以我们要对数据进行一些处理:

```javascript
  initData() {
    this.links = [
      { source: 'Microsoft', target: 'Amazon', type: 'licensing' },
      { source: 'Microsoft', target: 'HTC', type: 'licensing' },
      { source: 'Samsung', target: 'Apple', type: 'suit' },
      { source: 'Motorola', target: 'Apple', type: 'suit' },
      { source: 'Nokia', target: 'Apple', type: 'resolved' },
      ...
    ] // 这里省略了一些数据

    this.nodes = {}

    // Compute the distinct nodes from the links.
    this.links.forEach(link => {
      link.source =
        this.nodes[link.source] ||
        (this.nodes[link.source] = { name: link.source })
      link.target =
        this.nodes[link.target] ||
        (this.nodes[link.target] = { name: link.target })
    })
    console.log(this.links)
  }
```

上面这段代码的逻辑是, 遍历所有的links, 将其中的 source 和 target 作为key 放置到 nodes中, 这样我们就得到了不含重复节点的数据 nodes:

![公司  ==>  圆形节点](https://raw.githubusercontent.com/ssthouse/d3-blog/master/mobile-patent-suit/img/nodes_in_console.png)



```javascript
this.circle = this.svgNode
  .append('g')
  .selectAll('circle')
  .data(this.d3.values(this.nodes))
  .enter()
  .append('circle')
  .attr('r', 10)
  .style('cursor', 'pointer')
  .call(this.enableDragFunc())
```
