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

## useful docs

AWS, TERRAFORM, CLOUD-INIT, & ANSIBLE

https://developer.hashicorp.com/terraform/tutorials/aws-get-started/aws-build \
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance \
https://registry.terraform.io/providers/hashicorp/cloudinit/latest/docs/data-sources/config \
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/secretsmanager_secret \
https://cloudinit.readthedocs.io/en/latest/reference/examples.html \
https://cloudinit.readthedocs.io/en/latest/reference/modules.html \
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html

about terraform secrets \
https://blog.gruntwork.io/a-comprehensive-guide-to-managing-secrets-in-your-terraform-code-1d586955ace1

## code

you can check my terraform code on github: https://github.com/relyq/infrastructure

## extras

im not using this anymore as im now uploading my artifacts to an S3 bucket instead of github actions, but i wrote this one liner to get the artifact URL from github using gh-cli and some regex magic (which i definitely wrote myself heh)

`export ARTIFACT_URL=$(gh run list --json url -L 1 -R relyq/Tracker_angular | grep -o '".*"' | sed 's/"//g; s/^url://')`

this lets me then download the artifact using some bash parameter substring removal

`gh run download ${ARTIFACT_URL##*/} -R relyq/Tracker_angular`
