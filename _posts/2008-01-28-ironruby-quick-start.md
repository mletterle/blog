---
author: michael
comments: true
date: 2008-01-28 14:25:00+00:00
layout: post
link: http://blog.prokrams.com/2008/01/28/ironruby-quick-start/
slug: ironruby-quick-start
title: IronRuby Quick Start
wordpress_id: 97
tags:
- IronRuby
- Microsoft
- QuickStart
- Ruby
- Tutorial
---

[IronRuby](http://www.ironruby.com/) is [Microsoft's](http://www.microsoft.com/), with collaboration by the [public](http://rubyforge.org/projects/ironruby),  implementation of [Ruby](http://www.ruby-lang.org/en/) on their  ![Ruby Logo](http://blog.prokrams.com/wp-content/uploads/2008/03/rubylogo.thumbnail.png)[Dynamic Language Runtime](http://compilerlab.members.winisp.net/dlr-spec-hosting.pdf).  There's another version of Ruby for .NET called, ironically, [Ruby.NET](http://rubydotnet.googlegroups.com/web/Home.htm) that runs directly on the CLR.  This post won't be about that though, if you want to see a comparison, look [here](http://www.oreillynet.com/windows/blog/2008/01/rubynet_vs_ironruby_whats_the.html) for a fairly good write up.

Downloading IronRuby from the [SVN server](http://rubyforge.org/scm/?group_id=4359) and compiling in VS2005 was actually pretty painless.  But after that I couldn't find any, working, examples of getting an Ruby script running in the DLR.  A big part of this is due to IronRuby still being officially "pre-Alpha" with the Scripting Host API in flux.  Regardless, I hope this will be enough to get some people trying to use the latest SVN (rev. 75) up and running.


### Building The Source


Download the latest revision from [RubyForge using SVN](http://rubyforge.org/scm/?group_id=4359), if you need a client I HIGHLY recommend [TortiseSVN](http://tortoisesvn.org/).  Once you're done downloading the source, you should be able to open the IronRuby.sln file in Visual Studio.  We only need to make one change, and that's to the Microsoft Scripting Project.   Bring up the project properties and go to the Build tab:

[![signed.png](http://blog.prokrams.com/wp-content/uploads/2008/03/signed.png)](http://lh3.google.com/michaeldotnet/R53ltzc6xuI/AAAAAAAAABk/Zhil_6qTYoA/image3)

We have to remove the Conditional compilation symbol of "SIGNED", otherwise the Scripting host will be looking for Microsoft signed copies of the IronRuby library, which we don't have.  After that go ahead and build the solution (cross your fingers if it makes you feel better).

When all is done you should end up with a bin\Debug folder in your SVN root that looks something like this:

[![debugdir.png](http://blog.prokrams.com/wp-content/uploads/2008/03/debugdir.png)](http://lh6.google.com/michaeldotnet/R53lujc6xwI/AAAAAAAAAB0/4FDLIIu19tQ/image12)

You can go ahead and start rbx from right there and begin [playing with Ruby](http://www.ruby-lang.org/en/documentation/quickstart/) if you'd like.  But if that's all we wanted to do we would have just downloaded Ruby, right?  This is IronRuby, let's do it the .NET Way!

**
**


### **Hosting IronRuby in C#**


Create a new Console Application solution in Visual Studio, say RubyExample.  Add References to the Microsoft.Scripting.dll, IronRuby.dll, and IronRuby.Libraries.dll files.

Let's begin with the most basic, a simple Hello World:
using System;using Ruby;using Ruby.Runtime;using Microsoft.Scripting;using Microsoft.Scripting.Hosting;namespace RubyExample{  static class Program  {      /// <summary>      /// The main entry point for the application.      /// </summary>      [STAThread]      static void Main()      {         IScriptEnvironment scriptenvironment = ScriptEnvironment.GetEnvironment();         IScriptEngine rubyengine = scriptenvironment.GetEngine("ruby");         scriptenvironment.ExecuteSourceUnit(rubyengine.CreateScriptSourceFromString("puts'Hello World!         \nPress Any Key To Continue..'") );         Console.ReadKey();      }  }
[](http://11011.net/software/vspaste)Let's take a quick look at what we're doing here.  We're setting up a ScriptEnvironment, this is where our Dynamic languages are going to live and play.  Then out of that environment we're asking for someone who understands Ruby.  After that we're just saying, hey ScriptEnvironment, run what the Ruby guy says.

So that's pretty nifty, we could also tell the RubyEngine to CreateScriptSourceFromFile and move whatever code we want out of a string constant.  Which is probably a good idea for anything beyond a line a two.  But what if we actually want to talk back and forth?  Let's move on to the next example.


### Accessing Global Variables


The easiest way to pass data between IronRuby and C# is via Global Variables.  See below for an example.

    
    <span style="color: blue"></span>


[](http://11011.net/software/vspaste)As you can see I switched it up a bit and inited the Environment and Engine a bit differently, one way is more general, the other specific to IronRuby.  The power of the ScriptingHost API is you can on the fly decide what language you want to use.  But if you know you're only going to be doing IronRuby, you can use the above method.

The real trick is in the second line, we get a reference to the GlobalVariables of the current IronRuby execution context.  Once we have that we can start assigning global variables values and retrieving them back.


### Summary


Hopefully this is enough to get you started.  As I continue to delve into IronRuby, I will be sure to post what I find here, so keep an eye out!

[IronRuby Homepage](http://www.ironruby.com/)
[IronRuby RubyForge Project](http://rubyforge.org/projects/ironruby)
[Ruby Language Homepage](http://www.ruby-lang.org/en/)
[DLR Hosting Spec](http://compilerlab.members.winisp.net/dlr-spec-hosting.pdf)
[John Lam's homepage](http://www.iunknown.com/)
