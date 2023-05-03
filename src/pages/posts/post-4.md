---
layout: ../../layouts/MarkdownPostLayout.astro
title: "4. deploying a webapp to aws"
pubDate: 2023-05-02
description: "awsawsaws pt 2"
author: "relyq"
tags: ["devops", "aws", "ec2", "rds"]
---

### may 2nd

### 02:06

already started deploying some things manually to aws so i'll start documenting a bit here \
i also decided i will be using ec2 for the api instead of lambda/gateway as i'd have to change my code for that to work. i can try that later but ec2 will work for now

## plan

1. **deploy frontend**: set up an nginx server on ec2 with the webapp's angular frontend
2. **deploy database**: set up an sql server instance on rds and import my db
3. **deploy api**: set up another ec2 with my .net webapi

## progress

### 02:10

i've already deployed my frontend yesterday \
just now i created a new rds with sql server and im in the process of deploying my .bak

i already set up the role and option groups but for some reason when i run the `msdb.dbo.rds_restore_database` procedure the db is not getting restored so i'll have to figure that out

### 06:48

came back to this and found a way to import it from my server via the SSMS gui. however it wont work that way as im running SQL server 16 and AWS runs SQL server 14. \
i think generating a script for SQL server 2017 should work just fine

### 07:30

finally could create the db with the generated script

### may 3rd

### 08:20

and i just deployed the webapi to an ec2 instance and everything appears to be working as intended \
here's the aws-hosted webapp: https://aws.relyq.dev

next i will terraform my infrastructure and after that i'll make a ci/cd pipeline
