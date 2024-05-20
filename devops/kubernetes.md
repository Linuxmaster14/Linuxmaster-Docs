# Kubernetes

Kubernetes is an open-source container orchestration system for automating software deployment, scaling, and management. Originally designed by Google, the project is now maintained by a worldwide community of contributors, and the trademark is held by the Cloud Native Computing Foundation. 

## Installation with k3s.io

```bash
curl -sfL https://get.k3s.io | sh -
```

## Basic Commands

```bash
# kubectl version
Client Version: v1.29.4+k3s1
Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
Server Version: v1.29.4+k3s1
```

```bash
# kubectl help | head
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/

Basic Commands (Beginner):
  create          Create a resource from a file or from stdin
  expose          Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run             Run a particular image on the cluster
  set             Set specific features on objects
```

```bash
# kubectl get nodes
NAME   STATUS   ROLES                  AGE    VERSION
k8s    Ready    control-plane,master   3d2h   v1.29.4+k3s1
# kubectl get nodes -o wide
NAME   STATUS   ROLES                  AGE    VERSION        INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
k8s    Ready    control-plane,master   3d2h   v1.29.4+k3s1   157.90.144.2   <none>        Ubuntu 22.04.4 LTS   5.15.0-105-generic   containerd://1.7.15-k3s1
```

```bash

```

```bash

```

```bash

```

```bash

```