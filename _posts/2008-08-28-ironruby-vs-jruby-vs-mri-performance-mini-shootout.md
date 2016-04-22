---
author: michael
comments: true
date: 2008-08-28 01:15:52+00:00
layout: post
link: http://blog.prokrams.com/2008/08/27/ironruby-vs-jruby-vs-mri-performance-mini-shootout/
slug: ironruby-vs-jruby-vs-mri-performance-mini-shootout
title: IronRuby vs JRuby vs MRI. Performance Mini-Shootout.
wordpress_id: 135
tags:
- Benchmarks
- IronRuby
- JRuby
- MRI
- Performance
- Ruby
- ShootOut
---

One of the things I went over in my [eRubyCon talk](http://blog.prokrams.com/2008/08/25/erubycon-recap/) was the relative performance of [IronRuby](http://www.ironruby.net/) compared to both [JRuby](http://jruby.codehaus.org/) [![](http://farm3.static.flickr.com/2254/2141649813_03e886d464_m.jpg)](http://flickr.com/photos/46576087@N00/2141649813/sizes/s/)(which I consider to be a cousin of sorts to IronRuby) and [MRI](http://www.ruby-lang.org/en/). I did this by running the [same tests](http://antoniocangiano.com/2007/12/03/the-great-ruby-shootout/) that [Antonio Cangiano](http://antoniocangiano.com/) ran in December. 

 

There were a few test results that really stood out. The lists test was by far the worst for IR, where it was 56 times as slow as MRI and 3 times as slow as JRuby. But this wasn’t the only sore spot, there were other places that show room for improvement. Namely, vm1_blocks in which IronRuby was almost 9 times as slow as MRI. Also so_exception where IronRuby brought up the tail by being 10 times as slow as MRI. 

 

The nitty gritty is that IronRuby is about twice as slow as MRI with JRuby being slightly faster, overall. As the below graph illustrates:

 

[![image](http://blog.prokrams.com/wp-content/uploads/2008/08/image-thumb.png)](http://blog.prokrams.com/wp-content/uploads/2008/08/image.png)

 

There are a few high points in this test though. Number one, is that IronRuby ran the majority of the tests. There were only four failures out of forty, for a 90% success rate. For comparison JRuby had 95% success rate. Number two, there were actually a few tests in which IronRuby beat JRuby and MRI. No small feat for such a young implementation.

 

I think this mini-shootout illustrates that there is a lot of room for improvement for IronRuby. However, a little perspective goes a long way. JRuby was [announced in 2001](http://groups.google.com/group/comp.lang.ruby/msg/7d05f2ad7d84e6f3?hl=en), IronRuby was [announced last April](http://www.iunknown.com/2007/04/introducing_iro.html). The JRuby team has had 7 years to get to where they are today, whereas IronRuby has only had almost a year and a half! As the DLR improves IronRuby will receive those performance gains for free, and as compatibility becomes more stable the core team can focus on performance as well. The raw data can be downloaded [here](http://prokrams.com/files/ShootOut.csv).
