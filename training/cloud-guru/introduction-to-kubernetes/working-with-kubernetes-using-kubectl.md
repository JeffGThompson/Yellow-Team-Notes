# Working with Kubernetes Using kubectl

## Use kubectl to Check the Status of Worker Nodes in the Cluster

* Use `kubectl` to list all nodes in the cluster
* Verify the nodes are in the READY state





## Get a List of Pods Running in the Cluster

* Use `kubectl` to list Pods in all namespaces
* Verify whether or not these Pods are all up and running

```
kubectl get pods --all-namespaces
```

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

## Delete an Existing Pod

* Delete the Pod named `web-frontend`

```
kubectl delete pod web-frontend
kubectl get pods --all-namespaces
```

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

## Create a New Pod

* Create a new Pod called `web-frontend`
* Make it run a container with the `nginx` image



```
vi web-frontend.yml
```

**web-frontend.yml**

```
apiVersion: v1
kind: Pod
metadata:
    name: web-frontend
spec:
    containers:
    - name: nginx
      image: nginx    
```

```
kubectl create -f web-frontend.yml
kubectl get pods --all-namespaces
```

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>























