+++
title = "Rust is tricky"
description = "4/??? The browser stuff is way less complex than learning rust!"
date = 2021-12-03
draft = false
slug = "tricky-rust"

[taxonomies]
categories = [""]
tags = ["rust", "learning"]

[extra]
comments = true
+++

[Everything you're hearing now, contributes to turn you into a robot.](https://open.spotify.com/album/1pRQnDjYshduiknpZpWrPc?si=7kgyXPDUQo6LNIcEDAg5lg)

I start this day as I started lots of other days: lurking on twitter. Where I found a very interesting post about ["Hammock driven development"](https://stokoe.me/summary-hammock-driven-development/) and when I saw it was taken out of a Rich Hickey's talk I was kinda sold. Interesting approach and it quotes books I have on my shelf and didn't read yet. A small summary: flood your brain with good input when you have to solve a difficult problem and then just rest on the idea, you'll do better.

So here are some facts about Rust macros:
* you use them to the detriment of functions when you want to metaprogram. I imagine it's how they substitute templating here;
* they're more complex than function impls.
* Another important difference between macros and functions is that you must define macros or bring them into scope before you call them in a file, as opposed to functions you can define anywhere and call anywhere. (I don't reallly get why)
* the `derive` attribute?
* declarative macros seem to work kinda like a function that has some checks + a python map
* pattern syntax (Chapter 18) is the thing they use to make the checks, it looks real ugly
Oh jesus christ, this is just one type of macroing, but there's a whole universe. I'll stop here now, though.

I learned about:
* Macros
* Getting args on the command line
* Do some string operations

What I need to learn:
* Pointers? How does memory work on Rust? At the same time is somewhat similar to C++ I remember Karen saying it has some tricky things. I feel like I'm writing lots of shit code cause I don't understand this, so maybe this is next step for tomorrow
    The difference between `expected struct `String`, found `&str``, this kind of stuff
* Also don't know anything about naming patterns, looks like Python from the little I saw