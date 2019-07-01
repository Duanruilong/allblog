---
title: Ant Design + qrcode实现打印功能
date: 2019-06-13 18:19:17
tags: [React,Javascript,Ant Design]
description: 
copyright: true
categories: Ant Design
top:
---
react本身有一些打印的组件，但都不好用，都是基于`window.print()`，`window.print()`直接打印的话，处理直接当前网页的body设置为你要打印的区域，但是当你取消打印的时候你会发现整个网页都被你要打印的区域占满了，你还得用window.reload()重新加载一下页面，用户交互很不好，建议不要采用这种方式。

![React](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/react_d.png)

<!--more-->

# QRCode.js 生成二维码

QRCode.js 是一个用于生成二维码图片的插件。


## 使用方法

 ### 载入 JavaScript 文件

```h
<script src="qrcode.js"></script>

```
### DOM 结构

```h
<div id="qrcode"></div>
```

### 调用

```h
// 简单方式
new QRCode(document.getElementById('qrcode'), 'your content');

// 设置参数方式
var qrcode = new QRCode('qrcode', {
  text: 'your content',
  width: 256,
  height: 256,
  colorDark : '#000000',
  colorLight : '#ffffff',
  correctLevel : QRCode.CorrectLevel.H
});

// 使用 API
qrcode.clear();
qrcode.makeCode('new content');


```

# Ant Design里使用

react中一般生成二维码都是用react.qrcode，但是发现这玩意生成的是canvas，不是图片，打印的时候预览不出来。所以我想进一切办法去把canvas转成图片，无奈拿不到这个canvas标签。最后还是用js的qrcode来生成二维码，这样生成的默认是base64位的图片，打印正常。代码如下：


## 安装

`cnpm install qrcode --save`

## 在页面上引入

```h

import React, { Component} from 'react';
import styles from './ActivityDetail.less';
import {
  Button,
  message
} from 'antd';
import * as QrCodeEs from 'qrcode';


class ActivityDetail extends Component {
	state = {
			qrcodeImg:'', // 二维码图片地址
		};


	// 生成二维码点击
  pathQRCodeClick(item) {
    QrCodeEs.toDataURL(item._id)
    .then(url => {
      console.log('url=====<<<<',url)
        this.setState({
            qrcodeImg:url
        })
    })
    .catch(err => {
        console.error(err)
    })
  }

	// 确定-打印二维码
  handleOk = e => {
    const { item } = this.props.location;
    const win = window.open('','printwindow');
    let codeHtml =  '<div style="text-align: center;color: red;font-size: 40px;margin-bottom: 10px;margin-top: 40px">活动二维码</div><br/>\n' +
                    '<div style="font-size: 26px;text-align: center;">'+ item.name +'</div><br/>\n' +
                    '<img style="font-size: 26px;width: 340px;position: absolute;left: 50%;margin-left: -170px;" src='+ this.state.qrcodeImg +' alt="教科所" />\n'
    win.document.write(codeHtml);
    win.print();
    win.close()
    message.success('打印成功！');
    this.setState({
      visible: false,
    });
  };

	render(){
			return(
				<div>
						<Button onClick={()=>{this.pathQRCodeClick(item)}} style={{marginLeft:'24px'}} type="primary">生成二维码</Button>
						<div style={{width:'50%',margin:'auto'}}>
								<img id='imgCode' style={{width: '100%'}} src={this.state.qrcodeImg}  alt="二维码" ></img>
						</div>
				</div>
			)
	}
}

export default ActivityDetail;


```
