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

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

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

## Install Nginx Ingress Controller <a href="#lab-page-title" id="lab-page-title"></a>

Change directories. Within the terminal enter the following command

```
cd /home/project/code/ingress-nginx 
```

Install the Nginx Ingress Controller resources. Within the terminal enter the following

```
kubectl apply -f install.0.32.0.yaml
```

Examine the new Nginx Ingress Controller resources created by the previous command. Within the terminal run the following commands

```
kubectl get all -n ingress-nginx
```

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Behind the scenes minikube is being used to provide the Kubernetes cluster within this lab environment. To facilitate internet inbound traffic to the Nginx ingress controller hosted when using minikube for the cluster - you'll need to make a networking update (`hostNetwork: true`) to the **ingress-nginx-controller** deployment resource. To accomplish this, first generate a local copy of the YAML configuration for the **ingress-nginx-controller** deployment resource. Within the terminal run the following commands.

```
kubectl get deploy ingress-nginx-controller -n ingress-nginx -o yaml >> nginx-ingress-controller.yaml
```

The **nginx-ingress-controller.yaml** manifest file is very large. To make it easier to inject the new **hostNetwork** setting into the exact required location, use the provided **yq** utility. The **yq** utility will first need to be made executable. Within the terminal run the following commands.

```
chmod +x ../utils/yq
```

Confirm that the **yq** utility is now executable. Within the terminal run the following commands

```
ls -la ../utils/yq
```

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Inject the **hostNetwork: true** setting into the **nginx-ingress-controller.yaml** file. Within the terminal run the following commands.

```
../utils/yq w nginx-ingress-controller.yaml spec.template.spec.hostNetwork true > nginx-ingress-controller-v2.yaml
```

Delete and recreate the updated **ingress-nginx-controller** deployment. Within the terminal run the following commands

```
kubectl delete deploy ingress-nginx-controller -n ingress-nginx
kubectl apply -f nginx-ingress-controller-v2.yaml
```

### Deploy the API Deployment and Service Resources <a href="#lab-page-title" id="lab-page-title"></a>
