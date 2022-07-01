---
layout: post
title: Migrating legacy projects to .Net Core/Standard
date: 2019-01-07 08:42:52 UTC
updated: 2019-01-07 08:42:52 UTC
comments: false
categories: .net core .net standard legacy migration. tools useful
---

[![](https://4.bp.blogspot.com/-vH0H23rGVgM/XB9zzP4FndI/AAAAAAAAFIQ/WCAra5e_dsAi0MMLNfnuaKA4clrw3-1pACLcBGAs/s1600/ecosystem_grid_v2.png)](https://4.bp.blogspot.com/-vH0H23rGVgM/XB9zzP4FndI/AAAAAAAAFIQ/WCAra5e_dsAi0MMLNfnuaKA4clrw3-1pACLcBGAs/s1600/ecosystem_grid_v2.png)

## Introduction

After the second version of .NET Core, it is pretty likely to be the future for .NET development, at least for many years. Luckily for legacy projects, Microsoft still continues supporting .NET Framework and developing new versions, such as .NET framework 4.8, which currently available as early access [https://github.com/Microsoft/dotnet-framework-early-access](https://github.com/Microsoft/dotnet-framework-early-access). Even though, .NET Core provides some benefits that might be considered to port your legacy projects from .NET Framework to .NET Core. According to this article [Choosing between .NET Core and .NET Framework for server apps](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server), these are the points that you should consider to choose between .NET Core or .NET Framework:

**Use .NET Core for your server application when:**

- You have cross-platform needs.
- You are targeting microservices.
- You are using Docker containers.
- You need high-performance and scalable systems.
- You need side-by-side .NET versions per application.

**Use .NET Framework for your server application when:**

- Your app currently uses .NET Framework (recommendation is to extend instead of migrating).
- Your app uses third-party .NET libraries or NuGet packages not available for .NET Core.
- Your app uses .NET technologies that aren't available for .NET Core.
- Your app uses a platform that doesn’t support .NET Core.

So as conclusion, use .NET Core if you can, because there are more benefits. For this reason, if you start a new project you should consider .NET Core as first option. But, what happens with legacy projects? According to the previous article, they recommend extend legacy project with .NET Core, instead of migrating. However, I would say "it depends on the project". Calculate the cost of migrating and then decide, because using .NET Core will be rewarding in the long run if it is possible to migrate your project easily.

In addition, if you have some cross-cutting infrastructure libraries shared by your different services, you should consider a migration to .NET Standard for those libraries, so new micro-services or projects will be able to use .NET Core. Remember you can make your libraries compatible with .NET Framework and .NET Standard at the same time, which can be very handy to support legacy and new projects.

## Migrating analysis for Legacy Projects to .NET Core/.NET Standard

To illustrate how we can analyze the migration of a legacy project to .NET Core/Standard, I will use a DDD example project shared as open source on GitHub: [https://github.com/sandipray63in/DDD-Based-Enterprise-Application-Framework](https://github.com/sandipray63in/DDD-Based-Enterprise-Application-Framework)

In order to analyze the migration of a legacy project, we can use a very useful Visual Studio extension called .NET Portability Analyzer. You can download this extension here: [https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer.](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)  
After installing .NET Portability Analyzer, we can configure which parameters we want to analyze in or project. To do so, right click on your solution and you will see a new option "Portability Analyzer Settings".

[![](https://3.bp.blogspot.com/-8lPEqWGsSp8/XB9yale1vaI/AAAAAAAAFHw/AUjlVGDn6bk_k-rANIratl4iUNdqF1TIgCLcBGAs/s400/1.JPG)](https://3.bp.blogspot.com/-8lPEqWGsSp8/XB9yale1vaI/AAAAAAAAFHw/AUjlVGDn6bk_k-rANIratl4iUNdqF1TIgCLcBGAs/s1600/1.JPG)

In Options window, you can set up your settings, depending on the analysis you want to generate.

[![](https://1.bp.blogspot.com/-ZAsMvRL_QJ8/XB9yaqLepCI/AAAAAAAAFH0/SPI02m3AMdMM6BZHPmJxSCTa-UL3pb-NQCLcBGAs/s640/2.JPG)](https://1.bp.blogspot.com/-ZAsMvRL_QJ8/XB9yaqLepCI/AAAAAAAAFH0/SPI02m3AMdMM6BZHPmJxSCTa-UL3pb-NQCLcBGAs/s1600/2.JPG)

Now, in right-click menu, select "Analyze Assembly Portability" and after a few seconds (depending on the size of your project), you will have a new report. For the DDD project example, we can see we have a very high rate of compatibility with .NET Core. The project more problematic would be RestfulWebAPI with a 74.95% of compatible code.

[![](https://4.bp.blogspot.com/-1WEYxsX_F5E/XB9yakJ76pI/AAAAAAAAFH4/AjAtU7Dbz0govNNcwjzzsQZHZDDSVioMACLcBGAs/s640/3.JPG)](https://4.bp.blogspot.com/-1WEYxsX_F5E/XB9yakJ76pI/AAAAAAAAFH4/AjAtU7Dbz0govNNcwjzzsQZHZDDSVioMACLcBGAs/s1600/3.JPG)

In the second tab of the report, we can see the details of the compatibility problems that we have, so we can see the assemblies with classes and namespaces not supported by each platform.

[![](https://2.bp.blogspot.com/-8r0XzEKhlUc/XB9ybJsXDZI/AAAAAAAAFH8/iGLQ9nXwE_M8tGYlY9WvCJ8qcjzsVr1GgCLcBGAs/s640/4.JPG)](https://2.bp.blogspot.com/-8r0XzEKhlUc/XB9ybJsXDZI/AAAAAAAAFH8/iGLQ9nXwE_M8tGYlY9WvCJ8qcjzsVr1GgCLcBGAs/s1600/4.JPG)

Considering this report, we should have an estimation of the different incompatibilities of our project to migrate it to .NET Core/Standard. Depending of the incompatibilities, we should see if we have a similar functionality in .Net Core/Standard or we have to change our source code, which would involve extra work.

I hope you found this post useful.

Happy conding! :)
