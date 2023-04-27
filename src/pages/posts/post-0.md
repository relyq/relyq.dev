---
layout: ../../layouts/MarkdownPostLayout.astro
title: "0. learning terraform"
pubDate: 2023-04-23
description: "setting up terraform for proxmox"
author: "relyq"
tags: ["terraform", "proxmox", "devops"]
---

### 19:44

today i decided i'll learn terraform \
i guess it'd be nice to use it with AWS but i haven't learned AWS yet and [my self-hosted infrastructure](/projects/homelab) is based on proxmox so i'll be using that instead \
it will probably transfer to AWS whenever i pick that up anyways

## plan

1. **installing terraform**: installing the terraform client for windows - should be as easy as `winget install hashicorp.terraform` and following [this guide](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

2. **download provider**: i'll use [this proxmox provider by telmate](https://github.com/Telmate/terraform-provider-proxmox) - [docs here](https://registry.terraform.io/providers/Telmate/proxmox/latest/docs)

3. **read guide article**: next i'll be using [this article](https://tcude.net/using-terraform-with-proxmox/) as a guideline on how to proceed

## what's next

i guess i'll try to recreate some of my current containers using terraform \
as an extra i'll also try to do some of the things on the [cloudresumechallenge page for terraform](https://cloudresumechallenge.dev/docs/extensions/terraform-getting-started/)

## progress

### 21:10

well that was pretty easy \
just finished the first test creating & destroying an LXC using terraform. i had to write my own main.tf file as the one in the guide was for a VM instead of an LXC but doing that was simple enough reading the telmate/proxmox docs
