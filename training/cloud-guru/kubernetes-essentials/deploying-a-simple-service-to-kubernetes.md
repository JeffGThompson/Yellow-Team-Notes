# Deploying a Simple Service to Kubernetes

**Create a deployment for the store-products service with four replicas.**

##

**Create the deployment with four replicas:**

```
cat << EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: store-products
  labels:
    app: store-products
spec:
  replicas: 4
  selector:
    matchLabels:
      app: store-products
  template:
    metadata:
      labels:
        app: store-products
    spec:
      containers:
      - name: store-products
        image: linuxacademycontent/store-products:1.0.0
        ports:
        - containerPort: 80
EOF
```

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

## Create a store-products service and verify that you can access it from the busybox testing pod.

**Create a service for the store-products pods:**

```
cat << EOF | kubectl apply -f -
kind: Service
apiVersion: v1
metadata:
  name: store-products
spec:
  selector:
    app: store-products
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
EOF
```

**Make sure the service is up in the cluster:**

```
kubectl get svc store-products
curl http://$ClusterIP
```

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Use kubectl exec to query the store-products service from the busybox testing pod.**

```
kubectl exec busybox -- curl -s store-products
```

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>











