---
title: Async Functions (异步函数)
date: 2018-07-15 21:43:58
tags: [Javascript,编程]
description: 
copyright: false
categories: Javascript
top:
---
 ECMAScript 2017 新特性 Async Functions(异步函数) 。

![js](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/js_d.png)

<!-- more -->

# async(异步) 函数变体

以下是已经存在的异步函数变体。请注意无处不在的  async 关键字。

- 异步函数声明： async function foo() {}
- 异步函数表达式： const foo = async function () {};
- 异步函数定义：let obj = { async foo() {} }
- 异步箭头函数： const foo = async () => {};

# async(异步) 函数总是返回 Promises

async(异步) 函数的 Promise 完成状态：

```h
async function asyncFunc() {
    return 123;
}
 
asyncFunc()
.then(x => console.log(x));
    // 123

```

async(异步) 函数的 Promise 拒绝状态：

```h
async function asyncFunc() {
    throw new Error('Problem!');
}
 
asyncFunc()
.catch(err => console.log(err));
    // Error: Problem!
```

# 通过 await 处理 async(异步) 计算的结果和错误
await（只允许在 async(异步) 函数内部使用）等待其操作对象 Promise 返回：

- 如果 Promise 是完成状态，await 的结果是完成态的值。
- 如果 Promise 是拒绝状态，await 会抛出拒绝值。

处理单个 async(异步) 返回值：
```h
async function asyncFunc() {
    const result = await otherAsyncFunc();
    console.log(result);
}
 
// 等价于:
function asyncFunc() {
    return otherAsyncFunc()
    .then(result => {
        console.log(result);
    });
}
```
按顺序处理多个 async(异步) 返回值：

```h
async function asyncFunc() {
    const result1 = await otherAsyncFunc1();
    console.log(result1);
    const result2 = await otherAsyncFunc2();
    console.log(result2);
}
 
// 等价于:
function asyncFunc() {
    return otherAsyncFunc1()
    .then(result1 => {
        console.log(result1);
        return otherAsyncFunc2();
    })
    .then(result2 => {
        console.log(result2);
    });
}

```

并行处理多个 async(异步) 返回值：

```h
async function asyncFunc() {
    const [result1, result2] = await Promise.all([
        otherAsyncFunc1(),
        otherAsyncFunc2(),
    ]);
    console.log(result1, result2);
}
 
// 等价于:
function asyncFunc() {
    return Promise.all([
        otherAsyncFunc1(),
        otherAsyncFunc2(),
    ])
    .then([result1, result2] => {
        console.log(result1, result2);
    });
}
```

错误处理：

```h
async function asyncFunc() {
    try {
        await otherAsyncFunc();
    } catch (err) {
        console.error(err);
    }
}
 
// 等价于:
function asyncFunc() {
    return otherAsyncFunc()
    .catch(err => {
        console.error(err);
    });
}
```

# 理解 async(异步) 函数

解释 async(异步) 函数之前，我需要解释一下如何组合使用 Promises 和 Generator ，通过看起来同步的代码来执行 async(异步) 操作。

对于能够 async(异步) 计算其一次性结果的函数，作为 ES6 一部分的 Promises 已经变得流行起来。一个例子是 客户端 fetch API ，它是 XMLHttpRequest 获取数据的替代方法。使用示例如下：

```h
function fetchJson(url) {
    return fetch(url)
    .then(request => request.text())
    .then(text => {
        return JSON.parse(text);
    })
    .catch(error => {
        console.log(`ERROR: ${error.stack}`);
    });
}
fetchJson('http://example.com/some_file.json')
.then(obj => console.log(obj));

```
# 通过 generator 来编写异步代码

co 是一个使用 Promise 和 generator 来实现看似同步编码的库，但与上一示例中使用的样式相同：

```h
const fetchJson = co.wrap(function* (url) {
    try {
        let request = yield fetch(url);
        let text = yield request.text();
        return JSON.parse(text);
    }
    catch (error) {
        console.log(`ERROR: ${error.stack}`);
    }
});
```

每次回调函数（ generator 函数）产生一个 Promise 对象给 co ，回调会被暂停，只有当 Promise 执行完成后，co 才会继续执行回调 。 如果 Promise 处于完成状态，yield 返回完成状态的值，如果处于拒绝状态，yield 抛出拒绝状态的错误。此外，co 保证结果是通过回调执行完成才返回的（类似于  then() 所做的工作）。

# 通过 async(异步) 函数来编写异步代码

async(异步) 函数用的特定语法基本上和 co 类似：
```h
async function fetchJson(url) {
    try {
        let request = await fetch(url);
        let text = await request.text();
        return JSON.parse(text);
    }
    catch (error) {
        console.log(`ERROR: ${error.stack}`);
    }
}
```

在内部，异步函数写法更类似于 generators 。

# 以同步开始，异步处理的 async(异步) 函数
以下是 async(异步)函数是如何工作的：

- 1、async(异步) 函数总是返回一个 Promise 对象 p 。Promise 对象在 async(异步) 函数开始执行时被创建。
- 2、函数体执行过程中，可以通过 return 或 throw 终止执行。或者通过 await 暂停执行，在这种情况下，通常会在以后继续执行。
- 3、返回 Promise 对象 p。


当执行 async(异步) 函数的函数体时，return x 中的 x 是 Promise 对象 p 的完成状态的结果，而 throw err 是 p 的拒绝状态的结果。执行结果是异步返回的。换句话说：then() 和  catch() 的回调总是在当前代码完成后执行。

以下是代码示例：

```h

async function asyncFunc() {
    console.log('asyncFunc()'); // (A)
    return 'abc';
}
asyncFunc().
then(x => console.log(`Resolved: ${x}`)); // (B)
console.log('main'); // (C)
 
// Output:
// asyncFunc()
// main
// Resolved: abc
```

您可以认为是以下的执行顺序：

- 1、行A：async(异步) 函数以同步开始。async(异步) 函数的 Promise 通过 return 来返回完成状态的结果。
- 2、行C：执行继续。
- 3、行B：Promise 完成状态通知是异步发生的。

# 返回不被包裹的 Promise 对象
Promise 的 resolve 是一项标准操作。 return 就是使用它来 resolve async(异步) 函数的 Promise  p 的。这意味着：

- 1、返回一个非 Promise 值，该值将被处理成 p 的完成状态值。
- 2、返回一个 Promise 对象，那么 p 此时相当于是该 Promise 的状态。
因此，您可以返回一个 Promise ，并且这个 Promise 不会包裹在别的 Promise 中：
```h
async function asyncFunc() {
    return Promise.resolve(123);
}
asyncFunc()
.then(x => console.log(x)) // 123

```

有趣的是，返回一个拒绝状态（reject）的 Promise 对象会导致 async(异步) 函数被拒绝（reject）（通常，您可以使用 throw ）：

```h
JavaScript 代码:
async function asyncFunc() {
    return Promise.reject(new Error('Problem!'));
}
asyncFunc()
.catch(err => console.error(err)); // Error: Problem!
```

这与 Promise 解决方案的工作方式是一致的。 使你能够在不使用 await 的情况下，使用其他 async(异步) 计算来执行完成和拒绝处理：
```h
JavaScript 代码:
async function asyncFunc() {
    return anotherAsyncFunc();
}
```
上面的代码示例和下面的类似，但是比下面的更高效。（以下代码示例没有包裹  anotherAsyncFunc() 的 Promise ，而是包裹 anotherAsyncFunc() 本身 ）：
```h
JavaScript 代码:
async function asyncFunc() {
    return await anotherAsyncFunc();
}
```

# 使用 await 小贴士

## 不要忘记使用 await
在 async(异步) 函数中容易犯的一个错误就是在调用 async(异步) 函数时忘记使用 await ：
```h
JavaScript 代码:
async function asyncFunc() {
    const value = otherAsyncFunc(); // missing `await`!
    ···
}
```
在这个例子中，方法执行返回的 Promise 对象赋值给了 value ，它通常不是你在 async(异步) 函数中想要的结果。

await 甚至可以在 async(异步) 函数不返回任何值的情况下起作用。它的 Promise 只是用来告诉调用者完成状态。例如：
```h
JavaScript 代码:
async function foo() {
    await step1(); // (A)
    ···
}
```
行A中的 await 确保在执行 foo() 剩余部分之前， step1() 已经执行完成。

## 不需要使用 await 的情况


有时，你只想触发异步计算，并且不需要关注它什么时候完成。以下是代码示例：
```h
JavaScript 代码:
async function asyncFunc() {
    const writer = openFile('someFile.txt');
    writer.write('hello'); // don’t wait
    writer.write('world'); // don’t wait
    await writer.close(); // wait for file to close
}
```
在这里，我们不关心单个的写入操作是否完成，只需要他们以正确的顺序执行 (API必须保证，但这是由 async(异步) 函数的执行模型所鼓励的，正如我们所见)。

asyncFunc() 函数最后一行的 await 确保该函数仅在文件写入关闭后才会执行。

由于返回的 Promises 没有被其他 async(异步) 函数包裹，所以你可以用 return 替换  await writer.close() ：
```h
JavaScript 代码:
async function asyncFunc() {
    const writer = openFile('someFile.txt');
    writer.write('hello');
    writer.write('world');
    return writer.close();
}
```
这两个版本各有利弊，await 版本可能稍微更容易理解。

## await 是顺序执行的，Promise.all() 是并行的

下面的代码调用了两个 async(异步) 函数， asyncFunc1() 和 asyncFunc1() 。
```h
JavaScript 代码:
async function foo() {
    const result1 = await asyncFunc1();
    const result2 = await asyncFunc2();
}
```
这两个函数调用顺序执行。但是并行执行它们往往会加快速度。您可以使用 Promise.all() ：

```h
JavaScript 代码:
async function foo() {
    const [result1, result2] = await Promise.all([
        asyncFunc1(),
        asyncFunc2(),
    ]);
}
```
我们现在正在等待一个包含两个元素的数组的 Promise ，而不是等待两个 Promise。

# 异步函数和回调

async(异步) 函数的一个限制是 await(等待) 只影响直接相关的 async(异步) 函数。因此，async(异步) 函数无法在回调（但是，回调可以是 async(异步) 函数本身，稍后我们将会看到）中使用 await(等待)。这使得基于回调的实用函数和方法难以使用。例子中我们将使用数组方法 map() 和 forEach() 。

# Array.prototype.map()

我们从数组方法 map() 开始讲解。在下面的代码示例中，我们想要加载由 URLs 数组指向的文件，并将它们返回到数组中。
```h
JavaScript 代码:
async function downloadContent(urls) {
    return urls.map(url => {
        // 错误的语法!
        const content = await httpGet(url);
        return content;
    });
}
```
这不起作用，因为在正常箭头函数中 await 语法上是非法的(愚人码头注： await(等待) 只影响直接相关的 async(异步) 函数)。那么如何使用异步的箭头函数呢？
```h
JavaScript 代码:
async function downloadContent(urls) {
    return urls.map(async (url) => { // 注意这一行中的 async ;
        const content = await httpGet(url);
        return content;
    });
}
```
这段代码有两个问题：

现在返回的结果是一个 Promises 对象的数组，而不是一个字符串的数组。
一旦 map() 执行完成，回调执行的工作并不能同时完成，因为 await 只暂停了包裹它的箭头函数 和 httpGet() 异步执行达到完成状态。这意味着你不能使用 await，来等待  downloadContent() 执行结束。
我们可以通过 Promise.all() 来解决这两个问题，Promise.all() 可以将一系列的 Promise 转换为一个 Promise 数组（所有值都是经过 Promise 完成并返回）：
```h
JavaScript 代码:
async function downloadContent(urls) {
    const promiseArray = urls.map(async (url) => {
        const content = await httpGet(url);
        return content;
    });
    return await Promise.all(promiseArray);
}
```
map() 的回调并不对 httpGet() 的结果起作用，只是起到不断执行的作用。因此，这里我们不需要一个异步的箭头函数，只需要一个普通的箭头函数就能达到相同的结果。
```h
JavaScript 代码:
async function downloadContent(urls) {
    const promiseArray = urls.map(
        url => httpGet(url));
    return await Promise.all(promiseArray);
}
```
我们仍然可以做一个小的改进：这个异步函数稍微有点低效 – 首先通过 await 来解开  Promise.all() 的结果，然后通过 return 再次包裹它。 假设 return 不包裹 Promises，我们可以直接返回 Promise.all() 的结果：
```h
JavaScript 代码:
async function downloadContent(urls) {
    const promiseArray = urls.map(
        url => httpGet(url));
    return Promise.all(promiseArray);
}
```
# Array.prototype.forEach()

我们使用数组的 forEach() 方法在控制台中打印几个通过 URLs 加载的文件的内容：
```h
JavaScript 代码:
async function logContent(urls) {
    urls.forEach(url => {
        // Wrong syntax
        const content = await httpGet(url);
        console.log(content);
    });
}
```
同样的，这里的代码会产生一个语法错误，因为你不能在通常的箭头函数内部使用 await 。

我们换作异步箭头函数：
```h
JavaScript 代码:
async function logContent(urls) {
    urls.forEach(async url => {
        const content = await httpGet(url);
        console.log(content);
    });
    // Not finished here
}
```
这段代码起作用了，但是会出现一个警告：httpGet() 返回的 Promise 对象是异步完成的，这也意味着当 forEach() 返回的时候回调可能还没有结束，因此你无法等到 logContent() 只能结束。

如果你并不想要这个结果，你可以将 forEach() 转换为 for-of 循环。
```h
JavaScript 代码:
async function logContent(urls) {
    for (const url of urls) {
        const content = await httpGet(url);
        console.log(content);
    }
}
```
现在一切都在 for-of 循环完成后完成。但是，处理步骤依次发生：httpGet() 只是在第一次调用完成后再次调用。如果您希望处理步骤并行执行，你必须使用 Promise.all()：
```h
JavaScript 代码:
async function logContent(urls) {
    await Promise.all(urls.map(
        async url => {
            const content = await httpGet(url);
            console.log(content);
        }));
}
```
map() 用于创建一个 Promises 数组。 我们对他们的完成结果并不感兴趣，我们只要 await(等待) 所有方法执行完成。这意味着我们希望的是在 async(异步) 函数完成之后所有的执行都已经完成。我们也可以返回 Promise.all() ，但是该函数的结果是一个数组，其元素都是未完成状态的。

# 使用异步函数小贴士

## 了解你的 Promises

async(异步) 函数的基础就是 [Promises](http://exploringjs.com/es6/ch_promises.html) 对象，这就是为什么理解 Promises 对于理解 async(异步) 函数至关重要。特别是当遇到不是基于 Promises 的老代码来实现 async(异步) 函数时，你通常别无选择，只能用 Promise 来重构。

举个例子，这里有个 “promisified” 版本的 XMLHttpRequest ：
```h
JavaScript 代码:
function httpGet(url, responseType="") {
    return new Promise(
        function (resolve, reject) {
            const request = new XMLHttpRequest();
            request.onload = function () {
                if (this.status === 200) {
                    // Success
                    resolve(this.response);
                } else {
                    // Something went wrong (404 etc.)
                    reject(new Error(this.statusText));
                }
            };
            request.onerror = function () {
                reject(new Error(
                    'XMLHttpRequest Error: '+this.statusText));
            };
            request.open('GET', url);
            xhr.responseType = responseType;
            request.send();
        });
}
```
XMLHttpRequest 的 API 是基于回调的。通过一个 async(异步) 函数来实现它，意味着你必须在回调中返回 Promise 的完成(fulfill) 或拒绝(reject) 状态。这是不可能的，因为你只能通过 return 或者  throw 来完成这样的操作。你不能从回调函数内部 return 一个函数的结果。throw也有类似的约束。

因此，异步函数的通用编码风格是：

- 直接使用 Promise 对象来构建异步原语。
- 用异步函数来使用这些原语。
扩展阅读：“Exploring ES6” 中的 “[异步编程中的 Promises 对象](http://exploringjs.com/es6/ch_promises.html)” 章节

# 立即调用异步函数表达式

有时，如果你可以在模块或脚本的顶层使用 await ，那将是一种很好的选择。当然，它只能在异步函数中使用。您可以创建一个异步函数 main()  并立即调用它：
```h
JavaScript 代码:
async function main() {
    console.log(await asyncFunction());
}
```
main();
或者您可以使用立即调用异步函数表达式：
```h
JavaScript 代码:
(async function () {
    console.log(await asyncFunction());
})();
```
另一个选择是立即调用异步箭头函数：
```h
JavaScript 代码:
(async () => {
    console.log(await asyncFunction());
})();
```
# 用异步函数进行单元测试

以下代码使用 [测试框架 mocha](https://mochajs.org/) 对异步函数 asyncFun1() 和 asyncFun2() 来进行单元测试：

```h
import assert from 'assert';
 
// Bug: the following test always succeeds
test('Testing async code', function () {
    asyncFunc1() // (A)
    .then(result1 => {
        assert.strictEqual(result1, 'a'); // (B)
        return asyncFunc2();
    })
    .then(result2 => {
        assert.strictEqual(result2, 'b'); // (C)
    });
});
```
然而，这个测试总是成功的，因为 mocha 不会等待 B 行和 C 行断言执行完成。

你可以通过返回链式调用的 Promise 来解决这个问题，因为 mocha 会识别一个测试是否返回一个 Promise ，然后等待该 Promise 完成 再进行下一步（除非超时）。
```h
JavaScript 代码:
return asyncFunc1() // (A)

```
异步函数总是返回 Promises ，这使得它们能方便的、完美的来进行这种单元测试：
```h
JavaScript 代码:
import assert from 'assert';
test('Testing async code', async function () {
    const result1 = await asyncFunc1();
    assert.strictEqual(result1, 'a');
    const result2 = await asyncFunc2();
    assert.strictEqual(result2, 'b');
});
```
在 mocha 中使用异步单元测试异步函数有两个优点：代码更简洁，能够准确处理返回的 Promise 对象。

# 不要担心没有处理的拒绝拒态


当前的 JavaScript 引擎可以在拒绝态未处理的情况下提出警告。以下代码在过去会经常执行失败，但是当前的 JavaScript 引擎可以进行警告：
```h
JavaScript 代码:
async function foo() {
    throw new Error('Problem!');
}
foo();
```

# 阅读延伸

- [异步函数](https://github.com/tc39/ecmascript-asyncawait) （提出者Brian Terlson）
- [通过generators来简化异步计算](http://exploringjs.com/es6/ch_generators.html#sec_co-library)（“Exporing ES6”中的部分章节）
原文地址：[http://exploringjs.com/es2016-es2017/ch_async-functions.html](http://exploringjs.com/es2016-es2017/ch_async-functions.html)


