---
description: >-
  This lab will train you on Pod configuration concepts that teach you how to
  configure service accounts to provide Pods with identities to harden your
  Kubernetes application deployments.
---

# Mastering Kubernetes Pod Configuration: Service Accounts

## Walkthrough

Create a Namespace for the resources you'll create in this lab step and change your default kubectl context to use the Namespace.

```
# Create namespace
kubectl create namespace serviceaccounts
# Set namespace as the default for the current context
kubectl config set-context $(kubectl config current-context) --namespace=serviceaccounts
```

Get the ServiceAccounts in the Namespace. Each Namespace has a default ServiceAccount. The default ServiceAccount grants minimal access to APIs and cannot be used to get any cluster state information. Therefore, you should use custom ServiceAccounts when your application requires access to cluster state.

```
kubectl get serviceaccounts
```

<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Create a Pod and get its YAML manifest. Observe the spec.serviceAccount is automatically set to the default ServiceAccount. To configure a Pod's ServiceAccount you can set the spec.serviceAccount when a Pod is created.

```
kubectl run default-pod --image=mongo:4.0.6
kubectl get pod default-pod -o yaml | more
```

<figure><img src="../../../.gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

Create a new ServiceAccount. It is a best practice to create a ServiceAccount for each of your applications to use the least amount of access necessary (principle of least privilege) to improve security. The created ServiceAccount will not have any specific role bound to it so there are no additional permissions associated with it. In practice, your Kubernetes administrator would create a role and bind it to the ServiceAccount.

```
kubectl create serviceaccount app-sa
```

Create a new Pod that uses the custom ServiceAccount.&#x20;

**pod-custom-sa.yaml**

```
apiVersion: v1
kind: Pod
metadata:
  name: custom-sa-pod 
spec:
  containers:
  - image: mongo:4.0.6
    name: mongodb
  serviceAccount: app-sa
```

```
kubectl create -f pod-custom-sa.yaml
```

You could also quickly generate a similar manifest file by using the following command which uses --dry-run=client -o yaml and then customizing it to your needs.

```
kubectl run default-pod --image=mongo:4.0.6 --serviceaccount=app-sa --dry-run=client -o yaml
```

Get the Pod's YAML manifest. The output confirms the app-sa ServiceAccount is being used. Upon pod creation, the ServiceAccount added a projected volume for authentication onto the pod to allow communication with the kube-api-server.

```
kubectl get pod custom-sa-pod -o yaml | more
```

<figure><img src="../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

