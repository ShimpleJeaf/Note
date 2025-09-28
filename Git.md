# vpn 代理

需要在git中设置代理

# clone 克隆

基本克隆

```bash
git clone <url>
```

包含**子模块**，既引用的其他仓库

```bash
git clone --recursive <url>
```

**断点续传**

```bash
git clone --resume <url>
```

# submodule 子模块

如果克隆的时候没有包含子模块，cmake会报错:git submodule ... not found

可用以下命令加入

```bash
git submodule update --init --recursive
```

# mv 更名移动 文件、文件夹

git mv可以更名文件、移动文件、更名文件夹、移动文件夹

```bash
git mv srcFile dstFile
git mv srcFile1 srcFile2 ... dstDir
# 注意，当dstDir存在时，为移动，不存在时为更名
git mv srcDir dstDir 
```

# revert 还原

**还原**用于撤销某个特定提交的更改，而不更改提交历史。与重置不同的是，**还原不会删除提交记录**，而是创建一个新的提交，表示撤销了某个特定的更改。它在协作开发中更安全，因为不会更改历史记录。

# reset 重置

会更改历史记录，特别是 `reset --hard` 是不可逆的，因此在多人协作中应慎用。

* --soft    仅重置提交记录，保留工作区的修改。适合想要重新提交的情况。

* --mixed    head、index更改，但是本地代码不变，重置到版本后的所有变更作为本地修改保存在工作区中

* --hard    head、index、工作区代码全部回滚到指定版本，但是不会影响未跟踪的文件

# reflog

所有提交的操作记录，如果不小心回滚了代码，可以在这里找回

修改保存时间：

```bash
git reflog expire --expire-unreachable=180.days --all
```

# count-objects

查看仓库大小

* -v 详细信息

* -H human-readable大小表示

# gc

git gc 是指“垃圾回收（garbage collection）”，用于清理和优化 Git 仓库中的垃圾对象。通过执行 git gc，Git 会将不再需要的对象从仓库中清理出去，以保持仓库的整洁和性能的良好表现。

# prune

执行 git prune 会删除仓库中不再需要的垃圾对象。与 git gc 不同的是，git prune 只进行单纯的清理操作，不对存储进行优化。prune 可以轻松删除仅存在于本地的但没有在远程仓库中引用的分支。

# fecth和pull

`git fetch`是将远程主机的最新内容拉到本地，用户在检查了以后决定是否合并到工作本机分支中。

而`git pull` 则是将远程主机的最新内容拉下来后直接合并，即：`git pull = git fetch + git merge`，这样可能会产生冲突，需要手动解决。

# 仓库迁移

1. 将原仓库克隆到本地

2. 移除原远程仓库
   
   ```bash
   git remote remove origin
   ```

3. 添加新的远程仓库
   
   ```bash
   git remote add origin <new_repository_url>
   ```

4. 将代码推送到新的仓库
   
   ```bash
   git push -u origin master
   ```

## 记录太大，无法推送，increase http.postBuffer

> unable to rewind rpc post data - try increasing http.postbuffer

```bash
git config --global http.postBuffer 8192000000
git config http.postBuffer 8192000000
```

不能设置太大，不然会超过http协议的限制，也不行

> fatal: protocol error: bad line length 8192 47.18 MiB/s

* 可以分部push记录：
  
  先reset到开始的commit记录，再进行push

* push指定的commit记录，第一次推送时不能用，所以第一次推送必须先reset
  
  ```bash
  git push origin d3a34:master
  ```

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

**默认不会保存untracked和ignored文件**

**不会保存commit，切分支的话commit由分支系统保存**

* 保存临时修改
  
  不加子命令时使用push
  
  git stash
  
  或
  
  git stash push -m ["修改信息"]
  
  git stash save -m [“修改信息”]
  
  * -u | --include-untracked 
    
    包括untracked文件
  
  * -a | --all
    
    包括untracked、ignored文件

* 删除stash
  
  ```bash
  git stash drop 0
  ```

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

* 弹出临时修改
  
  应用临时修改，并从stash list里删除
  
  ```bash
  git stash pop 0
  ```

* 显示更改信息，会显示简略的文件变化信息
  
  ```bash
  git stash show 0
  ```

# 从历史记录中删除一个文件

```bash
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch secret.txt' --prune-empty --tag-name-filter cat -- --all
```

secret.txt将从版本记录中完全删除

注意：

* 这是一个耗时的操作

* 文件路径是相对于git仓库根目录的

* 文件路径不要以 / 开头，否则将会认为是相对git安装目录的路径

* 如果是删除文件夹，在 --cache 后加 -r 递归删除
