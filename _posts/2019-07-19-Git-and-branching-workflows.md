---
layout: post
title: Git and branching workflows
date: 2019-07-19 07:26:48 UTC
updated: 2019-07-19 07:26:48 UTC
comments: false
categories: CI/CD git Gitflow product delivery
---

[![](https://1.bp.blogspot.com/-ofEan6TGyM0/XTC0t8jlgoI/AAAAAAAAFqI/3PxMfRDjgqAvpDw32lCX9-VOhBS8rBcrwCLcBGAs/s640/git.png)](https://1.bp.blogspot.com/-ofEan6TGyM0/XTC0t8jlgoI/AAAAAAAAFqI/3PxMfRDjgqAvpDw32lCX9-VOhBS8rBcrwCLcBGAs/s1600/git.png)

## Introduction

When you are working in a software project, one of the most important aspects is your branching strategy. If you are using GIT (and if don't, probably you should consider it), there are several strategies, such as Gitflow, Github flow and Gitlab flow, etc.

In this article, I will try to introduce them and analyse pros and cons for each one. Spoiler alert, there are advantages and disadvantages for all of them, so taking that into account, you should consider which one fits better for your project and team.

## Environments and software life-cycle

First of all, in order to understand why we need a particular branching strategy, we need to have clear which is the relationships between versioning, environments and software life-cycle.

Normally, in software development, we can find three environments, where we will be deploying our versions for different purposes. These can be more than three, but lets focus on the basic pipeline. I will call those environments "Dev", "Test" and "Production" (although there are other conventions like "Dev", "Staging" and "Stable").

[![](https://1.bp.blogspot.com/-c-HWf445OBY/XTC5huQrphI/AAAAAAAAFqY/297IPC9awUg4eHzjo5fHN91kLe_7wEe_gCLcBGAs/s640/dev_test_prod.png)](https://1.bp.blogspot.com/-c-HWf445OBY/XTC5huQrphI/AAAAAAAAFqY/297IPC9awUg4eHzjo5fHN91kLe_7wEe_gCLcBGAs/s1600/dev_test_prod.png)

### Dev Environment 

Dev is the environment for development. If we are using CI/CD, each commit should be automatically deployed here. Normally, in Dev environment we can deploy and test functionally that we are currently developing for next version.

### Test Environment

Test environment is used when we have a particular version, which we are planning to release. In test environment, we should execute more intensive testing and fix the issues we find to make this release stable for production. In Test environment QA's will have a very relevant role. This environment is also known as Pre-Production, Release, QA, etc.

### Production Environment

Production environment is the environment that our final users will see and use. For this reason, if we find a bug in this environment and it is really critical for our users, we should hotfix that bug to sort it out as soon as possible.

So all versions that we deliver follow the flow Dev -> Test -> Production.

## Gitflow

[![](https://1.bp.blogspot.com/-zoegK6Ai5Tk/XTC7mmb3Y4I/AAAAAAAAFqk/c_oPuLqfesYfK_fZfw06YsaYROTfoY1xQCLcBGAs/s640/1_9yJY7fyscWFUVRqnx0BM6A.png)](https://1.bp.blogspot.com/-zoegK6Ai5Tk/XTC7mmb3Y4I/AAAAAAAAFqk/c_oPuLqfesYfK_fZfw06YsaYROTfoY1xQCLcBGAs/s1600/1_9yJY7fyscWFUVRqnx0BM6A.png)

[Gitflow](https://nvie.com/posts/a-successful-git-branching-model/) is the most known branching strategy, which tries to cover all the scenarios that we can find during our software life-cycle.

In Gitflow there are two branches with infinite lifetime:

- **Develop**: in this branch, the team integrate all new developments and fixes for future versions.
- **Master**: this branch contains the versions which are ready to deploy to production.

And there are also some temporary branches.

- **Release**: this branch will contain new Releases in order to make it stable before going to production. When a new release is stable, we destroy release branch, merging the changes (if any) to master and develop.
- **Feature**: when we start a new development, we create a new feature branch and we destroy it when we merge it to Develop.
- **Hotfix**: if there is a critical bug in production, we create a temporary hotfix branch and we destroy it when we solve it, merging the changes to master and develop.

### Advantages

- There is a clear and well defined way to proceed, which cover most of the situations that we will find during our software life-cycle
- There is a clear mapping between environments and branches:

- Develop -> Dev Environment
- Relese -> Test Environment
- Master -> Production Environtment

- It is a well known flow broadly used.
- Some tools allows you to automatise the flow configuring gitflow branches (for example SourceTree).
- You are sure that your versions are following a quality process as they progress through the workflow.

### Disadvantages

- You need to educate your team to use branches properly.
- Sometimes to you need to merge into two branches (Releases and Hotfix).

## Github flow

[![](https://1.bp.blogspot.com/-mev97K9l4u8/XTC8GDkP9aI/AAAAAAAAFqs/Gf67VvBnx3QoiHlfWR1vDRMOroULWxpWQCLcBGAs/s640/1_iHPPa72N11sBI_JSDEGxEA.png)](https://1.bp.blogspot.com/-mev97K9l4u8/XTC8GDkP9aI/AAAAAAAAFqs/Gf67VvBnx3QoiHlfWR1vDRMOroULWxpWQCLcBGAs/s1600/1_iHPPa72N11sBI_JSDEGxEA.png)

[Github flow](https://guides.github.com/introduction/flow/) is a simplified flow, which only two kind of branches:

- **Master**: this branch has infinite lifetime and always there is a change on Master, this should be deployed.
- **Feature/Bugfix branch**: This branches are created to add a modification, and they are destroyed after merging to master.

### Advantages

- Really simple flow, which works really well for open source projects.
- CI/CD friendly, as you deliver each change as soon as it comes to master branch.

### Disadvantages

- There is not a quality assurance stage for new releases, which can be not realistic for enterprise projects.
- It doesn't allow to hotfix a production version without delivering a new version from master.
- There is not a clear matching between environments and branches, which can lead to misunderstandings.
- Assuming master is free errors is not realistic and it can be risky.

## Gitlab flow

[Gitlab flow](https://docs.gitlab.com/ee/workflow/gitlab_flow.html) is a branching strategy which tries to combine the simplicity of Github flow with the environments-oriented branching strategy of Gitflow. As a result, there are several workflows, depending on the needs.

**Production branch with GitLab flow:** it is the result of adding a production branch to Github flow.

[![](https://1.bp.blogspot.com/-gD_PLt5w0UI/XTC8dmqn6cI/AAAAAAAAFq4/yVVXHylUgOg7N_CcXGqSBVlSNVqW12rMwCLcBGAs/s320/production_branch.png)](https://1.bp.blogspot.com/-gD_PLt5w0UI/XTC8dmqn6cI/AAAAAAAAFq4/yVVXHylUgOg7N_CcXGqSBVlSNVqW12rMwCLcBGAs/s1600/production_branch.png)

### Advantages

- You don't need to deploy each merge that is completed on master, which gives you more control over the delivery.
- You always have a snapshot of what you have in production in production branch.
- There is only one way for merges.

### Disadvantages

- There is not a ¡release validation stage to make stable in isolation what you are going to deliver to production.
- There is not a way to hotfix production branch without delivering all the changes you have on master, which can be a big limitation, depending on your delivery pipeline.
- As for Github flow, you have to assume that master is free of errors, which can be not realistic.

**Environment branches with GitLab flow**: this flow proposes to have an infinite life-time branch per environment.

[![](https://1.bp.blogspot.com/-fBGRi4X4AFk/XTC8Xfo-CHI/AAAAAAAAFq0/eOEsa9qHHm8Q7nx6D9PO13zHIFH45AWpwCLcBGAs/s320/environment_branches.png)](https://1.bp.blogspot.com/-fBGRi4X4AFk/XTC8Xfo-CHI/AAAAAAAAFq0/eOEsa9qHHm8Q7nx6D9PO13zHIFH45AWpwCLcBGAs/s1600/environment_branches.png)

### Advantages

- There is mapping between branches and environments.

### Disadvantages

- Same disadvantages as for the previous approach.

## Conclusions

Now that you know the most relevant git branching strategy, you might know which is the one that fits for your project.

Based on my personal experience, simple approaches like Github flow or Gitlab flow are cool for open source projects or when there is not a third party client. However, I think in those approaches there are gaps and more risk involved when it comes to enterprise projects. Even when you have a decent set of automatic testing, for enterprise projects we normally have a QA stage in order to ensure that the new release that you are delivering to your clients is stable.

In addition, if you are not in charge of delivering timelines or you have several production clients, the concept of CI/CD per each PR will be problematic as you cannot decide what you want to deliver and when you are going to deliver a new version.

Finally, your clients might require a hotfix for the current production version, but they don't want any new functionality. For this scenario, Gitflow is the only one which can support this by definition.
