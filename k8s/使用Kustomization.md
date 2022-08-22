# Kustomization

[toc]

[参考链接](https://kubernetes.io/zh-cn/docs/tasks/manage-kubernetes-objects/kustomization/)

## Kustomization.yaml

使用configMapGenerator 和 secretGenerator生成其他k8s对象需要的ConfigMap和Secret，可以从file或.env或字面量中生成。 name字段指定了configmap或secret的名称可以被其他kubenetes对象使用。新生成的configMap或secets名称都会加上hash值。如果想去掉这个功能，使用`generatorOptions.disableNameSuffixHash:true`

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

generatorOptions:
  disableNameSuffixHash: true

configMapGenerator:
  - name: survey-app-project-values
    namespace: survey-app
    files:
      - values.yaml=values/survey-app-values.yaml


configurations:
  - kustomizeconfig.yaml
```

使用resouce 来组织多个资源

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - demo_apps.yaml
  - project_level_values.yaml
```

## 基准（Bases）与覆盖（Overlays)

基准 是包含 kustomization.yaml 文件的一个目录，其中包含一组资源及其相关的定制。 基准可以是本地目录或者来自远程仓库的目录，只要其中存在 kustomization.yaml 文件即可

覆盖 也是一个目录，其中包含将其他 kustomization 目录当做 bases 来引用的 kustomization.yaml 文件。 换言之，Overlays中主要是替换base中的kustomization.yaml

overlays中的kustomization.yaml

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
bases:
  - ../../../base
```