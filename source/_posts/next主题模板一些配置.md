---
title: next主题模板一些配置
date: 2018-05-05 21:23:49
tags: 编程
description: 
copyright: true
categories: Hexo
---
使用Hexo + Github去搭建一个属于自己的博客站点，这里是一些next相关的细节配置。

![js](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/hexo_d.png)

<!-- more -->


# 1.修改文章底部的那个带#号的标签
    
实现效果图

具体实现方法

    ``` bash
        /themes/next/layout/_macro/post.swig
        找到 
        rel="tag">#
        修改为
        <i class="fa fa-tag"></i>
        完整的应该是这样的：  <a href="{{ url_for(tag.path) }}" rel="tag"><i class="fa fa-tag"></i> {{ tag.name }}</a>
    ```


# 2.在每篇文章末尾统一添加“本文结束”标记

实现效果图

具体实现方法

``` bash

    在路径 \themes\next\layout\_macro 中新建 passage-end-tag.swig 文件,并添加以下内容：

    <div>
        {% if not is_index %}
            <div style="text-align:center;color: #ccc;font-size:14px;">-------------本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
        {% endif %}
    </div>

    接着打开\themes\next\layout\_macro\post.swig文件，在post-body 之后， post-footer 之前添加如下部分代码（post-footer之前两个DIV）：

    <div>
    {% if not is_index %}
        {% include 'passage-end-tag.swig' %}
    {% endif %}
    </div>
```

然后打开主题配置文件`（_config.yml)`,在末尾添加：
``` bash
    # 文章末尾添加“本文结束”标记
    passage_end_tag:
    enabled: true

```
完成以上设置之后，在每篇文章之后都会添加如上效果图的样子。

# 3.修改“代码块自定义样式
具体实现方法
打开 `\themes\next\source\css\_custom\custom.styl`,向里面加入：(颜色可以自己定义)
``` bash
    // Custom styles.
    code {
        color: #ff7600;
        background: #fbf7f8;
        margin: 2px;
    }
    // 大代码块的自定义样式
    .highlight, pre {
        margin: 5px 0;
        padding: 5px;
        border-radius: 3px;
    }
    .highlight, code, pre {
        border: 1px solid #d6d6d6;
    }
```
# 4.侧边栏社交小图标设置
具体实现方法
打开主题配置文件`（_config.yml）`，搜索`social_icons:`,在[图标库](https://fontawesome.com/icons?from=io)找自己喜欢的小图标，并将名字复制在如下位置，保存即可
``` hash
    # 社交链接
    # Social Links
    social:
    GitHub: https://github.com/Duanruilong
    CSDN: https://github.com/Duanruilong
    微博: https://github.com/Duanruilong
    QQ: https://github.com/Duanruilong
    知乎: https://github.com/Duanruilong
    # Social Links Icons/设定链接的图标
    social_icons:
    enable: true
    icons_only: false
    transition: true
    # Icon Mappings.
    # KeyMapsToSocialItemKey: NameOfTheIconFromFontAwesome
    GitHub: github
    E-Mail: envelope
    Google: google
    Twitter: twitter
    Instagram: instagram
    StackOverflow: stack-overflow
    微博: weibo
    知乎: envelope
    QQ: qq
    微信: weixin
    CSDN: crosshairs
    links_title: discord
```
# 5.在网站底部加上访问量
具体实现方法
打开`\themes\next\layout\_partials\footer.swig`文件,在copyright前加上这句话：
``` hash
    <script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```
然后再合适的位置添加显示统计的代码，如图：
``` hash
    <div class="powered-by">
    <i class="fa fa-user-md"></i><span id="busuanzi_container_site_uv">
    本站访客数:<span id="busuanzi_value_site_uv"></span>
    </span>
    </div>
```
在这里有两中不同计算方式的统计代码：

    1.pv的方式，单个用户连续点击n篇文章，记录n次访问量
    ``` hash
        <span id="busuanzi_container_site_pv">
            本站总访问量<span id="busuanzi_value_site_pv"></span>次
        </span>
    ```

    2.uv的方式，单个用户连续点击n篇文章，只记录1次访客数
    ``` hash
        <span id="busuanzi_container_site_uv">
            本站总访问量<span id="busuanzi_value_site_uv"></span>次
        </span>
    ```

添加之后再执行`hexo d -g`，然后再刷新页面就能看到效果

# 6.添加热度
具体实现方法
next主题集成`leanCloud`，打开`/themes/next/layout/_macro/post.swig`,在画红线的区域添加`℃`：
``` hash
    {% if theme.post_meta.item_text %}
        <span class="post-meta-item-text">{{__('post.visitors')}} </span>
    {% endif %}
    <span class="leancloud-visitors-count"></span>
    <span>℃</span>
```
然后打开，`/themes/next/languages/zh-Hans.yml`,将画红框的改为热度就可以了
``` hash
post:
  created: 创建于
  modified: 更新于
  sticky: 置顶
  posted: 发表于
  in: 分类于
  read_more: 阅读全文
  untitled: 未命名
  toc_empty: 此文章未包含目录
  visitors: 热度
  wordcount: 字数统计
  min2read: 阅读时长
  copyright:
```
# 7.网站底部字数统计
具体方法实现
切换到根目录下，然后运行如下代码
``` hash
    $ npm install hexo-wordcount --save
```
然后在`/themes/next/layout/_partials/footer.swig`文件尾部加上：
``` hash
<div class="theme-info">
  <div class="powered-by"></div>
  <span class="post-count">博客全站共{{ totalcount(site) }}字</span>
</div>
```
# 8.添加 README.md 文件
每个项目下一般都有一个 README.md 文件，但是使用 hexo 部署到仓库后，项目下是没有 README.md 文件的。

在 Hexo 目录下的 `source` 根目录下添加一个 `README.md` 文件，修改站点配置文件 `_config.yml`，将 `skip_render` 参数的值设置为:
``` hash
skip_render: README.md
```
保存退出即可。再次使用 `hexo d` 命令部署博客的时候就不会在渲染 README.md 这个文件了。
# 9.设置网站的图标Favicon
在[EasyIcon](https://www.easyicon.net/)中找一张（32*32）的ico图标,或者去别的网站下载或者制作，并将图标名称改为favicon.ico，然后把图标放在`/themes/next/source/images`里，并且修改主题配置文件：
``` hash
# Put your favicon.ico into `hexo-site/source/` directory.
favicon: /favicon.ico
```
# 11.实现统计功能
在根目录下安装 `hexo-wordcount`,运行：
``` hash
$ npm install hexo-wordcount --save
```
然后在主题的配置文件中，配置如下：
``` hash
# Post wordcount display settings
# Dependencies: https://github.com/willin/hexo-wordcount
post_wordcount:
  item_text: true
  wordcount: true
  min2read: true
```
# 12.添加顶部加载条
打开`/themes/next/layout/_partials/head.swig`文件，添加如下代码
```hash
<script src="//cdn.bootcss.com/pace/1.0.2/pace.min.js"></script>
<link href="//cdn.bootcss.com/pace/1.0.2/themes/pink/pace-theme-flash.css" rel="stylesheet">
```
但是，默认的是粉色的，要改变颜色可以在`/themes/next/layout/_partials/head.swig`文件中添加如下代码（接在刚才link的后面）
```hash
<style>
    .pace .pace-progress {
        background: #1E92FB; /*进度条颜色*/
        height: 3px;
    }
    .pace .pace-progress-inner {
         box-shadow: 0 0 10px #1E92FB, 0 0 5px     #1E92FB; /*阴影颜色*/
    }
    .pace .pace-activity {
        border-top-color: #1E92FB;    /*上边框颜色*/
        border-left-color: #1E92FB;    /*左边框颜色*/
    }
</style>
```
现在升级最新版的next主题，升级后只需修改主题配置文件(`_config.yml`)将`pace: false`改为`pace: true`就行了，你还可以换不同样式的加载条.
# 13.在文章底部增加版权信息
在目录 `next/layout/_macro/`下添加 `my-copyright.swig`：
```hash
{% if page.copyright %}
<div class="my_post_copyright">
  <script src="//cdn.bootcss.com/clipboard.js/1.5.10/clipboard.min.js"></script>
  
  <!-- JS库 sweetalert 可修改路径 -->
  <script src="https://cdn.bootcss.com/jquery/2.0.0/jquery.min.js"></script>
  <script src="https://unpkg.com/sweetalert/dist/sweetalert.min.js"></script>
  <p><span>本文标题:</span><a href="{{ url_for(page.path) }}">{{ page.title }}</a></p>
  <p><span>文章作者:</span><a href="/" title="访问 {{ theme.author }} 的个人博客">{{ theme.author }}</a></p>
  <p><span>发布时间:</span>{{ page.date.format("YYYY年MM月DD日 - HH:MM") }}</p>
  <p><span>最后更新:</span>{{ page.updated.format("YYYY年MM月DD日 - HH:MM") }}</p>
  <p><span>原始链接:</span><a href="{{ url_for(page.path) }}" title="{{ page.title }}">{{ page.permalink }}</a>
    <span class="copy-path"  title="点击复制文章链接"><i class="fa fa-clipboard" data-clipboard-text="{{ page.permalink }}"  aria-label="复制成功！"></i></span>
  </p>
  <p><span>许可协议:</span><i class="fa fa-creative-commons"></i> <a rel="license" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank" title="Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0)">署名-非商业性使用-禁止演绎 4.0 国际</a> 转载请保留原文链接及作者。</p>  
</div>
<script> 
    var clipboard = new Clipboard('.fa-clipboard');
    $(".fa-clipboard").click(function(){
      clipboard.on('success', function(){
        swal({   
          title: "",   
          text: '复制成功',
          icon: "success", 
          showConfirmButton: true
          });
	});
    });  
</script>
{% endif %}
```
在目录`next/source/css/_common/components/post/`下添加`my-post-copyright.styl`：
``` hash
.my_post_copyright {
  width: 85%;
  max-width: 45em;
  margin: 2.8em auto 0;
  padding: 0.5em 1.0em;
  border: 1px solid #d3d3d3;
  font-size: 0.93rem;
  line-height: 1.6em;
  word-break: break-all;
  background: rgba(255,255,255,0.4);
}
.my_post_copyright p{margin:0;}
.my_post_copyright span {
  display: inline-block;
  width: 5.2em;
  color: #b5b5b5;
  font-weight: bold;
}
.my_post_copyright .raw {
  margin-left: 1em;
  width: 5em;
}
.my_post_copyright a {
  color: #808080;
  border-bottom:0;
}
.my_post_copyright a:hover {
  color: #a3d2a3;
  text-decoration: underline;
}
.my_post_copyright:hover .fa-clipboard {
  color: #000;
}
.my_post_copyright .post-url:hover {
  font-weight: normal;
}
.my_post_copyright .copy-path {
  margin-left: 1em;
  width: 1em;
  +mobile(){display:none;}
}
.my_post_copyright .copy-path:hover {
  color: #808080;
  cursor: pointer;
}
```
修改`next/layout/_macro/post.swig`，在代码
```hash
<div>
      {% if not is_index %}
        {% include 'wechat-subscriber.swig' %}
      {% endif %}
</div>
```
之前添加增加如下代码：
``` hash
<div>
      {% if not is_index %}
        {% include 'my-copyright.swig' %}
      {% endif %}
</div>
```
修改`next/source/css/_common/components/post/post.styl`文件，在最后一行增加代码：
``` hash
@import "my-post-copyright"
```
保存重新生成即可。
如果要在该博文下面增加版权信息的显示，需要在 Markdown 中增加copyright: true的设置，类似：

小技巧：如果你觉得每次都要输入`copyright: true`很麻烦的话,那么在`/scaffolds/post.md`文件中添加：
``` hash
---
title: {{ title }}
date: {{ date }}
tags:
description: 
copyright: 
categories:
---
```
这样每次`hexo new "你的内容"`之后，生成的md文件会自动把`copyright:`加到里面去
(注意：如果解析出来之后，你的原始链接有问题：如：`http://yoursite.com/2018/05/05/next主题模板一些配置/`,那么在根目录下`_config.yml`中写成类似这样：）
``` hash
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://duanruilong.github.io/
root: /blog
permalink: :year/:month/:day/:title/
permalink_defaults:
```
# 14.修改网页底部的桃心
打开`themes/next/layout/_partials/footer.swig`，找到：
``` hash
<span class="with-love">
    <i class="fa fa-{{ theme.authoricon }}" style="color:red"></i>
</span>
  ```
然后还是在[图标库](https://fontawesome.com/icons?from=io)中找到你自己喜欢的图标，然后修改画红线的部分就可以了。
# 15.文章加密访问
打开`themes->next->layout->_partials->head.swig`文件,在以下位置插入这样一段代码：
``` hash
<script>
    (function () {
        if ('{{ page.password }}') {
            if (prompt('请输入文章密码') !== '{{ page.password }}') {
                alert('密码错误！');
                if (history.length === 1) {
                    location.replace("http://xxxxxxx.xxx"); // 这里替换成你的首页
                } else {
                    history.back();
                }
            }
        }
    })();
</script>
```
然后在文章上写成类似这样：
``` hash
---
title: {{ title }}
date: {{ date }}
tags:
description: 
copyright: 
categories:
password: password
---
```
# 16.添加jiathis分享
在主题配置文件中，jiathis为true，就行了
``` hash
# Share 分享
jiathis: true
# Warning: JiaThis does not support https.
#add_this_id:
```
如果你想自定义话，打开`themes/next/layout/_partials/share/jiathis.swig`修改就可以了
``` hash
<!-- JiaThis Button BEGIN -->
<div class="jiathis_style">
  <a class="jiathis_button_tsina"></a>
  <a class="jiathis_button_tqq"></a>
  <a class="jiathis_button_weixin"></a>
  <a class="jiathis_button_cqq"></a>
  <a class="jiathis_button_douban"></a>
  <a class="jiathis_button_renren"></a>
  <a class="jiathis_button_qzone"></a>
  <a class="jiathis_button_kaixin001"></a>
  <a class="jiathis_button_copy"></a>
  <a href="http://www.jiathis.com/share" class="jiathis jiathis_txt jiathis_separator jtico jtico_jiathis" target="_blank"></a>
  <a class="jiathis_counter_style"></a>
</div>
```
# 17.博文置顶或者排序
修改 `hero-generator-index` 插件，把文件：`node_modules/hexo-generator-index/lib/generator.js` 内的代码替换为：
``` hash
'use strict';
var pagination = require('hexo-pagination');
module.exports = function(locals){
  var config = this.config;
  var posts = locals.posts;
    posts.data = posts.data.sort(function(a, b) {
        if(a.top && b.top) { // 两篇文章top都有定义
            if(a.top == b.top) return b.date - a.date; // 若top值一样则按照文章日期降序排
            else return b.top - a.top; // 否则按照top值降序排
        }
        else if(a.top && !b.top) { // 以下是只有一篇文章top有定义，那么将有top的排在前面（这里用异或操作居然不行233）
            return -1;
        }
        else if(!a.top && b.top) {
            return 1;
        }
        else return b.date - a.date; // 都没定义按照文章日期降序排
    });
  var paginationDir = config.pagination_dir || 'page';
  return pagination('', posts, {
    perPage: config.index_generator.per_page,
    layout: ['index', 'archive'],
    format: paginationDir + '/%d/',
    data: {
      __index: true
    }
  });
};
```
在文章中添加 `top` 值，数值越大文章越靠前，如
``` hash 
---
title: 第一篇blog
date: 2018-05-04 23:38:11
tags: 生活
description: 附加一段文章摘要，字数最好在140字以内，会出现在meta的description里面
copyright: true
top: 12
---
```
# 18.修改字体大小
打开`\themes\next\source\css\ _variables\base.styl`文件，将`$font-size-base`改成`16px`，如下所示：
``` hash
$font-size-base            =16px
```
# 19.修改打赏字体不闪动
修改文件`next/source/css/_common/components/post/post-reward.styl`，然后注释其中的函数`wechat:hover`和`alipay:hover`，如下：
```hash
/* 注释文字闪动函数
 #wechat:hover p{
    animation: roll 0.1s infinite linear;
    -webkit-animation: roll 0.1s infinite linear;
    -moz-animation: roll 0.1s infinite linear;
}
 #alipay:hover p{
   animation: roll 0.1s infinite linear;
    -webkit-animation: roll 0.1s infinite linear;
    -moz-animation: roll 0.1s infinite linear;
}
*/
```
# 20.点击爆炸效果
跟那个红心是差不多的，首先在`themes/next/source/js/src`里面建一个叫f`ireworks.js`的文件，代码如下：
```hash
"use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)}"use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)};
```
打开`themes/next/layout/_layout.swig`,在`</body>`上面写下如下代码：
```hash
{% if theme.fireworks %}
   <canvas class="fireworks" style="position: fixed;left: 0;top: 0;z-index: 1; pointer-events: none;" ></canvas> 
   <script type="text/javascript" src="//cdn.bootcss.com/animejs/2.2.0/anime.min.js"></script> 
   <script type="text/javascript" src="/js/src/fireworks.js"></script>
{% endif %}


```
打开主题配置文件，在里面最后写下：
```hash
# Fireworks
fireworks: true
```
# 21.DaoVoice 在线联系
首先在 [daovoice](http://dashboard.daovoice.io/get-started?invite_code=0f81ff2f) 注册账号,[邀请码](http://dashboard.daovoice.io/get-started?invite_code=0f81ff2f)是`0f81ff2f`，注册完成后会得到一个 app_id :

```hash
daovoice('init', {
  app_id: "0000000"
});
daovoice('update');
```

记下这个 app_id的值，然后打开`/themes/next/layout/_partials/head.swig`,写下如下代码：
```hash

{% if theme.daovoice %}
  <script>
  (function(i,s,o,g,r,a,m){i["DaoVoiceObject"]=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;a.charset="utf-8";m.parentNode.insertBefore(a,m)})(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/0f81ff2f.js","daovoice")
  daovoice('init', {
      app_id: "{{theme.daovoice_app_id}}"
    });
  daovoice('update');
  </script>
{% endif %}

``````````````````````````````````````````````
{# #238, Disable Baidu tranformation #}
<meta http-equiv="Cache-Control" content="no-transform" />
<meta http-equiv="Cache-Control" content="no-siteapp" />


{% if theme.google_site_verification %}
  <meta name="google-site-verification" content="{{ theme.google_site_verification }}" />
{% endif %}

{% if theme.daovoice %}
  <script>
  (function(i,s,o,g,r,a,m){i["DaoVoiceObject"]=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;a.charset="utf-8";m.parentNode.insertBefore(a,m)})(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/0f81ff2f.js","daovoice")
  daovoice('init', {
      app_id: "{{theme.daovoice_app_id}}"
    });
  daovoice('update');
  </script>
{% endif %}
```
接着打开主题配置文件，在最后写下如下代码：
```hash
# Online contact 
daovoice: true
daovoice_app_id: 这里填你的刚才获得的 app_id
```
重新 `hexo g && hexo s` 就能看到效果了。
安装成功后可以在DaoVoice 控制台上的聊天设置里设置聊天窗口样式

# 22.添加跟帖(来必力)
有两种实现方法：
①更新next主题，因为最新版本的主题已经支持这种评论。直接在主题配置文件`_config.yml` 文件中添加如下配置:
```hash
gentie_productKey: #your-gentie-product-key
```
②如果你不想更新的话，那么按下面步骤进行：
首先，还是在主题配置文件`_config.yml` 文件中添加如下配置:
```hash
gentie_productKey: #your-gentie-product-key
```


# 23.给文章设置阅读量

Hexo 目录下的 \themes\next\ _config.yml 文件

```hash
# 增加不算子访问数量统计
busuanzi_count:
  # count values only if the other configs are false
  enable: true
  # custom uv span for the whole site
  site_uv: true
  site_uv_header: <i class="fa fa-user"></i> 访问人数
  site_uv_footer:
  # custom pv span for the whole site
  site_pv: true
  site_pv_header: <i class="fa fa-eye"></i> 访问总量
  site_pv_footer: 次
  # custom pv span for one page only
  page_pv: true
  page_pv_header: <i class="fa fa-file-o"></i> 浏览
  page_pv_footer: 次

```

