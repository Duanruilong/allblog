---
title: 深度解析CSS单位px、em、rem、vh、vw、vmin、vmax
date: 2020-05-28 21:59:51
tags: [CSS,编程,Javascript]
description: 
copyright: true
categories: 编程
top: 
---

# 介绍

这是一份关于深度解析CSS单位px、em、rem、vh、vw、vmin、vmax。

![js](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/css.png)

<!--more-->


## 1、px 

px：绝对单位，页面按精确像素展示

##  2、em

em：相对单位，基准点为父节点字体的大小，如果自身定义了font-size按自身来计算（浏览器默认字体是16px），整个页面内1em不是一个固定的值。

### em是指字体高度

浏览器默认`1em=16px`,所以`0.75em=12px`;我们经常会在页面上看到根元素写的`font-size:62.5%`; 这样em就成了`16px*62.5=10em`;这是显示在页面的字体大小是10px; 这样`12px=1.2em,10px=1em`,也就是说只需要将你的原来的px数值除以10，然后换上em作为单位就行了

### em的特点 

em是个相对值 他会根据父级元素的大小而变化 但是如果嵌套了多个元素 要计算它的大小,是件很头疼的事情
这样的情况下,就出现了rem rem的区别在于它是相对于根基元素的,因此不会被它的父类影响到

### 结论：

之所以前端行业做移动端会普遍默认用rem或em,是因为可以通过js控制根元素(或者用@media)来达到适配各种分辨率的字体大小的效果


## 3、rem

rem：相对单位，可理解为`”root em”`, 相对根节点html的字体大小来计算，CSS3新加属性，chrome/firefox/IE9+支持。// 是截止目前用的最多也是最流行的

`rem`在移动端应用可参考淘宝的页面(http://m.taobao.com)[http://m.taobao.com] (html的font-size通过动态计算获取)


> 方案一：简单常用版本

页面基准`750px;html font-size`值的计算：

```h

(function(doc,win) {
	var htmlFont=function(){var docEl=doc.documentElement,l=docEl.clientWidth,f;f=l/7.5;l>750?docEl.style.fontSize=100+"px": docEl.style.fontSize=f+"px"
};

htmlFont();win.addEventListener("resize",htmlFont,false)
})(document,window);


```

注：把这段 原生JS 放到 HTML 的 head 标签中即可(需设置meta缩放比1:1）

`<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0, user-scalable=no" />`

> ----------分割线----------

> 方案二：rem 进阶版--高清方案

和上面一下还是把这段 原生JS 放到 HTML 的 head 标签中即可（注:不要手动设置viewport，该方案自动帮你设置，通过修改viewport  属性放大缩小 `initial-scale`）

```h


!function(e) {
	function t(a){if(i[a])return i[a].exports;var n=i[a]={exports: {
},id:a,loaded:!1
};

return e[a].call(n.exports,n,n.exports,t),n.loaded=!0,n.exports
}

var i= {
};

return t.m=e,t.c=i,t.p="",t(0)
}([function(e,t) {
"use strict"
;Object.defineProperty(t,"__esModule",{value: !0
});

var i=window;t["default"]=i.flex=function(e,t) {
var a=e||100,n=t||1,r=i.document,o=navigator.userAgent,d=o.match(/Android[\S\s]+AppleWebkit\/(\d{3
})/i),l=o.match(/U3\/((\d+|\.) {
5,
})/i),c=l&&parseInt(l[1].split(".").join(""),10)>=80,p=navigator.appVersion.match(/(iphone|ipad|ipod)/gi),s=i.devicePixelRatio||1;p||d&&d[1]>534||c||(s=1);var u=1/s,m=r.querySelector('meta[name="viewport"]');m||(m=r.createElement("meta"),m.setAttribute("name","viewport"),r.head.appendChild(m)),m.setAttribute("content","width=device-width,user-scalable=no,initial-scale="+u+",maximum-scale="+u+",minimum-scale="+u),r.documentElement.style.fontSize=a/2*s*n+"px"
},e.exports=t["default"]
}]);

flex(100, 1);

```


- 代码原理：

这是阿里团队的高清方案布局代码，所谓高清方案就是根据设备屏幕的DPR（设备像素比，又称DPPX，比如dpr=2时，表示1个CSS像素由4个物理像素点组成） 动态设置 html 的`font-size`, 同时根据设备DPR调整页面的缩放值，进而达到高清效果。

- 代码优势：

```h
引用简单，布局简便
根据设备屏幕的DPR,自动设置最合适的高清缩放。
保证了不同设备下视觉体验的一致性。（老方案是，屏幕越大元素越大；此方案是，屏幕越大，看的越多）
有效解决移动端真实1px问题（这里的1px 是设备屏幕上的物理像素）
总结：
1)两个方案默认 1rem = 100px，所以你布局的时候，完全可以按照设计师给你的效果图写各种尺寸啦。
2)绝不是每个地方都要用rem，rem只适用于固定尺寸！
3)比如你在效果图上量取的某个按钮元素长 86px, 宽27px ，那你直接可以这样写样式：
.div1{width:.85rem;height:.27rem;}

```

> 高清方案问题总结详看: (https://github.com/ant-design/ant-design-mobile/issues/732)[https://github.com/ant-design/ant-design-mobile/issues/732]

> 高清方案问题和参考: (https://zhuanlan.zhihu.com/p/25552482)[https://zhuanlan.zhihu.com/p/25552482]



> ----------分割线----------


关于方法二要修改页面根`font-size`；和`viewport`的比例，引出一种简单粗暴的页面适配，就是将设计稿按照PC的正常切图的方法输出后直接修改viewport的缩放比例达到想要的结果；缩放代码如下：`mate viewport`标签，用JS追加

```h

var head=document.getElementsByTagName('head')[0],scalevalue=screen.width/750,metaTag=document.createElement('meta');metaTag.setAttribute('name','viewport');metaTag.setAttribute('content','width='+scalevalue+',initial-scale='+scalevalue+',maximum-scale='+scalevalue+',user-scalable=no');head.appendChild(metaTag);

```


`tips`：和上面相似的适配，也可以利用css3 transform 的scale 值来实现,只是需要在放大（旋转）元素的基点位置上（`transform-origin`）做一下处理，下面用`vw`适配控制页面大小的方法中有用到，原理很简单；

## 4、vw、vh、vmin、vmax

vw、vh、vmin、vmax 主要用于页面视口大小布局，相对于rem;v*在页面布局上更加方便简单

```h

vw：viewpoint width，视窗宽度，1vw等于视窗宽度的1%。
vh：viewpoint height，视窗高度，1vh等于视窗高度的1%。
vmin：vw和vh中较小的那个。
vmax：vw和vh中较大的那个。

```

- `vw`逻辑非常清晰，`"1vw ＝ 1/100th viewport width"`，用viewport width的百分比来设置element width。

- `vw`被支持的太晚是其并不流行的根本原因，而当时移动端web app/page的开发需求已经十分旺盛，弹性布局是一种不错的移动端界面兼容展现方式，对于rem机遇就此而来，便成为一个实现弹性布局效果的极佳方案。

其实看目前状况，对vw最不利的是Android Browser，ndroid Browser 4.4以下的用户是不兼容的，目前新版x5更新后，安卓版微信的vw vh vmin vmax 已经没有兼容性问题了，那么随着时间推移，相信未来vw将会流行。

`vw, vh, vmin, vmax`：IE9+局部支持，chrome/firefox/safari/opera支持，ios safari 8+支持，android browser4.4+支持，chrome for android39支持

!(支持)[https://images2017.cnblogs.com/blog/824939/201707/824939-20170727110203515-222726527.jpg]

页面是基于浏览器窗口大小自动适配的如果感觉页面太大；可添加下面JS通过缩放控制页面的最大尺寸 

```h
<script type="text/javascript">
    (function(doc,win){
        var htmlScale = function(){
            console.log('2');
            oBdoy = doc.getElementsByTagName('html')[0];
            var domWidht = doc.documentElement.clientWidth;
            var myScale = 750/domWidht;
            if(domWidht){
                oBdoy.style.transform = 'scale('+myScale+')';
                oBdoy.style.transformOrigin = '50% '+0+'%';
            }else{
                oBdoy.removeAttribute('style','transform');
                oBdoy.removeAttribute('style','transformOrigin');
            }
             
        };
        htmlScale();
        win.addEventListener('resize',htmlScale,false);
    })(document,window);
</script>
　　

```

## 5、其它的单位还有

- %:百分比
- in:寸
- cm:厘米
- mm:毫米
- pt:point，大约1/72寸
- pc:pica，大约6pt，1/6寸
- ex：取当前作用效果的字体的x的高度，在无法确定x高度的情况下以0.5em计算(IE11及以下均不支持，firefox/chrome/safari/opera/ios safari/android browser4.4+等均需属性加么有前缀)

- ch:以节点所使用字体中的“0”字符为基准，找不到时为0.5em(ie10+,chrome31+,safair7.1+,opera26+,ios safari 7.1+,android browser4.4+支持)

 

实际应用中：建议综合` 固定宽度、字体大小可用rem、px；其他可结合vw %`
