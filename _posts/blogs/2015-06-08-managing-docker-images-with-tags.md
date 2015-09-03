---
layout:     post
title:      Managing Docker images with tags
category:   blog
description: When you build Docker images many times, you will find it is very necessary to manage images with tags.
---

When you build Docker images many times, you will find it is very necessary to manage images with tags.

If you build an image without a tag, the default tag 'latest' is created automatically.

```
docker build -t company/myimage .
```

The complete name is `company/myimage:latest`

When you need to update a new version of `company/myimage`, you don't need to create an image `company/myimage_v2`. You should create an image with the same name and a new tag.

```
docker build -t company/myimage:v2 .
```

And set it latest

```
docker tag -f company/myimage:v2 company/myimage:latest
```

So your new image has two tags: `v2` and `latest`. When you start a container based on `company/myimage`, `company/myimage:latest` will be used.

