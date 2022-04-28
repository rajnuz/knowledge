# k8s 命令

[TOC]

## 查看容器

```shell
kubectl get po -n ${namespace}
```

## 描述容器

```shell
kubectl describe po ${po name} -n ${namespace}
```

## 查看部署应用

```shell
kubectl get deployment.app -n ${namespace}
```

## 删除部署应用

```shell
kubectl delete deployment.app -n ${namespace}
```

## 拷出pod中的文件

```shell
kubectl cp -n ${namespace} ${pod name}:${文件在pod中的绝对路径} ${宿主机文件存储路径/文件名}
```

## 通过端口快速找到服务

```shell
kubectl get services --all-namespaces | grep ${port number}
```

## 查看namespace

```shell
kubectl get ns
```

## 进入pod

``` shell
kubectgl exec -it ${pod name} -n ${namespace} bash
```

## 删掉pod

```shell
kubectl delete po ${pod name}
```

## 导出k8s中clickhouse数据库的数据 存为.csv

进入clickhouse pod

```shell
clickhouse-client -h 127.0.0.1 --database="${db name}" --query="select * from ${db name}.${table name} FORMAT CSV" > ${导出的文件路径及名称} 
```
