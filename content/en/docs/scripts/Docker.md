---
title: "Docker"
date: 2020-02-13T14:43:41+01:00
lastmod: 2020-02-13T14:43:41+01:00
publishdate: 2020-02-13T14:43:41+01:00
description: ""
weight: 10
---

# README

This page contains a set of useful scripts to launch dockerized applications in any machine of the MRI Group.

## Freesurfer

```bash
docker run \
    -v <local_directory>/mprage.nii:/input/mprage.nii \
    -v $(pwd):/opt/freesurfer/subjects \
    -v /home/michele/Projects/rmi-pipelines/license.txt:/opt/freesurfer/.license \
        freesurfer/freesurfer:6.0 \
            recon-all -i /input/mprage.nii -s <subject> -all
```

When recon-all is terminated, the current directory will have the directory <subject> with the result of recon_all

## Copy images

Suppose you want to copy a Docker image to another host without using DockerHub:

```
docker save <image> | bzip2 | pv | ssh -p <port> <username>@<host> 'bunzip2 | docker load'
```

You can omit `-p <port>` if port is standard 22
