---
layout: ../../layouts/MarkdownPostLayout.astro
title: "7. dockerizing a webapp & deploying it with docker compose"
pubDate: 2023-05-16
description: "docker + compose"
author: "relyq"
type: "blog"
tags: ["devops", "docker"]
---

it would be nice to have the webapp available as two docker containers

that's what i'll do today

after learning iac writing dockerfiles is more or less trivial. here are the dockerfiles for both containers:

[angular frontend](https://github.com/relyq/Tracker_angular/blob/master/Dockerfile)

```
# syntax=docker/dockerfile:1
FROM node:latest as node
WORKDIR /app
COPY . .
RUN npm install
RUN npm run build --prod

FROM nginx:alpine
COPY --from=node /app/dist/tracker /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
EXPOSE 443
```

[dotnet api](https://github.com/relyq/Tracker_webapi/blob/master/Dockerfile)

```
# syntax=docker/dockerfile:1
FROM mcr.microsoft.com/dotnet/sdk:6.0 as build
WORKDIR /src
COPY . .
RUN dotnet restore
RUN dotnet publish -o /publish

FROM mcr.microsoft.com/dotnet/aspnet:6.0 as runtime
RUN apt-get update && apt-get install -y python3
WORKDIR /publish
COPY --from=build /publish .
RUN chmod +x scripts/*
EXPOSE 7004
ENTRYPOINT ["dotnet", "Tracker.dll"]
```

and here's the [docker-compose file](https://github.com/relyq/infrastructure/blob/master/tracker-docker/docker-compose.yml) to deploy both containers with the corresponding ssl cert & secrets

```
version: "3.9"
services:
  nginx:
    image: "tracker-nginx"
    volumes:
      - "${TRACKER_CERT_PATH}:/etc/ssl/"
    ports:
      - "80:80"
      - "443:443"
  api:
    image: "tracker-dotnet"
    ports:
      - "7004:7004"
    volumes:
      - "${TRACKER_CERT_PATH}:/etc/ssl/"
    environment:
      - ASPNETCORE_URLS=https://+:7004
      - ASPNETCORE_HTTPS_PORT=7004
      - ASPNETCORE_CONTENTROOT=/publish
      - ASPNETCORE_ENVIRONMENT
      - Secrets__SQLConnection
      - Jwt__Key
      - Secrets__SMTPPassword
      - Tracker__BaseUrl
```
