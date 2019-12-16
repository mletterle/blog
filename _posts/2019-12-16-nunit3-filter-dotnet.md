---
layout: post
title: Using NUnit's Test Selection Language in .NET Core
tags: nunit3 nunit dotnet vstest
excerpt_separator: "<!--more-->"
date: 2019-12-16 08:37 -0500
---
[TLDR]({{ page.url }}#tldr); In the next release of the [NUnit3 VS Test Adapter](https://github.com/nunit/nunit3-vs-adapter), it will be possible to utilize filters written
in NUnit's native [Test Selection Language](https://github.com/nunit/docs/wiki/Test-Selection-Language).

## Background

With the release of [.NET Core 3.1](https://docs.microsoft.com/en-us/dotnet/core/whats-new/dotnet-core-3-1), I've been finding myself
working on porting a number of code bases from Windows-only .NET Framework to this new exciting cross-platform world. My .NET Unit Testing
Framework of choice has long been [NUnit](https://nunit.org/) and a number of these code bases have utilized nunit to run continuous integration
testing. 

<!--more-->

There is a lot of infrastructure in those CI processes that has been built around the [nunit console runner](https://github.com/nunit/nunit-console).
We specify output locations for the report xml via `--work` and `--result`, allowing the CI system to report on it. Since the system reports
on the aggregate of test runs across environments and systems, we also utilize the [Template Based Test Naming](https://github.com/nunit/docs/wiki/Template-Based-Test-Naming)
with `--test-name-format` to distinguish the same test across environments.  Since a single test assembly might have tests that are only valid on a
specific platform, over time a robust system of filters has developed using NUnit's Test Selection Language and specified with `--where`.

Unfortunately, when it came time to start running some of these tests on *non-Windows* platforms, it was quickly discovered that the nunit console runner
is a *Windows-only* application (since it is compiled for the legacy Windows Framework). Luckily, we had already added the NUnit3 VS Test Adapter
to our test project in order to run tests from Visual Studio. This also allows us to use `dotnet test` to run the test project utilizing the NUnit test engine
anywhere .NET Core can run. So, the next step was to figure out how to configure the test engine via `dotnet test` to act in the same way as it does
with the `where`, `test-name-format`, `work`, and `result` command line options.

## Configuring the NUnit Engine 

As it turns out, the [vstest framework](https://github.com/microsoft/vstest) has an extensible framework for configuring test runners using [runsettings](https://github.com/microsoft/vstest-docs/blob/master/docs/configure.md). The documentation for NUnit's [Adapter Settings](https://github.com/nunit/docs/wiki/Tips-And-Tricks) shows a number of options that can be tweaked, 
and `dotnet test` allows us to set these on the [command line](https://github.com/Microsoft/vstest-docs/blob/master/docs/RunSettingsArguments.md). For instance
given an existing command line of:
```batch
nunit3-console TestAssembly.dll --work=workDir --result=test-results\TestAssembly.xml --test-name-format=testEnvironment.{m}.{a}
```
You can get the same results with:
```sh
dotnet vstest TestAssembly.dll -- NUnit.WorkDirectory=workDir NUnit.TestOutputXml=test-results NUnit.DefaultTestNamePattern=testEnvironment.{m}.{a}
```

We use `dotnet vstest` in this example in order to operate on the pre-built test assembly. Passing runsettings to `dotnet test` to operate on a test project
works exactly the same way.

Note that the `result` option differs, this is because the console runner allows specification of the filename for the output, while the runsettings only allow
specification of the output directory (the filename is [TestAssembly].xml), so the two examples *are* equivalent. 

## The Filtering Problem

But... what about `where`? This is where things got a bit tricker. As it turns out, vstest itself already has a cross-framework [filtering language](https://docs.microsoft.com/en-us/dotnet/core/testing/selective-unit-tests#nunit) 
that supports NUnit. Unfortunately, the syntax is completely different from NUnit's native Test Selection Language. Perhaps one could try to 
translate between NUnit's TSL and native VSTest filters, but that seems like adding a needless level of complexity. It's also not clear that the
VSTest filter language even supports all of the same features as NUnit's Test Selection Language. Plus, we can already pass other computed arguments
mostly unchanged between `nunit3-console` and `dotnet test`, why should `where` be any different? The NUnit engine used by both the console
runner and the vstest adapter supports the Test Selection Language, why not just expose the functionality the same way?

## The Solution 
{:#tldr}
Clearly something had to be done, so I rolled up my sleeves and dug into the NUnit engine and vstest adapter code to see what I could do. A couple days
of hacking later, and I submitted a [pull request](https://github.com/nunit/nunit3-vs-adapter/pull/669) adding just this functionality to the 
NUnit VS Adapter Settings. Any existing `where` argument can be specified in the runsettings and it should work exactly the same as if it
were passed to the console runner's command line. This allows us to take an existing invocation of the console runner like:
```sh
nunit3-console TestAssembly.dll --where="cat == SomeCategory or method == SomeMethodName or namespace == My.Name.Space or name == 'TestMethod(5)'"
```
and pass the same filter unmodified to the test runner on .NET Core like:
```sh
dotnet test -- NUnit.Where="cat == SomeCategory or method == SomeMethodName or namespace == My.Name.Space or name == 'TestMethod(5)'"
```
And the exact same subset of tests should run.

Filtering can also be configured in a `.runsettings` file like so:

```xml
<RunSettings>
    <NUnit>
        <Where>cat == SomeCategory or method == SomeMethodName or namespace == My.Name.Space or name == 'TestMethod(5)'</Where>
    </NUnit>
</RunSettings> 
```

And passed to `dotnet test -s [filename].runsettings`, `dotnet vstest --settings:[filename].runsettings`, or `vstest.console.exe /settings:[filename].runsettings`

## Summary

There are a number of ways to configure the NUnit Test engine when running tests, using either the console runner or runsettings with vstest.
In the next release of the vstest adapter, it will be possible to pass a NUnit Test Selection Language filter to the test engine using a `NUnit.Where`
runsetting. This will allow filters that work with the existing console runner to be passed, unmodified, to the `dotnet test` command line.

Overtime, I would suspect more options and features to be added to the VS Test Adapter as it's the most natural way to run NUnit tests under
.NET Core.
