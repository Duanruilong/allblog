---
title: React16新特性来认识一波
date: 2018-11-29 18:34:52
tags: [React,编程]
description: 
copyright: false
categories: React
top:
---
React的16版本，采用了MIT开源许可证,React16新特性来认识一波。
- `Fragments`：render函数可以返回数组和字符串
- `error boundaries`：错误处理
- `portals` ：支持声明性地将子树渲染到另一个DOM节点
- `custom DOM attributes` ：ReactDom允许传递非标准属性
- `improved server-side rendering`:提升服务端渲染性能
- 支持自定义DOM属性
- setState传入null时不会再触发更新
- 更好的服务器端渲染
- 新的打包策略

![React](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/react16.png)

<!--more-->

# 新特性

# 1、render可以return数组 ，字符串

 React16新增加了render的返回格式，你可以return返回`string，number，boolean，null，portal，以及fragments(带有key属性的数组)`，且不需要外层包含div标签。不过需要添加对应的key值。

```h
    //string
    render(){
        return 'Hello,world'
    }

    //number
    render(){
        return 0123456789
    }

    //boolean
    render(){
        return isTrue ? true : false
    }

    //null
    render(){
        return null
    }

    //fragments，遇到控制台出现warning,是未加key标识符，
    render(){
        return [
            <div>Hello</div>,
            <span>world</span>,
            <p>DRL</p>
        ]
    }

```

# 2、error boundaries错误处理

React16新增加了抛异常的生命周期函数`componentDidCatch`

```h
  componentDidCatch(err,info){
        this.setState({isError:1});
    }
```
 
 运用这个寿命周期在遇到页面报错的时候就可以定义报错方式，给用户更好的体验。React 16之前页面内的组件如果发生错误，那么整个组件树就会从根节点被卸载，到了React 16 产生了一个“错误边界（error boundaries）”的概念,它会捕捉页面内的错误并且对这些错误进行处理。我们可以把它比喻为`try-catch`

 在容易出错的组件外使用ErrorBoundary将它包裹起来:

 ```h
//使用方式

import React, { Component } from 'react'

export default class ErrorBoundary extends Component {
    constructor(props) {
        super(props)
        this.state = { hasError: false }
    }
    componentDidCatch(err, info) {
        this.setState({ hasError: true })
    }
    render(){
        if(this.state.hasError){
            return <div>Something wrong!</div>
        }
        return this.props.children
    }
}


render(){
    return (
        <div>
            <ErrorBoundary>
                <Listview user={this.state.data} />
            </ErrorBoundary>
            <button onClick={this.onClickChange}>toUpdate</button>
        </div>
    )
}

```

如果Listview组件发生错误，将会使用ErrorBoundary提供的`<div>Something went wrong</div>`代替它，不会引起整个组件树的卸载。


# 3、`portals`支持声明性地将子树渲染到另一个DOM节点

Portals机制提供了一种最直接的方式可以把一个子组件渲染到父组件渲染的DOM树之外。默认情况下，React组件树和DOM树是完全对应的，因此对于一些Modal,Overlay之类的组件，通常是将它们放在顶层，但逻辑上它们可能只是属于某个子组件，不利于组件的代码组织。通过使用createPortal，我们可以将组件渲染到我们想要的任意DOM节点中，但该组件依然处在React的父组件之内。带来的一个特性就是，在子组件产生的event依然可以被React父组件捕获，但在DOM结构中，它却不是你的父组件。对于组件组织，代码切割来说，这是一个很好的属性。

```h
//实现一个简易蒙层效果，抽象出一个通用的Overlay组件
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

export default class Overlay extends Component {
    constructor(props) {
        super(props);
        this.container = document.createElement('div');
        document.body.appendChild(this.container);
    }
    componentWillUnmount() {
        document.body.removeChild(this.container);
    }
    render() {
        return ReactDOM.createPortal(
            <div className='overlay'>
                <span className='overlay-close' onClick={this.props.onClose}>&times;</span>
                {this.props.children}
            </div>,
            this.container
        )
    }
}
//该组件对应的样式如下
.overlay{
    box-sizing:border-box;
    position: fixed;
    top:50%;
    left:50%;
    width:260px;
    height:200px;
    margin-left:-130px;
    margin-top:-100px;
    padding:10px;
    background-color: #fff;
    outline: rgba(0,0,0,.5) solid 9999px;
}
.overlay-close{
    position: absolute;
    top:10px;
    right:10px;
    color:red;
    cursor: pointer;
}

使用方式如下：
class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      overlayActive: false
    }
    this.closeOverlay = this.closeOverlay.bind(this);
    this.showOverlay = this.showOverlay.bind(this);
  }
  closeOverlay() {
    this.setState({ overlayActive: false })
  }
  showOverlay() {
    this.setState({ overlayActive: true })
  }
  render() {
    return (
      <div className="App">
        <div>hello world!</div>
        {this.state.overlayActive &&
          <Overlay onClose={this.closeOverlay}>overlay content</Overlay>}
        <button onClick={this.showOverlay}>show</button>
      </div>
    );
  }
}
 
```



# 4、`custom DOM attributes` ：ReactDom允许传递非标准属性
# 5、 `improved server-side rendering`:提升服务端渲染性能
# 6、 支持自定义DOM属性
# 7、 setState传入null时不会再触发更新
# 8、 更好的服务器端渲染
# 9、 新的打包策略




