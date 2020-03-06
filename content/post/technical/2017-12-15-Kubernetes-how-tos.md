---
title:  "Kubernetes How-Tos"
date:   2017-12-15 15:04:23
categories: [technical]
tags: [Microservices, Containers, Kubernetes]
---

Kubernetes has been taking over the container orchesteration stage for sometime now, it has a large community and a great set of tooling and contributers backing it. Along those lines, picking up kubernetes skills can be a little daunting at first, but fear not, as documentation is rich and concepts are well elaborated in not one but many places.

In this post, I won't attempt to re-explain kubernetes concepts (pods, deployments, services, replicasets, ...etc) since as I said, a lot of great people did that job already, and did it very well (if you still have doubts about some of the concepts, I would be glad to help clear smoke up). Instead, I will be adding a set of How-Tos for common tasks that you might encounter when working with kubernetes, the post might be updated every now and then to include more. Shall we begin?!

**N.B:** The How-Tos take the form of Q&A.

### How to access K8s API from the command line

```bash
# First Get the Token and the API server address
APISERVER=$(kubectl config view | grep server | cut -f 2- -d ":" | tr -d " ")
TOKEN=$(kubectl describe secret $(kubectl get secrets | grep default | cut -f1 -d ' ') | grep -E '^token' | cut -f2 -d':' | tr -d '\t')

# Now use the token and the address to call the API
curl $APISERVER/api --header "Authorization: Bearer $TOKEN" --insecure
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "10.0.1.149:443"
    }
  ]
}
```

### How to get access to the dashboard

- First  get the token.
  ```bash
  APISERVER=$(kubectl config view | grep server | cut -f 2- -d ":" | tr -d " ")
  TOKEN=$(kubectl describe secret $(kubectl get secrets | grep default | cut -f1 -d ' ') | grep -E '^token' | cut -f2 -d':' | tr -d '\t')
  ```
- Then create a secure channel to your kubernetes cluster using `kubectl proxy`
- Finally, visit http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/  and use the token as an input when prompted.

For more Info check the documentation and the repo [here](https://github.com/kubernetes/dashboard)

If you want to access the dashboard from outside, you need to modify the service with a `NodePort`

```bash
kubectl -n kube-system edit service kubernetes-dashboard
```

if you want to get access without a token or kube-config for the dashboard, apply the following [yaml](https://gist.githubusercontent.com/jpetazzo/fb0fd5c5233486e5b17c194b304a8905/raw/0dca3976cffa0c9f46c9a88549cdf9efd9cbf02e/k8s-admin-dashboard.yaml) to give the cluster-admin role:

```yml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```

### How to do health-checks with kubernetes

There are two options to do health checks:

- **Liveness Probes:** Many applications running for long periods of time eventually transition to broken states, and cannot recover except by being restarted. Kubernetes provides liveness probes to detect and remedy such situations.
- **Readiness Probes:** it’s used to check the start-up phase of a container in the pod. Imagine a container that loads some data from external storage such as S3 or a database that needs to initialize some tables. In this case you want to signal when the container is ready to serve traffic.

For more information check [documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) 

### What kind of selectors can be applied

|  Operator |  Description |
|---|---|
|key=value|key is set to value|
|key!=value|key is not set to value|
|key in (value1, value2)| key is either value1 or value2|
|key notin (value1, value2)|key is not one of value1 or value2|
|key|key is set|
|!key|key is not set|

### How to delete replicationsets without deleting pods

This can be achieved by setting the `--cascade` option to false at the end of the delete command, example:

```bash
kubectl delete rs rs_name --cascade=false
```

### How to list pod names only

```bash
kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'
```

OR

```bash
kubectl get pods -o jsonpath --template '{.items[*].metadata.name}'
```

if you want to delete all pods you can do it in a loop:

```bash
for pod in $(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}'); do kubectl delete pod $pod; done
```

OR 

```bash
for pod in $(kubectl get pods -o jsonpath --template '{.items[*].metadata.name}'); do kubectl delete pod $pod; sleep 60; done
```
