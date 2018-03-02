---
layout:     post
title:      "为博客添加评论系统"
subtitle:   "在Jekyll博客添加评论系统：gitment篇"
date:       2017-12-02 12:00:00
author:     "Marco Liu"
header-img: "img/post-bg-2015.jpg"
catalog:    true
tags:
    - 生活
---

> “Yeah I'm here'. ”


## 前言

最近在Github Pages上使用Jekyll搭建了一个[个人博客](http://hualong.ink),当需要添加评论系统时，找了一下国内的几个第三方评论系统，如“多说”，“畅言”，“友言”，“新浪云跟贴”：

	1.多说，曾经较火的评论系统，网上介绍文章比较多，但已关闭，无法再用了

	2.畅言，sohu旗下的，但是需要个人博客备案后才能使用，但github pages个人博客感觉备案比较难

	3.友言，iaThis旗下的，由于时http请求，github pages现在都是https了， 在https站点无法调用http请求，故也无法使用

	4.网易云跟贴，曾被当作“多说”的替代品，可惜官方通报说也将在2017.08.01关闭了

再看看disqus，国外比较火的评论系统，但在国内墙了，故也不考虑。

综上，国内几个比较主流的评论系统目前都无法在Github Pages上的个人博客使用， 后面我发现了gitment，一款由国内大神imsun开发的基于github issues的评论系统, 具体介绍请看[项目主页]( github.com/imsun/gitment )。

目前我已经成功把gitment应用到了自己的Github pages个人博客里，过程中碰了一些壁, 同时发现网上关于gitment的介绍并不多，故写一篇总结，希望可以帮助到大家。

## 正文

---
##### 1.申请一个Github OAuth Application

Github头像下拉菜单 > Settings > 左边Developer settings下的OAuth Application > Register a new application，填写相关信息：

	1.Application name, Homepage URL, Application description 都可以随意填写

	2.Authorization callback URL 一定要写自己Github Pages的URL 
	(ps: 之前我自己就是在这里碰壁了，调试半天才发现，我填的是http://hualong.ink)
	
	3.填写完上述信息后按Register application按钮，得到Client ID和Client Secret


---
##### 2. 在jekyll博客调用gitment

如gitment项目页Readme所示，在你需要添加评论系统的地方，一般是`_layout/`目录下的 `post.html`, 添加一下代码(你可以直接复制粘贴)

```
<div id="gitmentContainer"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
var gitment = new Gitment({
    owner: 'Your GitHub username',
    repo: 'The repo to store comments',
    oauth: {
        client_id: 'Your client ID',
        client_secret: 'Your client secret',
    },
});
gitment.render('gitmentContainer');
</script>
```
**需要修改的有4个地方**

	Your GitHub username：填写你的Github Pages博客所在的github账户名

	The repo to store comments：填写用来存放评论的github仓库，由于评论是 通过issues来存放
的，个人建议这里可以直接填Github Pages个人博客所在的仓库

	Your client ID：第1步所申请到的应用的Client ID

	Your client secret：第1步所申请到的应用的Client Secret

填写完这4项把代码保存上传到github就可以了。

---
##### 3. 为每篇博文初始化评论系统

由于gitment的原理是为每一遍博文以其URL作为标识创建一个github issue， 对该篇博客的评论就是对这个issue的评论。因此，我们需要为每篇博文初始化一下评论系统， 初始化后，你可以在你的github上会创建相对应的issue。

接下来，介绍一下如何初始化评论系统(必须先push到远端仓库)

	1.上面第2步代码添加成功并上传后，你就可以在你的博文页下面看到一个评论框，还 有看到以下错误Error: Comments Not Initialized，提示该篇博文的评论系统还没初始化

	2.点击Login with GitHub后，使用自己的github账号登录后，就可以在上面错误信息处看到一个Initialize Comments的按钮 (ps: 由于要求回调URL和当前地址一样，故第2步不能在本地调试， 需把代码先上传再调试)

	3.点击Initialize Comments按钮后，就可以开始对该篇博文开始评论了， 同时也可以在对应的github仓库看到相应的issue

![](/img/in-post/jekyll/2017-11-19-jekyll-06.jpg)

---
##### 4. 一些问题


请大家一定要确认github oauth application里的回调URL一定要 填写要使用gitment的博客的URL，这一点十分重要

点击Initialize Comments后弹出窗口提示 Error：Validation Failed，说明 文件名过长，改短就行了


## 后记

如果你遇到了什么问题，可以在原文下方的评论里给我提问。
