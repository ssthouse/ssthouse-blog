### Github Repository 可视化 (D3.js & Three.js)

先上 Demo 链接 & 效果图
[demo 链接](https://ssthouse.github.io/github-visualization/)
[github 链接](https://github.com/ssthouse/github-visualization)

效果图 2D:
![demo 2d](https://user-gold-cdn.xitu.io/2018/10/17/16681a0c00933e0b?w=1309&h=836&f=gif&s=4707231)

效果图 3D:
![demo 3d](https://user-gold-cdn.xitu.io/2018/10/17/16681a0c00a1e0ce?w=949&h=836&f=gif&s=4248026)

### 为什么要做这样一个网站?

最初想法是因为 github 提供的页面无法一次看到用户的所有 repository, 也无法直观的看到每个 repository 的量级对比(如 **commit** 数, **star** 数),

所以希望做一个能直观展示用户所有 repository 的网站.

### 实现的功能有哪些?

用户 Github Repository 数据的**2D**和**3D**展示, 点击用户 github 关注用户的头像, 可以查看他人的 Github Repository 展示效果.

2D 和 3D 版本均支持:

- 展示用户的 Repository 可视化效果
- 点击 _following people_ 的头像查看他人的 Repository 可视化效果

其中 2D 视图支持页面缩放和拖拽 && 单个 Repository 的缩放和拖拽, 3D 视图仅支持页面的缩放和拖拽.

### 用到了哪些技术?

- 数据来源为 Github 提供的 _GraphQL API._
- 2D 实现使用到了 _D3.js_
- 3D 实现使用到了 _Three.js_
- 页面搭建使用 _Vue.js_

### 实现细节?

#### 2D 实现

2D 效果图中, 每一个 Repository 用一个圆形表示, 圆形的大小代表了 _commit 数目 || start 数目 || fork 数目_.

布局使用的是 **d3-layout** 中的 **forceLayout**, 达到模拟物理碰撞的效果. 拖拽用到了 **d3-drag** 模块, 大致逻辑为:

==> 检测鼠标拖拽事件

==> 更新 UI 元素坐标

==> 重新计算布局坐标

==> 更新 UI 来达到圆形可拖拽的效果.

##### 让我们来看看具体代码:

2D 页面依赖 D3.js 的 force-layout 进行动态更新, 我们为 force-layout 添加了以下几种 **force**(作用力):

- `.force('charge', this.$d3.forceManyBody())` 添加节点之间的相互作用力
- `.force('collide',radius)` 添加物理碰撞, 半径设置为圆形的半径
- `.force('forceX', this.$d3.forceX(this.width / 2).strength(0.05))` 添加横坐标居中的作用力
- `.force('forceY', this.$d3.forceY(this.height / 2).strength(0.05))` 添加纵坐标居中的作用力

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

我们在这个 `tick` 回调中要完成的任务是: 刷新 **svg** 中 **circle** 和 **html** 的**span** 的坐标. 具体代码如下.
如果用过 D3.js 的同学应该很熟悉这段代码了, 就是使用 d3-selection 对 DOM 元素 `enter(), update(), exit()` 三种状态进行的简单控制.

这里需要注意的一点是, 我们没有使用 **svg** 的 **text** 元素来实现文字而是使用了 **html** 的 **span**, 目的是更好的控制文字换行.

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
![enter view](https://user-gold-cdn.xitu.io/2018/10/17/166819ff66d6c860?w=1116&h=836&f=gif&s=2621628)

但此时页面中的 圆圈 (circle)还不能响应鼠标拖拽事件, 让我们使用 d3-drag 加入鼠标拖拽功能.
代码非常简单, 使用 d3-drag 处理 `start, drag, end` 三个鼠标事件的回调即可:

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

如此我们便实现了拖拽效果:
![enter view](https://user-gold-cdn.xitu.io/2018/10/17/166819ff66fbe9ac?w=1116&h=836&f=gif&s=2330901)

最后让我们加上 2D 界面的缩放功能, 这里使用的是 **d3-zoom**. 和 d3-drag 类似, 我们只用处理鼠标滚轮缩放的回调事件即可:

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
![zoom effect](https://user-gold-cdn.xitu.io/2018/10/17/166819ff66fc3bde?w=1116&h=836&f=gif&s=1276719)

以上便是 2D 效果实现的主要逻辑.

#### 3D 实现

3D 效果图中的布局使用的是 d3-layout 中的 pack layout, 3D 场景中的拖拽合缩放直接使用了插件 **three-orbit-controls**.

##### 让我们来看看具体代码

##### 创建基本 3D 场景

3D 视图中, 承载所有 UI 组件的是 Three.js 中的 Scene,首先我们初始化 **Scene**.

```javascript
this.scene = new THREE.Scene()
```

接下来我们需要一个 **Render**(渲染器)来将 Scene 中的画面渲染到 Web 页面上:

```javascript
this.renderer = new THREE.WebGLRenderer({ alpha: true, antialias: true })
this.renderer.setClearColor(0xeeeeee, 0.3)
var contaienrElement = document.getElementById(this.containerId)
contaienrElement.appendChild(this.renderer.domElement)
```

然后我们需要加入 **Light**, 对 Three.js 了解过的同学应该很容易理解, 我们需要 Light 来照亮场景中的物体, 否则我们看到就是一片漆黑.

```javascript
// add light
var light = new THREE.AmbientLight(0x404040, 1) // soft white light
this.scene.add(light)
var spotLight = new THREE.DirectionalLight(0xffffff, 0.7)
spotLight.position.set(0, 0, 200)
spotLight.lookAt(0, 0, 0)
this.scene.add(spotLight)
```

最后我们需要加入 **Camera**. 我们最终看到的 Scene 的样子就是从 Camera 的角度看到的样子. 我们使用 render 来将 Scene 从 Camera 看到的样子渲染出来:

```javascript
this.renderer.render(this.scene, this.camera)
```

但是这样子我们只是渲染了一次页面, 当 Scene 中的物体发生变化时, Web 页面上的 Canvas 并不会自动更新, 所以我们使用 `requestAnimationFrame` 这个 api 来实时刷新 Canvas.

```javascript
  animate_() {
    requestAnimationFrame(() => this.animate_())
    this.controls.update()
    this.renderer.render(this.scene, this.camera)
  }
```

##### 实现布局

为了实现和 2D 视图中类似的布局效果, 我们使用了 D3 的 pack-layout, 其效果是实现嵌套式的圆形布局效果. 类似下图:
![Pack Layout](https://user-gold-cdn.xitu.io/2018/10/17/166819ff66eea24d?w=461&h=298&f=png&s=13642)

这里我们只是想使用这个布局, 但是我们本身的数据不是嵌套式的, 所以我们手动将其包装一层, 使其变为嵌套的数据格式:

```javascript
{
  "children": this.reporitoryList
}
```

然后我们调用 D3 的**pack-layout**:

```javascript
calcluate3DLayout_() {
  const pack = D3.pack()
    .size([this.layoutSize, this.layoutSize])
    .padding(5)
  const rootData = D3.hierarchy({
    children: this.reporitoryList
  }).sum(d => Math.pow(d.count, 1 / 3))
  this.data = pack(rootData).leaves()
}
```

这样, 我们就完成了布局. 在控制台从查看 `this.data`, 我们就能看到每个节点的 `x, y`属性.

##### 创建表示 Repository 的球体

这里我们使用 **THREE.SphereGeometry** 来创建球体, 球体的材质我们使用 **new THREE.MeshNormalMaterial()**. 这种材质的效果是, 我们从任何角度来看球体, 其四周颜色都是不变的.如图:
![Normal Material](https://user-gold-cdn.xitu.io/2018/10/17/166819ffb47cefbf?w=1008&h=503&f=png&s=117919)

```javascript
addBallsToScene_() {
  const self = this
  if (!this.virtualElement) {
    this.virtualElement = document.createElement('svg')
  }
  this.ballMaterial = new THREE.MeshNormalMaterial()
  const circles = D3.select(this.virtualElement)
    .selectAll('circle')
    .data(this.data)
  circles
    .enter()
    .merge(circles)
    .each(function(d, i) {
      const datum = D3.select(this).datum()
      self.ballGroup.add(
        self.generateBallMesh_(
          self.indexScale(datum.x),
          self.indexScale(datum.y),
          self.volumeScale(datum.r),
          i
        )
      )
    })
}

generateBallMesh_(xIndex, yIndex, radius, name) {
  var geometry = new THREE.SphereGeometry(radius, 32, 32)
  var sphere = new THREE.Mesh(geometry, this.ballMaterial)
  sphere.position.set(xIndex, yIndex, 0)
  return sphere
}
```

需要注意的是, 这里我们把所有的球体放置在 ballGroup 中, 并把 ballGroup 放置到 Scene 中, 这样便于管理所有的球体(比如清空所有球体).

##### 创建表示 Repository 名称的 文字物体

在一开始开发时, 我直接为每一个 Repository 的文字创建一个 **TextGeometry**, 结果 3D 视图加载非常缓慢. 后来经过四处搜索,终于在 Three.js 的 一个 github issue 里面的找到了比较好的解决方案:
将 26 个英文字母分别创建 **TextGeometry**, 然后在创建每一个单词时, 使用现有的 26 个字母的 TextGeometry 拼接出单词, 这样就可以大幅节省创建 TextGeometry 的时间.
讨论该 issue 的链接如下:

> github issue: https://github.com/mrdoob/three.js/issues/1825

示例代码如下:

```javascript
// 事先将26个字母创建好 TextGeometry
loadAlphabetGeoMap() {
  const fontSize = 2.4
  this.charGeoMap = new Map()
  this.charWidthMap = new Map()
  const chars =
    '1234567890abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ_-./?'
  chars.split('').forEach(char => {
    const textGeo = new THREE.TextGeometry(char, {
      font: this.font,
      size: fontSize,
      height: 0.04
    })
    textGeo.computeBoundingBox()
    const width = textGeo.boundingBox.max.x - textGeo.boundingBox.min.x
    this.charGeoMap.set(char, textGeo)
    this.charWidthMap.set(char, width)
  })
  console.log(this.charGeoMap)
}

// 创建整个单词时直接使用现有字母的 TextGeometry进行拼接
addTextWithCharGroup(text, xIndex, yIndex, radius) {
  const group = new THREE.Group()
  const chars = text.split('')

  let totalLen = 0
  chars.forEach(char => {
    if (!this.charWidthMap.get(char)) {
      totalLen += 1
      return
    }
    totalLen += this.charWidthMap.get(char)
  })
  const offset = totalLen / 2

  for (let i = 0; i < chars.length; i++) {
    const curCharGeo = this.charGeoMap.get(chars[i])
    if (!curCharGeo) {
      xIndex += 2
      continue
    }
    const curMesh = new THREE.Mesh(curCharGeo, this.textMaterial)
    curMesh.position.set(xIndex - offset, yIndex, radius + 2)
    group.add(curMesh)
    xIndex += this.charWidthMap.get(chars[i])
  }
  this.textGroup.add(group)
}
```

需要注意的是该方法仅适用于英文, 如果是汉字的话, 我们是无法事先创建所有汉字的 TextGeometry 的, 这方面我暂时也还没找到合适的解决方案.

如上, 我们便完成了 3D 视图的搭建, 效果如图:
![3D effect](https://user-gold-cdn.xitu.io/2018/10/17/16681b4638cad4eb?w=950&h=836&f=gif&s=8484168)

## 想了解更多 D3.js 和 数据可视化 ?

这里是我的 _D3.js_ 、 _数据可视化_ 的 github 地址, 欢迎 star & fork :tada:

[D3-blog](https://github.com/ssthouse/d3-blog)

## 如果觉得本文不错的话, 不妨点击下面的链接关注一下 : )

[github 主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)
