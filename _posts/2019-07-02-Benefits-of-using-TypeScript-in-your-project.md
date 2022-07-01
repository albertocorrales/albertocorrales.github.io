---
layout: post
title: Benefits of using TypeScript in your project
date: 2019-07-02 07:10:28 UTC
updated: 2019-07-02 07:10:28 UTC
comments: false
categories: frontend good practices spa typescript
---

[![](https://1.bp.blogspot.com/-GYyVQzpRCOo/XReIUVWIZMI/AAAAAAAAFm8/zWeYtDVH1JwnAG5V8MliOtmIEShq4OhxQCLcBGAs/s640/react-native-typescript.jpg)](https://1.bp.blogspot.com/-GYyVQzpRCOo/XReIUVWIZMI/AAAAAAAAFm8/zWeYtDVH1JwnAG5V8MliOtmIEShq4OhxQCLcBGAs/s1600/react-native-typescript.jpg)

## Introduction

Since I started my career in Computer Sciences, I have had the chance to work with a huge variety of frontend and backend technologies. During that period, on the client side, I witnessed how the web has evolved, starting static html, then adding javascript, AJAX, later animations with flash, silverlight or applets, html5 and recently new frontend frameworks like Angular, Vue and React.....or the incoming and promising Blazor, of which I will talk in a future post.

So Javascript at the beginning was created to add some scripting capabilities to our static websites, which most of the times led to a lot of spaghetti code difficult to maintain. Ajax was also an important turning point, which allowed us to render our page dynamically, instead of having that ugly user experience produced by server-side rendering frameworks, such as MVC, JSP or PHP. And this fact moved the logic for web pages from server to client, producing a great client experience. However webs become complex systems developed with a language which was initially designing to develop basic scripts.

But then, modern frontend frameworks appeared. In particular, I had the change to migrate a "javascript SPA", which was really chaotic and difficult to maintain, to Angular (starting with version 2 beta), which included out of the box some first class features, such as modules, Dependency Injection, MVVM pattern, double binding, and my favourite: Typescript.

Currently, I'm working in a React project, which started using JavaScript. As a contribution, I added TypeScript support and I migrated existing JS code to TS. In this post, I would like to explain which are the advantages that TypeScript can offer you, in case you didn't know or you are considering to use typescript in a new or an existing project.

[![](https://1.bp.blogspot.com/-ILdKzgLDtLc/XRkchHiO4RI/AAAAAAAAFoI/mxAi4PDx_kAI89HBE_WFLqZx0ev7rLnngCLcBGAs/s320/gmarkx.jpg.jpg)](https://1.bp.blogspot.com/-ILdKzgLDtLc/XRkchHiO4RI/AAAAAAAAFoI/mxAi4PDx_kAI89HBE_WFLqZx0ev7rLnngCLcBGAs/s1600/gmarkx.jpg.jpg)

## What is Typescript?

First things first, lets define TypeScript:

TypeScript is a programming language developed and maintained by Microsoft. It is a strict syntactical superset of JavaScript, and adds optional static typing to the language.

According to the official web page ([https://www.typescriptlang.org/](https://www.typescriptlang.org/)), they define typescript as "javascript that scales", and this feature makes it a really interesting option for the current web paradigm, where we develop complex and complete client side applications (known as Single Page Applications), which need to follow the best software principles in order to have maintainable and scalable applications.

TypeScript is designed for development of large applications and transcompiles to JavaScript, which means, your TypeScript code will produce JavaScript, and this JavaScript will be execute by the browser.

TypeScript is an open source project, so you can have a look at the source code if you are interested: [https://github.com/microsoft/TypeScript](https://github.com/microsoft/TypeScript)

[![](https://1.bp.blogspot.com/-ZKXH0161qAM/XReSsBESQLI/AAAAAAAAFnI/7ApbyElLZfEtgLkVvjOKb-XHEATo61_tACLcBGAs/s400/typescript.jpg)](https://1.bp.blogspot.com/-ZKXH0161qAM/XReSsBESQLI/AAAAAAAAFnI/7ApbyElLZfEtgLkVvjOKb-XHEATo61_tACLcBGAs/s1600/typescript.jpg)

## Why Typescript?

Typescript is a really great technology, but you shouldn't choose a technology because it is cool or trendy. You should choose a technology because of the advantages that it can provide for your team and your project. For this reason, in this section I will try not to be too technical and I will focus on which are the benefits why I chose to use Typescript in my current React project, so the same advantages might apply for you too.

### Bugs reduction

This is my favourite advantage and that for that reason it is the first in my list. Typescirpt provides code compilation and types checking, which help to detect bugs for typos, names updating, types misusing, etc. In the past, I've heard arguments against typescript like "the cool part of javascript is not-typing things" or "typing things will be time consuming". But having a robust code, will save you a lot of time for bugfixing and maintenance, so I think it is absolutely rewarding. In fact, this can be more important that we think. As a example, Airbnb studied the improvement achieved and they concluded that **38% of bugs could have been avoided by using typescript**.

[![](https://1.bp.blogspot.com/-QVh2Ok_NYWE/XRecNphobjI/AAAAAAAAFng/ZCzMINwznFstYNwigDMv7j3IZu1pjvRswCLcBGAs/s640/tsairbnb.jpg)](https://1.bp.blogspot.com/-QVh2Ok_NYWE/XRecNphobjI/AAAAAAAAFng/ZCzMINwznFstYNwigDMv7j3IZu1pjvRswCLcBGAs/s1600/tsairbnb.jpg)

### Build maintainable code with SOLID principles

Those who are fullstack developers, like me, know very well the advantages of both worlds: frontend and backend. While frontend has been evolving a lot in the latest years, backend development was already robust and based on best practices well known and accepted by the community.

With the introduction of typescript, we can design and implement our frontend software with object oriented programming in a similar way as we have been done for many years in backend. The first consequence of this fact is that we can use design patterns, which will help us to build high maintainable software. As an example of this, here you have a repository where you can find all the most relevant design patterns implemented in typescript: [https://github.com/totaldesigner/typescript-design-patterns](https://github.com/totaldesigner/typescript-design-patterns).

But that is not all, going further, by using typescript we can also apply SOLID principles to our legacy javascript applications and architectures based on the best practices. This will produce highly maintainable and evolutionary applications. Here we can see an example for node with typescript, where SOLID principles are implemented: [https://dev.to/remojansen/implementing-the-onion-architecture-in-nodejs-with-typescript-and-inversifyjs-10ad](https://dev.to/remojansen/implementing-the-onion-architecture-in-nodejs-with-typescript-and-inversifyjs-10ad).

[![](https://1.bp.blogspot.com/-2LeA7JUjngk/XRfSqTQrIPI/AAAAAAAAFns/NdR8Qu1FdeITAfrRI9zkQYJbOPfNP82_QCLcBGAs/s320/dolid.png)](https://1.bp.blogspot.com/-2LeA7JUjngk/XRfSqTQrIPI/AAAAAAAAFns/NdR8Qu1FdeITAfrRI9zkQYJbOPfNP82_QCLcBGAs/s1600/dolid.png)

### Faster Development

If you are technical, I'm pretty sure you will find this point redundant. However, this is a point I would like to highlight too, since business people sometimes tend to think technology update is not really a necessity for the business. But building highly maintainable and scalable applications will lead to saving a large amount of time, or in other words, budget.

In addition, more robust code will involve less bugfixing, so again it will mean saving time and also improving the reputation with your clients. Last, this will keep your developers happier, as it is really frustrating wasting your time fixing bugs that could have been avoided and maintaining an application which is really tedious to maintain or refactor.

### Flexibility, compatibility and easy migration from JavaScript

One of the biggest advantages if you are considering to migrate a legacy javascript project to typescript is the fact that typescript is really flexible and compatible with javascript. Typescript is highly configurable and you decide if you want to make it compatible with javascript and not require types.

In my current project, this is the approach I followed. First, I configured it to support javascript and undefined types ('any' type in typescript language). However, right now, we converted all the javascript files to typescript and we are defining types for models progressively.

However, for new developments, you should define types if you added typescript support. This can be challenging for people who are used to developing javascript, but please, don't be lazy defining types.

[![](https://1.bp.blogspot.com/-G7RbkQKnsXM/XRsDXB4fGuI/AAAAAAAAFoo/I3kLSFkpn2kHhIB8DVKd18r0B49gCSFPACLcBGAs/s400/34r1zn.jpg)](https://1.bp.blogspot.com/-G7RbkQKnsXM/XRsDXB4fGuI/AAAAAAAAFoo/I3kLSFkpn2kHhIB8DVKd18r0B49gCSFPACLcBGAs/s1600/34r1zn.jpg)

### Clear Api Definition

Type definitions help you when you are consuming an Api, it doesn't matter if it is an Api library or a web Api. In addition, having types will help you to make your code functional programming oriented, so consumers of your api will easily know what they should provide to call a method and what result they should expect.

Having types definition is crucial when you want to share your code with others. In fact, (spoiler alert), I will write an article about this to demonstrate how easy-to-use can be our Api by using typescript.

### Browser compatibility

Typescript allows you to transpile javascript code which will be compatible with multiple browsers and versions. I know this can be also achieved with ECMA Script and babel, which is great, but in typescript you will have that out of the box. In fact, ECMA script and typescript are closer and closer in functionality, but there are some important features only supported by typescript, such as types, interfaces, generics, etc.

## Conclusion

In this article we have seen the benefits of using typescirpt in your project. I talked more about React in this article because I recently added TypeScript in a React project, but I strongly recommend it also for other technologies like Vue.js, Node.js, etc, since the benefits are identical.

If you are considering typescript in a new project, this is the best time to do it, as you will have your code typed from the beginning. But it is also a really good option for legacy javascript projects, since we can transform it progressively. In fact, this is the scenario where I'm applying it nowadays, and day-by-day we have more typed code. As a consequence, my project is becoming more maintainable and scalable.

Finally, I would like to recommend two courses to learn typescript:

- (Beginner) Getting Started with TypeScript; [https://app.pluralsight.com/library/courses/typescript-getting-started/table-of-contents](https://app.pluralsight.com/library/courses/typescript-getting-started/table-of-contents)
- (Beginner) TypeScript Fundamentals: [https://app.pluralsight.com/library/courses/typescript/table-of-contents](https://app.pluralsight.com/library/courses/typescript/table-of-contents)
- (Advanced) Advanced TypeScript [https://app.pluralsight.com/library/courses/typescript-advanced/table-of-contents](https://app.pluralsight.com/library/courses/typescript-advanced/table-of-contents)

If you don't have PluralSight subscription don't worry, remember that the best source of information is always the official documentation: [https://www.typescriptlang.org/docs/home.html](https://www.typescriptlang.org/docs/home.html)
