
To install:


kubectl create namespace argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/v2.4.3/manifests/install.yaml



To see the gui

kubectl -n argo port-forward deployment/argo-ui 8001:8001


Clean all jobs:

```
argo delete $(argo list | awk '{ print $1 }' | tail -n +2)
```
