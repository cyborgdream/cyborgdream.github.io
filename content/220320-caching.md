+++
title = "Caching on the web"
description = "24/??? Cashcashcash $$$"
date = 2022-03-20
draft = false
slug = "caching"

[taxonomies]
categories = ["journal"]
tags = ["learning", "web-dev", "performance"]

[extra]
comments = true
+++

# Cache overview

There are many different uses for cache in the web: but mostly it's server and client cache.

Server cache is stored on the site's server. They can be CDN cachings, which load geographically relevant content, this is done in a level of Webmasters, it's also called Gateway caches. Opcode caching or Proxy cache(?), store stuff like DBs, or result of compiled files. It's shared on a level of ISP.

> I wonder how caching of WASM works?

### How web caches works

1. Fresh representations are served directly from the cache, they're valid when their expiring time is still good.
2. Under certain circumstances a cache can serve stale responses.

If the HTTP request doesn't contain an `ETag` or `Last-Modified`, and it doesn't have any explicit freshness information the info. will usually be considered uncacheable. (But as we'll see later on, it's possible to create some heuristics).

### Controling caches

So, some ways of controlling it is by using HTML tags. They're easy to use, but not very effective because when a document has to go through a proxy, there aren't many that are doing their job and obeing the conventions. A `Pragma: no-cache` won't necessarily keep the page fresh.

Depending on the server you use though, you actually have some level of control over your headers.

`Expire` allows you to put any date. It's good for things that don't change in the long run. The clock on the Web server and the clock on wherever you're storing info. on the cache have to be synchronized for this to work well. Another issue, is that it's easy to forget you set that specific time for your cache.

HTTP 1.1 has a bunch of `Cache-Control` commands:

- max-age=[seconds] — specifies the maximum amount of time that a representation will be considered fresh. Similar to Expires, this directive is relative to the time of the request, rather than absolute. [seconds] is the number of seconds from the time of the request you wish the representation to be fresh for.

* s-maxage=[seconds] — similar to max-age, except that it only applies to shared (e.g., proxy) caches.

* public — marks authenticated responses as cacheable; normally, if HTTP authentication is required, responses are automatically private.

* private — allows caches that are specific to one user (e.g., in a browser) to store the response; shared caches (e.g., in a proxy) may not.

* no-cache — forces caches to submit the request to the origin server for validation before releasing a cached copy, every time. This is useful to assure that authentication is respected (in combination with public), or to maintain rigid freshness, without sacrificing all of the benefits of caching.

* no-store — instructs caches not to keep a copy of the representation under any conditions.

* must-revalidate — tells caches that they must obey any freshness information you give them about a representation. HTTP allows caches to serve stale representations under special conditions; by specifying this header, you’re telling the cache that you want it to strictly follow your rules.

* proxy-revalidate — similar to must-revalidate, except that it only applies to proxy caches.

**Validators:**

It's a way of checking if the info. is fresh without really having to download the entire representation. That's what `ETags` are, they were introduced in 1.1 too.

#### Caveats

Pragma: is not specified for HTTP responses and is therefore not a reliable replacement for the general HTTP/1.1 Cache-Control header.
Pragma should only be used for backwards compatibility with HTTP/1.0 caches where the Cache-Control HTTP/1.1 header is not yet present.

Files that are infrequently updated: you use a technique called revving. These files are named in a specific way, in their URL a revision number is added. To be able to get a new version of this file you need to pull a new file and for the user to be able to do that you have to serve a new file (and apparently rename it everywhere) but I don't really understand where.

**Freshness lifetime:**

In case none of the headers can be found, a heuristic freshness checking approach might be applicable. First, you look for a `Last-Modified` header, then you look for the current date. Date - `Last-Modified` = current freshness.

**Vary:**

Vary is a good thing because allows you to know if a cached response can be used or if a fresh one must be requested from the origin server. It's usually used to allow a resource to be cached in uncompressed and various compressed forms.
One caveat of Vary is that you should normalize your data, if you have stuff like `Accept-Encoding: gzip,deflate,sdch`, `Accept-Encoding: gzip,deflate`, `Accept-Encoding: gzip`, you're gonna have different requests to store the same data 3x if it's a gzipe. So you should write functions to normalize the Vary type to treat everything as `gzip` once.

# Firefox caching

Firefox has different fluxes for different kinds of caches. (Only partical content, when the thing to be cached doesn't pass server revalidation).

This system is also thread safe, and it has an order of priority:
1. open
2. read
3. management (for the memory pool and CacheEntry background operations)
4. close
5. index (index is being rebuild here (?)I don't really understand what this means)
6. evict (files that are overreaching disk space consumption limit are evicted)

It seems like they use a hashtable mapped by a scope key to keep hashtables of cache entries. Access to the tables are protected by a global lock.

There's something called intermediate memory caching... Which is super tiny and frequently used metadata fits in there.

> I should probably revisit threading again, maybe a little about loops

# Memory systems, Cache, DRAM, Disk

This book is soo low level.
> What's scratch-pad memories

Wuuh, way over my head. Less parties next time.