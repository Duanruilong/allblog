---
title: 使用Hexo + Github搭建个人博客详细配置
date: 2018-05-05 19:02:51
tags: 生活
description: 
copyright: true
categories: Hexo
---

开始使用Hexo + Github去搭建一个属于自己的博客站点，可以在这里发布一下自己的想法，一些问题积累，一些发现。


![js](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/hexo_d.png)

<!-- more -->

# 新建文章

`hexo new '文章标题'`

# 生成静态网页&部署

` hexo clean ` 清除缓存
` hexo g ` 生成静态网页
` hexo d  ` 开始部署

连接写法：` hexo c && hexo g && hexo d`

# 开启评论
1.我使用多说代替自带的评论，在[多说](http://duoshuo.com/) 网站注册 > 后台管理 > 添加新站点 > 工具 === 复制通用代码 里面有 short_name

在根目录 _config.yml 添加一行 disqus_shortname: jslite 是在多说注册时产生的

复制到 themes\landscape\layout\_partial\article.ejs
把
```h
<% if (!index && post.comments && config.disqus_shortname){ %>
<section id="comments">
<div id="disqus_thread">
  <noscript>Please enable JavaScript to view the <a href="//disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
</div>
</section>
<% } %>

```
改为
```h
<% if (!index && post.comments && config.disqus_shortname){ %>
  <section id="comments">
    <!-- 多说评论框 start -->
    <div class="ds-thread" data-thread-key="<%= post.layout %>-<%= post.slug %>" data-title="<%= post.title %>" data-url="<%= page.permalink %>"></div>
    <!-- 多说评论框 end -->
    <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
    <script type="text/javascript">
    var duoshuoQuery = {short_name:'<%= config.disqus_shortname %>'};
      (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] 
         || document.getElementsByTagName('body')[0]).appendChild(ds);
      })();
      </script>
    <!-- 多说公共JS代码 end -->
  </section>
<% } %>
```


# hexo相处里的错误

## 部署没有权限

```h
[master 74ddc79] Site updated: 2018-07-22 23:14:27
 1 file changed, 1 insertion(+), 1 deletion(-)
Warning: Permanently added the RSA host key for IP address '13.250.177.223' to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: Warning: Permanently added the RSA host key for IP address '13.250.177.223' to the list of known hosts.
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

    at ChildProcess.<anonymous> (/Users/duanruilong/Github/hexo/node_modules/hexo-util/lib/spawn.js:37:17)
    at emitTwo (events.js:106:13)
    at ChildProcess.emit (events.js:191:7)
    at maybeClose (internal/child_process.js:877:16)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:226:5)


```
查看权限

```h

➜  ~ ssh -T git@github.com
git@github.com: Permission denied (publickey).

<!--就是没有权限 -->
```

### 接下来这样来

#### 1、检查是否有SSH key

- 登陆github，点击头像位置处 Settings ——> SSH and GPG keys ，查看是否有SSH keys。如果有，直接进行第三步。

#### 2、新建 SSH key，在git shell(或者git bash等命令窗口)，注意大小写：

`ssh-keygen -t rsa -C "邮箱名"`

然后会出现：
```h
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/dell/.ssh/id_rsa):

直接回车就可以。
然后会出现：

Enter passphrase (empty for no passphrase):
Enter same passphrase again:

要求你输入密码，这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入。这个设置是防止别人往你的项目里提交内容。

注意：输入密码的时候没有*字样的，直接输入就好。
然后会出现：

Your identification has been saved in /c/Users/dell/.ssh/id_rsa.
Your public key has been saved in /c/Users/dell/.ssh/id_rsa.pub.
The key fingerprint is:
65:69:······02:4b emailname@email.com
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|       .   o .   |
|    . o o = o    |
|   . o * = o     |
|  E  o + o .     |
| . o.   . .      |
|     ..          |
+-----------------+

至此，密钥已经成功生成。

```

#### 3、接下来在github上添加SSH key：

- ① 打开本地文件：id_rsa.pub（文件路径可以在上一步SSH生成成功后看到路径，比如我的是/Users/duanruilong/.ssh/id_rsa.pub），可以将这个文件在编辑器中打开，然后全选复制。

- ② 登陆github，点击头像位置处 Settings ——> SSH and GPG keys ——> New SSH key，点击新建SSH key。

- ③ 将 ① 中复制的内容粘贴在key文本框里，title可以不用填（或者自己起一个名字也可以）。

#### 4、测试设置是否成功

`ssh -T git@github.com`

有可能会出现：

```h

    The authenticity of host 'github.com (192.30.252.1)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)?

    或者是

    The authenticity of host 'github.com (192.30.252.1)' can't be established.
    RSA key fingerprint is nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no)?

    这种情况下，直接 yes 回车

    然后会出现（也可能在 ++$ ssh -T git@github.com++ 之后，直接出现的就是这个，我就是这样~）：

    Hi username! You've successfully authenticated, but GitHub does not 
    provide shell access.

```

#### 5、设置用户信息

`~ git config --global user.name`
`~ git config --global user.email`

#### 6、然后就可以部署你的博客

`hexo g  && hexo d`
