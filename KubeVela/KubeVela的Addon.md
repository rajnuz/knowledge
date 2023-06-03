# KubeVela Addon

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [KubeVela Addon](#kubevela-addon)
  - [Application](#application)
    - [如何部署一个Application](#如何部署一个application)
  - [Defination](#defination)
  - [Addon](#addon)
    - [Addon 目录结构](#addon-目录结构)
    - [将现有的k8s对象转为Defination](#将现有的k8s对象转为defination)
    - [使用Addon](#使用addon)

<!-- /code_chunk_output -->

## Application

[官方文档](https://kubevela.io/zh/docs/getting-started/core-concept)

KubeVela的应用部署计划是一种非规范的Application k8s对象 例如

```yaml
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: <name>
spec:
  components:
    - name: <component name>
      type: <component type>
      properties:
        <parameter values>
      traits:
        - type: <trait type>
          properties:
            <traits parameter values>
    - name: <component name>
      type: <component type>
      properties:
        <parameter values>
  policies:
  - name: <policy name>
    type: <policy type>
    properties:
      <policy parameter values>
  workflow:
    - name: <step name>
      type: <step type>
      properties:
        <step parameter values>   
```

在Application部署计划中会引用到已经定义过的[Defination](https://kubevela.io/zh/docs/getting-started/definition/).

这个 Application 对象会引用 component、trait、policy 以及 workflow step 的类型

### 如何部署一个Application

```sh
vela up -f ${Application.yaml}
```

## Defination

Defination有4种类型：Component，Trait， Policy，Workflow
KubeVela已经预置了一些Defination，查看已经安装的Defination:

```sh
vela def list
```

在应用计划中使用Defniation时可以看到在Properties中可以穿一些parameter，这是Defination定义时预留的可供传参的位置 例如下面是一个component:组件的定义, 文件名为 stateless.cue

```txt
stateless: {
    annotations: {}
    attributes: workload: definition: {
        apiVersion: "apps/v1"
        kind:       "Deployment"
    }
    description: ""
    labels: {}
    type: "component"
}

template: {
    output: {
        spec: {
            selector: matchLabels: "app.oam.dev/component": parameter.name
            template: {
                metadata: labels: "app.oam.dev/component": parameter.name
                spec: containers: [{
                    name:  parameter.name
                    image: parameter.image
                }]
            }
        }
        apiVersion: "apps/v1"
        kind:       "Deployment"
    }
    outputs: {}
    parameter: {
    name: string
    image: string
  }
}
```

校验这个cue文件是否有格式错误：

```sh
vela def vet stateless.cue
```

将这个ComponentDefinition停用到KubeVela:

```sh
vbela def apply stateless。cue
```

查看这个Defination，可以了解他有哪些参数:

```sh
vela show stateless
```

输出结果：

```txt
# Specification
+-------+-------------+--------+----------+---------+
| NAME  | DESCRIPTION |  TYPE  | REQUIRED | DEFAULT |
+-------+-------------+--------+----------+---------+
| name  |             | string | true     |         |
| image |             | string | true     |         |
+-------+-------------+--------+----------+---------+
```

## Addon

插件都包含一组模块定义以及支撑其功能的 CRD Controller, 插件提供了另一种部署应用的方式。遵循插件的目录和文件规范可以在启用插件时，渲染出一个Application部署计划。

### Addon 目录结构

[官方文档](https://kubevela.io/zh/docs/platform-engineers/addon/intro/)
addon 标准的目录结构是:

```txt
├── resources/
│   ├── xxx.cue
│   └── xxx.yaml
├── definitions/
├── schemas/
├── README.md
├── metadata.yaml
├── parameter.cue
└── template.yaml
```

使用

```sh
vela addon init
```

初始化该结构

我们可以基于helm构建vela从插件结构, 例如将mongo chart转为插件：

```sh
vela addon init mongodb --helm-repo https://marketplace.azurecr.io/helm/v1/repo --chart mongodb --chart-version 12.1.16
```

我们也可以使用这个命令构建一个空的目录结构，来从头创建插件：

```sh
vela addon init my-addon --no-sample
```

### 将现有的k8s对象转为Defination

在addon里比较好的做法是使用cue文件来描述需要创建的Defination等内容。如果我已有一个k8s Deployment.yaml，可以使用vela将其转换为cue文件，然后只需要稍作修改即可：

例如我有一个Deplyment stateless.yaml为

```yaml
apiVersion: "apps/v1"
kind: "Deployment"
spec:
  selector:
    matchLabels:
      "app.oam.dev/component": "name"
  template:
    metadata:
      labels:
        "app.oam.dev/component": "name"
    spec:
      containers: 
      - name: "name"
        image: "image"
```

转换cue的命令：在命令中-t表名我希望将这个yaml转为哪种Defination

```sh
vela def init stateless -t component --template-yaml ./stateless.yaml -o stateless.cue
```

得到的stateless.cue

```txt
stateless: {
    annotations: {}
    attributes: workload: definition: {
        apiVersion: "<change me> apps/v1"
        kind:       "<change me> Deployment"
    }
    description: ""
    labels: {}
    type: "component"
}

template: {
    output: {
        spec: {
            selector: matchLabels: "app.oam.dev/component": "name"
            template: {
                metadata: labels: "app.oam.dev/component": "name"
                spec: containers: [{
                    name:  "name"
                    image: "image"
                }]
            }
        }
        apiVersion: "apps/v1"
        kind:       "Deployment"
    }
    outputs: {}
    parameter: {}
}
```

校验cue文件格式

```sh
cue vet ${cue file}
```
或

```sh
vela vet ${cue file}
```

### 使用Addon

我们可以使用vela命令来启用Addon。这也相当于部署了Application:

```sh
vela addon enable ${addon name} 
```

启用本地addon

```sh
vela addon enable ./your-addon-dir/
```

校验addon

```sh
vela addon enable ${addon name} --dry-run
```

启用addon时，会将部署resource/路径下的文件。所以我们可以之前转换的cue文件放到`resource/` 路径下，并且修改`template.cue`引用这些资源。parametrer.cue中可以为其赋值

注意， 当执行`vela addon enbale`时,`defination/`路径下的文件会执行`vela def apply`命令添加新的Defination。使用这些Defination的办法是在Application.yaml中引用, parameter直接在Application.yaml的properties中传入。

