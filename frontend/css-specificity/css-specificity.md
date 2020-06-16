# 前端杂谈: CSS 权重 (Specificity)

**css 权重**想必大家都听说过, 一些简单的规则大部分人也都知道:

- 较长的 css selector 权重会大于较短的 css selector

- id selector 权重高于 class selector.

但是**具体规范**是什么? 浏览器是按照什么标准来判定不同选择器的权重的呢?

让我们来看一下[官方文档](https://www.w3.org/TR/CSS2/cascade.html#specificity)是怎么说的~

### 第一个关键词: **_Specificity_**

> **Specificity** is the means by which browsers decide which CSS property values are the most relevant to an element and, therefore, will be applied. Specificity is based on the matching rules which are composed of different sorts of [CSS selectors](https://developer.mozilla.org/en/CSS/CSS_Reference#Selectors)

官方文档中用 **Specificity: 特异性** 来表示一个 css selector 和其元素的相关性. 相关性越强, 即表示表示其权重最高.

#### 那么问题来了, _Specificity_ 是如何被比较的呢?

> Specificity is a weight that is applied to a given CSS declaration, determined by the number of each [selector type](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity#Selector_Types) in the matching selector.

Specificity 是由 selector 中 不同 **selector type** 的数目决定的.

### 第二个关键词: _Selector Type_

根据 [W3 标准](https://www.w3.org/TR/CSS2/cascade.html#specificity)中的规定, css selector 分为 4 种 type: `a, b, c, d`. 他们的数目计算规则为:

- **a:** 如果 css 属性声明是写在 `style=“”` 中的, 则数目为 1, 否则为 0
- **b:** id 选择器的数目
- **c:** class 选择器, 属性选择器(如 `type=“text”`), 伪类选择器(如: `::hover`) 的数目
- **d:** 标签名(如: `p`, `div`), 伪类 (如: `:before`)的数目

在比较不同 css selector 的权重时, 按照 **a => b => c => d** 的顺序进行比较.

由第一个 selector type a 的计算规则可知: 写在 html 代码 style 属性中的 css 相较写在 css 选择器中的 css 属性具有最高的优先级.

而 id selector (b) 相较 class selector (c) 有更高的优先级. 这也和我们平时的经验相吻合.

#### 还有一些 css 选择器你没提, 它们该怎么计算权重?

除了上面 Specificity 计算规则中的 css 选择器类型, 还有一些选择器如: `*`, `+`, `>`,`:not()` 等. 这些选择器该如何计算其权重呢?

**答案是这些选择器并不会被计算到 css 的权重当中 :)**

有一个需要特别注意一下的选择器: `:not()`, 虽然它本身是不计权重的, 但是写在它里面的 css selector 是需要计算权重的.

#### 如果 _a,b,c,d_ 算完都一样, 怎么办?

默认行为是: 当 specificity 一样时, 最后声明的 css selector 会生效.

#### 如果我重复同样的 css selectory type, 权重会增加吗?

让我们来做个实验, 我们声明一个 html 节点:

```html
<div>
  <div id="testId" class="testClass"><span>test div</span></div>
</div>
```

在 css 中我们添加两个选择器:

```css
.testClass.testClass {
  background-color: red;
}
.testClass {
  background-color: black;
}
```

如果重复的 css selector 会被忽略的话, 按照前面的规则, 最后声明的 css selector 会生效, 所以 这个 div 节点背景色应该是黑色. 让我们看看结果:

![FSvsHI.png](https://s1.ax1x.com/2018/11/19/FSvsHI.png)

结果我们得到的是一个红色的 div, 也就是说 `.testClass.testClass` 高于 `.testClass`. 所以结论是: 重复的 css selector, 其权重会被重复计算.

### 关于 `!important`:

按照 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)的说法, `!important` 是不在 css 选择器的权重计算范围内的, 而它之所以能让 css 选择器生效是因为浏览器在遇见 `!important` 时会进行特殊的判断. 当多个 `!important` 需要进行比较时, 才会计算其权重再进行比较.

通常来说, 不提倡使用 `!important`. 如果你认为一定要使用, 不妨先自检一下:

> - **总是**先考虑使用权重更高的 css 选择器, 而不是使用 `!important`
> - **只有**当你的目的是覆盖来自第三方的 css(如: Bootstrap, normalize.css)时, 才在页面范围使用 `!important`
> - **永远不要** 在你写一个第三方插件时使用 `!important`
> - **永远不要**在全站范围使用 `!important`

### 一些误导的信息

我在搜索关于 css 权重的资料时, 看到了下面这张图, 看似十分有道理, 但其实是**错误的!**![css_weight1](https://ws3.sinaimg.cn/large/006tNbRwly1fxbfiuanv1j30fu046glm.jpg)

让我们来做一个简单的测试:

按照图片中的计算公式: 如果一个 css 选择器包含**11 个 class selector type**, 另一个选择器是**1 个 id selector type**. 那么 class 选择器的权重会高于 id 选择器的权重. 让我们来试一试:

```css
.testClass.testClass.testClass.testClass.testClass.testClass
  .testClass.testClass.testClass.testClass.testClass {
  background-color: red;
}
#testId {
  background-color: black;
}
```

让我们看看结果:

[![FSvfgg.png](https://s1.ax1x.com/2018/11/19/FSvfgg.png)](https://imgchr.com/i/FSvfgg)

结果显示还是 **id 选择器**权重更高.

虽然我们在实际编码过程中很少会出现 10 多个 class 的情况, 但万一出现了, 知道权重真正的**计算**和**比较**规则, 我们才能正确的处理~

## 想了解更多 前端 和 数据可视化 ?

这里是我的 _D3.js_ 、 _数据可视化_ 的 github 地址, 欢迎 star & fork :tada:

[D3-blog](https://github.com/ssthouse/d3-blog)

## 如果觉得本文不错的话, 不妨点击下面的链接关注一下 : )

[github 主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)

## 想直接联系我 ?

邮箱: ssthouse@163.com
