---
layout: post
title:  "Programming Starfields"
date:   2020-06-29 18:00:00
categories: Programming
---

A lot of people have seen starfields from [star wars](https://starwarsblog.starwars.com/wp-content/uploads/2020/04/star-wars-backgrounds-14.jpg) and it looks so cool. As one of my daily programming challenges, I decided to take it up.


Basic idea: 
1. Randomly generating 400 stars' `(x, y)` from range `[-200, 200]`.
2. Pick `z` value for each star from `[0, 200]`.
3. Draw `(x / z * 200, y / z * 200)` every few frames and update `z -= 1`.

Assume we picked `x = 10`, `y = 10`, `z = 100`:
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

This trick ensured that
- At the beginning, `(x, y)` is close to the origin.
- As time goes by, `(x, y)` speeds up when it is close to the edge.

This is how it looks like:
<div id="js-holder"></div>

The source code in [github](https://gist.github.com/wwei10/441f8d96d22dbe04679f205888918c33) and feel free to play with it [p5js editor](https://editor.p5js.org).

**Credit**
- Got original idea from [this awesome youtube video](https://www.youtube.com/watch?v=17WoOqgXsRM). 
- Got help from [this example](https://raw.githubusercontent.com/KevinWorkman/HappyCoding/gh-pages/examples/p5js/_posts/2018-07-04-fireworks.md) to inject javascript into jekyll post.


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

