# 创建, 发布自己的 Vue UI 组件库

## 前言

在使用 Vue 进行日常看法时, 我们经常会用到一些开源的 UI 库, 如: Element-UI, Vuetify. 只需一行命令, 即可方便的将这些库引入我们当前的项目:

```shell
npm install vuetify
// or
yarn add vuetify
```

但是当我们自己开发了一个 component, 然后需要在多个项目中使用的时候呢? 我们首先想到的可能是直接复制一份过去对吗? 这样做是很方便, 但是有两个问题:

- 当该 component 需要更新时, 我们需要手动维护所有引用到该 component 的更新
- 当有多个 component 需要共享时, 手动复制太繁琐

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

接下来让我们写一个 Vue component, 这里我写了一个简单的顶栏控件, 用来展示页面标题, 我的个人信息, github 源码链接.

component 代码如下:

```javascript
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

下面我们来配置当前项目, 以使其可以发布到npm上

## 配置 project &写一个 Hello World component

## npm 发布

## 测试使用
