# **D3**.js 简介



> 原文: https://medium.com/@enjalot/the-hitchhikers-guide-to-d3-js-a8552174733a
>
> 译文源代码地址: https://github.com/ssthouse/d3-blog/blob/master/d3-guide/d3_roadmap_cn.md

如果这篇文章对你有帮助的话, 不妨点个赞 :tada: 

> github 地址:  https://github.com/ssthouse/d3-blog



学习D3.js的旅途中看到的风景十分美妙, 壮丽 有时甚至有些崎岖. 你可能会被d3.js文档长长的function列表所吓到([d3’s API documentation](https://github.com/d3/d3/blob/master/API.md) ), 或者被成堆的教程弄的疲惫不堪( [dozens of tutorials](https://github.com/d3/d3/wiki/Tutorials)). 这里有超过两万个例子你可以用来学习: [20,000+ d3 examples](http://blockbuilder.org/search) , 但你不知道哪些是真正对你学习D3.js有帮助的.

![我们仍然在努力摸清学习D3的整幅地图…](https://cdn-images-1.medium.com/max/4814/1*C17GW5l4S_W99_52lQMpBQ.png)

如果你只是想要快速得到一个柱状图或者图表, 那么这篇文章可能不太适合你. 这里有很多库可以帮你做到这一点: [plenty of charting libraries](https://github.com/wbkd/awesome-d3#charts) . 如果你更倾向于看书学习, 这本书也许是你不错的起点 [Interactive Data Visualization for the Web](http://shop.oreilly.com/product/0636920026938.do). [Elijah Meeks](https://twitter.com/Elijah_Meeks)  的 [D3.js in Action](https://www.manning.com/books/d3-js-in-action)  可以帮助你更好的了解D3的API.

这篇文章目的是让你在思想上做好学习D3的准备, 并且提供一些接下来学习的方向. 除了对于D3本身API的学习, 关于web标准的HTML, SVG, CSS, Javascript 和 数据可视化的概念,标准都是需要学习的. 很有可能你已经知道上面这些中的一部分, 这篇文章旨在为你继续学习提供一个好的起点.

![[r2d3.us visual introduction to machine learning](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/) sets the bar high](https://cdn-images-1.medium.com/max/3676/1*ofBagXi1x0h8TdHF8tc3nA.png)*[r2d3.us visual introduction to machine learning](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/) 设置了一个很高的起点*

在我们进入数据可视化和代码技巧的学习之前, 先让我们看看一些能让我们激发学习兴趣的东西. 这里有很多让人惊叹的例子, 不妨点进去看一看, 并给自己设定一个学习目标:  [New York Times article](https://www.google.com/search?q=new+york+times+d3+interactives&oq=new+york+times+d3+interactives&aqs=chrome..69i57j69i64.6825j0j1&sourceid=chrome&ie=UTF-8), [r2d3](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/), [distill.pub](http://distill.pub), [datasketch|es](http://www.datasketch.es/), [polygraph](https://pudding.cool/),  [ncase](http://ncase.me/). 

不要只是看看就好, 你可以给自己设定一个大一点的目标. 我从这篇文章中学习到, 最好的学习方式就是给自己设定一个想要完成的任务, 并且绞尽脑汁的去完成它:  [interviewing some of the top data visualization practitioners using d3.js](https://medium.com/@enjalot/how-do-you-learn-d3-js-ccffc151419b) .

## 图形化的展现形式

> D3 并没有引入一种新的视觉展现形式. 不同于 [Processing](http://processing.org/), [Raphaël](http://raphaeljs.com/), 或者 [Protovis](http://vis.stanford.edu/protovis/), D3 图形方面的词汇都是直接来自于 web标准: : HTML, SVG, and CSS
 — http://d3js.org

![Learning d3.js to write charts is like to learning French to write nutrition labels. To be fair, [@syntagmatic](https://twitter.com/syntagmatic) has made some beautiful [nutrition visualizations](http://bl.ocks.org/syntagmatic/2420080)](https://cdn-images-1.medium.com/max/2000/1*GaASA7rqnQVDpHQs9BdbqA.png)*学习D3来写图表就像是学习法语来写营养标签. 公平的来说 , [@syntagmatic](https://twitter.com/syntagmatic) 确实做出了非常不错的 [营养标签](http://bl.ocks.org/syntagmatic/2420080)*

图表仅仅是内部有一些形状的矩形. 而D3提供的是一种让你通过操作图标或者你自己定义的图形来表达你想要展示数据的方式.  它让你可以轻易地为你的图形添加可视化交互, 定义你的图形有怎样的行为. 你在D3中学到的将是一种可视化的表达方式, 这是你在其他library中所得不到的.

>  如果你想要了解人们所用的不同种类的标记和图形符号所遵循的准则, 你可以参考这本书: [Grammar of Graphics](https://smile.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448?sa-no-redirect=1).

不过不用担心, 仅仅是用圆圈和矩形你就已经可以写出无数具有创造力的作品了. 从简单的事情做起, 先在屏幕上展示一些东西, 再慢慢优化它.

## 在Web展示上

![[SVG Beyond Mere Shapes](https://www.visualcinnamon.com/2016/04/svg-beyond-mere-shapes.html) 是对web标准的图形操作非常棒的展示](https://cdn-images-1.medium.com/max/4432/1*TwUCVhrN9Xltsj3sDgntUQ.png)*[SVG Beyond Mere Shapes](https://www.visualcinnamon.com/2016/04/svg-beyond-mere-shapes.html) 是对web标准的图形操作非常棒的展示*

我们选择D3的原因之一是: 你可以非常方便的将你的作品分享给任何有浏览器的人. 这意味着你需要对于HTML5有一个基本的了解. 在你开始学习D3的API之前, 你应该已经掌握 SVG, HMTL 和 CSS的基本知识. 如果有时间的话, 你最好看看这篇讲Canvas的文章(如果你要展示的数据数据量非常大的话) [learn some Canvas](https://www.w3schools.com/html/html5_canvas.asp) . 我推荐你也看看这篇, 它能帮你很好的将Canvas和D3结合使用 [this is a great intermediate tutorial](https://medium.freecodecamp.com/d3-and-canvas-in-3-steps-8505c8b27444).

对于SVG, 我推荐看看这个简短有趣的介绍 [SVG primer](http://alignedleft.com/tutorials/d3/an-svg-primer) 来自 [Scott Murray](http://twitter.com/alignedleft). 使用工具: [BlockBuilder](http://blockbuilder.org) 来迅速开始你的coding而不用费时配置开发环境. 你也可以看看MDN对于SVG的官方文档 [MDN reference site for SVG](https://developer.mozilla.org/en-US/docs/Web/SVG). 等你掌握了SVG的基础知识后, 你可以再看看这篇文章, 可以让你对SVG有一个更深的理解 [SVG beyond mere shapes](https://www.visualcinnamon.com/2016/04/svg-beyond-mere-shapes.html) 来自 [Nadieh Bremer](http://visualcinnamon.com).

![[http://blockbuilder.org](http://blockbuilder.org) makes it easy to play with web standards!](https://cdn-images-1.medium.com/max/2140/1*PFwvxtqLRRVekNoMufclzw.gif)*[http://blockbuilder.org](http://blockbuilder.org) 让你可以快速上手玩转web图形标准!*

其实你甚至可以不用使用 SVG来进行图形展示, 有时候直接用 `div` 就足以让你做出想要的效果. 当然这要求你对 `CSS` 有较好的掌握: [CSS positioning](https://css-tricks.com/almanac/properties/p/position/) . 为了达到你想要的效果, 你甚至可以 [混用 HTML, SVG , Canvas](http://bl.ocks.org/sxv/4491174)!

## 开始学习 d3.js

![[d3js.org](http://d3js.org)](https://cdn-images-1.medium.com/max/4424/1*KfsnI5vicI0ozs1uP85Pfg.png)*[d3js.org](http://d3js.org)*

 你可能见过D3的API文档, 展示着成堆的Function: [d3’s API](https://github.com/d3/d3/blob/master/API.md), 幸运的是, 现在D3已经拆分为了一个个的模块, 所以我们从中挑选一些特别常用的来进行介绍.

### d3-scale

![[colors](http://blockbuilder.org/enjalot/f1ac6277c9b224ebf4daada75a06294d) are one common use of scales!](https://cdn-images-1.medium.com/max/3728/1*c2dJV4ZNJGdWdWNV3AVPxQ.png)*[colors](http://blockbuilder.org/enjalot/f1ac6277c9b224ebf4daada75a06294d) 是对于scale非常常见的一种使用方式*

scales是D3中非常基础的一个工具. 你可以从这里对它有一个大概的了解:  [Introducing d3-scale](https://medium.com/@mbostock/introducing-d3-scale-61980c51545f) 来自D3作者 Mike Bostock. 无论你做什么样的数据可视化, 你都非常有可能用到一种或多种scale.

### d3-shape

![A [streamgraph](http://bl.ocks.org/mbostock/582915), thanks to SVG paths!](https://cdn-images-1.medium.com/max/3828/1*6HpzsxMbWhLgTbAZNpf9Kg.png)* [streamgraph](http://bl.ocks.org/mbostock/582915),感谢 SVG paths!*

SVG 的path非常冗杂 ([see this thorough overview](https://css-tricks.com/svg-path-syntax-illustrated-guide/)), 所以 [d3-shape](https://github.com/d3/d3-shape#d3-shape) 提供了一些让我们非常方便创建并操作SVG path的方法. 你可以看看 Mike 的 [Introducing d3-shape](https://medium.com/@mbostock/introducing-d3-shape-73f8367e6d12) 来了解它的作用并着手开始使用它. d3-shape 还可以帮助你在Canvas中绘制各种各样的图形, 你仅仅添加一行代码就能将SVG的path添加到Canvas中!

### d3-selection

![the [General Update Pattern](https://bl.ocks.org/mbostock/3808234)](https://cdn-images-1.medium.com/max/2000/1*-qbObwrIKYG3_bKpZBaUgw.gif)*the [General Update Pattern](https://bl.ocks.org/mbostock/3808234)*

D3中最难以理解的部分可能就是它的selection了, 同样的, 看看D3作者的文章能让你对D3的selection模型有一个更好的理解:  [General Update Pattern](https://bl.ocks.org/mbostock/3808218). 我花了好几个月用脑袋锤桌子才最终理解了selection模型, 但是不要为此感到害怕! 你并不需要完完全全的掌握selection的所有api才能完成一份D3的作品. 当你做好了学习的准备, 你可以从这篇文章开始: [d3-selections](https://github.com/d3/d3-selection#d3-selection) README. 还有, 请一定看看这篇文章, 非常有助于理解selection模型 [Thinking in Joins](https://bost.ocks.org/mike/join/).

### d3-collection

![d3-nest makes it easy to [group similar things together](https://bl.ocks.org/mbostock/9490313)](https://cdn-images-1.medium.com/max/3808/1*jcnYXBvv1W033jfwFOrS1A.png)*d3-nest 让你可以非常轻松的 [将相似的数据归结在一起](https://bl.ocks.org/mbostock/9490313)*

操作数据是数据可视化中非常重要的步骤. 有时候这甚至是最困难的一部 (如果你的数据集不是很完美, 或者你对它没有很好的理解). 虽然有很多可以帮助进行数据处理的工具, 这里我还是推荐看看d3 collection  [d3-collection](https://github.com/d3/d3-collection/blob/master/README.md#d3-collection),特别是这个模块:  [nest](https://github.com/d3/d3-collection/blob/master/README.md#nests) function.

### d3-hierarchy

![a [Treemap](https://bl.ocks.org/mbostock/8fe6fa6ed1fa976e5dd76cfa4d816fec) is easy to layout thanks to d3-hierarchy](https://cdn-images-1.medium.com/max/2580/1*1CszAZA3t5oMlTOMFchzSg.png)* [Treemap](https://bl.ocks.org/mbostock/8fe6fa6ed1fa976e5dd76cfa4d816fec) 感谢  d3-hierarchy*

接着上面对于 `数据操作` 的讨论. 在很多数据可视化中, 按照数据的结构对其进行相应的展示是非常关键的一点. 你可以在这里找到很多工具Function, 它们能帮你很方便的进行这样的数据处理: [d3-hierarchy](https://github.com/d3/d3-hierarchy#d3-hierarchy) 绘制树状结构的数据.

### d3-zoom

![[zooming is fun](https://bl.ocks.org/mbostock/b783fbb2e673561d214e09c7fb5cedee)!](https://cdn-images-1.medium.com/max/2000/1*R96BnzJUPFSzTzWoxIBIAw.gif)*[zooming is fun](https://bl.ocks.org/mbostock/b783fbb2e673561d214e09c7fb5cedee)!*

缩放是一种非常常见的数据可视化交互, D3的作者 Mike 给出了 [一系列的例子](http://blockbuilder.org/search#text=zoom;user=mbostock;d3version=v4) 展示如何将缩放功能引入你的数据可视化作品中 [d3-zoom](https://github.com/d3/d3-zoom#d3-zoom). 你也可以看看和 `缩放` 非常类似的一种操作 `拖拽` [d3-drag](https://github.com/d3/d3-drag#d3-drag)

### d3-force

![[sparse matrices](https://bl.ocks.org/syntagmatic/75c5ca501200b0cf7a5958b4e404f777), amirite?](https://cdn-images-1.medium.com/max/2000/1*MiNLItcbqWuZP5y6DHVEsg.gif)

*[sparse matrices](https://bl.ocks.org/syntagmatic/75c5ca501200b0cf7a5958b4e404f777)*

D3中非常让人们感到惊艳的功能之一是 `d3 force layout`. 它非常容易上手使用, 但是很难真正掌握. 详细信息请参考: [d3-force](https://github.com/d3/d3-force#d3-force).

## 搜索!

最后一个tip: 你可以在这个网址对任何API Function搜索使用案例: [BlockBuilder’s search](http://blockbuilder.org/search).你甚至可以将你的搜索范围限定版本的d3上!

![look at all those blocks!](https://cdn-images-1.medium.com/max/2000/1*AAKuY9w1DNMIq0oBqTe3KA.gif)*look at all those blocks!*

## D3社区

![Welcome to the [Blockiverse](http://bl.ocks.org/micahstubbs/b35f2560f4205570b3328d1b40de0c6c)](https://cdn-images-1.medium.com/max/2824/1*m3WWkrnN1pr6V-pFZgK8KQ.png)*[Blockiverse](http://bl.ocks.org/micahstubbs/b35f2560f4205570b3328d1b40de0c6c)*

和志同道合的人们联系起来! 你可以加入我们的slack channel: [d3.js slack channel](https://d3-slackin.herokuapp.com/). 或者找到和你最近的D3线下活动: https://www.meetup.com/topics/d3-js/). 如果你对D3非常狂热的话, 来参加SF每年秋天的聚会吧! [annual d3.unconf](http://visfest.com)!

![how I see the d3 community and the many learning curves one encounters on their journey](https://cdn-images-1.medium.com/max/5100/1*gybGRNFfU-ZBYtK3D1qGYA.png)*我对于D3社区 和 D3学习过程中困难的理解*

*非常感谢 [Erik Hazzard](https://twitter.com/erikhazzard) 帮助我编辑和润色这篇文章. 感谢 [Kai Chang](https://twitter.com/syntagmatic) 对于文章提出的建议, 更加感谢你对于 d3 社区的帮助. 感谢slack channel #teaching-d3 in the [d3js Slack](http://d3-slackin.herokuapp.com), 特别是 [Sebastian](https://twitter.com/dashingd3js) 和 [John](https://twitter.com/JFSIII) 的反馈. 当然了, 最最感谢D3的作者 [Mike Bostock](https://twitter.com/mbostock) 创造了一个能让我们所有人玩耍的乐园 !*
