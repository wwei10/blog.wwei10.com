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



<div id="js-holder"></div>

源代码在 [github](https://gist.github.com/wwei10/441f8d96d22dbe04679f205888918c33)。可以复制粘贴到 [p5js editor](https://editor.p5js.org)里面玩一玩。

**鸣谢**
-  这个[youtube](https://www.youtube.com/watch?v=17WoOqgXsRM)视频提到了这个coding challenge。推荐一下这个频道，经常会有很有趣的coding challenge和idea。
- 从[这个例子](https://raw.githubusercontent.com/KevinWorkman/HappyCoding/gh-pages/examples/p5js/_posts/2018-07-04-fireworks.md) 学习了如何在jekyll博客里插入JS。


<script src="https://cdn.jsdelivr.net/npm/p5@1.0.0/lib/p5.js"></script>
<script>
let stars = [];
let height = 200;
let width = 200;

class Star {
  constructor(x, y) {
    this.x = random(-width, width);
    this.y = random(-height, height);
    this.z = random(0, width);
  }

  update() {
    this.z -= 1;
    if (this.z < 1) {
      this.x = random(-width, width);
      this.y = random(-height, height);
      this.z = random(1, width);
    }
  }

  show() {
    var x1 = map(this.x / this.z, -1, 1, 0, 2 * width);
    var y1 = map(this.y / this.z, -1, 1, 0, 2 * height);
    // ellipse(x1, y1, 3 + width / this.z / 2, 3 + width / this.z / 2);
    var x2 = map(this.x / (this.z + 20), -1, 1, 0, 2 * width);
    var y2 = map(this.y / (this.z + 20), -1, 1, 0, 2 * height);
    stroke(255);
    line(x1, y1, x2, y2);
  }
}

function setup() {
  const canvas = createCanvas(width * 2, height * 2);
  canvas.parent('js-holder');
  for (var i = 0; i < 400; i++) {
    stars.push(new Star());
  }
}

function draw() {
  background(0);
  for (var i = 0; i < stars.length; i++) {
    stars[i].show();
    stars[i].update();
  }
}
</script>

