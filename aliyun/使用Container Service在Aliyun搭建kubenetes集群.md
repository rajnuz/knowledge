# 使用aliyun Container Service

- [使用aliyun Container Service](#使用aliyun-container-service)
  - [创建集群](#创建集群)
  - [增加screts](#增加screts)
  - [连接集群](#连接集群)
    - [绑定ip](#绑定ip)
    - [找到aliyun集群的连接信息](#找到aliyun集群的连接信息)
    - [更改config文件](#更改config文件)
  - [查看aliyun的storage class](#查看aliyun的storage-class)
  - [检查状态](#检查状态)
  - [disk设置](#disk设置)

---

## 创建集群

选择Container Service
![图 1](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221207-194029002.png)  

选择create kubenetes cluster

![图 2](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221207-194426581.png)  

Region 选择China（Beijing）

![图 3](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221207-194513592.png)  

选择vpc vswitch 或者创建新的vswitch 或者vpc

![图 4](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221207-230938653.png)  

继续进入下一页

选择instance

![图 5](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221207-231107219.png)  
并且选择quantity

下一页
![图 6](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221207-231218996.png)  

## 增加screts

增加集群拉取镜像的secrets，可以选择namespace， secrets对选择的namespace生效

![图 7](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221207-231922368.png)  

## 连接集群

通过kubectl连接集群。

使用本机安装有kubectl命令的命令行工具连接aliyun，这里我使用的是wsl，已经安装好了kubectl。

### 绑定ip

初始情况下，集群是local的，需要绑定ip。下图中可以看到初始情况下的ip是192.168.XXX.XXX
![图 9](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-134434304.png)  

到basic information中选择bind EIP，绑定public endpoint

![图 10](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-135010918.png)  

选择带宽等参数

![图 11](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-135148585.png)  

### 找到aliyun集群的连接信息

在Cluster Information connection infomation中找到连接信息，配置在使用kubectl命令行工具的`$Home/.kube/config`位置。这个config文件就是kubectl连接kubernetes集群的连接配置。
注意：这个config文件只能用于连接该集群，如果你有两个集群想连接，需要替换这个config文件
![图 8](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-134336970.png)  

### 更改config文件

```shell
cd ./kube 
```

若已有连接其他集群的config文件，需要备份，因为kubectl将`./kube/config`作为default config文件，（如果希望执行kubectl时 指定config文件，可以用命令 `kubectl --kubeconnfig ${config文件名}`， 例如`kubectl --kubeconnfig .kube/config.bak get pod -A`）

```shell
mv config config.bak
```

然后创建config文件，将之前的连接信息复制进config文件

可以查看验证一下,

```shell
kubectl get pods -A
```

可以看到已经连上之前create的kubernetes集群了

![图 12](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-140609051.png)  

接下来在命令行里找到helm chart 就可以进行helm chart的部署

## 查看aliyun的storage class

```shell
kubectl get sc
```

![图 13](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-154626468.png)  

## 检查状态

部署过程中发现postgresql 有问题，可以在查看pod的状态和错误日志

![图 14](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-155222248.png)  

出现这个问题是，需要一个default storage class。所以需要在volumes->StorageClass中选择一个作为default，或者在values中配置

![图 15](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-155325048.png)  

## disk设置

部署时遇到一个坑，aliyun 的disk最小的capacity因为20Gi，而创建的pvc  capacity只request8Gi，所以删掉重建
在aliyun ui上创建 pvc:

![图 17](asset_IMG/%20%E4%BD%BF%E7%94%A8Container%20Service%E5%9C%A8Aliyun%E6%90%AD%E5%BB%BAkubenetes%E9%9B%86%E7%BE%A4/IMG_20221210-170018670.png)  

