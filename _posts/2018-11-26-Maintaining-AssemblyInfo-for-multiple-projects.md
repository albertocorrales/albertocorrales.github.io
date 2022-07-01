---
layout: post
title: Maintaining AssemblyInfo for multiple projects
date: 2018-11-26 23:10:44 UTC
updated: 2018-11-26 23:10:44 UTC
comments: false
categories: Custom Build MSBuild NuGet
---

[![](https://3.bp.blogspot.com/-QWWmpPO8tvI/W_x8fY7QPUI/AAAAAAAAFDk/kXO4LsgVv1UWumuR0iBwglIrcGQj6pIpwCLcBGAs/s400/msbuild.jpg)](https://3.bp.blogspot.com/-QWWmpPO8tvI/W_x8fY7QPUI/AAAAAAAAFDk/kXO4LsgVv1UWumuR0iBwglIrcGQj6pIpwCLcBGAs/s1600/msbuild.jpg)

When you are developing an application or manly a NuGet package, you might want to keep the same AssemblyInfo for all your packages, which involves updating each project when you want to publish a new version of your package according to the SemVer convention [Semantic Versioning 2.0.0 | Semantic Versioning](https://semver.org/).

In order to make it easier, with the improvements of dotnet core and the new csproj syntax, which I strongly recommend, MSBuild 15 introduced a pretty cool feature: Solution-wide project properties with Directory.Build.props [Customize your build - Visual Studio | Microsoft Docs](https://docs.microsoft.com/en-us/visualstudio/msbuild/customize-your-build?view=vs-2017). Basically, this allows you to define certain properties and use them in all your project in a centralised way, so you don't have to update your projects one by one.

All you have to do is create a new text file named Directory.Build.props and place it where you have your solution file. Here is an example of the properties you can use:

```xml
‍<Project>
  <PropertyGroup>
    <Version>1.1.0</Version>
    <FileVersion>1.1.0</FileVersion>
    <Product>Fenergo.Platform.Common</Product>
    <Company>Fenergo</Company>
    <Copyright>Copyright © Fenergo 2018</Copyright>
  </PropertyGroup>
</Project>‍‍‍‍‍‍‍‍‍
```

In addition, you can inherit another funcionalities such as analysers. For example, if you want to use the Stylecop.Analyzers for all your project, you can add a **Directory.Build.props** like this:

```xml
<Project>
  <PropertyGroup>
    <TreatWarningsAsErrors>True</TreatWarningsAsErrors>
  </PropertyGroup>
  <ItemGroup>
    <AdditionalFiles Include="..\\StyleCop.json" />
  </ItemGroup>
  <ItemGroup>
    <PackageReference Include="StyleCop.Analyzers" Version="1.0.2" />
  </ItemGroup>
</Project>
```

If you don't have your **Directory.Build.props** in your root directory, you can import it in the projects which you need it.

```xml
<Project>
  <!-- Import parent Directory.build.props -->
  <Import Project="../Directory.Build.props" />

  <!-- Properties common to all test projects -->
  <!-- ... -->
</Project>
```

I hope you found this post helpful,

Happy coding!

Alberto.
