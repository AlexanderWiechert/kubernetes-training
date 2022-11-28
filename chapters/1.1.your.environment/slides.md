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

# 1.1 Environment Preparation

<img class="absolute top-80 left-50" src="/chapters/1.1.your.environment/tools.jpg" />

---

# Your Environment

<img class="absolute bottom-10 right-10 w-30" src="/chapters/1.1.your.environment/minikube.png" />

Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day.

---

# Run Minikube remotely

<img class="absolute bottom-10 right-10 w-30 opacity-30 -z-10" src="/chapters/1.1.your.environment/minikube.png" />

We'll run minikube remotely on a playground:

* Open: https://kubernetes.io/docs/tutorials/hello-minikube/

* Click `Launch Terminal`

* To verify it works ok:

    ```shell
    $ kubectl get nodes
    NAME       STATUS   ROLES                  AGE     VERSION
    minikube   Ready    control-plane,master   2m26s   v1.20.2
    ```

* Click `Preview Port 30000` to see Kubernetes Dashboard.

---

# Install Kubectx

Kubectx is a helper tool to switch contexts and namespaces in kubectl.

* To install on the playground:

    ```shell
    snap install kubectx --classic
    ```

* This installs the following tools: `kubectx` and `kubens`.

    ```shell
    # kubectx is for switching contexts
    $ kubectx
    minikube
    # kubens is for switching namespaces
    $ kubens
    default
    kube-node-lease
    kube-public
    kube-system
    kubernetes-dashboard
    ```
