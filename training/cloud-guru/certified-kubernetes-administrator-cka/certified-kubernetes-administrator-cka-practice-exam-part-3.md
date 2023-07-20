# Certified Kubernetes Administrator (CKA) Practice Exam: Part 3

\
**Create a Service Account. Create a service account in the `web` namespace called `webautomation`.**

**Linux**

<pre><code><strong>kubectl config use-context acgk8s
</strong>kubectl create sa webautomation -n web
</code></pre>





**Create a ClusterRole That Provides Read Access to Pods. Create a ClusterRole called `pod-reader` that has `get`, `watch`, and `list` access to all Pods.**

**Linux**

```
vi pod-reader.yml
```

**pod-reader.yml**

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
   name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]   
```

**Linux**

<pre><code><strong>kubectl create -f pod-reader.yml
</strong></code></pre>



**Bind the ClusterRole to the Service Account to Only Read Pods in the web Namespace. Bind the ClusterRole to the `webautomation` service account so that it can read all Pods, but only in the `web` namespace.**

**Linux**

```
vi rb-pod-reader.yml
```

**rb-pod-reader.yml**

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
   name: rb-pod-reader
   namespace: web
subjects:
- kind: ServiceAccount
  name: webautomation    
roleRef:
   kind: ClusterRole
   name: pod-reader
   apiGroup: rbac.authorization.k8s.io   
```

**Linux**

<pre><code><strong>kubectl create -f rb-pod-reader.yml
</strong><strong>kubectl get pods -n web --as=system:serviceaccount:web:webautomation
</strong></code></pre>

There are no pods in this namespace but if there was we'd be able to view them as the webautomation service account.

<figure><img src="../../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>
