---
title: "Git"
description: "Git"
date: 2020-02-26T13:45:10+01:00
draft: false
weight: 1
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

### Download a repository

When you need to download a project to start working on it:

```
git clone https://github.com/mri-group-opbg/<repository>
```

### Save locally your changes

You can see what's changed in your code with:

```
git status
```

Than you need to add files to the local index:

```
git add file1 file2 ...
```

or if you want to add all files

```
git add *
```

Than you can save this change set to your local computer with:

```
git commit -m "Message that describe what you did"
```

Finally if you want that all your commit are saved on the remote repository:

```
git push
```

### 

