# linux 命令

- [linux 命令](#linux-命令)
  - [vi](#vi)
    - [搜索](#搜索)
    - [替换](#替换)
    - [删除swap文件](#删除swap文件)
  - [explorer](#explorer)

---

## vi

### 搜索

```shell
/${搜索的内容}
```

### 替换

```shell
: %s/${需要被替换的内容}/${希望替换后的内容}/g
```

### 删除swap文件

有时vi打开文件时会看到这样的报错，这是因为有该文件的swap存在

![图 1](asset_IMG/%20linux%E5%91%BD%E4%BB%A4/IMG_20221210-161612144.png)  

可以先Abort，然后ls当前目录，删掉提示的.swp文件即可

## explorer

打开当前命令行所在的文件夹

```shell
explorer.exe .
```
