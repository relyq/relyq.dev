---
layout: ../../layouts/MarkdownPostLayout.astro
title: "5. terraforming aws"
pubDate: 2023-05-15
description: "terraform + aws"
author: "relyq"
type: "blog"
tags: ["devops", "aws", "ec2", "terraform"]
---

this blog will be a review of what i've done instead of the usual posts till now as i already terraformed my infrastructure

my terraform code consists of the following parts

- networking

  - vpc
  - subnet
  - gateway
  - route table
  - security groups
  - certificate (\*.relyq.dev)
  - dns records (using porkbun's provider)

- secrets (stored on aws ssm params store)

  - cert privkey
  - api secrets
  - porkbun api key (domain provider)

- ec2 instances

- cloud-init / scripts

terraform will set up my ec2 instances (one for my frontend and one for my api) with their networking settings, add new A records pointing to the new instances ip addresses, and cloud-init will deploy my certificate to both instances and download my latest builds from github actions

you can check my terraform code on github: https://github.com/relyq/infrastructure
