# Certified Kubernetes Administrator (CKA) Practice Exam: Part 1

**Count the Number of Nodes That Are Ready to Run Normal Workloads**

**Determine how many nodes in the cluster are ready to run normal workloads (i.e., workloads that do not have any special tolerations).**

**Output this number to the file `/k8s/0001/count.txt`**



**Linux**

```
kubectl config use-context acgk8s
kubectl get nodes
```

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

I just grepped for taints first to see what was currently set to see what is ready to run normal workloads. Only control was not ready.

**Linux**

```
cloud_user@acgk8s-control:~$ kubectl describe node acgk8s-worker1 | grep -i taints
cloud_user@acgk8s-control:~$ kubectl describe node acgk8s-worker2 | grep -i taints
cloud_user@acgk8s-control:~$ kubectl describe node acgk8s-control | grep -i taints
```

<figure><img src="../../../.gitbook/assets/image (2) (3).png" alt=""><figcaption></figcaption></figure>

**Linux**

```
kubectl get nodes -o='custom-columns=NodeName:.metadata.name,TaintKey:.spec.taints[*].key' | grep "<none>" | wc -l > /k8s/0001/count.txt
cat /k8s/0001/count.txt
```

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Retrieve Error Messages from a Container Log**

**In the `backend` namespace, check the log for the `proc` container in the `data-handler` Pod.**

**Save the lines which contain the text `ERROR` to the file `/k8s/0002/errors.txt`.**

**Linux**

```
kubectl config set-context --current --namespace=backend
kubectl get pods
```

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

**Linux**

```
kubectl logs -n backend data-handler -c proc | grep "ERROR" > /k8s/0002/errors.txt
```



**Find the Pod with a Label of app=auth in the Web Namespace That Is Utilizing the Most CPU**

**Before doing this step, please wait a minute or two to give our backend script time to generate CPU load. Determine which Pod in the `web` namespace with the label `app=auth` is using the most CPU. Save the name of this Pod to the file `/k8s/0003/cpu-pod.txt`.**



**Linux**

```
kubectl config set-context --current --namespace=web
kubectl get pods
```

<figure><img src="../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**Linux**

```
kubectl top pods -n web --sort-by cpu --selector=app=auth 
```

<figure><img src="../../../.gitbook/assets/image (1) (5).png" alt=""><figcaption></figcaption></figure>

**Linux**

```
kubectl top pods -n web --sort-by cpu --selector=app=auth | grep auth-web | awk '{print $1}' > /k8s/0003/cpu-pod.txt
```

