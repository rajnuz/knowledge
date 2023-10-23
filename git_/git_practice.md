# git practice

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [git practice](#git-practice)
  - [场景1](#场景1)
  - [场景2](#场景2)

<!-- /code_chunk_output -->

## 场景1

现在有Repo A，需要fork A， 在fork的Repo B中进行修改，合并进Repo A

在Repo B中创建feature分支进行代码修改

本地克隆Repo B

此时在本地Repo B查看remote状态：只会显示origin为Repo B，

```sh
git remote -v

# 显示的结果
origin  git@repo-b.git (fetch)
origin  git@repo-b.git (push)
```

我们需要为本地仓库设置upstream

```sh
git remote add upstream git@repo-a.git 

# 显示的结果
origin  git@repo-b.git (fetch)
origin  git@repo-b.git (push)
upstream        git@repo-a.git (fetch)
upstream        git@repo-a.git (push)
```

此时我们在feature分支上进行开发，开发结束后希望将开发的内容提交到Repo A中

我们可以先切换回主分支，拉取upstream（因为在我们开发期间，upstream可能已经被更新）

```sh
git checkout main

git pull upstream
```

此时本地的main并不包含upsteam的内容，我们需要进行合并

```sh
git merge upstream/main main
```

切换到feature分支，尝试rebase（feature分支本来的base是feature分支创建时的commit，rebase会将尝试目标分支最新的状态作为feature的base，在这个场景中，即我们期望将在Repo B中的修改应用到开发期间Repo A的新提交之后）

```sh
git checkout <feature>

git rebase main
```

一般来说会有冲突，vim解决冲突，然后继续rebase

```sh
# 依然要add
git add .

git rebase --continue
```

此时可以提交到origin ，记得仍需要用

```sh
git push origin --force
```

然后我们可以先不着急将Repo B的feature分支合并至Repo B的main，而是发起首先更新origin main的状态，保持云端Repo B的main分支与Repo A的main分支同步。这样我们在发起merge request时，云端会自动显示Repo A的最新commit

```sh
git push origin main
```

Merge Request，尝试将Repo B的feature分支合并进Repo A的main分支。

整个过程中，可以保持Repo B的main分支与Repo A的main分支一直同步

## 场景2

针对同一个功能点，持续提交几次修改

当我们进行了一些修改后，进行首次commit

```sh
git add .

git commit -a -m "dome some change 1"
```

然后我们又做了一些修改，但是和上次修改属于同一个功能，为了避免commit记录过多，可以修改上次的commit

```sh
# 依然要add
git add .

git commit --amend
```

此时会弹出窗口，可以修改上次的commit message。修改好后保存并退出，再次查看commit记录，发现依然只有一个commit

注意如果第一次提交已经push到origin。依然可以通过这个办法修改：

```sh
# 依然要add
git add .

git commit --amend

# 通过--force强制修改origin的内容
git push origin --force
```

此时在origin上查看commit的记录，仍然只有一条commit，但是包含全部修改