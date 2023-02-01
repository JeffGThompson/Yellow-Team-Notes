---
description: >-
  Kubernetes provides a variety of features to get the most out of your
  containerized applications. This lab will train you on Pod configuration
  concepts that teach you how to persist data beyond the li
---

# Mastering Kubernetes Pod Configuration: Persistent Data

## Walkthrough

Create a Namespace for the resources you'll create in this lab step and change your default kubectl context to use the Namespace.

```
kubectl create namespace persistence
kubectl config set-context $(kubectl config current-context) --namespace=persistence
```

Create a PVC

#### pvc.yaml

```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: db-data
spec:
  # Only one node can mount the volume in Read/Write
  # mode at a time
  accessModes:
  - ReadWriteOnce 
  resources:
    requests:
      storage: 2Gi
```

Create pod from pvc.yaml

```
kubectl create -f pvc.yaml
```

You will use the PVC to store data in a database, a common example of persistent data that should survive in case a Pod were to be terminated. Use get to display the PVC.

```
kubectl get pvc
```

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

pvc is a kubectl alias for persistentvolumeclaim, so you don't need to type the complete Resource name. The output shows the PVC STATUS is Bound, but the output may show Pending while the underlying PV is being created. The STORAGECLASS is gp2 which is the type of automatically configured Amazon EBS volumes that are created. Use get to display the underlying PV.

```
kubectl get pv
```

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Similar information is displayed. There is a **RECLAIM POLICY** associated with the PV. The **Delete** policy means the PV is deleted once the PVC is deleted. It is also possible to keep the PV using other reclaim policies. Create a Pod that mounts the volume provided by the PVC.

#### db.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: db 
spec:
  containers:
  - image: mongo:4.0.6
    name: mongodb
    # Mount as volume 
    volumeMounts:
    - name: data
      mountPath: /data/db
    ports:
    - containerPort: 27017
      protocol: TCP
  volumes:
  - name: data
    # Declare the PVC to use for the volume
    persistentVolumeClaim:
      claimName: db-data
```



```
kubectl create -f db.yaml
```

The Pod uses the MongoDB image, which is a NoSQL database that stores its database files at /data/db by default. The PVC is mounted as a volume at that path causing the database files to be written to the EBS Persistent volume. Run the MongoDB CLI client to insert a document that contains the message "I was here" into a test database and then confirm it was inserted:

```
kubectl exec db -it -- mongo testdb --quiet --eval \
  'db.messages.insert({"message": "I was here"}); db.messages.findOne().message'
```

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Delete the Pod. At this point, a regular (emptyDir) volume would be destroyed and the database files would be lost.

```
kubectl delete -f db.yaml
```

Create a new database Pod. Although the Pod is new, the Pod's spec refers to the same PVC as before.

```
kubectl create -f db.yaml
```

Attempt to find a document in the test database. The output confirms the data was persisted by using the PVC.

```
kubectl exec db -it -- mongo testdb --quiet --eval 'db.messages.findOne().message'
```

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>





&#x20;
