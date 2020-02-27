---
title: "Freesurfer"
date: 2020-02-27T15:11:58+01:00
draft: false
weight: 0
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

# Example on how to use FreeSurfer on Docker

Assume you have a file `anat.nii.gz` and you want to perform recon-all on it:

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

Recon all:

```
docker run \
	--rm \
	-v $(pwd)/$SUBJECT_ID:/opt/freesurfer/subjects/$SUBJECT_ID \
	-v /usr/local/freesurfer/.license:/opt/freesurfer/.license \
		freesurfer/freesurfer:6.0 \
			recon-all -s $SUBJECT_ID -all
```
