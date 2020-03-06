---
title: "Utilities"
description: "Utilities"
date: 2020-01-28T00:34:51+09:00
draft: false
weight: 20
---

This page contains a set of useful scripts to launch dockerized applications in any machine of the MRI Group.

## Copy images

Suppose you want to copy a Docker image to another host without using DockerHub:

```
docker save <image> | bzip2 | pv | ssh -p <port> <username>@<host> 'bunzip2 | docker load'
```

You can omit `-p <port>` if port is standard 22
