---
author: michael
comments: true
date: 2008-01-31 14:06:00+00:00
layout: post
link: http://blog.prokrams.com/2008/01/31/semi-practical-ironruby/
slug: semi-practical-ironruby
title: (Semi) Practical IronRuby
wordpress_id: 98
tags:
- DLR
- Example
- IronLogo
- IronRuby
- QuickStart
- Ruby
- Tutorial
---

So you followed the [quick start](http://michaeldotnet.blogspot.com/2008/01/ironruby-quick-start.html) I posted earlier, and you're thinking "So what?  What good is IronRuby and DLR to me?"  The DLR provides an extensible, powerful way to add scripting capabilities to your application.  Let's take a look at how IronRuby and C# can really interact. We'll create an "IronLogo" application, consisting of a windows form we can draw on utilizing a simple DSL implemented in Ruby.  Commands can be loaded via a file or through a console.

Our bulk of our application is actually going to be in C#, the details aren't important, but let's take a quick look at the class definition.

![LogoWindow Class Diagram](http://blog.prokrams.com/wp-content/uploads/2008/03/logowindowclass.png)

I've hidden a lot of the cruft, but the important things here are the TurtlePoint property that holds a Point object of the turtle's current location, and the MoveTurtle method.  What we're going to want to do is take the current turtle's location and move some offset.  Of course we don't have to write some complicated command parser, so we're going to use IronRuby and the DLR to handle all the heavy lifting.  Let's take a quick look at the application's Main method:





   1        static void Main()




   2         {




   3             Application.EnableVisualStyles();




   4             Application.SetCompatibleTextRenderingDefault(false);




   5




   6             LogoWindow logowin = new LogoWindow();




   7




   8             IScriptEngine ruby = IronRuby.GetEngine(IronRuby.CreateRuntime());




   9




  10             Dictionary<SymbolId, object> globalvars = IronRuby.GetExecutionContext(ruby.Runtime).GlobalVariables;




  11             globalvars[SymbolTable.StringToId("logowin")] = logowin;




  12




  13             IScriptScope IronLogoScope = ruby.Runtime.CreateScope();




  14




  15             ruby.Execute(IronLogoScope, ruby.CreateScriptSourceFromString(Properties.Resources.IronLogoRuby, SourceCodeKind.File));




  16




  17             logowin.PassScopeAndEngine(IronLogoScope, ruby);




  18




  19             logowin.ShowDialog();




  20




  21         }


You can see a lot of the same stuff happening here as you did in the [quick start](http://michaeldotnet.blogspot.com/2008/01/ironruby-quick-start.html), though there are some subtle differences.  First, if you look at line 11 you can see that we're actually assigning a global variable to the reference to our windows form.  That's right, our Ruby code will be able to access our Window form object directly, all though the magic of the DLR!  Also, on line 13, I create a scope using the Runtime property of the engine, this is important as this will create a scope that has references to the right engine, context, and so forth.  We learned from the quick start about the IScriptEngine.Execute command, but what are we running on line 15?  Why, it's an embedded text file that contains our ruby code that implements the DSL!  Would you believe it's only 15 lines?  See for yourself:





   1 def up(steps=1)




   2     $logowin.MoveTurtle($logowin.get_TurtlePoint.X, $logowin.get_TurtlePoint.Y - steps)




   3 end




   4




   5 def dn(steps=1)




   6     $logowin.MoveTurtle($logowin.get_TurtlePoint.X, $logowin.get_TurtlePoint.Y + steps)




   7 end




   8




   9 def rt(steps=1)




  10     $logowin.MoveTurtle($logowin.get_TurtlePoint.X + steps, $logowin.get_TurtlePoint.Y)




  11 end




  12




  13 def lt(steps=1)




  14     $logowin.MoveTurtle($logowin.get_TurtlePoint.X - steps, $logowin.get_TurtlePoint.Y)




  15 end


Here you can see we're defining four methods, they take a parameter called "steps" (which defaults to one) and then accesses the main form object through the global variable and calls the MoveTurtle method, as well as accessing the TurtlePoint method (remember that properties are really just syntactic sugar/metadata for method pairs, so we're actually calling the get method directly).

The other bit of trickery in the Main method is on line 17, this is where I pass both my engine and the scope that the above Ruby script was executed in, to the LogoWindow form.

Let's take a look at what we can do in the LogoWindow form since we have a reference to our scripting engine and scope.  Let's say we want to be able to load a script of our DSL and have the application run it.  Let's create a text file that looks like so:

up 10
lt 10
dn 10
rt 10
dn 10

As you can see, what it really consists of is just calls of the methods we defined above, let's see how we load such a file in the application:





 147        private void applyIronLogoScriptToolStripMenuItem_Click(object sender, EventArgs e)




 148         {




 149             OpenFileDialog ofd = new OpenFileDialog();




 150             ofd.Filter = "IronLogo Files (*.ilogo)|*.ilogo|All Files (*.*)|*.*";




 151             ofd.Title = "Apply An IronLogo File...";




 152             if (ofd.ShowDialog() == DialogResult.OK)




 153             {




 154                 this.scriptengine.Execute(this.scriptscope, this.scriptengine.CreateScriptSourceFromFile(ofd.FileName));




 155




 156             }




 157         }


Thats.... it really.  See how simple that is?  Loading the file results in our methods being executed and our Turtle being moved:

[![Logo having been run](http://blog.prokrams.com/wp-content/uploads/2008/03/logorun.png)](http://lh3.google.com/michaeldotnet/R6HVyzc6x0I/AAAAAAAAACU/yKo5avxT27w/image%5B3%5D)

Yay for the power of the DLR!  What if we want to be more interactive though?  We actually want to open a console and let people run the commands interactively.  No fear, with the DLR that's easy as well!  Since this is a Windows application first, we do need to import a couple of external Windows API funcitons, namely AllocConsole and FreeConsole.  Once we do that we can then create a console window and tell the DLR to open a Ruby console in it.  There's some Threading trickery going on that I'll spare you from, but the method for actually opening a console looks like this:





   1         private void RunConsole()




   2         {




   3             if (AllocConsole())




   4             {




   5                 this.consoleopen = true;




   6                 Ruby.Hosting.RubyCommandLine rubycommandline = new RubyCommandLine(new Ruby.Runtime.RubyContext(ScriptDomainManager.CurrentManager));




   7                 Microsoft.Scripting.Shell.SuperConsole superconsole = new Microsoft.Scripting.Shell.SuperConsole(rubycommandline, this.scriptengine, true);




   8                 rubycommandline.Run(this.scriptengine, superconsole, new Ruby.Hosting.RubyConsoleOptions());




   9




  10             }




  11         }


11 lines of code...and the real heavy lifting is only done in three of them.  Wow.  All that work, done for us.  What you end up with after that is something that looks like this:

[![Interactive Logo session](http://blog.prokrams.com/wp-content/uploads/2008/03/interactivelogo.png)](http://lh5.google.com/michaeldotnet/R6HVzTc6x2I/AAAAAAAAACk/XPGpxgZ8tbY/image%5B8%5D)


### Summary


There you have it, a simple implementation of LOGO as a DSL in Ruby, running on the DLR, interoperating with our C# code.  I've uploaded the above application to [CodePlex](http://www.codeplex.com) for your enjoyment.

[IronLogo CodePlex Project](http://www.codeplex.com/ironlogo)
