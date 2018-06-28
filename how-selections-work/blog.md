# [译]D3.js 之 d3-selection 原理

## 译者注

> 原文: 来自 D3.js 作者 Mike Bostock 的[How Selections Works](https://bost.ocks.org/mike/selection/)

> 译者: [ssthouse](https://github.com/ssthouse)

## 译文

在前一篇文章中, 我介绍了关于 D3 selection 的基础, 这些基础能让你开始着手使用 D3 selection.

在这篇文章中, 我将介绍 d3-selection 的实现原理. 本文可能需要更长的时间来阅读, 但它能揭开 selection 的原理 并让你能真正掌握数据驱动文本(D3)

本文初看可能会显得结构组织比较随意. 文章会介绍 selection 内部的工作原理而不是 selection 的设计动机, 所以你可能会对为什么使用 selection 这种模式感到疑惑.
这只是因为先讲解每一个片段,再讲解如何将这些片段串联起来使用会更容易理解.
等你读到本文结尾时, 自然会明白 selection 如此设计的原因.

D3 是一个 用于可视化的库, 所以本文用图像的方式, 结合着文字进行讲解.

![data join](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/data_join.png)

圆角矩形, 比如 `thing` 表示 JavsScript 的各种对象, 从 object ({foo:16}) 到 基础数据类型 ("hello"), 数组 ([1,2,3]) 再到 DOM 元素. 不同种类的对象会用不同的颜色来区分.
对象之间的关系会用灰色的线来表示, 比如一个包含数字 42 的数组会表示成这样:

```javascript
var array = [42]
```

![sample pic](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/1.png)

大部分情况下, 图像对应的代码会出现在图片的上方. 你可以访问这个网站, 并打开调试窗口对文中的代码进行试验, 这样能助你更好的理解本文.

现在, 让我们开始!

### Array 的子类

可能有人和你说过: selection 就是 DOM 元素组成的数组. 但其实并不是的, selection 是 array 的子类,这个子类提供了一些操作选中的元素的方法 (比如设置属性, 设置样式). selection 同样继承了 array 的一些方法, 比如 array.forEach, array.map. 然而, 你并不会经常使用这些 array 继承来的方法, 因为 D3 提供了一些方便的替代方法(比如 selection.each). 并且, 有一些 array 的方法为了符合 selection 的逻辑而被 overridden, 比如 selection.filter 和 selection.sort.

### Group 元素

另一个 selection 不是 DOM 元素数组的原因是: selection 是 group 的数组, 而 group 才是 DOM 元素的数组. 举个例子, `d3.select` 返回的 selection 包含了一个 group, 而这个 group 包含了选中的 body 元素:

```javascript
var selection = d3.select('body')
```

![single group with body](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/2.png)

在 [JavaScript 控制台](https://bost.ocks.org/mike/selection/), 尝试运行下面的命令并查看 selection[0] ==> group 和 元素 selectio[0][0]. 虽然 D3 支持这种通过数组下标访问元素的方式, 但是你很快就会意识到用 selection.node 会更好.

相似的, d3.selectAll 也会返回一个 group, 这个 group 中会有若干个元素:

```javascript
d3.selectAll('h2')
```

![single group with many h2](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/3.png)

d3.select 和 d3.selectAll 都是返回的一个 group. 唯一获得包含多个 group 的 selection 的方法是 `selection.selectAll`. 比如, 如果你选中所有的 table row, 接着再选中这些 row 的 cell:

```javascript
d3.selectAll('tr').selectAll('td')
```

![many groups with nodes](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/4.png)

当运行上面代码的第二个 selectAll 时, 前面 `d3.selectAll('tr')` 得到的 selection 中, 每一个元素都将变成新 selection 中的一个 group; 每个 group 都会包含老的元素中符合条件的所有子元素. 所以, 如果 table 中每个 td 都包含有一个 span 的话, 我们调用下面的代码, 会得到:

```javascript
d3.selectAll('tr')
  .selectAll('td')
  .selectAll('span')
```

![many groups with spans](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/5.gif)

每一个 group 都有一个 parentNode 属性, 这个属性存储了 group 中所有元素的父节点.　父节点属性会在 group 被创建时就被赋值. 因此, 如果你调用 `d3.selectAll("tr")​.selectAll("td")` , 返回的 group 数组, 他们的父节点就是 tr. 而 `d3.select` 和 `d3.selectAll` 返回的 group, 他们的父节点就是 html.

通常来说, 你完全不用在意 selection 是由 group 组成的这个事实. 当你对 selection 调用 `selection.attr` 或者 `selection.style` 的时候, selection 中的所有 group 的所有子元素都会被调用. 而 group 存在的唯一影响是: 你在 `selection.attr('attrName', function(data, i))`时, 传递的 function(data, i) 中, 第二个参数 i 是元素在 group 中的索引而不是在整个 selection 中的索引.

### select 为何不涉及 group

只有 `selectAll` 会涉及到 group 元素, `select` 会保留当前已有的 group. select 方法之所以不同是因为在老的 selection 中的每个元素都只会在新的 selection 中对应一个新的元素. 因此 select 操作也会直接把数据从父元素传递给子元素 (因此也根本没有 data-join 的过程)

为了方便使用, `_append_ 方法和 _insert_ 方法都被挂载在了 selection 上, 这两个方法都会自动维持 group 结构, 并且自动传递数据. 比如我们现在有一个有四个 section 节点的页面:

```javascript
d3.selectAll('section')
```

![many groups with nodes](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/6.png)

如果你调用下面的方法, 为每一个 section 添加一个 p 元素, 你会得到一个有四个 p 元素的 group:

```javascript
d3.selectAll('section').append('p')
```

![many groups with spans](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/7.png)

需要注意的是, 现在这个 selection 的父节点仍然是 html, 因为 selection.selectAll 还没有被被调用来, 所以父节点还没有发生变化.

### 空元素

group 中可以保存 Null 元素, 用来声明元素的缺失. Null 会被大部分的操作所忽略, 比如: D3 会在 `selection.attr` 和 `selection.style` 的时候自动忽略 Null 元素.

Null 元素会在 selection.select 无法找到符合要求的子元素时被创建. 因为 select 方法会维持 group 的结构, 所以它会在缺失元素的地方填上 Null. 比如下面这个例子, 四个 section 中只有两个有 aside 元素:

```javascript
d3.selectAll('section').select('aside')
```

![4 selectio , 2 of which has aside](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/8.png)

虽然在大部分情况下, 你完全可以忽略 group 中的 Null 元素, 但是记住 Null 元素是确实存在于 group 的结构当中的, 并且他们会在计算索引时被考虑进来.

### 绑定数据

data 并不是 selection 的一个属性, 这一点可能会让你感到很惊讶, 但确实是这样. data 并不是 selection 的一个属性, 而是被保存为 DOM 元素的一个属性.
这就意味着, 当你使用 selection.data 绑定数据时, 其实数据是被绑定到了 DOM 元素上. data 会被赋值给 DOM 元素的 `__data__` 属性. 如果一个 DOM 元素没有 `__data__` 属性, 就表明它没有被绑定数据. 所以 selection 是临时性的, 但数据是被持久化在 DOM 里的, 你可以从新创建 selection, 而你的 selection 中的 DOM 元素仍会保有它之前被绑定的数据.

数据的绑定可以通过一下几种方式实现, 接下来我们会分别讲解这三种绑定方式:

- 给每一个单独的 DOM 元素调用 `selection.datum`
- 从父节点中继承来数据, 比如: append, insert, select
- 调用 `selection.data`

> 给每一个单独的 DOM 元素调用 `selection.datum`

因为有 selection.datum 方法的存在, 你不需要手动的去给 `__data__` 属性赋值, 虽然 selection.datum 就是这个实现的:

```javascript
document.body.__data__ = 42
```

![body with data 42](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/9.png)

使用 D3 的方式来达到同样的效果:

```javascript
d3.select('body').datum(42)
```

> 从父节点中继承来数据, 比如: append, insert, select

![body with data 42 in D3 way](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/10.png)

如果我们现在向 body 中 插入一个 _h1_ 元素, _h1_ 元素就会自动继承 body 的数据:

```javascript
d3.select('body')
  .datum(42)
  .append('h1')
```

![h1 get data from body](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/11.png)

> 调用 `selection.data`

最后我们来看 `selection.data`, 讲解这个方法会引入 d3 中非常重要的 `data-join` 思想. 但在我们讲解这个思想之前, 我们需要首先回答一个更加基本的问题: 什么是数据 ?

### 什么是数据?

在 D3 中, 数据可以是装有基础数据类型数据的数组, 比如下面这个:

```javascript
var numbers = [4, 5, 18, 23, 42]
```

或者是对象数组:

```javascript
var letters = [
  { name: 'A', frequency: 0.08167 },
  { name: 'B', frequency: 0.01492 },
  { name: 'C', frequency: 0.0278 },
  { name: 'D', frequency: 0.04253 },
  { name: 'E', frequency: 0.12702 }
]
```

甚至是矩阵(由数组组成的数组):

```javascript
var matrix = [[0, 1, 2, 3], [4, 5, 6, 7], [8, 9, 10, 11], [12, 13, 14, 15]]
```

你可以通过 selection 来描述数据和可视化图形之间的关系. 下面我们来具体讲解. 我们先创建一个有 5 个数字的数组:

![array with 5 number](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/12.png)

就像 selection.style 可以接受一个普通的 string(例: "red")或者一个返回 string 的 function (例: unction(d) => d.color), selection.data 也可以接受这两种参数.

然而, 和其他 selection 的方法不同, selection.data 是为每一个 group 定义了数据, 而不是为每一个 DOM 元素定义了数据: 对于 group 来说, 数据应该是一个数组或者是一个返回数组的 function. 因此, 一个有多个 group 的 selection 其对应的数据也应该是一个包含多个子数组的数组.

![array with 5 number](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/13.png)

上图中, 蓝色的线条表示data() 方法返回的是一个二维数组. 