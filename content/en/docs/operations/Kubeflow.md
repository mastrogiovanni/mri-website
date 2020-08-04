---
title: "Kubeflow"
date: 2020-03-25T23:31:01+01:00
draft: false
weight: 20
enableToc: true
tocLevels: ["h2", "h3", "h4"]
---

Kubeflow is a complex set of components running on top of a Kubernetes cluster that make possible to explore data via jupyther notebook and create complete pipelines for Machine Learning: from data preprocessing to training and serving.

In the following sections the steps followed to install kubeflow:

# K8s via Kubeadmin
sudo kubeadm init 

(you can add --pod-network-cidr=10.244.0.0/16)

# Kubectl informations
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Join command
kubeadm join 172.16.116.112:6443 --token jc7an8.162veo6fqii84jda \
    --discovery-token-ca-cert-hash sha256:72a739ebf23bb3e68c79cc281b279a5a89ad2608efc5eac7e665776e53d70cb3 

# Kuberneted GPU
https://docs.nvidia.com/datacenter/kubernetes/kubernetes-upstream/index.html

# Calico
 kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

# Taint from master
kubectl taint nodes --all node-role.kubernetes.io/master-

# Loca path provisioner (make storage class default)
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml

cat local-path-storage.yaml | sed 's|storageclass.kubernetes.io/is-default-class: "true"|storageclass.kubernetes.io/is-default-class: "false"|'


# Va modificato per mettere default storage class

kubeflow build apply
<link>


kubectl get no -o wide

export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')

Access minio/minio123

kubectl port-forward svc/minio-service -n kubeflow 9000:9000



## How to expose OPBG proxy in Kubeflow:

[Resource: Istio Docs](https://istio.io/docs/tasks/traffic-management/egress/http-proxy/)

```
apiVersion: networking.istio.io/v1alpha3
kind: ServiceEntry
metadata:
  name: proxy
spec:
  hosts:
  - mriproxy
  addresses:
  - 172.16.135.1/32
  ports:
  - number: 8080
    name: tcp
    protocol: TCP
  location: MESH_EXTERNAL
EOF
```

[Proxy Issue with Kubeflow](https://github.com/kubeflow/kfctl/issues/237)

# Add static users

[Documentation](https://www.kubeflow.org/docs/started/k8s/kfctl-istio-dex/#add-static-users-for-basic-auth)

```
# Download the dex config
kubectl get configmap dex -n auth -o jsonpath='{.data.config\.yaml}' > dex-config.yaml

# Edit the dex config with extra users.
# The password must be hashed with bcrypt with an at least 10 difficulty level.
# You can use an online tool like: https://passwordhashing.com/BCrypt

# After editing the config, update the ConfigMap
kubectl create configmap dex --from-file=config.yaml=dex-config.yaml -n auth --dry-run -oyaml | kubectl apply -f -

# Restart Dex to pick up the changes in the ConfigMap
kubectl rollout restart deployment dex -n auth
```

# Volumes

If you need to mount a volume to a notebook you need a persistent volume like this:

```

```


# References

[Tutorial](https://www.swiftdiaries.com/tech/kf-1-0/)

# Future

[Add LDAP](https://www.kubeflow.org/docs/started/k8s/kfctl-istio-dex/)


# Use proxy in notebook

```
pip install --user --proxy 172.16.135.1:8080 pandas
```


# Test GPU

```
import tensorflow as tf
with tf.device('/gpu:0'):
    a = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[2, 3], name='a')
    b = tf.constant([1.0, 2.0, 3.0, 4.0, 5.0, 6.0], shape=[3, 2], name='b')
    c = tf.matmul(a, b)

with tf.Session() as sess:
    print (sess.run(c))
```
