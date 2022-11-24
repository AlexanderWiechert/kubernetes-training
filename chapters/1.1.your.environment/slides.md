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

# 1.1 Environment Preparation

In this section we'll see how to prepare the environment with the necessary tools

- **Install Minikube** to Run Kubernetes locally
- **Install Kubectl** to manage Kubernetes through CLI
- **Install Kubernetic** to manage Kubernetes through GUI
- **Install AWS CLI** to access Amazon Web Services through CLI
- **Install Kops** to Run Kubernetes on AWS
- **Install Helm** to manage Kubernetes Packages

---

# Install Minikube

<img class="absolute bottom-10 right-10 w-30" src="/chapters/1.1.your.environment/minikube.png" />

Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day.

---

# Install Minikube

<img class="absolute bottom-10 right-10 w-30 opacity-30 -z-10" src="/chapters/1.1.your.environment/minikube.png" />

Minikube is easy to install

* on **OSX**

    ```shell
    $ curl -Lo minikube \
    https://storage.googleapis.com/minikube/releases/v0.22.2/minikube-darwin-amd64 \
    && chmod +x minikube \
    && sudo mv minikube /usr/local/bin/
    ```

* on **Linux**

    ```shell
    $ curl -Lo minikube \
    https://storage.googleapis.com/minikube/releases/v0.22.2/minikube-linux-amd64 \
    && chmod +x minikube \
    && sudo mv minikube /usr/local/bin/
    ```

On **Windows** there is an installer [here](https://github.com/kubernetes/minikube/releases)

---

# Requirements for Minikube

Minikube uses Virtualization to run a Virtual Machine that hosts all the components for running Kubernetes. Once the VM is ready you also need `kubectl` (The Kubernetes Client) to interact with it.

Requirements:

* OS X
  * xhyve driver, VirtualBox or VMware Fusion installation
* Linux
  * VirtualBox or KVM installation,
* Windows
  * Hyper-V
* VT-x/AMD-v virtualization must be enabled in BIOS
* `kubectl` must be on your path. 

---

# Install Kubectl

Kubectl is even easier to [install](https://kubernetes.io/docs/user-guide/prereqs/)

* on **OSX** with Brew (*Recommended*)

    ```shell
    # with brew (recommended)
    brew install kubernetes-cli
    # without brew
    $ curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl" \
    && chmod +x kubectl \
    && sudo mv kubectl /usr/local/bin/
    ```

* on **Linux**

    ```shell
    $ curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl" \
    && chmod +x kubectl \
    && sudo mv kubectl /usr/local/bin/
    ```

* on **Windows**

    ```shell
    $ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/windows/amd64/kubectl.exe
    ```

---

# Install Kubernetic

<img class="absolute top-30 left-20" src="/chapters/1.1.your.environment/kubernetic.png" />

Kubernetic is a Desktop Cient for Kubernetes Cluster management

---

# Installing Kubernetic

There are Installers for OSX, Linux or Windows at [http://kubernetic.com/](http://kubernetic.com/)

It reuses `kubectl` cluster configuration, so once the CLI is configured, Kubernetic can be used to visualize and manage the cluster

Kubernetic is developed by *Harbur Cloud Solutions S.L.*

During the Training we'll use it for visualizing better the concepts of Kubernetes

---

# Install AWS CLI

For Operating a Cluster in AWS we'll need [AWS CLI](https://github.com/aws/aws-cli) and some credentials

* on **OSX** with Brew (*Recommended*)

    ```shell
    brew install awscli
    ```

* on other OSes:

    ```shell
    pip install awscli
    ```

---

# Configure AWS CLI

* To check if AWS CLI is installed

    ```shell
    $ aws --version
    aws-cli/1.14.30 Python/3.6.4 Darwin/16.7.0 botocore/1.8.34
    ```

* To configure credentials you'll need an [AWS Access Key](https://aws.amazon.com/developers/access-keys/)

    ```shell
    $ aws configure
    AWS Access Key ID: foo
    AWS Secret Access Key: bar
    Default region name [us-west-2]: us-west-2
    Default output format [None]: json
    ```

* Check credentials work by listing the users

    ```shell
    $ aws iam list-users
    ```

---

# Install Kops

[Kops](https://github.com/kubernetes/kops) stands for Kubernetes Operations (kops) - Production Grade K8s Installation, Upgrades, and Management

Platforms:

* **AWS**
* **GCE**

* on **OSX** with Brew (*Recommended*)

    ```shell
    $ brew install kops
    ```

on other OSes visit [releases](https://github.com/kubernetes/kops/releases)

---

# Configure Kops

* To check if Kops is installed

    ```shell
    $ kops version
    Version 1.8.0
    ```

* To [configure](https://github.com/kubernetes/kops/blob/master/docs/aws.md) Kops you need a AWS S3 to store the State (save it at `/.bashrc`)

    ```shell
    export KOPS_STATE_STORE=s3://prefix-example-com-state-store
    ```

* Then you can check to see your clusters

    ```shell
    $ kops get clusters
    NAME                    CLOUD   ZONES
    mycluster.mydomain.io   aws     eu-west-1a,eu-west-1b,eu-west-1c
    ```

---

# Install Helm

<img class="absolute bottom-10 right-10 w-30" src="/chapters/1.1.your.environment/helm.png" />

[Helm](https://github.com/kubernetes/helm) is The Kubernetes Package Manager

---

# Install Helm

<img class="absolute bottom-10 right-10 w-30 opacity-30 -z-10" src="/chapters/1.1.your.environment/helm.png" />

* on **OSX** with Brew (*Recommended*)

    ```shell
    $ brew install kubernetes-helm
    ```

* On **Linux** (or OSX without Brew)

    ```shell
    $ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
    $ chmod 700 get_helm.sh
    $ ./get_helm.sh
    ```

* On **Windows**

    ```shell
    $ curl https://storage.googleapis.com/kubernetes-helm/helm-v2.2.2-windows-amd64.zip
    ```

---

# Install Helm

<img class="absolute bottom-10 right-10 w-30 opacity-30 -z-10" src="/chapters/1.1.your.environment/helm.png" />

* To review helm is installed

    ```shell
    $ helm version -c
    Client: &version.Version{SemVer:"v2.8.0", GitCommit:"14af25f1de6832228539259b821949d20069a222", GitTreeState:"clean"}
    ```

NOTE: `-c` stands for client version only, since we don't have kubernetes installed yet.

---

# 1.1 Environment Preparation

After this section we now have achieved the following:

* **Install Minikube** to Run Kubernetes locally
* **Install Kubectl** to manage Kubernetes through CLI
* **Install Kubernetic** to manage Kubernetes through GUI
* **Install AWS CLI** to access Amazon Web Services through CLI
* **Install Kops** to do Kubernetes Operations (e.g. Upgrades)
* **Install Helm** to manage Kubernetes Packages
