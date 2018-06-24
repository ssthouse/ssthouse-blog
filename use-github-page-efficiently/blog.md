# 使用 github pages, 快速部署你的静态网页

[Github Pages 官网](https://pages.github.com/)

> Github Pages:
> Websites for you and your projects.
> Hosted directly from your GitHub repository. Just edit, push, and your changes are live.

## 前言

在日常工作中, 我们经常会遇到要做 demo 展示的情况. 做 demo 展示不同于做项目开发, 我们需要的是快速轻便的开发和部署, 而不是完备的一整套开发流程.

下图中肯定不是我们做一个 demo 想要的流程.
![deploy_by_you_own](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/deploy_by_you_own.png)

尤其对于数据可视化工作, 能快速的创建一个 demo 来验证自己的想法, 并且方便的和同伴分享自己作品是非常重要的.
笔者在这里给大家介绍一下我经常用来做 demo 的流程.

## 选择 github pages 的理由

1.  使用 0 成本: github pages 集成在 github 中, 直接和代码管理绑定在一起, 随着代码更新自动重新部署, 使用非常方便.
2.  免费: 免费提供 username.github.io 的域名, 免费的静态网站服务器.
3.  无数量限制: github pages 没有使用的数量限制, 每一个 github repository 都可以部署一个静态网站.

## 使用流程

### 一. 部署静态网页

首先我们介绍一下部署最基础的静态网页, 最终的效果是展示出一个 `Hello, github pages :)` 页面.

#### 创建一个 github 项目

前往 [github 官网](https://github.com), 点击 `New repository` 创建新项目. 填入项目基本信息, 点击 `Create Repository` 确认.

![create_repository](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/create_repository.png)

确认完成后会看到如下页面:

![after_create_repository](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/after_create_repository.png)

#### 首先, 我们为该项目开启 github page 选项

如图, 我们选中 _Setting_ tab
![repository_setting_page](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/repository_setting_page.png)

往下滚动, 找到 _Github Pages_ 选项, 将 _Source_ 改为 `master branch`, 最后点击 `Save` 按钮
![github_page_finish_setting](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/github_page_finish_setting.png)

最后我们会得到一个链接, 通过这个链接, 待会我们就能访问到我们这个 repository 的 github pages 页面.

#### 现在, 我们将代码 clone 到本地, 并创建几个基本文件

![create_sample_files](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/create_sample_files.png)

#### 然后我们加点基础代码

注意这里 _html_ 因为和 _css_ 以及 _js_ 放在同一目录, 所以我们用相对路径来引用

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

#### 将代码更新到 github 仓库

```shell
    cd github-pages-demo
    git add .
    git commit -m "Add simple code"
    git push
```

#### 看看效果
现在我们访问之前开启 github pages选项时获得的 url, 就可以看到效果了
> 注: 代码push上去后可能要过几分钟才会部署好生效

### 二. 使用前端框架时, 如何使用 github pages

这里用 Vue template 做介绍, rect 和 angular 进行类似的配置即可.

### 三. 只可以是静态网站吗?
