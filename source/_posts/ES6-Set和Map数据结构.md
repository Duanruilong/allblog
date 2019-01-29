---
title: ES6--Set和Map数据结构
date: 2019-01-29 16:57:32
tags: [编程,Javascript,ES6]
description: 
copyright: false
categories: ES6
top:
---
ES6提供了新的数据结构Set, 它类似数组，但是成员的值都是唯一的，没有重复的值，Set本身是一个构造函数，用来生成Set数据结构。

![ES6攻略](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/es6_d.png)

<!-- more -->

# Set

Set构造函数可以结构一个数据作为参数，用来初始化

```h
let s = new Set();
let  s = new Set( [1,2,3,4,4] );
s
//Set{1,2,3,4}
s.size
//4
[...set]
//[1,2,3,4]

```
因此也出现了l两种数组去重的方法

```h

[...new Set( [1,2,3,4,4] ) ]
//[1,2,3,4]

//或者
Array.from( new Set( [1,2,3,4,5,5] )  )
//[1,2,3,4,5]
```



Set实例的属性和方法
Set结构的实例有属性:
Set.prototype.constructor：构造函数，默认就是Set函数；
Set.prototype.size：返回Set实例的成员总数；
Set实例的方法分为两类：操作方法和遍历方法；
add(value)：添加某个值，返回Set结构本身
delete(value)：删除某个值，返回一个布尔值，表示删除是否成功；
has(value)：返回一个布尔值，表示该值是否为Set的成员；
clear() ：清除所有成员，没有返回值；

```h
let s = new Set( [1,2,3,4,5,5] );
s.add(4);
s.size
//5
s.has(2)
//true
s.delete(2)
s.has(2)
//fasle
s.clear()
```

## 遍历操作

Set结构的实例有四个遍历方法，可以用于遍历成员
keys()：返回键名的遍历器
values()：返回键值的遍历器
entries()：返回键值对的遍历器
forEach() :使用回调函数遍历每个成员

###（1）keys()，values()，entries()

keys方法、values方法、entries方法返回的都是遍历器对象（详见《Iterator 对象》一章）。由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以keys方法和values方法的行为完全一致。

```h

let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]

```

### （2）forEach()

Set结构的实例的forEach方法，用于对每个成员执行某种操作，没有返回值。

```h

let set = new Set([1, 2, 3]);
set.forEach((value, key) => console.log(value * 2) )
// 2
// 4
// 6
```

# Map

JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。

```h

const map = new Map([
["name","张三"] , ["title","Author"] 
]);
map.size
//2
map.has("name")//true
map.get("name")//"张三"
```

## 实例的属性好操作方法
（1）size属性：返回 Map 结构的成员总数。

```h

const map = new Map();
map.set('foo', true);
map.set('bar', false);
map
//Map {"foo" => true, "bar" => false}
map.size // 2
```

（2）set(key, value)
set方法设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。

```h

const m = new Map();

m.set('edition', 6)        // 键是字符串
m.set(262, 'standard')     // 键是数值
m.set(undefined, 'nah')    // 键是 undefined
```

（3）get(key)
get方法读取key对应的键值，如果找不到key，返回undefined。

```h

const m = new Map([
["name","MGT360124"],["age",18]
])
m.get('name')
//"MGT360124"
```

（4）has(key),delete(key),clear()
has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。

```h
const m = new Map();

m.set('edition', 6);
m.set(262, 'standard');
m.set(undefined, 'nah');

m.has('edition')     // true
m.has('years')       // false
m.has(262)           // true
m.has(undefined)     // true

m.delete(undefined)
m.has(undefined)       // false
```

## Map遍历方法
Map结构原生提供三个遍历器生成函数和一个遍历方法;
keys()：返回键名的遍历器
values()：返回键值的遍历器
entries()：返回所有成员的遍历器
forEach()：遍历Map的所有成员
Map遍历的顺序就是插入的顺序

```h

const map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"
```

Map结构转为数组结构

```h

const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]

[...map.values()]
// ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]

```

Map 还有一个forEach方法，与数组的forEach方法类似，也可以实现遍历。

