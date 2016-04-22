---
author: michael
comments: true
date: 2007-01-04 03:42:00+00:00
layout: post
link: http://blog.prokrams.com/2007/01/04/useful-programming-skill-2/
slug: useful-programming-skill-2
title: 'Useful Programming Skill #2'
wordpress_id: 33
tags:
- Development
---

The ability to NOT overengineer a problem.  One of my latest tasks was to take a spreadsheet and update a SQL database with data from that spreadsheet.  A pilot had already been run, I was responsible for developing a deployable script.  I asked the pilot team to send me the script they used.  Turned out they took the spreadsheet, converted it to XML using a XML map, and copied and pasted that into the script. They then used OPENXML and proceeded to use a cursor to update each field one-by-one.  Ostensibly, this was done for logging purposes.  I replaced it with three update statements using OPENDATASOURCE direct to the spreadsheet with inner and left joins.  Much more maintainable and likely more performat.  Remember, just because you CAN doesn't mean you SHOULD.  And XML does not solve everything (sacrilege I know).
