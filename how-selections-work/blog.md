# [译]D3.js 之 d3-selection 原理

## 译者注

> 原文: 来自 D3.js 作者 Mike Bostock 的[How Selections Works](https://bost.ocks.org/mike/selection/)

> 译者: [ssthouse](https://github.com/ssthouse)

## 译文

在前一篇文章中, 我介绍了关于 D3 selection 的基础, 这些基础足以让你开始使用 D3 selection.

在这篇文章中, 我将介绍 d3-selection 的实现原理. 本文可能需要更长的时间来阅读, 但它能揭开 selection 的原理 并让你能真正掌握数据驱动文本的思想(D3的思想)

本文会介绍 selection 内部的工作原理而不是 selection 的设计动机, 所以你刚开始可能会对为什么使用 selection 这种模式感到疑惑.
但等你读到本文结尾时, 你自然会明白 selection 如此设计的原因.

D3 是一个 用于数据可视化的库, 所以本文也用可视化的方式, 结合着文字对selection原理进行讲解.

![data join](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/data_join.png)

我会用圆角矩形, 比如 `thing` 表示 JavsScript 的各种对象, 从 object ({foo:16}) 到 基础数据类型 ("hello"), 到数组 ([1,2,3]) 再到 DOM 元素. 不同种类的对象会用不同的颜色来区分.
对象之间的关系会用灰色的线来表示, 比如一个包含数字 42 的数组会表示成这样:

```javascript
var array = [42]
```

![sample pic](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/1.png)

大部分情况下, 图像对应的代码会出现在图片的上方. 你可以访问[这个网站](https://bost.ocks.org/mike/selection/), 并打开调试窗口对文中的代码进行试验, 这样能帮助你更好的理解本文.

现在, 让我们开始!

### Array 的子类

可能有人和你说过: selection 就是 DOM 元素组成的数组. 但事实并不是这样, selection 是 array 的子类,这个子类提供了一些操作选中元素的方法 (比如设置属性: selection.attr, 设置样式: `selection:style`). selection 同样继承了 array 的一些方法, 比如 `array.forEach`, `array.map`. 然而, 你并不会经常使用这些从 array 继承来的方法, 因为 D3 提供了一些方便的替代方法(比如 `selection.each`). 并且, 有一些 array 的方法为了符合 selection 的逻辑而被 _overridden_, 比如 `selection.filter` 和 `selection.sort`.

### Group 元素

另一个 selection 不是 DOM 元素数组的原因是: selection 是 group 的数组, 而 group 才是 DOM 元素的数组. 举个例子, `d3.select` 返回的 selection 包含了一个 group, 而这个 group 包含了选中的 body 元素:

```javascript
var selection = d3.select('body')
```

![single group with body](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/2.png)

在 [JavaScript 控制台](https://bost.ocks.org/mike/selection/), 尝试运行下面的命令并查看 selection[0] ==> group 和 元素 `selectio[0][0]`. 虽然 D3 支持这种通过数组下标访问元素的方式, 但是你很快就会意识到用 `selection.node` 会更好.

相似的, _d3.selectAll_ 也会返回一个 group, 这个 group 中会有若干个元素:

```javascript
d3.selectAll('h2')
```

![single group with many h2](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/3.png)

_d3.select_ 和 _d3.selectAll_ 都是返回的一个 group. 唯一获得包含多个 group 的 selection 的方法是 _selection.selectAll_ . 比如, 如果你选中所有的 table row, 接着再选中这些 row 的 cell:

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

每一个 group 都有一个 _parentNode_ 属性, 这个属性存储了 group 中所有元素的父节点.　父节点属性会在 group 被创建时就被赋值. 因此, 如果你调用 `d3.selectAll("tr").selectAll("td")` , 返回的 group 数组, 他们的父节点就是 _tr_. 而 _d3.select_ 和 _d3.selectAll_ 返回的 group, 他们的父节点就是 _html_.

通常来说, 你完全不用在意 selection 是由 group 组成的这个事实. 当你对 selection 调用 _selection.attr_ 或者 _selection.style_ 的时候, selection 中的所有 group 的所有子元素都会被调用. 而 group 存在的唯一影响是: 你在 `selection.attr('attrName', function(data, i))`时, 传递的 _function(data, i)_ 中, 第二个参数 _i_ 是元素在 group 中的索引而不是在整个 selection 中的索引.

### select 为何不涉及 group

只有 `selectAll` 会涉及到 group 元素, `select` 会保留当前已有的 group. select 方法之所以不同, 是因为在老的 selection 中的每个元素都只会在新的 selection 中对应一个新的元素. 因此 select 操作会直接把数据从父元素传递给子元素 (因此也根本没有 data-join 的过程)

为了方便使用, _append_ 方法和 _insert_ 方法都被挂载到了 selection 上, 这两个方法都会自动维护 group 的结构, 并且自动传递数据. 比如我们现在有一个有四个 section 节点的页面:

```javascript
d3.selectAll('section')
```

![many groups with nodes](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/6.png)

如果你调用下面的方法, 会为每一个 section 添加一个 p 元素, 你会得到一个有四个 p 元素的 group:

```javascript
d3.selectAll('section').append('p')
```

![many groups with spans](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/7.png)

需要注意的是, 现在这个 selection 的父节点仍然是 html. 因为 _selection.selectAll_ 还没有被调用, 所以父节点没有发生变化.

### 空元素

group 中可以保存 **Null** 元素, 用来声明元素的缺失. **Null** 会被大部分的操作所忽略, 比如: D3 会在 _selection.attr_ 和 _selection.style_ 的时候自动忽略 **Null** 元素.

**Null** 元素会在 _selection.select_ 无法找到符合要求的子元素时被创建. 因为 select 方法会维护 group 的结构, 所以它会在缺失元素的地方填上 **Null**. 比如下面这个例子, 四个 section 中只有两个有 aside 元素:

```javascript
d3.selectAll('section').select('aside')
```

![4 selectio , 2 of which has aside](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/8.png)

虽然在大部分情况下, 你完全可以忽略 group 中的 **Null** 元素, 但是记住 **Null** 元素是确实存在于 group 的结构当中的, 并且他们会在计算 _index_ 时被考虑进来.

### 绑定数据

data 并不是保存在 selection 中的一个属性, 这一点可能会让你感到惊讶, 但确实如此. data 并不是 selection 的一个属性, 而是被保存为 DOM 元素的一个属性.
这就意味着, 当你使用 selection.data 绑定数据时, 其实数据是被绑定到了 DOM 元素上. data 会被赋值给 DOM 元素的 `__data__` 属性. 如果一个 DOM 元素没有 `__data__` 属性, 就表明它没有被绑定数据. 所以 selection 是临时性的, 但数据是被持久化在 DOM 里的, 你可以重新创建 selection, 而你的 selection 中的 DOM 元素仍会保有它之前被绑定的数据.

数据的绑定可以通过以下几种方式实现, 接下来我们会分别讲解这三种方式:

- 给每一个单独的 DOM 元素调用 _selection.datum_
- 从父节点中继承来数据, 比如: _append_ , _insert_ , _select_
- 调用 _selection.data()_ 方法

> 给每一个单独的 DOM 元素调用 `selection.datum`

因为有 _selection.datum_ 方法的存在, 你不需要手动的去给 `__data__` 属性赋值, 虽然 _selection.datum_ 内部就是这样实现的:

```javascript
document.body.__data__ = 42
```

![body with data 42](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/9.png)

使用 D3 的方式来达到同样的效果:

```javascript
d3.select('body').datum(42)
```

> 从父节点中继承来数据, 比如: _append_, _insert_, _select_

![body with data 42 in D3 way](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/10.png)

如果我们现在向 body 中 插入一个 _h1_ 元素, _h1_ 元素就会自动继承 body 的数据:

```javascript
d3.select('body')
  .datum(42)
  .append('h1')
```

![h1 get data from body](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/11.png)

> 调用 _selection.data_

最后我们来看 _selection.data_ , 讲解这个方法会引入 d3 中非常重要的 *data-join* 思想. 但在我们讲解这个思想之前, 我们需要首先回答一个更加基本的问题: 什么是数据 ?

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

就像 _selection.style_ 可以传入一个普通的 string (例: "red") 或者传入一个返回 string 的 function (例: `function(d) => d.color` ) 一样, _selection.data_ 也可以接受这两种参数.

然而, 和其他 selection 的方法不同, selection.data 是为每一个 group 定义了数据, 而不是为每一个 DOM 元素定义数据: 对于 group 来说, 数据应该是一个数组或者是一个返回数组的 function. 因此, 一个有多个 group 的 selection 其对应的数据也应该是一个包含多个子数组的数组.

![array with 5 number](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/13.png)

上图中, 蓝色的线条表示 data() 方法返回的是多个数组. 你传入 _selection.data()_ 的 function 会有两个参数: **parentNode** 和 **groupIndex**. 然后我们根据这两个参数, 返回对应的数据. 因此,这里传入的 function 相当于是持有父级的数据, 然后根据 **parentNode** 和 **groupIndex** 将父级数据拆分为每个 group 的子级数据.

```javascript
selection.data(function(parentNode, groupIndex) {
  return data[groupIndex]
})
```

对于只有一个 group 的 selection, 你可以直接传入 group 对应的数组数据即可. 只有当你遇到需要处理多个 group 的情况时, 你才需要一个 function 来为不同的 group 返回不同的数组数据.

### data-join 的思想

现在, 我们终于可以开始讨论 d3-selection 的核心思想了. 

为了绑定 data 到 DOM 元素, 我们必须知道哪一个数据是对应的哪一个 DOM 元素. 这在D3中是通过比较 key 值来实现的. 一个 key 其实就是一个简单的字符串, 就比如一个名字. 当一个数据和一个 DOM 节点的 key 值相同时, 我们就认为这个数据和这个 DOM 元素是绑定的.

最简单的指定 key 值的方法是使用索引: 第一个数据和第一个 DOM 元素会被赋予 key 值 "0", 第二个会被赋予 "1", 以此类推. 将一个数字数组和一个 key 值匹配的 DOM 元素数组进行 join 操作, 效果如图所示:

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/14.png)

下面的代码得到的绑定好数据的 selection:

```javascript
d3.selectAll('div').data(numbers)
```

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/15.png)

如果你的数据和 DOM 元素的顺序恰好相同(或者对顺序并不在意)时, 通过下标索引作为 key 值是非常方便的. 但是, 一旦数据的顺序发生变化, 通过下表索引作为 key值就变得不可行了. 这时, 你需要手动设置一个 key functon, 将这个 function 作为第二个参数传入 `selection.data(data, keyFunction)`. 这个 keyFunction 需要根据当前的数据, 返回一个对应的 key 值. 比如, 你有一个对象数组作为数据. 每个数据有一个 name 属性, 你的 key function 就可以返回数据的 name 属性, 就像这样:

```javascript
var letters = [
  { name: 'A', frequency: 0.08167 },
  { name: 'B', frequency: 0.01492 },
  { name: 'C', frequency: 0.0278 },
  { name: 'D', frequency: 0.04253 },
  { name: 'E', frequency: 0.12702 }
]

function name(d) {
  return d.name
}

selection.data(data, name)
```

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/16.png)

同样的, 现在 DOM 元素和数据完成了绑定.

```javascript
d3.selectAll('div').data(letters, name)
```

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/17.png)

当有多个 group 时, 上面的情况会变得更加复杂. 但是不用担心, 因为每一个 group 会独立的进行 join 操作. 因此, 你只需要关心如何在一个 group 中保持 key 值的唯一性即可.

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/18.png)

上面的例子假设数据和 DOM 元素的数量是恰好 1:1. 那么当 DOM 元素和数据的数量不相同时呢? 比如有一个 DOM元素 没有对应 key 的数据, 或者有一个数据没有对应 key 的 DOM 元素?

### 进入, 刷新, 离开 (Enter, Update, Exit)

当我们用 key 值来匹配 DOM 元素和数据时, 有三种可能的情况会出现:

- Update - 对于某一个数据, 有相同 key 值的 DOM 元素想对应
- Enter - 对于某一个数据, 没有相同 key 至的 DOM 元素相对应
- Exit - 对于某一个 DOM 元素, 没有相同 key 值的数据相对应

想对应的, selection 也会返回三种状态的选择集: _selection.data_, _selection.enter_, _selection.exit_. 假设我们现在有一个柱状图, 柱状图有 5 列, 分别对应的 ABCDE 这五个字母. 现在你想将柱状图对应的数据从 ABCDE 切换成 YEAOI. 你可以通过设置一个 key function 来为此这五个字母和五列柱状图之间的关系, 数据转换的过程如图: ABCDE ==> YEAOI

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/19.png)

其中 A 和 E 是一直都存在的. 所以他们被划入了 Update 选择集, 并且顺序会切换为新数据集中的顺序, 如图:

```javascript
var div = d3.selectAll('div').data(vowels, name)
```

![abcde to yeaoi](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/20.gif)

剩下的 B, C, D 因为在新的数据(YEAOI)中没有对应的数据, 所以被划入了 Exit 选择集. 注意, Exit 选择集中数据的顺序保持原有数据集中的顺序, 这个顺序会在我们需要加入移除动画时很有帮助.

```javascript
div.exit()
```

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/22.gif)

最后, 新加入的三个字母: Y, O, I 因为没有对应的 DOM 元素, 所以被划分到了 Enter 选择集:

```javascript
div.enter()
```

![maching data array and dom array](https://github.com/ssthouse/d3-blog/raw/master/how-selections-work/img/23.gif)

在这三种状态的选择集中, Update 和 Exit 都是常规的选择集, 他们都是 selection 的子类. 而 Enter 不同, 因为 Enter 选择集中的 DOM 元素在 Enter 选择集创建时还并不存在. Enter 选择集包含的是 DOM 元素的占位符而不是真正的 DOM 元素. 这个占位符其实并没有什么特别的地方, 它就是一个有 `__data__` 属性的 普通 JavaScript 对象而已. 当对 Enter 选择集调用 _selection.append_ 方法时, d3 会进行特殊的处理, 让新插入的元素插入到 group 的父节点中去, 并且用新插入的元素取代占位符.

这也就是为什么我们需要先调用 _selection.selectAll_ 再调用 _selection.data_ : 因为我们要为 Enter 选择集的 group 指定好用于插入新元素的父节点.

### 同时操作 Enter & Update 选择集

#### 注: 此处作者的描述针对的是老版本 api, 本文在此使用新版本 api 进行讲解, 会和原文内容有所不同

通常我们使用 D3 都会分别的处理:

- Enter 选择集 ==> 创建新 DOM 元素, 为新元素跟新属性和样式
- Update 选择集 ==> 跟新属性和样式
- Exit 选择集 ==> 移除 DOM 元素

但是, 对于 Enter 选择集和 Update 选择集的操作, 经常会有重复的部分, 比如更新 DOM 元素的坐标, 更新 DOM 元素的 style 样式.

为了减少这部分冗余的代码, selection 提供了 merge 方法, 使用方法如下:

```javascript
var updateSelection = div
div
  .enter()
  .append('text')
  .text(d => d)
  .merge(updateSelection)
  .attr('x', function(d, i) {
    return i * 10
  })
  .attr('y', 10)
```

之所以 Enter 选择集和 Update 选择集可以 merge 是因为, div.enter().append('text')后, Enter 中的占位符已经被真实的 DOM 元素取代, 因而可以和 Update 选择集合并操作.

### 致谢

感谢: Anna Powell-Smith, Scott Murray, Nelson Minar, Tom Carden, Shan Carter, Jason Davies, Tom MacWright, John Firebaugh. 感谢你们的审阅和建议帮助本文变的更好.

### 进一步阅读

如果想进一步的学习 d3-selection, [阅读源代码](https://github.com/d3/d3-selection)是一个不错的方式. 这里也列出有一些其他人的演讲和文章, 方便进一步阅读:

- [Scott Murray 的 Binding Data](http://alignedleft.com/tutorials/d3/binding-data/)
- [Tom MacWright 的 A Fun, Difficult Introduction to D3](http://macwright.org/presentations/dcjq/)


## 如果觉得不错的话, 不妨关注一下 : )

[github主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)