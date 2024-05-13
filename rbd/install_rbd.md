# 为k8s上面安装rbd

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [为k8s上面安装rbd](#为k8s上面安装rbd)
  - [创建存储所需的id和秘钥](#创建存储所需的id和秘钥)
  - [安装rbd](#安装rbd)
  - [加载和配置 RBD (RADOS Block Device) 模块](#加载和配置-rbd-rados-block-device-模块)
  - [使用helm安装rbd](#使用helm安装rbd)

<!-- /code_chunk_output -->


为k8s集群配置，安装rbd

## 创建存储所需的id和秘钥

从存储管理员那里注册一个用户名和key用于后续配置rbd的secret
例如：

## 安装rbd

```sh
sudo apt  install ceph-common
```

安装完成可以使用rbd命令

## 加载和配置 RBD (RADOS Block Device) 模块

Linux 系统中加载和配置 RBD (RADOS Block Device) 模块，以便系统可以使用 Ceph 分布式文件系统提供的块存储功能。

```sh
sudo vim /etc/modules
```

添加rbd

确保每次系统启动时自动加载 RBD 模块

```sh
modprobe rbd
```

用于本次不重启系统的情况下也可以加在rbd模块

## 使用helm安装rbd