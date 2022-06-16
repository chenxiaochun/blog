## 问题背景

我平时经常会同时往内部的 coding 代码平台以及 github 上提交代码

往 coding 平台上 push 代码时，它会要求你提交代码时的用户名和邮箱必须符合其规定，否则，会拒绝你提交代码。解决此问题有两种方式：

第一种方式：

1. 使用`git config user.name "zhangsan"`和`git config user.email "zhangsan@jd.com"`修改成内部的用户名称和邮箱
2. 使用`git reset [hashID] --soft`回退到代码提交前的状态，再重新进行提交即可

第二种简单方式就是直接去 coding 平台，将该功能直接关掉即可：

<img src="https://img11.360buyimg.com/imagetools/jfs/t1/58638/34/19837/68688/62aa8cd7E1c590a61/238e23b403cd34d6.png" width="600" />

因为拉取新代码仓库毕竟是一个低频操作，下次拉取新仓库时，可能又会忘掉这件事儿。因此，可以在 git 的全局配置文件中设置一个用户名和邮箱，这样所有从 coding 拉取的仓库都会使用这套配置

但是，新的问题又来了。如果我要往 github 上提交代码，但是 github 上使用的用户名和邮箱和 coding 的不一样。那岂不是默认还是使用了全局的配置？如果不想用，
就只能在 github 的仓库中本地修改一下用户名和邮箱了。而且，下次拉取了新的 github 仓库，还需要再设置一次用户名和邮箱

于是，我就想有没有一种机制，可以让 git 根据当前编辑代码仓库的远程地址，自动设置成对应的用户名和邮箱呢？

coding 的远程仓库地址：

```
origin	git@coding.jd.com:rmb-frontend/test.git (fetch)
```

github 的远程仓库地址：
```
origin	git@github.com:chenxiaochun/blog.git (fetch)
```

## 更新 git 版本

要将 git 客户端升级到 2.36 及以后版本，否则不支持此功能

更新本地的 git 版本：

```
brew update

brew install git
```

更新完成之后，使用`git version`查看版本号

## 配置文件

git 的全局配置文件位于`/Users/{用户名}/.gitconfig`。在此目录中新建两个需要的配置文件：

`coding.jd.com`对应的配置文件`.coding`：

```
[user]
  email = zhangsan@jd.com
  name = zhangsan
```

`github.com`对应的配置文件`.github`：

```
[user]
  email = lisi@gmail.com
  name = lisi
```

然后在全局的`.gitconfig`中添加根据 remote url 来适配不同的配置文件：

这里面最重要的就是`includeIf "hasconfig:remote.*.url:`指令，它支持 glob 模式，可以匹配任意的远程仓库地址，如果匹配到，就会自动加载对应的配置文件

```
[core]
  editor = /usr/local/bin/nvim
  ignorecase = false

[includeIf "hasconfig:remote.*.url:git@coding.jd.com:*/**"]
  path=./.coding

[includeIf "hasconfig:remote.*.url:git@github.com:*/**"]
  path=./.github
```

参考链接：

* https://stackoverflow.com/questions/61983894/git-global-config-for-specific-repositories
* https://git-scm.com/docs/git-config#_conditional_includes
* https://www.ofcss.com/2020/02/17/auto-config-user-name-email-for-different-remote-repository.html
