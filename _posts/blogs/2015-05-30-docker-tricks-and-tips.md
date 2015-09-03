---
layout:     post
title:      Docker Tricks and Tips
category:   blog
description: Recently I deployed my node.js app, Redis, Postgres, Nginx with Docker and wrote a tutorial. I want to share you with some tricks and tips about Docker.
---

Recently I deployed my node.js app, Redis, Postgres, Nginx with Docker and wrote a [tutorial](/deploying-web-app-redis-postgres-and/). I want to share you with some tricks and tips about Docker.

###Choose Debian as the base image.

Debian image is much smaller than other OS images. It is recommended in Docker's official [best practices](https://docs.docker.com/articles/dockerfile_best-practices/#from). A problem is the packages in Debian apt are not always latest. I think Debian only likes the very stable versions. You may need to spend some time installing the proper packages.

###Choose the same base image for all your own images.

If your images are based on the same base image, it will save you a lot of disk spaces. Your images share the same base image on disk.

###Build images for each step.

If your node.js app needs to access Redis, Postgres, you'd better create a Redis client image, a Postgres client image, a node.js image, and your app image. Each image is based on the previous one. If you have 2 node.js app, you don't need to install Redis, Postgres clents, node.js again, just use the node.js image as the base.

###Put your important data at the host instead of the container.

If a container is deleted, all files in the container will be deleted too. Use volume for the important data.

###Be very careful with the image whose Dockerfile has VOLUME.

If you use this image without providing a folder of the host, the container will create a volume at /var/lib/docker/vfs/dir/. When the container is deleted, that volume will not be deleted automatically. If big data is stored there, it will use up your disk space. You can install this useful tool https://github.com/cpuguy83/docker-volumes on your host to find and remove the dangling volumes.

###You can run docker without sudo.

```
# Add theuser to docker group to run docker as a non-root user
# MUST logout and re-login to let it effective
usermod -aG docker theuser
```

###Delete the unnecessary files in the Dockerfile to save disk space.

For example,

```
FROM myredisclient

RUN apt-get update \
 && apt-get install -y wget \
 && echo 'deb http://apt.postgresql.org/pub/repos/apt/ wheezy-pgdg main' >> /etc/apt/sources.list.d/pgdg.list \
 && wget --no-check-certificate --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
 && apt-get update \
 && apt-get install -y --force-yes postgresql-client \
 && apt-get clean \
 && apt-get autoremove \
 && rm -rf /var/lib/apt/lists/*
```

