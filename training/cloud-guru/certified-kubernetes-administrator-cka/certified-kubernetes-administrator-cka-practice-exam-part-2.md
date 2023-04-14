# Certified Kubernetes Administrator (CKA) Practice Exam: Part 2

**Edit the Web Frontend Deployment to Expose the HTTP Port**

**In the `web` namespace, there is a deployment called `web-frontend`.**

**Edit this deployment so that the containers within its Pods expose port 80.**



**Linux**

```
kubectl config use-context acgk8s
kubectl config set-context --current --namespace=web
kubectl edit deployment -n web web-frontend
```

Add the following lines then save the file

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



**Create a Service to Expose the Web Frontend Deployment's Pods Externally**

**Create a service called `web-frontend-svc` in the `web` namespace. This service should make the Pods from the `web-frontend` deployment in the `web` namespace reachable from outside the cluster.**

**External entities should be able to reach the service by contacting any node in the cluster on port 30080.**

**Linux**

```
vi web-frontend-svc.yml
```

**web-frontend-svc.yml**

```
apiVersion: v1
kind: Service
metadata:
   name: web-frontend-svc
   namespace: web
spec:
   type: NodePort
   selector:
      app: web-frontend
   ports:
   - protocol: TCP
     port: 80
     targetPort: 80
     nodePort: 30080
```



**Linux**

```
kubectl create -f web-frontend-svc.yml
```

**Scale Up the Web Frontend Deployment**

**Scale the `web-frontend` deployment in the `web` namespace up to `5` replicas.**

**Linux**

```
kubectl scale deployment web-frontend -n web --replicas=5
kubectl get deployment -n web
```

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



**Create an Ingress That Maps to the New Service**

**Create an Ingress called `web-frontend-ingress` in the `web` namespace that maps to the `web-frontend-svc` service in the `web` namespace. The Ingress should map all requests on the `/` path.**



**Linux**

```
vi web-frontend-ingress.yml
```

**web-frontend-ingress.yml**

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
   name: web-frontend-ingress
   namespace: web
spec:
   rules:
   - http:
        paths:
        - path: /
          pathType: Prefix
          backend:
             service:
                name: web-frontend-svc
                port:
                   number: 80
```



**Linux**

```
kubectl create -f web-frontend-ingress.yml
```



