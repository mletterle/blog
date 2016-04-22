---
author: michael
comments: true
date: 2008-06-10 02:54:32+00:00
layout: post
link: http://blog.prokrams.com/2008/06/10/ironruby-quickstart-reduex/
slug: ironruby-quickstart-reduex
title: IronRuby QuickStart ReDuex
wordpress_id: 129
tags:
- Compile
- IronRuby
- Microsoft
- Quick Start
- Reduex
- Ruby
- Tutorial
---

[Back in January](http://blog.prokrams.com/2008/01/28/ironruby-quick-start/) I did a post on getting started with IronRuby.  That post was based on Rev. 75 of the SVN tree.  As of  today (June 9th) the [SVN tree](http://ironruby.rubyforge.org/svn/) is up to Rev. 113.  Obviously with things like [RailsConf](www.railsconf.org) and [TechEd](http://www.microsoft.com/events/TechEd2008/default.mspx) driving a lot of the core teams work, a number of things have changed since January.  This post will basically be a rehash of the previous one, but updated for all the new quirks that have been introduced.


### 




# Getting The Source


As before, I recommend using [TortoiseSVN](http://tortoisesvn.org/) to grab the [source](http://rubyforge.org/scm/?group_id=4359). Downloading will take a bit, after the 3.5MB of source is downloaded you should end up with a directory structure like the following:

[![image](http://blog.prokrams.com/wp-content/uploads/2008/06/image-thumb.png)](http://blog.prokrams.com/wp-content/uploads/2008/06/image.png)


## 




## 




# Compiling IronRuby




## 


Inside of the trunk directory you will find the IronRuby.sln file, double-clicking it will open the solution (note that the solution file is now in VS2008 format instead of the previous VS2005 format).  You may get a warning, you can select “Load the Project Normally” and uncheck “Ask me this for all projects in the solution.”

Once the solution is loaded, keep the solution on the “Debug” configuration.  Do NOT select “ExternalDebug” (you’ll get broken references if you do):

[![image](http://blog.prokrams.com/wp-content/uploads/2008/06/image-thumb1.png)](http://blog.prokrams.com/wp-content/uploads/2008/06/image1.png)

Next we will manually remove the “SIGNED” compilation symbol from all the projects, as of Rev. 113 you have to manually remove it from the IronRuby.Libraries, Microsoft.Scripting, and Microsoft.Scripting.Core (be sure not to delete the DLR symbol) projects.

[![image](http://blog.prokrams.com/wp-content/uploads/2008/06/image-thumb2.png)](http://blog.prokrams.com/wp-content/uploads/2008/06/image2.png)

Once those are removed you can build the source!


## 




# Running That Which You Have Wrought


Where has previously our compilation would output into a bin\Debug folder, the resulting files are now found in \trunk\build\debug, which should look like this:

[![image](http://blog.prokrams.com/wp-content/uploads/2008/06/image-thumb3.png)](http://blog.prokrams.com/wp-content/uploads/2008/06/image3.png)

Due to a conflict with [rubinius](http://rubini.us/), the rbx executable is now ir.

As before you can just run ir.exe and [enter the wonderful world of ruby](http://www.ruby-lang.org/en/documentation/quickstart/), or you can continue on to see how we roll in the .NET world.


### Speaking C# With A Ruby Accent


The initial steps are basically the same as before, start a new C# console project and add references to Microsoft.Scripting.dll, Microsoft.Scripting.Core.dll, IronRuby.dll, and IronRuby.Libraries.dll.

We are going to create a simple console app that shows passing variables into and out of a ruby script.

The below program should be a good example:








    
    <a href="http://11011.net/software/vspaste"></a>
    
    
    <span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">using </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">System</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">;
    
    </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">using </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">Ruby</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">;
    
    </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">using </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">Microsoft</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">Scripting</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">Hosting</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">;
    </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">namespace </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">IronRubyConsoleApp
    
    </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">{
    
        </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">class </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">Program
    
        </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">{
    
            </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">static void </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">Main</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">string</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">[] </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">args</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">)
    
            {
    </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">ScriptRuntime </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">irruntime </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">= </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">IronRuby</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">CreateRuntime</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">();
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">ScriptEngine </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">ir </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">= </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">IronRuby</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">GetEngine</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">irruntime</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">);
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">ScriptScope </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">scope </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">= </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">ir</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">CreateScope</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">();
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">ScriptSource </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">script </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">= </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">ir</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">CreateScriptSourceFromString</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #fd7979">"puts \"Hello, #{name}!\"\ninput + 2"</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">);
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">scope</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">SetVariable</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #fd7979">"name"</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">, </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #fd7979">".NET"</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">);
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">scope</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">SetVariable</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #fd7979">"input"</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">, </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #fd7979">2</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">);
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">int </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">x </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">= </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">script</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">Execute</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver"><</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">int</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">>(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">scope</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">);
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">Console</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">WriteLine</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #bbddff">string</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">Format</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">(</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #fd7979">"The Result was {0}"</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">, </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">x</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">));
    
                </span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #46aa0c">Console</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">.</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: #a3a3a3">ReadLine</span><span style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial; color: silver">();
    
            }
    
        }
    
    }</span>


[](http://11011.net/software/vspaste)


You can see a clear hierarchy here:

We have a Runtime

That has an Engine

That has a Scope

That has a Source

We create a runtime, and we get our IronRuby engine into the runtime, we create a scope and load a script using that engine.  Set a couple of variables in the scope and then execute the script within the scope that we have set the variables in.

Hopefully this is enough to get you started, you should probably check out the other CreateScriptSource methods that the engine contains, you have a veritable cornucopia of options:

[![image](http://blog.prokrams.com/wp-content/uploads/2008/06/image-thumb4.png)](http://blog.prokrams.com/wp-content/uploads/2008/06/image4.png)


### Ruby with a .NET Accent


Another of the popular activities with IronRuby, if not the most popular, is going to be interfacing with both the .NET framework and other .NET code.  The below example shows us interfacing with System.Windows.Forms and making a simple GUI app.







    
    
    <p style="background: #1b1b1b none repeat scroll 0% 0%; -moz-background-clip: -moz-initial; -moz-background-origin: -moz-initial; -moz-background-inline-policy: -moz-initial">require 'mscorlib'</p>
    require ' System.Windows.Forms, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'
    
    require 'System.Drawing, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'
    
    Swf=System::Windows::Forms
    
    Sd=System::Drawing
    
    class RubyForm <Swf::Form
    
    def add_button text, location
    
    button = Swf::Button.new
    
    button.Text = text
    
    button.Location = location
    
    self.Controls.Add button
    
    button
    
    end
    
    def initialize
    
    self.Text = "RubyForm"
    
    @rbutton = add_button "Click Me!",  Sd::Point.new(150, 150)
    
    @rbutton2 = add_button "Click Me!", Sd::Point.new(150, 100)
    
    @rbutton.Click {|sender, e| Swf::MessageBox.show 'Hello World!'}
    
    @rbutton2.Click {|sender, e| Swf::MessageBox.show 'Hello, .NET!'}
    
    end
    
    end
    
    rf = RubyForm.new
    
    rf.ShowDialog


As you can see above, when requiring items from the GAC we must include the Fully Qualified Name, including version and StrongNameToken if applicable. The above code inherits from System.Windows.Forms.Form and adds a helper class for adding buttons and wires up a couple of event handlers.  Again this is just a simple example to get you started.


# Summary




# 


In this post we downloaded the IronRuby source, compiled it, and worked with IronRuby both from C# and by running a ruby script against .NET objects.  Hopefully this gets you going a little faster and onto the fun stuff quicker!


# Links


[IronRuby Homepage](http://www.ironruby.com/)

[IronRuby RubyForge Project](http://rubyforge.org/projects/ironruby)

[Ruby Language Homepage](http://www.ruby-lang.org/en/)

[John Lam’s homepage](http://www.iunknown.com/)
