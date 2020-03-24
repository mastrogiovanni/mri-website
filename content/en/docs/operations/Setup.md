---
title: "Setup"
description: "Setup"
date: 2020-02-26T15:44:35+01:00
draft: false
weight: 5
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

In this page you can find all the instruction to setup a new machine.
In order 

# Operating System



# Default Users




# Docker

In order to install

# Hosts


# Proxy

In the following the proxy setup for some common software:

## Bash

`vi ~/.bashrc`

```
export http_proxy=172.16.135.1:8080
export ftp_proxy=172.16.135.1:8080
export https_proxy=172.16.135.1:8080
```

## APT

`vi /etc/apt/apt.conf.d/proxy.conf`

```
Acquire::http::Proxy "http://172.16.135.1:8080/";
Acquire::https::Proxy "http://172.16.135.1:8080/";
```

## Snap

```
sudo snap set system proxy.http="http://172.16.135.1:8080"
sudo snap set system proxy.https="http://172.16.135.1:8080"
```

## Docker

`vi ~/.docker/config.json`

```
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://172.16.135.1:8080",
     "httpsProxy": "http://172.16.135.1:8080"
   }
 }
}
```



# GPU Card Drivers

# CUDA Drivers

Cuda Toolik is a popular 

# Docker with GPU support

From version 19, Docker support natively GPUs.
For all other situations check [here for NVIDIA](https://github.com/NVIDIA/nvidia-docker).

# VLC Server

In order to access to machine you can connect to VNC server
https://medium.com/hackernoon/installation-of-vnc-server-on-ubuntu-1cf035370bd3


