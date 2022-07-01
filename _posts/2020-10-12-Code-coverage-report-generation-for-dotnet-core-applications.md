---
layout: post
title: Code coverage report generation for dotnet core applications
date: 2020-10-12 13:48:45 UTC
updated: 2020-10-12 13:48:45 UTC
comments: false
categories: .net core 2020 code coverage code quality ReportGenerator testing unit tests
---

[![](https://1.bp.blogspot.com/-SO8NfJrUFXk/X4NHxlp9oMI/AAAAAAAAGUw/WfgN4IjZIHAoaS1X56YWjxyJ4Mj9RruOgCLcBGAsYHQ/w400-h225/integration-ui-unit-test.png)](https://1.bp.blogspot.com/-SO8NfJrUFXk/X4NHxlp9oMI/AAAAAAAAGUw/WfgN4IjZIHAoaS1X56YWjxyJ4Mj9RruOgCLcBGAsYHQ/s1417/integration-ui-unit-test.png)

In _"Working Effectively with Legacy Code"_ Michael Feathers introduced a definition of legacy code as code without tests, since a code without tests is difficult to maintain, extend and evolve. It doesn't matter if you are using the latest technologies, if you don't test your code, it will hard to change without breaking anything, which will make it rigid and difficult to maintain. Remember that unit tests are the base in the testing pyramid.

In order to ensure that you are covering with unit tests the most important part of our application, code coverage can be helpful to detect where you have lack of tests.

Code coverage is a tool offered by products like [ReSharper](https://www.jetbrains.com/dotcover/) or the [Enterprise version of Visual Studio](https://docs.microsoft.com/en-us/visualstudio/test/using-code-coverage-to-determine-how-much-code-is-being-tested?view=vs-2019). However, these products are not free and you might not want to pay for this essential feature.

But if you are working with dotnet core, it is quite easy to get a code coverage report thanks to [ReportGenerator](https://github.com/danielpalme/ReportGenerator) package.

In order to generate your tests, first you need to install ReportGenerator in your machine with this command:

`dotnet tool install -g dotnet-reportgenerator-globaltool`

Then you can run your tests and create coverage reports by using this command:

```
dotnet test --collect:"XPlat Code Coverage"
```

Finally, you can use reportgenerator command to generate a report with actual coverage in your code:

```
reportgenerator "-reports:./**/coverage.cobertura.xml" "-targetdir:./coverage" "-reporttypes:Html"
```

And that is all. Handy, right?. In addition, since I like to make it even more easy to execute, I've created a .bat file, which execute my tests, generates a coverage report, and opens chrome to display the report. As a result, I can update my coverage report with double-click. This is the code for my script:

```
dotnet test --collect:"XPlat Code Coverage"reportgenerator "-reports:./**/coverage.cobertura.xml" "-targetdir:./coverage" "-reporttypes:Html"start chrome ./coverage/index.html
```

In order to provide an example, I've created this GitHub project with some tests: [https://github.com/albertocorrales/code-coverage-report](https://github.com/albertocorrales/code-coverage-report)

When I execute my coverage.bat script, my browser is automatically opened displaying this coverage report.

[![](https://1.bp.blogspot.com/-whSfuzZBSJ8/X4NGUOVtrFI/AAAAAAAAGUk/8inPvpWAKgo4Kne1aSpMMuHK0taCMUUcgCLcBGAsYHQ/w640-h416/report.PNG)](https://1.bp.blogspot.com/-whSfuzZBSJ8/X4NGUOVtrFI/AAAAAAAAGUk/8inPvpWAKgo4Kne1aSpMMuHK0taCMUUcgCLcBGAsYHQ/s1386/report.PNG)

In our report we can see the coverage for each of our files and different metrics like line coverage, branch coverage, etc.

I hope you found helpful this article.
