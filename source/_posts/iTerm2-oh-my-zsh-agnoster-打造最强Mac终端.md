---
title: iTerm2 + oh my zsh打造最强Mac终端
date: 2018-07-22 22:53:37
tags: [软件,编程]
description: 
copyright: true
categories: 软件
top:
---
iTerm2 + oh my zsh 打造最强Mac终端

![iTerm2](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/logo2x.jpg)

<!-- more -->

#安装iTerm2
iTerm2官方下载地址 [http://www.iterm2.com/downloads.html](http://www.iterm2.com/downloads.html)

#安装 oh-my-zsh

```h
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh 
```


#配置
创建配置文件
```h
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

# 设置zsh为默认的shell

`chsh -s /bin/zsh`

此时，zsh已经安装成功 

接来下，可以去https://github.com/robbyrussell/oh-my-zsh/wiki/themes 上下载一些zsh的主题，放置在 ~/.oh-my-zsh/themes 目录目录下，并在配置文件.zshrc中进行适当的配置。其实，默认情况下，themes目录下已有很多样式。 