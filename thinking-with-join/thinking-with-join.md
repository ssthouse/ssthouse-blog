

# 以 ***Join*** 的方式来思考D3.js



## 声明

原文链接: [https://bost.ocks.org/mike/join/]( https://bost.ocks.org/mike/join/)



打个比方, 你想用D3画一个 `散点图` , 并且你想创建一个 SVG元素来可视化你的数据. 你会惊讶的发现: D3居然没有直接创建多个DOM元素的方法! 怎么回事?



当然, D3有 `append` 方法, 你可以用来创建单个元素. 比如:

```javascript
svg.append("circle")
    .attr("cx", d.x)
    .attr("cy", d.y)
    .attr("r", 2.5);
```



但这只是一个圆, 如果你想要创建很多个圆(每一个圆代表一个数据点). 你可能会想到用一个for循环来实现 ? 这是非常直观的想法, 这个想法并没有什么错, 但是在这之前不妨看看D3中是如何实现创建多个元素的:

```javascript
svg.selectAll("circle")
  .data(data)
  .enter().append("circle")
    .attr("cx", function(d) { return d.x; })
    .attr("cy", function(d) { return d.y; })
    .attr("r", 2.5);
```

上面这段代码完美的实现了你想要的效果: 为没一个数据点创建了一个 circle, 用数据点的 `x` 和 `y` 属性作为circle的坐标. 但这段代码里面的 `selectAll("circle")` 是什么意思? 我们为什么要 `select` 我们知道当前并不存在的 circle, 还用这个方法的返回值去创建新的元素?



这段代码的思想是: **不要告诉D3如何去做, 而是告诉D3你想要的效果**. 你想要circle元素和数据一一对应, 那么你就不应该告诉D3去创建circle元素, 而是告诉D3: .`selectAll("circle")` 得到的circle集合应该和 `.data(data)` 一一对应. 这个思想就叫做 ***Join***



![Join 模型](https://raw.githubusercontent.com/ssthouse/d3-blog/master/thinking-with-join/thinking_with_join.png)

