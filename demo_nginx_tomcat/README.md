## k8s部署msyql

1.创建deployment
``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  # 指定Deployment的名称
  name: mysql-deployment
  # 指定Deployment的标签 
  labels:
    app: mysql
spec:
  # 指定创建的Pod副本数量 
  replicas: 1
  # 定义如何查找要管理的Pod
  selector:
    # 管理标签app为mysql的Pod
    matchLabels:
      app: mysql
  # 指定创建Pod的模板
  template:
    metadata:
      # 给Pod打上app:mysql标签
      labels:
        app: mysql
    # Pod的模板规约
    spec:
      containers:
        - name: mysql
          # 指定容器镜像
          image: mysql:8.0
          # 指定开放的端口
          ports:
            - containerPort: 3306
          # 设置环境变量
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: root
          # 使用存储卷
          volumeMounts:
            # 将存储卷挂载到容器内部路径
            - mountPath: /var/log/mysql
              name: log-volume
            - mountPath: /var/lib/mysql
              name: data-volume
            - mountPath: /etc/mysql/conf.d
              name: conf-volume
      # 定义存储卷
      volumes:
        - name: log-volume
          # hostPath类型存储卷在宿主机上的路径
          hostPath:
            path: /Users/tandy/work/IDEA_WORKSPACE_LAB/k8s/demo_nginx_tomcat/mysql/log
            # 当目录不存在时创建
            type: DirectoryOrCreate
        - name: data-volume
          hostPath:
            path: /Users/tandy/work/IDEA_WORKSPACE_LAB/k8s/demo_nginx_tomcat/mysql/data
            type: DirectoryOrCreate
        - name: conf-volume
          hostPath:
            path: /Users/tandy/work/IDEA_WORKSPACE_LAB/k8s/demo_nginx_tomcat/mysql/conf.d
            type: DirectoryOrCreate
```

``` shell
kubectl apply -f ./mysql-deployment.yaml
```

> 在macos环境中由于使用了m1芯片，无法使用amd64架构的容器，所以不能使用mysql.5.7的镜像,改用了8.0的镜像

2.如果外部访问，需要创建service
``` yml
apiVersion: v1
kind: Service
metadata:
  # 定义服务名称，其他Pod可以通过服务名称作为域名进行访问
  name: mysql-service
spec:
  # 指定服务类型，通过Node上的静态端口暴露服务
  type: NodePort
  # 管理标签app为mysql的Pod
  selector:
    app: mysql
  ports:
    - name: http
      protocol: TCP
      port: 3306
      targetPort: 3306
      # Node上的静态端口
      nodePort: 30306
```

``` shell
kubectl apply -f ./mysql-service.yaml
```

3.使用工具访问mysql
- mysql 8.0需要配置2个连接属性
  ``` config
    allowPublicKeyRetrieval=True
    useSSL=false
  ```

## 常用脚本
``` shell
kubectl get pods  -o wide
kubectl get service -o wide
kubectl logs mysql-deployment-657d6d9686-sjpmv -f --tail=100
```



