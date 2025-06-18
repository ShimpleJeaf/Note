# vpn

需要在git中设置代理

# revert 还原

**还原**用于撤销某个特定提交的更改，而不更改提交历史。与重置不同的是，**还原不会删除提交记录**，而是创建一个新的提交，表示撤销了某个特定的更改。它在协作开发中更安全，因为不会更改历史记录。

# reset 重置

会更改历史记录，特别是 `reset --hard` 是不可逆的，因此在多人协作中应慎用。

* --soft    仅重置提交记录，保留工作区的修改。适合想要重新提交的情况。

* --mixed    head、index更改，但是本地代码不变，重置到版本后的所有变更作为本地修改保存在工作区中

* --hard    head、index、工作区代码全部回滚到指定版本

# reflog

所有提交的操作记录，如果不小心回滚了代码，可以在这里找回

修改保存时间：

```bash
git reflog expire --expire-unreachable=180.days --all
```

# count-objects

* -v 详细信息

* -H human-readable大小表示

# gc

git gc 是指“垃圾回收（garbage collection）”，用于清理和优化 Git 仓库中的垃圾对象。通过执行 git gc，Git 会将不再需要的对象从仓库中清理出去，以保持仓库的整洁和性能的良好表现。

# prune

执行 git prune 会删除仓库中不再需要的垃圾对象。与 git gc 不同的是，git prune 只进行单纯的清理操作，不对存储进行优化。prune 可以轻松删除仅存在于本地的但没有在远程仓库中引用的分支。

# fecth和pull

`git fetch`是将远程主机的最新内容拉到本地，用户在检查了以后决定是否合并到工作本机分支中。

而`git pull` 则是将远程主机的最新内容拉下来后直接合并，即：`git pull = git fetch + git merge`，这样可能会产生冲突，需要手动解决。

# branch 分支

```bash
git branch 列出本地已经存在的分支，并且当前分支会用*标记
git branch -r 查看远程版本库的分支列表
git branch -a 查看所有分支列表（包括本地和远程，remotes/开头的表示远程分支）
git branch -v 查看一个分支的最后一次提交
git branch --merged  查看哪些分支已经合并到当前分支
git branch --no-merged 查看所有未合并工作的分支
```

* 创建分支
  
  ```bash
  git branch branchname
  ```

* 切换分支
  
  ```bash
  git checkout branchname
  ```

* 创建并切换分支
  
  ```bash
  git checkout -b branchname
  ```

* 删除分支
  
  ```bash
  git branch -d branchname
  ```

* 合并分支
  
  先将分支commit
  
  切换到master分支
  
  ```bash
  git merge branchname
  ```

* 删除远程分支
  
  ```bash
  git push remote_name -d remote_branchname
  # 例如：
  git push origin -d scene
  ```
  
  

# rebase

将master的更改合并到分支里，即改变分支的基础（base）节点，也就是说分支将会是在master的新版本的基础上修改的。

用法：

```bash
git rebase master
```

中途合并可以保证和master尽量同步，防止大量开发分支后和master合并困难。

最好用merge替代，记录更完整清晰

# stash 保存临时修改，不提交

在修改分支到一半时，如果要切换到主分支进行修改，则可以用该命令暂时保存

* 保存临时修改
  
  git stash
  
  或
  
  git stash save “修改信息”

* 列出保存的临时修改
  
  ```bash
  git stash list
  ```

* 应用临时修改，stash@{0}是修改的标识
  
  ```bash
  git stash apply stash@{0}
  ```
  
  也可以直接用编号
  
  ```bash
  git stash apply 0
  ```
