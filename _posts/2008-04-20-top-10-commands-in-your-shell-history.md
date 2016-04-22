---
author: michael
comments: true
date: 2008-04-20 23:43:27+00:00
layout: post
link: http://blog.prokrams.com/2008/04/20/top-10-commands-in-your-shell-history/
slug: top-10-commands-in-your-shell-history
title: Top 10 Commands In Your Shell History
wordpress_id: 116
tags:
- Geek
- Memes
- Programming
---

Saw [this post](http://objo.com/2008/4/19/history-blog-meme) on [objo's](http://twitter.com/objo) blog, apparently it's a meme of some kind, though his was the first I've seen.  But I thought it was pretty cool none the less.  Here's mine:

    
    $  history 1000 | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn
    | head
    114 cd
    84 ls
    79 irb
    39 git
    31 pedump
    10 ps
    10 objdump
    10 e
    8 ssh
    8 dd


You can see a bit of my dual developerness there, irb and git for ruby, and pedump and e for the windows side (this is in cygwin).  What does your shell history have to say?
