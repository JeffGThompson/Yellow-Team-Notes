---
description: Defining an application's resource requirements using limits and requests
---

# Mastering Kubernetes Pod Configuration: Defining Resource Requirements



## Walkthrough

Create a Pod manifest for a Pod that will consume a lot of CPU resources. The stress image runs a binary that can consume a varying amount of resources. The args instruct stress to attempt to consume two CPU cores, which is how many cores each node in the cluster has.

#### **load.yaml**

```
apiVersion: v1
kind: Pod
metadata:
  name: load
spec:
  containers:
  - name: cpu-load
    image: cloudacademydevops/stress
    args:
    - -cpus
    - "2"
```

Create the Pod. The pod simulates a situation where you notice degraded performance in the cluster. When there are many pods and nodes in the cluster, it is difficult to determine which pod or pods are causing the degradation by inspecting each node and pod individually.

```
kubectl apply -f load.yaml
```

List the resource consumption of pods. The **load** pod is using nearly two full cores (**1930m**illi-cores). Having such an unrestricted pod in your cluster could wreak havoc.

```
kubectl top pods
```

<figure><img src="../../../.gitbook/assets/image (18) (2).png" alt=""><figcaption></figcaption></figure>

The Pod is using almost all the CPU for one of the nodes in the cluster.

```
kubectl top nodes
```

<figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>



Create a similar Pod specification except with CPU and memory resource limits and requests. The resources key is added to specify the limits and requests. The Pod will only be scheduled on a Node with 0.35 CPU cores and 10MiB of memory available. It's important to note that the scheduler doesn't consider the actual resource utilization of the node. Rather, it bases its decision upon the sum of container resource requests on the node. For example, if a container requests all the CPU of a node but is actually 0% CPU, the scheduler would treat the node as not having any CPU available. In the context of this lab, the load Pod is consuming 2 CPUs on a Node but because it didn't make any request for the CPU, its usage doesn't impact following scheduling requests.

#### **load-limited.yaml**

```
apiVersion: v1
kind: Pod
metadata:
  name: load-limited
spec:
  containers:
  - name: cpu-load-limited
    image: cloudacademydevops/stress
    args:
    - -cpus
    - "2"
    resources:
      limits:
        cpu: "0.5" # half a core
        memory: "20Mi" # 20 mebibytes 
      requests:
        cpu: "0.35" # 35% of a core
        memory: "10Mi" # 20 mebibytes
```

The nodes and output their non-terminated Pods tables to highlight how scheduling decisions are made based on the current workload. Although the Pod is using almost 2 CPUs, the resource table does not allocate any request or limit for the Pod so it does not impact the scheduling decision of the load-limited Pod.

```
kubectl describe nodes | grep --after-context=5 "Non-terminated Pods"
```

<figure><img src="../../../.gitbook/assets/image (11) (2) (1).png" alt=""><figcaption></figcaption></figure>

Create the Pod with the resource constraints.

```
kubectl apply -f load-limited.yaml
```

Get the Pods in wide output to display the Node running each Pod.

```
kubectl get pods -o wide
```

<figure><img src="../../../.gitbook/assets/image (3) (2).png" alt=""><figcaption></figcaption></figure>

Wait a minute for the new Pod's metrics to be collected, and then display the Pod resource utilization. Notice the **load-limited** Pod is using almost half of a CPU core, which is the limit you set. The request is used for making scheduling decisions but the limit impacts the actual utilization. Using requests and limits for CPU and memory can prevent performance issues, and allow the scheduler to make the best use of the cluster's resources.

```
kubectl top pods
```

<figure><img src="../../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

#### **load.yaml - Updated #1**

The value of 1.7 is chosen because there may not be a Node with a full 2 CPUs available considering some system Pods also make requests in addition to the Pods we create.

<pre><code>apiVersion: v1
kind: Pod
metadata:
  name: load
spec:
  containers:
  - name: cpu-load
    image: cloudacademydevops/stress
    args:
    - -cpus
    - "2"
<strong>    resources:
</strong>      requests:
        cpu: "1.7"
</code></pre>

Apply the new Pod manifest

```
kubectl apply --force -f load.yaml
```

Confirm the Pods are running on separate Node

```
kubectl get pods -o wide
```

<figure><img src="../../../.gitbook/assets/image (8) (2).png" alt=""><figcaption></figcaption></figure>
