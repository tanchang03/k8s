无头服务实验
```shell
kubectl apply -f ./pod.yaml
kubectl apply -f ./service.yaml
```

```shell
> kubectl exec -i -t dnsutils -- nslookup  httpd
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   httpd.default.svc.cluster.local
Address: 10.244.146.123
Name:   httpd.default.svc.cluster.local
Address: 10.244.137.38
Name:   httpd.default.svc.cluster.local
Address: 10.244.137.54

```
集群内部某一个pod中执行一下脚本，会发现3个httpd pod交替执行
```shell
curl httpd://httpd

```

```shell
 ~  kubectl logs httpd-deployment-0 -f
[Tue May 30 06:22:53.641620 2023] [mpm_event:notice] [pid 1:tid 140352815406400] AH00489: Apache/2.4.57 (Unix) configured -- resuming normal operations
[Tue May 30 06:22:53.642053 2023] [core:notice] [pid 1:tid 140352815406400] AH00094: Command line: 'httpd -D FOREGROUND'
127.0.0.1 - - [30/May/2023:06:24:04 +0000] "GET / HTTP/1.1" 304 -
127.0.0.1 - - [30/May/2023:06:24:04 +0000] "GET / HTTP/1.1" 304 -
127.0.0.1 - - [30/May/2023:06:24:05 +0000] "GET / HTTP/1.1" 304 -
127.0.0.1 - - [30/May/2023:06:24:06 +0000] "GET / HTTP/1.1" 304 -
127.0.0.1 - - [30/May/2023:06:24:06 +0000] "GET / HTTP/1.1" 304 -
```
```shell
 ~  kubectl logs httpd-deployment-1 -f
[Tue May 30 06:22:58.395072 2023] [mpm_event:notice] [pid 1:tid 140532383169856] AH00489: Apache/2.4.57 (Unix) configured -- resuming normal operations
[Tue May 30 06:22:58.395339 2023] [core:notice] [pid 1:tid 140532383169856] AH00094: Command line: 'httpd -D FOREGROUND'
10.244.146.116 - - [30/May/2023:06:31:25 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:37 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:45 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:52 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:55 +0000] "GET / HTTP/1.1" 200 45
```
```shell
 ~  kubectl logs httpd-deployment-2 -f
[Tue May 30 06:23:02.325079 2023] [mpm_event:notice] [pid 1:tid 140144001080640] AH00489: Apache/2.4.57 (Unix) configured -- resuming normal operations
[Tue May 30 06:23:02.325329 2023] [core:notice] [pid 1:tid 140144001080640] AH00094: Command line: 'httpd -D FOREGROUND'

10.244.146.116 - - [30/May/2023:06:31:31 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:32 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:33 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:33 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:38 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:41 +0000] "GET / HTTP/1.1" 200 45
10.244.146.116 - - [30/May/2023:06:31:42 +0000] "GET / HTTP/1.1" 200 45
```