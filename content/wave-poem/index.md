+++
title = "A wave and a poem"
description = "5/??? p5js and html tags"
date = 2021-12-04
draft = false
slug = "wave-poem"

[taxonomies]
categories = ["journal"]
tags = ["art"]

[extra]
comments = true
+++

Today was a less of a tech day. Didn't have the time to do anything really, but decided to very briefly play around with p5js. I was trying to create an ocean, but all I got was this:

![wave](wave.gif)

Here's the code from it:

```
let t = 0; // time variable

function setup() {
  createCanvas(600, 600);
  stroke(color(0, 0, 255));
  strokeWeight(13);
  fill(40, 200, 40);
}

function draw() {
  background(10, 10); // translucent background (creates trails)

  // make a x and y grid of ellipses
  for (let x = 0; x <= width; x = x + 30) {
    for (let y = 0; y <= height; y = y + 30) {
      // starting point of each circle depends on mouse position
      const xAngle = map(mouseX, 0, width, -1 * PI, 4 * PI, true);
      const yAngle = map(mouseY, 0, height, -4 * PI, 4 * PI, true);
      // and also varies based on the particle's location
      const angle = xAngle * (x / width) + yAngle * (y / height);

      // each particle moves in a circle
      const myX = x + 20 * cos(2 * PI * t + angle);
      const myY = y + 20 * sin(2 * PI * t + angle);

      ellipse(myX, myY, 10); // draw particle
    }
  }

  t = t + 0.01; // update time
}
```

Coming mainly from an already made example on the docs.

I'll also come up with bad poetry on the go:

```
a dis<figure>d <head>
hanging by a <thread>
makes your title tab looks like shit
```

That's it for today.