# 创建, 发布自己的 Vue UI 组件库

## 前言

在使用 Vue 进行日常看法时, 我们经常会用到一些开源的 UI 库, 如: Element-UI, Vuetify. 只需一行命令, 即可方便的将这些库引入我们当前的项目:

```shell
npm install vuetify
// or
yarn add vuetify
```

但是当我们自己开发了一个component, 然后需要在多个项目中使用的时候呢? 我们首先想到的可能是直接复制一份过去对吗? 这样做是很方便, 但是有两个问题:
- 当该component需要更新时, 我们需要手动维护所有引用到该component的更新
- 当有多个 component 需要共享时, 手动复制太繁琐

那么, 我们为什么不发布一个 UI组件库给自己用呢?

本文笔者将介绍如何一步步, 创建并发布自己的 Vue UI 组件库

## 初始化 project

这里我们使用官方的 vue-cli 初始化一个 Vue项目

```shell
npm install -g @vue/cli
# or
yarn global add @vue/cli
vue create personal-component-set
```

进入我们新建的项目, 让我们看看当前的项目文件:


## 配置 project &写一个 Hello World component

## npm 发布

## 测试使用