---
layout: ../../layouts/MarkdownPostLayout.astro
title: "6. cicd with aws & github actions"
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

- update my github actions cicd code so it uploaded the artifacts & appspec files to the S3 bucket instead of to github
- create the two new lambdas
- set the lambdas permissions to read the S3 bucket and trigger codedeploy
- add the new policies to my EC2 instances so they could be managed by codedeploy

you can check my infrastructure code on github: https://github.com/relyq/infrastructure

## code

### github actions

here's the github actions .yaml files to build the projects and deploy the artifacts to my S3 bucket

for the [angular frontend](https://github.com/relyq/Tracker_angular/blob/master/.github/workflows/node.js.yml)

```
name: Node.js CI

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]
  workflow_dispatch:

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Use Node.js 18.x
      uses: actions/setup-node@v3
      with:
        node-version: 18.x
        cache: 'npm'
    - run: npm ci
    - run: npm run build --if-present

    - name: copy codedeploy files
      run: cp .aws/* dist/

    - name: tar artifact
      id: tar
      run: mkdir build && tar -czf build/frontend.tar.gz -C dist .

    - name: upload tar.gz build artifact to s3
      uses: jakejarvis/s3-sync-action@v0.5.1
      env:
        AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        AWS_REGION: 'us-east-1'
        SOURCE_DIR: 'build'
        DEST_DIR: 'frontend'

    - name: upload build artifact to github
      uses: actions/upload-artifact@v3
      with:
        name: dist
        path: dist/tracker/
```

for the [dotnet api](https://github.com/relyq/Tracker_webapi/blob/master/.github/workflows/dotnet.yml)

```
name: .NET

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]
  workflow_dispatch:

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: 6.0.x
    - name: publish
      run: dotnet publish --self-contained -r linux-x64

    - name: copy codedeploy files
      run: cp .aws/* bin/Debug/net6.0/linux-x64/

    - name: tar artifact
      id: tar
      run: mkdir build && tar -czf build/api.tar.gz -C bin/Debug/net6.0/linux-x64/ publish appspec.yml clean.sh post_install.sh start_api.sh stop_api.sh

    - name: upload tar.gz build artifact to s3
      uses: jakejarvis/s3-sync-action@v0.5.1
      env:
        AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        AWS_REGION: 'us-east-1'
        SOURCE_DIR: 'build'
        DEST_DIR: 'api'

    - name: upload build artifact to github
      uses: actions/upload-artifact@v3
      with:
        name: build
        path: bin/Debug/net6.0/linux-x64/publish
```

### codedeploy

both of these copy a codedeploy appspec.yaml file with some basic scripts that do some cleanup & post-install permission setup

[frontend codedeploy files](https://github.com/relyq/Tracker_angular/tree/master/.aws)\
[dotnet api codedeploy files](https://github.com/relyq/Tracker_webapi/tree/master/.aws)

### lambda

both lambdas look the same except for the bucket they reference. these are triggered when the artifact on the S3 bucket gets updated

```
import boto3

codedeploy = boto3.client('codedeploy')

def lambda_handler(event, context):
    return codedeploy.create_deployment(
        applicationName='tracker',
        deploymentGroupName='[api/frontend]',
        revision={
            'revisionType': 'S3',
            's3Location': {
                'bucket': '[tracker_bucket]',
                'key': '[api/frontend]/artifact.tar.gz',
                'bundleType': 'tgz'
            }
        },
    )
```

### terraform

finally i updated my terraform code so it downloads the artifacts from the bucket instead of github. this just meant changing a line on my cloud-init runcmd

`- aws s3 cp --region us-east-1 s3://tracker_bucket/[api/frontend]/artifact.tar.gz /home/ec2-user/`
