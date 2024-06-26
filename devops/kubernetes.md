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

# kubectl help | head
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/

Basic Commands (Beginner):
  create          Create a resource from a file or from stdin
  expose          Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run             Run a particular image on the cluster
  set             Set specific features on objects

# kubectl get nodes
NAME   STATUS   ROLES                  AGE    VERSION
k8s    Ready    control-plane,master   3d2h   v1.29.4+k3s1

# kubectl get nodes -o wide
NAME   STATUS   ROLES                  AGE    VERSION        INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
k8s    Ready    control-plane,master   3d2h   v1.29.4+k3s1   157.90.144.2   <none>        Ubuntu 22.04.4 LTS   5.15.0-105-generic   containerd://1.7.15-k3s1

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

#### What is a Pod? 

The shared context of a Pod is a set of Linux namespaces, cgroups, and potentially other facets of isolation - the same things that isolate a container. Within a Pod's context, the individual applications may have further sub-isolations applied.

A Pod is similar to a set of containers with shared namespaces and shared filesystem volumes.

Pods in a Kubernetes cluster are used in two main ways:

- Pods that run a single container. The "one-container-per-Pod" model is the most common Kubernetes use case; in this case, you can think of a Pod as a wrapper around a single container; Kubernetes manages Pods rather than managing the containers directly.

- Pods that run multiple containers that need to work together. A Pod can encapsulate an application composed of multiple co-located containers that are tightly coupled and need to share resources. These co-located containers form a single cohesive unit. Grouping multiple co-located and co-managed containers in a single Pod is a relatively advanced use case. You should use this pattern only in specific instances in which your containers are tightly coupled. You don't need to run multiple containers to provide replication (for resilience or capacity); if you need multiple replicas, see Workload management.

[Kubernetes.io - Pods](https://kubernetes.io/docs/concepts/workloads/pods/)

### Pod Example

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

### Pod Commands

```bash
# kubectl run nginx --image=nginx
pod/nginx created

# kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          7s
  
# kubectl apply -f pod.yaml
pod/myapp-pod created

# # kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          4s

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

## ReplicaSets

A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods.

#### How a ReplicaSet works 

A ReplicaSet is defined with fields, including a selector that specifies how to identify Pods it can acquire, a number of replicas indicating how many Pods it should be maintaining, and a pod template specifying the data of new Pods it should create to meet the number of replicas criteria. A ReplicaSet then fulfills its purpose by creating and deleting Pods as needed to reach the desired number. When a ReplicaSet needs to create new Pods, it uses its Pod template.

A ReplicaSet is linked to its Pods via the Pods' metadata.ownerReferences field, which specifies what resource the current object is owned by. All Pods acquired by a ReplicaSet have their owning ReplicaSet's identifying information within their ownerReferences field. It's through this link that the ReplicaSet knows of the state of the Pods it is maintaining and plans accordingly.

A ReplicaSet identifies new Pods to acquire by using its selector. If there is a Pod that has no OwnerReference or the OwnerReference is not a Controller and it matches a ReplicaSet's selector, it will be immediately acquired by said ReplicaSet.

#### When to use a ReplicaSet

A ReplicaSet ensures that a specified number of pod replicas are running at any given time. However, a Deployment is a higher-level concept that manages ReplicaSets and provides declarative updates to Pods along with a lot of other useful features. Therefore, we recommend using Deployments instead of directly using ReplicaSets, unless you require custom update orchestration or don't require updates at all.

This actually means that you may never need to manipulate ReplicaSet objects: use a Deployment instead, and define your application in the spec section.

[Kubernetes.io - ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

### ReplicaSet Example

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      type: frontend
  template:
    metadata:
      name: myapp-replicas
      labels:
        app: myapp
        type: frontend
    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
```

### ReplicaSet Commands

```bash
# kubectl create -f replicaset.yaml
replicaset.apps/myapp-replicaset created

# kubectl get pods -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP           NODE   NOMINATED NODE   READINESS GATES
myapp-replicaset-fqtxn   1/1     Running   0          31s   10.42.0.91   k8s    <none>           <none>
myapp-replicaset-z566r   1/1     Running   0          31s   10.42.0.93   k8s    <none>           <none>
myapp-replicaset-kbv9l   1/1     Running   0          31s   10.42.0.92   k8s    <none>           <none>

# kubectl get replicasets
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   3         3         3       56s

# kubectl get rs
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   3         3         3       59s

# kubectl describe rs/myapp-replicaset
Name:         myapp-replicaset
Namespace:    default
Selector:     type=frontend
Labels:       app=myapp
              type=frontend
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=myapp
           type=frontend
  Containers:
   nginx-container:
    Image:        nginx:latest
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  5m56s  replicaset-controller  Created pod: myapp-replicaset-fqtxn
  Normal  SuccessfulCreate  5m56s  replicaset-controller  Created pod: myapp-replicaset-kbv9l
  Normal  SuccessfulCreate  5m56s  replicaset-controller  Created pod: myapp-replicaset-z566r

# kubectl get pods myapp-replicaset-kbv9l -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2024-05-21T11:24:51Z"
  generateName: myapp-replicaset-
  labels:
    app: myapp
    type: frontend
  name: myapp-replicaset-kbv9l
  namespace: default
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: myapp-replicaset
    uid: d401b705-74e2-4f4b-a84c-f16956aff1c3
  resourceVersion: "109572"
  uid: 7108b120-c428-4af7-a5ef-67afd49ddb06
spec:
  containers:
  - image: nginx:latest
    imagePullPolicy: Always
    name: nginx-container
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-64w99
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: k8s
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-64w99
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2024-05-21T11:24:53Z"
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2024-05-21T11:24:51Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2024-05-21T11:24:53Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2024-05-21T11:24:53Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2024-05-21T11:24:51Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://f54298a7f82ef810cde637c0c80bc3a14029b18ae0aae50c756b09301c407b6e
    image: docker.io/library/nginx:latest
    imageID: docker.io/library/nginx@sha256:a484819eb60211f5299034ac80f6a681b06f89e65866ce91f356ed7c72af059c
    lastState: {}
    name: nginx-container
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2024-05-21T11:24:53Z"
  hostIP: 157.90.144.2
  hostIPs:
  - ip: 157.90.144.2
  phase: Running
  podIP: 10.42.0.92
  podIPs:
  - ip: 10.42.0.92
  qosClass: BestEffort
  startTime: "2024-05-21T11:24:51Z"
```

#### Scaling a ReplicaSet

A ReplicaSet can be easily scaled up or down by simply updating the .spec.replicas field. The ReplicaSet controller ensures that a desired number of Pods with a matching label selector are available and operational.

When scaling down, the ReplicaSet controller chooses which pods to delete by sorting the available pods to prioritize scaling down pods based on the following general algorithm:

1. Pending (and unschedulable) pods are scaled down first
2. If `controller.kubernetes.io/pod-deletion-cost` annotation is set, then the pod with the lower value will come first.
3. Pods on nodes with more replicas come before pods on nodes with fewer replicas.
4. If the pods' creation times differ, the pod that was created more recently comes before the older pod (the creation times are bucketed on an integer log scale when the `LogarithmicScaleDown` feature gate is enabled)

If all of the above match, then selection is random.

### Scaling Example

```yaml
...
spec:
  replicas: 8
...
```

### Scaling Commands

```bash
# kubectl replace -f replicaset.yaml
replicaset.apps/myapp-replicaset replaced

# kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-replicaset-fqtxn   1/1     Running   0          15m
myapp-replicaset-z566r   1/1     Running   0          15m
myapp-replicaset-kbv9l   1/1     Running   0          15m
myapp-replicaset-cnh5v   1/1     Running   0          3s
myapp-replicaset-jzmk2   1/1     Running   0          3s
myapp-replicaset-5l5kj   1/1     Running   0          3s
myapp-replicaset-qfjzp   1/1     Running   0          3s
myapp-replicaset-2ww4g   1/1     Running   0          3s

# kubectl scale --replicas=5 replicaset myapp-replicaset
replicaset.apps/myapp-replicaset scaled

# kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-replicaset-fqtxn   1/1     Running   0          17m
myapp-replicaset-z566r   1/1     Running   0          17m
myapp-replicaset-kbv9l   1/1     Running   0          17m
myapp-replicaset-cnh5v   1/1     Running   0          92s
myapp-replicaset-5l5kj   1/1     Running   0          92s

# kubectl delete replicasets myapp-replicaset
replicaset.apps "myapp-replicaset" deleted

# kubectl get pods
No resources found in default namespace.
```

## Deployments

A Deployment provides declarative updates for Pods and ReplicaSets.

You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate. You can define Deployments to create new ReplicaSets, or to remove existing Deployments and adopt all their resources with new Deployments.

#### Use Case 

The following are typical use cases for Deployments:

- Create a Deployment to rollout a ReplicaSet. The ReplicaSet creates Pods in the background. Check the status of the rollout to see if it succeeds or not.
- Declare the new state of the Pods by updating the PodTemplateSpec of the Deployment. A new ReplicaSet is created and the Deployment manages moving the Pods from the old ReplicaSet to the new one at a controlled rate. Each new ReplicaSet updates the revision of the Deployment.
- Rollback to an earlier Deployment revision if the current state of the Deployment is not stable. Each rollback updates the revision of the Deployment.
- Scale up the Deployment to facilitate more load.
- Pause the rollout of a Deployment to apply multiple fixes to its PodTemplateSpec and then resume it to start a new rollout.
- Use the status of the Deployment as an indicator that a rollout has stuck.
- Clean up older ReplicaSets that you don't need anymore.

[Kubernetes.io - Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

### Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      type: frontend
  template:
    metadata:
      name: myapp-deployment
      labels:
        app: myapp
        type: frontend
    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
```

### Deployment Commands

```bash
# kubectl create -f deployment.yaml
deployment.apps/myapp-deployment created

# kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
myapp-deployment-69c58b88c6-bn7mm   1/1     Running   0          13s
myapp-deployment-69c58b88c6-5q6st   1/1     Running   0          13s

# kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
myapp-deployment   2/2     2            2           38s

# kubectl get replicasets
NAME                          DESIRED   CURRENT   READY   AGE
myapp-deployment-69c58b88c6   2         2         2       66s

# kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/myapp-deployment-69c58b88c6-bn7mm   1/1     Running   0          2m1s
pod/myapp-deployment-69c58b88c6-5q6st   1/1     Running   0          2m1s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   7d4h

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/myapp-deployment   2/2     2            2           2m1s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/myapp-deployment-69c58b88c6   2         2         2       2m1s

# kubectl exec -it pod/myapp-deployment-69c58b88c6-5q6st -- nginx -v
nginx version: nginx/1.25.5
```

#### Upgrade an application

1. Update yaml file with new configuration
```yaml
...
image: nginx:1.26.0
...
```

2. Apply new changes

```bash
# kubectl apply -f deployment.yaml
deployment.apps/myapp-deployment configured
```

```bash
# kubectl set image deployment/myapp-deployment nginx-container=nginx:1.24.0
deployment.apps/myapp-deployment image updated
```

## Service

In Kubernetes, a Service is a method for exposing a network application that is running as one or more Pods in your cluster.

A key aim of Services in Kubernetes is that you don't need to modify your existing application to use an unfamiliar service discovery mechanism. You can run code in Pods, whether this is a code designed for a cloud-native world, or an older app you've containerized. You use a Service to make that set of Pods available on the network so that clients can interact with it.

If you use a Deployment to run your app, that Deployment can create and destroy Pods dynamically. From one moment to the next, you don't know how many of those Pods are working and healthy; you might not even know what those healthy Pods are named. Kubernetes Pods are created and destroyed to match the desired state of your cluster. Pods are ephemeral resources (you should not expect that an individual Pod is reliable and durable).

Each Pod gets its own IP address (Kubernetes expects network plugins to ensure this). For a given Deployment in your cluster, the set of Pods running in one moment in time could be different from the set of Pods running that application a moment later.

[Kubernetes.io - Service](https://kubernetes.io/docs/concepts/services-networking/service/)

### Service Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-db
spec:
  type: ClusterIP
  ports:
    - targetPort: 6379
      port: 6379
  selector:
    app: myapp
    name: redis-db
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: my-app
    name: front-end
```

### Service Commands

```bash
# kubectl apply -f service.yaml
service/redis-db created

# kubectl get services
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.43.0.1     <none>        443/TCP    7d13h
redis-db     ClusterIP   10.43.16.42   <none>        6379/TCP   9s

# kubectl apply -f service.yaml
service/web-service created

# kubectl get services
NAME          TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
kubernetes    ClusterIP   10.43.0.1     <none>        443/TCP        7d14h
redis-db      ClusterIP   10.43.16.42   <none>        6379/TCP       7m50s
web-service   NodePort    10.43.38.70   <none>        80:30008/TCP   5s
```

## DeamonSet

A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.

Some typical uses of a DaemonSet are:

- running a cluster storage daemon on every node
- running a logs collection daemon on every node
- running a node monitoring daemon on every node

In a simple case, one DaemonSet, covering all nodes, would be used for each type of daemon. A more complex setup might use multiple DaemonSets for a single type of daemon, but with different flags and/or different memory and cpu requests for different hardware types.

[Kubernetes.io - DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

### DaemonSet Example

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata: 
  name: node-exporter
  namespace: promethues
spec:
  selector:
    matchLabels:
      name: node-exporter
      part: monitoring
  template:
    metadata:
      labels:
        name: node-exporter
        part: monitoring
    spec:
      containers:
        - name: node-exporter
          image: prom/node-exporter:latest
```

### DaemonSet Commands

```bash
# kubectl create ns promethues
namespace/promethues created

# kubectl apply -f ds.yaml
daemonset.apps/node-exporter created

# kubectl get pods -n promethues
NAME                  READY   STATUS    RESTARTS   AGE
node-exporter-pvkm7   1/1     Running   0          59s

# kubectl get daemonsets -n prometheus
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
node-exporter   1         1         1       1            1           <none>          6d19h
```

## StatefulSets

StatefulSet is the workload API object used to manage stateful applications.

Manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods.

Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of its Pods. These pods are created from the same spec, but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.

If you want to use storage volumes to provide persistence for your workload, you can use a StatefulSet as part of the solution. Although individual Pods in a StatefulSet are susceptible to failure, the persistent Pod identifiers make it easier to match existing volumes to the new Pods that replace any that have failed.

[Kubernetes.io - StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

### StatefulSet Example

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis
  labels:
    name: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      name: redis
  template:
    metadata:
      labels:
        name: redis
    spec:
      containers:
        - name: redis
          image: redis:latest
```

#### StatefulSet Commands

```bash
# kubectl apply -f sts.yaml
statefulset.apps/redis created

# kubectl get statefulsets
NAME    READY   AGE
redis   3/3     14s

# kubectl get sts
NAME    READY   AGE
redis   3/3     55s

# kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
redis-0   1/1     Running   0          67s
redis-1   1/1     Running   0          63s
redis-2   1/1     Running   0          61s

# kubectl delete sts redis
statefulset.apps "redis" deleted
```

### StatefulSet Example with Volumes

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: redis
  labels:
    name: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      name: redis
  template:
    metadata:
      labels:
        name: redis
    spec:
      containers:
        - name: redis
          image: redis:latest
          volumeMounts:
            - name: redis-data
              mountPath: /var/lib/redis
  volumeClaimTemplates:
    - metadata:
        name: redis-data
      spec:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 1Gi
```

```bash
# kubectl apply -f sts.yaml
statefulset.apps/redis created

# kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
redis-0   1/1     Running   0          41s
redis-1   1/1     Running   0          33s
redis-2   1/1     Running   0          27s

# kubectl get pvc
NAME                 STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
redis-data-redis-0   Bound    pvc-053e82b4-8965-43bd-9953-bd22231294a7   1Gi        RWO            local-path     <unset>                 89s
redis-data-redis-1   Bound    pvc-4b0ffa32-5fc2-415f-a812-281272722875   1Gi        RWO            local-path     <unset>                 81s
redis-data-redis-2   Bound    pvc-463e9ac4-64fa-474f-b633-f87e7c36c90c   1Gi        RWO            local-path     <unset>                 75s

# kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                        STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pvc-053e82b4-8965-43bd-9953-bd22231294a7   1Gi        RWO            Delete           Bound    default/redis-data-redis-0   local-path     <unset>                          2m14s
pvc-4b0ffa32-5fc2-415f-a812-281272722875   1Gi        RWO            Delete           Bound    default/redis-data-redis-1   local-path     <unset>                          2m8s
pvc-463e9ac4-64fa-474f-b633-f87e7c36c90c   1Gi        RWO            Delete           Bound    default/redis-data-redis-2   local-path     <unset>                          2m2s
```