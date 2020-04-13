---
title: 前端面试集锦-javascript
date: 2020-02-26 22:43:33
tags: [CSS,编程,Javascript,ES6]
description: 
copyright: true
categories: 编程
top: 10
---

这是一份关于面试的集锦，会收集广泛的面试点或者热点资讯，及时掌握前端的动态和储备自己的面试库，才可以在面试过程里游刃有余，持续积累。

![js](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/javascript.jpg)

<!--more-->

# 前言

> 一些更新从最新的开始

# JavaScript

## 原型

JavaScript中的对象都有一个特殊的 prototype 内置属性，其实就是对其他对象的引用
几乎所有的对象在创建时 prototype 属性都会被赋予一个非空的值，我们可以把这个属性当作一个备用的仓库
当试图引用对象的属性时会出发get操作，第一步时检查对象本身是否有这个属性，如果有就使用它，没有就去原型中查找。一层层向上直到Object.prototype顶层

每个函数都有一个 prototype 属性，就是我们经常在各种例子中看到的那个 prototype ，比如：

```h

function Person() {

}
// 虽然写在注释里，但是你要注意：
// prototype是函数才会有的属性
Person.prototype.name = 'Kevin';
var person1 = new Person();
var person2 = new Person();
console.log(person1.name) // Kevin
console.log(person2.name) // Kevin

```

那这个函数的 prototype 属性到底指向的是什么呢？是这个函数的原型吗？

其实，函数的 prototype 属性指向了一个对象，这个对象正是调用该构造函数而创建的实例的原型，也就是这个例子中的 person1 和 person2 的原型。

那什么是原型呢？你可以这样理解：`每一个JavaScript对象(null除外)在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型"继承"属性`。

![prototype](https://raw.githubusercontent.com/mqyqingfeng/Blog/master/Images/prototype1.png)

**每一个实例对象都有一个私有属性`__proto__`,指向它的构造函数的原型对象`(prototype)`**

![prototype](https://raw.githubusercontent.com/mqyqingfeng/Blog/master/Images/prototype4.png)

## 原型链

**每一个实例对象都有一个私有属性`__proto__`,指向它的构造函数的原型对象`(prototype)`。原型对象也有自己的`__proto__`，层层向上直到一个对象的原型对象为null。这一层层原型就是`原型链`。**

构造函数是一种特殊的方法，主要用来在创建对象时初始化对象。每个构造函数都有`prototype(原型)(箭头函数以及Function.prototype.bind()没有)`属性，
这个`prototype`(原型)属性是一个指针，指向一个对象，这个对象的用途是包含特定类型的所有实例共享的
属性和方法，即这个原型对象是用来给实例对象共享属性和方法的。每个实例对象的`__proto__`都指向这个
构造函数/类的prototype属性。


原型链实际上在上面原型的问题中就有涉及到，在原型的继承中，我们继承来多个原型，这里再提一下实现完美
继承的方案，通过借助寄生组合继承，PersonB.prototype = Object.create(PersonA.prototype)
这是当我们实例化PersonB得到实例化对象，访问实例化对象的属性时会触发get方法，它会先在自身属性上查
找，如果没有这个属性，就会去__proto__中查找，一层层向上直到查找到顶层对象Object，这个查找的过程
就是原型链来。

```h

// 使用构造函数的方式

let protoRabbit = function(color, word, type) {
  this.color = color;
  this.word = word;
  this.type = type;
};
protoRabbit.prototype.getColor = function() {
	return this.color;
}
protoRabbit.prototype.speak = function() {
	console.log(`The ${this.type} rabbit says ${this.word}`);
}
let killerRabbit = new protoRabbit('grey', 'SKREEEEE!', 'assassin');
killerRabbit.speak();


```


面向对象的三大特性：继承/多态/封装

关于new操作符：

1. new执行的函数, 函数内部默认生成了一个对象

2. 函数内部的this默认指向了这个new生成的对象

3. new执行函数生成的这个对象, 是函数的默认返回值


```

ES5例子：
function Person(obj) {
	this.name = obj.name
	this.age= obj.age
}
// 原型方法
Person.prototype.say = function() {
  console.log('你好,', this.name )
}
// p为实例化对象，new Person()这个操作称为构造函数的实例化
let p = new Person({name: '番茄', age: '27'})
console.log(p.name, p.age)
p.say()

ES6例子：
class Person{
	constructor(obj) {
  	this.name = obj.name
		this.age= obj.age
  }
  say() {
  	console.log(this.name)
  }
}

let p = new Person({name: 'ES6-番茄', age: '27'})
console.log(p.name, p.age)
p.say()

```

## 继承

所谓继承不就是子类能够继承父类的属性和方法吗？换句话说就是子类能够找到父类的`prototype`，最简单的方法就是子类原型的`__proto__`指向父类原型就行了。



1. JS中的函数可以作为函数使用，也可以作为类使用
2. 作为类使用的函数实例化时需要使用new
3. 为了让函数具有类的功能，函数都具有`prototype`属性。
4. 为了让实例化出来的对象能够访问到`prototype`上的属性和方法，实例对象的`__proto__`指向了类的`prototype`。所以`prototype`是函数的属性，不是对象的。对象拥有的是`__proto__`，是用来查找`prototype`的。
5. `prototype.constructor`指向的是构造函数，也就是类函数本身。改变这个指针并不能改变构造函数。
6. 对象本身并没有`constructor`属性，你访问到的是原型链上的`prototype.constructor`。
7. 函数本身也是对象，也具有`__proto__`，他指向的是JS内置对象Function的原型`Function.prototype`。所以你才能调用`func.call,func.apply`这些方法，你调用的其实是`Function.prototype.call`和`Function.prototype.apply`。
8. `prototype`本身也是对象，所以他也有`__proto__`，指向了他父级的`prototype`。`__proto__`和`prototype`的这种链式指向构成了JS的原型链。原型链的最终指向是`Object`的原型。`Object`上面原型链是`null`，即`Object.prototype.__proto__ === null`。
9. `Function.__proto__ === Function.prototype`。这是因为JS中所有函数的原型都是`Function.prototype`，也就是说所有函数都是Function的实例。Function本身也是可以作为函数使用的----Function()，所以他也是Function的一个实例。类似的还有`Object，Array`等，他们也可以作为函数使用:`Object(), Array()`。所以他们本身的原型也是Function.prototype，即`Object.__proto__ === Function.prototype`。换句话说，这些可以new的内置对象其实都是一个类，就像我们的`Puppy`类一样。
10. ES6的class其实是函数类的一种语法糖，书写起来更清晰，但原理是一样的。

> [详细地址](https://juejin.im/post/5e50e5b16fb9a07c9a1959af#heading-10) 



## 函数的 this

如果要判断一个运行中函数的 this 绑定， 就需要找到这个函数的直接调用位置。 找到之后
就可以顺序应用下面这四条规则来判断 this 的绑定对象。

`“this 永远指向最后调用它的那个对象”`

- new 调用：绑定到新创建的对象，注意：显示return函数或对象，返回值不是新创建的对象，而是显式返回的函数或对象。

- call 或者 apply（ 或者 bind） 调用：严格模式下，绑定到指定的第一个参数。非严格模式下，`null和undefined`，指向全局对象（浏览器中是window），其余值指向被new Object()包装的对象。

- 对象上的函数调用：绑定到那个对象。

- 普通函数调用： 在严格模式下绑定到 undefined，否则绑定到全局对象。

- ES6 中的箭头函数：不会使用上文的四条标准的绑定规则， 而是根据当前的词法作用域来决定this， 具体来说， 箭头函数会继承外层函数，调用的 this 绑定（ 无论 this 绑定到什么），没有外层函数，则是绑定到全局对象（浏览器中是window）。 这其实和 ES6 之前代码中的 `self = this` 机制一样。
DOM事件函数：一般指向绑定事件的DOM元素，但有些情况绑定到全局对象（比如IE6~IE8的attachEvent）。

一定要注意，有些调用可能在无意中使用普通函数绑定规则。 如果想“ 更安全” 地忽略 this 绑
定， 你可以使用一个对象， 比如`ø = Object.create(null)`， 以保护全局对象。

 
## 理解setState

- `setState` 只在合成事件和钩子函数中是“异步”的，在原生事件和 setTimeout 中都是同步的。

- `setState`的“异步”并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，形式了所谓的“异步”，当然可以通过第二个参数 `setState(partialState, callback)` 中的`callback`拿到更新后的结果。

- `setState` 的批量更新优化也是建立在“异步”（合成事件、钩子函数）之上的，在原生事件和setTimeout 中不会批量更新，在“异步”中如果对同一个值进行多次 `setState` ， `setState` 的批量更新策略会对其进行覆盖，取最后一次的执行，如果是同时 `setState` 多个不同的值，在更新时会对其进行合并批量更新。
 

### 关于原型的继承我们借助寄生组合继承

```h
function Person(obj) {
    this.name = obj.name
    this.age = obj.age
}
Person.prototype.add = function(value){
    console.log(value)
}
var p1 = new Person({name:"番茄", age: 18})

function Person1(obj) {
    Person.call(this, obj)
    this.sex = obj.sex
}
// 这一步是继承的关键
Person1.prototype = Object.create(Person.prototype)
Person1.prototype.play = function(value){
    console.log(value)
}
var p2 = new Person1({name:"鸡蛋", age: 118, sex: "男"})
 

```

## 闭包

首先说明什么是闭包，闭包简单来说就是函数嵌套函数，内部函数引用来外部函数的变量，从而导致垃圾回收
机制没有把当前变量回收掉，这样的操作带来了内存泄漏的影响，当内存泄漏到一定程度会影响你的项目运行
变得卡顿等等问题。因此在项目中我们要尽量避免内存泄漏。



## 深拷贝浅拷贝

- 浅拷贝：浅拷贝通过ES6新特性Object.assign()或者通过扩展运算法...来达到浅拷贝的目的，浅拷贝修改
副本，不会影响原数据，但缺点是浅拷贝只能拷贝第一层的数据，且都是值类型数据，如果有引用型数据，修改
副本会影响原数据。

- 深拷贝：通过利用JSON.parse(JSON.stringify())来实现深拷贝的目的，但利用JSON拷贝也是有缺点的，
当要拷贝的数据中含有undefined/function/symbol类型是无法进行拷贝的，当然我们想项目开发中需要
深拷贝的数据一般不会含有以上三种类型，如有需要可以自己在封装一个函数来实现。



## 陈述输入URL回车后的过程

- 1.读取缓存： 
    搜索自身的 DNS 缓存。(如果 DNS 缓存中找到IP 地址就跳过了接下来查找 IP 地址步骤，直接访问该 IP 地址。)

- 2.DNS 解析:将域名解析成 IP 地址
- 3.TCP 连接：TCP 三次握手，简易描述三次握手
    客户端：服务端你在么？ 
    服务端：客户端我在，你要连接我么？ 
    客户端：是的服务端，我要链接。 
    连接打通，可以开始请求来
- 4.发送 HTTP 请求
- 5.服务器处理请求并返回 HTTP 报文
- 6.浏览器解析渲染页面
- 7.断开连接：TCP 四次挥手

关于第六步浏览器解析渲染页面又可以聊聊如果返回的是html页面
根据 HTML 解析出 DOM 树
根据 CSS 解析生成 CSS 规则树
结合 DOM 树和 CSS 规则树，生成渲染树
根据渲染树计算每一个节点的信息
根据计算好的信息绘制页面

 
## 陈述http


基本概念：

HTTP，全称为 HyperText Transfer Protocol，即为超文本传输协议。是互联网应用最为广泛的一种网络协议
所有的 www 文件都必须遵守这个标准。

http特性：

HTTP 是无连接无状态的
HTTP 一般构建于 TCP/IP 协议之上，默认端口号是 80
HTTP 可以分为两个部分，即请求和响应。

http请求：

HTTP 定义了在与服务器交互的不同方式，最常用的方法有 4 种
分别是 GET，POST，PUT， DELETE。URL 全称为资源描述符，可以这么认为：一个 URL 地址
对应着一个网络上的资源，而 HTTP 中的 GET，POST，PUT，DELETE 
就对应着对这个资源的查询，修改，增添，删除4个操作。

HTTP 请求由 3 个部分构成，分别是：状态行，请求头(Request Header)，请求正文。

HTTP 响应由 3 个部分构成，分别是：状态行，响应头(Response Header)，响应正文。

HTTP 响应中包含一个状态码，用来表示服务器对客户端响应的结果。
状态码一般由3位构成：

1xx : 表示请求已经接受了，继续处理。
2xx : 表示请求已经处理掉了。
3xx : 重定向。
4xx : 一般表示客户端有错误，请求无法实现。
5xx : 一般为服务器端的错误。

比如常见的状态码：

200 OK 客户端请求成功。
301 Moved Permanently 请求永久重定向。
302 Moved Temporarily 请求临时重定向。
304 Not Modified 文件未修改，可以直接使用缓存的文件。
400 Bad Request 由于客户端请求有语法错误，不能被服务器所理解。
401 Unauthorized 请求未经授权，无法访问。
403 Forbidden 服务器收到请求，但是拒绝提供服务。服务器通常会在响应正文中给出不提供服务的原因。
404 Not Found 请求的资源不存在，比如输入了错误的URL。
500 Internal Server Error 服务器发生不可预期的错误，导致无法完成客户端的请求。
503 Service Unavailable 服务器当前不能够处理客户端的请求，在一段时间之后，服务器可能会恢复正常。

 

## 数组去重


在ES6的时代，有个非常快速且简单的方法，使用`new Set()`以及`Array.from()`或者`展开运算符(...)`

```h

var fruits = [“banana”, “apple”, “orange”, “watermelon”, “apple”, “orange”, “grape”, “apple”];


// 方法一
var uniqueFruits = Array.from(new Set(fruits));
console.log(uniqueFruits); // returns [“banana”, “apple”, “orange”, “watermelon”, “grape”]
// 方法二
var uniqueFruits2 = […new Set(fruits)];
console.log(uniqueFruits2); // returns [“banana”, “apple”, “orange”, “watermelon”, “grape”]


```




- 第一种： 通过ES6新特性Set()
```

例如： var arr = [1, 2, 3, 1, 2]; var newArr= [...new Set(arr)]

```

- 第二种：封装函数利用 {} 和【】

```h
function uniqueEasy(arr) {
    if(!arr instanceof Array) {
        throw Error('当前传入的不是数组')
    }
    let list = []
    let obj = {}
    arr.forEach(item => {
        if(!obj[item]) {
            list.push(item)
            obj[item] = true
        }
    })
    return list
}


<!-- 

- Uniqueeasy ([1,2,3,4,1,2,11,3,1,2 ]) 

- (5) [1,2,3,4,11 -->

```

## Set,Map解构


ES6 提供了新的数据结构 Set。
它类似于数组，但是成员的值都是唯一的，没有重复的值。 Set 本身是一个构造函数，用来生成 Set 数据结构。

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。


## 对数组排序

第一种方法利用sort方法
第二种利用冒泡排序


## ES6新特性 

- 1.ES6引入来严格模式
    变量必须声明后在使用
    函数的参数不能有同名属性, 否则报错
    不能使用with语句 (说实话我基本没用过)
    不能对只读属性赋值, 否则报错
    不能使用前缀0表示八进制数,否则报错 (说实话我基本没用过)
    不能删除不可删除的数据, 否则报错
    不能删除变量delete prop, 会报错, 只能删除属性delete global[prop]
    eval不会在它的外层作用域引入变量
    eval和arguments不能被重新赋值
    arguments不会自动反映函数参数的变化
    不能使用arguments.caller (说实话我基本没用过)
    不能使用arguments.callee (说实话我基本没用过)
    禁止this指向全局对象
    不能使用fn.caller和fn.arguments获取函数调用的堆栈 (说实话我基本没用过)
    增加了保留字（比如protected、static和interface）

- 2.关于let和const新增的变量声明

- 3.变量的解构赋值

- 4.字符串的扩展
    includes()：返回布尔值，表示是否找到了参数字符串。
    startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
    endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。
- 5.数值的扩展
    Number.isFinite()用来检查一个数值是否为有限的（finite）。
    Number.isNaN()用来检查一个值是否为NaN。
- 6.函数的扩展
    函数参数指定默认值
- 7.数组的扩展
    扩展运算符
- 8.对象的扩展
    对象的解构
- 9.新增symbol数据类型

- 10.Set 和 Map 数据结构 
    ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。 Set 本身是一个构造函数，用来生成 Set 数据结构。
    
    Map它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。
- 11.Proxy
    Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问
    都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。
    Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。
    Vue3.0使用了proxy
- 12.Promise
    Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。
    特点是：
    对象的状态不受外界影响。
    一旦状态改变，就不会再变，任何时候都可以得到这个结果。
- 13.async 函数 
    async函数对 Generator 函数的区别：
    （1）内置执行器。
    Generator 函数的执行必须靠执行器，而async函数自带执行器。也就是说，async函数的执行，与普通函数一模一样，只要一行。
    （2）更好的语义。
    async和await，比起星号和yield，语义更清楚了。async表示函数里有异步操作，await表示紧跟在后面的表达式需要等待结果。
    （3）正常情况下，await命令后面是一个 Promise 对象。如果不是，会被转成一个立即resolve的 Promise 对象。
    （4）返回值是 Promise。
    async函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用then方法指定下一步的操作。
- 14.Class 
    class跟let、const一样：不存在变量提升、不能重复声明...
    ES6 的class可以看作只是一个语法糖，它的绝大部分功能
    ES5 都可以做到，新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。
- 15.Module
    ES6 的模块自动采用严格模式，不管你有没有在模块头部加上"use strict";。
    import和export命令以及export和export default的区别


## 说一说SessionStorage和localStorage还有cookie

- 共同点：都是保存在浏览器端、且同源的

- 不同点：
    1.cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。
    cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下
    sessionStorage和localStorage不会自动把数据发送给服务器，仅在本地保存。
    2.存储大小限制也不同，cookie数据不能超过4K，sessionStorage和localStorage可以达到5M
    3.sessionStorage：仅在当前浏览器窗口关闭之前有效；
    localStorage：始终有效，窗口或浏览器关闭也一直保存，本地存储，因此用作持久数据；
    cookie：只在设置的cookie过期时间之前有效，即使窗口关闭或浏览器关闭
    4.作用域不同
    sessionStorage：不在不同的浏览器窗口中共享，即使是同一个页面；
    localstorage：在所有同源窗口中都是共享的；也就是说只要浏览器不关闭，数据仍然存在
    cookie: 也是在所有同源窗口中都是共享的.也就是说只要浏览器不关闭，数据仍然存在



## 说一说前端性能优化方案

三个方面来说明前端性能优化
- 一： webapck优化与开启gzip压缩
    1.babel-loader用 include 或 exclude 来帮我们避免不必要的转译，不转译node_moudules中的js文件
    其次在缓存当前转译的js文件，设置loader: 'babel-loader?cacheDirectory=true'
    2.文件采用按需加载等等
    3.具体的做法非常简单，只需要你在你的 request headers 中加上这么一句：
    accept-encoding:gzip
    4.图片优化，采用svg图片或者字体图标
    5.浏览器缓存机制，它又分为强缓存和协商缓存

- 二：本地存储——从 Cookie 到 Web Storage、IndexedDB
    说明一下SessionStorage和localStorage还有cookie的区别和优缺点

- 三：代码优化
    1.事件代理
    2.事件的节流和防抖
    3.页面的回流和重绘
    4.EventLoop事件循环机制
    5.代码优化等等


## var和let/const

 ** var和let/const的区别: **

块级作用域
不存在变量提升
暂时性死区
不可重复声明
let、const声明的全局变量不会挂在顶层对象下面


** const命令两个注意点: **

let可以先声明稍后再赋值,而const在 声明之后必须马上赋值，否则会报错


const 简单类型一旦声明就不能再更改，复杂类型(数组、对象等)指针指向的地址不能更改，内部数据可以更改。

- 简单类型(number、string、boolean)：内存地址就是值,即常量(一变就报错).


- 复杂类型(对象、数组等)：地址保存的是一个指针，const只能保证指针是固定的(总是指向同一个地址),它内部的值是可以改变的(不要以为const就安全了！)
所以只要不重新赋值整个数组/对象， 因为保存的是一个指针，所以对数组使用的push、shift、splice等方法也是允许的，你就是把值一个一个全都删光了都不会报错。

 

 ** let、const使用场景: **

let使用场景：变量，用以替代var。
const使用场景：常量、声明匿名函数、箭头函数的时候。




## 说一说什么是跨域，怎么解决

因为浏览器出于安全考虑，有同源策略。也就是说，如果协议、域名或者端口有一个不同就是跨域，Ajax 请求会失败。
为来防止CSRF攻击

- 1.JSONP

```h

    JSONP 的原理很简单，就是利用 <script> 标签没有跨域限制的漏洞。
    通过 <script> 标签指向一个需要访问的地址并提供一个回调函数来接收数据当需要通讯时。
    <script src="http://domain/api?param1=a&param2=b&callback=jsonp"></script>
    <script>
        function jsonp(data) {
        	console.log(data)
    	}
    </script>
    JSONP 使用简单且兼容性不错，但是只限于 get 请求。

```

- 2.CORS
    CORS 需要浏览器和后端同时支持。IE 8 和 9 需要通过 XDomainRequest 来实现。
- 3.document.domain
    该方式只能用于二级域名相同的情况下，比如 a.test.com 和 b.test.com 适用于该方式。

    只需要给页面添加 document.domain = 'test.com' 表示二级域名都相同就可以实现跨域
- 4.webpack配置proxyTable设置开发环境跨域
- 5.nginx代理跨域
- 6.iframe跨域
- 7.postMessage
    这种方式通常用于获取嵌入页面中的第三方页面数据。一个页面发送消息，另一个页面判断来源并接收消息



## webpack配置入口出口

```
module.exports={
    //入口文件的配置项
    entry:{},
    //出口文件的配置项
    output:{},
    //模块：例如解读CSS,图片如何转换，压缩
    module:{},
    //插件，用于生产模版和各项功能
    plugins:[],
    //配置webpack开发服务功能
    devServer:{}
}

```

简单描述了一下这几个属性是干什么的。
描述一下npm run dev / npm run build执行的是哪些文件
通过配置proxyTable来达到开发环境跨域的问题，然后又可以扩展和他聊聊跨域的产生，如何跨域
最后可以在聊聊webpack的优化，例如babel-loader的优化，gzip压缩等等

## webpack3和webpack4区别

```
1.mode

webpack增加了一个mode配置，只有两种值development | production。对不同的环境他会启用不同的配置。

2.CommonsChunkPlugin

CommonChunksPlugin已经从webpack4中移除。
可使用optimization.splitChunks进行模块划分（提取公用代码）。
但是需要注意一个问题，默认配置只会对异步请求的模块进行提取拆分，如果要对entry进行拆分
需要设置optimization.splitChunks.chunks = 'all'。

3.webpack4使用MiniCssExtractPlugin取代ExtractTextWebpackPlugin。

4.代码分割。

使用动态import，而不是用system.import或者require.ensure

5.vue-loader。

使用vue-loader插件为.vue文件中的各部分使用相对应的loader，比如css-loader等

6.UglifyJsPlugin

现在也不需要使用这个plugin了，只需要使用optimization.minimize为true就行，production mode下面自动为true

optimization.minimizer可以配置你自己的压缩程序

 

```
 
 
 ## get和post区别

 ```h

GET在浏览器回退时是无害的，而POST会再次提交请求。
GET产生的URL地址可以被Bookmark，而POST不可以。
GET请求会被浏览器主动cache，而POST不会，除非手动设置。
GET请求只能进行url编码，而POST支持多种编码方式。
GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
GET请求在URL中传送的参数是有长度限制的，而POST么有。
对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
GET参数通过URL传递，POST放在Request body中。

```


## 浏览器的回流与重绘

浏览器使用流式布局模型 (Flow Based Layout)。
浏览器会把HTML解析成DOM，把CSS解析成CSSOM，DOM和CSSOM合并就产生了Render Tree。
有了RenderTree，我们就知道了所有节点的样式，然后计算他们在页面上的大小和位置，最后把节点绘制到页面上。
由于浏览器使用流式布局，对Render Tree的计算通常只需要遍历一次就可以完成，但table及其内部元素除外，他们可能需要多次计算，通常要花3倍于同等元素的时间，这也是为什么要避免使用table布局的原因之一。

`回流必将引起重绘，重绘不一定会引起回流`
`回流比重绘的代价要更高`


1、回流 (Reflow)

> 当Render Tree中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程称为回流。

```h

会导致回流的操作：

页面首次渲染
浏览器窗口大小发生改变
元素尺寸或位置发生改变
元素内容变化（文字数量或图片大小等等）
元素字体大小变化
添加或者删除可见的DOM元素
激活CSS伪类（例如：:hover）
查询某些属性或调用某些方法

```

2、重绘 (Repaint)

> 当页面中元素样式的改变并不影响它在文档流中的位置时（例如：color、background-color、visibility等），浏览器会将新样式赋予给元素并重新绘制它，这个过程称为重绘。

3、如何避免

> CSS

避免使用table布局。
尽可能在DOM树的最末端改变class。
避免设置多层内联样式。
将动画效果应用到position属性为absolute或fixed的元素上。
避免使用CSS表达式（例如：calc()）。

> JavaScript

避免频繁操作样式，最好一次性重写style属性，或者将样式列表定义为class并一次性更改class属性。
避免频繁操作DOM，创建一个documentFragment，在它上面应用所有DOM操作，最后再把它添加到文档中。
也可以先为元素设置display: none，操作结束后再把它显示出来。因为在display属性为none的元素上进行的DOM操作不会引发回流和重绘。
避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。





***

***


# 前端的一些知识模块
** HTML **
对Web理解，不同浏览器的内核区分和兼容性，选择器的优先级，标签元素类型，HTML5，HTML网页缓存与存储

** CSS **
flex布局，简单布局，圣杯布局和双飞翼布局，响应式布局，动画，编译工具（Sass,Less,Stylus），盒子模型，定位机制，iconfont字体，CSS3常见新特性

** JavaScript **
数据类型，对象操作，算法运算，继承，闭包，作用域，原型链，事件，function，JSON，AJax，RegExp，跨域，异步操作，DOM，BOM，内存泄漏，前端MVC，模块化，路由，Canvas，ES6，NodeJs，React，VUE

** 其他 **
移动端，自动化构建，WEB安全，项目优化，项目重构，职业规划，学习，团队协作

作为一名前端工程师，应该有一颗随时学习的心态，学会总结，对于有些知识点我们应该时常记住。



## flex布局


`flex: 0 1 auto`

```

不会增长变大占据flex容器中额外的剩余空间（flex-grow:0）

会收缩变小以适合容器（flex-shrink:1) 

尺寸根据自身宽高属性进行调整（flex-basis:auto）

```

flex财产分配三剑客
最后再说说一开始提到的`flex-grow，flex-shrink和flex-basis`。

一定要牢记这3个属性默认值，不然遇到只有1~2个参数时候，根本不知道什么意思。

`flex-grow`
flex-grow指定了容器剩余空间多余时候的分配规则，默认值是0，多余空间不分配。

`flex-shrink`
flex-shrink指定了容器剩余空间不足时候的分配规则，默认值是1，空间不足要分配。

`flex-basis`
flex-basis则是指定了固定的分配数量，默认值是auto。如会忽略设置的同时设置width或者height属性。flex-basis包含大量的细节知识，这个可以专门开一篇文章深入探讨。


`1个值`
如果flex的属性值只有一个值，则：
如果是数值，例如flex: 1，则这个1表示flex-grow，此时flex-shrink和flex-basis都使用默认值，分别是1和auto。
如果是长度值，例如flex: 100px，则这个100px显然指flex-basis，因为3个缩写CSS属性中只有flex-basis的属性值是长度值。此时flex-grow和flex-shrink都使用默认值，分别是0和1。

`2个值`
如果flex的属性值有两个值，则第1个值一定指flex-grow，第2个值根据值的类型不同表示不同的CSS属性，具体规则如下：
如果第2个值是数值，例如flex: 1 2，则这个2表示flex-shrink，此时flex-basis使用默认值auto。
如果第2个值是长度值，例如flex: 1 100px，则这个100px指flex-basis，此时flex-shrink都使用默认值0。

`3个值`
如果flex的属性值有三个值，则这3个值分别表示flex-grow，flex-shrink和flex-basis。这个顺序该如何记忆，时间久了岂不是很容易忘记。这个告诉大家我的邪恶记忆法，叫做“能大能小的鸡鸡”。grow是增加变大意思，shrink是收缩变小的意思，而basis是基（鸡）础基（鸡）准的意思，于是连起来就是“能大能小的鸡鸡”

# HTML

## XHTML与HTML的有何异同？

HTML是一种基于WEB的网络设计语言，XHTML是基于XML的置标语言，XHTML可以认为是XML版的HTML，所以它的语法比较严谨：元素必须关闭，嵌套必须正确，大小写区分，属性值必须用双引号，id属性代替name属性


##  Doctype作用？标准模式与兼容模式各有什么区别?

1、`<!DOCTYPE>`声明位于位于HTML文档中的第一行，处于 `<html> `标签之前。告知浏览器的解析器用什么文档标准解析这个文档。`DOCTYPE`不存在或格式不正确会导致文档以兼容模式呈现。

2、`标准模式的排版` 和 `JS运作模式`都是以该浏览器支持的最高标准运行。在兼容模式中，页面以宽松的形式向后兼容的方式显示,模拟老式浏览器的行为防止页面渲染失败。
***
## HTML5 为什么只需要写 <!DOCTYPE HTML>？
HTML5不基于`SGML`,所以不需要对DTD进行引用，但是他也需要`DOCTYPE`来规范浏览器的行为。
***
## 常见的浏览器有哪些，及其浏览器的内核是啥？
Trident内核：IE,MaxThon,TT,The World,360,搜狗浏览器等。  [又称MSHTML]
Gecko内核：Netscape6及以上版本，FF,MozillaSuite/SeaMonkey等
Presto内核：Opera7及以上。          [Opera内核原为：Presto，现为：Blink;]
Webkit内核：Safari,Chrome等。      [ Chrome的：Blink（WebKit的分支）]
***
## 浏览器内核的理解？
主要分为两部分：`渲染引擎（Layout engineer或Rendering Engine）`和`JS引擎`
> 渲染引擎:负责网页的内容（HTML、XML、图像等）、整理讯息（引入CSS、JS等），计算网页的显示方式，输出显示。不同的浏览器内核对于网页的语法解释也就不同，就存在网页渲染的效果不同，这也就是兼容性。

> JS引擎：解析和执行JavaScript实现网页动态效果。
***
## 页面样式引入，`link`与``@import`的区别？
1、`link`属于XHTML标签，除了加载CSS外，还能用于定义RSS, 定义rel连接属性等作用；
2、`@import`是CSS提供的，只能用于加载CSS;
3、页面被加载的时，link会同时被加载，而@import引用的CSS会等到页面被加载完再加载;
4、`import`是CSS2.1 提出的，只在IE5以上才能被识别，而`link`是XHTML标签，`无兼容问题`;
***
## 行内元素有哪些，块级元素有哪些，空元素有哪些？
> CSS规范规定，每个元素都有`display`属性，确定该元素的类型，每个元素都有默认的`display`值，如div的display默认值为“block”，则为“块级”元素；span默认display属性值为“inline”，是“行内”元素。

行内元素: `a b span img input select strong(强调的语气)`
块级元素：`div p ul ol li dl dt dd h1 h2 h3 h4 ...`
常见的空元素: `<br> <hr> <img> <input> <link> <meta>`和`<area> <base> <col> <command> <embed> <keygen> <param> <source> <track> <wbr>`
***
## HTML5有哪些新特性、移除了那些元素？如何处理HTML5新标签的浏览器兼容问题？

新特性：

画布`canvas`
用于媒介播放的`video和audio`
新的语义化标签：`article，header，nav，section，footer`
新的本地存储：`localstorage，sessionstorage`
新的表单控件：`date，time，calendar，url`
新的技术：`websocket，web worker，geoloacation`
移除得元素：
可以用css代替的元素，`font，fontbase，center，s，tt，u`
————————————————


```h
HTML5 现在已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等功能的增加。
    - 绘画 canvas;
    - 用于媒介回放的 video 和 audio 元素;
    - 本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失;
    - sessionStorage 的数据在浏览器关闭后自动删除;
    - 语意化更好的内容元素，比如 article、footer、header、nav、section;
    - 表单控件，calendar、date、time、email、url、search;
    - 新的技术webworker, websocket, Geolocation;

    - 移除的元素：
        纯表现的元素：basefont，big，center，font, s，strike，tt，u;
        对可用性产生负面影响的元素：frame，frameset，noframes；

   - 支持HTML5新标签：
        IE8/IE7/IE6支持通过document.createElement方法产生的标签，
        可以利用这一特性让这些浏览器支持HTML5新标签，
        浏览器支持新标签后，还需要添加标签默认的样式。

        当然也可以直接使用成熟的框架、比如html5shim;
        <!--[if lt IE 9]>
            <script> src="http://html5shim.googlecode.com/svn/trunk/html5.js"</script>
        <![endif]-->


```

## 如何区分 HTML 和 HTML5？

如何区分HTML5： DOCTYPE声明\新增的结构元素\功能元素

***
## 简述一下你对HTML语义化的理解？
`用正确的标签做正确的事情。`
html语义化让页面的内容结构化，结构更清晰，便于对浏览器、搜索引擎解析;
即使在没有样式CSS情况下也以一种文档格式显示，并且是容易阅读的;
搜索引擎的爬虫也依赖于HTML标记来确定上下文和各个关键字的权重，利于SEO;
使阅读源代码的人对网站更容易将网站分块，便于阅读维护理解。
***
## HTML5的离线储存怎么使用，工作原理能不能解释一下？
>  原理：HTML5的离线存储是基于一个新建的.appcache文件的缓存机制(不是存储技术)，通过这个文件上的解析清单离线存储资源，这些资源就会像cookie一样被存储了下来。之后当网络在处于离线状态下时，浏览器会通过被离线存储的数据进行页面展示。

如何使用：
1、页面头部像下面一样加入一个`manifest`的属性；
2、在`cache.manifest`文件的编写离线存储的资源；
    `CACHE  MANIFEST #v0.11 CACHE: js/app.js css/style.css NETWORK: resourse/logo.png FALLBACK: / /offline.html`
3、在离线状态时，操作`window.applicationCache`进行需求实现。

详细的使用请参考：[HTML5 离线缓存-manifest简介](http://yanhaijing.com/html/2014/12/28/html5-manifest/),[有趣的HTML5：离线存储](http://segmentfault.com/a/1190000000732617)
***

## 浏览器是怎么对HTML5的离线储存资源进行管理和加载的呢？
在线的情况下，浏览器发现html头部有`manifest`属性，它会请求`manifest`文件，如果是第一次访问app，那么浏览器就会根据manifest文件的内容下载相应的资源并且进行离线存储。如果已经访问过app并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面，然后浏览器会对比新的manifest文件与旧的manifest文件，如果文件没有发生改变，就不做任何操作，如果文件改变了，那么就会重新下载文件中的资源并进行离线存储。
离线的情况下，浏览器就直接使用离线存储的资源。
详细请参考：[有趣的HTML5：离线存储](http://segmentfault.com/a/1190000000732617)
***

## HTML语义化的理解？

可以清晰的向浏览器和开发者描述其意义，为了在丢失css的情况下也能很好的显示页面的结构，利于seo，seo可以根据标签和上下文己算权重，方便其他设备解析，方便开发和维护，可读性高

## iframe有那些缺点

会有过多的http请求，影响性能，会阻塞主页面的onload事件，不利于seo，页面调试样式很麻烦，会出现很多滚动条，浏览器后退按钮会没有效果，小型移动设备兼容性不好，会显示不全

## 描述一下 `Cookies`，`SessionStorage` 和 `LocalStorage` 的区别？
`cookie`是网站为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。
cookie数据始终在同源的http请求中携带（即使不需要），记会在浏览器和服务器间来回传递。
`sessionStorage`和`localStorage`不会自动把数据发给服务器，仅在本地保存。
存储大小：
* cookie数据大小不能超过4k。
* sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。

有期时间：
* localStorage    存储持久数据，浏览器关闭后数据不丢失除非主动删除数据；
* sessionStorage  数据在当前浏览器窗口关闭后自动删除。
* cookie          设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭
***

## **Iframe有那些缺点？**
iframe会阻塞主页面的Onload事件；
搜索引擎的检索程序无法解读这种页面，不利于SEO;

iframe和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载。
> 使用iframe之前需要考虑这两个缺点。如果需要使用iframe，最好是通过javascript动态给iframe添加src属性值，这样可以绕开以上两个问题。
***

## Label的作用是什么？是怎么用的？
> `label`标签来定义表单控制间的关系,当用户选择该标签时，浏览器会自动将焦点转到和标签相关的表单控件上。

```
<label for="Name">Number:</label>
<input type=“text“name="Name" id="Name"/>

<label>Date:<input type="text" name="B"/></label>
```
***

## 列出display的值，说明他们的作用？position的值， relative和absolute定位原点是？

```h
display：none，block，inline，inline-block，flex，grid，table，table-cell
position：static，relatice，absolute，fixed，sticky
relative定位原点是自己，absolute定位原点是离自己最近的父元素

```

## 前端的角度出发做好seo需要做什么

1、提高页面加载速度。 能用css解决的不用背景图片，背景图片也尽量压缩大小，可以几个icons放在一个图片上，使用background-position找到需要的图片位置。可以减少HTTP请求数，提高网页加载速度。
2、 结构、表现和行为的分离。另外一个重要的拖慢网页加载速度的原因就是将css和JS都堆积在HTML页面上，每次看到有人直接在页面上编写CSS和JS我都很痛心疾首。通过外链的方式能大大加快网页加载速度的，css文件可以放在head里，JS文件可以放置在body的最下方，在不影响阅读的情况下再去加载JS文件。
3、 优化网站分级结构。在每个内页加面包屑导航是很有必要的，可以让蜘蛛进入页面之后不至于迷路，有条件的话，最好能单独加个Sitemap页面，将网站结构一目了然地展示在蜘蛛面前，更有利于蜘蛛抓取信息。
4、 集中网站权重。由于蜘蛛分配到每个页面的权重是一定的，这些权重也将平均分配到每个a链接上，那么为了集中网站权重，可以使用”rel=nofollow”属性，它告诉蜘蛛无需抓取目标页,可以将权重分给其他的链接。
5、 文本强调标签的使用。当着重强调某个关键词需要加粗表示，选用strong标签比使用b标签要更有强调作用。
6、 a标签的title属性的使用。在不影响页面功能的情况下，可以尽量给a标签加上title属性，可以更有利于蜘蛛抓取信息。
7、 图片alt属性的使用。这个属性可以在图片加载不出来的时候显示在页面上相关的文字信息，作用同上。
8、 H标签的使用。主要是H1标签的使用需要特别注意，因为它自带权重，一个页面有且最多只能有一个H1标签，放在该页面最重要的标题上面，如首页的logo上可以加H1标签。
————————————————

## h5新特性

1、绘画的 canvas 元素
2、用于媒介回放的 video 和 audio 元素
3、对本地离线存储的更好的支持
4、新的特殊内容元素，比如 article、footer、header、nav、section 5、新的表单控件，比如 calendar、date、time、email、url、search



***

***

# CSS

## 介绍一下标准的CSS的盒子模型？低版本IE的盒子模型有什么不同的？
（1）有两种， `IE 盒子模型`、`W3C 盒子模型`；
（2）盒模型： 内容(content)、填充(padding)、边界(margin)、 边框(border)；
（3）区  别： IE的content部分把 border 和 padding计算了进去;
***

## css选择符有哪些？优先级算法如何计算

标签选择符，类选择符，ID选择符，伪类选择符，相邻选择符，子代选择符，后代选择符，通配符选择符，属性选择符
优先级：

就近原则，下面覆盖上面
`!important＞内联＞ID＞类＞标签`

## css实现垂直水平居中

定位，top和left设置50%，再通过transform的translate(-50%, -50%)设置实现垂直水平居中
定位，top和left设置50%，再通过margin-top和margin-left自己宽高的一半设置实现垂直水平居中
定位，top，bottom，left，right都设置为0，再通过margin设置为auto实现垂直水平居中
flex布局，先设置justify-content为center实现水平居中，再设置align-items为center实现垂直居中
table布局，设置父元素display为table-cell，再设置vertical-align为middle实现垂直居中，把自己display设置为inline-block，父级设置text-align为center实现水平居中
————————————————

## 行内元素和块级元素?img算什么?行内元素怎么转化为块级元素?

```h

行内元素：
    和有他元素都在一行上，高度、行高及外边距和内边距都不可改变，
    文字图片的宽度不可改变，只能容纳文本或者其他行内元素；
    其中img是行元素

块级元素：
    总是在新行上开始，高度、行高及外边距和内边距都可控制，
    可以容纳内敛元素和其他元素；
    
行元素转换为块级元素方式：display：block；

```

## 将多个元素设置为同一行?清除浮动有几种方式?

```h

将多个元素设置为同一行：float，inline-block
清除浮动的方式：方法一：添加新的元素 、应用 clear：both；
方法二：父级div定义 overflow: hidden；
方法三：利用:after和:before来在元素内部插入两个元素块，从面达到清除浮动的效果。
.clear{zoom:1;}
.clear:after{
    content:””;clear:both;display:block;height:0;
    overflow:hidden;visibility:hidden;
    }

```

## :before 和 ::before 区别

单冒号(:)用于CSS3伪类，双冒号(::)用于CSS3伪元素。 对于CSS2之前已有的伪元素，比如:before，单冒号和双冒号的写法::before作用是一样的。



## 伪类选择器和伪元素？c3中引入的伪类选择器有？c3中伪元素有

伪类用一个冒号来表示，而伪元素则用两个冒号来表示。

伪类选择器：
由于状态是动态变化的，所以一个元素达到一个特定状态时，它可能得到一个伪类的样式；当状态改变时，它又会失去这个样式。

伪元素选择器：
并不是针对真正的元素使用的选择器，而是针对CSS中已经定义好的伪元素使用的选择器；

c3中引入的伪类选择器：

> :root()选择器，根选择器，匹配元素E所在文档的根元素。在HTML文档中，根元素始终是<html>。:root选择器等同于<html>元素。

> :not()选择器称为否定选择器，和jQuery中的:not选择器一模一样，可以选择除某个元素之外的所有元素。

> :empty()选择器表示的就是空。用来选择没有任何内容的元素，这里没有内容指的是一点内容都没有，哪怕是一个空格。

> :target()选择器来对页面某个target元素(该元素的id被当做页面中的超链接来使用)指定样式，该样式只在用户点击了页面中的超链接，并且跳转到target元素后起作用。

> :first-child()选择器表示的是选择父元素的第一个子元素的元素E。简单点理解就是选择元素中的第一个子元素，记住是子元素，而不是后代元素。

> :nth-child()选择某个元素的一个或多个特定的子元素。

> :nth-last-child()从某父元素的最后一个子元素开始计算，来选择特定的元素

> :nth-of-type(n)选择器和:nth-child(n)选择器非常类似，不同的是它只计算父元素中指定的某种类型的子元素。

> :only-child表示的是一个元素是它的父元素的唯一一个子元素。

> :first-line为某个元素的第一行文字使用样式。

> :first-letter 为某个元素中的文字的首字母或第一个字使用样式。

> :before 在某个元素之前插入一些内容。

> :after 在某个元素之后插入一些内容。

c3中伪元素：

> ::first-line选择元素的第一行，比如说改变每个段落的第一行文本的样式
> ::before和::after这两个主要用来给元素的前面或后面插入内容，这两个常用"content"配合使用，见过最多的就是清除浮动
> ::selection用来改变浏览网页选中文的默认效果



## CSS3有哪些新特性

新选择器
阴影
圆角
自定义字体font-face
多列布局
渐变效果
transition
transform
animation

## 移动端常见的适配不同屏幕大小的方法

meta标签使用viewport属性
用rem代替px
使用框架flexible.js

## 高宽未知的图片如何在一个比他大的容器内水平居中

把图片设置为display: inline-block，父容器设置text-align: center
图片设置margin: 0 auto
父元素设置display: flex， justify-content: center

## 常见兼容性问题

* png24位的图片在iE6浏览器上出现背景，解决方案是做成PNG8.也可以引用一段脚本处理。

* 浏览器默认的margin和padding不同。解决方案是加一个全局的*{margin:0;padding:0;}来统一。

* IE6双边距bug:块属性标签float后，又有横行的margin情况下，在ie6显示margin比设置的大。

* 浮动ie产生的双倍距离（IE6双边距问题：在IE6下，如果对元素设置了浮动，同时又设置了margin-left或margin-right，margin值会加倍。） #box{ float:left; width:10px; margin:0 0 0 100px;} 这种情况之下IE会产生20px的距离，解决方案是在float的标签样式控制中加入 ——_display:inline;将其转化为行内属性。(_这个符号只有ie6会识别)

* 渐进识别的方式，从总体中逐渐排除局部。 首先，巧妙的使用“\9”这一标记，将IE游览器从所有情况中分离出来。 接着，再次使用“+”将IE8和IE7、IE6分离开来，这样IE8已经独立识别。
css// .bb{ background-color:#f1ee18; .background-color:#00deff\9; +background-color:#a200ff; _background-color:#1e0bd1; }

* IE下,可以使用获取常规属性的方法来获取自定义属性, 也可以使用getAttribute()获取自定义属性; Firefox下,只能使用getAttribute()获取自定义属性. 解决方法:统一通过getAttribute()获取自定义属性。

* IE下,event对象有x,y属性,但是没有pageX,pageY属性; Firefox下,event对象有pageX,pageY属性,但是没有x,y属性. * 解决方法：（条件注释）缺点是在IE浏览器下可能会增加额外的HTTP请求数。

* Chrome 中文界面下默认会将小于 12px 的文本强制按照 12px 显示, 可通过加入 CSS 属性 -webkit-text-size-adjust: none; 解决。

* 超链接访问过后hover样式就不出现了 被点击访问过的超链接样式不在具有hover和active了解决方法是改变CSS属性的排列顺序: L-V-H-A : a:link {} a:visited {} a:hover {} a:active {}

* 怪异模式问题：漏写DTD声明，Firefox仍然会按照标准模式来解析网页，但在IE中会触发怪异模式。为避免怪异模式给我们带来不必要的麻烦，最好养成书写DTD声明的好习惯。现在可以使用[html5](http://www.w3.org/TR/html5/single-page.html)推荐的写法：`<doctype html>`

* 上下margin重合问题
ie和ff都存在，相邻的两个div的margin-left和margin-right不会重合，但是margin-top和margin-bottom却会发生重合。
解决方法，养成良好的代码编写习惯，同时采用margin-top或者同时采用margin-bottom。

* ie6对png图片格式支持不好(引用一段脚本处理)



## px/em/rem有什么区别？ 为什么通常给font-size 设置的字体为62.5%

相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。
1、em的值并不是固定的；
2、em会继承父级元素的字体大小。使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。
rem是相对于浏览器进行缩放的。1rem默认是16px，在响应式布局中，一个个除来转换成rem，太麻烦，所以重置rem
`body{font-size=62.5% } 此时1rem = 10px;若是12px则是1.2rem`.

## 如何对css文件进行压缩合并? gulp ?

```h

使用gulp, 首页全局安装gulp。
1、npm install --global gulp
2、其次局部安装gulp。npm install gulp --save-dev
3、在项目根目录下创建一个名为 gulpfile.js 的文件
var gulp = require('gulp');
gulp.task('default', function() {
// 将你的默认的任务代码放在这});
4、运行gulp。（默认的名为 default 的任务（task）将会被运行，想要单独执行特定的任务（task），请输入 gulp <task> <othertask>）
gulp
合并和压缩JS、CSS文件
压缩JS，CSS文件需要引用如下组件：
gulp-minify-css: 压缩css

```



## a点击出现框，解决方法

```h

a,a:hover,a:active,a:visited,a:link,a:focus{ 
 -webkit-tap-highlight-color:rgba(0,0,0,0);
 -webkit-tap-highlight-color: transparent;
 outline:none;background: none;
 text-decoration: none;border:none;
 -webkit-appearance: none; }

 ```

 ## canvas 如何绘制一个三角形|正方形

 moveto 是移动到某个坐标， lineto 是从当前坐标连线到某个坐标。
这两个函数加起来就是画一条直线。 画线要用“笔”，那么MoveTo()把笔要画的起始位置固定了（x,y）然后要固定终止位置要用到LineTo函数确定终止位置（xend,yend）,这样一条线就画出来了。 每次与前面一个坐标相连 。
stroke() 方法会实际地绘制出通过 moveTo() 和 lineTo() 方法定义的路径。默认颜色是黑色。

```h

<!DOCTYPE HTML5>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
  <title>画布</title>
  </head> 
<body>
     <canvas id="myCanvas" width="200" height="100" style="border:1px solid #c3c3c3;">
     Your browser does not support the canvas element.
     </canvas>
     <script type="text/javascript">
         var c=document.getElementById("myCanvas");
//三角形
         var cxt=c.getContext("2d");
         cxt.moveTo(10,10);
         cxt.lineTo(50,50);
         cxt.lineTo(10,50);
         cxt.lineTo(10,10);
         cxt.stroke();
//正方形
         var cxt2=c.getContext("2d");
         cxt2.moveTo(60,10);
         cxt2.lineTo(100,10);
         cxt2.lineTo(100,50);
         cxt2.lineTo(60,50);
         cxt2.lineTo(60,10);
         cxt2.stroke(); 
     </script>
</body>
</html>

```

***

# 算法题

### 快速排序

思路:
- 随机选择数组中的一个数 A，以这个数为基准
- 其他数字跟这个数进行比较，比这个数小的放在其左边，大的放到其右边
- 经过一次循环之后，A 左边为小于 A 的，右边为大于 A 的
- 这时候将左边和右边的数再递归上面的过程
```h
const Arr = [85, 24, 63, 45, 17, 31, 96, 50];
function quickSort(arr) {
    if (arr.length <= 1) {
        return arr;
    }
    let pivotIndex = Math.floor(arr.length / 2);
    let pivot = arr.splice(pivotIndex, 1)[0];
    let left = [];
    let right = [];
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] < pivot) {
            left.push(arr[i]);
        } else {
            right.push(arr[i]);
        }
    }
    // 递归
    return quickSort(left).concat([pivot], quickSort(right));
}

console.log(quickSort(Arr));
```
ps:
这是阮老师的一个快排写法，网上对于这个的争论很多，第一说了阮老师不应该用splice去取值，应该用下标，还有就是不应该每次都从新开俩个新数组。
其实我觉得算法题重要的是思路，实现的方式有很多，不一定说谁对谁错，效率越好的算法的确是我们想要的，但是更多的理解一些不同的实现思路，我觉得也是可以的～。

这里是不同的声音： [面试官：阮一峰版的快速排序完全是错的](https://link.juejin.im/?target=https%3A%2F%2Fjuejin.im%2Fpost%2F5af4902a6fb9a07abf728c40)

***

### 二分排序法

二分查找法主要是解决「在一堆有序的数中找出指定的数」这类问题，不管这些数是一维数组还是多维数组，只要有序，就可以用二分查找来优化。
二分查找是一种「分治」思想的算法，大概流程如下：

- 数组中排在中间的数字 A，与要找的数字比较大小
- 因为数组是有序的，所以： a) A 较大则说明要查找的数字应该从前半部分查找 b) A
- 较小则说明应该从查找数字的后半部分查找
- 这样不断查找缩小数量级（扔掉一半数据），直到找完数组为止


题目：在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
```h

function Find(target, array) {
    let i = 0;
    let j = array[i].length - 1;
    while (i < array.length && j >= 0) {
        if (array[i][j] < target) {
            i++;
        } else if (array[i][j] > target) {
            j--;
        } else {
            return true;
        }
    }
    return false;
}

//测试用例
console.log(Find(10, [
    [1, 2, 3, 4], 
    [5, 9, 10, 11], 
    [13, 20, 21, 23]
    ])
);

```
***

### 解析url后的参数

```h
function parseParam(url) {
  let obj = {};
  let arr = url.split("?");
  if (arr.length == 1) { //判断没有问号
    return "无参数"
  }
  let total = arr[1].split("&");
  for (let i = 0; i < total.length; i++) {
    let single = total[i].split("=");
    if (single[0] == '') { //判断有？但是没有参数
      return '无参数'
    }
    if (!single[1]) {
      obj[single[0]] = true;
    } else {
      if (obj[single[0]]) {
        let concat
        if (!Array.isArray(obj[single[0]])) { //判断是否数组
          concat = [obj[single[0]]]
        } else {
          concat = obj[single[0]];
        }
        concat.push(single[1]);
        concat = new Set(concat);
        concat = Array.from(concat) //数组去重
        obj[single[0]] = concat
      } else {
        obj[single[0]] = decodeURI(single[1]) //进行转码
      }
    }
  }
  return obj
}

var url = 'http://www.baidu.com/?user=huixin&id=123&id=456&city=%E5%8C%97%E4%BA%AC&enabled';

var params = parseParam(url)

console.log(params)

```
***

### 实现一个简单的模版引擎：

例如：我叫a,年龄b，性别c； let data = { name: '小明', age: 18, } 没有定义的返回undefined

```h

let template = '我是{name}，年龄{age}，性别{sex}';
let data = {
    name: '小明',
    age: 18,
}
const  reg= /({([a-zA-Z]+)})/g;
var r= '',regrounp={};
while( r = reg.exec(template) ){
    Object.defineProperty(regrounp,r[2],{
        enumerable:true,
        value:r[2]
    })
}

var render = (template,regrounp)=>{
    var result='';
    for( key in regrounp){
        if(data[key] == undefined){
            result  = (result || template).replace(new RegExp(`{${regrounp[key]}}`,"g"),undefined);
        }else{		
            result  = (result || template).replace(new RegExp(`{${regrounp[key]}}`,"g"),data[key]);
        }
    }
    return result
}
let newtemple = render(template, regrounp);
console.log(newtemple) // 结果： 我是小明，年龄18，性别undefined

```

对于{}这样声明的对象，可以直接枚举，Object.defineProperty声明出的对象，如果不定义enumerable:true的话，是不能用for-in 枚举的。


这里有一片很好的文章 推荐 [编写一个简单的JavaScript模板引擎](https://link.juejin.im/?target=https%3A%2F%2Fjuejin.im%2Fpost%2F5b3b93115188251afa62ad46)

***

### 如何快速让字符串变成已千为精度的数字

```h
function exchange(num) {
    num += ''; //转成字符串
    if (num.length <= 3) {
        return num;
    }

    num = num.replace(/\d{1,3}(?=(\d{3})+$)/g, (v) => {
        console.log(v)
        return v + ',';
    });
    return num;
}

console.log(exchange(1234567));
```
***

### 实现 JS 对象的深拷贝

深拷贝就是在拷贝数据的时候，将数据的所有引用结构都拷贝一份。简单的说就是，在内存中存在两个数据结构完全相同又相互独立的数据，将引用型类型进行复制，而不是只复制其引用关系。
分析下怎么做 深拷贝 ：

- 首先假设深拷贝这个方法已经完成，为 deepClo
- 要拷贝一个数据，我们肯定要去遍历它的属性，如果这个对象的属性仍是对象，继续使用这个方法，如此往复

```h
function deepClo(o1, o2) {
    for (let k in o2) {
        if (typeof o2[k] === 'object') {
            o1[k] = {};
            deepClo(o1[k], o2[k]);
        } else {
            o1[k] = o2[k];
        }
    }
}
// 测试用例
let obj = {
    a: 1,
    b: [1, 2, 3],
    c: {}
};
let emptyObj = Object.create(null);
deepClo(emptyObj, obj);
console.log(emptyObj.a == obj.a);
console.log(emptyObj.b == obj.b);

```
递归容易造成爆栈，尾部调用可以解决递归的这个问题，Chrome 的 V8 引擎做了尾部调用优化，我们在写代码的时候也要注意尾部调用写法。递归的爆栈问题可以通过将递归改写成枚举的方式来解决，就是通过for或者while来代替递归。

***

### 求斐波那契数列（兔子数列） 1,1,2,3,5,8,13,21,34,55,89...中的第 n 项

```h
下面的代码中count记录递归的次数，我们看下两种差异性的代码中的count的值：
 let count = 0;
 function fn(n) {
    let cache = {};
    function _fn(n) {
        if (cache[n]) {
            return cache[n];
        }
        count++;
        if (n == 1 || n == 2) {
            return 1;
        }
        let prev = _fn(n - 1);
        cache[n - 1] = prev;
        let next = _fn(n - 2);
        cache[n - 2] = next;
        return prev + next;
    }
    return _fn(n);
}

let count2 = 0;
function fn2(n) {
    count2++;
    if (n == 1 || n == 2) {
        return 1;
    }
    return fn2(n - 1) + fn2(n - 2);
}

console.log(fn(20), count); // 6765 20
console.log(fn2(20), count2); // 6765 13529

```
***

### 算法的效率

算法的好坏可以通过算法复杂度来衡量，算法复杂度包括时间复杂度和空间复杂度两个。时间复杂度由于好估算、好评估等特点，是面试中考查的重点。空间复杂度在面试中考查得不多。
常见的时间复杂度有：

- 常数阶 O(1)
- 对数阶 O(logN)
- 线性阶 O(n)
- 线性对数阶 O(nlogN)
- 平方阶 O(n^2)
- 立方阶 O(n^3)
- !k次方阶 O(n^k)
- 指数阶 O(2^n)

随着问题规模 n 的不断增大，上述时间复杂度不断增大，算法的执行效率越低。
一般做算法复杂度分析的时候，遵循下面的技巧：

- 看看有几重循环，一般来说一重就是O(n)，两重就是 O(n^2)，以此类推
- 如果有二分，则为O(logN)
- 保留最高项，去除常数项

题目：分析下面代码的算法复杂度

```h
let i =0; // 语句执行一次 
while (i < n) { // 语句执行 n 次 
  console.log(`Current i is ${i}`); //语句执行 n 次
  i++; // 语句执行 n 次
}
根据注释可以得到，算法复杂度为1 + n + n + n = 1 + 3n，去除常数项，为O(n)。

```
更多阅读：
[在 JavaScript 中学习数据结构与算法](https://link.juejin.im/?target=https%3A%2F%2Fjuejin.im%2Fpost%2F594dfe795188250d725a220a%23heading-18)
[我接触过的前端数据结构与算法](https://link.juejin.im/?target=https%3A%2F%2Fjuejin.im%2Fpost%2F5958bac35188250d892f5c91%3Futm_source%3Dgold_browser_extension)


