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

# Section 3.2: Memory and CPU Quotas

---

# Section 3.2: Memory and CPU Quotas

Kubernetes schedules a Pod to run on a Node only if the Node has enough CPU and RAM available to satisfy the total CPU and RAM requested by all of the containers in the Pod.

* **resources:requests** field: When you create a Pod, you can request CPU and RAM resources for the containers that run in the Pod.

Also, as a container runs on a Node, Kubernetes doesn’t allow the CPU and RAM consumed by the container to exceed the limits you specify for the container.

If a container exceeds its RAM limit, it is terminated. If a container exceeds its CPU limit, it becomes a candidate for having its CPU use throttled.

* **resources:limits** field: You can also set limits for CPU and RAM resources.

---

# Section 3.2: Memory and CPU Quotas

* Here is an example of assigning CPU and RAM resources:

    ```shell
    $ cat kubernetes/11.cpu-ram-limited-pod/cpu-ram-limited-pod.yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: cpu-ram-limited
    spec:
      containers:
      - name: nginx
        image: nginx:1.13-alpine
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "1"
    ```

The configuration file for the Pod requests `250 milicpu` and `64 mebibytes` of RAM.

It also sets upper limits of `1 cpu` and `128 mebibytes` of RAM.

(As a reference: megabyte = 1000 bytes, mebibyte = 1024 bytes)

---

# Section 3.2: Memory and CPU Quotas

* Let's deploy it:

    ```shell
    $ kubectl create -f kubernetes/11.cpu-ram-limited-pod/
    ```

* You can check the usage of CPU and Memory per node by describing nodes:

    ```shell
    $ kubectl describe node
    ...
      Namespace			Name                        CPU Requests	CPU Limits	Memory Requests	Memory Limits
      ---------			----                        ------------	----------	---------------	-------------
      ...
      default       cpu-ram-limited                 250m (12%)	1 (50%)		64Mi (3%)	128Mi (6%)
      ...
    ```

* Let's delete it:

    ```shell
    $ kubectl delete pod cpu-ram-limited
    ```
