## k8s存储持久化（nfs用例）

参考：
https://blog.51cto.com/u_15064627/4251683


## 实验环境:
- node01: 172.16.0.111 master
- node02: 172.16.0.112
- node03: 172.16.0.113

## 安装nfs

所有节点安装nfs-utils
```shell
mkdir -p /home/nfs_store
yum -y install nfs-utils
systemctl start rpcbind
```

在node01存储节点执行：
```shell
touch /etc/exports
cat << EOF >/etc/exports
/home/nfs_store 172.16.0.0/24(rw,sync,no_root_squash) 
EOF
systemctl restart nfs
```

在所有服务器上执行以下脚本绑定nfs目录

```shell
mount -t nfs 172.16.0.111:/home/nfs_store /home/nfs_store
#自动挂在
cat << EOF >> /etc/fstab
172.16.0.111:/home/nfs_store /home/nfs_store nfs defaults 0 0
EOF
```


## 安装pv pvc
```shell
> kubectl apply -f ./pv-demo.yaml 
> kubectl get pv
# 观察pv状态
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv001   2Gi        RWO,RWX        Recycle          Available                                   7s

> kubectl apply -f ./pvc-demo.yaml
NAME    STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
mypvc   Bound    pv001    2Gi        RWO,RWX                       4s

# 再次观察pv状态
> kubectl get pv
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM           STORAGECLASS   REASON   AGE
pv001   2Gi        RWO,RWX        Recycle          Released   default/mypvc                           2m48s

```

## 使用pvc
```shell
> kubectl apply -f ./pod-demo.yaml
# 验证pvc mount状态
> kubectl exec -it vol-pvc -- df -h
Filesystem                    Size  Used Avail Use% Mounted on
overlay                        26G  6.2G   20G  24% /
tmpfs                          64M     0   64M   0% /dev
tmpfs                         3.9G     0  3.9G   0% /sys/fs/cgroup
shm                            64M     0   64M   0% /dev/shm
/dev/mapper/centos-root        26G  6.2G   20G  24% /etc/hosts
172.16.0.111:/home/nfs_store   26G  6.0G   21G  23% /usr/share/nginx/html
tmpfs                         7.6G   12K  7.6G   1% /run/secrets/kubernetes.io/serviceaccount
tmpfs                         3.9G     0  3.9G   0% /proc/acpi
tmpfs                         3.9G     0  3.9G   0% /proc/scsi
tmpfs                         3.9G     0  3.9G   0% /sys/firmware

# 测试
> kubectl exec -it vol-pvc -- curl 127.0.0.1
<html>
<body>
  hello world
</body>
</html>

```
