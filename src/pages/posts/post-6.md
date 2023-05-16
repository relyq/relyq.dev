---
layout: ../../layouts/MarkdownPostLayout.astro
title: "5. cicd with aws & github actions"
pubDate: 2023-05-15
description: "github actions + aws"
author: "relyq"
type: "blog"
tags: ["devops", "aws", "ec2", "terraform", "cicd", "github actions"]
---

same as [last time](/posts/post-5) i'll be writing a short post about how i set up my aws cicd pipeline with github actions & terraform

in the last post i had set up my terraform code so it downloaded the latest compiled angular build from github actions, and so it cloned my api repository and built it on the newly created ec2 instance for my api.

this is obviously not ideal, so i've now set my cicd pipeline to instead compile my angular & dotnet code and upload the artifacts to an AWS S3 bucket.

i also created two AWS lambdas that get triggered every time the artifacts on this bucket get updated. these two lambdas in turn call a codedeploy revision which copies the artifacts to the EC2 instances and restart my services so i get my latest build as soon as new code is pushed to my repos

to make this work i had to:

- create the two new lambdas
- set the lambdas permissions to read the S3 bucket and trigger codedeploy
- add the new policies to my EC2 instances so they could be managed by codedeploy
- update my github actions cicd code so it uploaded the artifacts to the S3 bucket instead of to github

you can check my infrastructure code on github: https://github.com/relyq/infrastructure
