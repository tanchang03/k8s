## pod共享存储方案

参考：https://kubernetes.io/zh-cn/docs/tasks/access-application-cluster/communicate-containers-same-pod-shared-volume/

在这个练习中，你会创建一个包含两个容器的 Pod。两个容器共享一个卷用于他们之间的通信。 Pod 的配置文件如下：

```yaml
apiVersion: v1
kind: Pod
metadata:
name: two-containers
spec:

restartPolicy: Never

volumes:
- name: shared-data
  emptyDir: {}

containers:

- name: nginx-container
  image: nginx
  volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html

- name: debian-container
  image: debian
  volumeMounts:
    - name: shared-data
      mountPath: /pod-data
      command: ["/bin/sh"]
      args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]
```
在配置文件中，你可以看到 Pod 有一个共享卷，名为 shared-data。

配置文件中的第一个容器运行了一个 nginx 服务器。共享卷的挂载路径是 /usr/share/nginx/html。 第二个容器是基于 debian 镜像的，有一个 /pod-data 的挂载路径。第二个容器运行了下面的命令然后终止。

echo Hello from the debian container > /pod-data/index.html
注意，第二个容器在 nginx 服务器的根目录下写了 index.html 文件。

创建一个包含两个容器的 Pod：

kubectl apply -f https://k8s.io/examples/pods/two-container-pod.yaml
查看 Pod 和容器的信息：

kubectl get pod two-containers --output=yaml
这是输出的一部分：
```yaml

apiVersion: v1
kind: Pod
metadata:
...
name: two-containers
namespace: default
...
spec:
...
containerStatuses:

- containerID: docker://c1d8abd1 ...
  image: debian
  ...
  lastState:
  terminated:
  ...
  name: debian-container
  ...

- containerID: docker://96c1ff2c5bb ...
  image: nginx
  ...
  name: nginx-container
  ...
  state:
  running:
  ...
```
你可以看到 debian 容器已经被终止了，而 nginx 服务器依然在运行。
进入 nginx 容器的 shell：

```shell
kubectl exec -it two-containers -c nginx-container -- /bin/bash
```
在 shell 中，确认 nginx 还在运行。

```shell
root@two-containers:/# apt-get update
root@two-containers:/# apt-get install curl procps
root@two-containers:/# ps aux
```
输出类似于这样：

USER       PID  ...  STAT START   TIME COMMAND
root         1  ...  Ss   21:12   0:00 nginx: master process nginx -g daemon off;
回忆一下，debian 容器在 nginx 的根目录下创建了 index.html 文件。 使用 curl 向 nginx 服务器发送一个 GET 请求：

root@two-containers:/# curl localhost
输出表示 nginx 向外提供了 debian 容器所写就的页面：

Hello from the debian container
