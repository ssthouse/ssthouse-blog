# 前端杂谈: DOM event 原理

DOM 事件是前端开发者习以为常的东西. 事件的监听和触发使用起来都非常方便, 但是他们的原理是什么呢? 浏览器是怎样处理 event**绑定**和**触发**的呢?

让我们通过实现一个简单的**event 处理函数**, 来详细了解一下.

### 首先, 如何注册 event ?

这个相比大家都很清楚了, 有**三种**注册方式:

1. html 标签中注册

```html
<button onclick="alert('hello!');">Say Hello!</button>
```

2. 给 DOM 节点的 `onXXX` 属性赋值

```javascript
document.getElementById('elementId').onclick = function() {
  console.log('I clicked it!')
}
```

3. 使用 `addEventListener()` 注册事件 (好处是能注册多个 event handler)

```javascript
document.getElementById('elementId').addEventListener(
  'click',
  function() {
    console.log('I clicked it!')
  },
  false
)
```

### event 在 DOM 节点间是如何传递的呢 ?

简单的来说: event 的传递是 **先自顶向下, 再自下而上**

完整的来说: event 的传递分为两个阶段: **capture 阶段** 和 **bubble 阶段**

让我们来看一个具体的例子:

```html
<html>
  <head> </head>

  <body>
    <div id="parentDiv">
      <a id="childButton" href="https://github.com"> click me! </a>
    </div>
  </body>
</html>
```

当我们点击上面这段 html 代码中的 a 标签时. 浏览器会首先计算出从 a 标签到 html 标签的**节点路径** (即: `html => body => div => a`).

然后进入 capture 阶段: 依次触发注册在`html => body => div => a`上的 capture 类型的 click event handler.

到达 a 节点后. 进入 bubble 阶段. 依次出发 `a => div => body => html`上注册的 bubble 类型的 click event handler.

最后当 bubble 阶段到达 html 节点后, 会出发浏览器的默认行为(对于该例的 a 标签来说, 就是跳转到指定的网页.)

从下图我们可以更直观的看到 event 的传递流程.

![FP1SMT.png](https://s1.ax1x.com/2018/11/22/FP1SMT.png)

### 那么, 这样的 event 传递流是如何实现的呢?

#### 让我们来看看 `addEventListener`的代码实现:

```javascript
HTMLNode.prototype.addEventListener = function(eventName, handler, phase) {
  if (!this.__handlers) this.handlers = {}
  if (!this.__handlers[eventName]) {
    this.__handlers[eventName] = {
      capture: [],
      bubble: []
    }
  }
  this.__handlers[eventName][phase ? 'capture' : 'bubble'].push(handler)
}
```

上面的代码非常直观, addEventListener 会根据 eventName 和 phase 将 handler 保存在 `__handler` 数组中, 其中 capture 类型的 handler 和 bubble 类型的 handler 分开保存.

### 接下来到了本文的核心部分: event 是如何触发 handler 的 ?

为了便于理解, 这里我们尝试实现一个简单版本的 event 出发函数 `handler()` (这并不是浏览器处理 event 的源码, 但思路是相同的)

首先让我们理清浏览器处理 event 的流程步骤:

1. 创建 event 对象, 初始化需要的数据
2. 计算触发 event 事件的 DOM 节点到 html 节点的**节点路径** (DOM path)
3. 触发 capture 类型的 handlers
4. 触发绑定在 onXXX 属性上的 handler
5. 触发 bubble 类型的 handlers
6. 触发该 DOM 节点的浏览器默认行为

##### 1. 创建 event 对象, 初始化需要的数据

```javascript
function initEvent(targetNode) {
  let ev = new Event()
  ev.target = targetNode // ev.target 是当前用户真正出发的节点
  ;(ev.isPropagationStopped = false), // 是否停止event的传播
    (ev.isDefaultPrevented = false) // 是否阻止浏览器默认的行为

  ev.stopPropagation = function() {
    this.isPropagationStopped = true
  }
  ev.preventDefault = function() {
    this.isDefaultPrevented = true
  }
  return ev
}
```

##### 2. 计算触发 event 事件的 DOM 节点到 html 节点的**节点路径**

```javascript
function calculateNodePath(event) {
  let target = event.target
  let elements = [] // 用于存储从当前节点到html节点的 节点路径
  do elements.push(target)
  while ((target = target.parentNode))
  return elements.reverse() // 节点顺序为: targetElement ==> html
}
```

##### 3. 触发 capture 类型的 handlers

```javascript
// 依次触发 capture类型的handlers, 顺序为: html ==> targetElement
function executeCaptureHandlers(elements, ev) {
  for (var i = 0; i < elements.length; i++) {
    if (ev.isPropagationStopped) break

    var curElement = elements[i]
    var handlers =
      (currentElement.__handlers &&
        currentElement.__handlers[ev.type] &&
        currentElement.__handlers[ev.type]['capture']) ||
      []
    ev.currentTarget = curElement
    for (var h = 0; h < handlers.length; h++) {
      handlers[h].call(currentElement, ev)
    }
  }
}
```

##### 4. 触发绑定在 onXXX 属性上的 handler

```javascript
function executeInPropertyHandler(ev) {
  if (!ev.isPropagationStopped) {
    ev.target['on' + ev.type].call(ev.target, ev)
  }
}
```

##### 5. 触发 bubble 类型的 handlers

```javascript
// 基本上和 capture 阶段处理方式相同
// 唯一的区别是 handlers 是逆向遍历的: targetElement ==> html

function executeBubbleHandlers(elements, ev) {
  elements.reverse()
  for (let i = 0; i < elements.length; i++) {
    if (isPropagationStopped) {
      break
    }
    var handlers =
      (currentElement.__handlers &&
        currentElement.__handlers[ev.type] &&
        currentElement.__handelrs[ev.type]['bubble']) ||
      []
    ev.currentTarget = currentElement
    for (var h = 0; h < handlers.length; h++) {
      handlers[h].call(currentElement, ev)
    }
  }
}
```

##### 6. 触发该 DOM 节点的浏览器默认行为

```javascript
function executeNodeDefaultHehavior(ev) {
  if (!isDefaultPrevented) {
    // 对于 a 标签, 默认行为就是跳转链接
    if (ev.type === 'click' && ev.tagName.toLowerCase() === 'a') {
      window.location = ev.target.href
    }
    // 对于其他标签, 浏览器会有其他的默认行为
  }
}
```

##### 让我们看看完整的调用逻辑:

```javascript
// 1.创建event对象, 初始化需要的数据
let event = initEvent(currentNode)

function handleEvent(event) {
  // 2.计算触发 event事件的DOM节点到html节点的**节点路径
  let elements = calculateNodePath(event)
  // 3.触发capture类型的handlers
  executeCaptureHandlers(elements, event)
  // 4.触发绑定在 onXXX 属性上的 handler
  executeInPropertyHandler(event)
  // 5.触发bubble类型的handlers
  executeBubbleHandlers(elements, event)
  // 6.触发该DOM节点的浏览器默认行为
  executeNodeDefaultHehavior(event)
}
```

以上就是当用户出发 DOM event 时, 浏览器的**大致**处理流程.

#### propagation && defaultBehavior

我们知道 event 有 `stopPropagation()` 和 `preventDefault()` 两个方法, 他们的作用分别是:

##### `stopPropagation()`

- 停止 event 的传播, 从上面代码的可以看出, 调用 `stopPropagation()` 后, 后续的 handler 将不会被触发.

##### `preventDefault()`

- 不触发浏览器的默认行为. 如: `<a>` 标签不进行跳转,`<form>` 标签点击 submit 后不自动提交表单.

当我们需要对 event handler 执行流进行精细操控时, 这两个方法会非常有用.

### 一些补充~

#### 默认 `addEventListener()` 最后一个参数为 false

注册 event handler 时, 浏览器默认是注册的 bubble 类型 (即默认情况下注册的 event handler 触发顺序为: 从当前节点到 html 节点)

#### `addEventListener()` 的实现是 native code

**addEventListener**是由浏览器提供的 api, 并非 JavaScript 原生 api. 用户触发 event 时, 浏览器会向 `message queue` 中加入 task, 并通过 Event Loop 执行 task 实现回调的效果.

> reference links:
>
> https://www.bitovi.com/blog/a-crash-course-in-how-dom-events-work
>
> https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events

## 想了解更多 前端 / D3.js / 数据可视化 ?

这里是我的博客的 github 地址, 欢迎 star & fork :tada:

[D3-blog](https://github.com/ssthouse/d3-blog)

## 如果觉得本文不错的话, 不妨点击下面的链接关注一下 : )

[github 主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)

## 想直接联系我 ?

邮箱: ssthouse@163.com

微信:

![image-20190729170042724](http://ww4.sinaimg.cn/large/006tNc79gy1g5gteujraqj31040fb78s.jpg)