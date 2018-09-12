---
title: ES6攻略(二)
date: 2018-05-28 23:36:57
tags: [编程,Javascript,ES6]
description: 
copyright: false
categories: ES6
top:
---
ES6的改版，主要是通过引入JAVA等静态语言的优秀思想来解决老版本的一些痼疾，如作用域，回调，继承和封装等问题。这些改革措施是非常成功的，ES6让JS真正变成了一种好用的语言。这里是关于promise对象。

![ES6攻略](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/es6_d.png)

<!-- more -->

# ES6
`回调`使我们时常需要面对的问题，现在`node`已经很全面的支持了es6和es7,在Node环境下就可以直接运行调试，不再依赖于`babel`去编译。

关于es6的pormise他就是一个对象，看下面：

```h
<!-- 对比一下他们的差异 -->


//布置一项任务
var promise = new Promise(function(resolve, reject) {
// ... some code
//resolve和reject是对象提供的两个回调函数
 if(/* 异步操作成功 */){
   resolve(value);
  } 
  else{
   reject(error);
  }
});
//执行任务
promise.then(function(data) {
// success
 console.log(data);
}).catch(function(err){
 console.log(err);
});
```
从中可以看出，他和`Q`模块有点类似，比如：
`var deferred = Q.defer()`

```h
var request = require('request');
//回调函数
var callback = function(resolve, reject){
 var url = 'http://localhost:3000/hello1';
 request(url , function(err , response , body){
  if(err){
    reject(err);//错误返回
  }else{
    resolve(body);//成功返回
  }
 });
};
//布置一项任务
var promise = new Promise(callback);
//执行任务
promise.then(function(data){
//正确响应
 console.log(data);
}).catch(function(err){
//捕获错误信息
 console.log('error info:',err);
});
```
![结果](http://mmbiz.qpic.cn/mmbiz_gif/amhuPdMsm1l8pHWF6CaJsuviaIjujRicK8VoxJJcJPvue0o3SdaAqnj9pDKzFPH7iaGAf0rWBicGrQaYibm6emNI2lA/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

将url地址http://localhost:3000/hello1,故意改错成：http//localhost:3000/hello1，然后再测试一下：

![结果](http://mmbiz.qpic.cn/mmbiz_gif/amhuPdMsm1l8pHWF6CaJsuviaIjujRicK8VTiamYTBttHLZhTzcWxMErJIzSzwVzJ9DFtfic7TibPX94NZZLjupFFbQ/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

再一次修改一下：

```h

var request = require('request');

var urls = [
'http://localhost:3000/hello1',
'http://localhost:3000/hello2',
'http://localhost:3000/hello3'
];
//这里需要改写一下~~
function createPromise(url){
//回调函数
 var callback = function(resolve, reject){

 request(url , function(err , response , body){

  if(err){
   reject(err);//错误返回
  }else{
   resolve(body);//成功返回
  }

 });
 };
//布置任务
 var promise = new Promise(callback);
//返回promise承诺
 return promise;
}
//第一个请求的回调
var callback_Request1 = function(data){
data = JSON.parse(data);
console.log(data);
var userid = data.userid;
var url = urls[1]+'?userid='+userid;
return createPromise(url);
};
//第二个请求的回调
var callback_Request2 = function(data){
data = JSON.parse(data);
console.log(data);
var userid = data.userid;
var ssoid = data.ssoid;
var url = urls[2]+'?userid='+userid+'&ssoid='+ssoid;
return createPromise(url);
};
//业务逻辑被剥离出去咯~~
createPromise(urls[0]).then(function(data){
console.log('第一次请求');
return callback_Request1(data);
}).then(function(data){
console.log('第二次请求');
return callback_Request2(data);
}).then(function(data){
//打印最终结果
console.log('第三次请求');
console.log(data);
});
```

![结果：](http://mmbiz.qpic.cn/mmbiz_gif/amhuPdMsm1l8pHWF6CaJsuviaIjujRicK8byeTYa8DOqXKaf3fSHzdvRFbsQXqZicYOXn4tLticA8WjejA993FBqTA/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)
