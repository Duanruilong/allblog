---
title: 使用prop-types进行属性确认
date: 2019-03-29 15:48:41
ttags: [编程,react,Javascript]
description: 
copyright: false
categories: 编程,Javascript
top:
---
在开发 React Native 自定义组件时，可以通过属性确认来声明这个组件需要哪些属性。这样，如果在调用这个自定义组件时没有提供相应的属性，则会在手机与调试工具中弹出警告信息，告知开发者该组件需要哪些属性。

> 为了保证 React Native 代码高效运行，属性确认仅在开发环境中有效。

![prop-types](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/prop_types.jpg)

<!-- more -->


# prop-types 库的安装和配置

- 进入项目根目录，执行如下代码安装 prop-types 库：

```h
npm install --save prop-types
```

- 在需要使用的 js 文件中使用如下代码引入：

```h
import PropTypes from 'prop-types';
```
# 使用prop-types

下面对 list.js 代码做个修改，增加属性确认代码

```h

import React, { Component } from 'react';
import {
   Text,
   View,
 } from 'react-native';
 
 import PropTypes from 'prop-types';
 
 export default class List extends Component {
   //........
 }
 
 //添加属性确认
 List.propTypes = {
   news: PropTypes.array,
 }
 
 const styles = StyleSheet.create({
   //........
 });

```

# 属性确认的语法

1、要求属性是指定的 JavaScript 基本类型

```h
属性: PropTypes.array,
属性: PropTypes.bool,
属性: PropTypes.func,
属性: PropTypes.number,
属性: PropTypes.object,
属性: PropTypes.string,
```

2、要求属性是可渲染节点

```h
属性: PropTypes.node,
```

3、要求属性是某个 React 元素

```h
属性: PropTypes.element,
```


4、要求属性是某个指定类的实例

```h
属性: PropTypes.instanceOf(NameOfAClass),
```

5、要求属性取值为特定的几个值

```h
属性: PropTypes.oneOf(['value1', 'value2']),
```

6、要求属性可以为指定类型中的任意一个

```h
属性: PropTypes.oneOfType([
  PropTypes.bool,
  PropTypes.number,
  PropTypes.instanceOf(NameOfAClass),
])
```

7、要求属性为指定类型的数组

```h
属性: PropTypes.arrayOf(PropTypes.number),
```

8、要求属性是一个有特定成员变量的对象

```h
属性: PropTypes.objectOf(PropTypes.number),
```

9、要求属性是一个指定构成方式的对象

```h
属性: PropTypes.shape({
  color: PropTypes.string,
  fontSize: PropTypes.number,
}),
```

10、属性可以是任意类型

```h
属性: PropTypes.any
```


# 属性声明为必需的

上述的 10 种语法，都可以通过在后面加上 isRequired 声明它是必需的。

```h
属性: PropTypes.array.isRequired,
属性: PropTypes.any.isRequired,
属性: PropTypes.instanceOf(NameOfAClass).isRequired,
```










