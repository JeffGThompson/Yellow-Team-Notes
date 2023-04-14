---
description: >-
  How to how to create a ConfigMap from a literal key-value pair and mount the
  configuration data into a Pod using a volume. and create a new secret and use
  it in a Pod through environment variables.
---

# Mastering Kubernetes Pod Configuration: Config Maps and Secrets

## Walkthrough

### ConfigMaps

Create a Namespace for the resources you'll create in this Lab Step and change your default kubectl context to use the Namespace.

```
# Create namespace
kubectl create namespace configmaps
# Set namespace as the default for the current context
kubectl config set-context $(kubectl config current-context) --namespace=configmaps
```

Create a ConfigMap from two literal key-value pairs. The command creates one ConfigMap named app-config with two key-value pairs, DB\_NAME=testdb and COLLECTION\_NAME=messages.

```
kubectl create configmap app-config --from-literal=DB_NAME=testdb \
  --from-literal=COLLECTION_NAME=messages
```

Display the ConfigMap. This is also how you would declare an equivalent ConfigMap using a manifest file passed to kubectl create -f.

```
kubectl get configmaps app-config -o yaml
```

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Create a Pod that mounts the ConfigMap using a volume. The volume uses the configMap key to create a volume using a ConfigMap.

#### pod-configmap.yaml

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
    - name: config
      mountPath: /config
    ports:
    - containerPort: 27017
      protocol: TCP
  volumes:
  - name: config
    # Declare the configMap to use for the volume
    configMap:
      name: app-config
```

```
kubectl create -f pod-configmap.yaml
```

List the /config directory, where the ConfigMap volume is mounted, in the container. The two ConfigMap keys are listed as files.

```
kubectl exec db -it -- ls /config
```

<figure><img src="../../../.gitbook/assets/image (3) (3) (2).png" alt=""><figcaption></figcaption></figure>

Get the contents of the DB\_NAME file. The file content is the value of the corresponding ConfigMap key-value pair. The && echo is added simply to put the shell prompt onto a new line.

```
kubectl exec db -it -- cat /config/DB_NAME && echo
```

View more examples of creating ConfigMaps by entering

```
kubectl create configmap --help | more
```

<figure><img src="../../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

### Storing and Accessing Sensitive Information Using Kubernetes Secrets <a href="#lab-page-title" id="lab-page-title"></a>

Create a Namespace for the resources you'll create in this lab step and change your default kubectl context to use the Namespace

```
# Create namespace
kubectl create namespace secrets
# Set namespace as the default for the current context
kubectl config set-context $(kubectl config current-context) --namespace=secrets
```

Use kubectl to create a Secret named app-secret. The generic key-value pair Secret is assigned using the --from-literal option with an equal sign separating the key (password) from the value (123457). You can see kubectl create secret generic --help for other methods and examples for creating generic secrets.

```
kubectl create secret generic app-secret --from-literal=password=123457
```

Get the YAML output for the Secret you created. The data field holds all of the key-value pairs. In this case, there is only one. The key password appears as expected, but the value (MTIzNDU3) is far from "123457". That is because secret values are base-64 encoded.

Note: When you use kubectl create secret, the value is automatically encoded. If you use kubectl create -f, and specify a resource file, you need to encode the value yourself when setting the data: mapping. See the next instruction for how to achieve this. Alternatively, you can set a stringData: mapping instead which will perform the encoding for you. See kubectl explain secret for more details about the two options.

```
kubectl get secret app-secret -o yaml
```

<figure><img src="../../../.gitbook/assets/image (11) (3).png" alt=""><figcaption></figcaption></figure>

Confirm the secret value is base-64 encoded by decoding it. The base64 command can encode/decode strings. The --decode option must be specified to decode while the behavior with no options is to encode. The final echo is used to add a new line to the output so the shell prompt is on its own line.

```
kubectl get secret app-secret -o jsonpath="{.data.password}" \
  | base64 --decode \
  && echo
```

<figure><img src="../../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

Create a Pod that uses the Secret through an environment variable. When using a secret through an environment variable, you must include valueFrom.secretKeyRef to specify the source of the environment variable.

#### pod-secret.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-secret
spec:
  containers:
  - image: busybox:1.30.1
    name: busybox
    args:
    - sleep
    - "3600"
    env:
    - name: PASSWORD      # Name of environment variable
      valueFrom:
        secretKeyRef:
          name: app-secret  # Name of secret
          key: password     # Name of secret key
```

```
kubectl create -f pod-secret.yaml
```

Print the value of the environment variable in the Pod's container. Notice that the value is base-64 decoded automatically, so there is no need to use base64 --decode inside the container.

```
kubectl exec pod-secret -- /bin/sh -c 'echo $PASSWORD'
```

<figure><img src="../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>
