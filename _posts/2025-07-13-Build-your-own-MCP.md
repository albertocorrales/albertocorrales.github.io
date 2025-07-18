---
layout: post
title: Level Up Your AI Skills, Build a Custom MCP from Scratch
date: 2025-07-13 10:00:00 UTC
updated: 2025-07-13 10:00:00 UTC
comments: false
tags: [AI, LLM, AI assistant, MCP, Claude, Typescript, pnpm]
image:
  path: /assets/img/2025-07-13/0.jpg
---

## Introduction

In our [previous article](https://albertocorrales.com/posts/Getting-starting-with-MCPs/), we explored the Model Context Protocol (MCP) and learned how to use existing MCP servers to extend Claude's capabilities. Today, we'll take the next step and build our own custom MCP server from scratch. We'll create a practical email validation server that can check if an email address is valid and detect potential spam addresses using a free API.

This hands-on tutorial will walk you through the entire process of creating, testing, and deploying a custom MCP server using TypeScript and pnpm. By the end, you'll have a solid foundation for building your own MCP integrations tailored to your specific needs.

## Project Overview

Our email validation MCP server will provide Claude with the ability to:
- Validate email address formats
- Check if an email is associated with spam or disposable email services
- Return detailed user information when available
- Integrate seamlessly with Claude's conversation flow

We'll use the UserCheck API as our validation service, but the principles apply to any public or private API you might want to integrate.

The source code of the example is available in the Github repo: https://github.com/albertocorrales/mcp-example/

## Setting Up the Project

Let's start by creating a new TypeScript project with pnpm. This approach works with npm and yarn as well, but pnpm offers faster installation and better disk space efficiency.

### Project Initialization

First, create a new directory for your project and initialize it:

```bash
pnpm init
```

### Installing Dependencies

Our project needs two main dependencies:

```bash
pnpm add @modelcontextprotocol/sdk zod
```

Let's break down what each dependency provides:

- **@modelcontextprotocol/sdk**: The official MCP SDK that provides all the tools we need to create MCP servers, including server classes, transport mechanisms, and type definitions.
- **zod**: A TypeScript-first schema validation library that helps us define and validate input parameters for our MCP tools. This ensures type safety and provides clear error messages for invalid inputs.

### Project Configuration

Update your `package.json` to include the necessary configuration:

```json
{
  "name": "mcp-check-email",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "inspector": "npx -y @modelcontextprotocol/inspector npx -y tsx main.ts"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "packageManager": "pnpm@10.12.1",
  "dependencies": {
    "@modelcontextprotocol/sdk": "^1.13.0",
    "zod": "^3.25.67"
  }
}
```

The key configuration points:

- **"type": "module"**: Enables ES6 module syntax in our Node.js project
- **"packageManager"**: Specifies the exact pnpm version for reproducible builds
- **"inspector" script**: Provides an easy way to test our MCP server using the official inspector tool

## Understanding the Implementation

Now let's dive into the implementation. Create a `main.ts` file in your project root:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
    name: "Check email MCP Server",
    version: "1.0.0",
    description: "Checks if an email address is valid and returns user information"
});

server.tool("check-email", "Checks if an email address is valid and returns user information",
    {
        email: z.string().email().describe("email address of the user to check"),
    },
    async ({ email }) => {

        const response = await fetch(`https://api.usercheck.com/email/${email}`);
        const data = await response.json();
        console.log(data);
        return {
            content: [
                {
                    type: "text",
                    text: `User information for ${email}: ${JSON.stringify(data)}`
                }
            ]
        }
    });

const tranport = new StdioServerTransport();
await server.connect(tranport);
```

Let's break down each part of this implementation:

### Server Initialization

```typescript
const server = new McpServer({
    name: "Check email MCP Server",
    version: "1.0.0",
    description: "Checks if an email address is valid and returns user information"
});
```

We create a new MCP server instance with metadata that helps identify and describe our server. This information is used by MCP clients to understand what our server does.

### Tool Definition

The core of our MCP server is the tool definition:

```typescript
server.tool("check-email", "Checks if an email address is valid and returns user information",
    {
        email: z.string().email().describe("email address of the user to check"),
    },
    async ({ email }) => {
        // Tool implementation
    });
```

This creates a tool with four key components:

- **Tool name**: "check-email" - This is how Claude will reference the tool
- **Description**: A human-readable explanation of what the tool does
- **Schema**: Zod schema that defines and validates input parameters
- **Handler function**: The async function that executes when the tool is called

### Input Validation with Zod

The schema definition uses Zod to ensure type safety:

```typescript
{
    email: z.string().email().describe("email address of the user to check"),
}
```

This schema:
- Ensures the input is a string
- Validates that it's a properly formatted email address
- Provides a description for documentation and error messages
- Automatically generates TypeScript types for the handler function

### API Integration

The tool handler makes a request to the UserCheck API:

```typescript
const response = await fetch(`https://api.usercheck.com/email/${email}`);
const data = await response.json();
```

This demonstrates how to integrate with external APIs. The UserCheck API provides information about email validity, spam detection, and user details.

### Response Formatting

MCP tools must return responses in a specific format:

```typescript
return {
    content: [
        {
            type: "text",
            text: `User information for ${email}: ${JSON.stringify(data)}`
        }
    ]
}
```

The response contains an array of content blocks. Each block has a type and associated data. Text blocks are the most common, but MCP also supports images and other content types.

The most powerful about the implementation of MCPs is the fact that we only have to return the data we get from the external API, and the AI assistant will interpret it, based on the structure and the name of the properties used in the response.

### Transport Setup

Finally, we set up the communication transport:

```typescript
const tranport = new StdioServerTransport();
await server.connect(tranport);
```

StdioServerTransport enables communication through standard input/output, which is the most common way MCP servers communicate with clients.

## Testing with MCP Inspector

Before integrating with Claude, it's crucial to test your MCP server. The MCP Inspector is a powerful tool that allows you to interact with your server directly.

### Running the Inspector

Use the script we defined in `package.json`:

```bash
pnpm inspector
```

This command:
1. Downloads the latest MCP inspector if not already available
2. Runs your MCP server using tsx (TypeScript execution)
3. Opens a web interface for testing

### Using the Inspector Interface

The inspector provides a web-based interface where you can:

- **View available tools**: See all tools your server exposes with their descriptions and schemas
- **Test tool calls**: Execute tools with different parameters and see the results
- **Debug issues**: View detailed error messages and logs
- **Validate schemas**: Ensure your input validation works correctly

### Testing Our Email Checker

In the inspector interface:

1. **Select the "check-email" tool** from the available tools list
2. **Enter a test email address** in the email parameter field
3. **Click "Call Tool"** to execute the request
4. **Review the response** to ensure it contains the expected user information

Try testing with various email addresses:
- Valid emails: `test@gmail.com`
- Invalid formats: `not-an-email`
- Disposable emails: `temp@10minutemail.com`

The inspector will show you exactly what Claude would receive when calling your tool.

![](/assets/img/2025-07-13/1.png)

## Integrating with Claude

Once you've tested your MCP server with the inspector, you can integrate it with Claude.

### Configuration Setup

Add your MCP server to Claude's configuration file. The location depends on your operating system:

- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Linux**: `~/.config/Claude/claude_desktop_config.json`

Add your server configuration:

```json
{
  "mcpServers": {
    "email-check": {
      "command": "npx",
      "args": [
        "-y",
        "tsx",
        "D:\\dev\\mcp\\main.ts"
      ]
    }
  }
}
```

Replace `D:\\dev\\mcp\\main.ts` with the actual path to your project's main.ts file.

## Usage Examples

After restarting Claude, you can start using your email validation tool in conversations. When you mention something about checking emails, the AI assistant will automatically detect that using the MCP for checking emails might be useful and it will ask for permissions to use it:

![](/assets/img/2025-07-13/2.png)

This example is very basic, it can add new capabilities to our AI assistant that could make it really powerful if we use it properly. Here there are some usage examples where this new MCP would add new useful capabilities:

### Basic Email Validation

```
User: Can you check if the email address john.doe@example.com is valid?

Claude: I'll check that email address for you using the email validation tool.

[Claude calls the check-email tool]

Based on the validation results, john.doe@example.com appears to be a valid email format. The domain example.com exists and can receive emails, though this is a documentation domain typically used for examples.
```

### Bulk Email Validation

```
User: I have a list of email addresses from a sign-up form. Can you check these for validity and potential spam?

- user1@gmail.com
- temp123@10minutemail.com  
- invalid-email@
- admin@mycompany.com

Claude: I'll check each of these email addresses for you:

[Claude calls the check-email tool for each address]

Here are the results:
- user1@gmail.com: Valid email from a reputable provider
- temp123@10minutemail.com: Valid format but this appears to be a temporary/disposable email service
- invalid-email@: Invalid email format - missing domain
- admin@mycompany.com: Valid format, domain exists and can receive emails
```

### Integration with Other Tasks

The power of MCP really shines when combined with other capabilities:

```
User: I'm reviewing user registrations from our database. Can you check the email addresses and create a report on data quality?

Claude: I'll help you analyze the email data quality. First, let me check the email addresses in your database.

[Claude uses filesystem MCP to read the database/CSV file, then calls check-email for each address]

Based on the analysis of 150 email addresses:
- 142 valid emails (94.7%)
- 8 invalid format (5.3%)
- 12 disposable email services (8.0%)
- 3 potentially spam-related domains (2.0%)

I recommend implementing email validation at registration time and consider blocking disposable email services for better data quality.
```

## Extending the Email Checker

This basic example can be extended in many ways:

### Additional Validation Features

- **Domain reputation checking**: Verify if the domain has a good sending reputation
- **Syntax validation**: More sophisticated email format validation
- **Mailbox verification**: Check if the specific mailbox exists (with appropriate permissions)
- **Bulk processing**: Handle multiple emails in a single request

### Integration with Other Services

- **CRM systems**: Validate emails before adding to customer databases
- **Marketing platforms**: Clean email lists before campaigns
- **User registration**: Real-time validation during sign-up processes

### Advanced Response Formats

- **Structured data**: Return validation results in a structured format
- **Confidence scores**: Provide confidence levels for validation results
- **Recommendations**: Suggest corrections for common email typos

## Conclusion

Building custom MCP servers opens up endless possibilities for extending Claude's capabilities. The email validation example demonstrates the key concepts:

- **Project setup** with proper TypeScript configuration
- **Tool definition** with input validation using Zod
- **API integration** patterns for external services
- **Testing workflows** using the MCP Inspector
- **Claude integration** through configuration files

The beauty of MCP lies in its simplicity and flexibility. With just a few lines of code, you can connect Claude to any API or service, creating powerful integrations adapted to your specific needs.

Whether you're building tools for data validation, API integration, database queries, or custom business logic, the patterns demonstrated here provide a solid foundation. The MCP ecosystem is rapidly growing, and by building your own servers, you're contributing to a more connected and capable AI assistant experience.

Start experimenting with your own MCP servers today - the possibilities are truly limitless!