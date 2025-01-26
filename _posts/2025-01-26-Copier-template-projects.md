---
layout: post
title: Templating tools to boost how you build consistent Microservices
date: 2025-01-26 10:00:00 UTC
updated: 2025-01-26 10:00:00 UTC
comments: false
tags: [Copier, Architecture, Tools]
image:
  path: /assets/img/2025-01-26/0.jpg
---

# Project Templating tools to boost how you build consistent Microservices

## Introduction

Templating tools are critical in modern microservices architecture, solving key challenges in rapid and consistent software development. They address three primary pain points:

1. **Standardization**: Ensure consistent project structure, configuration, and best practices across multiple services
2. **Productivity**: Dramatically reduce setup time by automating repetitive initialization tasks
3. **Scalability**: Enable quick replication of proven architectural patterns and configurations

In microservices environments, where teams often build multiple interconnected services, templating tools eliminate manual setup, reduce human error, and maintain architectural coherence. They allow developers to focus on unique business logic rather than boilerplate infrastructure.

## Templating Tools Comparison

In this section, we will analyze multiple alternatives to create, share and maintain templates that can help us to create templates that can be used as guidelines when we are creating new projects.

### Yeoman

[Yeoman](https://yeoman.io/) is an advanced templating tool that allows you to create your own generators, so you can create your template projects.

Its benefits include its many flexible automation use cases such as the ability to programmatically generate files in the template and even write unit tests on the generated files, which helps provide confidence in the templates and ensures developers have a productive and comfortable user experience.

While its customizability and flexibility are benefits, they also can become downsides — more complex templates can become more confusing for developers. Questions of maintaining and testing Yeoman-based projects arise. Setting up your generators and plugins can be time-consuming, although once they’re in place, Yeoman is relatively easy to use for developers. In order to create generators, it requires developing in JavaScript. Then, your generators should be published to NPM, so others can consume them.

### CookieCutter

[CookieCutter](https://www.cookiecutter.io/) is a templating engine that tries to simplify the way we create project templates. It is written in python, but it doesn’t require programming in python to create templates. You just need to create your project scaffolding and add your configuration in a file cookiecutter.json. With this file, cookie-cutter will ask for the prompt needed and it will replace the values in the corresponding parts following the jinja syntax.

Additionally, you can create scripts that are executed after or before copying the generated files.

CookieCutter doesn't require releasing packages, you can just use mercurial URLs as arguments and it works based on that.

### Copier

[Copier](https://copier.readthedocs.io/) is a newer project designed to bring the best of Yeoman and CookieCutter and get rid of the parts that are not so good.

Similarly to CookieCutter, with Copier you don’t need to develop to create your templates. You just need to configure your scaffolding project and use the jinja syntax to manage replacements. It also works with a URL, so you don’t need to publish NPM packages.

Comparing Copier with CookieCutter, the main advantage of Copier is the fact that it enables code lifecycle management for generated projects, including updating the answers provided in the prompts or syncing updates from evolved templates.

The potential con, as it is a newer project, the community is not as big as its competitors. However, the support for the project has exponentially increased over the last few years.

While tools like Yeoman and CookieCutter have been popular in the project templating space, Copier stands out with its unique approach and superior features:

- Extremely simple to use
- Robust update mechanism that allows template improvements to be applied to existing projects
- Language-agnostic design
- Minimal configuration overhead
- Clean and straightforward template definition

## Practical Example: NodeJS Project Template

Here's a simple example of how you might create a Copier template for a NodeJS microservice:

```yaml
# copier.yml
project_name:
  type: str
  help: Enter the name of your project

service_port:
  type: int
  default: 3000
  help: Enter the port number for the service

template_path: .

tasks:
  - git init
  - npm init -y
```

And a corresponding template structure:

```
{% raw %}
{{ project_name }}/
├── src/
│   └── index.js
├── package.json
├── README.md
└── .gitignore
{% endraw %}
```

To generate a project from this template, you would simply run:

```bash
copier copy https://github.com/yourusername/nodejs-microservice-template
```

## Conclusions

Copier represents a significant leap forward in project templating. Its simplicity, flexibility, and powerful update mechanism make it an excellent choice for developers looking to standardize and streamline their project creation process.

Whether you're working with microservices, maintaining multiple similar projects, or just want a more efficient way to bootstrap new development efforts, Copier offers a compelling solution that can save time and reduce repetitive setup work.

As a developer who has extensively used Copier across several microservices, I can attest to its incredible utility. The tool has been instrumental in maintaining consistency, reducing boilerplate code, and ensuring that common configurations and patterns are seamlessly shared across different projects.

The main features I love about Copier are:

- Simple and intuitive template creation
- Ability to update templates after initial project generation
- Supports multiple programming languages and frameworks
- Highly customizable template configuration
- Minimal dependencies and easy installation
- Versioning and conflict resolution based on Git
