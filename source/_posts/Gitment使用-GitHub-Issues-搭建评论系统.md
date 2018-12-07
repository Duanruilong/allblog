---
title: Gitment使用 GitHub Issues 搭建评论系统
date: 2018-12-07 15:42:06
tags: [软件,编程]
description:
copyright: true
categories: 软件
top:
---
Gitment 是一款基于 GitHub Issues 的评论系统。支持在前端直接引入，不需要任何后端代码。可以在页面进行登录、查看、评论、点赞等操作，同时有完整的 Markdown / GFM 和代码高亮支持。尤为适合各种基于 GitHub Pages 的静态博客或项目页面。

![Gitment](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/gitment.png)

<!-- more -->

# 基础使用

## 1. 注册 OAuth Application
[点击此处](https://github.com/settings/applications/new) 来注册一个新的 OAuth Application。其他内容可以随意填写，但要确保填入正确的 callback URL（一般是评论页面对应的域名，如 https://imsun.net）。

你会得到一个 client ID 和一个 client secret，这个将被用于之后的用户登录。

## 2. 引入 Gitment


将下面的代码添加到你的页面：

```h
<div id="container"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
var gitment = new Gitment({
  id: '页面 ID', // 可选。默认为 location.href
  owner: '你的 GitHub ID',
  repo: '存储评论的 repo',
  oauth: {
    client_id: '你的 client ID',
    client_secret: '你的 client secret',
  },
})
gitment.render('container')
</script>

```

注意，上述代码引用的 Gitment 将会随着开发变动。如果你希望始终使用最新的界面与特性即可引入上述代码。

如果你希望引用确定版本的 Gitment，则应该使用 npm 进行安装。

`$ npm install --save gitment`
关于构造函数中的更多可用参数请查看 Gitment Options

## 3. 初始化评论
页面发布后，你需要访问页面并使用你的 GitHub 账号登录（请确保你的账号是第二步所填 repo 的 owner），点击初始化按钮。

之后其他用户即可在该页面发表评论。

## 自定义
Gitment 很容易进行自定义，你可以写一份自定义的 CSS 或者使用一个新的主题。（主题可以改变 DOM 结构而自定义 CSS 不能）

比如你可以通过自定义主题将评论框放在评论列表前面：

```h
const myTheme = {
  render(state, instance) {
    const container = document.createElement('div')
    container.lang = "en-US"
    container.className = 'gitment-container gitment-root-container'
    container.appendChild(instance.renderHeader(state, instance))
    container.appendChild(instance.renderEditor(state, instance))
    container.appendChild(instance.renderComments(state, instance))
    container.appendChild(instance.renderFooter(state, instance))
    return container
  },
}
const gitment = new Gitment({
  // ...
  theme: myTheme,
})
gitment.render('container')
```

更多自定义内容请查看[文档](https://github.com/imsun/gitment#customize)。

## 其他问题
语言问题
考虑到 GitHub 本身使用英文，而本项目面向用户均为 GitHub 用户，所以作者没有提供中文支持的打算。实在有需求的可以通过自定义主题支持中文。
