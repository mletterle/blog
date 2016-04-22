---
author: michael
comments: true
date: 2007-06-18 20:17:00+00:00
layout: post
link: http://blog.prokrams.com/2007/06/18/determining-the-ordinal-of-a-weekday/
slug: determining-the-ordinal-of-a-weekday
title: Determining the Ordinal of A Weekday.
wordpress_id: 73
tags:
- Development
- SQL
---

So, at work I'm having the fun experience of having to write a job scheduling app, using MSSQL as the back end.  One of the things I had to figure out was how to determine of a day was, say, the first Monday of the month or the third Thursday or so forth.  I couldn't find any built in functions that did this, so I had to write one.  Behold:

    
    <code>Create Function fn_ReturnOrdinalDay( @TestDate datetime) returns int
    begin
    declare @ordinal int, @loopdate datetime
    
    set @loopdate = @TestDate
    set @ordinal = 0
    
    </code>



    
    <code>while datepart(m, @TestDate) = datepart(m, @loopdate)
    begin
    </code>



    
    <code>    set @ordinal = @ordinal + 1
        set @testdate = dateadd(d, -7, @testdate)
    end
    
    return @ordinal
    end
    </code>


When called like fn_ReturnOrdinalDay(getdate()) will return 1 - 5 depending on how many times that day has occured.  For instance for today (Monday June 18th, 2007) it would return 3 since this is the Third Monday of the Month.
