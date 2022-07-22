---
layout: post
title: Run docker on windows easily without docker desktop
date: 2022-07-22 10:00:00 UTC
updated: 2022-07-22 10:00:00 UTC
comments: false
tags: [docker, rancher, tools, development, wsl]
image:
  path: /assets/img/2022-07-22/0.png
---

# Introduction

During the last years few, Docker has become one of the most popular technologies for vitualizing all kind of services. Until last year, if you were working with Docker, you were probably using the Docker Desktop application, which nicely installs and manages your docker containers, images, kubernetes, etc.

However, at the beginning of this year, Docker changed their policy and now you can use docker desktop without paying a license only for personal usage. But enterprise usage requires a license.

![](/assets/img/2022-07-22/01.png)

The positive side is that you can still use for free docker-cli, docker engine, etc, so you can still run docker containers.

If you are working on Windows, you are going to be able to run docker on WLS 2. WSL (Windows Subsystem for Linux) allows you to run a Linux distribution on Windows. In order to configure it, there are many articles that explain how to do it (for example [this](https://dev.to/_nicolas_louis_/how-to-run-docker-on-windows-without-docker-desktop-hik) and [this](https://dev.solita.fi/2021/12/21/docker-on-wsl2-without-docker-desktop.html). This process can very tedious, specially when you find that some commands are not working for you. But you can manage to configure it with some researching.

When you configure docker on Ubuntu, you will able to run docker commands or even install docker-compose in Linux. But the integration with Windows can be not as good as with Docker Desktop. This can make a little harder to work with tools like FluentDocker.

However, I recently tried [Rancher Desktop](https://rancherdesktop.io/), and it simplifies a lot working with docker on Windows, offering a similar experience as working with Docker Desktop.

# Working with Rancher Desktop

Rancher Desktop for windows is a very straightforward application. You just install it as any other applications for Windows, selecting dockerd as container runtime.

Under the hood, rancher is managing for you all the complexity of creating a Linux subsystem and configure it to work with docker. In addition, it offers a great experience when you are working with Windows, since all your docker commands will work automatically, including docker-compose or kubernetes.

![](/assets/img/2022-07-22/1.svg)

The only issue that I had when I installed rancher, is the fact that I couldn't pull any docker images.

![](/assets/img/2022-07-22/2.png)

This is happening because the DNS is not properly configured. I also had that issue when I tried to configure docker on Ubuntu WSL2.

If you are having the same issue, open the command console, go to the linux system created by rancher with the command:

```
wsl -d rancher-desktop
```

Then go to the file `/etc/wsl.conf` and update the value `generateResolvConf` to `false`.

Finally go to the file `/etc/resolv.conf` and add replace the content with:

```
options use-vc
nameserver 8.8.8.8
```

Now you can check that docker commands are pulling the images properly:

![](/assets/img/2022-07-22/3.png)

When you run an image with docker rancher, it will run in localhost, so applications like FluentDocker will continue working as always. Apart from that, it also works with docker-compose and kubernetes.

One feature that I like is hte fact that you can list and manage your images:

![](/assets/img/2022-07-22/4.png)

But there you can not see or manage your containers. For that I would recommend using [this VS Code extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker), which allow you manage images and containers:

![](/assets/img/2022-07-22/5.png)

# Conclusions

In this post, we have seen different alternatives to run Docker on Windows without Docker desktop, since it is not a free tool anymore for companies.

In particular, we have seen how to install and configure Docker Rancher, which gives us a very easy way to keep working with docker on our Windows machines.
