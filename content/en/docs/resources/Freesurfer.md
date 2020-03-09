---
title: "Freesurfer"
description: "Freesurfer"
date: 2020-02-27T15:11:58+01:00
draft: false
weight: 0
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

# Prepare data for FreeSurfer

Assume you have a file `anat.nii.gz` and you want to perform recon-all on it.

```
SUBJECT_ID=tizio
INPUT_FILE=<the-path-of-anat-file>/anat.nii.gz
```

Setup directory:

```
mkdir -p $SUBJECT_ID/mri/orig
cp $INPUT_FILE $SUBJECT_ID/mri/orig/
```

Creation of mgz

```
docker run \
	--rm \
	-v $(pwd)/$SUBJECT_ID:/opt/freesurfer/subjects/$SUBJECT_ID \
	-v /usr/local/freesurfer/.license:/opt/freesurfer/.license \
		freesurfer/freesurfer:6.0 \
			mri_convert \
                /opt/freesurfer/subjects/$SUBJECT_ID/mri/orig/anat.nii.gz \
                /opt/freesurfer/subjects/$SUBJECT_ID/mri/orig/001.mgz
```

# recon-all: Docker version

From the machine with the subject data run:

```
docker run \
	--rm \
	-v $(pwd)/$SUBJECT_ID:/opt/freesurfer/subjects/$SUBJECT_ID \
	-v /usr/local/freesurfer/.license:/opt/freesurfer/.license \
		freesurfer/freesurfer:6.0 \
			recon-all -s $SUBJECT_ID -all
```

# recon-all: Kubernetes version

Create a file `recon-all-pod.yml` with the following content:

```recon-all-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: recon-all
spec:
  containers:
  - name: recon-all
    image: freesurfer/freesurfer:6.0
    command: ["recon-all", "-openmp", "4", "-s", "tizio", "-all"]
    volumeMounts:
    - name: license
      mountPath: /opt/freesurfer/.license
    - name: subject
      mountPath: /opt/freesurfer/subjects/tizio
  restartPolicy: Never
  volumes:
  - name: license
    hostPath:
      path: /usr/local/freesurfer/.license
  - name: subject
    hostPath:
      path: "$(pwd)/$SUBJECT_ID"
```

Exec the computation on Kubernetes cluster:

```
microk8s.kubectl create -f recon-all-pod.yml
```
