---
title: React利用react-to-print实现打印功能
date: 2019-06-28 17:28:42
tags: [React,Ant Design,dva]
description: 
copyright: true
categories: React
top:
---
最近需要在react项目中实现打印功能，刚开始想到的都是基于window.print(),但是window.print()如果直接在钉钉应用内部打印的话，直接报错，用户交互很不好。

于是就找到了`react-to-print` 下面分享下。


![React](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/react_d.png)

<!--more-->

# react-to-print

[react-to-print的github地址](https://github.com/gregnb/react-to-print)

# 项目

## 开始

在项目中安装依赖：

`npm install react-to-print --save-dev`

在项目中引入依赖：

```h

import ReactToPrint from "react-to-print";

```

在项目中使用：

```h

<div ref={el => (this.componentRef = el)}>
    <div style={{textAlign: 'center',color: 'red',fontSize: '40px',marginBottom: '10px',marginTop: '40px'}}>活动二维码</div>
    <div style={{fontSize: '26px',textAlign: 'center'}}>活动标题</div>
    <img id='imgCode' style={{width: '100%'}} src={this.state.qrcodeImg}  alt="二维码" ></img>
</div>
<Button style={{ marginRight: 16,marginLeft: 30 }}  onClick={() => this.handleCancel()}>取消</Button>

<ReactToPrint
    trigger={() => <a ><Button type="primary">打印二维码</Button></a>}
    content={() => this.componentRef}
    onAfterPrint={()=>{
    message.success('打印成功！');
    }}
/>

```



> 实例完整代码

```h

import React from "react";
import ReactToPrint from "react-to-print";
import PropTypes from "prop-types";

class ComponentToPrint extends React.Component {
  render() {
    return (
      <table>
        <thead>
          <th>column 1</th>
          <th>column 2</th>
          <th>column 3</th>
        </thead>
        <tbody>
          <tr>
            <td>data 1</td>
            <td>data 2</td>
            <td>data 3</td>
          </tr>
          <tr>
            <td>data 1</td>
            <td>data 2</td>
            <td>data 3</td>
          </tr>
          <tr>
            <td>data 1</td>
            <td>data 2</td>
            <td>data 3</td>
          </tr>
        </tbody>
      </table>
    );
  }
}

class Example extends React.Component {
  render() {
    return (
      <div>
        <ReactToPrint
          trigger={() => <a href="#">Print this out!</a>}
          content={() => this.componentRef}
        />
        <ComponentToPrint ref={el => (this.componentRef = el)} />
      </div>
    );
  }
}

export default Example;

```

## API

T该组件接受以下API:

|         名称          | 类型     | 描述                                                                                                                         |
| :-------------------: | :------- | :---------------------------------------------------------------------------------------------------------------------------------- |
|     **`trigger`**     | function | 一个返回React Component或HTML元素的函数                                                                           |
|     **`content`**     | function | 返回组件引用值的函数。然后将该参考值的内容用于打印                     |
|   **`copyStyles`**    | boolean  | 将所有<style>和<link type =“stylesheet”/>从父窗口中复制到打印窗口中。（默认值：true） |
|  **`onBeforePrint`**  | function | 在打印之前触发的回调函数                                                                                      |
|  **`onAfterPrint`**   | function | 打印后触发的回调函数                                                                                       |
| **`closeAfterPrint`** | boolean  | 操作后关闭打印窗口                                                                                                 |
|    **`pageStyle`**    | string   | 覆盖默认打印窗口样式                                                                                               |
|    **`bodyClass`**    | string   | 传递给打印窗体的可选类 



## 常问问题


为什么`react-to-print`跳过`<link rel="stylesheet" href="">`标签？ `<link>`带有空href属性的s 是[INVALID HTML](https://www.w3.org/TR/html50/document-metadata.html#attr-link-href)。此外，它们可能导致各种[不良行为](https://gtmetrix.com/avoid-empty-src-or-href.html)。例如，许多浏览器（包括现代浏览器）在显示时<link href="">将尝试加载当前页面。有些人甚至尝试加载当前页面的父目录。
