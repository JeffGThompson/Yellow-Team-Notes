---
description: >-
  Reviewing the available options for configuring security contexts. Then
  creating multiple pods with differing security contexts to observe the effects
  of each.
---

# Mastering Kubernetes Pod Configuration: Security Contexts

## Walkthrough

Explain the available Pod-level security context field

```
kubectl explain pod.spec.securityContext | more
```

Explain the available container-level security context fields

```
kubectl explain pod.spec.containers.securityContext | more
```



#### pod-no-security-context.yaml

The pod simply runs a container that sleeps.

```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-test-1
spec:
  containers:
  - image: busybox:1.30.1
    name: busybox
    args:
    - sleep
    - "3600"
```

Create the Pod and use exec to list the available devices in the container. There are only a minimal number of devices available in the container and none that can do any harm. For the sake of what you will do next, notice there are no block devices. In particular, there is no nvme0n1p1 device that is the host's file system disk.

```
kubectl create -f pod-no-security-context.yaml
kubectl exec security-context-test-1 -- ls /dev
```

<figure><img src="../../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

#### pod-privileged.yaml

Delete the previous Pod and create a similar Pod that has a privileged container

```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-test-2
spec:
  containers:
  - image: busybox:1.30.1
    name: busybox
    args:
    - sleep
    - "3600"
    securityContext:
      privileged: true
```

```
kubectl delete -f pod-no-security-context.yaml
kubectl create -f pod-privileged.yaml
```

List the devices available in the container. All of the host devices are available including the host file system disk **nvme0n1p1**. This could be a major security breach and shows the importance of carefully considering if you should ever use a privileged container.

```
kubectl exec security-context-test-2 -- ls -lah /dev
```

<figure><img src="../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

#### pod-runas.yaml

Create another pod that includes a Pod security context as well as a container security context. The Pod security context enforces that container processes do not run as root (runAsNonRoot) and sets the user ID of the container process to 1000. The container securityContext sets the container process' user ID to 2000 and sets the root file system to read-only.

```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-test-3
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    runAsGroup: 1000
  containers:
  - image: busybox:1.30.1
    name: busybox
    args:
    - sleep
    - "3600"
    securityContext:
      runAsUser: 2000
      readOnlyRootFilesystem: true
```

```
kubectl delete -f pod-privileged.yaml
kubectl create -f pod-runas.yaml
```

Open a shell in the container. Notice that the shell prompt is **$** and not # indicating that you are not the root user.

```
kubectl exec security-context-test-3 -it -- /bin/sh
```

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

List the running processes in the container. The **USER** ID is 2000 illustrating that it is not root and that the container security context overrides the setting in the Pod security context when both security contexts include the same field. Whenever possible you should not run as root.

```
ps
```

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Attempt to create a file in the /tmp directory

```
touch /tmp/test-file
exit
```

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Delete the pod resource

```
kubectl delete -f pod-runas.yaml
```
