---
author: michael
comments: true
date: 2008-03-13 20:49:19+00:00
layout: post
link: http://blog.prokrams.com/2008/03/13/mbunit-ncover-and-teambuild-living-together-mass-hysteria/
slug: mbunit-ncover-and-teambuild-living-together-mass-hysteria
title: MbUnit, NCover, and TeamBuild Living Together, Mass Hysteria!
wordpress_id: 111
tags:
- MbUnit
- MSBuild
- NCover
- TeamBuild
- TFS
---

 

Recently I've been asked to make a case for using something other then [MSTest](http://www.kiwidude.com/dotnet/DownloadPage.html), one of the arguments against using an [xUnit](http://en.wikipedia.org/wiki/XUnit) framework was the lack of integration to Team Foundation Server. Well, I took it upon myself to show that MbUnit and NCover could perform just as well in a TeamBuild environment. Here's how to get it all working.

 

First you'll want to get [MbUnit](http://www.mbunit.com/), [NCover](http://www.ncover.com/download/discontinued) (1.5.8), [NCoverExplorer](http://www.kiwidude.com/dotnet/DownloadPage.html) (1.4.0.7) along with it's Extras (1.4.0.5), Note that NCover and NCoverExplorer are no longer free, but their past version were and are still available free of charge, just without official support. They work quite well though!

 

Next you'll need to edit the proj file for your unit tests, near the top of the file, enter some UsingTask directives for NCoverExplorer:

 

 
    
    <UsingTask TaskName="NCoverExplorer.MSBuildTasks.NCoverExplorer" AssemblyFile="..\libs\NCoverExplorer.MSBuildTasks.dll"/>
    <UsingTask TaskName="NCoverExplorer.MSBuildTasks.NCover" AssemblyFile="..\libs\NCoverExplorer.MSBuildTasks.dll"/>
    <UsingTask TaskName="NCoverExplorer.MSBuildTasks.NUnitProject" AssemblyFile="..\libs\NCoverExplorer.MSBuildTasks.dll"/>





As you can see, I'm using a relative path to the MSBuildTask assemblies, you can put in whatever path you need, but there are good arguments for keeping your testing frameworks in the same project directory structure as your unit tests.





Near the bottom uncomment out the <Target> section with the name of "AfterBuild", this is were we're going to tell our build system to run our tests and coverage analysis.





Remember we're going to be doing TeamBuild, but we'd still like it to work outside that environment for local use. So let's add a couple of property groups that set up some variables for us depending on our build environment:








    
    <PropertyGroup>
    	<CoveragePath  Condition="'$(DropLocation)' != ''">$(DropLocation)\$(BuildNumber)\$(Configuration)\CodeCoverage</CoveragePath>
    	<CoveragePath  Condition="'$(DropLocation)' == ''">$(TargetDir)\CodeCoverage</CoveragePath>
    	<TestPath Condition="'$(DropLocation)' != ''">$(DropLocation)\$(BuildNumber)\$(Configuration)\TestResults</TestPath>
    	<TestPath Condition="'$(DropLocation)' == ''">$(TargetDir)\TestResults</TestPath>
    </PropertyGroup>
    
    <PropertyGroup>
    	<CoverageFile>$(CoveragePath)\Coverage.xml</CoverageFile>
    	<TestReport>TestResults{0}{1}</TestReport>
    </PropertyGroup> 









As you can see here we check to see if DropLocation is set and if it is or isn't determines where we're going to place our test and coverage reports. Next we'll simply add some tasks to setup directories and actually run NCover and NCover explorer:




    
    <MakeDir Condition="!Exists('$(CoveragePath)')" Directories="$(CoveragePath)" />
    <MakeDir Condition="!Exists('$(TestPath)')" Directories="$(TestPath)" />
    
    <NCover CoverageFile="$(CoverageFile)" CommandLineExe="C:\Program Files\MbUnit\mbunit.cons.exe" CommandLineArgs='"$(TargetPath)" /rf:"$(TestPath)" /rt:HTML /rnf:"$(TestReport)"'  />
    
    <NCoverExplorer
    	ToolPath="C:\Program Files\NCoverExplorer"
    	OutputDir="$(CoveragePath)"
    	XmlReportName="CoverageSummary.xml"
    	HtmlReportName="CoverageSummary.html"
    	CoverageFiles="$(CoverageFile)" 
    	ShowExcluded="False"
    	MinimumCoverage="75"
    	SatisfactoryCoverage="80"
    	Exclusions="Assembly=.*Tests.*"
    />





And that's it really, when you build via a TeamBuild, local MSBuild, or even from Visual Studio, you'll have two extra directories in your output location, CodeCoverage and TestResults, in there you'll find nicely formatted HTML reports informing you of tests and code coverage. If any tests fail then the build will fail, if code coverage doesn't meet the minimum your build will fail. NCoverExplorer's build task has an option called "FailMinimum", if you set this to false then the build will succeed even if your code coverage doesn't meet the "minimum". With TFS 2008's "build on check-in" feature, you start to see possibilities here. With the NCover and MbUnit reports having xml equivalents the reporting/analytic possibilities are endless.
