---
title: "MDT"
date: 2020-03-09T19:59:15+01:00
draft: false
weight: 0
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

## Prerequisite

In order to execute MDT you need to have:
* a working GPU card,
* GPU card driver,
* CUDA Toolkit,
* Docker with GPU support

## First run

Check if the installation is ok with the following command:

```
docker run \
    --rm \
    --gpus all \
    mrigroupopbg/mri-mdt \
        mdt-list-devices
```

The following command takes files `bvals` and `bvecs` and produce in the directory the file `bvecs.prtcl`:

```
docker run \
    --rm \
    --gpus all \
    -v $(pwd):/app \
    -w /app \
    mrigroupopbg/mri-mdt \
        mdt-create-protocol bvecs bvals
```

The following command run MDT over `data.nii`, `bvecs.prtcl` and `nodif_brain_mask.nii` to produce the directory `subject` performing the `NODDI` fitting.

```
docker run \
    --rm \
    --gpus all \
    -v $(pwd):/apps \
    -w /apps mrigroupopbg/mri-mdt:latest \
        mdt-model-fit \
            --cl-device-ind 0 \
            -o subject \
            -n 1 \
            --double \
            --no-recalculate \
            NODDI \
            data.nii \
            bvecs.prtcl \
            nodif_brain_mask.nii
```

### NOTICE: how to solve the GPU stuck bug.

The previous task can stuck using some GPUs (e.g TITAN RTX). This is due to the computation of the hessian matrix that is performed compiling a kernel for the GPU: its code is not perfectly general purpose and can stuck the computation itself in that phase. In order to resolve this issue you need to get the configuration file mdt.conf inside container with the following:

```
docker create -ti --name dummy mrigroupopbg/mri-mdt:latest bash
docker cp dummy:/root/.mdt/1.2.2/mdt.conf mdt.conf
docker rm -f dummy
```

put to false the `uncertainties` field:

```
...
active_post_processing:
    optimization:
        uncertainties: False
...
```

than overwrite the container configuration adding `-v $(pwd)/mdt.conf:/.mdt/1.2.2/mdt.conf` like the following:

```
docker run \
    --rm \
    --gpus all \
    -v $(pwd)/mdt.conf:/root/.mdt/1.2.2/mdt.conf \
    -v $(pwd):/apps \
    -w /apps mrigroupopbg/mri-mdt:latest \
        mdt-model-fit \
            --cl-device-ind 0 \
            -o subject \
            -n 1 \
            --double \
            --no-recalculate \
            NODDI \
            data.nii \
            bvecs.prtcl \
            nodif_brain_mask.nii
```

## Run MDT in dev mode

You can play with docker container using the `master` version of MDT downloaded from github.
In order to work with it you need to download latest source code:

```
git clone https://github.com/robbert-harms/MDT.git
```

this create directory `MDT`.

Furthermore you need to execute commands "mounting" the source code in the Docker container's proper location:

```
-v $(pwd)/MDT/mdt:/usr/lib/python3/dist-packages/mdt
```

For example the previous command became:

```
docker run \
    --rm \
    --gpus all \
    -v $(pwd)/mdt.conf:/root/.mdt/1.2.2/mdt.conf \
    -v $(pwd)/MDT/mdt:/usr/lib/python3/dist-packages/mdt \
    -v $(pwd):/apps \
    -w /apps mrigroupopbg/mri-mdt:latest \
        mdt-model-fit \
            --cl-device-ind 0 \
            -o subject \
            -n 1 \
            --double \
            --no-recalculate \
            NODDI \
            data.nii \
            bvecs.prtcl \
            nodif_brain_mask.nii
```
