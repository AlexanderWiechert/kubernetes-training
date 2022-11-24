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

# Section 1.3: Running Kubernetes

---

# Section 1.3: Running Kubernetes

In this section we'll talk about options how to run a Kubernetes Cluster

* Minikube (For Single-node Clusters in Laptops)
* Kops (For Multi-node Clusters in AWS)

---

# Choosing the proper Cluster

If you want to experiment with a Kubernetes Cluster yourself use:

* Minikube

If you want a collaborative environment (For Dev, Test, Production) and have access to AWS use:

* Kops

Today we'll use Minikube for most of the course

---

# Starting Minikube

* Check if minikube is installed properly:

    ```shell
    $ minikube version
    minikube version: v0.25.0
    ```

* To check the state of your Kubernetes

    ```shell
    $ minikube status
    minikubeVM: Running
    localkube: Running
    ```

* minikubeVM is the Virtual Machine where kubernetes runs
* localkube is the Kubernetes (all components in one binary) inside the minikube

---

# Starting Minikube

* To start the Kubernetes

    ```shell
    $ minikube start
    Starting local Kubernetes cluster...
    Starting VM...
    SSH-ing files into VM...
    Setting up certs...
    Starting cluster components...
    Connecting to cluster...
    Setting up kubeconfig...
    Kubectl is now configured to use the cluster.
    ```

* If the minikubeVM image is not downloaded before, it will download it for the first time. It will start the VM with Kubernetes. Configure `kubectl` locally to use the cluster.

* Now you can check connectivity with your cluster using `kubectl`:

    ```shell
    $ kubectl get nodes
    NAME       STATUS    AGE
    minikube   Ready     2d
    ```

---

# Starting Kubernetic

Now launch Kubernetic, you should be able to connect to the Cluster

<img class="absolute top-40 left-0" src="/chapters/1.3.running.kubernetes/kubernetic.png" />

---

# Section 1.3: Running Kubernetes

In this section we managed to create a Kubernetes cluster and connect to it.