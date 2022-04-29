# J visual VM 使用

[TOC]

##关于 J Visual VM

J visual VM 是jdk自带工具，位置在jdk/bin路径下
打开后可以看到正在跑的application

![图 2](asset_IMG/%20jvisual%20VM/IMG_20220429-153538263.png)  

## 查看进程调用关系和耗时

选择application，点击sampler，将希望调查的包配置进CPU setting

![图 3](asset_IMG/%20jvisual%20VM/IMG_20220429-153912707.png)  

程序运行完点击stop 然后点击snapshot

![图 4](asset_IMG/%20jvisual%20VM/IMG_20220429-154024343.png)  

在snapshot中可以看到每个函数的调用时间

![图 5](asset_IMG/%20jvisual%20VM/IMG_20220429-154118577.png)  
