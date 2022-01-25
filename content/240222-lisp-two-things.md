+++
title = "Lisp has only two things?"
description = "16/??? Is this confusing or am I confused?"
date = 2022-01-24
draft = false
slug = "lisp-two-things"

[taxonomies]
categories = ["journal"]
tags = ["learning", "lisp"]

[extra]
comments = true
+++

#### My baby functional programming

So here's a first interesting concept on Rasket: `let`, it's a way of defining a list of functions. The content of `let` dies after the lifetime of the `define` dies.

But how does lifetime work?
As soon as there is no references/pointers to an object it's garbage.
Talking about scope life times, same as C, `let` dies after the parenthesis that created it were close.

```
(define (checker p1 p2)
  (let ([p12 (hc-append p1 p2)]
        [p21 (hc-append p2 p1)])
    (vc-append p12 p21)))

(checker (colorize (square 10) "red")
           (colorize (square 10) "black"))
```

There are two things in a lisp language. Atoms and lists of symbolic expressions.

The way the lang. works is by using Eval, which eval things recursively:
    You evaluate a symbol by returning its value.
    Any other atom returns itself.
    If you quote something before whatever it will return itself.
    The way forms are evaluated is by recursevily evaluating what's inside procedures, and then to atoms:

    ```
    (define x 10)
    circle ( + 50 x)
    ```

    This evaluate circle to a process(? forgot the name), + to a process, 50 to itself, x to the value in the scope.

Lisp has only two things?
Now I'm confused about what I wrote. I should check the board before the explanation is gone.

#### Errands

Here's a random thing I thought about: people are constantly full of bullshit about their CC opinions. And I'm not coming from "I'm right they're wrong cause they disagree with me" is more like a "why and the answer doesn't make the least sense". The problem with this notion takes a while to develope because you need general broad understanding of CC things. Not that I have this completely, but I can see it in some things, that I remember a few years ago I'd just acce[t blindly. So, this is interesting.

Woot! Found the beginning of a solution for a problem at work. This feels nice. :) Now I'm using another lib, way simpler! `exprtk`. I seriously considered writing my own, a generic simple one, but seems like this one is making a very good job. Maybe I'll still do it, to have something small and exclusive to deal with boolean logic. I don't think it's particularly an interesting problem, but I could make it really nice with some templating and stuff, so maybe I can actually learn this for real.

#### Rhino workshop:

3D models can be found:

* 3dhouse
* 3dwarehouse

[extrude surface] -> turns it into a 3D model

P.S.: Never go to a class without an objective.