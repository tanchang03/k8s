参考：https://kubernetes.github.io/ingress-nginx/deploy/#quick-start

1.使用helm安装ingress-nginx
```shell
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
  
```
2.编辑service改为NodePort
```shell
> kubectl edit service ingress-nginx-controller -n ingress-nginx

 46   selector:
 47     app.kubernetes.io/component: controller
 48     app.kubernetes.io/instance: ingress-nginx
 49     app.kubernetes.io/name: ingress-nginx
 50   sessionAffinity: None
 51   type: NodePort
```

3.修改kube-apiserver，允许NodePort使用80 443端口
```shell
> vim /etc/kubernetes/manifests/kube-apiserver.yaml
## 增加kube
spec:
  containers:
  - command:
    - kube-apiserver
    ... ## 增加命令行参数
    - --service-node-port-range=1-65535
```
修改文件后，kubelet自动重启api-server

4.修改service的映射端口为80 443
```shell
> kubectl edit service ingress-nginx-controller -n ingress-nginx
spec:
  clusterIP: 10.107.129.248
  clusterIPs:
  - 10.107.129.248
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - appProtocol: http
    name: http
    nodePort: 80 #修改端口为80
    port: 80
    protocol: TCP
    targetPort: http
  - appProtocol: https
    name: https
    nodePort: 443  # 修改端口为443 
    port: 443
    protocol: TCP
    targetPort: https
```



