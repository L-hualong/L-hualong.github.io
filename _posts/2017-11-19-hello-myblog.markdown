---
layout:     post
title:      "Welcome to Marco Blog"
subtitle:   "个人博客搭建（Jekyll + Github Page + 域名关联）"
date:       2017-11-19 12:00:00
author:     "Marco Liu"
header-img: "img/post-bg-2015.jpg"
tags:
    - 生活
    - IOS开发
---

> “Yeah It's on. ”


## 前言

Marco Liu 的 Blog 就这么开通了。

[跳过废话，直接看技术实现 ](#介绍)


2017年，Marco 总算有个地方可以好好写点东西了。


作为一个程序员， Blog 这种轮子要是挂在大众博客程序上就太没意思了。一是觉得大部分 Blog 服务都太丑，二是觉得不能随便定制不好玩。之前因为太懒没有折腾，结果就一直连个写 Blog 的地儿都没有。

在秋招结束过后，已经刷过许许多多的笔试题和面试题的我，脑海中早已形成了一个自己的知识体系，再加上拿到offer的兴奋感，于是乎就有了开博客写技术总结和生活总结的冲动。一不做二不休，不如花一天时间搞一个吧！


<p id = "build"></p>
---

## 正文
接下来说说搭建这个博客的技术细节。  
* 开始
	* [介绍](#介绍)
	* [环境要求](#环境要求)
	* [环境配置](#环境配置)
* 博客搭建
	* [创建博客](#创建博客)
	* [目录结构](#目录结构)
	* [部署博客](#部署博客)
	* [绑定域名](#绑定域名)
* 博客主题
	* [博客主题](#博客主题)
	* [配置博客](#配置博客) 

---
###### 介绍

[Jekyll 中文文档](http://jekyll.bootcss.com/)、[Jekyll 英文文档](https://jekyllrb.com/)、[Jekyll 主题列表](http://jekyllthemes.org/)。

Jekyll 是一个简单的博客形态的静态站点生产机器，也可以说是一个模版引擎。
它有一个模版目录，其中包含原始文本格式的文档，通过 Markdown （或者 Textile） 以及 [Liquid](http://www.jianshu.com/p/b11bc7b3306c) 转化成一个完整的可发布的静态网站。

你可以发布在任何你喜爱的服务器上。Jekyll 也可以运行在 GitHub Page 上，也就是说，你可以使用 GitHub 的服务来搭建你的项目页面、博客或者网站，而且是完全免费的，需要自定义域名，也只需要简单改改 DNS 加个 CNAME 就好了。

---
###### 环境要求

使用 Jekyll 搭建博客之前要确认下本机环境
```
Git环境（用于部署到远端）
Ruby环境（Jekyll是基于Ruby开发的）、包管理器RubyGems
Jekyll 。
```
如果你是 Mac 用户，你就需要安装 Xcode 和 Command-Line Tools了。下载方式 Preferences → Downloads → Components。或者在终端输入   **$  xcode-select --install**

---
###### 环境配置

安装 jekyll
```
// 安装 HomeBrew (如已安装HomeBrew 无需执行此行)
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
// 安装 Git
$ brew install git   
// 安装 Ruby (此处需安装一个新的ruby版本，Mac系统自带ruby库无法安装jekyll)
$ brew install ruby  
// 安装 jekyll
$ gem install jekyll
// 安装 bundle
$ bundle install
```

---
###### 创建博客
```    
$ jekyll new myBlog 

$ cd myBlog 

$ jekyll serve
//还可以输入`jekyll serve --watch`，这样可以边修改边自动运行修改后的文件。

//如果执行上述命令报错了，请根据错误提示自行谷歌，多半是跟没装bundle有关。
```
在浏览器里输入： [http://localhost:4000]( http://localhost:4000)  就可以看到你的博客效果了。
![](/img/in-post/jekyll/2017-11-19-jekyll-02.jpg)

---
###### 目录结构

Jekyll 的核心其实是一个文本转换引擎。它的概念其实就是： 你用你最喜欢的标记语言来写文章，可以是 Markdown，也可以是 Textile,或者就是简单的 HTML, 然后 Jekyll 就会帮你套入一个或一系列的布局中。在整个过程中你可以设置URL路径, 你的文本在布局中的显示样式等等。这些都可以通过纯文本编辑来实现，最终生成的静态页面就是你的成品了。

一个基本的 Jekyll 网站的目录结构一般是像这样的：
```
├── _config.yml
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   ├── post.html
|   └── page.html
├── _posts
|   └── 2016-10-08-welcome-to-jekyll.markdown
├── _sass
|   ├── _base.scss
|   ├── _layout.scss
|   └── _syntax-highlighting.scss
├── about.md
├── css
|   └── main.scss
├── feed.xml
└── index.html
```

这些目录结构以及具体的作用可以参考 [官网文档](http://jekyll.com.cn/docs/structure/) 

---
###### 部署博客
我这里讲的是部署到 Github Page 上。其他服务器请自行搜索 🔍  。

**1.创建 Github**
<br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先你需要注册一个 Github 账号，注意 username，这会影响到你的域名， 你的域名将会是 username.github.io ，所以认真的取个名字吧。

**2.创建仓库**
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Respository name 中的 username.github.io 的 username 
一定与前面的 Owner 一致
![](/img/in-post/jekyll/2017-11-19-jekyll-01.png)

**2.3.部署博客到刚创建仓库**
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;创建好了之后，把刚才建立的 myBlog 项目 push 到 username.github.io仓库里去（username指的是你的github用户名），检查你远端仓库已经跟你本地 myBlog 同步了，然后你在浏览器里输入 http://username.github.io ，就可以访问你的博客了。

---
###### 绑定域名

**1.购买域名**
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;买买买，大家都会吧，登录[万网](https://wanwang.aliyun.com/?utm_medium=text)可以进行域名购买。

**1.CNAME 绑定域名**
<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;默认使用阿里云提供的万网 DNS 服务器，在域名解析中，A 记录就是直接指定一个 IP，CNAME 就是重命名，指向另一个域名。
![](/img/in-post/jekyll/2017-11-19-jekyll-03.jpg)
```
添加以下解析

记录类型：CNAME
主机记录：www
解析线路：默认
记录值是：username.github.io.

记录类型：CNAME
主机记录：@
解析线路：默认
记录值是：username.github.io.

//在这里千万不要忘记记录值中 .io 后面还有一个点 .  ！！！！！
```
至此你的域名可以被访问。

---
###### 博客主题

虽然博客部署完成了，你会发现博客太简单不是你想要的，如果你喜欢我的模板的话，可以使用我的模板。

首先你要获取的我博客，[Github项目地址](https://github.com/L-hualong/L-hualong.github.io)，你可以直接[点击下载博客](https://github.com/L-hualong/L-hualong.github.io/archive/master.zip)，进去L-hualong.github.io/ 目录下， 使用命令部署本地服务，然后push到你在GitHub上的username.github.io仓库里去，检查远端仓库是否与本地仓库同步，最后你在浏览器里输入 username.github.io ，就可以看到你新的博客模版了。

在此过程可能会遇到一些错误，[点击查看解决办法](http://blog.csdn.net/wyc12306/article/details/51504885)

在 `_config.yml`文件中把下图中url修改为“http://username.github.io”
![](/img/in-post/jekyll/2017-11-19-jekyll-04.jpg)

在 `_config.yml`文件中把"gems: [jekyll-paginate]"修改为“plugins: [jekyll-paginate]”
![](/img/in-post/jekyll/2017-11-19-jekyll-05.jpg)

重新在浏览器里输入 username.github.io，查看博客是否可以正常显示。

---
###### 配置博客

>* 如果你想使用我的模板请把 _posts/ 目录下的文章都去掉。
>*  修改 _config.yml 文件里面的内容为你自己的。

详细的配置教程和博客撰写教程可以点击 [这里](https://github.com/L-hualong/L-hualong.github.io/blob/master/_posts/blogConfig.md)

## 后记

使用了 Jekyll 你会发现如果你想使用多台电脑发博客都很方便，只要把远端 github 仓库里的博客 clone 下来，写文章后再提交就可以了，Hexo 由于远端提交的是静态网页，所有无法直接写 Markdown 的文章。如果你想看 Hexo 搭建博客，可以看看这一篇[HEXO搭建个人博客](http://baixin.io/2015/08/HEXO%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)的教程。

如果你在搭建博客遇到问题，可以在原文下方的评论里给我提问。

#### 鸣谢

感谢各位大牛的开源贡献，文章部分材料摘自以下网站
<br>
<br>
[詹瞻要去哪](http://errnull.top/2016/05/PersonalBlogTourist/)
<br>
[潘柏信博客](http://baixin.io/2016/10/jekyll_tutorials1/)
<br>
[黄玄的博客](http://huangxuan.me)
<br>
[创建个人博客详细流程](https://www.jianshu.com/p/6aec3347b0ea)

—— Marco 后记于 2017.11




