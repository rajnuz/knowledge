# FineBI使用介绍

- [FineBI使用介绍](#finebi使用介绍)
  - [参考文档](#参考文档)
  - [下载安装](#下载安装)
  - [初始设置](#初始设置)
  - [添加自定义driver](#添加自定义driver)
    - [配置 FineDB](#配置-finedb)
    - [添加finedb配置修改模板](#添加finedb配置修改模板)
  - [使用](#使用)
    - [创建连接](#创建连接)
    - [查询](#查询)

---

## 参考文档

[link](https://help.fanruan.com/finebi/doc-view-1679.html)

## 下载安装

参考官方文档安装，要注意需要有JDK环境，jdk大于8u102

在安装过程中会选择分配JVM大小，这里测试过建议分配8G以上，不然在执行某些配置时会卡。

如果初始分配的JVM较小，也可以在安装成功后改。 改法参考：[link](https://help.fanruan.com/finebi/doc-view-56.html)
修改 `${安装目录}\bin\finebi.vmoptions` : -Xms的值
 ![图 1](asset_IMG/%20FineBI%E4%BD%BF%E7%94%A8%E4%BB%8B%E7%BB%8D/IMG_20221107-220000677.png)  ，需要重启FineBI服务器

## 初始设置

初次运行时，会要求填写激活码，这个可以在帆软官网注册账号获取，帆软提供免费试用版本。

启动fineBI服务器会自动打开web服务

初次登录时，需要设置管理员账号，密码。

初次登录会要求选择数据库，这个数据库可以理解为是系统数据库，为了方便可以选择内置数据库，直接登录

![图 2](asset_IMG/%20FineBI%E4%BD%BF%E7%94%A8%E4%BB%8B%E7%BB%8D/IMG_20221107-223352663.png)  

## 添加自定义driver

配置过程参考[link](https://help.fanruan.com/finebi/doc-view-1540.html)。

用户添加自定义的jar作为driver，有以下几点要注意

在管理系统 ->数据链接 ->数据连接管理 ->驱动管理中添加jar
![图 3](asset_IMG/%20FineBI%E4%BD%BF%E7%94%A8%E4%BB%8B%E7%BB%8D/IMG_20221107-224017249.png)  

直接上传jar会有如下报错，需要修改报错中提示的配置

![图 4](asset_IMG/%20FineBI%E4%BD%BF%E7%94%A8%E4%BB%8B%E7%BB%8D/IMG_20221107-224216585.png)  

### 配置 FineDB

配置过程参考[link](https://help.fanruan.com/finebi/doc-view-1080.html)。FineBI内置了HSQL，可以作为FineDB。
这一步如果不做，稍后无法获取配置值

### 添加finedb配置修改模板

从驱动管理的文档中下载 相应模板cpt文件，按照文档中的描述放入目录，然后就可以修改SystemConfig.driverUpload=true

## 使用

### 创建连接

在管理系统- >数据连接 ->数据连接管理中新建数据连接。同一般的数据库工具

### 查询

参考[link](https://help.fanruan.com/finebi/doc-view-887.html)
在数据准备 ->数据列表 新建业务包
![图 5](asset_IMG/%20FineBI%E4%BD%BF%E7%94%A8%E4%BB%8B%E7%BB%8D/IMG_20221107-225355381.png)  

在业务包中可以对已有的连接的表格进行查询

