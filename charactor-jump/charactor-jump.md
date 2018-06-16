

# 用D3.js, 十分钟实现字符跳动效果

## 注

>本文基于 D3.js 作者 `Mike Bostock` 的 [例子](https://bl.ocks.org/mbostock/3808218)

原文分为三部分, 在这里笔者将其整合为了一篇方便阅读.

本文基于 *D3.js*, 主要使用到了 *d3-selection*  如果对d3-selection的基本使用逻辑不太清楚可以参见 [这篇文章](https://juejin.im/post/5b20a5925188251371243c14)

## 效果图

图中会 `随机` 生成一个字符串, 新生成的字符串和原始字符串进行对比:

- 新出现的字符会以 `绿色` 进入画面
- 被移除的字符会以 `红色` 从画面消失
- 不变的字符以 `黑色` 移动到新的位置

![final demo](https://github.com/ssthouse/d3-blog/raw/master/charactor-jump/final_demo.gif)

## 代码实现

### 1. 字符切换

#### 第一步要完成的效果是: 

- 完成基本字符切换
- 进入时为**绿色**, 不变时为**黑色**
- 被移除的字符直接被从界面中移除

#### 先上代码,  [点我运行](https://jsfiddle.net/ssthouse/5ca13ymb/1/)

代码不长, 接下来一步步分析代码逻辑:

```html
<script>
var alphabet = "abcdefghijklmnopqrstuvwxyz".split("");

var svg = d3.select("svg"),
    width = +svg.attr("width"),
    height = +svg.attr("height"),
    g = svg.append("g").attr("transform", "translate(32," + (height / 2) + ")");

function update(data) {

  // DATA JOIN
  // Join new data with old elements, if any.
  var text = g.selectAll("text")
    .data(data);

  // UPDATE
  // Update old elements as needed.
  text.attr("class", "update");

  // ENTER
  // Create new elements as needed.
  //
  // ENTER + UPDATE
  // After merging the entered elements with the update selection,
  // apply operations to both.
  text.enter().append("text")
      .attr("class", "enter")
      .attr("x", function(d, i) { return i * 32; })
      .attr("dy", ".35em")
    .merge(text)
      .text(function(d) { return d; });

  // EXIT
  // Remove old elements as needed.
  text.exit().remove();
}

// The initial display.
update(alphabet);

// Grab a random sample of letters from the alphabet, in alphabetical order.
d3.interval(function() {
  update(d3.shuffle(alphabet)
      .slice(0, Math.floor(Math.random() * 26))
      .sort());
}, 1500);

</script>
```

首先, 获取svg的宽高信息. 并创建一个 <g> 元素用来承接接下来要创建的字符(<text> 元素)

```javascript
var svg = d3.select("svg"),
    width = +svg.attr("width"),
    height = +svg.attr("height"),
    g = svg.append("g").attr("transform", "translate(32," + (height / 2) + ")");
```

在 `update()` 方法中, 我们传进来一个字符串 **data ** (data由26个字母中随机选出一些组成). 首先我们选中所有的 **text** 元素并将其和**data** 进行绑定: 

```javascript
var text = g.selectAll("text")
    .data(data);
```

然后我们处理**enter**集合 (也就是新加入的字符), 为每一个新字符添加一个 **text** 元素, 并为其添加**class**属性, 使用index为其计算出横纵坐标:

```javascript
text.enter().append("text")                            // 添加svg text元素
      .attr("class", "enter")                          // 添加class属性 (绿色)
      .attr("x", function(d, i) { return i * 32; })    // 按每个字符32像素, 为其计算x坐标
      .attr("dy", ".35em")                             // 设置y轴偏移量
```

接下来同时处理 **enter** 和 **update** 集合, 将字符数据填入 **text** 元素中:

```javascript
  .merge(text)
      .text(function(d) { return d; });
```

最后处理 **exit**集合, 我们暂时直接将其从屏幕中移除:

```javascript
text.exit().remove();
```

我们用 `d3.interval(callback, timeInterval)` 定时调用`update()`来实现字符刷新. 现在我们就得到了下面的效果:

![first step](https://github.com/ssthouse/d3-blog/raw/master/charactor-jump/first-step.gif)



### 2. 为字符设定key值

如果你观察仔细的话, 你可能已经发现第一步实现的效果中: 新加的字符总是出现在最后. 这显然不是我们想要的效果, 新加的字符出现的位置应该是随机的. 那么出现现在这个效果的原因是什么呢?

答案在于我们在绑定字符数据时: `data(data)` 并没有指定data的key值, 那么**d3**会默认使用index作为key, 这样就是为什么新增加的字符总是出现在最后面.

所以我们为字符加入**key**值的**accessor**:

```javascript
  var text = g.selectAll("text")
    .data(data, function(d) { return d; });
```

现在 **key** 值绑定好后, 已经存在的字符在update时text已经不会再改变, 但是坐标需要重新计算, 所以我们做以下改动:

```javascript
text.enter().append("text")
      .attr("class", "enter")
      .attr("dy", ".35em")
      .text(function(d) { return d; })                 // 将text赋值移动到 enter中
    .merge(text)
      .attr("x", function(d, i) { return i * 32; });   // 将坐标计算移动到 merge后 (enter & update)
```



现在我们得到的效果:

![second step](https://github.com/ssthouse/d3-blog/raw/master/charactor-jump/second-step.gif)