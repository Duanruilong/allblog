---
title: ES6攻略(三)
date: 2018-05-28 23:54:45
tags: [编程,Javascript,ES6]
description: 
copyright: false
categories: ES6
top:
---
ES6的改版，主要是通过引入JAVA等静态语言的优秀思想来解决老版本的一些痼疾，如作用域，回调，继承和封装等问题。这些改革措施是非常成功的，ES6让JS真正变成了一种好用的语言。这里是关于es7的async/await。

![ES6攻略](https://coding.net/u/DRuilong/p/phone_drl/git/raw/master/image/es6_d.png)

<!-- more -->

# ES6

`es6`的`promise`写法还是不够优雅，因为后面会跟很多`then()`。应该说，`async/await`是目前解决回调问题最好的方案了。


> a:`async `函数是什么？
> b:一句话，它就是 `Generator` 函数的语法糖。
> a:而`Generator`又是神马？
> b:是`es6`的一个语法。
> a:这个语法干嘛的？
> b:不用管它，只需要把async搞明白。

要在node7下面才能玩es7，低版本是玩不了的哈。目前官网最新测试版本是`10.*`，稳定版出到`8.*`，所以安装好就可以使用了。

使用`async/await`的目的就是去除掉`Promise`后面的`then()`,在并行下也没有`then()`的，主要是串行，就是互相依靠的请求方式。
- 假设有三个请求：
  请求1：拿到userid，传递给请求2。
  请求2：根据userid，查询ssoid，传递给请求3。
  请求3：根据ssoid，查到最终结果。

那么，如果可以这么写就好了：

```h
var userid = getRequest1();
var ssoid = getRequest2(userid);
var res = getRequest3(userid,ssoid);
//打印最终结果
res.then(function(data){console.log(data);});

```
把之前es6的写法改一下：

```h
var request = require('request');
var urls = [
'http://localhost:3000/hello1',
'http://localhost:3000/hello2',
'http://localhost:3000/hello3'
];
//创建任务函数不变
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
//声明async函数，返回的一定是promise对象
async function doIt(){
 var data_userid = await createPromise(urls[0]);//第一个请求任务
 data_userid = JSON.parse(data_userid);//解析成对象
 var userid = data_userid.userid;//获取userid
 var data_ssoid = await createPromise(urls[1]+"?userid="+userid);//第二个请求任务
 data_ssoid = JSON.parse(data_ssoid);//解析成对象
 var ssoid = data_ssoid.ssoid;//获取ssoid
 var result = await createPromise(urls[2]+'?userid='+userid+'&ssoid='+ssoid);//第三个请求任务
 return result;//返回promise对象
}
//最终执行
doIt().then(function(data){
//打印最终结果
console.log(data);
}).catch(function(err){
//捕获异常
console.log(err);
});
```
![结果：](http://mmbiz.qpic.cn/mmbiz_gif/amhuPdMsm1l8pHWF6CaJsuviaIjujRicK8ttrnjYQERA7C47wkJUtibIickCS8UwynqvOjETy7Yyz97syziaPcqDjXQ/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1)

> 注意：
``` h
1.async需要放在函数声明前，表示这个函数返回的一定是promise对象。
2.await后面也必须是promise对象，表示这个任务是需要等待的。
3.最后用then()获取所有接口的信息，catch()捕获所有异常。
4.这种写法虽然类似同步，但实际是promise的简化写法，不可混为一谈！

这样，简单整洁，只留一个then和catch。
```
