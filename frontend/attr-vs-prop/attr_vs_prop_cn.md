# 前端杂谈: Attribute VS Property

### 第一个问题: 什么是 attribute & 什么是 property ?

**attribute** 是我们在 **html** 代码中经常看到的键值对, 例如:

```html
<input id="the-input" type="text" value="Name:" />
```

上面代码中的 input 节点有三个 attribute:

- id : the-input
- type : text
- value : Name:

**property** 是 attribute 对应的 DOM 节点的 对象属性 (Object field), 例如:

```javascript
HTMLInputElement.id === 'the-input'
HTMLInputElement.type === 'text'
HTMLInputElement.value === 'Name:'
```

### 第二个问题:

从上面的例子来看, 似乎 attribute 和 property 是相同的, 那么他们有什么区别呢?

让我们来看另一段代码:

```html
<input id="the-input" type="typo" value="Name:" /> // 在页面加载后,
我们在这个input中输入 "Jack"
```

注意这段代码中的 type 属性, 我们给的值是 **typo**, 这并不属于 input 支持的 type 种类.

让我们来看看上面这个 input 节点的 attribute 和 property:

```javascript
// attribute still remains the original value
input.getAttribute('id') // the-input
input.getAttribute('type') // typo
input.getAttribute('value') // Name:

// property is a different story
input.id // the-input
input.type //  text
input.value // Jack
```

可以看到, 在 attribute 中, 值仍然是 html 代码中的值. 而在 property 中, type 被自动修正为了 **text**, 而 value 随着用户改变 input 的输入, 也变更为了 **Jack**

这就是 attribute 和 Property 间的区别:

attribute 会始终保持 html 代码中的初始值, 而 Property 是有可能变化的.

其实, 我们从这两个单词的名称也能看出些端倪:

**attribute** 从语义上, 更倾向于不可变更的

而 **property** 从语义上更倾向于在其生命周期中是可变的

## Attribute or Property 可以自定义吗?

先说结论: **attribute 可以** **property 不行**

我们可以尝试在 html 中自定义 attribute:

```html
<input value="customInput" customeAttr="custome attribute value" />
```

然后我们尝试获取自定义的属性:

```javascript
input.getAttribute('customAttr') // custome attribute value
input.customAttr // undefined
```

可以看到, 我们能够成功的获取自定义的 attribute, 但是无法获取 property.

其实不难理解, DOM 节点在初始化的时候会将**html 规范**中定义的 attribute 赋值到 property 上, 而自定义的 attribute 并不属于这个氛围内, 自然生成的 DOM 节点就没有这个 property.

### 一些补充

需要注意, 有一些特殊的 attribute, 它们对应的 Property 名称会发生改变, 比如:

- for (attr) => htmlFor (prop)
- class (attr) => className (prop)

(如果我们追到 DOM 的源码中, 应该是能列出一份清单的: 哪些 attribute 的名称会对应到哪些 Property, 感兴趣不妨试试)

关于 attribute 和 property 两者之间的差别, stackoverflow 上有一些很有意思的讨论:

https://stackoverflow.com/a/6377829/5033286

其中有些人认为 attribute 的值表示的是 defaultValue, 而 DOM 节点的 Property 则是另一回事. 比如: check (attr) 对应的是 defaultChecked (prop), value(attr) 对应的应该是 defaultValue (prop)

关于我们在 attribute 中 value 的限制 (如 input 的 type 有那些有效值), 可以参考这个链接:

https://www.w3.org/TR/html5/infrastructure.html#reflect



## 想了解更多 D3.js 和 数据可视化 ?

这里是我的 _D3.js_ 、 _数据可视化_ 的 github 地址, 欢迎 star & fork :tada:

[D3-blog](https://github.com/ssthouse/d3-blog)

## 如果觉得本文不错的话, 不妨点击下面的链接关注一下 : )

[github 主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)

## 想直接联系我 ?

邮箱: ssthouse@163.com

微信:

![image-20190729170042724](http://ww4.sinaimg.cn/large/006tNc79gy1g5gteujraqj31040fb78s.jpg)