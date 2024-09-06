---
layout: post
title: Kubernetes Cheatshet
datetime: 2024-08-17 18:32:00
categories: devops, cheatsheet
---

## Basic commands

This is a list of the basic command operations you are able to do with Kubernetes.

- `kubectl get pods` - List all pods in the current namespace
- `kubectl get pods -n <namespace>` - List all pods in a specific namespace
- `kubectl get pods -o wide` - List all pods in the current namespace with more information
- `kubectl describe pod <pod-name>` - Get detailed information about a pod
- `kubectl run <pod-name> --image=<image-name>` - Create a pod
- `kubectl delete pod <pod-name>` - Delete a pod
- `kubectl edit pod/<pod-name> -o yaml` - Edit a pod in yaml format

Kubernetes work basically by creating config files with the resources you need.
In first place, we are going to see how to create a Pod.

## Pod definition

The pod is the tinyest unit in Kubernetes. It is a group of containers
that are deployed together in the same host.
They are used to deploy applications and manage their lifecycle.

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
      - name: busybox
        image: busybox
```

## Replica Sets

A replica set is a group of identical pods that are running at the same time.
It is used to ensure that a specified number of pod replicas
are running at any given time.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-rc
  labels:
    app: myapp
    tier: frontend
spec:
  replicas: 3
  selector: # select the pods that are going to be managed by this replica set
    matchLabels:
      tier: frontend
  template: # define the pod template
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx
```

It also exists the Replication Controller,
which is the older version of the Replica Set.

Some useful commands:

- `kubectl get rs` - List all replica sets
- `kubectl create -f <file>` - Create a replica set with the definition file
- `kubectl scale --replicas=6 -f <file>` - Scale the number of replicas in a replica set
- `kubectl delete rs <rs-name>` - Delete a replica set
- `kubectl replace -f <file>` - Replace a replica set with the definition file

## Deployments

A deployment is a higher level concept that manages Replica Sets.
It is used to define the desired state of the application.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    tier: frontend
spec: # this is like the replica set spec
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      tier: frontend
  template:
    metadata:
      labels:
        app: myapp
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx
```

## Services

Enables communication between different parts of the application.
It is a stable endpoint to access the application.
There are three types of services:

1. NodePort
2. ClusterIP
3. LoadBalancer

### NodePort

Exposes the service on each node's IP at a static port.
It is useful for development and testing purposes.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector: # use the labels to select the pods that are going to be part of the service
    app: myapp
    tier: frontend
```

### ClusterIP

It's the default service type. It's used to expose the service inside the cluster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: ClusterIP
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: myapp
    tier: frontend
```

### LoadBalancer

It's only supported in cloud providers.
It creates a load balancer that will distribute the traffic to the pods.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  ports:
    - targetPort: 80
      port: 80
  selector:
    app: myapp
    tier: frontend
```

## Namespaces

Namespaces are used to organize the resources in the cluster.
They are used to divide the cluster resources between multiple users.

Some useful commands:

- `kubectl get ns` - List all namespaces
- `kubectl create ns <namespace>` - Create a namespace
- `kubectl get <resource> -n <namespace>` - List all pods in a specific namespace

You can also define the namespace in the resource definition file.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mynamespace
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: mynamespace
  labels:
    app: nginx
    tier: frontend
  spec:
    containers:
      - name: nginx
        image: nginx
```

## Resource Quotas

Resource quotas are used to limit the amount of resources that can be used in a namespace.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: myquota
  namespace: mynamespace
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 4Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

## Taints and Tolerations

Taints are used to repel pods from nodes.
Tolerations are used to allow pods to be scheduled on nodes with taints.

```bash
kubectl taint nodes node1 key=value:NoSchedule
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
  tolerations:
    - key: "key"
      operator: "Equal"
      value: "value"
      effect: "NoSchedule"
```

## Node Selector

Node selectors are used to select the nodes where the pods are going to be scheduled.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    disktype: ssd
    size: large
  containers:
    - name: nginx
      image: nginx
```

## Node Affinity

Node affinity is used to schedule pods based on the labels of the nodes.
There are three types of node affinity:

1. RequiredDuringSchedulingIgnoredDuringExecution
2. PreferredDuringSchedulingIgnoredDuringExecution
3. RequiredDuringSchedulingRequiredDuringExecution

The two first types are used to schedule the pods based on
the labels of the nodes during the scheduling phase.

The third type is used to schedule the pods based on the labels
of the nodes during the scheduling and execution phases, meaning
that the pods are going to be rescheduled if the node labels change.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: disktype
                operator: In
                values:
                  - ssd
                  - hdd
  containers:
    - name: nginx
      image: nginx
```

## Resource requirements

Resource requirements are used to specify the amount of resources that the pod needs.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
```

## Limit Ranges

Limit ranges are used to specify the minimum and maximum amount of resources that the pod can use.

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mylimitrange
  namespace: mynamespace
spec:
  limits:
    - type: Container
      default:
        memory: 512Mi
        cpu: 500m
      defaultRequest:
        memory: 256Mi
        cpu: 250m
      min:
        memory: 128Mi
        cpu: 125m
      max:
        memory: 1Gi
        cpu: 1
```

## Daemon Sets

Daemon sets are used to run a copy of a pod on each node in the cluster.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: myapp-daemonset
  labels:
    app: myapp
    tier: frontend
spec:
  selector:
    matchLabels:
      app: myapp
      tier: frontend
  template:
    metadata:
      labels:
        app: myapp
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx
```
