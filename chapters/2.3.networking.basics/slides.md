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

# 2.3. Networking Basics

<img class="absolute top-80 left-90 w-70" src="/chapters/2.3.networking.basics/networking.png" />

---

# Section 2.3: Networking Basics

As we mentioned before Pods are ephemeral and they can be born and die during the day.

In order to be able to group together a set of Pods and make them discoverable without having to keep in mind that they may die and be regenerated (e.g. because a node was faulty) we need something more abstract. This is where Services come in.

A Service is basically a group of Pods that consistitute a Service (e.g. a group of Nginx instances).

When someone wants to access this group of Pods it does it through the service, which will redirect the request to one of those Pods.

---

# Section 2.3: Networking Basics

In this section we'll deploy a Deployment with 3 nginx replicas (Pods) and a Service that connects them.

This is the most common way to deploy an stateless application.

* The Deployment is the same as before, below is the Service definition:

    ```shell
    $ cat kubernetes/06.nginx-service/nginx-service.yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      type: LoadBalancer
      ports:
        - name: web
          port: 80
      selector:
        app: nginx
    ```

---

# Section 2.3: Networking Basics

There are three types of Services:

* **ClusterIP**: Exposes the service on a cluster-internal IP. Choosing this value makes the service only reachable from within the cluster (default).
* **NodePort**: Exposes the service on each Node’s IP at a static port (the NodePort).
* **LoadBalancer**: Exposes the service externally using a cloud provider’s load balancer.
* **ExternalName**: Maps the service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value.

We used `LoadBalancer`, but since we're in minikube and don't have a cloud provider it is equivalent to `NodePort`.

---

# Section 2.3: Networking Basics

* Let's deploy it:

    ```shell
    $ kubectl create -f kubernetes/06.nginx-service/
    ```

* Get the Service:

    ```shell
    $ kubectl get service nginx
    ```

* Get the NodePort of the Service

    ```shell
    $ kubectl get service nginx -o jsonpath={.spec.ports[*].nodePort}
    ```

* Get the IP of minikube

    ```shell
    $ minikube ip
    ```

* Get the output of NODE_IP:NODE_PORT

    ```shell
    $ curl -sL $(minikube ip):$(kubectl get service nginx -o jsonpath={.spec.ports[*].nodePort})
    ```

---

# Section 2.3: Networking Basics

In Docker we discussed about launching multiple containers on one node

* Each container was assigned one IP
* Only services exposed could be seen to outside world

In Kubernetes we have more than one node

* we want two Pods that are in different machines to be able to talk together
* But we don't want to expose everything to the outside world

This is solved by the network model

---

# Section 2.3: Networking Basics

On Kubernetes there are two IP ranges:

* The Pods range
* The Services range

Each Pod is assigned one IP (IP-per-pod model) from the Pods range.

Each Pod can talk to other Pods directly with their IP (in same or different Nodes)

Each Service is assigned an IP on the Services range. That IP is doesn't change during the lifetime of the Pods attached to the Service

There are various implementations of the network model:

* Contiv
* Flannel
* GCE
* L2 networks and linux bridging
* Nuage Networks VCS
* Weave Net
* ...

---

# Section 2.3: Networking Basics

Let's create two Pods and talk to each other

* First create an `nginx` pod:

    ```shell
    $ kubectl create -f kubernetes/02.single-container-pod/
    ```

* Get the IP of the Pod

    ```shell
    $ kubectl get pod nginx --template={{.status.podIP}}
    ```

* Then create a temporal interactive Pod to ping the Pod above

    ```shell
    $ kubectl run --rm ping -it --image alpine:3.5 sh
    wget -qO- 172.17.0.5
    ```

* Now cleanup

    ```shell
    $ kubectl delete -f kubernetes/02.single-container-pod/
    ```

---

# Section 2.3: Networking Basics

But this way we need to **know** the other Pod's IP. What if the Pod dies and IP changes?

We need a way to discover other Services. There are two ways:

* **Environment Variables**: Each Pod when run it has environment variables for each Service that point to the virtual IP of the Service.

* **DNS**: An optional (though strongly recommended) cluster add-on is a DNS server. For each Service it creates a set of DNS records.

Using DNS we can simply do:

    ```shell
    $ kubectl run --rm ping -it --image alpine:3.5 sh
    wget -qO- nginx
    ```

* DNS will respond for `nginx` with the IP of Service `nginx`
* The virtual IP will basically load balance between the different instances of `nginx`
* If a Pod dies, another one will take its place, the service IP will stay the same and the service will continue to work as expected.

---

# Section 2.3: Networking Basics

Let's cleanup:

    ```shell
    $ kubectl delete -f kubernetes/06.nginx-service/
    ```

---

# Section 2.3: Networking Basics

During this section we learned:

* How the networking works in Kubernetes
* How we can expose a Service to the outside world
* How to do Service discovery inside the Cluster

