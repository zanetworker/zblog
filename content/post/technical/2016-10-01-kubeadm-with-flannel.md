---
title:  "Kubernetes Kubeadm with Flannel Up & Running"
date:   2016-10-01 15:04:23
categories: [technical]
tags: [Kubernetes, Microservices]
---


Okay, so I am going to try and make this brief and to the point for you to get going. You need a Kubernetes cluster working with Kubeadm? good you are in the right place.

First, lets get over the problems first. What you need to see at the end is the following:

```bash
kube-system   dummy-2088944543-1x359            1/1       Running   1          1d
kube-system   etcd-master                       1/1       Running   1          1d
kube-system   kube-apiserver-master             1/1       Running   0          1d
kube-system   kube-controller-manager-master    1/1       Running   1          1d
kube-system   kube-discovery-1769846148-1khb9   1/1       Running   1          1d
kube-system   kube-dns-2924299975-pg660         4/4       Running   4          1d
kube-system   kube-flannel-ds-25l4t             2/2       Running   2          1d
kube-system   kube-flannel-ds-9jtd5             2/2       Running   2          1d
kube-system   kube-flannel-ds-lvskl             2/2       Running   8          1d
kube-system   kube-proxy-l1hn5                  1/1       Running   1          1d
kube-system   kube-proxy-p1kbt                  1/1       Running   1          1d
kube-system   kube-proxy-x2b1m                  1/1       Running   1          1d
kube-system   kube-scheduler-master             1/1       Running   1          1d
```
*Not*  this ...

```bash
kube-system   dummy-2088944543-1x359            1/1       Running   1          1d
kube-system   etcd-master                       1/1       Running   1          1d
kube-system   kube-apiserver-master             1/1       Running   0          1d
kube-system   kube-controller-manager-master    1/1       Running   1          1d
kube-system   kube-discovery-1769846148-1khb9   1/1       Running   1          1d
kube-system   kube-dns-2924299975-pg660         4/4       CrashLoopBackOff   4          1d
kube-system   kube-flannel-ds-25l4t             2/2       Running   2          1d
kube-system   kube-flannel-ds-9jtd5             2/2       Running   2          1d
kube-system   kube-flannel-ds-lvskl             2/2       Running   8          1d
kube-system   kube-proxy-l1hn5                  1/1       CrashLoopBackOff   1          1d
kube-system   kube-proxy-p1kbt                  1/1       CrashLoopBackOff   1          1d
kube-system   kube-proxy-x2b1m                  1/1       CrashLoopBackOff   1          1d
kube-system   kube-scheduler-master             1/1       Running   1          1d
```


To get it right, follow the following steps:

- First initiate your cluster with kubeadm

```bash
kubeadm init --api-advertise-addresses=<_your_private_network_address> --pod-network-cidr=10.244.0.0/16
```
- Once your kubeadm is ready for joining running the following (to avoid getting the crashLoopBackOff message):

```bash
# Overwrite the exisiting address with the new advertised address
jq \
   '.spec.containers[0].command |= .+ ["--advertise-address=<_your_private_network_address>"]' \
   /etc/kubernetes/manifests/kube-apiserver.json > /tmp/kube-apiserver.json
mv /tmp/kube-apiserver.json /etc/kubernetes/manifests/kube-apiserver.json

# setup the correct proxy parameters
kubectl -n kube-system get ds -l 'component=kube-proxy' -o json \
  | jq '.items[0].spec.template.spec.containers[0].command |= .+ ["--proxy-mode=userspace"]' \
  |   kubectl apply -f - && kubectl -n kube-system delete pods -l 'component=kube-proxy'

```

- Now you can apply the networking configuration file using [Copy the content of this yaml file](https://github.com/coreos/flannel/blob/master/Documentation/kube-flannel.yml):

```bash
kubectl apply -f flannel_config.yaml
```
- Finally, join your nodes using the following command (you should have noted that from earlier):

```bash
kubeadm join --token=<token> <master_ip>
```

To test run the following command to deploy a pod.

```bash
kubectl run --image=weaveworks/hello-world hello
```
I hope this helps!
