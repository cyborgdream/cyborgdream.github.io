+++
title = "Fun, productive code week!"
description = "22/??? Got a lot of creative coding done and it feels really great!"
date = 2022-02-20
draft = false
slug = "fun-code"

[taxonomies]
categories = ["journal"]
tags = ["ai", "python", "art", "tattoo", "community", "web-dev"]

[extra]
comments = true
+++

Big progress on my machine learning thing. Colab is super annoying, but, things are progressing.

I fixed the code for downloading stuff from [wikiart](https://github.com/marimeireles/stylegan3/blob/main/wikiart.py), and for scraping [pinterest](https://github.com/marimeireles/pinterest-web-scraper) too. I just realized this might be slightly illegal, so I'm just gonna drop it here that I'm not really using these scripts, they're just learning material. And remember, champions don't desobey the laws!

The Colab notebook is a work in progress, but sometimes I post the updated version [here](https://github.com/marimeireles/stylegan3/blob/main/stylegan3_tattoo_gen.ipynb).

Did some tiny work in Pyladies Berlin website, that's allways needing some love in case you want to help check the [open issues](https://github.com/PyLadiesBerlin/PyLadiesBerlin.github.io/issues)!

And developed a new talk to teach people who don't know how to code how to create an environment with mamba and maitain their own [static website](https://docs.google.com/presentation/d/1hoSefqb4wJy16nCynKQs0cI3iRgjH4-TQ9XItSbcEMU/edit#slide=id.p).

I also released a new version of my [website](https://raw.githubusercontent.com/marimeireles/psychonautgirl/master/old-website/%202022-02-20.png). The rest is not so interesting. I have to figure out a better way of saving it though, instead of screenshots.

Okay, so another thing I did was generating a cool thing out of processing. Actually, I did this last week, but I was trying to integrate on my website for real today and then I asked help for Flo, a front-end dev friend because I'm always lazy to learn javascript. He showed me an interesting thing I never thought about before:

```

const throttle = (func, limit) => {
  let lastFunc
  let lastRan
  return function() {
    const context = this
    const args = arguments
    if (!lastRan) {
      func.apply(context, args)
      lastRan = Date.now()
    } else {
      clearTimeout(lastFunc)
      lastFunc = setTimeout(function() {
        if ((Date.now() - lastRan) >= limit) {
          func.apply(context, args)
          lastRan = Date.now()
        }
      }, limit - (Date.now() - lastRan))
    }
  }
}
```

When you have way too many events you have to find a way to throttlle them.

Interestingly enough you can't do something like `sleep` because the browser just stacks up the events where ever and once the sleep is done it just bursts events into your face... So this is what you have to do.

At work unfortunately it was zero fun! :/
I was documenting things the whooole week and tonight I intend to finish it so I can have a fresh start and work on something new and challenging!
