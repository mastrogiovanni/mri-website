---
title: "Overlay"
description: "Overlay"
date: 2020-02-21T19:09:25+01:00
draft: false
weight: 30
---

## RMN

Disk is located in 172.16.30.158:

```
postprocessing-datasets/dataset/type/subject-1
                                     ...
                                     subject-n
```

e.g. *dataset* can be: 

For example:

Content Here

vi mounts.sh

mount --bind /data/RMN/MASTROGIOVANNI/fsaverage /data/RMN/MASTROGIOVANNI/autismo/fsaverage
# mount -o remount,ro /data/RMN/MASTROGIOVANNI/autismo/fsaverage

Mount a directory from RMN in a local sharing:

sudo mount -t cifs //172.16.30.158/Dati-RMN/postprocessing-datasets $(pwd)/mounted -o username=guest.siemens-neuro,password=RDXXGUsgk542,domain=domain,ro
