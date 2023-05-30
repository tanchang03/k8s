参考：https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner

1. 部署provisioner
```shell
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=172.16.0.111 \
    --set nfs.path=/home/nfs_store
```
执行以上安装以后会创建对应的storageclass，名称为nfs-client
```shell
 > kubectl get sc     
NAME         PROVISIONER                                     RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
nfs-client   cluster.local/nfs-subdir-external-provisioner   Delete          Immediate           true                   3d20h
```


2.创建PVC
```shell
kubectl apply -f ./pvc.yaml
```
动态创建对应的pv和pvc
```shell
 > kubectl get pvc                
NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
nfs-pv-claim   Bound    pvc-14c58feb-e0f2-49f7-8974-8b649d6ed2f9   1Mi        RWX            nfs-client     32s


 > kubectl get pv 
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
pvc-14c58feb-e0f2-49f7-8974-8b649d6ed2f9   1Mi        RWX            Delete           Bound    default/nfs-pv-claim   nfs-client              71s
```