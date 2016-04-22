---
author: michael
comments: true
date: 2007-11-13 21:18:00+00:00
layout: post
link: http://blog.prokrams.com/2007/11/13/t-sql-hex-string-to-varbinary-improved/
slug: t-sql-hex-string-to-varbinary-improved
title: T-SQL Hex String to VarBinary (Improved)
wordpress_id: 92
tags:
- .NET
- Development
- SQL
- TSQL
---

[Peter DeBetta](http://sqlblog.com/blogs/peter_debetta/default.aspx) posted a while back with [a function](http://sqlblog.com/blogs/peter_debetta/archive/2007/03/09/t-sql-convert-hex-string-to-varbinary.aspx) to take a hex string and convert it to varbinary  It has a couple of slight issues, the biggest of which is it can't handle an odd number of hex digits.  Below is my replacement, because it's using bigints the upper limit isn't as high, but it's good enough for most things:





    
    <span style="color: #0000ff">CREATE</span> <span style="color: #0000ff">FUNCTION</span> dbo<span style="color: #808080">.</span>HexStrToVarBinary<span style="color: #808080">(</span>@hexstr <span style="color: #0000ff">varchar</span><span style="color: #808080">(</span>8000<span style="color: #808080">))
    </span><span style="color: #0000ff">RETURNS</span> <span style="color: #0000ff">varbinary</span><span style="color: #808080">(</span>8000<span style="color: #808080">)
    </span><span style="color: #0000ff">AS
    BEGIN</span>
        <span style="color: #0000ff">DECLARE</span> @hex <span style="color: #0000ff">char</span><span style="color: #808080">(</span>1<span style="color: #808080">),</span> @i <span style="color: #0000ff">int</span><span style="color: #808080">,</span> @place <span style="color: #0000ff">bigint</span><span style="color: #808080">,</span> @a <span style="color: #0000ff">bigint
    </span>    <span style="color: #0000ff">SET</span> @i <span style="color: #808080">=</span> <span style="color: #ff00ff">LEN</span><span style="color: #808080">(</span>@hexstr<span style="color: #808080">)</span> 
    
        <span style="color: #0000ff">set</span> @place <span style="color: #808080">=</span> <span style="color: #ff00ff">convert</span><span style="color: #808080">(</span><span style="color: #0000ff">bigint</span><span style="color: #808080">,</span>1<span style="color: #808080">)
    </span>    <span style="color: #0000ff">SET</span> @a <span style="color: #808080">=</span> <span style="color: #ff00ff">convert</span><span style="color: #808080">(</span><span style="color: #0000ff">bigint</span><span style="color: #808080">,</span> 0<span style="color: #808080">)
    
    </span>    <span style="color: #0000ff">WHILE</span> <span style="color: #808080">(</span>@i <span style="color: #808080">></span> 0 <span style="color: #808080">AND</span> <span style="color: #808080">(</span><span style="color: #ff00ff">substring</span><span style="color: #808080">(</span>@hexstr<span style="color: #808080">,</span> @i<span style="color: #808080">,</span> 1<span style="color: #808080">)</span> <span style="color: #808080">like</span> <span style="color: #ff0000">'[0-9A-Fa-f]'</span><span style="color: #808080">))</span>
         <span style="color: #0000ff">BEGIN</span>
            <span style="color: #0000ff">SET</span> @hex <span style="color: #808080">=</span> <span style="color: #ff00ff">SUBSTRING</span><span style="color: #808080">(</span>@hexstr<span style="color: #808080">,</span> @i<span style="color: #808080">,</span> 1<span style="color: #808080">)</span>
            <span style="color: #0000ff">SET</span> @a <span style="color: #808080">=</span> @a <span style="color: #808080">+</span>
        <span style="color: #ff00ff">convert</span><span style="color: #808080">(</span><span style="color: #0000ff">bigint</span><span style="color: #808080">,</span> <span style="color: #0000ff">CASE</span> <span style="color: #0000ff">WHEN</span> @hex <span style="color: #808080">LIKE</span> <span style="color: #ff0000">'[0-9]'</span>
             <span style="color: #0000ff">THEN</span> <span style="color: #ff00ff">CAST</span><span style="color: #808080">(</span>@hex <span style="color: #0000ff">as</span> <span style="color: #0000ff">int</span><span style="color: #808080">)</span>
             <span style="color: #0000ff">ELSE</span> <span style="color: #ff00ff">CAST</span><span style="color: #808080">(</span><span style="color: #ff00ff">ASCII</span><span style="color: #808080">(</span><span style="color: #ff00ff">UPPER</span><span style="color: #808080">(</span>@hex<span style="color: #808080">))-</span>55 <span style="color: #0000ff">as</span> <span style="color: #0000ff">int</span><span style="color: #808080">)</span> <span style="color: #0000ff">end</span> <span style="color: #808080">*</span> @place<span style="color: #808080">)
    </span>    <span style="color: #0000ff">set</span> @place <span style="color: #808080">=</span> @place <span style="color: #808080">*</span> <span style="color: #ff00ff">convert</span><span style="color: #808080">(</span><span style="color: #0000ff">bigint</span><span style="color: #808080">,</span>16<span style="color: #808080">)
    </span>        <span style="color: #0000ff">SET</span> @i <span style="color: #808080">=</span> @i <span style="color: #808080">-</span> 1
    
         <span style="color: #0000ff">END</span> 
    
        <span style="color: #0000ff">RETURN</span> <span style="color: #ff00ff">convert</span><span style="color: #808080">(</span><span style="color: #0000ff">varbinary</span><span style="color: #808080">(</span>8000<span style="color: #808080">),</span>@a<span style="color: #808080">)
    </span><span style="color: #0000ff">END
    </span>GO
