

# 用D3.js 十分钟实现字符跳动效果

## 注

>本文基于 D3.js 作者 `Mike Bostock` 的 [例子](https://bl.ocks.org/mbostock/3808218)

原文分为三部分, 在这里笔者将其整合为了一篇方便阅读.

该效果基于 *D3.js*, 主要使用到了 *d3-selection*.  如果对d3-selection的基本使用逻辑不太清楚可以参见 [这篇文章](https://juejin.im/post/5b20a5925188251371243c14).

## 效果图

- Step1 首先代码会随机生成一个字符串, 该字符以绿色进入画面.

- Step2 接下来, 代码随机生成一个新字符串, 新生成的字符串会和原始字符串进行对比:

  2.1 新字符串和原始字符串中相同的字母,会变成黑色保留在屏幕上

  2.2 原始字符串中有, 而新字符串中没有的字母, 会变成红色,被移除屏幕

  2.3 新字符串中有, 而原始字符串中没有的字母, 会变成绿色,被添加到屏幕

![final demo](https://github.com/ssthouse/d3-blog/raw/master/charactor-jump/final_demo.gif)

## 代码实现

### 1. 字符切换

#### 第一步要完成的效果是: 

- 完成基本字符切换
- 进入时为**绿色**, 不变时为**黑色**
- 被移除的字符直接被从界面中移除

#### 先上代码,  [点我运行](https://jsfiddle.net/ssthouse/5ca13ymb/1/)

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

代码不长, 接下来一步步分析代码逻辑:

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

**现在我们的代码长这样, [点我在线运行](https://jsfiddle.net/ssthouse/bo7guhm2/):**

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
    .data(data, function(d) { return d; });

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
      .attr("dy", ".35em")
      .text(function(d) { return d; })
    .merge(text)
      .attr("x", function(d, i) { return i * 32; });

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



**现在我们得到的效果:**

![second step](https://github.com/ssthouse/d3-blog/raw/master/charactor-jump/second-step.gif)



### 3.添加动画

动画能让我们更好的观察元素的变化过程和状态, 给不同状态的元素赋予不同的动画可以更直观的展示我们的数据.

现在我们给字符的变化增加动画效果, 并把字符移除时的颜色变化补上.

首先我们定义一个 **transition**变量, 并设置其动画间隔为 750

```javascript
var t = d3.transition()
      .duration(750);
```

在D3中使用动画非常简单, 在动画前指定元素的一些属性, 调用动画, 再指定动画后的一些属性. D3会自动根据插值器生成动画. 

下面的代码对于离开界面的字符(exit selection)进行了处理:

```javascript
text.exit()
      .attr("class", "exit")          // 动画前, 设置class属性, 字体变红
    .transition(t)                    // 设置动画
      .attr("y", 60)                  // 设置y坐标, 使元素向下离开界面 (y: 0 => 60)
      .style("fill-opacity", 1e-6)    // 设置透明度, 使元素渐变消失 (opacity: 1 => 0)
      .remove();                      // 最后将其移出界面
```

同样的, 我们对 **enter** 和 **update** selection进行处理:

```javascript
// UPDATE old elements present in new data.
  text.attr("class", "update")
      .attr("y", 0)
      .style("fill-opacity", 1)
    .transition(t)
      .attr("x", function(d, i) { return i * 32; });

  // ENTER new elements present in new data.
  text.enter().append("text")
      .attr("class", "enter")
      .attr("dy", ".35em")
      .attr("y", -60)
      .attr("x", function(d, i) { return i * 32; })
      .style("fill-opacity", 1e-6)
      .text(function(d) { return d; })
    .transition(t)
      .attr("y", 0)
      .style("fill-opacity", 1);
```

**最终我们的代码长这样, [点我运行](https://jsfiddle.net/ssthouse/h9mp7a34/)**

```javascript
<script>

var alphabet = "abcdefghijklmnopqrstuvwxyz".split("");

var svg = d3.select("svg"),
    width = +svg.attr("width"),
    height = +svg.attr("height"),
    g = svg.append("g").attr("transform", "translate(32," + (height / 2) + ")");

function update(data) {
  var t = d3.transition()
      .duration(750);

  // JOIN new data with old elements.
  var text = g.selectAll("text")
    .data(data, function(d) { return d; });

  // EXIT old elements not present in new data.
  text.exit()
      .attr("class", "exit")
    .transition(t)
      .attr("y", 60)
      .style("fill-opacity", 1e-6)
      .remove();

  // UPDATE old elements present in new data.
  text.attr("class", "update")
      .attr("y", 0)
      .style("fill-opacity", 1)
    .transition(t)
      .attr("x", function(d, i) { return i * 32; });

  // ENTER new elements present in new data.
  text.enter().append("text")
      .attr("class", "enter")
      .attr("dy", ".35em")
      .attr("y", -60)
      .attr("x", function(d, i) { return i * 32; })
      .style("fill-opacity", 1e-6)
      .text(function(d) { return d; })
    .transition(t)
      .attr("y", 0)
      .style("fill-opacity", 1);
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

**最终效果:**

![final demo](https://github.com/ssthouse/d3-blog/raw/master/charactor-jump/final_demo.gif)





## 如果觉得不错的话, 不妨点击下面的链接关注一下 : )

[github主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)

## 想直接联系我 ?

邮箱: ssthouse@163.com

微信:

![wechat](https://github.com/ssthouse/d3-blog/raw/master/img/QR_300px.png)