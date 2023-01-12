# helm 实践

- [helm 实践](#helm-实践)
  - [安装mongodb](#安装mongodb)

---

## 安装mongodb

本次安装使用的是bitnami 的mongo helm chart，使用远程仓库中的chart

首先添加bitnami helm repo

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
```

kubernetes中创建一个namespace，我起的名字是mongo

```shell
kubectl create namespace mongo
```

helm install

```shell
helm install my-mongo bitnami/mongodb --version 13.6.2 -n mongo
```

默认生成的用户是root，查看root的密码

``` shell
kubectl get secret --namespace mongo my-mongodb -o jsonpath="{.data.mongodb-root-password}" | base64 -d
```