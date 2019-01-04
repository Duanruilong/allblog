---
title: styled-components快速入门
date: 2019-01-03 15:20:15
tags: [Javascript,编程,CSS,React]
description: 
copyright: false
categories: 编程
top:
---
styled-components利用标记的模板文字来设置组件的样式。

它删除了组件和样式之间的映射。这意味着当您定义样式时，实际上是在创建一个正常的React组件，它会将样式附加到它上面。

![styled-components](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/styled.png)

<!--more-->

# styled-components

`styled-components` 是一个常用的`css in js` 类库。和所有同类型的类库一样，通过 js 赋能解决了原生 css 所不具备的能力，比如变量、循环、函数等。

# 文档

官方文档[链接](https://www.styled-components.com/)

# 优势

className 的写法会让原本写css的写法十分难以接受,`sass` 、`less` 等预处理可以解决部分 `css` 的局限性，还是要学习新的语法，而且需要对其编译，如果通过导入css的方式 会导致变量泄露成为全局 需要配置webpack让其模块化,其复杂的 `webpack` 配置也总是让开发者抵触。

有过`sass` 、`less`开发经验，也能很快的切换到`styled-components`，因为大部分语法都类似，比如`嵌套`、`&` 、 `继承`等， `styled-componens` 很好的解决了学习成本与开发环境问题，很适合 `React 技术栈` 、`React Native`的项目开发。

# 安装

```h

npm install --save styled-components

```

> webstorm需要安装 styled-component 插件 ; vscode已支持智能提示


# 实战


## 简单的组件

创建了两个简单的组件，一个包装器和一个标题，并附加了一些样式：

```h

import styled from 'styled-components';

//创建一个Title组件，它将使用某些样式
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

// 创建一个包装器组件，它将使用某些样式
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;

//像任何其他React组件一样使用Title和Wrapper  - 除了它们的样式！
render(
  <Wrapper>
    <Title>
      Hello World!
    </Title>
  </Wrapper>
);

```

可以看到控制台中输出了一个随机的className，这是styled-components帮我们完成的. 注意: 组件名要以大些开头 不然会被解析成普通标签

## 传递props

```h
const Button = styled.button`
    background: ${props => props.primary ? 'palevioletred' : 'white'};
    color: ${props => props.primary ? 'white' : 'palevioletred'};
    font-size: 1em;
    margin: 1em;
    padding: 0.25em 1em;
    border: 2px solid palevioletred;
    border-radius: 3px;
`
render(
    <div>
        <Button>Normal</Button>
        <Button primary>Primary</Button>
    </div>
);

```

在组件传递的props都可以在定义组件时获取到，这样就很容易实现定制某些风格组件

## props高级用法

设置默认值，在未设定必须传值的情况下我们会给一个默认值(defaultProps)

```h

export default class ALbum extends React.Component {

    constructor (props) {
        super(props)
        this.state = {
            // 接收传递的值
            imgSrc: props.imgSrc
        }
    }
    
    render () {
        const {imgSrc} = this.state
        return (
            <Container imgSrc={imgSrc}>
            </Container>
        )
    }
}

// 在这里是可以拿到props的 
const Container = styled.div`
    background-size: cover;
    background-image: url(${props =>  props.imgSrc});
    width: 100%;    
    height: 300px;
`

// 当然没传值也没关系  我们设置默认值
Container.defaultProps = {
    imgSrc: Cover
}
```


## 塑造组件

这个非常有用 你可能会遇到一些原本就已经是组件了 但是你要为他添加一些样式，这时候该怎么办呢 ?

```h
// 传递className 在react-native 中要使用 style
const Link = ({className , children}) => (
    <a className={className}>
        {children}
    </a>
)

const StyledLink = styled(Link)`
    color: palevioletred;
`
render(
    <div>
        <Link>普通组件</Link>
        <StyledLink>有颜色吗？</StyledLink>
    </div>
);

```

## 组件样式继承

```h
const Button = styled.button`
    color: palevioletred;
    font-size: 1em;
    margin: 1em;
    padding: 0.25em 1em;
    border: 2px solid palevioletred;
    border-radius: 3px;
`;
const TomatoButton = Button.extend`
    color: tomato;
    border-color: tomato;
`;
// TomatoButton 部分样式继承自 Button 这种情况下不会生成两个class

```

## 改变组件标签

在闲的蛋疼的情况下 我们想要改变组件的标签 比如把 button 变成 a 标签

```h
// 利用上面定义的 Button 组件 调用 withComponent 方法
const Link = Button.withComponent('a')

```

## 维护其他属性

在某种情况下，我们可能需要用到第三方库样式，我们可以使用这个方法轻松达到
```h
const Input = styled.input.attrs({
    // 定义静态 props
    type: 'password',
    // 没传默认使用 1em
    margin: props => props.size || '1em',
    padding: props => props.size || '1em'
})`
    color: palevioletred;
    font-size: 1em;
    border: 2px solid palevioletred;
    border-radius: 3px;
    // 动态计算props
    margin: ${props => props.margin};
    padding: ${props => props.padding}
`
render ( <Input size='1em'></Input>  <Input size='2em'></Input> )

```
## 动画

动画会生成一个随机类名 而不会污染到全局
```h
import { keyframes } from 'styled-components'
// CSS 动画
const rotate360 = keyframes`
    from {
        transform: rotate(0);
    }
    to {
        transform: rotate(360deg);
    }
`
const Rotate = Button.extend`
    animation: ${rotate360} 2s linear infinite;
`
render ( <Rotate>  💅  </Rotate> )
```


# 最后

styled-components虽然解决了大部分问题，增加了可维护性，但是破坏了原生体验，时常我们需要写更多的代码来达到业务要求，希望未来有更好的方案.



