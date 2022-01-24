+++
title = "This is a journey into HTTP2"
description = "All I learned about HTTP2 in my Mozilla internship. This used to be a three parts blogpost but I condensed everything here."
date = 2018-12-20
draft = false
slug = "http2"

[taxonomies]
categories = ["meta-internet"]
tags = ["performance", "web-dev"]

[extra]
comments = true
+++


As a performance intern working with the Webcompat team part of my initial proposal was to update the Webcompat server to HTTP/2. But what is HTTP/2? What's the difference between HTTP/1.1, HTTPS and HTTP/2? Newer is always better? How should I know if I should implement it on my website? We'll dive in into the basics and I'll give you the building blocks to understand a little bit about internet protocols and in later articles I'll introduce you to my process of analysis and implementation of an HTTP/2 server focused on improving Webcompat's website performance, hopefully that will give you some insights about how to improve your own server performance.

HTTP/1.1 stands for Hypertext Transfer Protocol version 1.1 and it's a standardized way of computers talk to each other through hyperlinks. HTTP/1.1 defines **methods** like GET that retrieves data and POST that sends it, those requests and responses have to be treated synchronous opening one TCP connection for each one of them, which might slow things a bit; HTTP/1.1 implements **status** there are responses to the methods like 404 not found or 200 that means that a request was successful;and HTTP **header** fields, which sometimes can be used to fetch data with the HEAD method. HTTP is stateless, which means there is no link between two requests being successively carried out on the same connection, sometimes you may to have a "memory" to store preferences of an user or a cart on an e-commerce and for that HTTP implements **cookies**. HTTP also defines **caching**, a very relevant tool for performance. Caching is the way to save data locally in order to decrease the number of requests you have to make to another computer, W3C offers [extensive documentation](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html) about it. If you want to get a deeper understanding on how HTTP/1.1 works MDN has this [great overview article](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview) about it, you can follow up the links, I recommend reading the entire section.

![http11](/assets/blog/http-1.png)

[source: cloudflare](https://blog.cloudflare.com/http-2-for-web-developers/)

HTTPS is a safer way to transfer data between computers. HTTPS websites encrypts the data you send and receive using TLS/SSL and the websites have to be certified by authorities on the internet that ensures that you are exchanging information with who you think you are, here is a link if you're interested in learning more about HTTPS. When we're talking about performance it seems obvious that HTTPS will have a bigger cost than HTTP because of the extra encrypting process, but according to this website's tests their results are pretty similar with HTTPS being [approximately 1% slower than HTTP](https://www.tunetheweb.com/blog/http-versus-https-versus-http2/).

Finally, HTTP/2 implements the same tools that HTTP/1.1 does, but on top of that it has some sweet new stuff. HTTP/2 as opposed to HTTP/1.1 is a multiplexed protocol which means it can handle parallel requests, that often means your page will load faster; it's implemented with a binary protocol which allows the use of improved optimization techniques; it compresses headers; and it allows a server to populate data in a client cache, in advance of it being required, through a mechanism called the server push. Besides that, all of the browsers only support HTTP/2 if they're over TLS which means HTTP/2 websites are always encrypted.


![http-compare](/assets/blog/http-compare.png)

[source: cloudflare](https://blog.cloudflare.com/http-2-for-web-developers/)


These are the basic differences between the three protocols. Now that you have your feet wet on the water it's time to learn how to run analysis and find out if HTTP/2 is good for you.


In my [previous blog post](https://psychonautgirl.space/blog/2018/12/20/journey-into-http2-building-blocks.html) I gave a small introduction to the main differences between the HTTP protocol versions. In this article I want to help you understand if you need HTTP/2 on your website. If you decide you do you can keep following this series of blog posts where I'll upgrade my own website and where I try in the best of my ability to give you some neat tips on how to analyse and upgrade yours!

### Is HTTP/2 worth it?

Even though using HTTP/2 is broadly advertised on the internet as the best possible option I don't believe it fits everybody needs. Here are some things to consider if implementing HTTP/2 is worth it for you:

 * It's not supported by older browsers. You can check [here](https://caniuse.com/#search=http2) which browsers support HTTP/2. If you know your user base accesses your website mainly from these browsers, it might not be worth it. Maybe it'll be a better use of your time to look for optimizations for HTTP/1.1, those can help a lot;

 * It's not supported by some web servers. You should check if your current server supports HTTP/2, [here](https://github.com/http2/http2-spec/wiki/Implementations) is an updated list on which servers currently support it;

 * It's not supported by some content delivery networks. You should check it either by looking at their website or checking this [list](https://en.wikipedia.org/wiki/HTTP/2#Content_delivery_networks);

 * Upgrading your whole ecosystem to run a HTTP/2 server might mean you'll spend a lot of time/money. Besides upgrading your web server and your content delivery network, you might also need to buy a certificate, you can find more information about it [here](https://letsencrypt.org/getting-started/);

 * You can [upgrade your HTTP/1.1 server to HTTPS](https://support.google.com/webmasters/answer/6033049), so access to HTTPS alone is not a good reason to upgrade to HTTP/2. HTTP/2 isn't intrisically safe and this [article](https://queue.acm.org/detail.cfm?id=2716278) goes over some reasons why;

 * Updating your web server might mean only a small improvement on loading time and sometimes it might mean a loss in performance. I've seen this happening in some cases while [analysing webcompat.com with HTTP/2](https://psychonautgirl.space/webcompat.html#HTTP-2), and [other](https://dev.to/david_j_eddy/is-http2-really-worth-it-1m2e) [people](https://blog.fortrabbit.com/http2-reality-check) have seen the same. The only way to find out if it's going to be worth it for your website is by running a lot of tests.

 If you think all of the points above aren't a real issue for you - or if you're willing to deal with them, let's dive in to how to analyze your website! In my next post I'll show you the methods and tools I used to analyze staging.webcompat.com and hopefully this real life example will help you to improve the performance of your own website.


This post is the continuation of the series “This is a journey in to HTTP/2”, you’ll find the first post where I talk about the difference between HTTP protocols [here](https://medium.com/@electrika01/this-is-a-journey-into-http-2-building-blocks-cba57a1e924c) and the second one where I advise whether you should or not use HTTP/2 on your own website [here](https://psychonautgirl.space/blog/2019/01/01/journey-into-http2-should-i-use-it.html). On this post we’ll explore how to find the configuration that fits your needs. Better than giving you a formula that might or might not work for you I’ll try to show you how to find the result that’s going to be optimal for your website.

## Initial setup

There are a lot of tools out there to help you evaluate your website’s performance. Here I decided to use Firefox DevTools, Chrome DevTools and [sitespeed.io](https://www.sitespeed.io/) by Wikimedia. Why these tools? They’re simple, easy to use, very customizable and they will cover a representative part of what HTTP/2 is doing in our website. Besides that Firefox, and Sitespeed offer a wide set of awesome open source tools and as a conscious developer that fights for the free web you should contribute with these companies by using them. To run tests on your computer use a real mobile device if you can, I recommend using a Nexus 5. If you don’t have a Nexus 5 at your disposal any mid-range device will do, this [list](https://twitter.com/katiehempenius/statuses/1067969800205422593) might help you to find something. Concerning internet speed I advise you to use a 5mbps internet connection. If you have a slower internet connection at your disposal it’s fine to run the tests with a lower speed, just remember to use a tool like [Throttle](https://www.sitespeed.io/documentation/throttle/) to establish a speed your internet supports. Note that if you have an internet speed faster than 5mbps you will also need to throttle it down. To run tests on mobile I believe the 3g slow speed or 300kbps is a good one. You can check how much speed you have at your disposal looking at [fast.com](https://fast.com/) and if you’re still feeling insecure about how your network is behaving you can use a tool like [Wireshark](https://www.wireshark.org/) to monitor it closer.

If you’re not comfortable with these tools Google has a great [doc](https://developers.google.com/web/tools/chrome-devtools/) about how to use DevTools and Firefox [too](https://developer.mozilla.org/en-US/docs/Tools), Sitespeed also have some fine [docs](http://sitespeed.io/documentation/). If you don’t want to learn this kind of thing right now and just jump straight away for the tests and results you can try using [WebPageTest](http://webpagetest.org/), it’s great tool and it’s also open source 🎔. WebPageTest is not as customizable as the tools I first advised you to use, but it’s possible to change a lot of settings on the website, so if you’re looking for simpler tests you should go have a look, it’s really a great tool. I recommend the same settings as I did before: using Nexus 5 for running tests with a slow 3g connection and Chrome and Firefox with a 5mbps connection.

I recommend you to use this setup because according to my [previous research](https://psychonautgirl.space/webcompat.html#Nexus-5) Nexus 5 represents the average setup of a mobile nowadays and the internet speeds also represents the average speed connection throughout the world. If you have more accurate statistics for your main users go ahead and use them!

To clarify things here this is the setup I’m using:

```
Server: ngnix 1.14.2
Desktop: MacBook Pro 2018 Mojave
Mobile: Nexus 5 Android 7
Internet speed: 2mbps/300kbps
```

P.S.: I have to use 2mbps to run my tests because my internet is currently that bad.

## Defining priorities

Performance is about an ocean, you can swim for miles in a lot of different directions but if you don’t focus chances are you’ll be lost in a sea of testing and hardly any insights or improves will come out from there.

![xkcd](/assets/blog/time-cost-xkcd.png)


source: [xkcd](http://xkcd.com/)

It’s important that you find what are the crucial points that need to be upgraded in your website to avoid getting lost. In my case the main aspects I want to improve are **time to first paint** and **time to be interactive**. Once you find what are your main aspects try to identify which files are responsible for triggering them, on my case I found out that two bundle files containing all of the page JS and CSS were the files that dictated the website performance if taking in account these two aspects.

Besides that define which are the most important pages that you want to optimize and focus on those, in my case I’m most interested in improving the performance of webcompat.com, webcompat.com/issues/new and webcompat.co/issues/#.

Another important thing to decide is whether it’s actually valuable for you to run tests using mobile. Running tests on mobile is a boring task, it’s super slow and they’re not the easiest creatures to handle, so if it’s not relevant for your context (which is rare nowadays) I’d say you could skip it. You can use [Lighthouse](https://developers.google.com/web/tools/lighthouse/) to give you an overview about how your website is handling mobile and maybe if you get good results you could also skip the mobile tests. In my case mobile is relevant because it’s where webcompat has the lowest scores when I run this kind of test and also because a substantial part of our traffic comes from mobile.

![traffic](/assets/blog/traffic-webcompat.png)

## Pre analysis

The `push` resource has to be used carefully for a couple of reasons, for example it interferes directly with your first paint time, so you have to take watch the size of the files you’re pushing, according to [this](https://docs.google.com/document/d/1K0NykTXBbbbTlv60t5MyJvXjqKGsCVNYHyLEXIxYMv0/edit#) article you should `push` files to fill your idle network time and no more. Using DevTools should make fairly simple to find out how much idle time we have between the time an user makes a request and your website starts responding it. You can also check this [link](https://stackoverflow.com/questions/667555/how-to-detect-idle-time-in-javascript-elegantly) to learn how to do it “elegantly”. Here are my results:

![idletime](/assets/blog/idle-time.png)

Webcompat’s idle time. Time in ms.

To get an idea of how HTTP/2 is going to change things I first measured the performance of my website without any change. You can measure this using Speedtest or Devtools. I recommend to use Speedtest because it’ll probably make your life easier, but if you want to run tests on DevTools it’s okay too. Keep in mind that you should create a new profile to do so, so your addons, cookies, etc don’t have any effects on your profiling, this is a very important step since extensions can have a [profound impact on your performance](https://twitter.com/denar90_/statuses/1065712688037277696).

![wpdefault](/assets/blog/wpdefault.png)
Webcompat’s performance with no changes. Time in ms.

Some explanation here: the “/” stands for webcompat.com, the “new” for webcompat.com/issues/new and the “396” for webcompat.com/issues/396, a random issue.

## The plan

`Push` will send the files you chose for the user before the user browser even requests them and this is the main difference from preloading, but there is a lot of specificities about it and if you’re interested, there is a great [article](https://dexecure.com/blog/http2-push-vs-http-preload/) explaining more about them. Because of that we don’t want to push the files every time the user access our page we only want to send them when it’s needed. To do that a good solution is to create a cookie that will indicate to the server if the user needs new information to be pushed or if it doesn;t and the user already has this info cashed on the browser. If you want to learn more about cookies the [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie) page is a nice starting point. Here is an example on how to create a session cookie on nginx:

```
server {
[...]
    http2_push_preload on;
    location = / {
        add_header Set-Cookie "session=1";
        add_header Link $resources;
    }
}

map $http_cookie $resources {
    "~*session=1" "";
    default "</style.css>; as=style; rel=preload, </image1.jpg>; as=image; rel=preload";
}
```

Since we know how much idle time we have we can estimate how many files we can `push`. In my case I’ll be pushing only two huge bundle files that are critical for my performance improvement. But in case my estimates are wrong I’ll also run tests pushing more files than I think the website can handle. I’ll also run some tests using the HTTP/2 server but not pushing anything so I can analyze how much pushing files are improving performance.

While running the tests I’m interested in looking at four different aspects of the website: time to first paint, speed index, time to start render and time to load all the requests. You can chose the parameters that make more sense to your website. But careful not to take too many things in consideration. Testing a lot of different aspects will not always give you a more accurate answer, in my experience is quite the opposite, testing things that might be unrelated just gave me a lot of work and confusing results.

To summarize we’ll be testing the following:
* Default HTTP/2 server (not pushing any files);
* Pushing the most crucial files and keeping inside the idle time budget;
* Pushing more files than the available idle time.

Besides that maybe it might be smart to use `preload` as a complement to the files that couldn’t be pushed. It depends on your currently situation, in my case I decided it wasn’t a priority but I’ll come back to it later on this post and I’ll try to give you some insights about it.

## The test analysis

To make the next tests I’ll use Sitespeed! Sitespeed offers you the option to load your configuration as a JSON file, this is the file I used to run my tests:

```
{
  "browsertime": {
    "iterations": 5,
    "browser": "BROWSER"
  },
  "utc": true,
  "outputFolder": "OUTPUT_FOLDER"
}
```

And this is the command line I used to run tests on my desktop: `docker run --shm-size=1g --rm -v "($pwd)":/sitespeed.io sitespeedio/sitespeed.io:7.73 --config config.json https://staging.webcompat.com` .

I wanted to make this report as accurate as possible so I decided to write a Selenium script to login on my website while I was testing. I recommend you looking in to Selenium and Sitespeed docs if you’re interested in creating something alike. The script is pretty straightforward and here is the one I used:

```
module.exports = {
  run(context) {
    return context.runWithDriver((driver) => {
      return driver.get('LOGIN_HTML')
        .then(() => {          
          const webdriver = context.webdriver;
          const until = webdriver.until;
          const By = webdriver.By;

          const userName = 'USER_LOGIN';
          const password = 'USER_PASSWORD';
          driver.findElement(By.name('login')).sendKeys(userName);
              
          driver.findElement(By.name('password'))
                             .sendKeys(password);
          const loginButton = driver.findElement(
                                     By.classN* Firefox had a performance consistently worse than Chrome’s on desktop that’s worth an investigation. According to previous tests done on other tools like DevTools and WebPageTest they didn’t used to have such a disparate performance;

                                    *
                                 In most of the cases just upgrading the server to HTTP/2 doesn't make much difference. I imagine that most of the performance improvement comes from nginx HPACK;
Even though the time to first paint didn't improve as much as I expected on the single issue page while I was pushing more than just the bundle files on Chrome mobile the TTI had a huge performance increase;
If comparing the Speed index the performance was worse in all of the devices in the new issue page while I was pushing more than just the bundle files, which means the new issue page have a smaller idle time and I shouldn't push as many files as I did.ame('btn-block'));
          loginButton.click();
          return driver.wait(until.elementLocated(
                                   By.id('body-webcompat')), 3000);
        });
    })
  }
};
```

While running the tests on my Nexus 5 I’ve used the following command: `sitespeed.io --browsertime.chrome.android.package com.android.chrome --preScript login_mobile.js --config config.json https://staging.webcompat.com` , you can learn more about how to use your mobile to run tests on Sitespeed reading their docs. Keep in mind that you have to redirect the user from the login page to the page you want to test directly, otherwise the browser will just cache the information from your first loaded page to the page you’re really trying to test. Maybe this issue explains a little better what I mean.

Using all of the knowledge above we can finally run some tests and analyze the results.

![traffic](/assets/blog/2only.png)
Webcompat’s performance without pushing anything. Time in ms.

![traffic](/assets/blog/2budgetok.png)
Webcompat’s performance pushing only crucial files. Time in ms.

![traffic](/assets/blog/2budgetnotok.png)
Webcompat’s performance pushing more files than idle time supports. Time in ms.

Seeing this data we can observe a few interesting things:

* Firefox and Chrome mobile have a bigger idle time than Chrome, so these environments benefit more from push . While pushing more than the budget I had estimate was better for Firefox and Chrome mobile it meant a worsen in performance for Chrome desktop;
* Firefox had a performance consistently worse than Chrome’s on desktop that’s worth an investigation. According to [previous tests](https://psychonautgirl.space/webcompat.html) done on other tools like DevTools and WebPageTest they didn’t used to have such a disparate performance;
* In most of the cases just upgrading the server to HTTP/2 doesn't make much difference. I imagine that most of the performance improvement comes from nginx [HPACK](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/);
* Even though the time to first paint didn't improve as much as I expected on the single issue page while I was pushing more than just the bundle files on Chrome mobile the TTI had a huge performance increase;
* If comparing the Speed index the performance was worse in all of the devices in the new issue page while I was pushing more than just the bundle files, which means the new issue page have a smaller idle time and I shouldn't push as many files as I did.

If you want to further optimize your website you can try to use preload. I encourage you to read [this article](https://www.keycdn.com/blog/http2-hpack-compression) about preloading to find out if it’s worth it for you. Generally if you have any of the following on your website it might be a good idea to use it:

* Fonts referenced by CSS files;
* Hero image which is loaded via background-url in your external CSS file;
* Critical inline CSS/JS.

Currently the website I'm testing doesn't have any critical inline CSS /JS, nor hero image and our fonts are hosted by third-parties. So I decided I wouldn’t make any changes by the time, since I was currently focusing on the push performance. If you’re planning to do that on your website keep in mind that we’ve used `http2_push_preload` on nginx and now we can’t preload the files on the server anymore because it’s set to default that all of the preloaded files should be pushed. Instead of using the server configuration file and sending them on headers you’ll have to use the `preload` tag on HTML files.

## Conclusion

Analyzing the data I got to the conclusion that for my website I should use the `push` resource on a few other files than I had first planned. I learned that using `push` was specially helpful on single issue pages and on the homepage and that the new issue page has a small idle time and I shouldn’t push as much resources as I push to the rest of the website, or it will start to get in the way of the page rendering.

I hope this article had helped. Let me know if I missed anything!