---
layout: post
title: Working with Event Sourcing, CQRS and Web Sockets on AWS
date: 2023-01-18 10:00:00 UTC
updated: 2023-01-18 10:00:00 UTC
comments: false
tags: [aws, EventStoreDB, event sourcing, CQRS, WebSockets]
image:
  path: /assets/img/2023-01-18/0.png
---

# Introduction

The WebSocket API is an advanced technology that makes it possible to open a two-way interactive communication session between the user's browser and a server. With this API, you can send messages to a server and receive event-driven responses without having to poll the server for a reply.

We normally use WebSockets when we want to get live updates of a particular object without having to constantly poll for new updates. In this scenario, WebSockets can be particularly helpful, as having many clients polling our APIs might increase the cost of our infrastructure, and if the number of request is very high, the API might apply throttling.

In this article, we will review how we could use WebSockets for EverntSourcing+CQRS. Nevertheless, you can follow the same approach for architectures that are not using that pattern, but you need to notify the clients when there are changes in a certain dataset.

# Architecture

When we are using [EventSourcing and CQRS](https://www.eventstore.com/blog/event-sourcing-and-cqrs), we have commands that issue events. These events are projected into the state database. Then, we have queries that allow us to efficiently query the read models that we store in the state database.

In some scenarios, we might want to be notified in the frontend every time there is a change in a particular object, so we can refresh the UI with the latest data. Examples of these are chats, dashboards, push notifications, etc.

When we are using Event Sourcing and CQRS, we can leverage the capabilities of the projections for notifying our clients through WebSockets when the data changes.

In this architecture, from the client perspective, it only requires creating a new WebSocket connection and reacting when there are new events coming through the socket.

If we are using DynamoDB as our state database, an alternative to this could be using Streams on DynamoDB. In this case, when te data changes, DynamoDB streams invoke a lambda, which will notify the corresponding sockets. The disadvantage of this approach is the fact that it won't work with other data stores (ElasticSearch, Neptune, etc). For this reason, notifying during after projecting the data is more generic.

![](/assets/img/2023-01-18/1.png)

Regarding the data we send though the WebSocket, ideally we would be sending the model we want to render in the frontend, so we can use directly the models we get through the WebSocket. However, this might not be suitable for some scenarios, as we night need to apply permissions or data aggregation for the DTOs we serve in our queries. If this is the case, we can just send a notification to our clients telling them that the data has changed, so they fetch latest from the Query API.

# Security

For security reasons, the data we send though the socket must be encrypted. For this, it is important using secure WebSocket connections (wss).

Additionally, if our data is not public, we should implement authentication, so we ensure that only authenticated users can receive updates from the WebSocket.

The authentication on WebSockets works in a similar way as for HTTP APIs, so when we request creating a new connection, we send the access token. Then, the token will be validated by the authorizer in the API Gateway. However, how we pass the token in a WebSocket connection request might be a little different. For http request, we normally use the header `authorization`, but we cannot use custom headers in WebSockets when we are working with javascript. For this case, there are several alternatives, such as sending the token in the socket protocol or as a query parameter.

Apart from that, there are other alternatives that allow you to create the connection without authentication, and then the client sends the token in the first message, once the connection is open. The problem with this approach is the fact that we would be allowing the clients to create connections without passing the authentication, which might be less secure. For this reason, I would advise validating the token before the client creates a new connection.

# WebSockets on AWS

In order to implement WebSockets on AWS, API Gateway provides built-in support, so the service will automatically manage the communication between clients and server. In order to implement WebSockets, we need to create at least two lambdas: one for creating new connections and another lambda for closing connections. Optionally, we can create other lambdas for sending data.

Once you get a request for creating a new connection, you need to store the connection in a data store. In a similar way, when a connection is closed, you need to remove it from the store. This store will be used to know which connections we need to notify when there is a new update. If we try to send data to a connection and the connection is `GONE`, we can delete the connection from the data store. In our architecture, we store the WebSocket connections in MemoryDB for performance reasons, but you could pick any data stores you prefer.

In spite of the fact that you can you can use the default domain that AWS API Gateway generates automatically, it is recommended creating your own subdomain (for example wss://websockets.my-domain.com) and create a new endpoint in API Gateway for WebSockets.

Here you have examples of WebSockets applications in [javascript](https://github.com/aws-samples/simple-websockets-chat-app) and [csharp](https://github.com/normj/netcore-simple-websockets-chat-app).

# WebSockets JavaScript clients

In order to implement your javascript client, you can use the [class WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket).

If you are using react hooks, our code would look similar to this example:

```javascript
const [socket, setSocket] = (useState < WebSocket) | (undefined > undefined);

useEffect(() => {
  if (!socket) {
    const webSocket = new WebSocket(
      "wss://websockets.my-domain.com/notifications",
      accessToken
    );

    webSocket.addEventListener("open", async () => {
      await refreshMyData();
    });

    webSocket.addEventListener("message", async (event) => {
      if (event.data === "DataUpdated") {
        await refreshMyData();
      }
    });

    setSocket(webSocket);
  }
  const fiveMinutesInMilliseconds = 300_000;
  const interval = setInterval(() => {
    if (socket && socket.readyState === socket.OPEN) {
      socket.send("PING");
    }
  }, fiveMinutesInMilliseconds);
  return () => {
    if (interval) {
      clearInterval(interval);
    }

    if (socket) {
      socket.close();
    }
  };
}, []);
```

In this example, we would be fetching the latest data with the method `refreshMyData()` every time there is an update. In addition, once the connection is open, we fetch the data, so we are sure that we are displaying the latest version of the object. This part wouldn't be needed if you only care about new events (for example in a chat room).

In addition, you should also bear in mind the [AWS API Gateway quotas](https://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html). For example, AWS API Gateway would close any connections that have been idle for more than 10 minutes. If you don't want this to happen, you should ping the WebSocket from time to time in order to keep it alive. In the example above, we just create an interval that pings the socket every 5 minutes.

Finally, when you unload the component in the UI, you should clear ping interval (if you are using any) and close the connection.

# Testing Web Sockets

In order to mock the socket in unit tests, you can use the npm package [`jest-websocket-mock`](https://github.com/romgain/jest-websocket-mock), so you can mock it and check that is it is working as expected.

For integration tests, you can implement integration tests where you subscribe to a particular object with a web socket. After that, you can issue commands and check if you are getting the updates through the web socket.

Finally, for end-to-end tests, we don't need to do anything specific. However, if you are using Cypress, I spotted [an issue](https://github.com/cypress-io/cypress/issues/7664) with Cypress when we are working with WebSockets, as it closes the before receiving the handshake response.

If you are facing this issue, you need to bypass the WebSockets endpoint in your cypress configuration, so cypress won't proxy these requests. For that, we would add this custom configuration in the file `cypress.config.ts`.

```javascript
import { defineConfig } from "cypress";

export default defineConfig({
  setupNodeEvents(on, config) {
    on("before:browser:launch", (browser = {}, launchOptions) => {
      launchOptions.args = launchOptions.args.map((arg) => {
        if (arg.startsWith("--proxy-bypass-list")) {
          return "--proxy-bypass-list=<-loopback>,wss://websockets.my-domain.com";
        }
        return arg;
      });
      return launchOptions;
    });
  },
});
```

# Conclusions

In this post, we have seen what WebSockets are and how we can use them to reduce the number of requests we perform to our APIs when we need to keep updated our clients.

As we have discussed, WebSockets can be specially relevant to prevent polling our APIs, which could overload them, increasing the cost or even consuming part of the traffic quota.

In this post, we have also analyzed an architecture that leverages Event Sourcing and CQRS to send updates through WebSockets, so the web clients can get updates in real time.

Finally, we have explained how WebSockets can be implemented, including the backend with AWS API Gateway + Lambdas, and the frontend with the provided API for WebSockets.
