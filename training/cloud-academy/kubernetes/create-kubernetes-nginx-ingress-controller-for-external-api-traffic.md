---
description: >-
  This lab will teach how to install and setup the Nginx Ingress Controller,
  Deploy a ready made Programming Languages themed API into a Kubernetes
  cluster, Create and deploy an Ingress resource to expo
---

# Create Kubernetes Nginx Ingress Controller for External API Traffic

## Walkthrough

### Connecting to the CloudAcademy Web based K8s IDE <a href="#lab-page-title" id="lab-page-title"></a>

In the AWS Management Console search bar, enter _EC2_, and click the **EC2** result under **Services.**

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

In the left-hand menu, click on **Instances**, select the _k8s.cluster.cloudacademy.platform.instance_ EC2 instance, and locate and copy the _IPv4 Public IP_ address.&#x20;

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

The web-based CloudAcademy IDE has been configured to listen for inbound connections on port **3000** using HTTP. Using your browser, navigate to the IDE hosted on the _k8s.cluster.cloudacademy.platform.instance_ EC2 instance using the public IP address you just copied.

```
http://PUBLIC_IP_IDE_CLOUDACADEMY_PLATFORM_INSTANCE:3000
```

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### Install Cilium CNI <a href="#lab-page-title" id="lab-page-title"></a>

Within the Files Explorer pane right-click on the **project/code/cillium** folder and then select the **Open in Terminal** option to launch the integrated terminal

<figure><img src="../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

Use the **kubectl** command to install the Cilium Kubernetes CNI plugin into the local Kubernetes cluster. Within the terminal enter the following command.

```
kubectl apply -f install.1.6.1.yaml
```

Use both **watch** and **kubectl** to view the Cilium pods starting up. Within the terminal enter the following commands

```
watch -n2 kubectl -n kube-system get pods
```

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

