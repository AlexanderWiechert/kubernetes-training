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

# 2.1. Deployment Process

<img class="absolute top-80 left-80 w-80" src="/chapters/2.1.deployment.process/deployment.jpg" />

---

# 2.1. Deployment Process

In this section we'll learn how to manage deployments on Kubernetes without downtimes, manage upgrades, scale and do rollbacks.

We'll learn about the following resources:

* Deployments

---

# 2.1. Deployment Process

With Replica Sets we now have the definition multiple Pods under a specific version.

In order to be able to manage an upgrade or downgrade of a service without having downtime during the upgrade we have to handle multiple instances of both versions during the upgrade.

The objective of Deployment is to handle this process.

---

# 2.1. Deployment Process

* Let's use a Deployment definition to deploy 2 Nginx instances in our Cluster.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      name: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.12-alpine
            ports:
            - containerPort: 80
    ```

---

# 2.1. Deployment Process

* Let's deploy it:

    ```shell
    $ kubectl create -f 05.nginx-deployment/
    ```

You'll see that the Deployment has generated a Replica Set and that in turn generated 2 Pods.

* Let's see the Pods:

    ```shell
    $ kubectl get pods
    ```

* Let's see the ReplicaSets:

    ```shell
    $ kubectl get replicasets
    ```

* Let's see the Deployment:

    ```shell
    $ kubectl get deployment
    ```

---

# 2.1. Deployment Process

The Deployment is configured with 2 replicas, but we can update the definition of the Deployment to increase or decrease the number of Pods and that information is delegated to the Replica Set which in turn is responsible of the number of Pods running.

* As an example we'll increase the number of Pods to 8.

    ```shell
    $ kubectl scale deployment nginx --replicas=8
    ```

---

# 2.1. Deployment Process

We currently use Nginx `v1.12`, but there is a new version of Nginx `v1.13` we want to deploy to the cluster.

* Let's say we have 8 instances of Nginx running, the objective is to change all of them from v1.12 to v1.13 without loosing service availability.

    ```shell
    $ kubectl set image deployment nginx *=nginx:1.13-alpine
    ```

* We can see the status of the rollout:

    ```shell
    $ kubectl rollout status deployment nginx
    ```

* If something goes wrong with the new deployment, we can simply rollback the deployment to the previous version:

    ```shell
    $ kubectl rollout undo deployment nginx
    ```

* Let's cleanup the Deployment:

    ```shell
    $ kubectl delete deployment nginx
    ```

---

# 2.1. Deployment Process

In this section we learned how to manage deployments on Kubernetes without downtimes, manage upgrades, scale and do rollbacks.
