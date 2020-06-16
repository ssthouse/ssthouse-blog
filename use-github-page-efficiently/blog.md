# 使用 github pages, 快速部署你的静态网页

[Github Pages 官网](https://pages.github.com/)

> Github Pages:
> Websites for you and your projects.
> Hosted directly from your GitHub repository. Just edit, push, and your changes are live.

## 前言

在日常工作中, 我们经常会遇到要做 demo 展示的情况. 做 demo 展示不同于做项目开发, 我们需要的是快速轻便的开发和部署, 而不是完备的一整套开发流程.

下图肯定不是我们做一个 demo 想要的流程.

![deploy_by_you_own](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/deploy_by_you_own.png)

尤其对于数据可视化工作, 能快速的创建一个 demo 来验证自己的想法, 并且方便的和同伴分享自己作品是非常重要的.
在这里给大家介绍一种笔者经常用来做 demo 的方法: **Github Pages**.

## 选择 github pages 的理由

1.  **使用零成本:** github pages 集成在 github 中, 直接和代码管理绑定在一起, 随着代码更新自动重新部署, 使用非常方便.
2.  **免费:** 免费提供 username.github.io 的域名, 免费的静态网站服务器.
3.  **无数量限制:** github pages 没有使用的数量限制, 每一个 github repository 都可以部署为一个静态网站.

## 使用方法

### 一. 部署静态网页

首先我们介绍一下部署最基础的静态网页, 最终的效果是展示出一个 `Hello, github pages :)` 页面.

> demo 地址: [github repository](https://github.com/ssthouse/github-pages-demo/settings)
>
> github page 地址: [https://ssthouse.github.io/github-pages-demo/](https://ssthouse.github.io/github-pages-demo/)

#### 1.1 创建一个 github 项目

前往 [github 官网](https://github.com), 点击 `New repository` 创建新项目. 填入项目基本信息, 点击 `Create Repository` 确认.

![create_repository](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/create_repository.png)

确认完成后会看到如下页面:

![after_create_repository](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/after_create_repository.png)

#### 1.2 为 repository 开启 github page 选项

如图, 我们选中 _Setting_ tab

![repository_setting_page](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/repository_setting_page.png)

往下滚动, 找到 _Github Pages_ 选项, 将 _Source_ 改为 `master branch`, 最后点击 `Save` 按钮
![github_page_finish_setting](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/github_page_finish_setting.png)

最后我们会得到一个链接, 通过这个链接, 待会我们就能通过这个链接访问到该项目的 github pages 页面.

#### 1.3 代码 clone 到本地, 并创建几个基本文件

![create_sample_files](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/create_sample_files.png)

#### 1.4 添加基础代码

注意这里 _html_ 因为和 _css_ 以及 _js_ 放在同一目录, 所以我们用相对路径来引用.

代码逻辑很简单, 就是在页面加载好后在页面中添加 `Hello, github pages :)` 这段文字.

**index.html**

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Github Page demo</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" type="text/css" media="screen" href="main.css" />
    <script src="index.js"></script>
</head>
<body>
    <div id="main-content">

    </div>
</body>
</html>
```

**index.js**

```javascript
window.onload = function() {
  document.getElementById('main-content').innerHTML = 'Hello, github pages :)'
}
```

**main.css**

```css
#main-content {
  font-size: 36px;
  font-weight: bold;
  padding: 16px;
}
```

#### 1.5 将代码更新到 github 仓库

```shell
    cd github-pages-demo
    git add .
    git commit -m "Add simple code"
    git push
```

#### 1.6 看看效果

现在我们访问之前开启 github pages 选项时获得的 url, 就可以看到效果了

> 注: 代码 push 上去后, 可能要过几分钟才会部署好生效

效果如图 :arrow_down:

![simplest demo](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/github_page_simple_result.png)

### 二. 使用前端框架时, 如何使用 github pages

如今我们创建一个前端项目的时候, 已经很少手动创建 index.html, main.js , main.css 这文件了, 通常我们都会选择一个前端框架, 并使用相应的 command line tool 来初始化项目.

这里笔者用 Vue 的 webpack 项目 做介绍, rect 和 angular 进行类似的配置即可.

#### 创建项目

首先我们用 vue-cli 创建一个 webpack 管理的 vue 项目 ([点我安装 vue-cli](https://github.com/vuejs-templates/webpack)),

```shell
vue init webpack github-page-vue-demo
```

然后我们进入项目, 看看目录结构:

![simplest demo](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/vue_demo_file_tree.png)

可以看到 config 目录中有三个文件:

```shell
config                     //  配置目录
├── dev.env.js             // 用于development模式的环境变量
├── index.js               // 用于配置 `dev` 模式和 `prod` 模式的 webpack config 文件
└── prod.env.js            // 用于product模式的环境变量
```

这里我们需要配置的就是 **index.js** 文件, 先看看该文件内容 (这里将不相关的代码用...略过), 其中我们需要关注的是 _module.exports_ 的 _build_ 属性, 我们将在这里配置 _webpack build_ 时生成文件的路径

```javascript
module.exports = {
  dev: {
      ...
  },

  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../dist/index.html'),

    // Paths
    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    ...
  }
}
```

可以看到图中主要配置了 _index_ 文件和 _assets_ 文件的路径. 默认执行 `yarn run build` 后 webpack 会将项目打包到项目根目录的 ./dist 文件夹, 如图:

![default build result](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/vue_default_build_result.png)

#### 修改编译配置

但是 _github pages_ 默认只能识别项目根目录的 _index_ 文件, 如果我们想要让 _github pages_ 识别到我们 _build_ 出来的文件应该怎么办呢?

你可能会想到直接将 _dist_ 文件夹中 _build_ 生成的文件直接复制到项目的根目录, 这确实是个办法. 但是这样的话, 我们每次 build 完, 都需要手动复制一边文件, 这无疑增加了很多重复性的工作.

我们可以通过修改默认的配置来达到项目 _build_ 的文件直接生成到项目根目录的目的, 像这样:

```javascript
module.exports = {
  dev: {
      ...
  },

  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../index.html'),  //之前是 '../dist/index.html'

    // Paths
    assetsRoot: path.resolve(__dirname, '../'),  // 之前是 '../dist'
    assetsSubDirectory: 'static',
    assetsPublicPath: './',    // 之前是 '/'
    ...
  }
}
```

所做的改动就是去掉了默认的 _dist_ 目录, 并且将 _assets_ 的引用路径从 _绝对路径_ 改为了 _相对路径_.

去掉 _dist_ 目录是为了将 _build_ 的 _target_ 路径改为项目根目录. 改为相对路径是因为在部署到 _github pages_ 的时候, 我们的域名是 `https://username.github.io/repositoryName`, 也就是说我们的项目是部署在子域名上的, 如果用绝对路径会导致 _assets_ 文件 404.

这样修改完后我们又发现一个问题: 在这样的配置下, build 结束生成的 index.html 文件会覆盖原有的 template index.html 文件, 并且根目录多了一个 static 文件夹, 很容易让人对这个文件夹的作用产生疑惑. 有没有更好的解决办法呢 ?

让我们回到 github page 的 setting 页面:
![default build result](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/github_page_setting_use_docs.png)

可以看到这里有一个选项是 `master branch /docs folder` . 当前状态是不可选的, 原因是我们的项目代码里面没有 `/docs` 目录.

这个选项的意思是 github page 可以识别我们项目中的 docs 文件夹, 并在这个文件夹中寻找 index 文件进行部署. 选中这个选项后, 我们只需要将之前 webpack 默认的 _dist_ 文件夹改为 _docs_ 文件夹即可, 修改后配置如下:

```javascript
module.exports = {
  dev: {
      ...
  },

  build: {
    // Template for index.html
    index: path.resolve(__dirname, '../docs/index.html'),  //之前是'../dist/index.html'

    // Paths
    assetsRoot: path.resolve(__dirname, '../docs'),  // 之前是 '../dist'
    assetsSubDirectory: 'static',
    assetsPublicPath: './',    // 之前是 '/'
    ...
  }
}
```

完成以上的修改后, 我们再次运行 `yarn run build`, 你会发现根目录下多了一个 _docs_ 文件夹, 里面承载了 _index_ 文件和 _static_ 文件夹. 我们讲 _docs_ 目录以及其下的文件全部加入 git 版本管理, 并 push 到 github.

再次来到 该项目的 _github page setting_ 页面, 这时 **master branch /docs folder** 就变成可选中的了. 我们选中这个选项, 并保存设置.

过两分钟左右, 我们再次访问我们项目的 _github page url_, 就会发现项目已经部署成功了 :tada:

#### 自定义 template

在上一步中, 我们自己配置了一个 基于 `Vue + Webpack` 的项目配置. 但如果每次我们想创建一个 demo, 我们都要修改一遍配置的话, 还是很花费时间. 特别是忘记了配置步骤的话, 还得再查找之前的配置方法.

一个比较好的解决方案是创建一个基础的 template repository, 在下次需要创建一个 demo 项目的时候, 直接 fork 过来, 基于这个项目着手开发即可.

比如我经常使用的技术栈是: Vue + D3.js + Webpack, 我就给自己创建了一个这样的 template, 并做好 github page 的配置. 下次要做数据可视化 demo 的时候, 直接 fork 这个 repository 进行开发, 节省了许多项目配置的时间, 如果有和我类似技术栈的小伙伴, 不妨一试:

> Vue + D3.js + Webpack (github page ready)
> [github 地址](ttps://github.com/ssthouse/vue-d3-template)

### 三. 只可以是静态网站吗?

github page 可以非常方便的部署静态网页. 但是因为没有数据库, 无法存储数据, 也就没办法实现较为复杂的业务逻辑. 但是 github page 真的就只能做做简单的纯静态网站了吗?

其实, 如今前后端分离, 只要有后端 api, 前端完全可以单独开发和部署.

如果我们的网站已有现成的后端 api, 使用 github page, 再加上 javascript 调用后端 api, 完全可以实现所有的业务逻辑.

如果没有现有后端 api, 但是想要实现简单的后端逻辑的话, 这里推荐一个笔者常用的小 tip:

> 使用 [leancloud](https://leancloud.cn/) 作为对象存储的后端数据库
> 前端调用 leancloud api 实现所有的业务逻辑

如果感兴趣不妨看看我的这个项目: [github visualization](https://github.com/ssthouse/github-visualization)

这个项目就是部署在 github pages 上, 并使用 leancloud api 实现的 **用户访问信息记录** 的功能.
具体的 leancloud 使用细节, 请参见 [leancloud 文档](https://leancloud.cn/docs/sdk_setup-js.html)

### 最后

github pages 可以说是为我节省了许多的时间, 为我开发 demo 展示提供了非常多的便利. 推荐没有使用过的小伙伴试试, 相信也会给你们带来很多便利.

## 如果觉得这篇文章不错的话, 不妨关注一下 : )

[github 主页](https://github.com/ssthouse)

[知乎专栏](https://zhuanlan.zhihu.com/c_196857379)

[掘金](https://juejin.im/user/57bc46c8efa631005a891573/posts)

## 想直接联系我 ?

邮箱: ssthouse@163.com

微信:

![wechat](https://github.com/ssthouse/d3-blog/raw/master/img/QR_300px.png)