---
title: ES6攻略(五)
date: 2018-05-29 22:50:35
tags: [编程,Javascript,ES6]
description: 
copyright: false
categories: ES6
top:
---
ES6的改版，主要是通过引入JAVA等静态语言的优秀思想来解决老版本的一些痼疾，如作用域，回调，继承和封装等问题。这些改革措施是非常成功的，ES6让JS真正变成了一种好用的语言。这里是关于一些导出模块。

![ES6攻略](https://coding.net/u/DRuilong/p/phone_drl/git/raw/master/image/es6_d.png)

<!-- more -->

# 数组扩展

## forEach()方法
这个方法其实是`es5`的，还是很好用的。
```h
var arr = ['a', 'b', 'c', 'd'];
//遍历数组
arr.forEach(function(val){
 console.log(val);
});
```
## Array.from()方法

它是将两类对象装换为真的数组：`类数组对象arguments`、`可遍历对象`（可数Object、Set、Map).

```h
let myObj = {length:2,'0':'hello','1':'world'};
console.log(Array.from(myObj));
//[ 'hello', 'world' ]

<!-- Set -->

let mySet = new Set([1,1,2,3]);
mySet.add('hello');
mySet.add('hello');
mySet.add(NaN);
mySet.add(NaN);
mySet.add(undefined);
mySet.add(undefined);
console.log(Array.from(mySet));
//[ 1, 2, 3, 'hello', NaN, undefined ]

<!-- Map -->
let myMap = new Map();
myMap.set(0,'hello');
myMap.set(true,'world');
myMap.set(1,'haha');
console.log(Array.from(myMap));
//[ [ 0, 'hello' ], [ true, 'world' ], [ 1, 'haha' ] ]

<!-- arguments -->
function foo(){
console.log(Array.from(arguments));
}
foo(1,3,5);
//[ 1, 3, 5 ]

```

## find()和findindex()

`find()`查找数组里符合条件的`第一个值`
`findindex()`是查找数组里符合条件的第一个索引的`下标`

```h

<!-- find() -->
console.log([-1, 4, -5, 10].find((n) => n < 2));
// -1

<!-- findindex() -->
console.log([1, 5, 10, 15].findIndex(function(value, index, arr) {
 return value > 4;
 //5这个值的索引下标是1
}));

// 1

```

## fill()填充方法

将`空数组`填充固定的某一个值。

```h
console.log(new Array(4));
//[ , , , ]
console.log(new Array(4).fill(2));
//[2, 2, 2, 2]
```

## lterator 遍历器

使用最多的遍历器是：`for...in`和`for`
前者是循环遍历`Object`对象，后者是遍历数组
在`ES6`里又新增加了2种数据结构：`Set`和`Map`。这里就用到了`lterator`，他其实就是链表结构，每一个对象记录它下个对象的位置，一个一个连成串。他们二者的原理是利用`for....of`循环。

```h
let myMap = new Map();
myMap.set(0,'hello');
myMap.set(1,'world');
myMap.set('hi','jack');

for(let v of myMap){
 console.log(v);
 console.log(v[0]+'--'+v[1]);
}

/*
    [0, "hello"]
    0--hello
    [1, "world"]
    1--world
    ["hi", "jack"]
    hi--jack
*/

let mySet = new Set([1,3,5]);

for(let v of mySet){
 console.log(v);
}
/*
    1
    3
    5
*/

```

## Module 

`Module`它可以让JS变成一个个可以拆分的先文件，然后通过简单的方拼接起来，极大的增大了灵活性。它的一些关键字是默认的，可以忽略。

这里需要注意：
`Node`环境下使用`export`和`require`
`前端JS`环境下使用`export`和`import`(需要babel等工具来解析)

```h
<!-- foo.js -->

//导出方法
module.exports.sayHi = function(){
 console.log('hello,world');
};


//导出对象
module.exports.obj = {'a':1,'b':2};

class Person {
 constructor( name){
  this.name = name;
 }
 eat() {
  console.log(this.name+' eat');
 }
}

//导出一个类
module.exports.Person = Person;

```

```h
<!-- text.js -->

//引入模块
var text = require('./foo.js');
//使用模块
text.sayHi();
console.log(text.obj.a);

var p = new text.Person('jack');
p.eat();

```

在se6的语法下，需要babel 的支持。
1、安装

`npm install   babel-preset-es2015`
`npminstall --global  babel-cli`

2、配置.babelrc文件

`{"presets":["es2015"]}`

3、测试一下


```h
<!-- export.js -->

var yourname = 'Jack';
var age = 18;
export {yourname, age};
```

```h
<!-- import.js -->

import {yourname, age} from './export.js';
function say() {
 console.log(yourname,age);
}
```

4、运行

`babel export.js --out-file export_out.js`
`babel import.js --out-file import_out.js`

运行结束之后，目录下增加了2个输出文件，`export_out.js`和`import_out.js`

```h
<!--  export_out.js-->

'use strict';

Object.defineProperty(exports, "__esModule", {
value: true
});
var yourname = 'Jack';
var age = 18;

exports.yourname = yourname;
exports.age = age;
```

```h
<!-- import_out.js -->

'use strict';

var _export = require('./export.js');

function say() {
 console.log(_export.yourname, _export.age);
}
```
其实翻译过来后，仍旧是`exports`和`require`！

5、在前端测试text.html

```h
<!DOCTYPE html>
<html>
<head lang="en">
<meta charset="UTF-8">
<title>测试一下</title>
<script src="./import_out.js"></script>

</head>
<body>
<div></div>　　　
</body>
</html>
<script>
    say();
</script>
```
这样是运行不了的，他们环境都不一样，怎么可能运行。
`babel`只是一个编译工具，这里只是把es6编译为`common.js`规范的语法，`require`这个关键字属于commomJS范畴，并不是前端原生的JS,也就无法识别。
> common、cmd、amd:
> `cmd`是seajs遵循的规范
> `amd`是requirejs遵循的规范
> `commomJS`是node遵循的规范

就需要加入`webpack`.

## webpack

1、安装
`npm install webpack -g (全局安装)`
`npm install webpack (本地安装)`

2、运行编译
`webpack import_out.js bundle.js`

因为我们已经对import_out.js使用babel编译过了，所以只需要使用Webpack把commonJS规范这个文件，编译成浏览器识别的文件。

3、 再一次测试test.html
> 会有错误抛出：say is not defined

这个其实是模块化的问题，`say()`方法的存活空间只存在模块内，也就是Webpack解析后的自执行函数里，这样的好处就是避免了全局变量的污染，在bundle.js文件下有
` (function(modules) { ....... })();  ` 这就是一个自执行的封闭空间。
如果需要在外部调用say()这个方法，只需要把它暴露在全局就好了。
webpack有个`expose-loader`这个插件，可以将jquery的$暴露到全局，以此类推，把say()方法挂到window下面就ok了。我们直接修改babel翻译过的文件import_out.js:
```h
'use strict';

var _export = require('./export.js');

function say() {
console.log(_export.yourname, _export.age);
}
<!-- 把say()方法挂到window下面 -->
window.say = say;

```

然后再运行一下webpack命令：
`webpack import_out.js bundle.js`
好啦，你再运行下test.html，发现终于成功啦！

# 总结
其实webpack集成了babel翻译的相关工作，我们并不需要这么麻烦拆分出来。不过为了搞清楚他们各自的工作原理，还是需要这样的拆分。

> 参考原文：[ES6攻略](https://mp.weixin.qq.com/mp/homepage?__biz=MzIzMTU4MzMzOQ==&hid=13&sn=7b07c59cafcf19e4805a803bf9f34347&scene=1&devicetype=android-26&version=26060637&lang=zh_CN&nettype=WIFI&ascene=7&session_us=gh_be748ba8c36a&wx_header=1)









