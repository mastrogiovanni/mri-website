---
title: "Scripting Resources"
description: "Script examples"
date: 2020-02-20T00:34:41+09:00
draft: false
weight: 10
---

In this page we report some useful examples and references to solve problems using bash scripts.

# Reduce number of concurrent Jobs

Sometime you want to execute N concurrent bash scripts but you don't want to overload the CPU.
You prefere to control the execution such that anytime no more that K bash scripts are running 
together.

In order to solve this problem you need to create a script like the following `exec-async.sh`:

```bash
joblist=($(jobs -p))
while (( ${#joblist[*]} >= K ))
do
    sleep 1
    joblist=($(jobs -p))
done
$* &
```

In the previous script you need to change `K` in the number of maximum job allowed.

Now you can use in your script in the following way:

```bash
for i in 0 1 2 ... N
do
    . exec-async.sh ./<script-to-launch>.sh <param-1> ... <param-n>
done
```

The script `exec-async.sh` will schedule all `N` jobs in background but any of them will 
throttle for a second if the number of other jobs is greater than `K`.

Reference [here](https://stackoverflow.com/questions/1537956/bash-limit-the-number-of-concurrent-jobs).
