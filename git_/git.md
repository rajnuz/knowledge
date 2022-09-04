# git

[toc]
[git 指导链接](https://git-scm.com/book/zh/v2)

[在idea中使用git](../idea_/git%20in%20idea.md)

## branch

将本分支关联到远程分支

```shell
git branch --set-upstream-to=origin/${远程分支名} ${本地分支名}
```

删除本地分支

```shell
git branch -d ${本地分支名}
```

强制删除本地分支
```shell
git branch -D ${本地分支名}
```

## pull

拉取远程代码，--rebase的效果本质上是会将本地开发的commit附加到远程分支在你开发期间新提交的commit后面，从而产生一条顺序易读的commit，同时也不会自动产生merge commit。但是注意**最好不要在公共分支进行rebase**。 详情见
[rebase产生的事故](https://juejin.cn/post/6891822489194889230)

```shell
git pull origin ${远程分支名} --rebase
```

直接pull 远程分支到本地当前分支：会自动产生一个merge commit

```shell
git pull origin ${远程分支名}
```

## push

将打了tag的文件推到远程

```shell
git push origin refs/tags/${tag名}
```

删除远程分支

```shell
git push origin --delete ${远程分支名}
```

本地新建的分支，希望将改动提交到远程某个分支上，但是不将两个分支进行关联

```shell
git push origin ${本地分支名}:${远程分支名}
```

本地新建分支，希望将改动提交到远程代码仓库（即以本地分支名为名称在远程仓库创建一个同名分支，并关联两者）,（如果远程已存在这个名称的代码，则不会新创建而是直接提交）

```shell
git push origin  --set-upstream ${本地分支名}
```

## stash

无需commit，将本地改动的文件储藏起来，文件改动会暂时小时，idea中文件名从蓝变白

```shell
git stash
```

将stash中储藏的改动从新暴露出来，idea中文件名由白变蓝

```shell
git stash pop
```

## add

一次性将所有改动添加

```shell
git add .
```

## commit

```shell
git commit -a -m "${commit的消息}"
```

## reset

当add了不需要的文件需要撤销

```shell
git reset HEAD ${文件名}
```

## tag

给本地代码打tag

```shell
git tag ${tag名称}
```

列出所有tag

```shell
git tag
```

## 同步fork原仓库

当我们fork了原仓库后，同步原仓库新的一些提交时：

### 在本地查看当前项目remote信息

```shell
git remote -v
```

### 添加upstream

结果中如果只有origin 的fetch 和 pull 两条记录，说明还未设置upstream
可以设置远程upstream至fork的原仓库

```shell
git remove -add upstream ${fork的原远程git}
```

这一步的结果是看到了使用`git remote -v`可以看到了upstream的pull 和fetch

### 抓取原仓库

```shell
git fetch upstream
```

这一步的结果看到log中显示fork的原仓库的新增branch

### 合并

切换到本地想要合并的分支，合并upstream拉取的分支:

```shell
git merge upstream/${fork 的仓库中的branch名字}
```
