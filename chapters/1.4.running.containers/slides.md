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

# 1.4. Running Containers

<img class="absolute top-80 left-75 w-100" src="/chapters/1.4.running.containers/running.png" />


---

# Section 1.4: Running Containers

In this section we'll learn how to run our first containers in Kubernetes and we'll learn about:

* Pods
* ReplicaSets

---

# Pods

A pod is **a group of one or more containers** (such as Docker containers), the shared storage for those containers, and options about how to run the containers.

Pods are always co-located and co-scheduled, and run in a shared context.

A pod models an application-specific “logical host” - it contains one or more application containers which are relatively tightly coupled — in a pre-container world, they would have executed on the same physical or virtual machine.

---

# Pods

Why not just run multiple programs in a single (Docker) container?

* **Transparency**. Making the containers within the pod visible to the infrastructure enables the infrastructure to provide services to those containers, (e.g. process management and resource monitoring)

* **Decoupling software dependencies**. The individual containers may be versioned, rebuilt and redeployed independently. Kubernetes may even support live updates of individual containers someday.
Ease of use. Users don’t need to run their own process managers, worry about signal and exit-code propagation, etc.

* **Efficiency**. Because the infrastructure takes on more responsibility, containers can be lighter weight.

Why not support affinity-based co-scheduling of containers?

* That approach would provide co-location, but would not provide most of the benefits of pods, such as resource sharing, IPC, guaranteed fate sharing, and simplified management.

---

# Hello World Pod

* Let's see a simple hello-world Pod:

    ```shell
    $ cat kubernetes/01.hello-world/hello-world-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-world
    spec:
      restartPolicy: Never
      containers:
      - name: hello
        image: "alpine:3.5"
        command: ["echo", "Hello", "World"]
    ```

* We use Image `alpine:3.5`
* We run the command `echo Hello World`
* We tell Pod not to restart if finished

---

# Hello World Pod

* Let's create the Pod with `kubectl` CLI. In order to create it:

    ```shell
    $ kubectl create -f kubernetes/01.hello-world/hello-world-pod.yaml
    ```

* Let's see the Pods (`-a` is to also see the Completed ones):

    ```shell
    $ kubectl get pods -a
    ```

* To see the logs:

    ```shell
    $ kubectl logs hello-world
    ```

* Let's delete the Pod using the file reference:

    ```shell
    $ kubectl delete -f kubernetes/01.hello-world/hello-world-pod.yaml
    ```

* You can also delete a Pod using the name reference:

    ```shell
    $ kubectl delete pod hello-world
    ```

---


# Long Running Single-Container Pod

* A more useful example is a long running container. We'll now create a pod with one container that will run `nginx`.

    ```shell
    $ cat kubernetes/02.single-container-pod/single-container-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.13-alpine
        ports:
        - containerPort: 80
    ```

---


# Long Running Single-Container Pod

* Let's deploy it (You can reference a directory to the `create` command):

    ```shell
    $ kubectl create -f kubernetes/02.single-container-pod/
    ```

You'll see that the Pod once it finishes to download the image it is in `Running` State and is staying running.

* If you click on the Pod you'll not see any logs, but that's ok because Nginx doesn't give any logs. We'll see later how we can use the Nginx, but for now let's delete the Pod.

    ```shell
    $ kubectl delete pod nginx
    ```

---

# Multi-Container Pod

We'll now run a Pod with two containers.

Multi-container Pods should be avoided when possible:

* Containers inside a Pod are co-located (cannot spread to different machines)
* They scale up or down together (cannot scale separately)
* They share the same network IP (may exist port conflicts)

This makes the containers that are under the same port to be **tightly-coupled**, instead of **loosely-coupled**.

But there are some scenarios where this tightly-coupled containers are necessary, such as side-kick, health-check or log-collecting processes.

In our example we'll use a `nginx` together with an `alpine` container that will do requests of the nginx every `2` secs.

---


# Multi-Container Pod

* Multi-container pod:

    ```shell
    $ cat kubernetes/03.multi-container-pod/multi-container-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: multi-container
    spec:
      terminationGracePeriodSeconds: 0
      containers:
      - name: nginx
        image: nginx:1.13-alpine
        ports:
        - containerPort: 80
      - name: alpine
        image: alpine:3.5
        command: ["watch", "wget", "-qO-", "localhost"]
    ```

* Let's deploy it:

    ```shell
    $ kubectl create -f kubernetes/03.multi-container-pod/
    ```

---

# Multi-Container Pod

* What would happen if one of the containers crashes?

    ```shell
    $ minikube ssh -- 'docker kill $(docker ps -ql)'
    ```

* The Pod will enter `Error` State, will recreate the failing container (a new container)

    ```shell
    $ kubectl get pods
    ```

* What would happen if one of the containers keep crashing?

  * The Pod will enter `CrashLoopBackOff` State, it will wait some time (increased by each failure) and retry.

* What would happen if I loose the node where the Pod is assigned?

  * The Pod is lost, that's why we shouldn't create Pods directly

---

# Multi-Container Pod

* Let's cleanup the Pod:

    ```shell
    $ kubectl delete pod multi-container
    ```

---

# Replica Sets

With Pods we manage to declare the definition of one unit of deployment, e.g. one instance of Nginx.

In a cluster environment we probably want to have more than one instances of our applications for example for the sake of redundancy and throughput.

In order to manage this need we'll use Replica Sets. A Replica Set defines the Pods that needs to run and the number of their replicas.

We'll use the same example of Nginx before but now we'll run multiple instances.

---

## Replica Sets

* Replica Sets:

    ```shell
    $ cat kubernetes/04.nginx-replicaset/nginx-replicaset.yaml
    apiVersion: extensions/v1beta1
    kind: ReplicaSet
    metadata:
      name: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
          template: replicaset
      template:
        metadata:
          labels:
            app: nginx
            template: replicaset
        spec:
          containers:
          - name: nginx
            image: nginx:1.13-alpine
            ports:
            - containerPort: 80
    ```

---

# Replica Sets

* Let's deploy it:

    ```shell
    $ kubectl create -f kubernetes/04.nginx-replicaset/
    ```

* Let's see the Pods:

    ```shell
    $ kubectl get pods
    ```

* Let's see the ReplicaSet (you can use both singular or plural on the CLI):

    ```shell
    $ kubectl get replicaset
    ```

* You can also use aliases for faster typing:

    ```shell
    $ kubectl get rs
    ```

* To see the available resources (and their aliases):

    ```shell
    $ kubectl get
    ```

---

# Replica Sets

We can change the number of the desired replicas and the controller will make sure to update the cluster:

* We can scale up:

    ```shell
    $ kubectl scale replicaset nginx --replicas=10
    ```

* or scale down:

    ```shell
    $ kubectl scale replicaset nginx --replicas=2
    ```

If a Pod is deleted, Replica Set Controller will create a new Pod to meet the desired number of replicas.

So if I loose a node, the Pods there will be rescheduled to other Nodes automatically.

* Let's delete the ReplicaSet:

    ```shell
    $ kubectl delete replicaset nginx
    ```

Deleting the ReplicaSet it propagates the deletion of the Pods it manages

---

# Section 1.4: Running Containers

In this section learned how to run our first containers in Kubernetes and the following resources in the Kubernetes API:

* Pods
* ReplicaSets
