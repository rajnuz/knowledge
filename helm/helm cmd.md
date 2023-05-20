# Helm 命令

- [Helm 命令](#helm-命令)
  - [helm 安装](#helm-安装)
  - [helm 删除](#helm-删除)
  - [helm 更新](#helm-更新)
  - [helm list](#helm-list)

---

## helm 安装

```shell
helm install janus janus --debug --dry-run -n default
```

`--debug`: 可以将install的内容打印出日志
`--dry-run`：不会真正安装而是模拟安装，用于测试helm chart,dry run还提供一个能力，可以将整个helmchart生成的kubectl产物预览，然后使用可以使用kubectl apply手动安装
`-n` 指定namespace
例子中第一个janus是安装后期望helm release的名字，第二个是需要部署的文件夹的名字。例如我的我的Chart.yaml保存在/aaa/janus/路径下（即/aaa/janus/Chart.yaml），那我第二个参数应该是 /aaa/janus/

正常安装后的日志提示

![图 1](asset_IMG/%20helm%20cmd/IMG_20221210-154446110.png)  

## helm 删除

```shell
helm delete janus -n ${namespace name}
```

这个命令会删除该namespace下面所有基于这个release的内容

pvc是不会随着helm delete被删除的，且重新install的时候，已经有同名的pvc了，是不会覆盖的。
例如原本我声明了pvc mongo 他的storageClass是nas。当我将chart中的storageClass声明为其他类型后，重新helm install后，`kubectl get pvc` 可以看到mongo的storageClass还是nas，这种情况需要手动删除pvc，命令:

``` shell
kubectl delete pvc ${pvc名称} -n ${namespace名称}
```

## helm 更新

当我们更新了使用helm部署的一些东西时，例如修改了某个java服务的application.yaml

```shell
helm upgrade --install ${helm release} -n mis ${helm包的位置}
```

--install的作用是，如果没有就install，如果有就替换
helm包的位置可以试官网的，也可以是本地一个路径

helm upgrade命令可以改的范围是有限的，这个命令可以在chart中的yaml等文件改变时起到更新作用，但是有一些内容是不可以通过这个命令更改的。例如修改了 storage class的size。所以这种情况下先helm delete 在helm install

## helm list

查看部署的helm内容

```shell
helm list -n ${namespace}
或
helm ls -n ${namespace}
```
