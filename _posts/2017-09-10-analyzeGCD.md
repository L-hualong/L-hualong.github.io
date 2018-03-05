---
layout:     post
title:      "一口一口吃掉GCD"
subtitle:   "从源码层面分析GCD的底层实现原理"
date:       2017-09-10 12:00:00
author:     "Marco Liu"
header-img: "img/post-bg-2015.jpg"
catalog:    true
tags:
    - IOS开发
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


