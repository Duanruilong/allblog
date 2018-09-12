---
title: ES6里 三种异步解决方案
date: 2018-06-27 23:47:45
tags: [编程,Javascript,ES6]
description: 
copyright: false
categories: ES6
top:
---

介绍一下ES6里的三种异步方案。

![ES6攻略](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/es6_d.png)

<!-- more -->

# 知识尝鲜

> 1、newGenerator 函数，它执行之后会返回一个迭代器，在这个迭代器上我们可以去调用`next()`  方法；
> 2、next() 方法，可以给他传入一个参数。调用`next()`方法，会返回一个对象`{value: res,done: false}` ,这里的done 表示迭代器。

# 方法一： Generator + Promise 

```h

const fund = require('fund')

// Promise 版的readFile
const readFile = function (fileName) {
  return new Promise(function(resolve, reject) {
    fund.readFile(fileName, function(err, data){
      if (err) return reject(error);
      resolve(data);
    })
  })
}

const newgen = function * () {
  let demo1 = yield readFile('a.txt');
  let demo2 = yield readFile('b.txt');

  console.log('demo1--->', demo1.toString());
  console.log('demo2--->', demo2.toString());
}


// 基于 Generator 和 Promise 的自动执行器
function run(gen) {

  let g = gen();
  
  function next(data) {
    
    let result = g.next(data);

    if (result.done) return result.value;

    result.value.then(function(data) {
      next(data);
    });
  }
  next();
}

run(gen);

```

执行器 中的 `result.value` 现在是一个Promise, 通过 then 方法拿到需要的结果，传下一次 next 方法，这样  ` let f1 = yield readFile('a.txt');  `就可以拿到值.

# 方法二： Generator + Thunk函数

```h
const fs = require('fs')

// 把一个单一执行的函数 ，变成需要再次调用的函数，固定一部分参数
function thunkify(fn, obj = {}) {
    return function () {
        let args = Array.from(arguments);
        return function (m) {
            args.push(m)
            return fn.apply(obj, args)
        }
    }
}

const readFile = thunkify(fs.readFile, fs);

const gen = function* () {
    let f1 = yield readFile('a.txt');
    let f2 = yield readFile('b.txt');

    console.log('F1-->', f1.toString());
    console.log('F2-->', f2.toString());
}


// 基于 Generator 和 Thunk函数的自动执行器
function run(fn) {
    let gen = fn();

    function next(err, data) {
        let result = gen.next(data);
        if (result.done) return 1;
        result.value(next);
    }

    next();

}

run(gen);
```
`Thunk `转换器，把原来的 fs.readFile 函数 转换成需要两次调用的函数 ，readFile 的执行结果，可以通过回调函数能参数传递出来，再传给 next 方法

# 方法三：async 函数 + await 的异步处理方式 

```h

const fs = require('fs')

// Promise 版的readFile
const readFile = function (fileName) {
  return new Promise(function(resolve, reject) {
    fs.readFile(fileName, function(err, data){
      if (err) return reject(err);
      resolve(data);
    })
  })
}

const asyncReadFile = async function () {
  const f1 = await readFile('a.txt');
  const f2 = await readFile('b.txt');
  console.log(f1.toString());
  console.log(f2.toString());
};

asyncReadFile();

```

`readFile `函数 对比方法一没有大的变化 ，Generator 函数变成 了 async 函数，可见这处方式 只是一个语法糖，async 函数自带了执行器。