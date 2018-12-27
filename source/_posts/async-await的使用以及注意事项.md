---
title: "async\await的使用以及注意事项"
date: 2018-12-25 10:13:24
tags: [Javascript,编程,ES6]
description: 
copyright: false
categories: 编程
top:
---
`async` / `await`, 搭配 `promise`, 可以通过编写形似同步的代码来处理异步流程, 提高代码的简洁性和可读性. 这里介绍 `async` / `await`的基本用法和一些注意事项.

![Javascript](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/async_await.png)

<!--more-->

# async

使用 `async function` 可以定义一个异步函数,其语法为:
```h
async function name([param[, param[, ... param]]]) { statements }
```

`async` 函数的返回值很特殊: 不管在函数体内 return 了什么值, async 函数的实际返回值总是一个 Promise 对象. 详细讲就是:若在 async 函数中 return 了一个值 a, 不管 a 值是什么类型, async 函数的实际返回值总是 `Promise.resolve(a)`.

那么 `Promise.resolve(a)` 最终返回一个什么样的promise呢? 看一下MDN的介绍:

> Promise.resolve(value)方法返回一个以给定值解析后的Promise对象。但如果这个值是个thenable（即带有then方法），返回的promise会“跟随”这个thenable的对象，采用它的最终状态（指resolved/rejected/pending/settled）；否则以该值为成功状态返回promise对象。

来看一段代码有这样一个场景: 需要先通过 请求a 拿到 数据a, 然后通过通过 请求b 并携带发送 数据a 获取到 数据b, 拿到 数据b 之后再展示到页面上.

方案一:
```h
async function getData() {
    // 假装请求数据a
    var data_a = await new Promise((resolve) => {
        setTimeout(() => {
            resolve('data_a');
        }, 1000);
    });

    // 假装请求数据b且此请求依赖数据a
    var data_b = await new Promise((resolve) => {
        setTimeout(() => {
            resolve('data_b');
        }, 1000);
    });

    // 展示数据2
    console.log(data_b);
}

getData();
```

上面依次获取到 数据a 和 数据b, 然后再展示 数据b.

既然 `async`函数总是返回一个`promise`, 那么也可以在一个 async 函数返回获取 数据b 的`promise`, 调用函数后再使用 `then` 方法拿到数据, 代码如下:

```h
async function getData() {
    // 假装请求数据a
    var data_a = await new Promise((resolve) => {
        setTimeout(() => {
            resolve('data_a_value');
        }, 1000);
    });

    // 假装请求数据b且此请求依赖数据a
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log(data_a);  // ===>data_a_value
            resolve('data_b_value'); 
        }, 1000);
    });
}
getData().then((value) => {
      console.log(value);  // ===>data_b_value
  });

// 结果返回顺序
data_a_value
data_b_value

```

# await

引用MDN的介绍:

> await 操作符用于等待一个 Promise 对象, 它只能在异步函数 async function 内部使用.

await 的使用语法非常简单:

```h
[return_value] = await expression;
```
其中 `expression` 是一个 `Promise` 对象或者任何要等待的值;

而 `await expression` 的执行结果有以下几种情况:

- 若 `expression` 是一个 `Promise` 对象, 并且其以值 x 被 `fulfilled`, 则返回值为 x.

- 若 `expression` 是一个 `Promise` 对象, 并且其以异常 e 被 `rejected`, 则抛出异常 e.

- 若 `expression` 不是 `Promise` 对象, 则会将 expression 处理成一个以 expression 值被 fulfilled 的 Promise 对象, 然后返回这个 Promise 对象的最终值 (即 expression 值). 这种用法没太大意义, 因此实际使用时还是尽量在 await 后跟一个 Promise 对象.

另外需要注意的是, `await` 在等待 `Promise` 对象时会导致 `async function` 暂停执行, 一直到 `Promise` 对象决议之后才会 `async function` 继续执行.

通过一段代码来看一下:

```h
async function foo() {
    var a = await new Promise((resolve) => {
        setTimeout(() => {
            resolve(1);
        }, 2000);
    });
    console.log(a); // 第2秒时输出: 1

    try {
        var b = await new Promise((resolve, reject) => {
            setTimeout(() => {
                reject(2);
            }, 1000);
        })
    } catch (e) {
        console.log(e); // 第3秒时输出: 2
    }

    // 函数暂停2秒后再继续执行
    var sleep = await new Promise((resolve) => {
        setTimeout(() => {
            console.log('sleep_value');
            resolve('sleep');
        }, 2000);
    });

    var c = await 3;
    console.log(c); // 第5秒时输出: 3
}

foo();

// 结果返回顺序

1
2
sleep_value
3

```