# [译] D3.js 嵌套选择集 (Nested Selection)

## 译者注:

> 原文: Mike Bostock (D3.js 作者) -- [Nested Selections](https://bost.ocks.org/mike/nest/)

> 译者: [ssthouse](https://github.com/ssthouse)

本文讲解的是关于 D3.js 中 d3-selection 的使用. d3-selection 是 d3 的核心所在, 它提供了一种和以往 _Dom 操作_ 和 _数据操作_ 完全不同的思路, 让我们能非常优雅的进行数据可视化工作.
本文是 d3 作者对于 d3-selection 中 _嵌套选择集_ 的讲解, 本人阅读后觉得很有启发, 所以翻译成中文, 希望对读者也能有所帮助.

## 译文

D3 的 选择集是分层级的, 就像 Dom 元素和数据集是可以有层级的一样. 比如说 Table:

```html
<table>
  <thead>
    <tr><td>  A</td><td>  B</td><td>  C</td><td>  D</td></tr>
  </thead>
  <tbody>
    <tr><td>  0</td><td>  1</td><td>  2</td><td>  3</td></tr>
    <tr><td>  4</td><td>  5</td><td>  6</td><td>  7</td></tr>
    <tr><td>  8</td><td>  9</td><td> 10</td><td> 11</td></tr>
    <tr><td> 12</td><td> 13</td><td> 14</td><td> 15</td></tr>
  </tbody>
</table>
```

如果让你只选中 tbody 元素中的 td, 你会如何操作? 直接使用 `d3.selectAll('td')` 显然会选中所有的 td 元素(包括 thead 和 tbody). 如果想只选中存在与 B 元素中的 A 元素, 你需要这样操作:

```javascript
var td = d3.selectAll('tbody td')
```

除了上面那种方法, 你还可以先选中 tbody, 再选中 td 元素, 像这样:

```javascript
var td = d3.select('tbody').selectAll('td')
```

因为 selectAll 会对当前选择集中的每个元素(如: tbody)选中其符合条件的子元素(如: td). 这种方法会得到和 `d3.selectAll('tbody td')` 相同的结果. 但如果你之后想要对同一父元素的选择集引申出更多的选择集的话 (比如区分选中 table 中的奇数行选择集和偶数行选择集), 这种嵌套选择集方式会方便的多.

### 嵌套中索引的使用

接着上面的例子, 如果你使用 `d3.selectAll('td')` , 你会得到一个平展的选择集, 像这样:

![flat selection](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/1.png)

```javascript
var td = d3.selectAll('tbody td')
```

平展的选择集缺少了层级结构: 不论是 thead 中的 td 还是 tbody 中的 td 全都被展开成了一个数组, 而不是以父元素进行分组. 这让我们想对每一行或是每一列的 td 进行操作变得很困难. 与此相反的, D3 的嵌套选择集能保存层级关系. 比如我们想以行的方式对选择集分组, 我们首先选中 tr 元素. 然后选中 td 元素.

![nested selection](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/2.png)

```javascript
var td = d3.selectAll('tbody tr').selectAll('td')
```

现在, 如果你想让第一列的所有元素变红, 你可以利用 index 参数 i:

```javascript
td.style('color', function(d, i) {
  return i ? null : 'red'
})
```

上面的参数 i 是 td 元素在 tr 中的索引, 你还可以通过添加一个 j 参数来获得当前的行数的索引. 像这样:

```javascript
td.style('color', function(d, i, j) {
  console.log(`current row: ${j}`)
  return i ? null : 'red'
})
```

### 嵌套和数据间的关联

层级结构的 Dom 元素常常是由层级结构的数据来驱动的. 而层级的选择集能更方便的处理数据绑定.
继续上面的例子, 你可以把 table 的数据表示为一个矩阵:

```javascript
var matrix = [
  [0, 1, 2, 3], 
  [4, 5, 6, 7], 
  [8, 9, 10, 11], 
  [12, 13, 14, 15]
]
```

为了让这些数据绑定上对应的 td 元素, 我们首先将矩阵的每一行和 tr 绑定对应起来, 然后再将矩阵中一行的每一个元素和 tr 中的每一个 td 绑定起来:

```javascript
var td = d3
  .selectAll('tbody tr')
  .data(matrix)
  .selectAll('td')
  .data(function(d, i) {
    return d
  }) // d is matrix[i]
```

需要注意的是, `data()` 不仅可以传入一个数据, 它还可以传入一个 _返回一个数组的 function_. 平展的选择集通常对应的是单个数组, 是因为平展的选择集只有一个 group.

上面的 row 选择集是一个平展的选择集, 因为它是直接由 d3.selectAll 创建的:

![tr flat selection](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/3.png)

```javascript
var tr = d3.selectAll('tbody tr').data(matrix)
```

而 td 的选择集是嵌套的:

![tr nested selection](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/4.png)

```javascript
var td = tr.selectAll('td').data(function(d) {
  return d
}) // matrix[i]
```

data 传入的 操作函数给每一个 group 绑定了一个数组数据. d3 会对每一行 tr 调用操作函数. 因为父元素数据是矩阵, 所以操作函数在每次被调用时只是简单的返回矩阵中当前行的数据, 来和 tr 进行绑定.

### 嵌套中父节点作用

嵌套选择集有一个微妙但可能造成严重影响的副作用: 它会给每个 group 设置父节点. 父节点是选择集的一个隐藏属性, 它会在被调用 append 方法时使用, 将子元素添加到父节点的 Dom 元素当中. 比如: 如果你想通过下面的方式进行数据绑定操作, 你会得到一个 error:

![wrong append operation](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/5.png)

```javascript
d3.selectAll('table tr')
  .data(matrix)
  .enter()
  .append('tr') // error!
```

上面的代码之所以会报错, 是因为默认的父节点是 html 元素, 你不能直接将 tr 元素添加到 html 元素中. 所以, 我们应该在进行数据绑定前, 先选择好父节点:

![choose parent node before append operation](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/6.png)

```javascript
d3.select('table')
  .selectAll('tr')
  .data(matrix)
  .enter()
  .append('tr') // success
```

这种方式可以用来选择任意层级的嵌套选择集. 比如你想从头创建一个 table, 并填入上面矩阵中的数据, 你可以首选选中 body 元素:

![create table manually](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/7.png)

```javascript
var body = d3.select('body')
```

接下来在父节点 body 中添加一个 table:

![append table to body](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/8.png)

```javascript
var table = body.append('table')
```

接下来绑定矩阵数据, 创建 tr 元素. 因为 selectAll 是在 table 元素上进行调用的, 所以父节点是 table:

![append tr to table](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/9.png)

```javascript
var tr = table
  .selectAll('tr')
  .data(matrix)
  .enter()
  .append('tr')
```

最后我们以 tr 作为父节点, 创建 td 元素:

![append td to tr](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/10.png)

```javascript
var td = tr
  .selectAll('td')
  .data(function(d) {
    return d
  })
  .enter()
  .append('td')
```

### 要不要使用嵌套选择集 ?

在 D3 中, select 和 selectAll 有一个很重要的区别: select 会继续使用当前存在的 group, 而 selectAll 总是会创建新的 group. 因此调用 select 能保存原有 selection 的数据, 索引位置, 甚至父节点.

比如, 下面的平展的选择集, 它的父节点仍然是 html 节点:

![parent node is html](https://github.com/ssthouse/d3-blog/raw/master/nested-selection/img/11.png)

```javascript
var td = d3.selectAll('tbody tr').select('td')
```

想要得到嵌套的选择集, 唯一的方法就是在已有的选择集的基础上, 调用 selectAll 方法. 这就是为什么数据绑定总是出现在 selectAll 之后, 而不是 select 之后.

这篇文章使用 table 作为例子仅仅是为了方便讲解层级结构. 其实 table 的使用并不是特别具有典型性. 其实还有许多其它 嵌套选择集的例子([点我查看](https://bost.ocks.org/mike/miserables/), [点我查看](https://bl.ocks.org/mbostock/882152))

就像 _[用 join 的方式思考](https://juejin.im/post/5b20a5925188251371243c14)_ 一样, 嵌套选择集同样使用了一种思想上完全不同的处理 _Dom 元素_ 和 _数据_ 的思路. 这种思路刚开始可能很难理解, 但你一旦掌握了, 你就能驾轻就熟的使用 D3.js 来完成你的数据可视化任务了.

## 如果觉得不错的话, 不妨点下面的链接关注一下 : )

[github 主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)

## 想直接联系我 ?

邮箱: ssthouse@163.com

微信:

![image-20190729170042724](http://ww4.sinaimg.cn/large/006tNc79gy1g5gteujraqj31040fb78s.jpg)