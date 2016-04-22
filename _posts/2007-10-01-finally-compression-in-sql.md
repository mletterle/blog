---
author: michael
comments: true
date: 2007-10-01 00:13:00+00:00
layout: post
link: http://blog.prokrams.com/2007/10/01/finally-compression-in-sql/
slug: finally-compression-in-sql
title: Finally, Compression in SQL.
wordpress_id: 83
---

Here's a [good overview](http://blogs.msdn.com/sqlserverstorageengine/archive/2007/09/30/data-compression-why-do-we-need-it.aspx) of the compression in SQL 2008 and why it's a good thing. Though I don't quite get the point on memory, don't you have to decompress the data at some point? Surely this will end up using more memory resources, say you have a 50% compressed piece of data in memory, to store the uncompressed data would require another 100% on top of that, effectively using 150% more then if you just had the uncompressed data in memory to begin with. Or am I missing something? Regardless, if you've ever zipped up a SQL Backup (I have, more times then I can count) then this seems like an obvious piece of "low hanging fruit" for the SQL 2008 team. I've always wondered why they didn't at least include built in compression for database backup and restore...
