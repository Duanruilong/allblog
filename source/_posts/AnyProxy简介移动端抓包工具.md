---
title: AnyProxy简介移动端抓包工具
date: 2018-12-26 11:29:07
tags: [Javascript,编程,工具]
description: 
copyright: false
categories: 编程
top:
---

移动端的开发，难免需要抓包查看情况，在实际工作里开发移动端业务需求，找到了AnyProxy抓包神器，它是阿里巴巴基于 Node.js 开发的一款开源代理服务器，使用场景很广，满足日常需求开发和测试。


![AnyProxy](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/AnyProxy.png)

<!--more-->

# 1. 简介

> AnyProxy 是阿里巴巴基于 Node.js 开发的一款开源代理服务器。[AnyProxy链接](https://github.com/alibaba/anyproxy)

- 代理服务器站在客户端和服务端的中间，它可以收集双方通信的每个比特。一个完整的代理请求过程为：客户端首先与代理服务器创建连接，接着根据代理服务所使用的代理协议，请求对目标服务器创建连接或者获取目标服务器的指定资源。一些代理协议允许代理服务器改变客户端的原始请求、目标服务器的原始响应。
- AnyProxy 是完全可以灵活配置的代理服务器。它支持 https明文代理 ，且提供了 Web 界面便于观测请求情况，同时支持二次开发，可以用 JavaScript 来控制代理的全部流程，搭建前端个性化调试环境。

# 2. 安装
## 安装 Node.js
```h
~ brew update
~ brew install node
~ node --version
```

## 安装 AnyProxy（需要node8）
```h
~ 安装稳定正式版
$ npm install -g anyproxy
~ 最新测试版，需要使用最新版本 node 进行安装
$ npm install -g anyproxy@beta
~ 有时需要添加 sudo
```

## 使用AnyProxy

### 1、启动 anyproxy
```h
~ anyproxy
```
### 启动结果

```h
 ~ anyproxy
[AnyProxy Log][2018-12-26 11:42:19]: Http proxy started on port 8001
[AnyProxy Log][2018-12-26 11:42:19]: web interface started on port 8002
```

### 2、启动浏览器

```h
http://192.168.0.195:8002/   或   http://127.0.0.1:8002/
```

### 3、客户端配置代理 ip 端口号8001
![客户端配置代理](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/tool/AnyProxy.png)

启动anyproxy
![客户端配置代理](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/tool/AnyProxy1.png)

## 配置 Https

### 1、 生成 rootCA

```h
//  稳定版本生成方法
~ sudo anyproxy --root
//  最新版本生成方法
~ anyproxy-ca
```
![生成rootCA](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/tool/AnyProxy2.png)

## 以支持 Https 方式重新启动 AnyProxy

```h
~ anyproxy --intercept
//  简写
~ anyproxy -i

```

启动结果
```h
~ anyproxy -i

[AnyProxy WARN][2018-12-26 11:55:54]: ROOT CA NOT INSTALLED YET
? The rootCA is not trusted yet, install it to the trust store now? Yes
[AnyProxy Log][2018-12-26 11:56:03]: About to trust the root CA, this may requires your password
Password:
[AnyProxy Log][2018-12-26 11:56:07]: Root CA install, you are ready to intercept the https now
[AnyProxy Log][2018-12-26 11:56:07]: The root CA file path is: /Users/xmaczone/.anyproxy/certificates/rootCA.crt
[AnyProxy Log][2018-12-26 11:56:08]: Http proxy started on port 8001
[AnyProxy Log][2018-12-26 11:56:08]: web interface started on port 8002

```

## 手机安装证书
![生成rootCA](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/tool/AnyProxy3.png)


# 拓展

[windows下安装AnyProxy抓取移动App Http请求](http://aiezu.com/article/windows_anyproxy_install.html)
