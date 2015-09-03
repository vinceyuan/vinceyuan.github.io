---
layout:     post
title:      Restoring/Backing up Postgres Database in a Docker Container
category:   blog
description: This post shows you how to restore and backup Postgres database which is running in a Docker container.
---

In the [previous tutorial](/deploying-web-app-redis-postgres-and/), I show you how to deploy a web app, Redis, Postgres, Nginx with Docker. This post shows you how to restore and backup Postgres database which is running in a Docker container. You can get all source code at [https://github.com/vinceyuan/DockerizingWebAppTutorial](https://github.com/vinceyuan/DockerizingWebAppTutorial).

I use `pg_dump` to dump a database to a text file. And then zip and upload it to Amazon S3 with a handy command line tool s3cmd. In this tutorial, I can't provide `access_key` and `secret_key` of S3. So you may not really run it until you get your own.

Let's install s3cmd on the host. 

```
apt-get install -y s3cmd
```

Run `s3cmd` to configure. You should input your `access_key` and `secret_key` of S3. It creates `.s3cfg` at your home directory.

###Restore

In the previous tutorial, after running Postgres in a container, we should restore database from the backup. We need to get the backup first.

```
cd /mydata && mkdir db_restore && cd db_restore
```

List backup files in your s3.

```
s3cmd ls s3://your_db_dumps/
```

Download a backup file and unzip.

```
s3cmd get s3://db_dumps/dump2015-05-22T09:15:13+0800.txt.gz
gunzip dump2015-05-22T09\:15\:13+0800.txt.gz
```

Run a container with Postgres client to restore db from dump.

```
docker run -d --name myredispgclient --link mypostgres:postgres -v /mydata/db_restore/:/tmp/ -i -t myredispgclient /bin/bash
```

If it does not show the console of the container, run this to get it

```
# docker exec -i -t myredispgclient bash $ env | grep POSTGRES_
$ psql -h $POSTGRES_PORT_5432_TCP_ADDR -p $POSTGRES_PORT_5432_TCP_PORT -U postgres
> \l # List all databases. Make sure mynodeappdb exists
> \q # Quit
$ psql -h $POSTGRES_PORT_5432_TCP_ADDR -p $POSTGRES_PORT_5432_TCP_PORT -U postgres -d mynodeappdb < /tmp/dump2015-05-22T09\:15\:13+0800.txt
$ exit # Exit the console of the container
# docker stop myredispqclient
```

###Backup

Let's build mys3cmd image which has s3cmd 1.0 installed. The latest version is 1.5.2. But 1.5.2 fails to upload big files for me. So I have to use 1.0. This is the Dockerfile. It is based on myredispgclient image.

```
FROM myredispgclient

RUN apt-get update \
 && wget -O- -q http://s3tools.org/repo/deb-all/stable/s3tools.key | apt-key add - \
 && wget -O/etc/apt/sources.list.d/s3tools.list http://s3tools.org/repo/deb-all/stable/s3tools.list \
 && apt-get update \
 && apt-get install -y --force-yes --no-install-recommends s3cmd=1.0.0-4 \
 && apt-get clean \
 && apt-get autoremove \
 && rm -rf /var/lib/apt/lists/\*
```

Build it.

```
cd /DockerizingWebAppTutorial/dockerfiles/mys3cmd
docker build -t mys3cmd .
```

Let's build `mydbbackup2s3` image for backing up. I tried many times to write a working Dockerfile for mydbbackup2s3. That's why I create mys3cmd image. With mys3cmd image, it doesn't need to download and install s3cmd when I re-build mydbbackup2s3 again and again. In the Dockerfile of mydbbackup2s3, we copied `.pgpass` and `.s3cfg` to `/root/` (You need to provide `.s3cfg` yourself). `.pgpass` stores the password of the database and we have to `chmod 600` for it. `dump_db_and_upload.sh` is a script I wrote to dump, zip, and upload the backup to s3.

```
FROM mys3cmd

COPY .pgpass /root/
COPY .s3cfg /root/
COPY dump_db_and_upload.sh /root/

RUN chmod 600 /root/.pgpass

VOLUME /db_dumps

CMD ["/root/dump_db_and_upload.sh"]
```

The first `postgres` in `.pgpass` is the linked container.

```
postgres:5432:mynodeappdb:postgres:postgres
```

dump_db_and_upload.sh

```
#!/bin/bash
# A script to dump db and compress it and then upload the file to S3.
# should change mode like 'chmod 777 dump_db_and_sync.sh'

FILENAME=$(TZ=Asia/Hong_Kong date +"dump%Y-%m-%dT%H:%M:%S+0800.txt.gz")
FULLDIR="/db_dumps/"
FULLPATH="$FULLDIR$FILENAME"
S3PATH="s3://db_dumps/"
echo "Begin to dump mynodeappdb to $FULLPATH"

# We don't use $POSTGRES_PORT_5432_TCP_ADDR for host, but use postgres which is linked
# $POSTGRES_PORT_5432_TCP_ADDR will change, but link name postgres does not change.
# We also use the link name postgres in .pgpass

pg_dump -h postgres -U postgres mynodeappdb | gzip > $FULLPATH
echo "Done"
echo "Begin to upload the dump to $S3PATH"
s3cmd put $FULLPATH $S3PATH
echo "Done"
echo "Delete the local dump"
rm $FULLPATH
echo "Finished dump and upload"
```

Build the image.

```
cd /DockerizingWebAppTutorial/dockerfiles/mydbbackup2s3
docker build -t mydbbackup2s3 .
```

Run the container. We don't add `--restart=always` here.

```
docker run -d --name mydbbackup2s3 --link mypostgres:postgres -v /mydata/db_dumps:/db_dumps mydbbackup2s3
```

It should dump database and backup immediately. The container will quit after backing up is done.

###Auto-backup

We should backup the database regularly and automatically. Let's create a cron job by running this command:

```
crontab -e
```

Input the following lines, save and quit. It will run mydbbackup2s3 container to backup database every Tuesday.

```
#MIN (0-59)  HOUR (0-23)  DoM (1-31)  MONTH (1-12)  DoW (0-7)     CMD
#Dump pophub db and upload to S3 every Tuesday at 10:00 Hong Kong time
     0            2           *           *            2          docker start mydbbackup2s3
```

Here are some [tips](/docker-tricks-and-tips/) about Docker.
