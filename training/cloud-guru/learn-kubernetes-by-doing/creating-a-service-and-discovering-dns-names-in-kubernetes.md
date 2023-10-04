# Creating a Service and Discovering DNS Names in Kubernetes

## Create an nginx deployment, and verify it was successful

Use this command to create an nginx deployment:

```
kubectl run nginx --image=nginx
```

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Use this command to verify deployment was successful:

```
kubectl get deployments
```

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Create a service, and verify the service was successful

Use this command to create a service:

```
kubectl expose deployment nginx --port 80 --type NodePort
```

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Use this command to verify the service was created:

```
kubectl get services
```

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Create a pod that will allow you to query DNS, and verify it’s been created

**Use the following YAML to create the busybox pod spec:**

**busybox.yaml**

```
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - image: busybox:1.28.4
    command:
      - sleep
      - "3600"
    name: busybox
  restartPolicy: Always
```

Use the following command to create the busybox pod:

```
kubectl create -f busybox.yaml
```

Use the following command to verify the pod was created successfully:

```
kubectl get pods
```

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Perform a DNS query to the service

Use the following command to query the DNS name of the nginx service:

```
kubectl exec busybox -- nslookup nginx
```

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

## Record the DNS name

Record the name:

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>



























