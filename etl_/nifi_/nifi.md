# nifi

[toc]

## 文档

[中文文档](https://nifichina.gitee.io/)
[英文文档](https://nifi.apache.org/docs.html)

## 安装

下载[link](https://nifi.apache.org/download.html)
可以下载免安装版本，解压即用
安装步骤参考 [link](https://nifichina.gitee.io/1-%E5%9F%BA%E7%A1%80%E6%96%87%E6%A1%A3/2-GettingStarted.html#%E5%90%AF%E5%8A%A8nifi)
安装好启动后会开启web服务，访问本机8080或8443即可

## 集群

采用Zero-Master Clustering集群模式，集群中的每个节点对数据执行相同的任务，但每个节点都在不同的数据集上运行。其中一个节点自动选择（通过Apache ZooKeeper）作为集群协调器

## 亮点

数据缓冲，数据优先级，可视化编程，模板复用，节点间数据可加密，访问授权，集群扩展

## 认证

文档 [link](https://nifichina.gitee.io/1-%E5%9F%BA%E7%A1%80%E6%96%87%E6%A1%A3/6-AdminGuide.html#%E7%94%A8%E6%88%B7%E8%AE%A4%E8%AF%81-user-authentication)
NiFi支持通过客户端证书，用户名/密码，Apache Knox或OpenId Connect (opens new window)进行用户身份验证
支持多租户，不同级别的授权

## 扩展

[定制化processor和controller service例子](https://medium.com/hashmapinc/creating-custom-processors-and-controllers-in-apache-nifi-e14148740ea)

## 使用

nifi的使用实际上就是processor的组合，用户定义processor之间的连接关系(relationship)，通过定义的规则来路由flowfile
FlowFile：每条"用户数据"（即用户带进NiFi的需要进行处理和分发的数据）称为FlowFile。FlowFile由两部分组成：Attributes 和 Content。Content是用户数据本身。Attributes是与用户数据关联的键值对

### 增加数据源

当需要在某个流程中配置数据源，例如需要为一个processor配置一个jdbc连接池
![图 3](asset_IMG/%20nifi/IMG_20221128-170835392.png)  

jdbc连接池在nifi中也属于一种ControllerService，可以在ControllerService管理页面看到，例如：
![图 2](asset_IMG/%20nifi/IMG_20221128-170819611.png)  

连接池参数，需要注意添加driver的地址配置
![图 1](asset_IMG/%20nifi/IMG_20221128-170652082.png)  

注意数据源连接需要在enable状态下工作（在controller service界面点击闪电图标enable）

### 增加处理器

nifi不同的处理器有不同的能力和配置，但是都需要为这些处理器处理好他所需要的relationship，即定义处理器各种情况下下一步的数据流向，relation可以连到下一个processor，port或者在processor自己设置中勾选

### 增加连接

![图 4](asset_IMG/%20nifi/IMG_20221128-171747755.png)  
连接其实是两个processor之间的数据缓冲区，维护了一个队列，在连接的配置中，可以设置背压值控制上下有处理速度，设置队列数据的优先级，设置队列过期时间（0为不过期）

### 查看数据血缘关系

如图选择某个processor或者整个视图的flow之间的关系，点击view data provenance或 右侧菜单的data provenance

![图 5](asset_IMG/%20nifi/IMG_20221128-192617209.png)  



### 处理器分类

**数据转换** 