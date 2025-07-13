---
layout: post
title: Getting started with MCPs, add super powers to your AI assistant
date: 2025-06-22 10:00:00 UTC
updated: 2025-06-22 10:00:00 UTC
comments: false
tags: [AI, LLM, AI assistant, MCP, Claude]
image:
  path: /assets/img/2025-06-22/0.png
---

## Introduction

As AI assistants become increasingly integrated into our development workflows, the need for standardized ways to extend their capabilities has become paramount. Enter the Model Context Protocol (MCP) - a game-changing specification that's revolutionizing how we connect AI models with external tools and data sources.

The Model Context Protocol (MCP) is an open standard developed by Anthropic that enables AI assistants to securely connect with external data sources and tools. Think of MCP as a universal adapter that allows AI models to interact with your local files, databases, APIs, and custom tools in a standardized way.

## Why MCPs are important and how they work
Before MCP, extending AI capabilities required custom integrations for each use case. This led to fragmented solutions and limited interoperability. MCP solves these problems by providing:

- **Standardization**: A unified protocol that works across different AI models and tools, reducing development overhead and ensuring consistency.

- **Security**: Built-in permission controls and sandboxing mechanisms that allow you to grant specific access rights without compromising system security.

- **Extensibility**: A plugin-like architecture that enables developers to create reusable components for common tasks like file operations, database queries, and API integrations.

- **Local Control**: Unlike cloud-based extensions, MCPs run locally, giving you complete control over your data and tools while maintaining privacy.

At its core, an MCP operates as a server that implements the protocol's specification. When an AI assistant needs to perform an action, it sends a request to the MCP server describing what it wants to do. The MCP server then validates the request, checks permissions, and executes the requested operation using the appropriate tool or resource. For example, if the AI needs to read a file, it would call the appropriate file system tool provided by the MCP, which would handle the actual file operations in a secure, controlled manner. This architecture ensures that all interactions between the AI and external systems are mediated through the MCP, maintaining security and providing a consistent interface regardless of the underlying implementation.

![](/assets/img/2025-06-22/1.png)

More information in [the official MCP web](https://modelcontextprotocol.io/).

## Examples of Using Existing MCPs 

The MCP ecosystem already includes several powerful pre-built servers that demonstrate the protocol's versatility. Let's explore some practical examples:

### Filesystem MCP

The filesystem MCP is one of the most commonly used servers, allowing to interact with your local files and directories. Here's what you can accomplish:

Once configured, you can ask to:
- Read and analyze code files across your entire project
- Create new files with generated content
- Search for specific patterns across multiple files
- Reorganize directory structures
- Generate documentation based on existing code

**Example interaction**: "Please read all the TypeScript files in my `/src` directory and create a summary of the API endpoints defined in the route handlers."

### SQLite and PostgreSQL MCPs

The SQLite MCP server enables you to query and manipulate SQLite and PostgreSQL databases directly:

This allows for powerful database interactions like:
- Writing complex SQL queries based on natural language descriptions
- Analyzing data patterns and generating reports
- Creating and modifying database schemas
- Performing data migrations and transformations

**Example interaction**: "Examine my sales database and create a monthly revenue report for Q4, including a breakdown by product category."

### Git MCP

The Git MCP server provides repository management capabilities:

With Git MCP, you can:
- Analyze commit history and generate release notes
- Review code changes and suggest improvements
- Create branch summaries and merge reports
- Help with conflict resolution strategies

**Example interaction**: "review the commits from the last week and create a changelog for our next release."

### GitHub MCP

For teams using GitHub, the GitHub MCP server enables direct repository interactions:

This powerful integration allows the AI assistant to:
- Create and manage issues and pull requests
- Review code changes and provide feedback
- Search repositories and analyze project structures
- Generate automated reports on project activity

**Example interaction**: "Create a pull request for the authentication feature branch and include a detailed description of the changes."

### Brave Search MCP

The Brave Search MCP server gives access to real-time web search capabilities:

With this server, you can:
- Get current information on any topic
- Research technical solutions and best practices
- Find relevant documentation and tutorials
- Stay updated on the latest technology trends

**Example interaction**: "Search for the latest best practices for implementing OAuth 2.0 with Node.js and summarize the key recommendations."

## Getting Started with MCP Setup and Claude

As it was mentioned, MCPs can be used with all the popular AI assistants. In this section, we will configure Claude to use existing AI assistants. This example is based on Windows and NPX.

As a prerequisite, we need NodeJS installed. Then, we need to go to the Claude desktop app. Open the menu `File > Settings > Developer > Edit Config`. This will point you to the file `claude_desktop_config.json`, where you can configure your MCPs.

Depending on the MCP you want to configure, the parameters might be different. 

For example, if we want to configure the MCP to work with the filesystem of our computer, we need to configure:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "C:\\Users\\username\\Desktop",
      ]
    }
  }
}
```
With this configuration, we are allowing Claude to interact without filesystem in the Desktop folder.

After this, we need to reset Claude. Then for example we can ask Claude to list the files in a particular folder:

![](/assets/img/2025-06-22/2.png)

The first time Claude uses the MCP, it will ask for permissions. We can decide the scope of these permissions.

## Conclusions

The Model Context Protocol represents a significant step forward in making AI assistants more useful and integrated into our daily workflows. By providing a standardized way to connect AI assistants with external tools and data sources, MCP opens up possibilities that were previously complex or impossible to achieve.

### What's Next?

This introduction has shown you the power of existing MCP servers and how to get started with basic configurations. The MCP ecosystem is still young, but it's already clear that this protocol will fundamentally change how we interact with AI assistants. Starting with these existing servers and understanding the underlying concepts is the fist step to start mastering the evolution of AI assistants.

Whether you're looking to improve your development workflow, enhance your data analysis capabilities, or simply make Claude more useful for your daily tasks, MCP provides the foundation to make it happen. The examples we've explored today are just the beginning - the real power lies in combining multiple MCP servers and eventually creating your own custom integrations tailored to your specific needs.

Start experimenting with these existing MCP servers today, and stay tuned for our next article where we'll build a custom MCP server from scratch!