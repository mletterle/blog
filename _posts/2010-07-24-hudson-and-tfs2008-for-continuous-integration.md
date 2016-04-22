---
author: michael
comments: true
date: 2010-07-24 19:16:03+00:00
layout: post
link: http://blog.prokrams.com/2010/07/24/hudson-and-tfs2008-for-continuous-integration/
slug: hudson-and-tfs2008-for-continuous-integration
title: Hudson and TFS2008 For Continuous Integration
wordpress_id: 150
tags:
- continuous integration
- Development
- hudson
- TFS
---

At [work](http://www.preemptive.com/) we're taking some time to get a proper [CI](http://en.wikipedia.org/wiki/Continuous_Integration) environment up and running. Since we also have [a Java product](http://www.preemptive.com/products/dasho/overview), [Hudson](http://hudson-ci.org/) was already set up and being used. I had used Hudson in the past for doing continuous integration of [IronRuby](http://www.ironruby.net/) under [Mono](http://www.mono-project.com/Main_Page) on [Linux](http://www.ubuntu.com/). Getting our build up and running was pretty much a breeze thanks to the [TFS](http://wiki.hudson-ci.org/display/HUDSON/Team+Foundation+Server+Plugin), [Gallio](http://wiki.hudson-ci.org/display/HUDSON/Gallio+Plugin), [MSBuild](http://wiki.hudson-ci.org/display/HUDSON/MSBuild+Plugin), and [NCover](http://wiki.hudson-ci.org/display/HUDSON/NCover+Plugin) plugins. Once that was set up and running, it was easy enough to[ subscribe to the CheckinEvent on TFS](http://msdn.microsoft.com/en-us/magazine/cc507647.aspx) and have it[ ping a defined URL](http://wiki.hudson-ci.org/display/HUDSON/Remote+access+API) on Hudson to kick off the build. Yay! Proper Continuous Integration!

But then something unexpected started to happen. At two hour intervals, the build would kick off, even if not check in had occurred! Some digging into the Windows Event Log, lead to what seemed to be the culprit. Every two hours TFS syncs users and groups with Active Directory. For some reason this raises a CheckinEvent. So the cause was discovered, now how to stop it? A quick searching on [SearchEngine] revealed[ this tidbit](http://blogs.msdn.com/b/adamsinger/archive/2007/07/31/great-news.aspx): 

<blockquote>you can now take advantage of the ability to modify the periodic AD/GSS sync period. To do so, you need to manually modify a Web.config file on the Application Tier that by deafult will appear under the folder "%PROGRAMFILES%\Microsoft Visual Studio 2008 Team Foundation Server\Web Services\services". DISCLAIMER: I do not guarrantee that this will not cause your computer to eat all of the data it has ever come into contact with, possible with fire. This confers no warranties and no rights, your mileage may vary, use at your own risk, void where prohibited, etc. [Note: still my blog]. You'll need to add the following two lines to the key/value pair section at the top of this file, followed by resetting IIS to make sure our application pool picks up the changes:  

> 
> </blockquote>

<blockquote>`<add key="IdentityUpdatePeriod" value="01:00:00" />  
<add key="IdentityUpdateInitial" value="01:00:00" />`  

> 
> </blockquote>

<blockquote>Note that I've set both the initial update (i.e. the delay after startup that the first periodic sync happens) and the delay between periodic syncs to 1 hour. You can increase this to multiple hours, or decrease it to a matter of minutes. 
> 
> </blockquote>

So after making those changes, I noticed that the Events were no longer being logged every two hours, but the CheckinEvent was still getting raised at that interval! What could be going on? Well after some (read: A LOT) of [SearchEngine]ing, [it turns out](http://blogs.msdn.com/b/granth/archive/2008/09/15/does-tfs-guarantee-event-subscription-delivery.aspx), TFS will automatically try resending "failed" alerts every two hours for ~12 hours. Since the CheckinEvent subscription is expecting a SOAP webservice, and not just a random url, it thinks that the notification "failed" when it doesn't get the response it expects. Causing it to try resending to the same url over, and over, and over again at two hour intervals. 

So what we need is a very very simple web service to take the SOAP message then turn around and ping a url. This way TFS gets the response it expects and acknowledges that the notification was successfully sent.

All you have to do is implement the 'Notify' method that TFS expects to be able to call:
    
    
            [SoapDocumentMethod("http://schemas.microsoft.com/TeamFoundation/2005/06/Services/Notification/03/Notify",
            RequestNamespace = "http://schemas.microsoft.com/TeamFoundation/2005/06/Services/Notification/03")]
            [WebMethod]
            public void Notify(string eventXml)
            {
                    try
                    {
                        System.Net.WebRequest req = System.Net.HttpWebRequest.Create("http://www.example.org/hudson/job/AutomatedBuild/build?token=TOKEN");
                        req.GetResponse();
                    }
                    catch
                    {
                    }
            }

I have put together a simple service that will do this for multiple URLs defined in the Web.config. Feel free to get it [here](http://github.com/mletterle/TFSUrlPinger) at [GitHub](https://github.com/) and save yourself the frustration I encountered. 
