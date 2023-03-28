# 创建容器部署
```shell
kubectl apply -f nginx-dep.yml
```

## 如何查看pod日志
```shell
kubectl logs -f nginx-deployment-774f96d4d9-5gvwk
```

## 删除pod
```shell
kubectl delete -f ./nginx-dep.yml
``` 

## scale
```shell
kubectl scale --replicas=1 deploy/nginx-deployment
```

## 进入容器
```shell
kubectl exec -it nginx-deployment-774f96d4d9-5t7n6 /bin/sh
```