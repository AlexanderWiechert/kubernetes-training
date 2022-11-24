---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)

layout: cover
# persist drawings in exports and build
drawings:
  persist: true
---

# Section 1.2: Kubernetes Architecture

---

# Why Containers

<img class="absolute top-23 left-50" src="/chapters/1.2.kubernetes.architecture/containers.png" />

---

# Kubernetes Architecture

<img class="absolute top-30 left-20 w-200" src="/chapters/1.2.kubernetes.architecture/architecture.png" />

---

# Master Architecture

<img class="absolute top-20 left-30 w-200" src="/chapters/1.2.kubernetes.architecture/master.png" />

---

# ETCD

etcd is a distributed, reliable key-value store which Kubernetes uses for persistent storage of all of its REST API objects.

* **Access Control**: give only kube-apiserver read/write access to etcd. Access to etcd is equivalent to root in your cluster.

* **Data Reliability**: Either etcd needs to be run as a cluster (multiple machines each running etcd) or etcd’s data directory should be located on durable storage (e.g., GCE’s persistent disk). In either case, if high availability is required–as it might be in a production cluster–the data directory ought to be backed up periodically, to reduce downtime in case of corruption.

etcd is the only place that Kubernetes keeps state.

---

# Kubernetes API Server

The Kubernetes API server validates and configures data for the api objects which include pods, services, replicationcontrollers, and others.

The API Server services REST operations and provides the frontend to the cluster’s shared state through which all other components interact.

In a typical Kubernetes cluster, the API served on port 443.

* All the Cluster State

* No Logic is added In the API Server.

* It only exposes the API and stores the state in etcd.

* The Kubernetes CLI (`kubectl`) only talks with the API, not directly to other components (This makes the system declarative, as you declare your desired state and other components drive the cluster to the desired state)

---

# Kubernetes Controller Manager

The Kubernetes controller manager is a daemon that embeds the core control loops shipped with Kubernetes.

In applications of robotics and automation, a control loop is a non-terminating loop that regulates the state of the system.

In Kubernetes, a controller is a control loop that watches the shared state of the cluster through the apiserver and makes changes attempting to move the current state towards the desired state.

Examples of controllers that ship with Kubernetes today are:

* Deployment Controller
* Replication Controller
* Endpoints Controller
* Namespace Controller
* ServiceAccounts Controller

---

# Kubernetes Scheduler

The Kubernetes scheduler is a policy-rich, topology-aware, workload-specific function that significantly impacts availability, performance, and capacity.

The scheduler needs to take into account individual and collective resource requirements, quality of service requirements, hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference, deadlines, and so on.

Workload-specific requirements will be exposed through the API as necessary.

**Basically, it is responsible for Scheduling which Pods run where**.

---

# Node Architecture

<img class="absolute top-20 left-30 w-200" src="/chapters/1.2.kubernetes.architecture/node.png" />

---

# Kubernetes Kubelet

The kubelet is the primary “node agent” that runs on each node.

The kubelet works in terms of a PodSpec. A PodSpec is a YAML or JSON object that describes a pod.

The kubelet takes a set of PodSpecs that are provided through various mechanisms (primarily through the apiserver) and ensures that the containers described in those PodSpecs are running and healthy.

The kubelet doesn’t manage containers which were not created by Kubernetes.

**Basically, it is responsible for running the Pods assigned to the specific Node**.

---

# Kubernetes Proxy

The Kubernetes network proxy runs on each node.

This reflects services as defined in the Kubernetes API on each node and can do simple TCP,UDP stream forwarding or round robin TCP,UDP forwarding across a set of backends.

Service cluster ips and ports are currently found through Docker-links-compatible environment variables specifying ports opened by the service proxy.

There is an optional addon that provides cluster DNS for these cluster IPs.

The user must create a service with the apiserver API to configure the proxy.

**Basically, it is responsible from the Networking of the Node**.

---

# 1.2 Kubernetes Architecture

During this section we learned about the different components of Kubernetes architecture:

* Etcd
* Kubernetes API Server
* Kubernetes Controller Manager
* Kubernetes Scheduler
* Kubernetes Kubelet
* Kubernetes Proxy
