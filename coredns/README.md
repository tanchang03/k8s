## 如何验证coredns

1.使用dnsutils工具
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: dnsutils
  namespace: default
spec:
  containers:
  - name: dnsutils
    image: registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3
    command:
      - sleep
      - "infinity"
    imagePullPolicy: IfNotPresent
  restartPolicy: Always
```

``` shell
kubectl apply -f https://k8s.io/examples/admin/dns/dnsutils.yaml
kubectl get pods dnsutils
kubectl exec -i -t dnsutils -- nslookup kubernetes.default

```


## 检查DNS Pod 是否运行
``` shell
kubectl get pods --namespace=kube-system -l k8s-app=kube-dns
```
