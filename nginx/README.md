# 创建容器部署
``` shell
kubectl apply -f nginx-dep.yml
```

## 如何查看pod日志
```
kubectl logs -f nginx-deployment-774f96d4d9-5gvwk
```

## 删除pod
``` shell
kubectl delete -f ./nginx-dep.yml
``` 

## scale
```
kubectl scale --replicas=1 deploy/nginx-deployment
```