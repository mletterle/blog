---
author: michael
comments: true
date: 2007-09-29 19:04:00+00:00
layout: post
link: http://blog.prokrams.com/2007/09/29/query-analyzer-resurrected/
slug: query-analyzer-resurrected
title: Query Analyzer, Resurrected.
wordpress_id: 80
tags:
- Development
- SQL
- Tips
---

[Carpe Datum](http://blogs.msdn.com/buckwoody) over at MSDN talks about people [wanting Query Analyzer back over Management Studio](http://blogs.msdn.com/buckwoody/archive/2007/09/28/argh-bring-back-query-analyzer.aspx). I admit, the lack of Query Analyzer has been a source of ire for me as well. I like the default Management Studio for editing databases and what not, but if I want to whip up a quick query or something I miss query analyzer. Unfortunately the solution he provides causes Management Studio to always assume you want to run a query. After a bit of fooling around, I came up with a batch routine that seems to mimic Query Analyzer pretty well. I present to you isqlw.bat :)
    
    @echo off<br></br>if  '%1'=='' goto nofile<br></br>goto file<br></br>:nofile<br></br>echo. > %TEMP%\Untitled.sql<br></br>start sqlwb -nosplash %TEMP%\Untitled.sql<br></br>goto end<br></br>:file<br></br>start sqlwb -nosplash %1<br></br>:end<br></br>

  


  


This works well for me, YMMV of course, but I hope you might find it useful.
