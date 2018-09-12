---
title: ES6攻略(一)
date: 2018-05-27 21:33:27
tags: [编程,Javascript,ES6]
description: 
copyright: false
categories: ES6
top:
---
ES6的改版，主要是通过引入JAVA等静态语言的优秀思想来解决老版本的一些痼疾，如作用域，回调，继承和封装等问题。这些改革措施是非常成功的，ES6让JS真正变成了一种好用的语言。这里是es6的Q模块。

![ES6攻略](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/es6_d.png)

<!-- more -->

# ES6
两个很有用的原生JS函数：`map`和`reduce`。

- 1、map() 方法：

它返回一个由原数组中的每个元素调用一个指定方法之后的返回值组成新的数组。

```h
var data = [1,2,3];
var callback = function(a){
    // 这里的a就是数组每一项的值
    return a+1;
}

var demo = data.map(callback);
consloe.log(demo)
//[2,3,4]
```
在这里需要注意的是`map`里面的回调函数，参数只有一个

- 2、reduce()方法

这个方法先从代码例子开始
```h
var data = [1,2,3];
var callback = function(a,b){
    console.log('初始值=',a);
    return a+b;
};
var demo = data.reduce(callback,100);
console.log(demo);
//初始值=100
//初始值=101
//初始值=103
//106
```
reduce它有2个参数：
- 第一个是`callback`回调函数，主要是针对数组的操作；
- 第二个是传入的`初始值`

工作原理：
- 1、传入一个初始值；
- 2、第一次传入的结果（初始值）作为回调函数的第一个参数，数组的第一个值则作为第二个参数传入，执行完回调函数之后，返回一个结果；
- 3、再把第二步的结果作为这一次第一个参数的入参，数组的第二个数值作为第二个参数传入，执行回调函数，返回一个结果；
- .....

依次类推，遍历完数组则停止，也就是一层一层的回调，最终得到结果。
这和`ajax`的串行请求很类似，就是请求1，请求2，请求3等，是按照顺序排队执行，他们彼此有所依赖；同时还有一个`并行`，就是请求1，请求2，请求3等同时执行请求，他们彼此互不影响。并行请求时间取最大者
`Q模块`是用来解决回调问题，与之类似的有`jQuery`的延迟对象，ES6的`promise`对象，ES7的`async`和`await`.
可以把Q理解为node版本的延迟对象，那么什么情况下要在Node下发送请求，其实爬虫程序就是这样的逻辑。
> 爬虫的工作原理：

先请求首页的连接，然后在首页的基础下对各个超链接不断的爬取，直到遍历完所有的页面。

先安装一个发送请求模块`request`和Q模块：
> `npm install request --dev`
> `npm install q --dev`

然后开始写个脚本，先用`express`搭建一个服务：
```h
var express = require('express')
var router = express.Router();

// GET home page
router.get('./',function(req,res,next){
res.render('index',{title:'Express'});
})

// 请求1
router.get('/list1',function(req,res,next){
    setTimeout(() => {
        res.send('请求1完成了！')
    }, 1000);
})
// 请求2
router.get('/list2',function(req,res,next){
    setTimeout(() => {
        res.send('请求2完成了！')
    }, 2000);
})
// 请求3
router.get('/list3',function(req,res,next){
    setTimeout(() => {
        res.send('请求3完成了！')
    }, 3000);
})

module.exports = router;

```
以上为数据接口。

```h
var Q = require('q');
var request = require('request');

function createPromise(url){
    var deferred = Q.defer(); // 创建任务

    request(url, function(err,response,body){
        console.log('requested',url);
        if (err) {
            deferred.reject(err); // 错误返回
        }else{
            deferred.resolve(body) // 成功返回
        }
    })
    return deferred.promise; // 返回一个承诺
}

createPromise('http://localhost:3000/list1').then(function(data){
    console.log(data);
},function(err){
    console.error(err);
})
```
> `resolve`就是继续执行，`reject`就是拒绝，`defer`就是布置任务。
> Q把每一个请求都会布置成一个任务，然后返回一个`promise`对象，`promise`就是继承的意思，也就是这个任务就是一个承诺，承诺任务成功之后返回成功的结果，而失败就返回一个失败的结果，然后就可以从`then()`方法里面拿到需要的数据。

并行请求：
```h
var Q = require('q');
var request = require('request');
var urls = [
    'http://localhost:3000/hello1',
    'http://localhost:3000/hello2',
    'http://localhost:3000/hello3',
    'http//localhost:3000/hello_110' //这是个错误的地址
];

function createPromise(url) {
    var deferred = Q.defer(); //创建任务
    request(url, function (err, response, body) {
        console.log("requested " + url);
        if (err) {
            deferred.reject(err); //错误返回
        } else {
            deferred.resolve(body); //成功返回
        }

    });
    return deferred.promise; //返回一个承诺
}

var promises = urls.map(function (url) {
    return createPromise(url);
});


Q.allSettled(promises).then(function (results) {
    console.log(results);
    results.forEach(function (result) {
        if (result.state === "fulfilled") {
            console.log(result.value);
        } else {
            console.error(result.reason);
        }
    });
});
```
用`map()`方法，在回调函数中批量创建任务，然后等待结果都执行完，一次性的拿到所有结果数据（以数组的形式返回）

串行请求。先来个传统写法:
```h
var Q = require('q');
var request = require('request');

request('http://localhost:3000/hello1' , function(err1 , response1 , body1){
 console.log(body1);
 request('http://localhost:3000/hello2',function(err2 , response2 , body2){
    console.log(body2);
    request('http://localhost:3000/hello3',function(err3 , response3 , body3){
      console.log(body3);

   });
  });

});
```
这种写法也叫回调地狱，实在太难维护了，代码一层摞一层，如果要改个东西那简直是要了命了，所以地狱二字用的灰常贴切。
Q是如何解决回调地狱问题的呢？
其实也简单，就是把发请求部分单独拆出去，做成子任务即可。
前面的reduce就是这样，在有一些场景下（比如微信api），我们可以结合这个函数写出漂亮的代码：

```h
var Q = require('q');
var request = require('request');

var urls = [
    'http://localhost:3000/hello1',
    'http://localhost:3000/hello2',
    'http://localhost:3000/hello3',
    'http//localhost:3000/hello_110' //瞪大眼睛瞅好咯，这是个错误的地址
];

function createPromise(url) {
    var deferred = Q.defer();
    request(url, function (err, response, body) {
        if (err)
            deferred.reject(err);
        else
            deferred.resolve(body);
    });
    return deferred.promise;
}

//这里是亮点~~
urls.reduce(function (promiseObj, url) {
    //promiseObj第一次是Q(null)，也就是空的任务
    return promiseObj.then(function (data) {
        if (data) {
            console.log(data);
        }
        //返回promise，层级传递，直到最后一个拿到结果
        return createPromise(url);
    }, function (err) {
        console.error(err);
        return createPromise(url);
    })
}, Q(null));
```
> 比较难理解的还是reduce。我们这里再来分析一遍他：
> 第1步：给定一个初始值Q(null)。
> 第2步：把上次的结果Q(null)作为回调的第一个参数，数组的第1个url作为第二个参数，执行回调函数，拿到上次的结果，然后返回一个promise对象（因为我们这里拿到的是null，所以在if条件判断时没有打印出来）。
> 第3步：把第2步的结果`Q('http://localhost:3000/hello1')`作为回调的第一个参数，数组的第2个url作为第二个参数，执行回调函数，这时打印的data就是hello1的结果，然后再次返回一个promise对象。以此类推，最终走到最后一个url，拿完所有的数据。

现实中我们需求一般是这样的：
> 请求1：拿到userid，传递给请求2。
> 请求2：根据userid，查询ssoid，传递给请求3。
> 请求3：根据ssoid，查到最终结果。
我们根据这种情况，再来一次：
改造下接口：
```h
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function (req, res, next) {
    res.render('index', {
        title: 'Express'
    });
});
//请求1
router.get('/hello1', function (req, res, next) {
    setTimeout(function () {
        res.json({
            code: 200,
            userid: 123
        });
    }, 1000);
});
//请求2
router.get('/hello2', function (req, res, next) {
    var param = req.query || req.params;
    var userid = param.userid;
    setTimeout(function () {
        res.json({
            code: 200,
            userid: userid,
            ssoid: 456
        });
    }, 2000);
});
//请求3
router.get('/hello3', function (req, res, next) {
    var param = req.query || req.params;
    var userid = param.userid;
    var ssoid = param.ssoid;
    setTimeout(function () {
        res.json({
            code: 200,
            userid: userid,
            ssoid: ssoid,
            msg: '完成啦！'
        });
    }, 3000);
});
module.exports = router;
```
其次：
```h
var Q = require('q');
var request = require('request');

var urls = [
    'http://localhost:3000/hello1',
    'http://localhost:3000/hello2',
    'http://localhost:3000/hello3'
];

function createPromise(url) {
    var deferred = Q.defer();
    request(url, function (err, response, body) {
        if (err)
            deferred.reject(err);
        else
            deferred.resolve(body);
    });
    return deferred.promise;
}
//第一个请求的回调
var callback_Request1 = function (data) {
    data = JSON.parse(data);
    console.log(data);
    var userid = data.userid;
    var url = urls[1] + '?userid=' + userid;
    return createPromise(url);
};
//第二个请求的回调
var callback_Request2 = function (data) {
    data = JSON.parse(data);
    console.log(data);
    var userid = data.userid;
    var ssoid = data.ssoid;
    var url = urls[2] + '?userid=' + userid + '&ssoid=' + ssoid;
    return createPromise(url);
};
//业务逻辑被剥离出去咯~~
createPromise(urls[0]).then(function (data) {
    console.log('第一次请求');
    return callback_Request1(data);
}).then(function (data) {
    console.log('第二次请求');
    return callback_Request2(data);
}).then(function (data) {
    //打印最终结果
    console.log('第三次请求');
    console.log(data);
});
```
就可以把再怎么复杂的逻辑都可以单独封装到一个回调函数里面，整体代码干干净净。