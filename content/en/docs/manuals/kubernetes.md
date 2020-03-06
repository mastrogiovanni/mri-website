---
title: "Kubernetes"
description: "Kubernetes"
date: 2020-02-26T14:40:20+01:00
draft: false
weight: 0
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

Run a simple container:

```
microk8s.kubectl run hello-world --replicas=1 --labels="experiment=my-experiment" --image="hello-world"
```

Write a Pod descriptor for your computation:

```pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["/bin/bash", "-c", "/bin/ls", "/data/redis"]
    volumeMounts:
    - name: redis-storage
      mountPath: /data/redis
  restartPolicy: Never
  volumes:
  - name: redis-storage
    emptyDir: {}
```

Execute it on the cluster:

```
microk8s.kubectl apply -f pod.yml
```
