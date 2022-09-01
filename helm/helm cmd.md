# Helm 命令

[toc]

## helm 安装

```shell
helm install janus janus --debug --dry-run -n default
```

`--debug`: 可以将install的内容打印出日志
`--dry-run`：不会真正安装而是模拟安装，用于测试helm chart
`-n` 指定namespace
例子中第一个janus是安装后期望的名字，第二个是需要部署的文件夹的名字。
运行这个命令需要在这个chart文件夹外，例如我的我的values.yaml保存在/aaa/janus/路径下，那么我应该在/aaa下面运行helm install

## helm 删除

```shell
helm delete janus -n ${namespace name}
```

这个命令会删除该namespace下面所有基于这个release的内容