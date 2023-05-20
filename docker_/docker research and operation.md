# docker研究和问题整理

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [docker研究和问题整理](#docker研究和问题整理)
  - [问题](#问题)
    - [1. docker nginx 负责端口转发容器启动异常](#1-docker-nginx-负责端口转发容器启动异常)

<!-- /code_chunk_output -->

## 问题

### 1. docker nginx 负责端口转发容器启动异常

发现该问题的场景时，我们规划了一台VM（ip：140.231.89.106）作为集群的Load Balancer，在该VM上使用docker nginx配置端口转发。集群服务为NodePort类型，因此通过nginx的配置，可以实现外部主机通过访问140.231.89.106某个端口访问集群中的服务。
默认LB开启的端口为30000~32767，docker启动的时候会为每个端口都分配docker-proxy，可以参考网上其他开发者的[分享](https://blog.csdn.net/m0_45406092/article/details/105913959)

在这种情况下，会导致容器启动后会有多个docker-proxy相关的进程，使用`ps -aux | grep docker-proxy`可以查看到。这个会消耗系统资源

解决办法可以为`/etc/docker/daemon.json` 添加 "userland-proxy":false, '
并重启docker `docker`:
`sudo systmectl restart docker`