---
layout:     post
title:      Deploying a Web App, Redis, Postgres and Nginx with Docker
category:   blog
description: This tutorial introduces how to deploy a web app, Redis, Postgres and Nginx with Docker on the same server. In this tutorial, the web app is a node.js(express) app. We use Redis as a cache store, Postgres as the database, and Nginx as the reverse proxy server. 
---

This tutorial introduces how to deploy a web app, Redis, Postgres and Nginx with Docker on the same server. In this tutorial, the web app is a node.js(express) app. We use Redis as a cache store, Postgres as the database, and Nginx as the reverse proxy server. You can get all source code at [https://github.com/vinceyuan/DockerizingWebAppTutorial](https://github.com/vinceyuan/DockerizingWebAppTutorial).

###Why Docker

Docker is a virtualization technology. The key feature I like most is it provides resource isolation. The traditional way of building a (low-traffic) website is we install the web app, cache, database, Nginx directly on a server. It's not easy to change the settings or the content a lot, because they are in the same environment. Changing one may impact others. With Docker, we can put each service in a container. It keeps the host server very clean. We can easily create/delete/change/re-create containers.

###Install Docker on the host

Docker runs on a 64-bit Linux OS only. If your Linux is 32-bit, you have to re-install the 64-bit version. My original OS was 32-bit CentOS. Now I am using 64-bit Debian 8. The main reason I choose Debian is its distribution size is small and Docker recommends it in [Best Practices](https://docs.docker.com/articles/dockerfile_best-practices/#from)(it's ridiculous that almost all examples at docker.com use ubuntu). Actually the host's OS can be different to the container's OS. I choose Debian instead of 64-bit CentOS because I don't want to spend any time on the differences. For example, the package management tools on Debian and CentOS are different. One is apt, the other is yum.

Currently, Docker's [official installation](https://docs.docker.com/installation/debian/#installation) on Debian 8 does not work. You need to run the following commands as root. theuser is the user of host OS.

```
sudo su
curl -sSL https://get.docker.com/ | sh
# Add theuser to docker group to run docker as a non-root user
# MUST logout and re-login to let it effective
usermod -aG docker theuser
# Start docker service
systemctl enable docker.service
systemctl start docker.service
```

###Prepare

```
cd /
git clone https://github.com/vinceyuan/DockerizingWebAppTutorial.git
```

The folder `/DockerizingWebAppTutorial` contains all we need. mynodeapp is a very simple node.js (express) app. It just reads a number from Redis, and gets a query result from Postgres. There are several Dockerfiles in the dockerfiles folder. We will use them to build images.

Create folders:

```
cd / && mkdir mydata && cd myata
mkdir redis_data && mkdir postgres_data && mkdir nginx_data
root@pophubserver:/mydata# mkdir log_mynodeapp && mkdir log_nginx
```

Let's run the first container.

###Redis

We use the official Redis image. Run it directly with this command:

```
docker run -d -v /mydata/redis_data:/data --name myredis --restart=always redis
```

`-v /mydata/redis_data:/data` means we mount a folder `/mydata/redis_data` of the host as a volume `/data` in a container. Nginx will save `dump.rdb` at `/mydata/redis_data` in the host. If we don't mount a volume, Nginx will save `dump.rdb` in the container. When this container is deleted, `dump.rdb` will be deleted too. So we should always mount a volume for the important data e.g. database file, logs.
`--name myredis` means we name this container myredis
`--restart=always` means the container will restart after it quits unexpectedly. It also makes the container start automatically after the server reboots.

That command outputs:

```
$ docker run -d -v /mydata/redis_data:/data --name myredis --restart=always redis
Unable to find image 'redis:latest' locally
latest: Pulling from redis
7a3e804ed6c0: Pull complete 
b96d1548a24e: Pull complete 
5ba9a5b9710f: Pull complete 
37f07aacbfe5: Pull complete 
ec7f3a6b5dc6: Pull complete 
499b313c4d4e: Pull complete 
4416945429c6: Pull complete 
0daf71066555: Pull complete 
1f86439b265d: Pull complete 
9e6288fa06c0: Pull complete 
3c083702089f: Pull complete 
71cc4c7123fc: Pull complete 
91e5e3734476: Pull complete 
8d7fb9bd09ab: Pull complete 
e6b7cf8bf1b1: Pull complete 
96182c1bd121: Pull complete 
4b7672067154: Already exists 
redis:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
Digest: sha256:01b59520487a9ada4b8e31558c0580930a4e5f2a565a1cb85b66efe7c6ce810d
Status: Downloaded newer image for redis:latest

a96b6d2555e9f9fb1f70fea60f8cf75326cd331ebef9d4b667e322cea899d48c
```

It downloads `redis:latest` image from Docker Hub. Let's check if myredis container is running.

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
a96b6d2555e9        redis:latest        "/entrypoint.sh redi   16 minutes ago      Up 16 minutes       6379/tcp            myredis
```

We can see myredis is running.

We need to run `redis-cli` in this container to set a value in Redis.

```
$ docker exec -i -t myredis bash
root@a96b6d2555e9:/data# redis-cli
127.0.0.1:6379> set number 1
OK
127.0.0.1:6379> save
OK
127.0.0.1:6379> exit
root@a96b6d2555e9:/data# exit
```

###Postgres

We use the official Postgres image too. Just run it directly.

```
docker run -d --name mypostgres -e POSTGRES_PASSWORD=postgres -v /mydata/postgres_data:/var/lib/postgresql/data --restart=always postgres
```

`-e POSTGRES_PASSWORD=postgres` means we set the environment variable `POSTGRES_PASSWORD` to postgres.

`-v /mydata/postgres_data:/var/lib/postgresql/data` means we mount `/mydata/postgres_data` as a volume. This is very important. It's safe to keep database files in the host.

Create mynodeappdb:

```
$ docker exec -i -t mypostgres bash
root@11602c44f706:/# psql -U postgres
psql (9.4.2)
Type "help" for help.

postgres=# create database mynodeappdb;
CREATE DATABASE
postgres=# \q

root@11602c44f706:/# exit
```

We can see mypostgres and myredis are running.

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED              STATUS              PORTS               NAMES
11602c44f706        postgres:latest     "/docker-entrypoint.   About a minute ago   Up About a minute   5432/tcp            mypostgres          
a96b6d2555e9        redis:latest        "/entrypoint.sh redi   32 minutes ago       Up 32 minutes       6379/tcp            myredis
```

###Redis client and Postgres client

The Dockerfile for redis client:

```
FROM debian:7

RUN apt-get update \
 && apt-get install -y redis-server \
 && apt-get clean \
 && apt-get autoremove \
 && rm -rf /var/lib/apt/lists/*

RUN service redis-server stop
```

It's based on `debian:7`. It actually installs both redis server and client. But we only need the client. So it stops redis-server.

Build it:

```
cd /DockerizingWebAppTutorial/dockerfiles/myredisclient
docker build -t myredisclient .
```

The Dockerfile for Postgres client:

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

It's based on `myredisclient`, because our web app needs to access both redis and postgres. The annoying thing is the default postgresql-client in Debian apt is a very old version (`pg_dump` will not work, because the version does not match the server's version). This Dockerfile installs the latest version (currently 9.4).

Build it

```
cd /DockerizingWebAppTutorial/dockerfiles/myredispgclient
docker build -t myredispgclient .
```

We can see there are 5 images in the host.

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
myredispgclient     latest              78b18351c561        6 minutes ago       132.5 MB
myredisclient       latest              bb2ac4846244        8 minutes ago       87.7 MB
postgres            latest              1636d90f0662        2 days ago          214 MB
redis               latest              4b7672067154        4 days ago          111 MB
debian              7                   b96d1548a24e        9 days ago          84.97 MB
```

###Node.js

Let's build a Node.js image. In the Dockerfile for mynodejs image, we install node.js, express, forever and then set `NODE_ENV` production. In this example, I am not using the latest version.

```
FROM myredispgclient

RUN apt-get update \
 && apt-get install -y --force-yes --no-install-recommends \
      apt-transport-https \
      build-essential \
      curl \
      ca-certificates \
      git \
      lsb-release \
      python-all \
      rlwrap \
 && apt-get clean \
 && apt-get autoremove \
 && rm -rf /var/lib/apt/lists/*

RUN curl https://deb.nodesource.com/node/pool/main/n/nodejs/nodejs_0.10.30-1nodesource1~wheezy1_amd64.deb > node.deb \
 && dpkg -i node.deb \
 && rm node.deb

RUN npm install -g express@3.4.7 \
 && npm install -g forever \
 && npm cache clear

ENV NODE_ENV production
```

Build it.

```
cd /DockerizingWebAppTutorial/dockerfiles/mynodejs
docker build -t mynodejs .
```

###mynodeapp

Then we build an image for mynodeapp.  In Dockerfile, we run npm install, and use forever to run the node.js app. We don't use forever start, because we don't run it as a daemon (otherwise, the container will quit immediately).

```
FROM mynodejs

COPY . /src
RUN cd /src && npm install
VOLUME /log
CMD ["forever", "-l", "/log/forever.log", "-o", "/log/out.log", "-e", "/log/err.log", "/src/app.js"]
```

Build it

```
cd /DockerizingWebAppTutorial/mynodeapp
docker build -t mynodeapp .
```

Actually we can merge these 4 Dockerfiles into one to create one image. I build 4 images for re-using images. For example, if we want to build an image for another node.js app, we can write a Dockerfile based on mynodejs image. If we want to replace node.js with Go, we can write a Dockerfile based on myredispgclient.

The core code of mynodeapp:

```
var conString;

if ('development' == app.get('env')) {
  app.use(express.errorHandler());
  conString = "postgres://vince:@localhost/mynodeappdb"; // Use your db, user and password
} else {
 conString = "postgres://postgres:postgres@localhost/mynodeappdb"; // Use your db, user and password
}

var pgClient = new pg.Client(conString);

pgClient.connect(function(err) {
  if(err) return console.error('Could not connect to postgres', err);
  console.log('Connected to postgres');
});

var redisClient = redis.createClient(6379, '127.0.0.1', {})

app.get('/', function(req, res) {
  pgClient.query('SELECT NOW() AS "theTime"', function(err1, result) {
    redisClient.get("number", function(err2, reply) {
      res.render('index', { pgTime: result.rows[0].theTime, number: reply });
    });
  }); // Errors Ignored in this example. You should check errors in a real project. 
});

http.createServer(app).listen(app.get('port'), function() {
  console.log('Express server listening on port ' + app.get('port'));
});
```

There is a problem. We are using `localhost` or `127.0.0.1` for redis and postgres' host address. It works only when they are installed on the same server. But now they are in different containers. Even if we use `--link`, we still cannot access them via `localhost` and `127.0.0.1`. We can use the following code to get correct host and port.

```
var redis_host = process.env.REDIS_PORT_6379_TCP_ADDR || '127.0.0.1';
var redis_port = process.env.REDIS_PORT_6379_TCP_PORT || 6379;
var db_host = process.env.POSTGRES_PORT_5432_TCP_ADDR || 'localhost';
```

`REDIS_PORT_6379_TCP_ADDR` is created by Docker if you run a container with `--link myredis:redis`. You can get Postgres user account, password, port from the environment variables too.

Run a container based on mynodeapp image. We also name the container mynodeapp. You can rename it whatever you like.

```
docker run -d --name mynodeapp --link mypostgres:postgres --link myredis:redis -v /mydata/log_mynodeapp:/log -p 3000:3000 --restart=always mynodeapp
```

By default, each container is isolated. `--link` allows a container access another container. `--link mypostgres:postgres` means we can access mypostgres container with the alias `postgres` just like `localhost` for `127.0.0.1`.
`-v /mydata/log_mynodeapp:/log` mounts a volume. We want to keep logs in the host.
`-p 3000:3000` maps host's port 3000 to container's port 3000. It is not mandatory. But with it, we can use `curl localhost:3000` in the host to check if mynodeapp container runs correctly.

```
$ curl localhost:3000
<!DOCTYPE html><html><head><title></title><link rel="stylesheet" href="/stylesheets/style.css"></head><body><H1>mynodeapp</H1><p>Number from Redis: 1</p><p>Time from Postgres: Fri May 29 2015 09:47:54 GMT+0000 (UTC)</p></body></html>
```

The web app runs correctly in the container. 

###Nginx

Now we install Nginx. In the Dockerfile, we make directory `/mynodeapp/public`. A folder in the host will be mounted here.

```
FROM nginx

# Create folder for static files
RUN mkdir /mynodeapp && mkdir /mynodeapp/public

# copy sslcert files to /etc/nginx/ for https
#COPY mydomain.* /etc/nginx/

# copy conf
COPY nginx-docker.conf /etc/nginx/nginx.conf
```

In nginx-docker.conf, we use mynodeapp for the server address, because it is linked.

```
    upstream mynodeapp_upstream {
        server mynodeapp:3000;
        keepalive 64;
    }
```

Build the image and run the container.

```
cd /DockerizingWebAppTutorial/dockerfiles/mynginx
docker build -t mynginx .
```

Run mynginx container.

```
docker run -d --name mynginx --link mynodeapp:mynodeapp -v /mydata/nginx_data:/var/cache/nginx -v /mydata/log_nginx:/var/log/nginx -v /DockerizingWebAppTutorial/mynodeapp/public:/mynodeapp/public -p 80:80 -p 443:443 --restart=always mynginx
```

`--link mynodeapp:mynodeapp` means we link mynodeapp container to mynginx container. We don't link myredis and mypostgres because mynginx does not access them directly.
We also mount 2 folders for logging. 
`-p 443:443` is for https. However, this example does not provide ssl certificate files.

Run `curl localhost` and `curl localhost/stylesheets/style.css` to check if mynginx runs correctly.

```
# curl localhost
<!DOCTYPE html><html><head><title></title><link rel="stylesheet" href="/stylesheets/style.css"></head><body><H1>mynodeapp</H1><p>Number from Redis: 1</p><p>Time from Postgres: Fri May 29 2015 10:12:35 GMT+0000 (UTC)</p></body></html>root@pophubserver:/DockerizingWebAppTutorial/dockerfiles/mynginx# 

root@pophubserver:/DockerizingWebAppTutorial/dockerfiles/mynginx# curl localhost/stylesheets/style.css
body {
  padding: 50px;
  font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
}

a {
  color: #00B7FF;
}
```

Now we finished deploying a web app, Redis, Postgres and Nginx with Docker. It took me a lot of time to really deploy my real app with Docker. Luckily I tested in a VirtualBox VM. I can delete/create images/containers back and forth easily with Docker.

An important part is missing. That's restoring and backing up database. I will show you in [another tutorial](/restoringbacking-up-postgres-database/). Here are some [tips](/docker-tricks-and-tips/) about Docker.

