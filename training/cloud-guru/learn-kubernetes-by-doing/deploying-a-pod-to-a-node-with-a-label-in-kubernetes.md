# Deploying a Pod to a Node with a Label in Kubernetes

## **Get All Node Labels**

**Run a command to get all labels for all cluster nodes**

```
kubectl get no --show-labels
```

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

We can also run this to show nodes by label

```
kubectl get no -l disk=ssd
```

## Create and Apply the Pod YAML

**Create a file named `pod.yaml` and ensure that it has specified the node with the label `disk=ssd`**

**pod.yaml**

<pre><code>apiVersion: v1
kind: Pod
metadata:
    name: new-pod
spec:
   containers:
    - name: nginx
      image: nginx 
<strong>   nodeSelector:
</strong>      disk: ssd    
</code></pre>

**Apply the YAML to your Kubernetes cluster**

```
kubectl create -f pod.yaml
```



## Verify The pod is Running on the Correct Node

**Run a command that will show the pods and their associated nodes**

```
kubectl get po -o wide
```

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>



















