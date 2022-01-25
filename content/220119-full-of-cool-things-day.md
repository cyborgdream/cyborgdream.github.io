+++
title = "All days are full, but some days are fuller than others"
description = "11/??? Or how to use your brain for 16h"
date = 2022-01-19
draft = false
slug = "full-of-cool-things-day"

[taxonomies]
categories = ["journal"]
tags = ["learning", "art", "electronics"]

[extra]
comments = true
+++

#### Deving a kernel with loops with Qt

I'm having an `ld: library not found for -lxcanvas`, I think it might be trying to link Qt's canvas lib. Why this is not available on macos through the conda package is a mistery, but I see two solutions to this, download it from brew or not include it...

Seems like this is the problem. Honestly, don't know what's the less annoying solution. Just exporting the CMAKE var did the trick: `export CMAKE_PREFIX_PATH=/usr/local/opt/qt5/`. The problem is, still not working. I'm in clueless territory. It's not a Qt thing? What is it then.

It was something from a dark lib that a coworker created! And I just had to include it on the CMAKE. About 2,5h of suffering could have been avoided if I had asked for help earlier 🐉.

#### Starting up

Just brainstormed some ideas to make a vape with a friend I made here and save people from nicotine adiction. Let's see! Hope to be able to develop it on the open, including the hardware. I miss creating physical little electronic things like in the good ol days. I might add some electronic insights to this journal in the future.

#### Stencil making and street art

Hiquain(ask vanessa the name) tribe see the patterns of the boa snake as essential, they do it every where, every day.
When you see a piece of art, think about how the artist made that.
e.g How many layers are they using?

* Vaporwave
* Tattoo style stencils
* Carol's style

Illustrator, Photoshop -> .ai, .pdf (it has to be vectorized)
- it's better to work with PS and then use the automatic tool from Illustrator or InkScape to vectorize it for you.

You can take photos and make stencils out of it.

For beginners, you should try to go fast with the can.

(I wanna make a frog for Ba Sin Se.)

How African societies have more fractal patterns, and how it might be related to recursivity and the philosophies of circle of life. What's the name of the book? Ask Vanessa.

Iuroba -> Iemanja goddness, it's African culture that's very intertwined with Brazilian culture.

* Islamic Design, A genius for geometry


#### AI art

Played around a bit with GPT3. Interesting technology. I'm interested in testing my own model with my own sets to maybe generate some cool tattoos to do it on people in Mars. I have to learn about how to use these technologies with little data. Gene is marginally interested in that, we might be able to explore it together in the following classes. Zane might be interested too.
