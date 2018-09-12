---
title: 怎样在JS里尽量少写for循环
date: 2018-07-29 23:39:15
tags: [Javascript,编程]
description: 
copyright: false
categories: Javascript
top:
---
 
"很多场景，for来的更直接，更明了。"  "最喜欢for循环了。想break就break。可能会多数据的还是for循环。" "for循环灵活易用，高阶函数简洁明了，合适的用合适方式，还是不能限制的太死吧，不然思维也会受影响",这是很多时候都会考虑的问题。

![js](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/js_d.png)

<!-- more -->

# 用好 filter，map，和其它 ES6 新增的高阶遍历函数

问题一： 将数组中的空值去除
```h

const arrContainsEmptyVal = [3, 4, 5, 2, 3, undefined, null, 0, ""];

答案：

const compact = arr => arr.filter(Boolean);

```
问题二： 将数组中的 VIP 用户余额加 10
```h

const users = [
  { username: "Kelly", isVIP: true, balance: 20 },
  { username: "Tom", isVIP: false, balance: 19 },
  { username: "Stephanie", isVIP: true, balance: 30 }
];


答案：

users.map(
  user => (user.isVIP ? { ...user, balance: user.balance + 10 } : user)
);


补充：有人说很明显这里该用 forEach，你为了举例而举例！我比较无语。forEach 是用来执行副作用的好吗？你把原数据都改了。我的习惯是使用 Immutable 数据。你要改数据的话，开心就好。

```
问题三： 判断字符串中是否含有元音字母
```h

const randomStr = "hdjrwqpi";

答案：

const isVowel = char => ["a", "e", "o", "i", "u"].includes(char);
const containsVowel = str => [...str].some(isVowel);

containsVowel(randomStr);


```
问题四： 判断用户是否全部是成年人
```h
const users = [
  { name: "Jim", age: 23 },
  { name: "Lily", age: 17 },
  { name: "Will", age: 25 }
];

答案：

users.every(user => user.age >= 18);


```
问题五： 找出上面用户中的未成年人
```h

const users = [
  { name: "Jim", age: 23 },
  { name: "Lily", age: 17 },
  { name: "Will", age: 25 }
];
答案：

const findTeen = users => users.find(user => user.age < 18);

findTeen(users);


```
问题六： 将数组中重复项清除
```h

const dupArr = [1, 2, 3, 3, 3, 3, 6, 7];

答案：

const uniq = arr => [...new Set(arr)];

uniq(dupArr);

```
问题七： 生成由随机整数组成的数组，数组长度和元素大小可自定义
```h
答案：
const genNumArr = (length, limit) =>
  Array.from({ length }, _ => Math.floor(Math.random() * limit));

genNumArr(10, 100);

```
# 理解和熟练使用 reduce

问题一： 不借助原生高阶函数，定义 reduce
```h
答案：
const reduce = (f, acc, arr) => {
  if (arr.length === 0) return acc;
  const [head, ...tail] = arr;
  return reduce(f, f(head, acc), tail);
};

```
问题二： 将多层数组转换成一层数组
```h

const nestedArr = [1, 2, [3, 4, [5, 6]]];

答案：

const flatten = arr =>
  arr.reduce(
    (flat, next) => flat.concat(Array.isArray(next) ? flatten(next) : next),
    []
  );

```
问题三：将下面数组转成对象，key/value 对应里层数组的两个值
```h

const objLikeArr = [["name", "Jim"], ["age", 18], ["single", true]];

答案：

const fromPairs = pairs =>
  pairs.reduce((res, pair) => ((res[pair[0]] = pair[1]), res), {});

fromPairs(objLikeArr);

```
问题四： 取出对象中的深层属性
```h

const deepAttr = { a: { b: { c: 15 } } };

答案：

const pluckDeep = path => obj =>
  path.split(".").reduce((val, attr) => val[attr], obj);

pluckDeep("a.b.c")(deepAttr);

```
问题五：将用户中的男性和女性分别放到不同的数组里：
```h

const users = [
  { name: "Adam", age: 30, sex: "male" },
  { name: "Helen", age: 27, sex: "female" },
  { name: "Amy", age: 25, sex: "female" },
  { name: "Anthony", age: 23, sex: "male" },
];

答案：

const partition = (arr, isValid) =>
  arr.reduce(
    ([pass, fail], elem) =>
      isValid(elem) ? [[...pass, elem], fail] : [pass, [...fail, elem]],
    [[], []],
  );
  
const isMale = person => person.sex === "male";

const [maleUser, femaleUser] = partition(users, isMale);

```
问题六： reduce 的计算过程，在范畴论里面叫 catamorphism，即一种连接的变形。和它相反的变形叫 anamorphism。现在我们定义一个和 reduce 计算过程相反的函数 unfold（注：reduce 在 Haskell 里面叫 fold，对应 unfold）

```h

const unfold = (f, seed) => {
  const go = (f, seed, acc) => {
    const res = f(seed);
    return res ? go(f, res[1], acc.concat(res[0])) : acc;
  };
  return go(f, seed, []);
};
根据这个 unfold 函数，定义一个 Python 里面的 range 函数。
答案：

const range = (min, max, step = 1) =>
  unfold(x => x < max && [x, x + step], min);

```
# 用递归代替循环（可以break！）


问题一： 将两个数组每个元素一一对应相加。注意，第二个数组比第一个多出两个，不要把第二个数组遍历完。
```h

const num1 = [3, 4, 5, 6, 7];
const num2 = [43, 23, 5, 67, 87, 3, 6];

答案：

const zipWith = f => xs => ys => {
  if (xs.length === 0 || ys.length === 0) return [];
  const [xHead, ...xTail] = xs;
  const [yHead, ...yTail] = ys;
  return [f(xHead)(yHead), ...zipWith(f)(xTail)(yTail)];
};

const add = x => y => x + y;

zipWith(add)(num1)(num2);

```
问题二： 将 Stark 家族成员提取出来。注意，目标数据在数组前面，使用 filter 方法遍历整个数组是浪费。
```h

const houses = [
  "Eddard Stark",
  "Catelyn Stark",
  "Rickard Stark",
  "Brandon Stark",
  "Rob Stark",
  "Sansa Stark",
  "Arya Stark",
  "Bran Stark",
  "Rickon Stark",
  "Lyanna Stark",
  "Tywin Lannister",
  "Cersei Lannister",
  "Jaime Lannister",
  "Tyrion Lannister",
  "Joffrey Baratheon"
];

答案：

const takeWhile = f => ([head, ...tail]) =>
  f(head) ? [head, ...takeWhile(f)(tail)] : [];

const isStark = name => name.toLowerCase().includes("stark");

takeWhile(isStark)(houses);

```
问题二： 找出数组中的奇数，然后取出前4个：
```h

const numList = [1, 3, 11, 4, 2, 5, 6, 7];

答案：

const takeFirst = (limit, f, arr) => {
  if (limit === 0 || arr.length === 0) return [];
  const [head, ...tail] = arr;
  return f(head)
    ? [head, ...takeFirst(limit - 1, f, tail)]
    : takeFirst(limit, f, tail);
};

const isOdd = n => n % 2 === 1;

takeFirst(4, isOdd, numList);

```
# 使用高阶函数遍历数组时可能遇到的陷阱

问题一：  从长度为 100 万的随机整数组成的数组中取出偶数，再把所有数字乘以 3
```h

// 用我们刚刚定义的辅助函数来生成符合要求的数组
const bigArr = genNumArr(1e6, 100);

答案：

const isEven = num => num % 2 === 0;
const triple = num => num * 3;

bigArr.filter(isEven).map(triple);

// 注意，上面的解决方案将数组遍历了两次，无疑是浪费。如果写 for 循环，只用遍历一次：

const results = [];
for (let i = 0; i < bigArr.length; i++) {
  if (isEven(bigArr[i])) {
    results.push(triple(bigArr[i]));
  }
}

// 测试，先 filter 再 map 的方法耗时 105.024 ms，而采用 for 循环的方法耗时仅 25.598 ms！那是否说明遇到此类情况必须用 for 循环解决呢? No！

# Transduce

先用 reduce 来定义 filter 和 map，至于为什么这样做等下再解释。
```h
const filter = (f, arr) =>
  arr.reduce((acc, val) => (f(val) && acc.push(val), acc), []);

const map = (f, arr) => arr.reduce((acc, val) => (acc.push(f(val)), acc), []);
```
重新定义的 filter 和 map 有共有的逻辑。我们把这部分共有的逻辑叫做 reducer。有了共有的逻辑后，我们可以进一步地抽象，把 reducer 抽离出来，然后传入 filter 和 map：
```h
const filter = f => reducer => (acc, value) => {
  if (f(value)) return reducer(acc, value);
  return acc;
};

const map = f => reducer => (acc, value) => reducer(acc, f(value));
```
现在 filter 和 map 的函数 signature 一样，我们就可以进行函数组合（function composition）了。
```h
const pushReducer = (acc, value) => (acc.push(value), acc);

bigNum.reduce(map(triple)(filter(isEven)(pushReducer)), []);
```
但是这样嵌套写法易读性太差，很容易出错。我们可以写一个工具函数来辅助函数组合：
```h
const pipe = (...fns) => (...args) => fns.reduce((fx, fy) => fy(fx), ...args);
```
然后我们就可以优雅地组合函数了：
```h
bigNum.reduce(
  pipe(
    filter(isEven),
    map(triple)
  )(pushReducer),
  []
);
```
经过测试（用 console.time()/console.timeEnd()）,上面的写法耗时 33.898 ms，仅比 for 循环慢 8 ms。为了代码的易维护性和易读性，这点性能上的微小牺牲，我认为是可以接受的。
这种写法叫 transduce。有很多工具库提供了 transducer 函数。比如 [transducers-js](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fcognitect-labs%2Ftransducers-js)。除了用 transducer 来遍历数组，还能用它来遍历对象和其它数据集。功能相当强大。

# for 循环和 for ... of 循环的区别
for ... of 循环是在 ES6 引入 Iterator 后，为了遍历 Iterable 数据类型才产生的。EcmaScript 的 Iterable 数据类型有数组，字符串，Set 和 Map。for ... of 循环属于重型的操作（具体细节我也没了解过），如果用 AirBNB 的 ESLint 规则，在代码中使用 for ... of 来遍历数组是会被禁止的。
那么，for ... of 循环应该在哪些场景使用呢？目前我发现的合理使用场景是遍历自定义的 Iterable。来看这个：

```
问题： 将 Stark 家族成员名字遍历，每次遍历暂停一秒，然后将当前遍历的名字打印来，遍历完后回到第一个元素再重新开始，无限循环。
```h

const starks = [
  "Eddard Stark",
  "Catelyn Stark",
  "Rickard Stark",
  "Brandon Stark",
  "Rob Stark",
  "Sansa Stark",
  "Arya Stark",
  "Bran Stark",
  "Rickon Stark",
  "Lyanna Stark"
];

答案：

function* repeatedArr(arr) {
  let i = 0;
  while (true) {
    yield arr[i++ % arr.length];
  }
}

const infiniteNameList = repeatedArr(starks);

const wait = ms =>
  new Promise(resolve => {
    setTimeout(() => {
      resolve();
    }, ms);
  });

(async () => {
  for (const name of infiniteNameList) {
    await wait(1000);
    console.log(name);
  }
})();

```
前面讲到的问题基本覆盖了大部分需要使用 for 循环的场景。那是否我们可以保证永远不用 for 循环呢？其实不是。常用的数组原型链上的 map，filter 等高阶函数，底层其实是用 for 循环实现的。在需要写一些底层代码的时候，还是需要写 for 循环的。