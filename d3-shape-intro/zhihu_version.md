

## 译者注

> 原文: 来自 D3.js 作者 Mike Bostock 的 [Introducing d3-shape](https://medium.com/@mbostock/introducing-d3-shape-73f8367e6d12)

> 译者: [ssthouse](https://github.com/ssthouse)
> 联系译者: 邮箱(ssthouse@163.com) & 微信(wssst123456789)

## 译文

假设你现在想创建一个用于学习特定数据集的工具, 你最容易想到的呈现方式是什么呢? 一个可以自定义的 chart? 一个抽象的坐标系统? 将数据编码成图像来表示?

每一种方法都有它的好处. 比如你想做探索性的数据可视化, 你可能会更偏向于快速(高效)的方法, 因为这样你可以快速的测试各种不同的可视化效果.
如果是做讲解性质的数据可视化, 你可能会选择能精细控制的方法, 以让你的观众能精准的理解你的意图.

不管你选择的哪种方法, 你最终都需要将实际的图形画在屏幕上, 也就是说你需要用你的数据生成一些能代表这些数据的图形.

### 那么, 你打算如何画出图形呢?

一些简单的图形, 比如柱状图, 通过 canvas 的 api 就可以轻松的画出: [fillRect](https://www.w3.org/TR/2dcontext/#dom-context-2d-fillrect)

![bar chart](https://user-gold-cdn.xitu.io/2018/7/2/1645a68fd374cf9d?w=960&h=500&f=png&s=10680)

如果要画出一些直线, 或者折线也不难, 使用 canvas 的 [moveTo](http://www.w3.org/TR/2dcontext/#dom-context-2d-moveto) , [lineTo](http://www.w3.org/TR/2dcontext/#dom-context-2d-lineto) 方法即可:

![line chart](https://user-gold-cdn.xitu.io/2018/7/2/1645a68fd31def0f?w=888&h=240&f=png&s=6603)

但若是想画出曲线图呢? 想要画出 [圆滑的曲线](http://www.cemyuksel.com/research/catmullrom_param/) 似乎开始变得不那么容易了:

![smooth line](https://user-gold-cdn.xitu.io/2018/7/2/1645a68fcca20dbe?w=888&h=240&f=png&s=26325)

我们再增加一点难度, 如果我们想要曲线圆滑的同时, 还要保持数据的[单调性](http://adsabs.harvard.edu/full/1990A%26A...239..443S)呢?

![monotonicity slice](https://user-gold-cdn.xitu.io/2018/7/2/1645a68fcdefc090?w=888&h=240&f=png&s=9320)

再者, 如果我们想画出扇形切片呢? 如果我们还想给扇形切片加上圆角, 加上切片之间的间距呢,

![donut line](https://user-gold-cdn.xitu.io/2018/7/2/1645a68fcda64380?w=500&h=500&f=png&s=41618)

怎么样, 是不是觉得有些**挑战**了呢?

这时候该 **d3-shape** 出场了:
d3-shape 是一个用于绘制数据可视化中常见的几何图形的库. 它非常的小巧, 而且可以同时和 SVG 和 Canvas 协同工作.

### d3-shape 有多小?

大概 28kb, 压缩后仅仅 6kb. 它还包括了 [_d3-path_](https://github.com/d3/d3-path) . 它总共代码仅仅 1500 行, 所以我非常推荐你看看它的[源代码](https://github.com/d3/d3-shape/tree/master/src).

### 使用 d3-shape 你将得到什么呢?

简单的说, 你将得到绘制 [_线_](https://github.com/d3/d3-shape#lines) 和 [_面_](https://github.com/d3/d3-shape#areas) 的能力. 包括: 各种各样的曲线, 派图, 扇形图, 散点图等等.

除此之外还有更多, [_d3-hierarchy_](https://github.com/d3/d3-hierarchy) 模块包括了绘制包含层级结构数据的功能(比如树状图). 还有更多独立的模块能够帮助你绘制出更多定制化的图形: 比如 绘制地理位置的图像, 绘制模拟物理系统的网络图等等.

### d3-shape 的目的是什么呢?

d3-shape 是一个让你进行数据可视化的工具. 它特别适合和已有的操作 DOM 的框架一同使用(意味着不需要 d3-selection), 比如 Angular, Vue, React. d3-js 中还有许多方便的模块, 配合着使用能更好的提升你的数据可视化效率, 比如: [d3-color](https://github.com/d3/d3-color), [d3-format](https://github.com/d3/d3-format), [d3-time](https://github.com/d3/d3-time) and [d3-scale](https://github.com/d3/d3-scale).

### 想要贡献代码?

想要自己实现一个曲线生成的算法? 想要创建一种消除数据噪点的曲线? 查看源代码, 在 github 上提交 pull request. 或者查看编写 d3 插件的文章, 创建并发布你自己的 [d3 插件](https://medium.com/@mbostock/let-s-make-a-d3-plugin-c8e697599f48) 模块.

祝你 d3-shape 使用愉快!



## 想继续了解 D3.js


这里是我的 _D3.js_ 、 _数据可视化_ 的github 地址, 欢迎 start & fork :tada:


[D3-blog](https://github.com/ssthouse/d3-blog)




## 如果觉得不错的话, 不妨点击下面的链接关注一下 : )


[github主页](https://github.com/ssthouse)


[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)


[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)