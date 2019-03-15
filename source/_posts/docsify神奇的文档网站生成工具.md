---
title: docsify神奇的文档网站生成工具
date: 2019-03-14 14:50:13
ttags: [编程,工具]
description: 
copyright: false
categories: 编程
top:
---
docsify 是一个动态生成文档网站的工具。不同于 GitBook、Hexo 的地方是它不会生成将 .md 转成 .html 文件，所有转换工作都是在运行时进行。

这将非常实用，如果只是需要快速的搭建一个小型的文档网站，或者不想因为生成的一堆 .html 文件“污染” commit 记录，只需要创建一个 index.html 就可以开始写文档而且直接部署在 GitHub Pages。

![docsify](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/docsify.jpg)

<!-- more -->

# 特性

- 无需构建，写完文档直接发布
- 容易使用并且轻量 (~19kB gzipped)
- 智能的全文搜索
- 提供多套主题
- 丰富的 API
- 支持 Emoji
- 兼容 IE10+
- 支持 SSR (example)


# 快速开始

推荐安装 docsify-cli 工具，可以方便创建及本地预览文档网站。

```h
npm i docsify-cli -g
```

# 初始化项目
如果想在项目的 ./docs 目录里写文档，直接通过 init 初始化项目。

```h
docsify init ./docs
```

# 开始写文档
初始化成功后，可以看到 ./docs 目录下创建的几个文件

- index.html 入口文件
- README.md 会做为主页内容渲染
- .nojekyll 用于阻止 GitHub Pages 会忽略掉下划线开头的文件
直接编辑 `docs/README.md` 就能更新网站内容，当然也可以写多个页面。

# 本地预览网站
运行一个本地服务器通过 `docsify serve` 可以方便的预览效果，而且提供 LiveReload 功能，可以让实时的预览。默认访问 http://localhost:3000 。

```h
docsify serve docs
```

更多命令行工具用法，参考 [docsify-cli 文档](https://github.com/docsifyjs/docsify-cli)。

# 手动初始化
如果不喜欢 npm 或者觉得安装工具太麻烦，我们其实只需要直接创建一个 index.html 文件。

```h
// index.html

<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <meta charset="UTF-8">
  <link rel="stylesheet" href="//unpkg.com/docsify/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      //...
    }
  </script>
  <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
</body>
</html>
```

如果系统里安装 Python 的话，也可以很轻易的启动一个静态服务器。

```h
cd docs && python -m SimpleHTTPServer 3000
```

# Loading 提示
初始化时会显示 Loading... 内容，你可以自定义提示信息。

```h
// index.html

  <div id="app">加载中</div>
```

如果更改了 el 的配置，需要将该元素加上 data-app 属性。

```h
// index.html

  <div data-app id="main">加载中</div>

  <script>
    window.$docsify = {
      el: '#main'
    }
  </script>

```

# 示例

这是一份docsify开发的文档欢迎点击查看

- [git地址](https://github.com/Duanruilong/doctry)
- [线上预览](https://duanruilong.github.io/doctry)


>  更加详细的设置配置参考[官方文档](https://docsify.js.org/#/zh-cn/)