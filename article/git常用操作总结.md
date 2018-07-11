### 提交文件到本地仓库：

先提交文件到本地暂存盘，然后再提交到本地仓库：

```
git add ./
git commit
```

或者只需要一条命令直接搞定：

```
git commit -am [message]
```

此外，`git add`还可以指定参数来应对特定的情况：

* `git add -u`，将文件修改和文件删除添加到暂存盘
* `git add .`或者`git add -A`将文件修改、文件新建、文件删除添加到暂存盘
* `git add * -f`将对文件的所有操作都添加到暂存盘，包括添加到`.gitignore`中被忽略的文件

### 修改最近的一条 commit message

```
git commit --amend
```

### 创建并切换到新分支：

```
git checkout -b [name]
```

### 查看分支：

查看本地分支

```
git branch -l
```

查看远程分支

```
git branch -r
```

查看本地分支以及远程分支

```
git branch -a
```

### 删除分支

删除本地分支

```
git branch -d branch_name
```

有时候，由于某些原因无法删除分支时，可以使用强制删除

```
git branch -D branch_name
```

删除远程分支

```
git branch -r -d branch_name
git push origin :branch_name
```

### 合并分支文件到 master

先在 master 中将远程仓库最新文件拉取到本地，然后切换到你要合并的分支中：

```
git pull origin master
git checkout [branchName]
```

在当前分支与 master 执行衍合操作：

```
git rebase master
```

如果提示有文件冲突，那么解决冲突并提交到本地仓库。直到没有任何文件冲突提示之后，切换回 master 进行合并：

```
git checkout master
git merge [branchName]
```

### 修改当前项目的远程仓库地址：

可以直接修改：

```
git remote set-url origin [url]
```

或者先删除再添加：

```
git remote rm origin
git remote add origin [url]
```

### 查看当前项目的远程仓库地址：

```
git remote -v
```

### 拉取远程文件时，与本地文件发生了冲突：

这种情况一般发生在本地文件已经做了若干修改，但还没有提交到暂存盘。然后我想拉取一下远程仓库最新文件，结果因为某些文件和远程仓库造成冲突而无法拉取成功。这时你可以使用以下操作：

```
git stash
git pull origin master
git stash apply
```

第一步是将你修改过的文件先暂时贮藏起来（注意：和上面提到的暂存盘不是一回事儿），第二步是从远程仓库拉取最新的文件到本地，然后第三步是再将刚才贮藏的文件恢复出来。

三步操作完之后，最新文件已经拉取到本地，但是有可能和本地文件依然存在冲突。但此时你只要去解决每个文件的冲突即可。

### 放弃当前修改，拉取远程代码强制覆盖本地的代码

```
git fetch --all
git reset --hard origin/master
git pull //可以省略
```

### 推送文件到远程仓库时，发生了冲突：

这种情况一般发生在文件已经提交到本地仓库，但是往远程仓库推送时发生了冲突，可以使用以下操作：

```
git pull origin master --rebase
git push origin master
```

先将远程操作仓库的提交记录拉到本地进行衍合，然后再次进行提交即可。

### 从暂存盘恢复文件

```
git reset [fileName]
```

### `git revert`

撤消某一次的`commit`操作，不会影响在这之前或者之后的`commit`记录

### 从本地仓库恢复文件

```
git checkout -- [fileName]
```

### 恢复当前项目到某一历史版本

先通过`git log`命令获取要恢复到的版本 hash 值，然后再用`git reset`恢复：

```
git log
git reset [tree-ish] --hard
```

### 强制将当前项目推送到远程仓库

```
git push origin master --force
```

### 查看提交历史

```
git log
```

* `git log --stat`，查看每一次的提交都修改了哪些文件
* `git log -p`，深入查看每一个文件的修改细节
* `git log --pretty=oneline fileName`，查看某一具体文件的改动历史


### 查看某一次 commit 的相关改动

```
git show hash_id
```

### 执行`git pull`时提示："refusing to merge unrelated histories"

遇到此错误提示时，可以执行：

```
git pull origin master --allow-unrelated-histories
```
