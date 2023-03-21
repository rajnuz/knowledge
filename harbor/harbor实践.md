# harbor实践


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [harbor实践](#harbor实践)
  - [问题整理](#问题整理)
    - [使用域名访问harbor](#使用域名访问harbor)
    - [insecure security](#insecure-security)
      - [添加harbor证书](#添加harbor证书)
    - [创建kubernetes secret](#创建kubernetes-secret)

<!-- /code_chunk_output -->

## 问题整理

### 使用域名访问harbor

需要在各个节点的`/etc.hosts` 增加harbor的ip和域名的映射。

### insecure security

自建的harbor因为证书问题没法`docker login`，会有下列报错：

```txt
Error response from daemon: Get "https://xxx.com/v2/": x509: certificate signed by unknown authority
或者
Error response from daemon: Get "https://xxx.com/v2/": x509: certificate is valid for ingress.local, not xxx.com
```

需要在`/etc/docker/daemon.json`中加入下列配置：

```json
{
  "insecure-registries" : ["harbor的域名"]
}
```

再重启docker：

```bash
sudo systemctl restart docker
```

#### 添加harbor证书

如果harbor上面配置了自签名证书，首先可以从搭建的harbor的ui上面下载证书。
分别创建如下的路径，并将证书拷贝进去

```bash
mkdir /etc/docker/certs.d/${harbor域名} -p
cp ${cert} /etc/docker/certs.d/${harbor域名}

mkdir ~/.docker/tls/${harbor域名} -p
cp ${cert} /~/.docker/tls/${harbor域名}
```

然后可以docker login

### 创建kubernetes secret

从kubernetes上面拉取镜像，如果需要配置ImagePullSecrets，可以使用如下先创建secret

```bash
kubectl create secret docker-registry ${secret名} \ 
-n ${namespace} \
--docker-server=${harbor域名} \ 
--docker-username=${username} \
--docker-password=${password}

```