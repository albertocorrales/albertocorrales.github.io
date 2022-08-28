---
layout: post
title: Dropping the cost of DynamoDB
date: 2022-08-28 10:00:00 UTC
updated: 2022-08-28 10:00:00 UTC
comments: false
tags: [aws, dynamodb, cost saving, cloud, serverless]
image:
  path: /assets/img/2022-08-28/1.png
---

# Introduction

DynamoDB is the most popular NoSql database on AWS. It allows you to easily build high performant applications taking care of the good practices, such as: encryption, access control, high-availability, backups, etc.

DynamoDB allows you to configure two types of capacity allocation: provisioned and on-demand, and it defines the capacity with read capacity units (RCU) and write capacity units (WCU). One RCU is one strongly consistent read or two eventually consistent reads for a data block of up to 4 KB. On the other hand, one WCU represents write request for a data block of up to 1 KB.

When it comes to pricing, it is important to understand these two modes and pick the one that fits better for each case, so we can have tables that auto-scale without over-provisioning.

In this post, we will see a real case, where picking the right mode helped to drastically reduce the cost of using DynamoDB.

# Provisioned mode

With provisioned capacity, you can configure the maximum RCU ans WCU of the table and this usage will be charged hourly. In addition, you have to configure RCU and WCU for the indexes of the table.

When you use provisioned mode, if the application exceeds the defined limits, the exceeding requests will be throttled. From the user perspective, it could be reflected as write or read requests being temporary denied, which is not a good user experience.

Normally the traffic of a table tends to be variable, for example high during working hours and low during non-working hours. Or we might have spikes of traffic for more demanding processes like a data migration.

When you use provisioned mode and the traffic of the table is variable, you might have to configure a high limit for RCUs and WCUs, so you prevent rejecting requests. The problem with this approach, is the fact that you would be over-provisioning and paying for a high capacity that is not being used. In addition, planning the capacity needed for each table and index of the table might be challenging, because it can change over time.

In order to try to make the provisioned mode less rigid, DynamoDB allows you to configure auto-scaling policies. These policies allow you to configure values for min ans max capacity and the percentage of target utilization that will be trigger the alarm to scale up.

![](/assets/img/2022-08-28/2.png)

However, there are two important limitations with this approach:

1. You still need to have a minimum capacity and pay for it, even if you are not using it.
2. When DynamoDB scales up, it reacts very slowly, so you will end up having throttling for RCUs and WCUs. In [this article](https://medium.com/nerd-for-tech/aws-dynamodb-auto-scaling-is-not-a-magic-bullet-25b2fdc50e5a) we can see good examples of this, where the dynamodb is scaling up too late, so there will cause throttling.

![](/assets/img/2022-08-28/3.png)

# On-Demand mode

With on-demand capacity, pricing is based on the amount of read and write request units the application consumes throughout the month. With on-demand mode there are not up-front charges for the DynamoDB capacity and it will auto-scale following [these rules](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.OnDemand).

The most interesting part of on-demand is the fact that when you create a new table with on-demand mode, you will serve up to 4,000 write request units and 12,000 read request units, but you don't have to pay provisioning that capacity and your workload won't be throttled for using this capacity. For this reason, on-demand is the best mode to work with frequently unpredictable workloads and it is what [AWS recommends](https://aws.amazon.com/premiumsupport/knowledge-center/dynamodb-auto-scaling/) for these scenarios.

# Taking the best of provisioned and on-demand

On-demand mode in dynamodb can help us to reduce the cost for our tables, so we only pay for the requests we perform, instead of over-provisioning by using fixed RCUs or WCUs. However, when you are using on-demand mode, you cannot set any limits on WCUs or RCUs to prevent unfair usage of the table. This can be a risk, because if a table is having a constant very high load, it might lead to a huge increment in the cost.

Being able to establish limits is one of the good features about provisioned mode. For example, imagine we want to limit a table with 100 RCUs and 150 RCUs, but we don't want to pay for that capacity if we are not using it. In addition we don't want to have throttling when we go from 0 RCUs to 100 RCUs. In this scenario is when we can use a hybrid model, where we normally work with on-demand mode, but we establish limits for RCUs and WCUs, so if we exceed one of those limits, we change the mode of the table to provisioned to start applying throttling. This will restrict the traffic for the affected dynamodb table, so can prevent unfair usage of it.

The proposed architecture is represented by the following diagram:

![](/assets/img/2022-08-28/4.png)

In the diagram, we can see that for each of our tables that we configure as on-demand, we define AWS CloudWatch alarms. When any alarms are triggered, AWS sends an event to the default bus of AWS EventBridge, so we can subscribe to those alarms and react to them. In order to be able to filter out these alerts, we defined a concrete naming convention, so in the EventBridge target, we only subscribe to alarms which starts with `dynamodb-threshold-alarm#`:

```
dynamodb-threshold-alarm#{TableName}#{AlertName}
```

In addition, from the alarm name, we know the name of the table that triggered the alarm and the alarm type (for example table-rcu, index-wcu, etc).

When we receive any of these alarms, we trigger a step function, which will switch the mode of the table from on-demand to provisioned. In order to establish the limits for each table, we will store those values in a DynamoDB table.

In the step function, we will setup the needed logic to perform the mode update. When we change from on-demand to provisioned, we will have to keep the provisioned mode for 24 hours, since DynamoDB only allows one switch every 24 hours. After that, we can switch back to on-demand. We will also have to check if for a given table we already have an update in-progress. In that case, we will skip the flow.

![](/assets/img/2022-08-28/5.png)

Bear in mind that updating from on-demand to provisioned shouldn't happen too often. If that is the case, we might be having unfair usage of the table, or our the limits we establish were too restrictive, so we should review it. In order to notify us, we can send the same alerts to our email or any provider we are using to manage incidents, like PagerDuty.

# Cost reduction results

When we starting using DynamoDB, we used provisioned mode as the default mode, since there was a requirement for setting up usage limits in our DynamoDB tables. We work with a big number of DynamoDB tables, since we have independent tables for each micro-service. In addition, for each tenant the data is stored in a different table for segregation and security purposes. In order to prevent throttling, we have to set high thresholds for RCUs and WCUs, especially in those tables with more traffic.

As you can see in the plot, as we are adding new clients and micro-services, the cost of having DynamoDB started growing more and more between April and May.

In June, we starting progressively applying the approach presented in this article to some of the tables. As a result, we started to see how the cost was decreasing.

In July and August, we already had applied the new approach for most of our DynamoDB tables and the cost went flat, in spite of the fact that we continue adding more DynamoDB tables due to new clients onboarding or new micro-services being setup.

![](/assets/img/2022-08-28/6.png)

# Conclusions

In this article, we have seen the different modes that DynamoDB offers, and how they can impact our budget. Then, it has been presented the approach we followed to reduce the cost of DynamoDB, by leveraging the best of provisioned and on-demand modes.

In the cost analysis, we have seen that now we are effectively managing the cost of DynamoDB, which will allow us to keep growing as a platform.

In the future, it would be a great improvement if AWS implements a built-in feature to establish limits for on-demand mode, so we don't have to switch to provisioned mode. Because it makes sense to use a pay-as-you-go model, but being able to setup limits. I've sent this feedback to AWS, so I hope they consider adding something like that.

Finally, bear in mind that this approach might not fit for every case, so it is important to understand how your data stores are being used and how DynamoDB pricing works, so you can apply the best strategy for your case.
