---
layout: ../../layouts/MarkdownPostLayout.astro
title: "3. detour - learning github actions - ci/cd"
pubDate: 2023-04-28
description: "it do be continuous"
author: "relyq"
type: "blog"
tags: ["devops", "ci-cd", "github actions"]
---

### april 28th

### 09:10

spent a few hours learning a bit of ci/cd before deploying a webapp to aws as i figured it would be useful \
took a bit to figure each job ran on its own instance so they didnt share artifacts but now updates to this site are deployed automatically \
the repo might not be public yet but here's the workflow file: https://github.com/relyq/relyq.dev/blob/master/.github/workflows/astro.yml

## useful docs

https://docs.github.com/en/actions/learn-github-actions

## code

```
name: Deploy Astro site to ftp server

on:
  push:
    branches: ["master"]

  workflow_dispatch:

concurrency:
  group: "ftp"
  cancel-in-progress: false

env:
  BUILD_PATH: "."

jobs:
  build:
    name: build & deploy
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3.6.0
        with:
          node-version: "18.16.0"

      - name: setup pnpm
        uses: pnpm/action-setup@v2.2.4
        with:
          version: 7.18.1
          run_install: false

      - name: Get pnpm store directory
        id: pnpm-cache
        shell: bash
        run: |
          echo "STORE_PATH=$(pnpm store path)" >> $GITHUB_OUTPUT

      - uses: actions/cache@v3
        name: Setup pnpm cache
        with:
          path: ${{ steps.pnpm-cache.outputs.STORE_PATH }}
          key: ${{ runner.os }}-pnpm-store-${{ hashFiles('**/pnpm-lock.yaml') }}
          restore-keys: |
            ${{ runner.os }}-pnpm-store-

      - name: install dependencies
        run: pnpm install

      - name: Build with Astro
        run: pnpm astro build
        working-directory: ${{ env.BUILD_PATH }}

      - name: FTP Deploy
        id: ftp
        uses: SamKirkland/FTP-Deploy-Action@v4.3.4
        with:
          server: ${{ secrets.ftp_server }}
          username: ${{ secrets.ftp_username }}
          password: ${{ secrets.ftp_password }}
          port: 21
          protocol: ftp
          local-dir: ${{ env.BUILD_PATH }}/dist/
          server-dir: public_html/
          exclude: |
            **/.git*
            **/.git*/**
            **/node_modules/**
            **/resume.pdf
```
