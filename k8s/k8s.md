# k8s 命令

- [k8s 命令](#k8s-命令)
  - [官方文档](#官方文档)
  - [查看容器](#查看容器)
  - [描述容器](#描述容器)
  - [查看部署应用](#查看部署应用)
  - [删除部署应用](#删除部署应用)
  - [拷出pod中的文件](#拷出pod中的文件)
  - [通过端口快速找到服务](#通过端口快速找到服务)
  - [查看namespace](#查看namespace)
  - [进入pod](#进入pod)
  - [删掉pod](#删掉pod)
  - [导出k8s中clickhouse数据库的数据 存为.csv](#导出k8s中clickhouse数据库的数据-存为csv)
  - [从容器中看java 镜像的环境变量](#从容器中看java-镜像的环境变量)
  - [查询一个namespace下面的资源消耗](#查询一个namespace下面的资源消耗)
  - [查询各个worker的资源](#查询各个worker的资源)
  - [查询各个worker 各个pod的资源](#查询各个worker-各个pod的资源)
  - [查询某个服务的资源](#查询某个服务的资源)
  - [修改k8s当前context的namespace（使用场景不多](#修改k8s当前context的namespace使用场景不多)
  - [查看pod日志](#查看pod日志)
  - [删除pvc](#删除pvc)
  - [以yaml形式查看资源的文件](#以yaml形式查看资源的文件)

---

## 官方文档

[link](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

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
kubectl exec -it ${pod name} -n ${namespace} bash
```

## 删掉pod

```shell
kubectl delete po ${pod name} -n ${namespace}
```

## 导出k8s中clickhouse数据库的数据 存为.csv

进入clickhouse pod

```shell
clickhouse-client -h 127.0.0.1 --database="${db name}" --query="select * from ${db name}.${table name} FORMAT CSV" > ${导出的文件路径及名称} 
```

## 从容器中看java 镜像的环境变量

当我们在Dockerfile里写了环境变量时：

```docker
ENV APP_NAME="@project.artifactId@"
```

如何检查容器中环境变量名是否正确呢：

在k8s中进入容器shell，然后输入

```shell
echo $APP_NAME
```

## 查询一个namespace下面的资源消耗

```shell
kubectl resource-capacity --util --pods | grep ${namespace}
```

结果展示
![图 1](asset_IMG/%20k8s/IMG_20220627-141149624.png)  

6列分别是：cpu request， cpu limit，cpu usage，memory request， memory limit，memory usage. 每行都是一个pod

## 查询各个worker的资源

```shell
kubectl resource-capacity --util
```

![图 2](asset_IMG/%20k8s/IMG_20220627-141628584.png)  

## 查询各个worker 各个pod的资源

```shell
kubectl resource-capacity --util --pods
```

## 查询某个服务的资源

```shell
kubectl resource-capacity --util --pods | grep ${服务名}
```

## 修改k8s当前context的namespace（使用场景不多

```shell
kubectl config set-context --current --namespace=${namespace}
```

## 查看pod日志

```shell
kubectl logs ${pod name} -n ${namespace name} -f
```

`-f`的作用是持续输出日志

## 删除pvc

``` shell
kubectl delete pvc ${pvc名称} -n ${namespace名称}
```

## 以yaml形式查看资源的文件

```shell
查看statefulset
kubectl get statefulset ${statefulset 名字} -n ${namespace} -o yaml

查看pod
kubectl get pod ${pod 名字} -n ${namespace} -o yaml
```