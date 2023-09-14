+++
title = "Getting the feet wet in the WWW shores"
description = "2/??? Implementing a telnet in Rust"
date = 2021-11-30
draft = false
slug = "telnet-rust"

[taxonomies]
categories = ["journal"]
tags = ["rust", "learning", "web-dev"]

[extra]
comments = true
+++

Because I'm super ignorant towards how the web works and I love the web I decided to start this project by following [this](https://browser.engineering/http.html) book and build a simple browser myself.

Telnet is a very simple software that allows you to interact with other computers. 

    A URL like http://example.org/index.html has several parts:

        * The scheme, here http, explains how to get the information
        * The host, here example.org, explains where to get it
        * The path, here /index.html, explains what information to get

The first step for the browser development is to create something that parses an URL and send and receive the appropriate messages.

Because this layer of communication is dealt by the OS we now have to use a feature called socket.

        * A socket has an address family, which tells you how to find the other computer. Address families have names that begin with AF. We want AF_INET, but for example AF_BLUETOOTH is another.
        * A socket has a type, which describes the sort of conversation that’s going to happen. Types have names that begin with SOCK. We want SOCK_STREAM, which means each computer can send arbitrary amounts of data over, but there’s also SOCK_DGRAM, in which case they send each other packets of some fixed size.
        * A socket has a protocol, which describes the steps by which the two computers will establish a connection. Protocols have names that depend on the address family, but we want IPPROTO_TCP.

Next step is to pick up all of these to create a functional socket.

Now that we have a socket is possible to connect with other computer as long as we have a DNS and a port.

Next we can send a request, but there's quite a stiff way to do it:

```
s.send(b"GET /index.html HTTP/1.0\r\n" + 
       b"Host: example.org\r\n\r\n")
```

The `b` stands for raw bytes, it's important to convert this.

To read the response, use the "read" function of socket. The response comes by parts, so it's necessary to write a loop to gather all of them. Or use the ` ` in Rust. Might be necessary to inform the way `newline`s are ending: `newline="\r\n"`.

The response also needs to be split into pieces. It contains:

- Status (don't check HTTP version because of misconfigured servers)
- Headers (split each line at the first column and fill in a map. Headers are case insensitive, so you can normalize them and remove whitespace)
- Body

Now we can print the content of `body` while removing all tags.

I wrote this while I was in the plane and didn't do progress code wise! So... Dec 2 is the day I actually try to follow this recipe, let's see what's up :)