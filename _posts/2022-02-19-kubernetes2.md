---
layout: post
title: kubernetes之dashboard部署
date: 2022-02-19
tags: kubernetes
---

## 一: dashboard:

部署kubernetes的web管理界面dashboard

https://github.com/kubernetes/dashboard



### 1.1: 部署dashboard:

![](D:\boke\images\posts\02_k8s\01\1.png)

![](D:\boke\images\posts\02_k8s\01\2.png)

```
root@k8s-master1:~# wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.3.0/aio/deploy/recommended.yamlls

#添加如下配置：
vim recommended.yaml
```

![](D:\boke\images\posts\02_k8s\01\3.png)

```
root@k8s-master1:~# cat admin-user.yaml 
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard

---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard

root@k8s-master1:~# kubectl apply -f recommended.yaml
root@k8s-master1:~# kubectl apply -f admin-user.yaml
```

![](D:\boke\images\posts\02_k8s\01\4.png)

### 1.1.2: tocken登录dashboard:

```
root@k8s-master1:~# kubectl get secret -A | grep admin
kubernetes-dashboard   admin-user-token-9sxcb                           kubernetes.io/service-account-token   3      11s
root@k8s-master1:~# kubectl -n kubernetes-dashboard describe secret admin-user-token-9sxcb
Name:         admin-user-token-9sxcb
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 25b40ed3-5cd1-4474-a16f-4b3cbac933d1

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6InJNSVBzV3lvb0hodHRJemN4UGgxX0U2VThYTzJLZ3puLXZxdDg1ZWIxRkEifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLTlzeGNiIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNWI0MGVkMy01Y2QxLTQ0NzQtYTE2Zi00YjNjYmFjOTMzZDEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.TmvP13iwyZhmqHSsiD-qyQc6a673xCivq4FLTLvYugxHu7pJN-4uL0updipIf4S2Enz-2QMG6x4QQU8kRsKHjhXY-_0KHPYTne814jSOwJrKpyifPAfKPX1cOCnCI_bsPfULn2OUmpZa6V08RHK6aW3IlbjCyweNP-HitCFiM85Ia8nX8ygZNTx35Oq8tbrHD4NeLhaKQk9f_PHtT_zqVGUTWKDOHeoPPmfR6AHTXgPyoN6BNeGoqDkwlF7lfADRjsCBzP9h9mpPHSsGgTfMkrejh2W5LldTiADd2hdCNpzCwQzAv5p_i0XfbJo7-7-NJfW9WIaj5H9V_ZaRV9SCHA
ca.crt:     1350 bytes
namespace:  20 bytes


```

![](D:\boke\images\posts\02_k8s\01\5.png)

![](D:\boke\images\posts\02_k8s\01\6.png)

### 1.1.3: 这是token登录会话保持时间

```
  root@k8s-master1:~# vim recommended.yaml
  spec:
      containers:
        - name: kubernetes-dashboard
          image: kubernetesui/dashboard:v2.3.0
          imagePullPolicy: Always
          ports:
            - containerPort: 8443
              protocol: TCP
          args:
            - --auto-generate-certificates
            - --namespace=kubernetes-dashboard
            - --token-ttl=43200
  root@k8s-master1:~# kubectl apply -f recommended.yaml
```

### 1.2.1: kuboard

kuboard-kubernetes多集群管理界面

```
docker run -d --restart=unless-stopped --name=kuboard -p 80:80/tcp -p 10081:10081/tcp -e KUBOARD_ENDPOINT="http://192.168.48.145:80" -e KUBOARD_AGENT_SERVER_TCP_PORT="10081" -v /root/kuboard-data:/data swr.cn-east-2.myhuaweicloud.com/kuboard/kuboard:v3


在浏览器输入ip：80 既可以访问kuboard的界面登录方式：
用户名：admin
密码：Kuboard123
```

![](D:\boke\images\posts\02_k8s\01\7.png)



![](D:\boke\images\posts\02_k8s\01\8.png)

![](D:\boke\images\posts\02_k8s\01\9.png)



```
#在mster服务器上执行以上命令，把里面内容复制过来
root@k8s-master1:~# cat ~/.kube/config
```

![](D:\boke\images\posts\02_k8s\01\10.png)

![](D:\boke\images\posts\02_k8s\01\11.png)

![](D:\boke\images\posts\02_k8s\01\12.png)


