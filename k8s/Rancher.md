# Rancher 操作

- [Rancher 操作](#rancher-操作)
  - [为pod添加host](#为pod添加host)

---

## 为pod添加host

当我们需要访问的服务需要配置ip和hostname映射时，可以进入workload，编辑workload

![图 1](asset_IMG/%20Rancher/IMG_20220929-142952430.png)  

在网络下 选择 add host alias, 注意subdomain需要小写，才能保存

![图 2](asset_IMG/%20Rancher/IMG_20220929-143028180.png)  

修改完成保存

