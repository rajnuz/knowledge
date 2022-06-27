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