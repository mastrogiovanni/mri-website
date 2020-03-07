---
title: "Jump Box"
date: 2020-03-04T14:05:49+01:00
draft: false
weight: 10
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

Labs' hosts are hidden to the external world.

It is possible only to access them from the internal OPBG network via SSH of remote desktop.

From inside the labs's hosts is possible to access to the outside web sites via OPBG Proxy.

The proxy for http and https connections is:
```
172.16.135.1:8080
```

Neverthless is possible to access from external world via *Jump Box*.
A *Jump Box* is a particular machine exposed on internet to whom the internal servers
establish connections.

An external visitor could access to internal servers only accessing to the *Jump Box*.

The machine that must be accessed through the Jump Box must have the following service 
defined in the file `/etc/systemd/system/secure-tunnel@.service`

```
[Unit]
Description=Secure tunnel to Bulma
After=network.target

[Service]
EnvironmentFile=/etc/default/secure-tunnel@%i
ExecStart=/usr/bin/ssh -N -R 0.0.0.0:5558:127.0.0.1:22 %i 
RestartSec=5
Restart=always

[Install]
WantedBy=multi-user.target
```


