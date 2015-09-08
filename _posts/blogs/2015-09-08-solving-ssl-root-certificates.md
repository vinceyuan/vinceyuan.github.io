---
layout:     post
title:      Solving the SSL root certificates issue
category:   blog
description: Fixing this error x509 failed to load system roots and no roots provided in a Docker container
---

I created a docker container for a golang app, based on debian 7 image. Everything works well until it visits https. The error is `x509: failed to load system roots and no roots provided`.

I thought there might be something that should be configured for golang. But actually, it is nothing about golang. It's about SSL certificates. For debian, /etc/ssl/certs/ca-certificates.crt is the root certificates. But the offical debian 7 image does not contain it. Installing ca-certificates via apt-get solved the problem. (You can manually copy /etc/ssl/certs/ca-certificates.crt from another linux OS too.)

Dockerfile:

```
FROM debian:7
RUN apt-get update \
 && apt-get install -y --force-yes --no-install-recommends \
      apt-transport-https \
      curl \
      ca-certificates \
 && apt-get clean \
 && apt-get autoremove \
 && rm -rf /var/lib/apt/lists/*
 ```

