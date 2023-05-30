参考：https://www.cnblogs.com/hahaha111122222/p/16113462.html

1.create pv & pvc
```shell
kubectl apply -f ./pv.yaml 
kubectl apply -f ./pvc.yaml 

```
2.create secret


```shell
> openssl req -newkey rsa:4096 -nodes -sha256 -keyout ca.key -x509 -days 3650 -out ca.crt
Generating a 4096 bit RSA private key
........................................................................................................++++
............++++
writing new private key to 'ca.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) []:CN
State or Province Name (full name) []:Hubei
Locality Name (eg, city) []:Wuhan
Organization Name (eg, company) []:sds
Organizational Unit Name (eg, section) []:tech
Common Name (eg, fully qualified host name) []:hub.sds.com
Email Address []:tandy0343@gmail.com

> openssl req -newkey rsa:4096 -nodes -sha256 -keyout tls.key -out tls.csr
Generating a 4096 bit RSA private key
.....................................................................................................................................................................................................................++++
.............++++
writing new private key to 'tls.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) []:CN
State or Province Name (full name) []:Hubei
Locality Name (eg, city) []:Wuhan
Organization Name (eg, company) []:sds
Organizational Unit Name (eg, section) []:tech
Common Name (eg, fully qualified host name) []:hub.sds.com
Email Address []:tandy0343@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:

> openssl x509 -req -days 3650 -in tls.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out tls.crt
Signature ok
subject=/C=CN/ST=Hubei/L=Wuhan/O=sds/OU=tech/CN=hub.sds.com/emailAddress=tandy0343@gmail.com
Getting CA Private Key

```

3.install harbor by helm
```shell
helm install harbor ./harbor
```


4.配置客户端-mac docker desktop环境
```shell
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ./ca.crt
```
实验没成功，直接加入了insecure-repositories
```json
{
  "insecure-registries":["hub.sds.com","10.199.253.212:5000"]
}
```


5.注意

- pv对应的nfs目录，需要增加容器用户的可写入的权限，否则各种问题
```shell
[root@k8s-master114 home]# cd nfs_store/
[root@k8s-master114 nfs_store]# ls
data  ddi  ddi_admin  harbor  logs  nats-server.conf  pgdata  prometheus  redisdata  report
[root@k8s-master114 nfs_store]# cd harbor/
[root@k8s-master114 harbor]# ls
chartmuseum  database  jobservice  redis  registry  trivy
[root@k8s-master114 harbor]# ll -lh
总用量 0
drwxr-xr-x 2 root root  6 5月  26 17:17 chartmuseum
drwxrwxrwx 3 root root 20 5月  29 17:39 database
drwxrwxrwx 2 root root 42 5月  30 11:36 jobservice
drwxrwxrwx 2 root root 22 5月  30 11:40 redis
drwxrwxrwx 3 root root 20 5月  30 10:05 registry
drwxrwxrwx 4 root root 34 5月  29 17:39 trivy 
[root@k8s-master114 harbor]# chmod -R a+w ./

```

