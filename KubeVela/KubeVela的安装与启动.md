# KubeVela 安装与启动

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [KubeVela 安装与启动](#kubevela-安装与启动)
  - [安装vela命令行](#安装vela命令行)
  - [准备集群](#准备集群)
  - [安装KubeVela组件](#安装kubevela组件)
  - [启用KubeVela的UI VelaUX](#启用kubevela的ui-velaux)

<!-- /code_chunk_output -->

参考[官方链接](https://kubevela.io/zh/docs/installation/kubernetes)

## 安装vela命令行

linux环境：

```sh
curl -fsSl https://kubevela.net/script/install.sh | bash
```

## 准备集群

这一步我是采用wsl中创建k3d集群

```sh
k3d cluster create my-cluster
```

## 安装KubeVela组件

```sh
vela install
```

这一步会在集群中创建KubeVela的一些服务

## 启用KubeVela的UI VelaUX

```sh
vela addon enable velaux
```

这一步会创建velaux的一些服务，兵器日志中提示了一些用浏览器访问的方法，具体可以参考官方提供的一些[访问方式](https://kubevela.io/docs/reference/addons/velaux/)，如lb NodePort等形式

在我这个case中，我将使用k3d命令暴露集群服务

```sh
# 将velaux设为NodePort
vela addon enable velaux serviceType=NodePort

#查看暴露的端口，假定暴露的是30000 
kubectl get svc -A

# 暴露k3d集群相应的端口
k3d cluster edit my-cluster --port-add 30000:30000@loadbalancer
```

然后可以通过浏览器访问：localhost:30000, 默认用户名`admin`,密码`VelaUX12345`
