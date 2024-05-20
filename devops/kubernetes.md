# Kubernetes

Kubernetes is a portable, extensible, open source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available.

[Kubernetes.io - Overview](https://kubernetes.io/docs/concepts/overview/)

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
# kubectl api-resources | head
NAME                              SHORTNAMES   APIVERSION                        NAMESPACED   KIND
bindings                                       v1                                true         Binding
componentstatuses                 cs           v1                                false        ComponentStatus
configmaps                        cm           v1                                true         ConfigMap
endpoints                         ep           v1                                true         Endpoints
events                            ev           v1                                true         Event
limitranges                       limits       v1                                true         LimitRange
namespaces                        ns           v1                                false        Namespace
nodes                             no           v1                                false        Node
persistentvolumeclaims            pvc          v1                                true         PersistentVolumeClaim
```

### Kind Versions

| Kind       | Version |
| ---------- | ------- |
| Pod        | v1      |
| Service    | v1      |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

## Pods

Pods are the smallest deployable units of computing that you can create and manage in Kubernetes.

A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. A Pod's contents are always co-located and co-scheduled, and run in a shared context. A Pod models an application-specific "logical host": it contains one or more application containers which are relatively tightly coupled. In non-cloud contexts, applications executed on the same physical or virtual machine are analogous to cloud applications executed on the same logical host.

[Kubernetes.io - Pods](https://kubernetes.io/docs/concepts/workloads/pods/)

```bash
# kubectl run nginx --image=nginx
pod/nginx created

# kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          7s

# kubectl get pods -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP           NODE   NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          10s   10.42.0.88   k8s    <none>           <none>
```

pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
    tier: frontend
spec:
  containers:
    - name: nginx
      image: nginx
```

### Create/Apply a Pod

```bash
# kubectl apply -f pod.yaml
pod/myapp-pod created

# # kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          4s
```

### Describe a Pod

```bash
# kubectl describe pod nginx
Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             k8s/157.90.144.2
Start Time:       Mon, 20 May 2024 11:37:00 +0000
Labels:           app=nginx
                  tier=frontend
Annotations:      <none>
Status:           Running
IP:               10.42.0.90
IPs:
  IP:  10.42.0.90
Containers:
  nginx-controller:
    Container ID:   containerd://bdd2731ff6673fe732d0bf4a9f002db8ed624ee9c2e3c21834abd30b20792d61
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:a484819eb60211f5299034ac80f6a681b06f89e65866ce91f356ed7c72af059c
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 20 May 2024 11:37:02 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9rtkw (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       True
  ContainersReady             True
  PodScheduled                True
Volumes:
  kube-api-access-9rtkw:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  20s   default-scheduler  Successfully assigned default/nginx to k8s
  Normal  Pulling    20s   kubelet            Pulling image "nginx"
  Normal  Pulled     19s   kubelet            Successfully pulled image "nginx" in 767ms (767ms including waiting)
  Normal  Created    19s   kubelet            Created container nginx-controller
  Normal  Started    19s   kubelet            Started container nginx-controller
```