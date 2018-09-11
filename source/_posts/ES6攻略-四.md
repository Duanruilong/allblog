---
title: ES6攻略(四)
date: 2018-05-29 00:09:06
tags: [编程,Javascript,ES6]
description: 
copyright: false
categories: ES6
top:
---
ES6的改版，主要是通过引入JAVA等静态语言的优秀思想来解决老版本的一些痼疾，如作用域，回调，继承和封装等问题。这些改革措施是非常成功的，ES6让JS真正变成了一种好用的语言。这里是关于Reflect和Proxy。

![ES6攻略](https://coding.net/u/DRuilong/p/phone_drl/git/raw/master/image/es6_d.png)

<!-- more -->

# Set
之前的JS只提供了两种容器：`数组`和`对象`。es6又添加了另一种非常有用的数据结构：`Set`。他就是`集合`的意思，本身也是一种容器。
既然是容器，肯定有对应的增查改删的操作了。增查改删是容器必备的职能。那么这种新式容器，到底有什么特点呢？
拿他跟数组比较的话，`Set`的最大的特点就是`每个值都是唯一的，不能有重复值`。经常会遇到数组去重的问题吧？自从有了Set，就开始变得美好了。

```h
let container = new Set([1, 2, 2, 3, 4, 4]);//可以直接传入数组进行初始化
container.add(5);//添加元素
container.delete(5);//删除元素
console.log(container.has(5));//false，5这个元素被删除了
console.log(container);//Set { 1, 2, 3, 4 }
console.log(container.size);//长度4
let unique_arr = [...container];//省略号语法
console.log(unique_arr);//唯一值数组：[ 1, 2, 3, 4 ]
```
Set的构造方法可以直接传数组进行初始化，当然也可以这样声明：
`let mySet = new Set();`

Set跟数组极为类似，二者可以互相转换着用。有了它，就可以轻松的解决数组去重问题了。

```h
//简单去重方案
var arr = [1,1,2,3,3,3];
console.log([...new Set(arr)]);//一句话搞定
```

这并不适用于含有`重复对象的数组`，因为对象是比较的是内存地址。
```h
console.log({}=={});//false,内存地址不同
let arr = [{},{},{}];
console.log([...new Set(arr)]);//对象是无法去重的
```

对象数组到底如何去重呢？
一种办法是你需要递归比较，两个对象的每一个值是否相等，不等的放入一个新数组中，相等的跳过。但是这个方法很普通，可以利用`Set`的不重复特性，把对象先转成字符串存入`Set`，去掉重复值后，再转回来就好。

```h
function unique(arr){
 var container = new Set();
//遍历
 arr.forEach(function(val){
//将对象转换为字符串,存入set容器中，确保唯一性。
  container.add(JSON.stringify(val));
 });

 var newarr = [...container].map(function(item){
   return JSON.parse(item);//转换回去
  });
 return newarr;
}
```

# 新数据结构：Map
这个是`Map`容器，而不是前面的`map()`方法，千万别弄混了（注意大小写）！`Map`就是键值对容器，跟`Object`非常类似。它有什么特别之处呢？
其实`Object`有个很大的问题：就是键只能用字符串，而不能使用其他数据类型。
如果让键不限于字符串，而是各种数据类型呢？比如num？

```h
<!DOCTYPE html>
<html>
<head lang="en">
<meta charset="UTF-8">
<title>map</title>
</head>
    <body>
        <div>
            <button id="btn1">提交</button>
            <button id="btn2">关闭</button>
        </div>　　　
    </body>
</html>
<script>
    var dom1 = document.getElementById('btn1');
    var dom2 = document.getElementById('btn2');

    var callback1 = function(){
        alert('提交');
    };
    var callback2 = function(){
        alert('关闭');
    };
    const m = new Map();
    m.set(dom1,callback1); //键是dom
    m.set(dom2,callback2);
    console.log(m);
</script>
```

![结果：](http://mmbiz.qpic.cn/mmbiz_png/amhuPdMsm1nZRR0oA8QqZfcQGqUNYUJzkRiclCN5500iaJhsyUpyjt8bu7xM47zaUoDJibSN1iazVaKN9wQjLkctgw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)


一般来说，对象的键应该是唯一的。Object的键因为是字符串，所以没啥问题，但Map既然放开了类型限制，这就得注意了

```h

const map = new Map();
map.set(['a'], 555);
map.get(['a']) // undefined

```
因为对象比较的是内存地址，所以`['a']`作为新开辟的空间，无法在`Map容器`中找到。

再来看一下Map的增查改删，非常简单。

```h
//利用二维数组，可以构建Map
const myMap = new Map([
 ['name', '张三'],
 ['content', 'hello,world']
]);
console.log(myMap);//Map { 'name' => '张三', 'content' => 'hello,world' }
console.log(myMap.size); // 长度
myMap.set('age',18);//增加属性
console.log(myMap.get('name')); //获取属性
myMap.delete('content');//删除属性
console.log(myMap.has('content')); // 判断是否有这个属性
console.log(myMap.keys());//获取所有的键 MapIterator { 'name', 'age' }
console.log(myMap.values());//获取所有的值 MapIterator { '张三', 18 }
```

获取的keys集合是`MapIterator`类型的，这叫遍历器。顾名思义，就是专供遍历用的一种数据结构

# Reflect是面镜子

`Reflect`就是反射的意思，那究竟反射啥呢？
大家知道镜子可以反射吧？
`Reflect`就是一面镜子。那是谁照镜子呢？就是`Object`。
也就是说，`Object`把自己的属于语言内部的方法（比如Object.defineProperty）照到了`Reflect`上。

```h
<!DOCTYPE html>
<html>
<head lang="en">
<meta charset="UTF-8">
<title>利用反射实现数据和视图联动</title>

</head>
<body>
<div>
<div>测试Object：<input type="text" id='userName'/></div>
<div>测试Reflect：<input type="text" id='age'/></div>
<div id="showName"></div>
<div id="showAge"></div>
</div>　　　
</body>
</html>
<script>
var userInfo = {};
//Object的方式
Object.defineProperty(userInfo, "userName", {
get: function(){
return document.getElementById('showName').innerHTML;
},
set: function(name){
document.getElementById('showName').innerHTML = name;
}
});

//Reflect是一面大镜子，Object你有的我就有！
Reflect.defineProperty(userInfo, "age", {
get: function(){
return document.getElementById('showAge').innerHTML;
},
set: function(name){
document.getElementById('showAge').innerHTML = name;
}
});

document.getElementById('userName').onkeyup = function(e){

userInfo.userName = this.value;
};

document.getElementById('age').onkeyup = function(e){

userInfo.age = this.value;
};
</script>
```

> `Reflect`一共是13个静态方法，就是13种武器哈：
```h

Reflect.apply(target,thisArg,args)
Reflect.construct(target,args)
Reflect.get(target,name,receiver)
Reflect.set(target,name,value,receiver)
Reflect.defineProperty(target,name,desc)
Reflect.deleteProperty(target,name)
Reflect.has(target,name)
Reflect.ownKeys(target)
Reflect.isExtensible(target)
Reflect.preventExtensions(target)
Reflect.getOwnPropertyDescriptor(target, name)
Reflect.getPrototypeOf(target)
Reflect.setPrototypeOf(target, prototype)
```
看下几个常用的方法。

```h

let obj = {
name:'jack',
age:18
};
//简单set方法
Reflect.set(obj,'name','tom');
console.log(obj.name);//tom
//简单get方法
let age = Reflect.get(obj,'age');
console.log(age);//18

//带有接收者对象的set方法
let receiver = {
name:'lily'
};
//意思就是：发起者是obj，把name属性改为bill，但这个动作的接收者是receiver。
//所以，obj的name属性还是tom，但接收者receiver的name被改变了。
//这种机制的作用是，可以在对象A中操作对象B。
Reflect.set(obj,'name','bill',receiver);

console.log(obj.name);//tom
console.log(receiver.name);//bill
//判断obj是否有name属性？
//旧写法--in
console.log('name' in obj);//true
//新写法--has
console.log(Reflect.has(obj,'name'));//true
```

他与`Proxy`结合使用更合理。

# Proxy

`Proxy`就是拦截器，也可以译为代理器，跟设计模式的代理模式差不多。他跟`Reflect`是一一对应的。
只要`Proxy`对象有的方法，`Reflect`对象上都有。当然咯，`Proxy`没有的，`Reflect`也有。

拦截器本身也是对象，他的主要作用在于拦截。看一下语法定义：
`var proxy = new Proxy(target, handler);`

>1.target就是目标对象，我们要给哪个对象加拦截？
> 2.handler就是处理对象，他是一系列拦截操作的集合。这些拦截操作，就是对应的Reflect具有的十三种静态方法（所谓的一一对应其实就是说的这个）。注意，只有这十三种武器，不支持其他的。

拿最简单的set方法为例:

```h
//形参说明：
// target:待测试的Person实例
// validator:验证规则personValidators

function createValidator(target, validator) {
//拦截器，拦截赋值操作，触发set函数
return new Proxy(target, {

 set(target, key, value) {
//如果待测试的Person对象中含有这个属性，就启用验证规则，
// 验证规则的方法：name(val)和age(val)，名称需要跟传入的属性key一致。
  if (target.hasOwnProperty(key)) {

    let rules = validator[key];//rules拿到的是name方法的引用
//相当于调用方法name('hello')
    if (rules(value)) {
//如果验证无误，target的name属性可以修改成功
     return Reflect.set(target, key, value);
    } else {
//否则失败
      return 'error';
   }
  } else {
     return 'error';
  } 
 }
});
}
//验证规则
const personRules = {
 name(val) {
   return typeof val === 'string';
 },
 age(val) {
   return typeof val === 'number' && val > 18;
 }
};
//测试类
class Person {
 constructor(name, age) {
   this.name = name;
   this.age = age;
//开启验证，传入这个类的实例和验证规则personRules
   return createValidator(this, personRules);
 }
}

const bill = new Person('Bill', 25);
bill.name = 'jack';//赋值成功
bill.age = 10;//赋值失败
console.log(bill.name);//jack
console.log(bill.age);//25
```

`createValidator`这个函数，返回的是Proxy拦截器对象。这个拦截器对象，对原Person类的set操作做了修改，加入了一些验证方案，其实就是起到了包装增强的作用，让Person具有了可验证字段类型的功能。

再来看下`Reflect.apply`的用法。这个方法的使用频率还是蛮高的

```h
//假如这个foo函数是同事张三写的
var obj = {};
function foo(){

console.log(this);
}

//李四引入并使用了这个函数
foo.apply(obj);
//这时，如果张三把函数名改成了bar,如果没及时通知到李四，那么李四的调用就会报错了。
//可以在不依赖函数名的情况下改变函数的作用域么？
Function.prototype.apply.call(foo,obj);
//这种写法太长了，可以使用反射Reflect给我们提供的简单方法。
Reflect.apply(foo,obj,[]);//要注意，apply最后一个参数要跟数组，这个没有的话也得传[]，否则报错
```

## 结语
`Proxy`是用于修改语言内部行为的机制，因为`Object`对象内部的方法大多属于这种情况，所以`handler`的配置，又跟`Reflect`紧密联系在一起（因为Reflect是Object的镜子嘛）。
应该说，`Reflect`和`Proxy`之间的关系还是不太容易理顺的。
