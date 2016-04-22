---
author: michael
comments: true
date: 2008-03-16 03:07:15+00:00
layout: post
link: http://blog.prokrams.com/2008/03/16/announcing-zliby-all-zlib-pure-ruby/
slug: announcing-zliby-all-zlib-pure-ruby
title: Announcing Zliby -- All Zlib, Pure Ruby
wordpress_id: 112
tags:
- IronRuby
- Ruby
- Zlib
- Zliby
---

A few weeks ago [Dr. Wayne Kelly](http://sky.fit.qut.edu.au/~kellyw/) posted on the [IronRuby](http://www.ironruby.com) mailing [list](http://www.ruby-forum.com/forum/34) the [set of libraries and methods](http://www.ruby-forum.com/topic/143427#new) that had to be implemented in order to perform a [rubygems](http://www.rubygems.org/) setup. This is an important first step towards full rails support. I volunteered to take on the task of porting the [Zlib](http://www.zlib.net/) library. I thought, why not implement it in pure Ruby, that way there would be no external dependencies. It would have to be API compatible with the [ruby-core Zlib](http://www.ruby-doc.org/core/classes/Zlib.html) implementation, so it could be used in place of the natively compiled libraries.

 

After about a week of hacking on it, I've decided to make the initial public release of [Zliby](http://rubyforge.org/projects/zliby/). Right now the only thing that's implemented is [Zlib::Inflate's](http://zliby.rubyforge.org/classes/Zlib/Inflate.html) inflate functionality and [Zlib::GzipReader's](http://zliby.rubyforge.org/classes/Zlib/GzipReader.html) read functionality. Eventually all of the [ruby-core Zlib API](http://www.ruby-doc.org/core/classes/Zlib.html) will be implemented, and I hope to add other compression support going forward. Before you ask, yes it's slower then a native implementation, but it's portable, and has not be optimized for speed in any way yet. Also, since it's being developed for use in IronRuby, there are a few constructs that I can't currently use ([Array#pack](http://www.ruby-doc.org/core/classes/Array.html#M002245) for instance) though I will be able to in the future. Comments, feedback, and suggestions welcome!

 

Links:

 

[Zliby Project at RubyForge](http://rubyforge.org/projects/zliby/)

 

[Ruby-Core Zlib API Docs](http://www.ruby-doc.org/core/classes/Zlib.html)

 

[Zlib Homepage](http://www.zlib.net/)
