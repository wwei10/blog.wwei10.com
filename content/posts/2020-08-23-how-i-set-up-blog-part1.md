---
title: "搭建博客 - 从Jekyll到Gin"
date: 2020-08-23 14:00:00
---

这两天自己还是想更端到端的了解网站，学习下更多前端后端，网络，运维知识，于是就开始琢磨自己怎么搭建个网站。

<!--more-->

## Jekyll -> ?

静态网站好处多多，我之前的[博客](https://github.com/wwei10/wwei10.github.io)就是在github pages上host的，设置总的来说很简单：

1. 轻松支持custom domains，我可以在google domains + github里设置好DNS的A和CNAME记录。
2. 所有博客写在markdown里就行，jekyll会把markdown翻译成html。直接在markdown里写html，javascript也是支持的，连syntax higlighting都有，体验很不错。
3. 部署简单，写完markdown，git add，git commit，git push之后github自动用jekyll生成静态网站。

这些优势同时也就代表着很多领域上的劣势。

1. 没法login，没法生成个性化内容，没法处理带参数的GET和带表单的POST请求，
2. 初始建站成本比较高，需要自己买服务器，搞数据库。时间成本也相对多一些。

但鉴于我比较想全面体验下做网站做app的感觉，把自己的个人主页从Jekyll迁到能动态处理请求的框架应该还是值得的。

我大概考虑下面几个框架，如下图。最终基于自己对golang的兴趣（虽然对Rust兴趣最高[1]），相对熟悉golang开发，性能高等优势，决定入了golang [Gin](https://gin-gonic.com)框架的坑。

![Web Frameworks](/images/web-frameworks.png)


## Jekyll -> Gin!

我大概花了一天时间就把博客从Jekyll迁移到Gin了，进度比我预想的要快不少。

我的迁移之路大概是这个流程：把框架搭起来，把部署E2E搞一遍，之后把最简单的about页面渲染出来，之后是把index页面渲染自己最近所有博客，再然后是把每一个博客都渲染出来，最后收个尾保证之前Jekyll网站里有的东西新的框架都能支持。

### 1/ 熟悉框架，决定Host

跟着Gin的Quickstart流程，把最简单的index.html跑起来。试了下google app engine和digital ocean的droplet。GAE部署挺方便的，但是感觉没有digital ocean那么flexible（毕竟一个PaaS，另一个IaaS）于是还是决定用digital ocean了。

搞了个droplet试着部署[2]了下自己的简易网站。试着用ip加端口访问网站，能访问到，第一个milestone达成。

### 2/ About页面 - 学习HTML/Template

首先是把最简单的[关于](https://wwei10.com/about)页面渲染出来。这里面的坑是我不是很熟悉Golang里面的html/template库，这个库和Jekyll用的Liquid对应。看了不少材料[3]，终于把Golang里面的template搞清楚了，关于页面渲染出来了，第二个milestone解决了！

### 3/ 生成信息流和渲染每篇博客

这里要做一个比较大的决定，使用数据库还是和原来一样用git存markdown format的博客。

选项1: 继续用yaml + markdown形式的博客，在框架内parse markdown的各种metadata，把内容渲染成html。

选项2: 使用数据库，把博客的metadata，内容都放到数据库里面。

选项1简单，work少，便宜但以后每次写博客还要手动登陆到host上面git pull然后更新博客，维护成本更高。我觉得还是先简单点好，于是选择了选项1.

一旦确定，就开始干活儿了。主要就两部分的活儿：

1. 写了个比较简易的parser可以把markdown文件里面的yaml metadata部分取出再把markdown里面内容部分取出。
2. 找了下markdown to html的库，Golang里面的话blackfriday github星星数量最多，就决定用那个了。总的来说挺顺利。中间也有一些坑，比如template.URL template.HTML template.JS有不同的escape规则，不同代码要用不同的template。

### 5/ 收尾

- [X] 把网站迁到https ([digital ocean](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04)资源很多，这个是挺好的参考)
- [X] 重启Discourse评论区功能，搞不定第一次访问博客自动创建discourse topic这个功能。于是以后都要手动创建topic然后在markdown里写清楚这个博客对应哪个discourse讨论。
- [X] [www.wwei10.com](www.wwei10.com) 的流量转到[wwwei10.com](https://wwei10.com)，这种活儿其实有的时候比编程耗时间多了。


## 总结

这次建站体验还不错，时间比我预想的短不少，下一周继续玩玩这个网站。

- [X] Code syntax highlighting还不支持，需要再研究下怎么搞。可能是在blackfriday里加个renderer也有可能会用Javascript解决问题。
- [X] 之后还想再试试react js，实现个markdown editor功能，方便在网页里编程。

----

## 附录

**[1]** 最近对Rust学习热情高涨（更有过于之前那阵子对Swift的），programming rust这本书读了五章了，被Rust的类型检查，错误处理，以及对并行编程的支持所吸引。感觉C++确实有的时候有很多很难debug的情景，Rust能在编译时就把这些bug检查出来确实是造福了程序员了。

**[2]** 说是部署其实就是直接在host上面go run main.go。

**[3]** [Multi Template](https://github.com/gin-contrib/multitemplate)这个网页比较好的介绍了如何在Gin里面用多个模版渲染。