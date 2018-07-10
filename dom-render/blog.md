# 可视化讲解 DOM 渲染过程

## 前言

最近在看 <<Secrets of the JavaScript Ninja>>, 书中第二章讲到 DOM 的渲染流程. 记得我之前也为理解 DOM 渲染流程查阅过数次资料, 虽然每次查阅完都觉得 DOM 渲染流程很简单, 看完便懂, 但是懂了又忘还是让人有些头疼.

所以为了给自己加深印象, 也为了为大家提供一个可视化的理解 DOM 渲染过程的方式, 笔者制作了一个简单的网页来动态演示 DOM 渲染过程. 希望能给大家带来些帮助.

## 效果

这个网页展示了一个简单的 HTML 页面的 DOM 渲染过程, 用户可以点击前进,后退按钮, 页面左侧会显示出当前的 HTML 代码, 右侧则会显示出实时的 DOM 元素的树状结构:

### 点击 前进, 后退按钮

![forward and backword](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/forward_and_backword.gif)

### 自动播放

![auto play](https://raw.githubusercontent.com/ssthouse/d3-blog/master/dom-render/img/autoplay.gif)

## DOM 渲染过程

DOM 渲染过程其实非常简单, 当浏览器拿到 HTML 文件时, 按照从上往下的顺序依次渲染. 在渲染过程中可能会遇到以下几种情况:

- 遇到普通的 DOM 元素标签 ==> 创建 DOM 元素
- 遇到 `<head>` 或 `<style>` 标签, 正常解析, 不创建 DOM 元素
- 遇到 `<script>` 标签, 执行 script 标签中代码, 这一步中的 JS 代码可以访问在该`<script>` 标签之前创建好的 DOM 元素, 并且可以动态的向起添加或删除 DOM 元素

下面用可视化讲解中的步骤依次讲解:

####