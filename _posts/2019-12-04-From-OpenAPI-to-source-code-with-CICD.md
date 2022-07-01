---
layout: post
title: From OpenAPI to source code with CI/CD
date: 2019-12-04 22:17:06 UTC
updated: 2019-12-04 22:17:06 UTC
comments: false
categories: CI/CD NPM NuGet OpenApi typescript Web API web services
---

[![](https://1.bp.blogspot.com/-Er3RXxo4b1U/XayBY6Xr29I/AAAAAAAAFv4/_wAFNlHk1TALvcTH_DBfLI8bd3o_vHizgCLcBGAsYHQ/s640/1_Qf94xFwde421J_ZSmdPJDw1.png)](https://1.bp.blogspot.com/-Er3RXxo4b1U/XayBY6Xr29I/AAAAAAAAFv4/_wAFNlHk1TALvcTH_DBfLI8bd3o_vHizgCLcBGAsYHQ/s1600/1_Qf94xFwde421J_ZSmdPJDw1.png)

## Introduction

In the era of Microservices, you are probably designing or consuming one or more Web Services. As a result, you might be aware of the importance of having a good definition of your web services, since that is what clients and developers will use to know how a web service can be used.

For that reason, you should have a clear and easy way to define and understand your Web API. In order to help you to make a good definition we have [REST (Representational state transfer)](https://restfulapi.net/), which is a software architectural style that defines a set of rules to create Web Services. I'm not going to go into details about RESTful, because there is enough material for a full post and it is out of the scope of this post. But I might write another post about REST, since even nowadays I see some crazy stuff when it comes to Web APIs design.

Well, imagine we already have a really awesome definition for the resources of our Web API, and your clients are going to be really excited to use it, but...how do I let them know which are my endpoints, models, message errors, etc?

In order to provide a way to define our REST APIs, in 2011 Swagger created the first version for RESTful APIs specification. Over the subsequent years, Swagger specification has been evolving. In 2017, the Open APIInitiative, under the sponsorship of the Linux Foundation, published the [OpenAPI Specification v3.0.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md), which is the consequence of the evolution of RESTful API definition and it is considered the standard to define REST APIs nowadays.

In this article we will see the benefits of having an OpenAPI contract and all the possibilities that it can offer you, such as generating source code automatically form your definition and distributing it among your clients.

## Generating your OpenAPI contract

If you are working with Web Services, I strongly recommend that you follow a contract-first approach. No matter if your consumers are external to your organisation, if you define the contract first, you will have several benefits:

- Separation of concerns.
- Avoid blocks between teams (frontend and backend).
- Good and consistent communication between teams.
- A clear definition of your API from the beginning.
- Easy documentation.
- Consistent API models.
- Reusability and code generation.
- Easy versioning control.

In order to define your OpenAPI contract, you can use multiple tools. However, I would like to recommend two of them, one to work locally and another to work in the cloud.

### Generating your contract from VS Code

If you prefer working offline, there is a nice extension for VS Code to edit your API definition called OpenAPI (Swagger) Editor ([https://github.com/42Crunch/vscode-openapi](https://github.com/42Crunch/vscode-openapi)).

By using this editor, you will have a GUI to edit your contract and you will have your contract edited in real time.

[![](https://1.bp.blogspot.com/-nJqE5cg0sWs/XbXG3HM5WCI/AAAAAAAAFwk/RuCDnJnw_HA3Ek2qbLCYrw1jbBobMgoFwCLcBGAsYHQ/s640/vscode.PNG)](https://1.bp.blogspot.com/-nJqE5cg0sWs/XbXG3HM5WCI/AAAAAAAAFwk/RuCDnJnw_HA3Ek2qbLCYrw1jbBobMgoFwCLcBGAsYHQ/s1600/vscode.PNG)

By using VS commands, you will have also a list of commands that can help you to add new elements into your OpenAPI contract:

[![](https://1.bp.blogspot.com/-3442dzASpJM/XbXHz9Q-1qI/AAAAAAAAFww/E2qlVFvLrykDbN0xzC50wTjnXujWt7Z1wCLcBGAsYHQ/s640/commands.PNG)](https://1.bp.blogspot.com/-3442dzASpJM/XbXHz9Q-1qI/AAAAAAAAFww/E2qlVFvLrykDbN0xzC50wTjnXujWt7Z1wCLcBGAsYHQ/s1600/commands.PNG)

Finally, one cool feature added recently is the security audit, which can generate a report from your contract, where all the security issues detected are reported for you:

[![](https://1.bp.blogspot.com/-xb9y3hVrHoE/XbXIt4v0QmI/AAAAAAAAFw8/hBBaekOvsQooYp2W4gsPGqHzciDbj7CUwCLcBGAsYHQ/s400/security.PNG)](https://1.bp.blogspot.com/-xb9y3hVrHoE/XbXIt4v0QmI/AAAAAAAAFw8/hBBaekOvsQooYp2W4gsPGqHzciDbj7CUwCLcBGAsYHQ/s1600/security.PNG)

### Generating your contract on the cloud

If you prefer working on the cloud, the tool that I like the most is _apicurio_ ([https://studio.apicur.io](https://studio.apicur.io/)), which is also open source [https://github.com/Apicurio/apicurio-studio](https://github.com/Apicurio/apicurio-studio). With _apicurio_, you will be able to define and modify your OpenAPI contract from your favourite browser. In addition, you can invite collaborators and have a history with the changes that are made to your contract.

[![](https://1.bp.blogspot.com/-7djrocO5Rok/XbXJP2o2mmI/AAAAAAAAFxE/mi-omJ9lc5Ie3Y5kE7E-uXyJcXhVrVrHgCLcBGAsYHQ/s640/apicurio.PNG)](https://1.bp.blogspot.com/-7djrocO5Rok/XbXJP2o2mmI/AAAAAAAAFxE/mi-omJ9lc5Ie3Y5kE7E-uXyJcXhVrVrHgCLcBGAsYHQ/s1600/apicurio.PNG)

Apart from helping you with your API definition, apicurio can generate documentation from your OpenAPI contract, which can be especially interesting if you are working with third party clients or business people.

[![](https://1.bp.blogspot.com/-cqPztpV-Tdo/XbXLXAT1DtI/AAAAAAAAFxU/FEk9rAzANWgo5jd1kQY9XFbJVEVhzUZzACLcBGAsYHQ/s640/docu.PNG)](https://1.bp.blogspot.com/-cqPztpV-Tdo/XbXLXAT1DtI/AAAAAAAAFxU/FEk9rAzANWgo5jd1kQY9XFbJVEVhzUZzACLcBGAsYHQ/s1600/docu.PNG)

If you want to know more about tools for editing your OpenAPI contracts, I suggest having a look at this URL: [https://openapi.tools/#gui-editors](https://openapi.tools/#gui-editors)

## Generating Code from your contract

Apart from having a clear definition of what your clients can expect from your API, OpenAPI contracts offer a lot of additional advantages. One advantage that I found extremely useful is generating code from your contract.

During the latest months, I've been working in a project based on React and at the beginning the client code to consume APIs was implemented manually. As a consequence, when there was change in a service, you had to find out what part of the code you had to change to update to a newer version of the contract.

To make things worse, the project was implemented in JavaScript. In other words, mistakes during manual updates of your API code could lead to run-time errors. Considering the fact that our client application was going to consume multiple services in constant evolution, the manual coding approach didn't seem a good option.

One of the best contribution that we had was adding Typescript support. By using Typescript (as I analysed in my previous post [Benefits of using TypeScript in your project](https://www.albertocorrales.com/2019/07/benefits-of-using-typescript-in-your.html)), one of the greatest advantages is having errors in compilation time. As a result, in spite of having manual updates for contracts, our changes were consistent through the application and real-time errors were reduced to a large extent.

With this scenario, another improvement that I introduced is generating our code from OpenAPI contract. By doing so, we are saving a large amount of time, as no manual changes in our code are required anymore. In addition, when we update a contract for any API, we will have feedback about compilation errors if we have breaking changes, which leads to a smooth and safer maintenance of our client code.

In order to generate your code from an OpenAPI contract, there are multiple alternatives. Particularly, we are using _openapi-generator_ ([https://github.com/OpenAPITools/openapi-generator](https://github.com/OpenAPITools/openapi-generator)), due to its multiple possibilities to be used by almost any existing framework or language.

I personally use _openapi-generator_ as an npm package, so the first step is to install the package globally:  
npm install @openapitools/openapi-generator-cli -g

Next, I can configure the generator to generate typescript client code with the following command:  
openapi-generator generate -i MyOpenApiContract.json -g typescript-fetch

## Distributing your API definition with CI/CD

At this point you already know how you can generate code from your OpenAPI contract. However, in order to use that code for multiple projects, as it is likely to happen for Web APIs, you might want to pack the generated code and publish it, so all your clients will be able to get the latest version by using your packages.

In order to make this process really easy, I came up with the idea of creating and publishing an NPM package from the source code generated by _openapi-generator_. Furthermore, I included CI/CD, so I will publish automatically a new version of my package each time I modify my contract.

[![](https://1.bp.blogspot.com/-mp3dz2gHWXM/XdU05fz3ogI/AAAAAAAAF1E/3FJcJKREF1oHlZ9srI0ludW27WJWOtY0gCLcBGAsYHQ/s1600/Untitled%2BDiagram.png)](https://1.bp.blogspot.com/-mp3dz2gHWXM/XdU05fz3ogI/AAAAAAAAF1E/3FJcJKREF1oHlZ9srI0ludW27WJWOtY0gCLcBGAsYHQ/s1600/Untitled%2BDiagram.png)

In my case, I'm using Azure DevOps, so I have a repository where I store my contract and my build will be triggered always that a new change is detected. During the build process, I will execute some commands that I've defined in my file _package.json_:

    {    "name": "my-api",    "version": "1.0.0",    "private": false,    "repository": {        "type": "git"

If we have a look at my build pipeline, we can see the first two tasks, which are intended to read the version from the contract definition file and write it into my package.

In the third step, I will install all the packages that I need to execute the following steps.

Next, I generate my code by execute the command "npm run api-gen". As you can see in the package.json definition, this step is invoking the library _openapi-generator_ and subsequently, it is doing a replacement. This replacement is done because we are working with a more recent version of Typescript.

Next, during the step "Transpile" I execute the command "npm run tsc", which transpiles the typescript code into javascript + ts definitions. Finally, I publish the results of the transpilation as a build artifact.

[![](https://1.bp.blogspot.com/-YHy9gEUzKYY/XdVCXF4JWbI/AAAAAAAAF1Q/0xl90x7M8GAcq6w-5GRhcqsjlS72ixuVACLcBGAsYHQ/s640/ci.PNG)](https://1.bp.blogspot.com/-YHy9gEUzKYY/XdVCXF4JWbI/AAAAAAAAF1Q/0xl90x7M8GAcq6w-5GRhcqsjlS72ixuVACLcBGAsYHQ/s1600/ci.PNG)

After a successful build, my release definition basically reads version of the package, concatenates the number of the build, and publishes the package as an NPM package.

[![](https://1.bp.blogspot.com/-dwtBaFHcxJU/XdVCXZxAe-I/AAAAAAAAF1U/CppunWztDn4OeD7TF-3E9VVhogvg4Ar6gCLcBGAsYHQ/s640/cd.PNG)](https://1.bp.blogspot.com/-dwtBaFHcxJU/XdVCXZxAe-I/AAAAAAAAF1U/CppunWztDn4OeD7TF-3E9VVhogvg4Ar6gCLcBGAsYHQ/s1600/cd.PNG)

## Conclusions

In this article, we introduced OpenAPI and the importance of using contracts to define your Web APIs.

Subsequently, we have saw how you can generate your typescript client source code from an OpenAPI contract, which can be extremely useful and can save you time developing and maintaining that code manually.

Finally, we analysed the idea of publishing packages from your OpenAPI contract automatically, by using CI/CD. As a result, our clients will be able to get new versions of your Web API and consume it easily.

This process can be extended, since *openapi-generator* supports multiple languages and server code generation, so there are a lot of scenarios that this process might cover when it comes to automatise source code generation. For example, here I showed how we can generate an NPM package, but I'm also using this process to generate and publish a dotnet Nuget package.
