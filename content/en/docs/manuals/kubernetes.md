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


## Recon All with a Pod

```
apiVersion: v1
kind: Pod
metadata:
  name: recon-all
spec:
  containers:
  - name: recon-all
    image: freesurfer/freesurfer:6.0
    command: ["recon-all", "-i", "/data/anat.nii.gz", "-s", "subject", "-all"]
    volumeMounts:
    - name: license
      mountPath: /opt/freesurfer/.license
    - name: anat-file 
      mountPath: /data/anat.nii.gz
    - name: subject
      mountPath: /opt/freesurfer/subjects
  restartPolicy: Never
  volumes:
  - name: license
    hostPath:
      path: /usr/local/freesurfer/.license
  - name: subject
    hostPath:
      path: /home/mastrogiovanni/test 
  - name: anat-file
    hostPath:
      path: /data/MASTROGIOVANNI/autismo/controllo/28698/session_1/anat_1/anat.nii.gz
```