### Github Repository 可视化 (D3.js & Three.js)

[demo 链接](https://ssthouse.github.io/github-visualization/)
[github 链接](https://github.com/ssthouse/github-visualization)

效果图 2D:
![demo 2d](https://raw.githubusercontent.com/ssthouse/d3-blog/master/github-visualization/img/visual-github-repo.gif)

// TODO not completed yet!
效果图 3D:
![demo 3d]()

### 为什么要做这样一个网站?

最初想法是因为 github 提供的 repository 查看的页面无法一次看到用户的所有 repository, 也无法直观的看到每个 repository 的量级对比(如 **commit** 数, **star** 数)

所以希望做一个能直观展示用户所有 repository 的网站.

### 实现的功能有哪些?

2D 和 3D 版本均支持:

- 展示用户的 Repository 可视化效果
- 点击 _following people_ 的头像查看他人的 Repository 可视化效果

其中 2D 可视化支持页面缩放和拖拽 & 单个 Repository 的缩放和拖拽, 3D 可视化仅支持页面的缩放和拖拽.

### 用到了哪些技术?

- 数据来源为 Github 提供的 _GraphQL API._
- 2D 实现使用到了 _D3.js_
- 3D 实现使用到了 _Three.js_
- 页面搭建使用 _Vue.js_

### 实现细节?

#### 2D 实现

2D 效果图中, 每一个 Repository 用一个圆形表示, 圆形的大小代表了 _commit 数目 || start 数目 || fork 数目_

布局使用的是 **d3-layout** 中的 **forceLayout**, 达到模拟物理碰撞的效果. 拖拽用到了 **d3-drag** 模块, 大致逻辑为:

==> 检测鼠标拖拽事件

==> 更新 UI 元素坐标

==> 重新计算 forceLayout 的位置

==> 更新 UI 来达到圆形单独拖拽的效果.

##### 让我们来看看具体代码:

2D 页面依赖 D3.js 的 force-layout 进行动态更新, 我们为 force-layout 添加了以下几种 **force**(作用力):

- `.force('charge', this.$d3.forceManyBody())` 添加节点之间的相互作用力
- `.force('collide',radius)` 添加物理碰撞, 半径设置为圆形的半径
- `.force('forceX', this.$d3.forceX(this.width / 2).strength(0.05))` 横坐标居中的作用力
- `.force('forceY', this.$d3.forceY(this.height / 2).strength(0.05))` 纵坐标居中的作用力

主要代码如下:

```javascript
this.simulation = this.$d3
  .forceSimulation(this.filteredRepositoryList)
  .force('charge', this.$d3.forceManyBody())
  .force(
    'collide',
    this.$d3.forceCollide().radius(d => this.areaScale(d.count) + 3)
  )
  .force('forceX', this.$d3.forceX(this.width / 2).strength(0.05))
  .force('forceY', this.$d3.forceY(this.height / 2).strength(0.05))
  .on('tick', tick)
```

最后一行 `.on('tick', tick)` 为 force-layout simulation 的回调方法, 该方法会在物理引擎更新的每个周期被调用, 我们可以在这个回调方法中更新页面, 以达到动画效果.

我们在这个 `tick` 回调中要做的就是: 刷新 svg 中 **circle** 和 html 的**span** 的坐标. 具体代码如下.
如果用过 D3.js 的同学应该很熟悉这段代码了, 就是使用 d3-selection 对 DOM 元素的 `enter(), update(), exit()` 三种状态的简单控制.

这里需要注意的一点是, 我们没有使用 svg 的 **text** 元素来实现文字而是使用了 html 的 **span**, 目的是更好的控制文字换行.

```javascript
const tick = function() {
  const curTransform = self.$d3.zoomTransform(self.div)
  self.updateTextLocation()
  const texts = self.div.selectAll('span').data(self.filteredRepositoryList)
  texts
    .enter()
    .append('span')
    .merge(texts)
    .text(d => d.name)
    .style('font-size', d => self.textScale(d.count) + 'px')
    .style(
      'left',
      d =>
        d.x +
        self.width / 2 -
        ((self.areaScale(d.count) * 1.5) / 2.0) * curTransform.k +
        'px'
    )
    .style(
      'top',
      d => d.y - (self.textScale(d.count) / 2.0) * curTransform.k + 'px'
    )
    .style('width', d => self.areaScale(d.count) * 1.5 + 'px')
  texts.exit().remove()

  const repositoryCircles = self.g
    .selectAll('circle')
    .data(self.filteredRepositoryList)
  repositoryCircles
    .enter()
    .append('circle')
    .append('title')
    .text(d => 'commit number: ' + d.count)
    .merge(repositoryCircles)
    .attr('cx', d => d.x + self.width / 2)
    .attr('cy', d => d.y)
    .attr('r', d => self.areaScale(d.count))
    .style('opacity', d => self.alphaScale(d.count))
    .call(self.enableDragFunc())
  repositoryCircles.exit().remove()
}
```

完成以上的逻辑后, 就能看到 2D 初始加载数据时的效果了:
![enter view](https://raw.githubusercontent.com/ssthouse/d3-blog/master/github-visualization/img/enter-view.gif)

但此时页面中的 圆圈 (circle)还不能响应鼠标拖拽事件, 让我们使用 d3-drag 加入鼠标拖拽功能.
代码非常简单, 使用 d3-drag 处理 `start, drag, end` 三个鼠标事件即可:

- **start & drag** ==> 将当前节点的 `fx, fy` (即 forceX, forceY, 设置这两个值会让 force-layout 添加作用力将该节点移动到 `fx, fy`)
- **end** ==> 拖拽事件结束, 清空选中节点的 `fx, fy`,

```javascript
enableDragFunc() {
      const self = this
      this.updateTextLocation = function() {
        self.div
          .selectAll('span')
          .data(self.repositoryList)
          .each(function(d) {
            const node = self.$d3.select(this)
            const x = node.style('left')
            const y = node.style('top')
            node.style('transform-origin', '-' + x + ' -' + y)
          })
      }
      return this.$d3
        .drag()
        .on('start', d => {
          if (!this.$d3.event.active) this.simulation.alphaTarget(0.3).restart()
          d.fx = this.$d3.event.x
          d.fy = this.$d3.event.y
        })
        .on('drag', d => {
          d.fx = this.$d3.event.x
          d.fy = this.$d3.event.y
          self.updateTextLocation()
        })
        .on('end', d => {
          if (!this.$d3.event.active) this.simulation.alphaTarget(0)
          d.fx = null
          d.fy = null
        })
    },
```

需要注意的是,我们在 drag 的回调方法中,调用了 `updateTextLocation()`, 这是因为我们的 drag 事件将会被应用到 circle 上, 而 text 不会自动更新坐标, 所以需要我们去手动更新.
接下来,我们将 d3-drag 应用到 circle 上:

```javascript
const repositoryCircles = self.g
  .selectAll('circle')
  .data(self.filteredRepositoryList)
repositoryCircles
  .enter()
  .append('circle')
  .append('title')
  .text(d => 'commit number: ' + d.count)
  .merge(repositoryCircles)
  .attr('cx', d => d.x + self.width / 2)
  .attr('cy', d => d.y)
  .attr('r', d => self.areaScale(d.count))
  .style('opacity', d => self.alphaScale(d.count))
  .call(self.enableDragFunc()) // add d3-drag function
repositoryCircles.exit().remove()
```

现在我们便实现了拖拽效果:
![enter view](https://raw.githubusercontent.com/ssthouse/d3-blog/master/github-visualization/img/drag-effect.gif)

最后让我们加上 2D 界面的缩放功能, 这里使用的是 **d3-zoom**.和 d3-drag 类似, 我们只用处理鼠标滚轮缩放的回调事件即可:

```javascript
enableZoomFunc() {
  const self = this
  this.zoomFunc = this.$d3
    .zoom()
    .scaleExtent([0.5, 10])
    .on('zoom', function() {
      self.g.attr('transform', self.$d3.event.transform)
      self.div
        .selectAll('span')
        .data(self.repositoryList)
        .each(function(d) {
          const node = self.$d3.select(this)
          const x = node.style('left')
          const y = node.style('top')
          node.style('transform-origin', '-' + x + ' -' + y)
        })
      self.div
        .selectAll('span')
        .data(self.repositoryList)
        .style(
          'transform',
          'translate(' +
            self.$d3.event.transform.x +
            'px,' +
            self.$d3.event.transform.y +
            'px) scale(' +
            self.$d3.event.transform.k +
            ')'
        )
    })
  this.g.call(this.zoomFunc)
}
```

同样的, 因为 span 不是 svg 元素, 我们需要手动更新缩放和坐标. 这样我们便实现了鼠标滚轮的缩放功能.
![zoom effect](https://raw.githubusercontent.com/ssthouse/d3-blog/master/github-visualization/img/zoom.gif)

以上便是 2D 效果实现的主要逻辑.

##### 3D 实现

3D 效果图中的布局使用的是 d3-layout 中的 pack layout, 3D 场景中的拖拽合缩放直接使用了插件:
