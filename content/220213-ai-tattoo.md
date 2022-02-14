+++
title = "First steps in GAN"
description = "21/??? Mixing some loves, computer science, tattooing, art!"
date = 2022-02-13
draft = false
slug = ""

[taxonomies]
categories = ["journal"]
tags = ["ai", "python", "art", "tattoo", "love"]

[extra]
comments = true
+++

I should be studying C++ but since it's Sunday I decided to procrastinate on this new pet project I came up with.

Through David, I found some interesting inspiration to generate cool AI tattoos. [Here](https://www.instagram.com/p/CZ8eajTrLg5/)'s an example.

I've contacted the dude who created this dataset to learn more about the configurations he used, if he pre-trained stuff, etc. I've compiled a [gist](https://gist.github.com/marimeireles/b976fdde03982deb54bffefc8389f5f2) with initial pointers to this.

First, I'd like to understand what's the difference between some random collabs that I found and the original one. Some of them claim to be better to train your own model on top.

After running a test in a Stylegan3+CLIP notebook I reached the conclusion (yes, after one experiment, they take too long!) that the model is the soul of this stuff. I reached out to the dude who created the inspirational thing, maybe he still has this dataset somewhere and would be up to giving it to me. Anyways, I think I'll start to create my own.

Okay, so creating a model seems like a bit crazy. Ideally I'd find a drawing model.

Then, I'd fine tune it with my own stuff that I'll scrape and/or the dude will share with me.

Apparently, all you have to do for that is using the `--resume` flag. [This](https://colab.research.google.com/drive/1Nal3M-wjv6BeIgyTgvxhaccPFGEP6cbk?usp=sharing#scrollTo=00S7pPSdhZ5g) colab is pretty straight forward.

I think next step is getting the data.