# Helm 命令

[toc]

## helm 安装

```shell
helm install janus janus --debug --dry-run -n default
```

`--debug`: 可以将install的内容打印出日志
`--dry-run`：不会真正安装而是模拟安装，用于测试helm chart
`-n` 指定namespace