+++
title = "Penis"
description = "18/??? The web is beautiful"
date = 2022-01-26
draft = false
slug = "web-beautiful"

[taxonomies]
categories = ["journal"]
tags = ["art", "web-dev"]

[extra]
comments = true
+++

Today I'm working, but it's been hard to focus on it. Not sure if it's because it's my version of Friday or just Mars is a very interesting place.
Could also be because I have to compile mamba every time I want to see a change and that takes me from 2 to 3 minutes and multi tasking is the poison of the brain.

Today I advanced the vape project a little further, we discussed a bit of electronics. Friday we have another meeting where we present good open source ideas to build the vapes.

I also attended the AI workshop but since it wasn't so super interactive Flo and I ended up creating a script to upvote a penis picture in the website we were using. [Check it out](https://abraham.ai/creations), if you click on 🤲 or 🔥 should be the top one, if it isn't, feel free:

```
#!/bin/bash
while :
do

curl 'https://abraham.ai/backend/burn' \
  -H 'Connection: keep-alive' \
  -H 'Pragma: no-cache' \
  -H 'Cache-Control: no-cache' \
  -H 'sec-ch-ua: " Not;A Brand";v="99", "Google Chrome";v="97", "Chromium";v="97"' \
  -H 'Accept: application/json, text/plain, */*' \
  -H 'DNT: 1' \
  -H 'Content-Type: application/json;charset=UTF-8' \
  -H 'sec-ch-ua-mobile: ?0' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36' \
  -H 'sec-ch-ua-platform: "macOS"' \
  -H 'Origin: https://abraham.ai' \
  -H 'Sec-Fetch-Site: same-origin' \
  -H 'Sec-Fetch-Mode: cors' \
  -H 'Sec-Fetch-Dest: empty' \
  -H 'Referer: https://abraham.ai/creations' \
  -H 'Accept-Language: en-US,en;q=0.9,de;q=0.8,it;q=0.7,pl;q=0.6,es;q=0.5' \
  --data-raw '{"password":"ilybeaVdeyINaJ6C","creation_id":"61f1ccd62acd791123d018b9"}' \
  --compressed

sleep 0
done
```