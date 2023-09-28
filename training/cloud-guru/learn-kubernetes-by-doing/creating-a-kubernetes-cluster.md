# Creating a Kubernetes Cluster

## Install Docker and Kubernetes on All Servers

### Nodes

**Once we have logged in, we need to elevate privileges using `sudo`:**

```
sudo su
```

**Disable SELinux:**

```
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```

**Enable the `br_netfilter` module for cluster communication:**

```
modprobe br_netfilter 
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

**Ensure that the Docker dependencies are satisfied:**

```
yum install -y yum-utils device-mapper-persistent-data lvm2
```

**Add the Docker repo and install Docker:**

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 
yum install -y docker-ce
```

**Set the cgroup driver for Docker to systemd, reload systemd, then enable and start Docker:**

```
sed -i '/^ExecStart/ s/$/ --exec-opt native.cgroupdriver=systemd/' /usr/lib/systemd/system/docker.service
systemctl daemon-reload
systemctl enable docker --now
```

**Add the Kubernetes repo:**

```
cat << EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
  https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

**Install Kubernetes `v1.14.0`:**

```
yum install -y kubelet-1.14.0-0 kubeadm-1.14.0-0 kubectl-1.14.0-0 kubernetes-cni-0.7.5
```

**Enable the `kubelet` service. The `kubelet` service will fail to start until the cluster is initialized, this is expected:**

```
systemctl enable kubelet
```

### Master

**Once we have logged in, we need to elevate privileges using `sudo`:**

```
sudo su
```

**Disable SELinux:**

```
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```

**Enable the `br_netfilter` module for cluster communication:**

```
modprobe br_netfilter 
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

**Ensure that the Docker dependencies are satisfied:**

```
yum install -y yum-utils device-mapper-persistent-data lvm2
```

**Add the Docker repo and install Docker:**

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 
yum install -y docker-ce
```

**Set the cgroup driver for Docker to systemd, reload systemd, then enable and start Docker:**

```
sed -i '/^ExecStart/ s/$/ --exec-opt native.cgroupdriver=systemd/' /usr/lib/systemd/system/docker.service
systemctl daemon-reload
systemctl enable docker --now
```

**Add the Kubernetes repo:**

```
cat << EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
  https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

**Install Kubernetes `v1.14.0`:**

```
yum install -y kubelet-1.14.0-0 kubeadm-1.14.0-0 kubectl-1.14.0-0 kubernetes-cni-0.7.5
```

**Enable the `kubelet` service. The `kubelet` service will fail to start until the cluster is initialized, this is expected:**

```
systemctl enable kubelet
```

**Initialize the cluster using the IP range for Flannel:**

```
kubeadm init --pod-network-cidr=10.244.0.0/16
```

**Copy the `kubeadmn join` command that is in the output. We will need this later.**

<figure><img src="../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

### Nodes

Login back to the node and run the kubeadm command from master

```
kubeadm join 10.0.1.100:6443 --token 8j9ng5.9dmce3s6rnhyx6ez \
    --discovery-token-ca-cert-hash sha256:48adc8e2ae7e83d92ec143f1720c4652da7c4703f0ca0197bf9d7a3bf6b5210b
```

### Master

**Exit `sudo`, copy the `admin.conf` to your home directory, and take ownership.**

```
exit 
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**Deploy Flannel:**

```
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel-old.yaml
```

&#x20;**Check the cluster state:**

```
kubectl get pods --all-namespaces
kubectl get nodes
```

<figure><img src="../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

## Create and Scale a Deployment Using kubectl

### Master

**Create a simple deployment:**

```
kubectl create deployment nginx --image=nginx
```

**Inspect the pod:**

```
kubectl get pods
```

**Scale the deployment:**

```
kubectl scale deployment nginx --replicas=4
```

**Inspect the pods. We should have four now:**

```
kubectl get pods
```

<figure><img src="../../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

























