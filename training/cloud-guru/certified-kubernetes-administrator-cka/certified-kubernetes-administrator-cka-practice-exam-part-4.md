# Certified Kubernetes Administrator (CKA) Practice Exam: Part 4



## Back Up the etcd Data

From the exam server, log in to the `etcd1` server and back up the etcd data to a file located at `/home/cloud_user/etcd_backup.db`.

> **Note:** On the `etcd1` server, you can find certificates which you can use to authenticate with etcd in `/home/cloud_user/etcd-certs`.

**Linux**

```
ETCDCTL_API=3 etcdctl snapshot save /home/cloud_user/etcd_backup.db \
--endpoints=https://etcd1:2379 \
--cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
--cert=/home/cloud_user/etcd-certs/etcd-server.crt \
--key=/home/cloud_user/etcd-certs/etcd-server.key
```

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



## Restore the etcd Data from the Backup

On the `etcd1` server, restore your etcd data from the backup file at `/home/cloud_user/etcd_backup.db`.



**Linux**

```
sudo systemctl stop etcd
rm -rf /var/lib/etcd/
```

**Linux**

```
sudo ETCDCTL_API=3 etcdctl snapshot restore /home/cloud_user/etcd_backup.db \
--initial-cluster etcd-restore=https://etcd1:2380 \
--initial-advertise-peer-urls https://etcd1:2380 \
--name etcd-restore \
--data-dir /var/lib/etcd
```

**Linux**

```
sudo chown -R etcd:etcd /var/lib/etcd
sudo systemctl start etcd
```

**Linux**

```
ETCDCTL_API=3 etcdctl get cluster.name \
--endpoints=https://etcd1:2379 \
--cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
--cert=/home/cloud_user/etcd-certs/etcd-server.crt \
--key=/home/cloud_user/etcd-certs/etcd-server.key
```

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



