---
layout: post
title: How to Setup EventStoreDB on AWS EC2 with Pulumi IaC
date: 2020-11-16 08:08:49 UTC
updated: 2020-11-16 08:08:49 UTC
comments: false
categories: AWS EBS EC2 ESDB eventstore IaC Pulumi
---

[![](https://1.bp.blogspot.com/-ngTZILTFtKQ/X7FWCi9b6eI/AAAAAAAAGX4/HSFDjF6zSEsbr5VBLtBmztY8ro5-OfaqwCLcBGAsYHQ/w640-h224/header.png)](https://1.bp.blogspot.com/-ngTZILTFtKQ/X7FWCi9b6eI/AAAAAAAAGX4/HSFDjF6zSEsbr5VBLtBmztY8ro5-OfaqwCLcBGAsYHQ/s952/header.png)

---

## Introduction 

EventStoreDB (ESDB) is an industrial-strength database technology used as the central data store for event-sourced systems. It is available open-source to run locally on most platforms or as SaaS through Event Store Cloud. Currently their SaaS version is under development, so if you want to run it on your cloud, you will have to set it up.

One option is docker, since ESDB is available as [docker image on Docker Hub](https://hub.docker.com/r/eventstore/eventstore/). However, as [Greg Young explains in this issue](https://github.com/EventStore/eventstore-docker/issues/7), it might be not the best option when it comes to performance, due to the extra virtualization layer. For the same reason, we don't usually use docker images for other database engines like SQL Server, MongoDB, etc.

Then, if you want to run ESDB on cloud, in particular AWS cloud, EC2 + EBS is the most reasonable option.

In this post, I would like to share how you can configure ESDB on EC2+EBS easily with Pulumi, which will allow you to automate this process following the best practices.

## Creating EC2 with Pulumi

If you are not familiar with Pulumi, I recommend to start having a look at [this documentation](https://www.pulumi.com/docs/get-started/aws/), where it is explained how to setup a new project for AWS.

Creating a new EC2 in Pulumi is really straightforward. You just need to define your instance like this:

[![](https://1.bp.blogspot.com/-84plL3ZvC8U/X7FT8C5KaOI/AAAAAAAAGXs/aBrsiLAbg5YtaAykrPfpF-I__ija2GaKwCLcBGAsYHQ/w400-h184/esdb1.PNG)](https://1.bp.blogspot.com/-84plL3ZvC8U/X7FT8C5KaOI/AAAAAAAAGXs/aBrsiLAbg5YtaAykrPfpF-I__ija2GaKwCLcBGAsYHQ/s683/esdb1.PNG)

In that code, you can specify the following parameters:

- **instanceType**: type of EC2 instance that you want to use
- **keyName**: key pair that you will use to connect with SSH
- **ami**: AMI you want to use. For example AMI "ami-06fd8a495a537da8b" is to use Ubuntu Server 20.04 LTS
- **vpcSecurityGroupIds**: the security group you are going to use. Here for ESDB you might need to enable ports 1113 and 2113. For SSH you will need to enable port 22.
- **userData**: this is the script to setup ESDB. We will see this more in detail later.
- **tags**: your AWS for this resource. It is highly recommended to tag all your AWS resources with meaningful tags.

## Setup EventStoreDB on EC2

First of all, lets have a look at ESDB config file.

```
# Paths
Db: /data/eventstore/db
Index: /data/eventstore/index
Log: /data/eventstore/log

# Certificates configuration
CertificateFile: /data/certs/node1/node.crt
CertificatePrivateKeyFile: /data/certs/node1/node.key
TrustedRootCertificatesPath: /data/certs/ca

# Network configuration
HttpPort: 2113
ExtTcpPort: 1113
EnableExternalTcp: true
EnableAtomPubOverHTTP: true

# Cluster config
ClusterSize: 1

# Projections configuration
RunProjections: System
```

If you want to know more about ESDB, I recommend you to visit [this guide](https://developers.eventstore.com/server/20.6/server/installation/#quick-start-preview), which is quite good. Here I normally configure ESDB data and certificates in folder /data, which is in an independent EBS, but I will skip that part in this post, in order to simplify.

### Now, in our EC2 userData we need to follow these steps:

### 1) Install EventStore

In this step we install EventStore repository and we install the latest version, which currently is version 20.6.1.

# Install EventStore

`curl -s https://packagecloud.io/install/repositories/EventStore/EventStore-OSS/script.deb.sh | sudo bash`

`sudo apt-get install eventstore-oss=20.6.1-2 `

###   2) Update EventStore Config

In this step, we overwrite eventstore.conf file with the configuration we saw previously.

# Update EventStore Config

`sudo echo "${getEventStoreConfig()}" >| /etc/eventstore/eventstore.conf`

### 3) Create /data folder and add permissions to eventstore

Then, we create folder /data and we assign permissions to eventstore, so the service will be able to own that folder.

# Create /data folder and add permissions to eventstore

```bash
    cd /

    mkdir data

    sudo chown eventstore data

    sudo chgrp eventstore data
```

### 4) Create certificates

In this step, we create CA and Node certificates for SSL connection with [es-gencert-cli](https://github.com/EventStore/es-gencert-cli), which is a software created by EventStore to simplify this process. You could also use OpenSSL if you wish.

# Create certificates

```bash
    cd /data

    sudo mkdir certs

    cd certs/

    sudo wget -c https://github.com/EventStore/es-gencert-cli/releases/download/1.0.2/es-gencert-cli\_1.0.2\_Linux-x86\_64.tar.gz

    sudo tar -xzvf es-gencert-cli\_1.0.2\_Linux-x86\_64.tar.gz

    sudo ./es-gencert-cli create-ca

    sudo ./es-gencert-cli create-node -out ./node1 --dns-names \*.example.com

    find . -type f  -name '\*.crt' -o -name '\*.key' -print0 | sudo xargs -0 chmod 666

### 5) Run EventStore

 Finally we enable and start eventstore service.

    # Run EventStore

    sudo systemctl enable eventstore

    sudo systemctl start eventstore
```

You can find the full example source code on this GitHub repository: [https://github.com/albertocorrales/eventstoredb-aws-ec2](https://github.com/albertocorrales/eventstoredb-aws-ec2)

Conclusions

In this post, we have seen how we can setup EventStoreDB on AWS EC2 with Pulumi, as IaC framework. If you want to know more about these technologies, I suggest to visit [EventStore documentation](https://www.eventstore.com/) and [Pulumi documentation](https://www.pulumi.com/).
