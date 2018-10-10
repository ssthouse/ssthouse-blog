### Github Repository 可视化 (D3.js & Three.js)

[demo 链接](https://ssthouse.github.io/github-visualization/)
[github 链接](https://github.com/ssthouse/github-visualization)

效果图 2D:
![demo 2d](https://raw.githubusercontent.com/ssthouse/d3-blog/master/github-visualization/img/visual-github-repo.gif)

// TODO not completed yet!
效果图 3D:
![demo 3d]()

### 为什么要做这样一个网站?

最初想法是因为github提供的repository查看的页面无法一次看到用户的所有 repository, 也无法直观的看到每个repository的量级对比(如 commit数, star数)

所以希望做一个能直观展示用户所有repository的网站.

### 用到了哪些技术?

- 数据来源为Github 提供的 GraphQL API.
- 2D的实现使用到了 D3.js
- 3D的实现使用到了 Three.js
- 页面的搭建为 Vue.js

### 实现的功能有哪些?

2D 和 3D版本均支持:

- 展示用户的 Repository 可视化效果
- 点击 following people 的头像查看他人的 Repository 可视化效果

其中2D可视化支持页面缩放和拖拽 & 单个Repository 的缩放和拖拽, 3D可视化仅支持页面的缩放和拖拽.