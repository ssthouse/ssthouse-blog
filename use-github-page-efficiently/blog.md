# 使用 github pages, 快速部署你的静态网页

## 前言

在日常工作中, 我们经常会遇到要做 demo 展示的情况. 做 demo 展示不同于做项目开发, 我们需要的是快速轻便的开发和部署, 而不是完备的一整套开发流程.
尤其对于数据可视化工作, 能快速的创建一个 demo 来验证自己的想法, 并且方便的和同伴分享自己作品是非常重要的.
笔者在这里给大家介绍一下我经常用来做 demo 的流程.

## 选择 github pages 的理由

1.  使用 0 成本: github pages 集成在 github 中, 直接和代码管理绑定在一起, 随着代码更新自动重新部署, 使用非常方便.
2.  免费: 免费提供 username.github.io 的域名, 免费的静态网站服务器.
3.  无数量限制: github pages 没有使用的数量限制, 每一个 github repository 都可以部署一个静态网站.

## 使用流程

### 部署静态网页

首先我们介绍一下部署最基础的静态网页, 最终的效果是展示出一个 `hello world` 页面.

#### 创建一个 github 项目

前往 [github 官网](https://github.com), 点击 `New repository` 创建新项目. 填入项目基本信息, 点击 `Create Repository` 确认.

![new_repo_input_page](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/new_repo_input_page.png)

确认完成后会看到如下页面:

![new_repo_finish_page](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/new_repo_finish_page.png)

#### 现在, 我们将代码 clone 到本地, 并创建几个基本文件

![git_clone_shell](https://raw.githubusercontent.com/ssthouse/d3-blog/master/use-github-page-efficiently/git_clone_shell.jpeg)

#### 然后我们加点基本的静态网站代码
- 代码
- git push上去

#### 最后在github上为该项目开启 github page