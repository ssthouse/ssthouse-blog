# 创建, 发布自己的 Vue UI 组件库

## 前言

在使用 Vue 进行日常开发时, 我们经常会用到一些开源的 UI 库, 如: Element-UI, Vuetify. 只需一行命令, 即可方便的将这些库引入我们当前的项目:

```shell
npm install vuetify
// or
yarn add vuetify
```

但是当我们自己开发了一个 component, 需要在多个项目中使用的时候呢? 我们首先想到的可能是直接复制一份过去对吗? 这样做是很方便, 但是有两个问题:

- 当该 component 需要更新时, 我们需要手动维护所有用到该 component 的更新
- 当有多个 component 需要共享时, 手动复制过于繁琐

那么, 我们为什么不发布一个 UI 组件库给自己用呢?

本文笔者将介绍如何一步步, 创建并发布自己的 Vue UI 组件库

## 初始化 project

这里我们使用官方的 vue-cli 初始化一个 Vue 项目

```shell
npm install -g @vue/cli
# or
yarn global add @vue/cli
vue create personal-component-set
```

进入我们新建的项目, 让我们看看当前的项目文件:

![empty prj](https://raw.githubusercontent.com/ssthouse/d3-blog/master/create-own-vue-library/img/empty_prj.png)

接下来让我们写一个 Vue component. 这里我写了一个简单的顶栏控件, 用来展示页面标题, 我的个人信息, github 源码链接.

component 代码如下:

```html
<template>
    <v-toolbar>
        <v-toolbar-side-icon @click="toMainPage()"></v-toolbar-side-icon>
        <v-toolbar-title>Visual Explain</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items class="hidden-sm-and-down">
            <v-btn flat @click="openMyGithub()">
                <v-avatar size=42>
                    <img src="https://avatars3.githubusercontent.com/u/10973821?s=460&v=4">
                </v-avatar>
                <span style="margin-left:8px;">About me: ssthouse</span>
            </v-btn>
            <v-tooltip bottom>
                <v-btn slot="activator" flat :href="sourceCodeLink">
                    <v-avatar size=42>
                        <img src="https://assets-cdn.github.com/images/modules/logos_page/GitHub-Mark.png">
                    </v-avatar>
                    Source Code
                </v-btn>
                <span class="top-bar-tooltip">Welcome to fork & star <br/> ; )</span>
            </v-tooltip>
        </v-toolbar-items>
    </v-toolbar>
</template>

<script>
export default {
  props: ['sourceCodeLink'],
  methods: {
    openMyGithub() {
      const a = document.createElement('a')
      a.target = '_blank'
      a.href = 'https://github.com/ssthouse'
      a.click()
    },
    toMainPage() {
      this.$emit('to-main-page')
    }
  }
}
</script>

<style scoped>
.top-bar-tooltip {
  font-size: 18px;
}

a {
  color: black;
}
</style>
```

以上代码构成了一个非常简单的 Vue component, 提供了一个 props: sourceCodeLink 方便定制化跳转链接, 提供了一个 event: to-main-page, 用户跳转回主页的回调.

效果如图:

![top bar](https://raw.githubusercontent.com/ssthouse/d3-blog/master/create-own-vue-library/img/top_bar.png)



下面我们来配置当前项目, 以使其可以发布到 npm 上

## 配置 project

首先我们编辑入口文件 `src/components/index.js`, 使其被作为UI库导入时能自动先Vue中注册我们的Component:



```javascript
import Vue from 'vue'
import TopBar from './TopBar.vue'
const Components = {
  TopBar
}

Object.keys(Components).forEach(name => {
  Vue.component(name, Components[name])
})

export default Components

```



接下来我们添加 build 项目的脚本到 package.json 的 scripts 中:

```json
"build-bundle": "vue-cli-service build --target lib --name personal-component-set ./src/components/index.js",
```

其中 `--name libraryName` 会指定要发布的库的名称, 我们执行上面新加的脚本:

![build result](https://raw.githubusercontent.com/ssthouse/d3-blog/master/create-own-vue-library/img/run_build_bundle.png)



这里我们选择默认发布我们的 *.common.js 文件, 所以我们在 package.json中添加main属性:



![build result](https://raw.githubusercontent.com/ssthouse/d3-blog/master/create-own-vue-library/img/package_main_attr.png)



指定该属性后, 当我们引用该组件库时, 会默认加载main中指定的文件.



最后, 我们再配置 package.json中的 files属性, 来配置我们想要发布到 npm上的文件路径. 我们这里将用户引用我们的组件库可能用到的所有文件都放进来:

![build result](https://raw.githubusercontent.com/ssthouse/d3-blog/master/create-own-vue-library/img/package_files_attr.png)

## npm 发布

首先我们注册一个 npm 账号 (如果已有账号, 可以跳过此步骤)

```shell
npm add user

// 按照提示输入用户名, 邮箱等即可
```

然后使用 `npm login` 登录注册号的状态

登录后可以使用 `npm whoami` 查看登录状态



在发布之前, 我们修改一下项目的名称(注意不要和已有项目名称冲突):

![build result](https://raw.githubusercontent.com/ssthouse/d3-blog/master/create-own-vue-library/img/package_name_attr.png)

推荐使用 @username/projectName 的命名方式.



接下来我们就可以发布我们的UI组件库了, 在发布之前我们再编译一次, 让build出的文件为我们最新的修改:



```shell
npm run build-bundle
```

我们使用下面的命令发布我们的项目:

```shell
npm publish --access public
```

需要注意的是 package.json中指定的version, 每次要更新我们的组件库都需要更新一下version(毕竟同一个version 的代码不同,很容易让人产生疑惑)



## 测试使用



这样我们就完成了自己的UI组件库的发布. 接下来我们在任何需要使用到该组件库的项目中使用:

```shell
npm install --save @ssthouse/personal-component-set
```

然后再index文件 (如src/main.js) 中引入该组件库:

```javascript
import '@ssthouse/personal-component-set'
```

接下来我们就可以在 Vue的template中使用组件库中的 Component了:

```html
<template>
  <v-app id="app">
    <top-bar :sourceCodeLink="sourceCodeLink"></top-bar>
    <router-view/>
  </v-app>
</template>
```



