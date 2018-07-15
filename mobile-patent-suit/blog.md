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

(为了方便搭建用户界面, 使用了 **Vue** 作为前端框架. 但 Vue 并不对数据可视化逻辑产生影响, 不使用也不会对我们的实现造成影响.)

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

细心的读者可能已经发现了, 上面的数据中有许多 x, y 的坐标数据, 这些数据是从哪里来的呢?  答案就是 d3-force, 因为我们要实现的是模拟物理作用力的分布图, 所以我们使用了 d3-force 来模拟并帮助我们计算出每个节点的位置, 调用方法如下:

```javascript
    this.force = this.d3
      .forceSimulation(this.d3.values(this.nodes))
      .force('charge', this.d3.forceManyBody().strength(50))
      .force('collide', this.d3.forceCollide().radius(50))
      .force('link', forceLink)
      .force(
        'center',
        this.d3
          .forceCenter()
          .x(width / 2)
          .y(height / 2)
      )
      .on('tick', () => {
        if (this.path) {
          this.path.attr('d', this.linkArc)
          this.circle.attr('transform', transform)
          this.text.attr('transform', transform)
        }
      })
```



这里我们为d3-force添加了三种作用力:

- `.force('charge', this.d3.forceManyBody().strength(50))` 为每个节点添加互相之间的吸引力
- `.force('collide', this.d3.forceCollide().radius(50))`  为每个节点添加刚体碰撞效果
- `.force('link', forceLink)`  添加节点之间的连接力

执行上面的代码后, d3-force 就会为每一个节点计算好坐标并将其 作为 x, y 属性赋予每个节点.



处理好了数据, 让我们将其映射到页面上的 **svg** ==> **circle** 元素:

```javascript
this.circle = this.svgNode           // svgNode 为页面中的 svg节点 (d3.select('svg'))
  .append('g')
  .selectAll('circle')
  .data(this.d3.values(this.nodes))  // d3.values() 将对象数据 Object{}转换为数组数据 Array[]
  .enter()
  .append('circle')
  .attr('r', 10)
  .style('cursor', 'pointer')
  .call(this.enableDragFunc())
```



注意到这里我们在最后调用了 `.call(this.enableDragFunc())` , 这点代码是为了实现circle节点的拖拽功能, 我们在后面再进一步讲解.

上面这段代码逻辑为: 将 nodes 数据映射为 circle元素, 并设置circle元素的属性:

- 半径 10
- 鼠标悬停图标为手指
- 将每个node的 x, y 属性赋予 circle的 x, y (˙这一步我们在代码中没有声明, 是因为d3默认会将数据的 x, y属性作为circle的x, y 属性)

执行以上代码后的效果:



