
```shell
kubectl apply -f https://k8s.io/examples/application/mongodb/mongo-deployment.yaml

kubectl get pods
kubectl get deployment
kubectl get replicaset
kubectl apply -f https://k8s.io/examples/application/mongodb/mongo-service.yaml
kubectl get service mongo
# 验证 MongoDB 服务是否运行在 Pod 中并且在监听 27017 端口：
# 将 mongo-75f59d57f4-4nd6q 改为 Pod 的名称
kubectl get pod mongo-75f59d57f4-4nd6q --template='{{(index (index .spec.containers 0).ports 0).containerPort}}{{"\n"}}'
# 27017 是分配给 MongoDB 的互联网 TCP 端口。
```

转发端口
```shell
kubectl port-forward mongo-75f59d57f4-4nd6q 28015:27017
kubectl port-forward pods/mongo-75f59d57f4-4nd6q 28015:27017
kubectl port-forward deployment/mongo 28015:27017
kubectl port-forward replicaset/mongo-75f59d57f4 28015:27017
kubectl port-forward service/mongo 28015:27017
```

验证mongo client
```shell
mongosh --port 28015

test> db.runCommand( { ping: 1 } )

```