---
title: 服务器端渲染 vs 客户端渲染
date: 2020-09-15
slug: csr-vs-ssr
---

之前把博客从静态网页改到了服务器端渲染（SSR），最近又抽空体验了一把客户端渲染（CSR），有了点心得感悟记录一下。

<!--more-->

术语：

1. CSR：client side rendering。客户端端渲染。代表有react。
2. SSR：server side rendering。服务器端渲染。代表有django。
3. SSG：static site generator。静态网页生成器，提前准备好静态网页，直接返回给客户端。代表有jekyll，hugo。

- SEO: search engine optimization。搜索引擎优化。

## 服务器端渲染

在做SSR的时候，总感觉自己重新写了遍静态网页生成器（jekyll/hugo），只不过不是完全静态的：

1. 准备好各种网页的布局，模版。
2. 把博客以markdown格式存在posts文件夹中，然后遍历文件，提取各种信息。
3. 把每个博客的内容用blackfriday从markdown格式转到html，然后放到模版。
4. 把准备好的HTML返回给客户。

### 客户端渲染

CSR是个完全不一样的体验，对我来说挺新鲜的。市面上有三种框架：

1. React.js
2. Angular.js
3. Vue.js

本着对与我司的信任，入了React.js的坑，create-react-app挺好用，一个命令就建立好了一个react app。把之前golang的html template转成JSX挺简单的。我觉得React JSX开发体验比html template要好。可以在JSX里面写javascript处理中间结果，html template支持的指令就少了不少。如果设置react app的时候用了typescript的话，typescript也能静态编译差错误，大大提高了效率。

对CSR的新鲜感驱使我把博客完全迁移到了react。在react上面两周了，盘点下CSR的一些坑。

### 1/ 搜索引擎优化

用google搜索`wwei10.com 如何快速处理CSV文件`时，搜索结果非常靠后。按理说Google的爬虫是会等Javascript取数据渲染页面的，用google search console看了下原因，是因为我的标准链接（canonical url）都设成了wwei10.com。一个解决方案就是在网页模版里要设一下`<link rel="canonical" href="https://wwei10.com/posts/how-to-processing-csv-efficiently" />`。

网上的其他[文章](https://rubygarage.org/blog/seo-for-react-websites)还提到各种其他问题，比如Google bot遇到CSR渲染更慢更可能遇到超时，遇到JS错误整个网页基本也就废了等等。


### 2/ 加载速度

毕竟多了一个javascript call，加载速度比SSR还是要慢不少。对于博客这种很静态的网页，不是很能忍。


上面这两个弊端也是老生常谈的问题了，不少人推荐Next.js这个混合了SSG，SSR和CSR的框架，能比较好的解决上面两个弊端，但近期不想折腾博客的实现上面了，打算好好想想写什么内容。有好内容，用github issues都能写博客。Next.js还是等有合适的side project的时候试试看。如果网站既有静态的部分又有很动态的人机交互和个性化内容，那用next.js会挺不错的。当然了，还有一种设计是静态的东西提出来用SSG做，动态的东西提出来用CSR或者SSR，这样就不会被next.js这一个框架所束缚了。

## 写在最后

对于静态的不能再静态的博客，最终我选择了回归了纯静态网页。杀鸡就不用牛刀了。用回了静态网页生成器Hugo就不瞎折腾了。顺带提一句，github支持jekyll比较好，hugo的话会麻烦些，图省事儿的话netlify是个很好的选择。