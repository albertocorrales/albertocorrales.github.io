---
layout: post
title: Managing snapshots for Amazon ElasticSearch with Dotnet Core Lambdas
date: 2020-12-13 22:30:14 UTC
updated: 2020-12-13 22:30:14 UTC
comments: false
categories: .net core 2020 Amazon AWS AWS lambda Backup ElasticSearch
cover-img: https://1.bp.blogspot.com/-vJNWwJ_H_iA/X9aSnSQIY3I/AAAAAAAAGZs/CidKLUKxeFkEYaxkCMlt_cmYgUuIVkHKgCLcBGAsYHQ/s1200/aws-elasticsearch-1200x487.jpg
thumbnail-img: https://1.bp.blogspot.com/-vJNWwJ_H_iA/X9aSnSQIY3I/AAAAAAAAGZs/CidKLUKxeFkEYaxkCMlt_cmYgUuIVkHKgCLcBGAsYHQ/s1200/aws-elasticsearch-1200x487.jpg
share-img: https://1.bp.blogspot.com/-vJNWwJ_H_iA/X9aSnSQIY3I/AAAAAAAAGZs/CidKLUKxeFkEYaxkCMlt_cmYgUuIVkHKgCLcBGAsYHQ/s1200/aws-elasticsearch-1200x487.jpg
tags: []
---

Introduction

===============

It is awesome to have some useful services like [ElasticSearch managed by AWS](https://aws.amazon.com/elasticsearch-service/), so you don't have to care about patching, monitoring, etc.

When it comes to backup management for indexes, Amazon ElasticSearch includes automated snapshots. Automated snapshots are only for cluster recovery. You can use them to restore your domain in the event of red cluster status or other data loss. Amazon ES stores automated snapshots in a preconfigured Amazon S3 bucket at no additional charge. In particular, if you are using a higher version than 5.3, Amazon ElasticSearch takes hourly automated snapshots and retains up to 336 of them for 14 days.

However, this policy might not be enough if you need to keep your snapshots for a longer 15 days, or you need to take a snapshot to create a new cluster in another region, subnet, etc. If you have different requirements to manage your snapshots, you will have to work with manual snapshots. For this part, AWS does not offer a good managed experience, since the solution is calling ElasticSearch Web API, as it is explained in [this article](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html#es-managedomains-snapshot-restore). In fact, even for automatic snapshots, you will also have to use ElasticSearch Web API if you need to restore your index.

In orther to make requests to ElasticSearch, they must be signed by AWS ([more info here](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html)). For this purpose, AWS provides packages for multiple languages (Java, Python, Ruby, Node and  Go), but not for C#.

In this article, we will see how we can manage Amazon ElasticSearch snapshots with dotnet core.

# Creating ElasticSearch snapshots with dotnet core

First of all, you need to cover some pre-requisites that are detailed [here](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html#es-managedomains-snapshot-restore), as for the rest of languages:

1.  You have to create an S3 bucket. In this S3 bucket, you won't be able to configure lifecycle policies to move to cold start or delete old data.
2.  You have to create an IAM role with access to the S3 bucket created previously.
3.  You need an IAM user and permissions to assume previous role or call elastic search with HTTP PUT.

For the dotnet implementation, I created a new dotnet core AWS Lambda. In this way, I could invoke it from multiple sources. For example I can schedule a CloudWatch event to trigger it every day, every month, etc. The source code of this example can be found on [this GitHub repository](https://github.com/albertocorrales/ElasticSearchSnapshotsLambda).

First of all, this is my request object for AWS lambda input:

```csharp
public class Request
{
    public string Endpoint { get; set; }
    public string RepositoryName { get; set; }
    public string S3Bucket { get; set; }
    public string Region { get; set; }
    public string RoleArn { get; set; }
}
```

Then this is the lambda handler:

```csharp
public async Task FunctionHandler(Request request, ILambdaContext context)
{
    try
    {
        var accessKey = Environment.GetEnvironmentVariable("ACCESS_KEY");
        var secretKey = Environment.GetEnvironmentVariable("SECRET_KEY");
        var signer = new AWS4RequestSigner(accessKey, secretKey);
        var repositoryUrl = $"{request.Endpoint}/_snapshot/{request.RepositoryName}";
        var service = "es";

        await RegisterS3Repository(request, context, signer, repositoryUrl, service);
        await CreateSnapshot(request, context, signer, repositoryUrl, service);

    }
    catch (Exception ex)
    {
        context.Logger.LogLine($"Error performing backup: {ex.Message}");
        throw;
    }
}
```

As I mentioned previously, requests to ElasticSearch have signed and this is not supported by AWS. However, there is a signer for AWS that you can use from this project: [https://github.com/tsibelman/aws-signer-v4-dot-net](https://github.com/tsibelman/aws-signer-v4-dot-net) by using [this NuGet package](https://github.com/tsibelman/aws-signer-v4-dot-net). Once we create the signer with AWS access key and secret key, we just have to register the repository and create the snapshot.

In order to register the S3 repository, we have to create the request, sign it and send it with PUT http operation:

```csharp
private static async Task RegisterS3Repository(Request request, ILambdaContext context, AWS4RequestSigner signer, string repositoryUrl, string service)
{
    context.Logger.LogLine($"Register ElasticSearch Repository: {request.RepositoryName}");
    var requestBody = new
    {
        type = "s3",
        settings = new
        {
            bucket = request.S3Bucket,
            region = request.Region,
            role_arn = request.RoleArn
        }
    };
    string requestBodyString = System.Text.Json.JsonSerializer.Serialize(requestBody);
    var content = new StringContent(requestBodyString, Encoding.UTF8, "application/json");

    var httpRequestRepository = new HttpRequestMessage
    {
        Method = HttpMethod.Put,
        RequestUri = new Uri(repositoryUrl),
        Content = content
    };

    httpRequestRepository = await signer.Sign(httpRequestRepository, service, request.Region);
    var client = new HttpClient();
    var responseRepository = await client.SendAsync(httpRequestRepository);

    if (!responseRepository.IsSuccessStatusCode)
    {
        throw new Exception($"Error registering repository {request.RepositoryName}.\n" +
            $"Error code: {responseRepository.StatusCode}.\n" +
            $"Content: {await responseRepository.Content.ReadAsStringAsync()}");
    }
    context.Logger.LogLine($"ElasticSearch Repository {request.RepositoryName} successfully registered");
}
```

Once we register the repository, we can create new snapshots in a similar way, signing the request and sending a PUT http operation to $"{repositoryUrl}/{snapshotName}" endpoint:

```csharp
private static async Task CreateSnapshot(Request request, ILambdaContext context, AWS4RequestSigner signer, string repositoryUrl, string service)
{
    var snapshotName = DateTime.Now.ToString("dd-MM-yyyy-h-mm-ss").ToLower();

    context.Logger.LogLine($"Create ElasticSearch Image: {repositoryUrl}/{snapshotName}");

    var httpRequestSnapshot = new HttpRequestMessage
    {
        Method = HttpMethod.Put,
        RequestUri = new Uri($"{repositoryUrl}/{snapshotName}"),
        Content = null
    };

    httpRequestSnapshot = await signer.Sign(httpRequestSnapshot, service, request.Region);
    var client = new HttpClient();
    var responseSnapshot = await client.SendAsync(httpRequestSnapshot);

    if (!responseSnapshot.IsSuccessStatusCode)
    {
        throw new Exception($"Error creating snapshot {snapshotName}.\n" +
            $"Error code: {responseSnapshot.StatusCode}.\n" +
            $"Content: {await responseSnapshot.Content.ReadAsStringAsync()}");
    }
    context.Logger.LogLine($"ElasticSearch snapshot {snapshotName} successfully registered");
}
```

# Conclusions

In this article, we have seen the options AWS ElasticSearch offers to create snapshots. In particular, the automatic way to create snapshots is quite limited and it is not configurable, so depending on our requirements we might need to create manual snapshots.

If we want to implement this process with dotnet core, in this article we have seen an example to implement a lambda which we can use go create manual snapshots. We could also extend this approach to build scheduled lambdas to remove old snapshots or any other operations.

In my personal opinion, Amazon ElasticSearch should offer better alternatives, for example integration with [AWS Backup](https://aws.amazon.com/backup/).
