---
layout: post
title: Graph databases with AWS and dotnet core
date: 2020-06-15 11:01:51 UTC
updated: 2020-06-15 11:01:51 UTC
comments: false
categories: .net core AWS graph databases gremlin neptune
---

[![](https://1.bp.blogspot.com/-8_kbBkDfbpM/XuT9Zpf8hNI/AAAAAAAAGFA/lTSQ4bQKeRcpaSoBilD--dHFARhQrWKMgCLcBGAsYHQ/s640/1_glL1giv7-LUYJXL36gJ5ew.jpeg)](https://1.bp.blogspot.com/-8_kbBkDfbpM/XuT9Zpf8hNI/AAAAAAAAGFA/lTSQ4bQKeRcpaSoBilD--dHFARhQrWKMgCLcBGAsYHQ/s1600/1_glL1giv7-LUYJXL36gJ5ew.jpeg)

## Introduction

Graph databases are NoSQL databases that store data using graph structures. In other words, your data will be stored as nodes and edges, which represent your objects and their relationships. Graph databases were created to address the limitations of relational databases when you need to create relationships and consume then in an efficient way.

Some use cases where graph databases are a good choice are: fraud detection, retail, risk and compliance, social networking, knowledge graph, recommendation engines, life sciences, telecommunications, etc.

In my particular use case, we wanted an efficient data storage to understand the relationship between entities that we have registered in our system and extract that knowledge for multiple purposes like fraud detection, retail, risk and compliance, etc. In addition, as our product is could, we analysed the options that AWS offers us to implement graph databases with dotnet core.

## Alternatives to work with AWS and dotnet core

In spite of the fact that graph databases are a quite innovative concept for most companies, there is a big community and plenty of engines, languages, etc. When it comes to AWS, you can run whatever technology you want, but we focused on the most popular engines and languages.

### Neo4j

Neo4j ([https://neo4j.com/](https://neo4j.com/)) is a graph database management system developed by Neo4j, Inc. Neo4j is the most popular graph database engine and it was released in 2007.

[![](https://1.bp.blogspot.com/-WAADRic0Ckk/XuUEymK_sRI/AAAAAAAAGFQ/RQu_dy7i1YMnXUwTMMLSkF2XFzUtR9gHACPcBGAYYCw/s400/Neo4j-01.png)](https://1.bp.blogspot.com/-WAADRic0Ckk/XuUEymK_sRI/AAAAAAAAGFQ/RQu_dy7i1YMnXUwTMMLSkF2XFzUtR9gHACPcBGAYYCw/s1600/Neo4j-01.png)

**Advantages:**

- Neo4j uses Cypher as language, which is a language easy to understand.
- There are some nice clients to work with Neo4j, which makes easy to visualize what you have in your database.
- The community and the documentation for Neo4j are really good.
- Cyper is supported by many languages (javascript, phyton, .NET, Java, etc).

**Disadvantages:**

- Cypher was created by Neo4j, so you can't use it with other engines.
- There is not SaaS offering on AWS for Neo4j, although there is a cloud service out of AWS ([https://neo4j.com/aura/?ref=cloud](https://neo4j.com/aura/?ref=cloud))
- If you decide to run it on AWS, you have to run an EC2 instance or a docker container, so you will be responsible for configuration, security, backups, maintenance, scalability, etc.

### Amazon Neptune

Amazon Neptune ([https://aws.amazon.com/neptune/](https://aws.amazon.com/neptune/)) is the service offered by AWS to work with graph databases. Amazon Neptune is a relatively new service (compared to Neo4j) that was released in 2017.

[![](https://1.bp.blogspot.com/--TMXNKuSawY/XuUF_YE8-FI/AAAAAAAAGFc/ZVcqXkvylTgerd229Kqj-c8i3Hvr8U0qACPcBGAYYCw/s400/Capture.PNG)](https://1.bp.blogspot.com/--TMXNKuSawY/XuUF_YE8-FI/AAAAAAAAGFc/ZVcqXkvylTgerd229Kqj-c8i3Hvr8U0qACPcBGAYYCw/s1600/Capture.PNG)

**Advantages**

- Amazon Neptune is a AWS SaaS product and you will have some important features out-of-the-box, such as:

- highly availability
- read replicas
- point-in-time recovery
- continuous backup to Amazon S3
- replication across Availability Zone
- etc

- Amazon Neptune is based on standards.
- It supports two of the most popular languages: Gremlin ([http://tinkerpop.apache.org/gremlin.html](http://tinkerpop.apache.org/gremlin.html)) and SPARQL ([https://www.w3.org/TR/rdf-sparql-query/](https://www.w3.org/TR/rdf-sparql-query/)). These languages allow you to work with Neptune or other engines.
- Gremlin is supported by a huge variety of languages, such as .NET, java, typescript, scala, phyton, go, elixir, php, etc.
- Gremlin is supported by many graph systems like Microsoft CosmosDB, Neo4j, Titan, Hadoop, IBM Graph, Alibaba Graph Database, etc. More info: [http://tinkerpop.apache.org/providers.html#data-system-providers](http://tinkerpop.apache.org/providers.html#data-system-providers)

**Disadvantages**

- Amazon Neptune doesn't include any tools to visualize your data. If you use gremlin you can use graphexp ([https://github.com/bricaud/graphexp](https://github.com/bricaud/graphexp)), but it is quite basic and a little buggy, since it is an open source project with few contributors.
- The learning curve for Gremlin language can be a little harder at the beginning as there are less resources, but fortunately there is a good documentation available [http://tinkerpop.apache.org/docs/current/reference/](http://tinkerpop.apache.org/docs/current/reference/) and this ebook with many examples: [https://kelvinlawrence.net/book/Gremlin-Graph-Guide.html](https://kelvinlawrence.net/book/Gremlin-Graph-Guide.html)
- Amazon Neptune cluster is not accessible if you are outside of the VPC where you deploy it. There are options like deploy an EC2 or a network load balancer, but it would be nice if you could get access without paying for additional resources. That is something that AWS could address by allowing IAM access control, as for other database engines.

## Gremlin and dotnet core

Taking into account pros and cons, we decided to have a try to Neptune with gremlin language. Firstly, because it is an AWS fully managed service and one of our principles is trying to invest our time building business value, instead of maintaining the cluster, the storage, etc. In addition, using gremlin as language will give us the possibility to use other platforms like DynamoDB, CosmosDB or even Neo4j.

### Run gremlin locally

In order to run gremlin locally, I strongly recommend using docker. In fact, here you can find a docker compose which will run gremlin server and grephexp as UI, which will be handy to visualize your database: [https://github.com/designfrontier/gremlin-local](https://github.com/designfrontier/gremlin-local)

Once you run command "docker-compose up", you should be able to see your empty data base on [http://localhost:8183/](http://localhost:8183/)

[![](https://1.bp.blogspot.com/-WlNPbJKA8Ic/XuY7PCyLf4I/AAAAAAAAGG4/k3uHBc2rH9k0vLX5-ZSdccUAK25fkpiTgCLcBGAsYHQ/s640/gremlin1.PNG)](https://1.bp.blogspot.com/-WlNPbJKA8Ic/XuY7PCyLf4I/AAAAAAAAGG4/k3uHBc2rH9k0vLX5-ZSdccUAK25fkpiTgCLcBGAsYHQ/s1600/gremlin1.PNG)

### Using Gremlin.NET

If you are using dotnet core, the most popular plugin to work with gremlin is Gremlin.NET: [https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet)

To work with Gremlin.NET you just have to install Gremlin.NET NuGet package in your dotnet core application.

> nuget install Gremlin.Net

Then, you need to create your server, your client and your traversal, which will allow you to perform different operations with your graph database.

```
var server = new GremlinServer(                "localhost",                8182,                false);var client = new GremlinClient(server);var g = Traversal().WithRemote(new DriverRemoteConnection(client));
```

In case you are working with Neptune cluster, the hostname will be the DNS for your cluster and SSL will be enabled.

With Gremlin.NET package, you can write queries with string format or you can use the fluent API. Here you can find an example to see how you can execute string queries: [https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin-dotnet.html](https://docs.aws.amazon.com/neptune/latest/userguide/access-graph-gremlin-dotnet.html)

However, fluent library will provide you a better experience. To illustrate this, I've created a very simple REST Web API where you can register, remove or query people. Each person in the graph database will be represented by one vertex or node. In addition, you can create relationships between people, which will be represented by edges.

[![](https://1.bp.blogspot.com/-PX-561nBiNk/XuZW8DEJJMI/AAAAAAAAGHE/-KgjfmoCgGgIGF4hr4t-Gw59EkAFPb2vwCLcBGAsYHQ/s640/gremlin2.PNG)](https://1.bp.blogspot.com/-PX-561nBiNk/XuZW8DEJJMI/AAAAAAAAGHE/-KgjfmoCgGgIGF4hr4t-Gw59EkAFPb2vwCLcBGAsYHQ/s1600/gremlin2.PNG)

Once we have our transversal configured, we could create a vertex by doing:

```
[HttpPost]        public async Task Post([FromBody] Person person)        {            await _g.AddV("person")                .Property("key", person.Id)                .Property("name", person.Name)                .Promise(t => t.Next());        }
```

As you can see, you add a vertex by using "AddV" instruction and then you define the label for that vertex. Then, you can define your custom properties for that vertex. For each vertex property, you can define an array of values.

Finally, you resolve the promise to have an async execution of your action, which is strongly recommended if you want to have a better performance. You might notice the "Id" property has been defined as "key", because "id" can cause conflicts with some engines which use that property to auto-generate internal ids. However, for Neptune you can overwrite you could use "id" property to define your own id without any conflicts.

In order to define an edge, you can do:

```
[HttpPost]        public async Task Post([FromBody] Relationship relationship)        {            var source = await _g.V().Has("key", relationship.SourcePersonId).Promise(t => t.Next());            var target = await _g.V().Has("key", relationship.TargetPersonId).Promise(t => t.Next());            await _g.V(source)                .AddE(relationship.Type)                .To(target)                .Property("key", relationship.Id)                .Property("sourceId", relationship.SourcePersonId)                .Property("targetId", relationship.TargetPersonId)                .Property("type", relationship.Type)                .Promise(t => t.Iterate());        }
```

In this code, you are getting source and target vertexes, and then creating a connection between them by using "AddE". Then, you can add your custom properties. In this case, each property will have only one value.

As a example, by using this API  I've created a simple graph with four people and different relationships between them. In Graphexp it would look like this:

[![](https://1.bp.blogspot.com/-H7tNbvJT1eQ/XuZW9HUoJFI/AAAAAAAAGHI/N6GY2XlJrZU5aYuULvKU_pxDT5wxpcnyQCLcBGAsYHQ/s640/gremlin3.PNG)](https://1.bp.blogspot.com/-H7tNbvJT1eQ/XuZW9HUoJFI/AAAAAAAAGHI/N6GY2XlJrZU5aYuULvKU_pxDT5wxpcnyQCLcBGAsYHQ/s1600/gremlin3.PNG)

You can find the source code in my GitHub repository: [https://github.com/albertocorrales/gremlin-dotnet-example](https://github.com/albertocorrales/gremlin-dotnet-example).

## Conclusions

In this article we reviewed the most popular alternatives to use graph databases on AWS, analysing pros and cons. In my use case, we decided to have a try to Neptune with Gremlin, since it is a fully managed service and Gremlin is a standard language supported by multiple engines. However, Neo4j is also a great technology with a good community and support.

Finally, we saw an example with dotnet core. This example allow us to register people and create relationships between them using graph databases with gremlin.
