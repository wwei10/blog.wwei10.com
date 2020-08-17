---
layout: post
title:  "如何用P5JS实现星空效果"
date:   2020-06-29 18:00:00
categories: Programming
---

星球大战影片开始和结束的时候都会有很酷的[星空效果](https://starwarsblog.starwars.com/wp-content/uploads/2020/04/star-wars-backgrounds-14.jpg) 最近想学学Javascript，于是就动脑筋解决一下如何用程序画出来星空图。


基本想法：
1. 随机生成400个星星的坐标 `(x, y)` x和y取值于 `[-200, 200]`
2. 对于每颗星星生成 `z`，范围也是`[0, 200]`
3. 对于每一帧中的每一个星星，在 `(x / z * 200, y / z * 200)`画一个点
4. 每一帧结束更新`z -= 1`

举个例子来方便讨论，假设初始值是 `x = 10`, `y = 10`, `z = 100`，那么之后十帧的数据是这样的：
- `z = 100` => `(20, 20)`
- `z = 99` => `(20.2, 20.2)`
- `z = 98` => `(20.4, 20.4)`
- `z = 90` => `(22.2, 22.2)`
- `z = 80` => `(25, 25)`
- `z = 50` => `(40, 40)`
- `z = 40` => `(50, 50)`
- `z = 30` => `(66, 66)`
- `z = 20` => `(100, 100)`
- `z = 10` => `(200, 200)`

这个方法的好处是，刚开始的时候每个星星靠近原点，随着时间流逝，星星加速向外运动。

<p class="codepen" data-height="493" data-theme-id="light" data-default-tab="js,result" data-user="wwei10" data-slug-hash="ZEWOGpm" style="height: 493px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Starfield">
  <span>See the Pen <a href="https://codepen.io/wwei10/pen/ZEWOGpm">
  Starfield</a> by Wei (<a href="https://codepen.io/wwei10">@wwei10</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

源代码在上面codepen link里。想玩的话可以复制粘贴到[p5js editor](https://editor.p5js.org)或者codepen里面玩一玩。

**鸣谢**
-  这个[youtube](https://www.youtube.com/watch?v=17WoOqgXsRM)视频提到了这个coding challenge。推荐一下这个频道，经常会有很有趣的coding challenge和idea。
- 从[这个例子](https://raw.githubusercontent.com/KevinWorkman/HappyCoding/gh-pages/examples/p5js/_posts/2018-07-04-fireworks.md) 学习了如何在jekyll博客里插入JS。**更新：后来改用codepen.io的嵌入js代码，感觉更方便。因为一个p5js有不少global的东西，如果想在一个页面上加载两个p5js画布需要修改代码。**


**附录：用p5js画Lorenz System**

再贴一个[lorenz system](https://en.wikipedia.org/wiki/Lorenz_system)的图，也挺漂亮的！我这里选择的参数是蝴蝶效应对应的那个图。微小的起始量的改动也会极大的影响后续走势。一开始红蓝两线重合，后来就分叉了。这也可以作为1% rule的解读，每天如果进步一点点，长久以来的优势积累是巨大的。

<p class="codepen" data-height="500" data-theme-id="light" data-default-tab="js,result" data-user="wwei10" data-slug-hash="WNwxvxd" style="height: 500px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CodePen JavaScript Console Template">
  <span>See the Pen <a href="https://codepen.io/wwei10/pen/WNwxvxd">
  CodePen JavaScript Console Template</a> by Wei (<a href="https://codepen.io/wwei10">@wwei10</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

