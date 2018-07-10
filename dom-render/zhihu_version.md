## 前言

最近在看 _Secrets of the JavaScript Ninja_, 书中第二章讲到 DOM 的构建流程.

记得我之前也为理解 DOM 构建流程查阅过数次资料, 虽然每次查阅完都觉得 DOM 构建流程很简单, 看完便懂, 但是懂了又忘还是让人有些头疼.

为了给自己加深印象, 也为了为大家提供一个可视化的理解 DOM 构建过程的方式, 笔者制作了一个简单的网页来动态演示 DOM 构建过程. 希望能给大家带来一些帮助.

## 效果

### 在线查看

[在线 demo (请使用 pc 访问)](https://ssthouse.github.io/visual-explain/#/list/domRender)

### 前进, 后退

网页展示了一个简单的 HTML 页面的 DOM 渲染过程. 用户点击**前进**,**后退**按钮时, 页面左侧会显示出当前的 HTML 代码, 右侧则会显示出实时的 DOM 结构图:

![forward and backword](https://user-gold-cdn.xitu.io/2018/7/10/1648262e352622c8?w=1840&h=911&f=gif&s=178001)

### 自动播放

点击 `Auto Play` 按钮, 页面会自动播放页面的整个构建过程:

![auto play](https://user-gold-cdn.xitu.io/2018/7/10/1648262e36e8704e?w=1840&h=911&f=gif&s=205016)

## DOM 构建过程

### DOM 元素的作用 & 基本构建过程:

这里直接引用一下原文:

> The goal of this page-building phase is to set up the UI of a web application, and this is done in two distinct steps:
> 1 Parsing the HTML and building the Document Object Model (DOM)
> 2 Executing JavaScript code
> Step 1 is performed when the browser is processing HTML nodes, and step 2 is > performed whenever a special type of HTML element—the script element (that > contains or
> refers to JavaScript code)—is encountered. During the page-building phase, the browser
> can switch between these two steps as many times as necessary.

浏览器 **页面构建** 步骤的目的是为 UI 渲染做准备, **页面构建**是由下面两部分购成的:

- 解析 HTML 节点, 并且构建 DOM 元素
- 执行 JavaScript 代码

其中第一步在浏览器解析到 HTML 节点时执行, 第二步在解析到 **script** 标签时执行. 在页面构建的过程中, 以上两步可以无数次的交替执行.

> It’s important to emphasize that, although the HTML and the DOM are closely
> linked, with the DOM being constructed from HTML, they aren’t one and the same.
> You should think of the HTML code as a blueprint the browser follows when > constructing the initial DOM—the UI—of the page. The browser can even fix > problems that it finds with this blueprint in order to create a valid DOM.

需要注意的是, 虽然 HTML 和 DOM 两者关系紧密(DOM 是由 HTML 文件构建而来), 但他们并不是相同的. 你应该将 HTML 看作是浏览器用来渲染 DOM 元素(页面 UI) 的蓝图. 浏览器甚至可以可以修复这个蓝图(HTML)中的问题, 并构建出有效的 DOM.

### 下面用可视化讲解中的步骤依次讲解:

#### 首先看看我们想要渲染的 HTML 代码:

```html
<!DOCTYPE html>
<html>
    <head>
      <title>Web app lifecycle</title>
      <style>
          #list { color: green;}
          #second { color: red;}
      </style>
    </head>
    <body>
        <h1>head one</h1>
        <ul id="list"></ul>
        <script>
            var liElement = document.createElement("li");
            liElement.textContent = 'I am a li';
            document.getElementById('list').appendChild(liElement);
        </script>
    </body>
</html>
```

#### 接下来按照浏览器的构建顺序来看:

首先浏览器遇到下面这段代码, 解析出 **html** 节点作为 DOM 的根节点:

```html
<!DOCTYPE html>
<html>
```

![step 1](https://user-gold-cdn.xitu.io/2018/7/10/1648262e37670b53?w=1841&h=495&f=png&s=27300)

接下来是 `<head>` 标签, 将其放置在 **html** 节点下:

![step 2](https://user-gold-cdn.xitu.io/2018/7/10/1648262e3783b141?w=1843&h=456&f=png&s=32495)

继续解析, 遇到 `<title>` 标签, 因为其是 `<head>` 的子标签, 故将其放置在 **head** 节点下.

![step 3](https://user-gold-cdn.xitu.io/2018/7/10/1648262edfa8db7d?w=1845&h=584&f=png&s=40639)

然后是 `<style>` 标签, 类似的, 放在 **head** 节点下:

```html
<style>
    #list { color: green;}
    #second { color: red;}
</style>
```

![step 4](https://user-gold-cdn.xitu.io/2018/7/10/1648262e37720416?w=1834&h=617&f=png&s=58456)

接下来解析到 `<body>` 标签, 因其为 `<html>` 的子标签, 故将其放置在 **html** 节点下:

![step 5](https://user-gold-cdn.xitu.io/2018/7/10/1648262e434ecae4?w=1846&h=587&f=png&s=67204)

然后是 `<h1>` 标签, 放置在 **body** 节点下:

![step 6](https://user-gold-cdn.xitu.io/2018/7/10/1648262e94e5fc1d?w=1838&h=596&f=png&s=72915)

继续, `<ul>` 标签, 同样的, 放置在 **body** 节点下:

![step 7](https://user-gold-cdn.xitu.io/2018/7/10/1648262eb8455c62?w=1843&h=721&f=png&s=105314)

接下来, 浏览器遇到了 `<script>` 标签, 根据前面的知识我们知道, 浏览器会停下来并执行`<script>` 中的代码. 所以下面这段代码会被立即执行:

```html
<script>
    var liElement = document.createElement("li");
    liElement.textContent = 'I am a li';
    document.getElementById('list').appendChild(liElement);
</script>
```

这段代码的逻辑是: 向 **id** 为 **list** 的 DOM 节点添加一个 **li** 作为子元素, 故执行完成后 DOM 树会是这样:

![step 7](https://user-gold-cdn.xitu.io/2018/7/10/1648262eb8455c62?w=1843&h=721&f=png&s=105314)

最后, 浏览器会解析到 `<body/></html>` 等标签, 结束解析过程. 最终我们得到的 DOM 结构如图:

![step 9](https://user-gold-cdn.xitu.io/2018/7/10/1648262ecddf8c4f?w=1847&h=701&f=png&s=109034)

## 后记

预计我会将 _Secrets of the JavaScript Ninja_ 后续章节中的一些知识点也通过类似的方式进行可视化. 

如果你也有希望能做成**可视化讲解**的: **知识点**, **算法**, **技术原理**, 欢迎在下面留言与我交流, 期待大家的反馈 :)

演示页面用到的技术为: Vue, D3.js, 欢迎 fork & star
[Github 地址](https://github.com/ssthouse/visual-explain)

## 想继续了解 D3.js

这里是我的 _D3.js_ 、 _数据可视化_ 相关博客的 github 地址, 欢迎 fork & star :tada:

[D3-blog](https://github.com/ssthouse/d3-blog)

## 如果觉得不错的话, 不妨点击下面的链接关注一下 : )

[github 主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)
