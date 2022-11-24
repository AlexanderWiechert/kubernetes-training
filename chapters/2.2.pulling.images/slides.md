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

# Section 2.2: Pulling Images

<img class="absolute top-80 left-80 w-90" src="/chapters/2.2.pulling.images/registry.jpg" />

---

# Pulling Images

For each Pod there is a setting `imagePullPolicy` (by default is `IfNotPresent`)

* **IfNotPresent**: Image will be reused if already exists locally, otherwise will pull from registry.
* **Always**: Will always pull from registry.
* **Never**: Will never pull from registry and only try to run if the image already exists locally.

If you specify image tag `:latest` then it will always pull the image

If you don't specify image tag, `:latest` will be assumed

Nevertheless, you should avoid use `:latest` tag

---

# Private Registry

In order for Kubernetes to access Images on Private Registry it needs credentials

* Native support for Google Container Registry (GCR) when running on GCE
* Native support for AWS EC2 Container Registry, when running on AWS EC2

* Otherwise use a Secret to Store Docker Config:

    ```shell
    $ kubectl create secret docker-registry myregistrykey --docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL
    secret "myregistrykey" created.
    ```

And set it to `imagePullSecrets` on Pods or ServiceAccount.

