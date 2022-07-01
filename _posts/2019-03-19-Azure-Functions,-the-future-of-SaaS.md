---
layout: post
title: Azure Functions, the future of SaaS
date: 2019-03-19 09:39:34 UTC
updated: 2019-03-19 09:39:34 UTC
comments: false
categories: azure azure functions DI OpenApi SaaS Visual Studio
---

## [![](https://3.bp.blogspot.com/-5PZq6OtF0v4/XI6hbtwN8KI/AAAAAAAAFUo/x8VlVbKhaK0RTkAy1SLWoQvkVctmDytKgCLcBGAs/s640/Azure-Functions-1.png)](https://3.bp.blogspot.com/-5PZq6OtF0v4/XI6hbtwN8KI/AAAAAAAAFUo/x8VlVbKhaK0RTkAy1SLWoQvkVctmDytKgCLcBGAs/s1600/Azure-Functions-1.png)

## Introduction

Recently, I have had the chance to use Azure Functions as candidate for the design and development of a service, which will be delivered as Software as a Service (SaaS). Azure Functions is a relatively recent technology, and as a consequence, I have realized many people didn't heard of it yet. For this reason, I would like to write this post as a brief introduction and my personal experience using it at the present.

## What is Azure Functions?

Azure Functions provide a framework to build and manage easily serverless oriented architectures. The idea behind Azure Functions  is that you have a piece of code, which provides a functionality. In order to execute your piece of code, Azure functions introduce the concept of "_trigger_". A trigger can be an HTTP request, a timer, a message in a queue, a modification on a Blog, etc. In addition, Azure Functions provides the concept of "_bindings_" Binding to a function is a way of declaratively connecting another resource to the function; bindings may be connected as input bindings, output bindings, or both. Data from bindings is provided to the function as parameters. Here you can see the triggers&bindings supported by Azure functions at this moment: [https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings)

Azure Functions offers build-in runtime scaling and you will pay only for the request you have and the resources you need in your functions. This business model makes specially interesting Azure Functions when you want to deploy your business logic without caring about the infrastructure. (More info here: [https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale))

Currently, Azure Functions implements two versions. Version 1.x is supports .NET development from the portal or Windows machines and uses the .NET Framework. 1.x supports C#, JavaScript, and F#, with experimental support for Python, PHP, TypeScript, Batch, Bash, and PowerShell.

Version 2.x **is in preview**. It leverages .NET Core and supports cross-platform development on Windows, macOS, and Linux machines. 2.x adds first-class support for Java but doesn't yet directly support any of the experimental languages. Version 2.x uses a new binding extensibility model that enables third-party extensions to the platform, independent versioning of bindings, and a more streamlined execution environment.

In my last approach, I have used version 2, as I wanted to get all the benefits introduced by dotnet core.

## Creating a new Azure Function from Azure

One of the key features of Azure Function is the flexibility to turn your code into functionality. For this reason, you can write your function directly on Azure and it will be available without care about deployment, infrastructure, etc.

To do so, go to your azure account and create a new Azure App. Then, choose In-portal.

[![](https://1.bp.blogspot.com/-g6UqiN6VgTQ/XI6nqig_L1I/AAAAAAAAFU0/svxHgDUqTQI4jWDUfA6fXbitogce5RWEgCLcBGAs/s640/1.PNG)](https://1.bp.blogspot.com/-g6UqiN6VgTQ/XI6nqig_L1I/AAAAAAAAFU0/svxHgDUqTQI4jWDUfA6fXbitogce5RWEgCLcBGAs/s1600/1.PNG)

You will be able to select several triggers. For this example, we will use Webhook + Api:

[![](https://2.bp.blogspot.com/-DrnVt9dZl7I/XI6rONcUPvI/AAAAAAAAFVE/savooLBjmUoQKZnVQmvhVEyamtKXuPUjgCLcBGAs/s640/2.PNG)](https://2.bp.blogspot.com/-DrnVt9dZl7I/XI6rONcUPvI/AAAAAAAAFVE/savooLBjmUoQKZnVQmvhVEyamtKXuPUjgCLcBGAs/s1600/2.PNG)

Now, the only thing left to do is write your code. For this example, I will implement sum function, so the sum of two parameters will be returned as a result.

[![](https://3.bp.blogspot.com/-m4L4sjt2EqQ/XI6rODI9aTI/AAAAAAAAFVA/vBnOM5HGkeIZ3LvSTabtEKbsux2FyQGtACLcBGAs/s640/3.PNG)](https://3.bp.blogspot.com/-m4L4sjt2EqQ/XI6rODI9aTI/AAAAAAAAFVA/vBnOM5HGkeIZ3LvSTabtEKbsux2FyQGtACLcBGAs/s1600/3.PNG)

From the same Azure screen, you can test your function and we see the result obtained is as we expected.

[![](https://4.bp.blogspot.com/-Nd5MAKXGnCU/XI6sEPOqGxI/AAAAAAAAFVY/1Kx8Vv8lbGsSBgf2NmJuCfEDy2nPOnSPACLcBGAs/s1600/5.PNG)](https://4.bp.blogspot.com/-Nd5MAKXGnCU/XI6sEPOqGxI/AAAAAAAAFVY/1Kx8Vv8lbGsSBgf2NmJuCfEDy2nPOnSPACLcBGAs/s1600/5.PNG)[![](https://1.bp.blogspot.com/-xehtjpuItiE/XI6sEJ-yCHI/AAAAAAAAFVU/JZqLW6g4SVI7Op93A8u_8s76ukbCy085ACLcBGAs/s320/4.PNG)](https://1.bp.blogspot.com/-xehtjpuItiE/XI6sEJ-yCHI/AAAAAAAAFVU/JZqLW6g4SVI7Op93A8u_8s76ukbCy085ACLcBGAs/s1600/4.PNG)

## Creating a new Azure Function from Visual Studio

Creating functions from Azure portal can be handy, but it might not be the best choice to develop complex functions, where you want to use a code repository, automatic testing, CI/CD pipelines, etc. For real scenarios, you should use Visual Studio to develop your Azure Functions. However, it is as easier as creating them on Azure Portal.

To create a new Azure Function in Visual Studio, first select a new Azure Functions project:

[![](https://2.bp.blogspot.com/-UnInmBvN75o/XJAWJ0-XqUI/AAAAAAAAFV8/D-m7VHvvr9c6NV2Bqklv_N_6yg5JvDkbwCLcBGAs/s640/6.PNG)](https://2.bp.blogspot.com/-UnInmBvN75o/XJAWJ0-XqUI/AAAAAAAAFV8/D-m7VHvvr9c6NV2Bqklv_N_6yg5JvDkbwCLcBGAs/s1600/6.PNG)

Then, you will be able to select version, triggers, storage and access rights. For this example I will use Http trigger with version 2 (.net core)

[![](https://2.bp.blogspot.com/-S4LwFBe5-1g/XJAWJ-9a0OI/AAAAAAAAFWE/5qR0zXJCLPkCdNktSsOesmvwZPEBnErSwCLcBGAs/s640/7.PNG)](https://2.bp.blogspot.com/-S4LwFBe5-1g/XJAWJ-9a0OI/AAAAAAAAFWE/5qR0zXJCLPkCdNktSsOesmvwZPEBnErSwCLcBGAs/s1600/7.PNG)

Now all you have to do is writing your code, since the first time you execute it, Azure functions Tools will be installed automatically.  If you execute your function on a browser you will see something like this:

[![](https://4.bp.blogspot.com/-1IeRK3hxI4U/XJAWJ631D8I/AAAAAAAAFWA/U-9q6UQxS0wIWcQh5xV8bPzqN4Rz6gKBQCLcBGAs/s640/9.PNG)](https://4.bp.blogspot.com/-1IeRK3hxI4U/XJAWJ631D8I/AAAAAAAAFWA/U-9q6UQxS0wIWcQh5xV8bPzqN4Rz6gKBQCLcBGAs/s1600/9.PNG)

## Adding OpenApi definition and Dependency Injection

Once you start developing your Azure Functions as Azure Http triggers, you might want to add OpenApi definition for your clients and Dependency Injection to keep decoupled the implementation of your functions. And here is when you will realize Azure Functions might be not as mature as asp.net core, as it doesn't include this functionality out-of-the-box.

Fortunately, you will be able to find some temporary solutions until Azure Functions version 2 include it.

For example, for OpenApi, there is a NuGet package available, which allows you to use OpenApi with Swagger UI. [https://devkimchi.com/2019/02/02/introducing-swagger-ui-on-azure-functions](https://devkimchi.com/2019/02/02/introducing-swagger-ui-on-azure-functions) and the same for DI [https://devkimchi.com/2019/02/22/performing-constructor-injections-on-azure-functions-v2](https://devkimchi.com/2019/02/22/performing-constructor-injections-on-azure-functions-v2).

Here you can see all the source code to use those NuGet packages with Azure Functions on my GitHub repo:

> [https://github.com/albertocorrales/sum-azure-function](https://github.com/albertocorrales/sum-azure-function)

After configuring OpenApi and DI NuGet packages, you can see the example of this post running with Swagger going to the url [http://localhost:7071/api/swagger/ui](http://localhost:7071/api/swagger/ui)

[![](https://1.bp.blogspot.com/-zvNSnZ2Wb3w/XJA08cAvy5I/AAAAAAAAFWY/1Oi0ttaxROAyn9sjGmhkkjSKsV5R30rgwCLcBGAs/s640/10.PNG)](https://1.bp.blogspot.com/-zvNSnZ2Wb3w/XJA08cAvy5I/AAAAAAAAFWY/1Oi0ttaxROAyn9sjGmhkkjSKsV5R30rgwCLcBGAs/s1600/10.PNG)

However, from my point of view, this is a workaround and there are some disadvantages if we compare with dotnet core:

- To use Swagger plugin, you need to have a lot of classes (maybe these clases shoudl be included within the NuGet package)

[![](https://3.bp.blogspot.com/-DtCcAJV1NQg/XJA4HaetYiI/AAAAAAAAFWk/CFq3NTds6lg4f6n1VCRchp6gJs71fUk4gCLcBGAs/s320/11.PNG)](https://3.bp.blogspot.com/-DtCcAJV1NQg/XJA4HaetYiI/AAAAAAAAFWk/CFq3NTds6lg4f6n1VCRchp6gJs71fUk4gCLcBGAs/s1600/11.PNG)

- Swagger doesn't generate automatic documentation from XML comments for WebApi
- To support DI in your functions, you have to implement an empty interface (bad practice), which implements IFunction. However, there is another approach, which simplifies this and support \[Inject\] attribute in your http trigger definition [https://github.com/BorisWilhelms/azure-function-dependency-injection](https://github.com/BorisWilhelms/azure-function-dependency-injection)
- To load DI in StartUp class, your application must target .net standard 2.0. If you try to target .net core it doesn't work.

##  Conclusions

Azure Functions is a really promising technology and it might the future of SaaS and WebApi development. This is reason why all the competitors have their own implementation of the same concept. Some of them are: Azure functions, AWS lambdas, IBM OpenWhisk, Iron.io, Google Cloud functions, etc.

In spite of the fact that Azure Functions might seem an Azure technology, it supports Docker, so you can containerize your azure functions and deliver them as a container to other cloud technology or even to on-premise environments. The possibilities of Azure Functions are endless.

However, as we analyzed in this article, Azure Functions version 2 is still in preview version and there are some missing features, such as OpenApi or DI. In spite of the fact that there are some NuGet packages to provide this functionality, those are workarounds and the implementation has some lacks, if we compare with dotnet core.

For this reasons, until Azure Functions includes the missing functionality, I would suggest to use asp.net core to develop your Web Apis in the meantime. But keep an eye on Azure Functions, as it is very promising technology and it will be soon the future of SaaS.

## Learn more about Azure Functions

If you want to learn more about Azure Functions, I recommend you the following material:

- (eBook) Serverless apps: Architecture, patterns, and Azure implementation [https://dotnet.microsoft.com/learn/cloud/azure-architecture](https://dotnet.microsoft.com/learn/cloud/azure-architecture)
- Official Documentation: [https://docs.microsoft.com/en-us/azure/azure-functions](https://docs.microsoft.com/en-us/azure/azure-functions)

Pluralsight courses:

- Microsoft Azure Developer: Create Serverless Functions [https://app.pluralsight.com/library/courses/microsoft-azure-serverless-functions-create/table-of-contents](https://app.pluralsight.com/library/courses/microsoft-azure-serverless-functions-create/table-of-contents)
- Writing and Testing Precompiled Azure Functions in Visual Studio 2017 [https://app.pluralsight.com/library/courses/azure-functions-visual-studio/table-of-contents](https://app.pluralsight.com/library/courses/azure-functions-visual-studio/table-of-contents)

Azure functions is open source, so you can have a look at the source code:

- Azure Functions project: [https://github.com/Azure/Azure-Functions](https://github.com/Azure/Azure-Functions)
