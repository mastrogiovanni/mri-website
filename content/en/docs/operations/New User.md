---
title: "New User"
date: 2020-03-06T22:17:00+01:00
draft: false
weight: 0
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

In order to create a new user you need:

Create a user with the home directory
```
sudo useradd -m <user>
```

Assign the shell `bash` to the user: 
```
sudo usermod --shell /bin/bash <user>
```

Make it optionally sudo user:
```
sudo usermod -aG sudo mastrogiovanni
```
  