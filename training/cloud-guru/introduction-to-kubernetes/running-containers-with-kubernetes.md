# Running Containers with Kubernetes

**Create an Nginx Pod to Function as a Simple Web Server**

* **Create a Pod running a container with the `nginx` image**
* **Expose port 80 in order to communicate with Nginx once it is running**

**nginx-pod.yml**

```
apiVersion: v1
kind: Pod
metadata:
    name: nginx-pod
spec:
    containers:
    - name: nginx
      image: nginx 
     ports:
     - containerPort: 80      
```

```
kubectl create -f nginx-pod.yml
```

**Run a Redis Instance Using a Kubernetes Pod**

* **Create a Pod running a container with the `redis` image**

**redis-pod.yml**

```
apiVersion: v1
kind: Pod
metadata:
    name: redis-pod
spec:
    containers:
    - name: redis
      image: redis  
```

```
kubectl create -f redis-pod.yml
kubectl get pods
```

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Check Nginx Container Logs**

* **View the logs from the Nginx container**

```
kubectl get pods -o wide
curl 192.168.194.65
kubectl logs nginx-pod
```

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>



