---
layout: ../../layouts/MarkdownPostLayout.astro
title: "2. learning aws"
pubDate: 2023-04-27
description: "awsawsaws"
author: "relyq"
type: "blog"
tags: ["devops", "aws"]
---

### april 27th

### 19:57

it is time \
this feels like a big undertaking but i already know some of the underlying concepts as im already hosting my own on-prem server so i think the knowledge will transfer and hopefully it will be a smooth ride \
from what i've seen there are a lot of services and a fuckton of things to learn and experiment with so first thing i'll do will be deciding what to start with

## plan

1. **list some resources & important things**:

- [aws free tier](https://aws.amazon.com/free)
- [aws training](https://aws.amazon.com/training/)
- [aws workshops](https://workshops.aws/)
- [aws cloud practitioner cert](https://aws.amazon.com/certification/certified-cloud-practitioner/) & [training](https://aws.amazon.com/training/learn-about/cloud-practitioner/)
- [aws sysops admin cert](https://aws.amazon.com/certification/certified-sysops-admin-associate/)
- [aws solutions architect cert](https://aws.amazon.com/certification/certified-solutions-architect-associate/)
- [open guides aws repo](https://github.com/open-guides/og-aws)
- [reddit post about security](https://www.reddit.com/r/aws/comments/mu0d38/i_want_to_learn_aws_where_should_i_start/iqf1wfb/)

2. **create an account**: and make sure it's setup securely
3. **get something working**: i'll probably start with a simple website and go from there
4. **uhhh**: explore services??

## what's next

i'll decide what's next once i get something working \
i'll probably try to host a webapp with an api & db

### 21:48

now that i've set up my aws account i think i should work on some of these things:

- [host a static website](https://aws.amazon.com/getting-started/hands-on/host-static-website/)
- [deploy a webapp](https://aws.amazon.com/getting-started/guides/deploy-webapp-decision/)
- [deploy a container](https://aws.amazon.com/getting-started/guides/deploy-containers-decision)
- [run serverless code](https://aws.amazon.com/getting-started/hands-on/run-serverless-code)

i should also explore the [free tier](https://aws.amazon.com/free) once i understand how things work

## progress

### 21:40

checkpoint - aws cli is working with sso

what i've done so far:

1. created my aws account
2. created an organization
3. added mfa to the root acc
4. created an iam acc
5. added a user, assigned it to the iam acc, and added the PowerUserAccess perm set (and added mfa to this sso too)
6. logged in with the AWS CLI using sso

- added a zero-spend budget with alerts

### april 28th

### 03:23

- uploaded a copy of this website to an [S3 bucket](http://relyqbucket0.s3-website-sa-east-1.amazonaws.com)

i think i'm beginning to understand how everything works together. in the [next blog post](/posts/post-4) i'll try to host [my issue tracker webapp](/projects/tracker) on EC2 + RDS + lambda
