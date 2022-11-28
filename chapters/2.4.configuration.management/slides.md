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

# Section 2.4: Configuration Management

---

# Section 2.4: Configuration Management

In this section we'll discuss about the following resources:

* Environment Variables
* ConfigMaps
* Secrets

---

# Section 2.4: Configuration Management

In order to configure our environment there are two ways to do it:

* Environment Variables
* Mounted Files

Why configurable containers, why not store it inside the image?

* Configuration is different on each environment
* The same Image should be able to be reused in different environments (Build once, run anywhere)
* Configuration should not be burned inside the Image, but deployed on runtime

---

# Section 2.4: Configuration Management

* Let's create a Pod with an environment variable

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: env-pod
    spec:
      restartPolicy: Never
      containers:
      - name: app
        image: "alpine:3.5"
        command: ["env"]
        env:
        - name: HELLO
          value: "Hello from the environment"
    ```
---

# Section 2.4: Configuration Management

* Let's create the Pod:

    ```shell
    $ kubectl create -f 07.env-pod/
    ```

* Let's see the logs:

    ```shell
    $ kubectl logs env-pod
    ```

* Let's cleanup:

    ```shell
    $ kubectl delete pod env-pod
    ```

---

# Section 2.4: Configuration Management

Let's create a Pod with a mounted file.

* The content of the file can be stored at a ConfigMap:

    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: volumemount-configmap
    data:
      key-a: bob
      key-b: alice
      key-c: |
        This is a multiline
        data value
    ```

---

# Section 2.4: Configuration Management

* And in a Pod we can reference that ConfigMap to bind mount it in a directory:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: volumemount
    spec:
      restartPolicy: Never
      containers:
      - name: app
        image: "alpine:3.5"
        command: ["cat", "/config/app.conf"]
        volumeMounts:
        - mountPath: /config
          name: config
      volumes:
      - name: config
        configMap:
          name: volumemount-configmap
          items:
          - key: key-c
            path: app.conf
    ```

---

# Section 2.4: Configuration Management

* Let's create the Pod:

    ```shell
    $ kubectl create -f 08.volumemount-configmap/
    ```

Let's see the logs:

    ```shell
    $ kubectl logs volumemount
    ```

Let's cleanup:

    ```shell
    $ kubectl delete -f 08.volumemount-configmap/
    ```
