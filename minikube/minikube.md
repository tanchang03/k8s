## 相关命令
```shell
minikube delete
minikube start
minikube config 
```

## 解决macos无法访问service NodePort的问题
通过service tunnel访问service
```shell
minikube service nginx_service 
```

## 镜像下载失败如何排查问题
通过ssh登录到minikube环境中,执行docker ps & docker image 
```shell
minikube ssh
```

