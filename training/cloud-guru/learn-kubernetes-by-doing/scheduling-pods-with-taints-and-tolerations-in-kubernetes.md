# Scheduling Pods with Taints and Tolerations in Kubernetes

## Taint one of the worker nodes to repel work

Use the following command to taint the node:

```
kubectl get nodes
kubectl taint node $nodeName node-type=prod:NoSchedule
```

<figure><img src="../../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

## Schedule a pod to the dev environment

Use the following YAML to specify a pod that will be scheduled to the dev environment:

**dev-pod.yaml**

```
apiVersion: v1
kind: Pod
metadata:
  name: dev-pod
  labels:
    app: busybox
spec:
  containers:
  - name: dev
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
```

Use the following command to create the pod:

```
kubectl create -f dev-pod.yaml
```

## Allow a pod to be scheduled to the prod environment

Use the following YAML to create a deployment and a pod that will tolerate the prod environment:

**prod-deployment.yaml**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prod
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prod
  template:
    metadata:
      labels:
        app: prod
    spec:
      containers:
      - args:
        - sleep
        - "3600"
        image: busybox
        name: main
      tolerations:
      - key: node-type
        operator: Equal
        value: prod
        effect: NoSchedule
```

Use the following command to create the pod:

```
kubectl create -f prod-deployment.yaml
kubectl get pods -o wide
```

<figure><img src="../../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

## Verify each pod has been scheduled and verify the toleration.

Use the following command to verify the pods have been scheduled:

```
kubectl get pods -o wide
```

Scale up the deployment:

We can see the prod pods will be deployed on node .102 or .103 where as the dev-pod would only ever be deployed on .103 because of the taint on node .102

```
kubectl scale deployment/prod --replicas=3
```

<figure><img src="../../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

Verify the toleration of the production pod:

```
kubectl get pods $podName -o yaml | grep tolerations: -A12
```

<figure><img src="../../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

I found the video didn't do a great job explaining what was happening but to show it's working, I made a new deployment and tried making pods until it crashed because the tainted node wouldn't accept them,

**test.yaml**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - args:
        - sleep
        - "3600"
        image: busybox
        name: main
```

Use the following command to create the pod:

We can see the test pods won't get deployed on node .102 because unlike the prod code it doesn't have the  toleration set to bypass the taint on the node.

```
kubectl create -f test.yaml
kubectl scale deployment/test --replicas=30
kubectl get pods -o wide
```

<figure><img src="../../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

