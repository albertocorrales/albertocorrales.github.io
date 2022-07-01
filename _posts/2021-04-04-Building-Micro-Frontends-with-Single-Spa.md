---
layout: post
title: Building Micro-Frontends with Single-Spa
date: 2021-04-04 13:58:30 UTC
updated: 2021-04-04 13:58:30 UTC
comments: false
categories: architecture frontend micro-frontends react single-spa spa
cover-img: https://1.bp.blogspot.com/-RgWJcBmk7hg/YDQVP92nC6I/AAAAAAAAGcs/IxcU2ZG2BKcicXjWvCZaC81ZsTk1fK2LgCLcBGAsYHQ/s563/microfrontends.png
thumbnail-img: https://1.bp.blogspot.com/-RgWJcBmk7hg/YDQVP92nC6I/AAAAAAAAGcs/IxcU2ZG2BKcicXjWvCZaC81ZsTk1fK2LgCLcBGAsYHQ/s563/microfrontends.png
share-img: https://1.bp.blogspot.com/-RgWJcBmk7hg/YDQVP92nC6I/AAAAAAAAGcs/IxcU2ZG2BKcicXjWvCZaC81ZsTk1fK2LgCLcBGAsYHQ/s563/microfrontends.png
tags: []
---

## Introduction

Micro-services architecture is becoming trendy since it is a suitable option many projects, specially those that are complex and we want to be able spilt our business logic into different services that can be built and deliver independently. In spite of the fact that micro-services architectures are more complex, they increase the resilience of your system and increase the time-to-market delivery, which can be crucial for many companies.

But it is not all about services and backend. With the introduction of single page applications (SPAs), we can build fully-functional user interfaces that can work independently in the browser. And here we should ask ourselves the same questions as for backend applications: Is my SPA becoming a huge monolith? Would I like to deliver functional modules independently? If you using micro-services, is there a relation between my services and your UI? Do we want to have functional teams that can deliver new features (frontend+backend) independently? If your answer to most of those questions is 'yes', you might need to consider micro-frontend architectures.

## What are Micro-Frontends?

Here we have one of my favourite posts about micro-frontends: [https://martinfowler.com/articles/micro-frontends.html](https://martinfowler.com/articles/micro-frontends.html). In particular, this image summarizes quite well the importance of micro-frontends:

[![](https://1.bp.blogspot.com/-8NxeDb9eIlM/YDIwqLfFxwI/AAAAAAAAGcE/-9ejhlORY6M3xo8cOyJtfrYUVWlGERJUACLcBGAsYHQ/w640-h214/deployment.png)](https://1.bp.blogspot.com/-8NxeDb9eIlM/YDIwqLfFxwI/AAAAAAAAGcE/-9ejhlORY6M3xo8cOyJtfrYUVWlGERJUACLcBGAsYHQ/s1041/deployment.png)

So, when it comes to micro-frontend good practices and principles, there is a list of points that I consider important in the design:

1.  Each micro-frontend should be independently developed and delivered.
2.  Micro-frontends should load efficiently by using lazy-loading.
3.  We should be able to reuse common stuff, like styles, auth, frameworks in an efficient way.
4.  Communication between micro-frontends should be allowed in a decoupled and async way, and it shouldn't be too chatty.
5.  We should be allowed to use micro-frontends with different technologies.

## Single-Spa

Technology-wise, there are some popular options available (here there is [an article](https://itnext.io/11-micro-frontends-frameworks-you-should-know-b66913b9cd20) with some of the most trendy ones).

I've been comparing some of the frameworks of that list, and Single-Spa ([https://single-spa.js.org/](https://single-spa.js.org/)) seems the most suitable candidate for many reasons:

- It allows you to build and deliver your micro-frontends independently. In fact, they recommend having separated repositories and pipelines for each of your micro-frontends.
- It supports lazy loading. So your micro-frontends will be loading as you need them, instead of having a massive loading time at the beginning.
- It allows reusability of code and styles. In order to support this, you have multiple alternatives, like utility-modules or parcels.
- Data loading for shared modules is efficient. To do this, the preference is [Import-maps](https://github.com/WICG/import-maps), but [Module federation](https://dev.to/marais/webpack-5-and-module-federation-4j1i) is also also supported.
- Communication between micro-frontends is supported. Here the preference is [cross microfrontend imports](https://single-spa.js.org/docs/recommended-setup#cross-microfrontend-imports), but other alternatives like custom events or redux are also supported.
- Single-Spa doesn't depend on any frameworks. Under the hood, it works with javascript and webpack. In addition, it offers [helper libraries](https://single-spa.js.org/docs/ecosystem#help-for-frameworks) for the most trendy frameworks.
- Single-Spa is [open source](https://github.com/single-spa/single-spa) and the community support is strong. The documentation and the number of examples is quite good.
- You can mount micro-frontends dynamically with [import-map-overrides](https://github.com/joeldenning/import-map-overrides). This feature is just awesome. It allows you to inject one or several micro-frontends, so you don't have to run everything locally.
- There is a CLI ([create-single-spa](https://single-spa.js.org/docs/create-single-spa)), which makes really easy to create new micro-frontends with the initial scaffolding.

And here some caveats:

- Micro-frontends are more complex than monolith SPAs. This is not only for Single-Spa, but for every micro-frontend architecture, since distributed systems are always more complex.
- If you are used to working with one SPA and CRA, it can be challenging at the beginning and you might need to learn some concepts, specially about Webpack, since Single-Spa doesn't support CRA.

## Single-Spa with Create-React-App

In the previous caveats list I mentioned "Single-Spa doesn't support CRA". Well, that is not true. They prefer using Webpack directly, but CRA under the hood uses Webpack. So, with the proper configuration, CRA can work with Single-Spa.

On the internet, you can find some articles or snippets to give you a gist of how this needs to be configured. However, I haven't found any full examples. So I built my own example application, that you can find on my GitHub: [https://github.com/albertocorrales/single-spa-cra-example](https://github.com/albertocorrales/single-spa-cra-example).

In order to build a micro-frontend with Single-Spa and CRA you need to do the following steps:

### 1) Create a new micro-frontend with create-react-app

You can just create a new app with the following command

      npx create-react-app app1 --template typescript

I normally work with typescript [for many reasons](https://www.albertocorrales.com/2019/07/benefits-of-using-typescript-in-your.html), but this example will also work with javascript.

### 2) Install and configure react-app-rewired

In order to be able to change CRA pre-configuration, you need to install a package like [react-app-rewired](https://github.com/timarney/react-app-rewired).

Execute this command:

    npm install react-app-rewired --save-dev

### 3) Configure react-app-rewired

First of all, edit package.json to add rewired script (optionally, you can keep cra scripts):

Then, in the root of your app, add a new file "config-overrides.js" with the following content:

```js
module.exports = {
  webpack(config, env) {
    config.entry = "./src/index-single-spa.tsx";
    config.output = {
      ...config.output,
      filename: "@company/app1.js",
      libraryTarget: "system",
    };
    config.plugins = config.plugins.filter(
      (plugin) =>
        plugin.constructor.name !== "HtmlWebpackPlugin" &&
        plugin.constructor.name !== "MiniCssExtractPlugin"
    );
    delete config.optimization;
    return config;
  },
  devServer(configFunction) {
    return function (proxy, allowedHost) {
      const config = configFunction(proxy, allowedHost);
      config.disableHostCheck = true;
      config.headers = config.headers || {};
      config.headers["Access-Control-Allow-Origin"] = "*";
      return config;
    };
  },
};
```

### 4) Add single spa support

Install package "single-spa-react" with this command:

      npm i -D single-spa-react

Then, create index-single-spa.tsx, which will be your entry point for your micro-frontend:

```js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
// Note that SingleSpaContext is a react@16.3 (if available) context that provides the singleSpa props
import singleSpaReact from "single-spa-react";

const reactLifecycles = singleSpaReact({
  React,
  ReactDOM,
  rootComponent: App,
  errorBoundary(err, info, props) {
    // https://reactjs.org/docs/error-boundaries.html
    return <div>This renders when a catastrophic error occurs</div>;
  },
});

export const bootstrap = reactLifecycles.bootstrap;
export const mount = reactLifecycles.mount;
export const unmount = reactLifecycles.unmount;
```

At this point, your CRA app is ready to run as single-spa micro-frontend. If you run the command "npm start", you should be able to see your javascript file on [http://localhost:3000/@company/app1.js](http://localhost:3000/@company/app1.js).

### 5) Add your micro-frontend to root application

Now it is time to create a root-config app with Single-Spa and load our micro-frontend. To do so, create a new root-config app with command:

      create-single-spa --moduleType root-config

Finally, in your file index.ejs, replace example "@single-spa/welcome" with your new micro-frontend "@compan/app1".  Now, if you run "npm startW for the root application, you should be able to see your micro-service working on [http://localhost:9000/](http://localhost:9000)

## Conclusions

In this post, we have seen some principles and good design practices about micro-frontends. Technology-wise, Single-Spa is a strong candidate, which offers a powerful framework, which tries to simplify building micro-frontends.

In spite of the fact that Single-Spa includes a CLI what helps to create new micro-frontends, you could still using Create-React-App to run your CRA applications as Single-Spa micro-frontends, which can be a good starting point if are already working with CRA and you don't want to move to Webpack. However, in the long term, I would suggest to getting familiar with Webpack, since sooner or later you will need some configuration and not using CRA you will have more control over it.

**Update 04/04/2021**

Recently I've found [this plugin](https://github.com/hasanayan/craco-plugin-single-spa-application) based on [CRACO](https://github.com/gsoft-inc/craco) that also allows you to work with Singe-Spa and CRA. Here you have an example of this plugin configured with Single-Spa [https://github.com/albertocorrales/single-spa-cra-example/tree/main/app2](https://github.com/albertocorrales/single-spa-cra-example/tree/main/app2)
