# [译]D3.js 之 d3-selection 原理 (How Selections Works)

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

![many groups with spans](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/5.png)

每一个 group都有一个 parentNode属性, 这个属性存储了group中所有元素的父节点.