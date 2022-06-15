我平时经常会往内部的 coding 代码平台以及 github 上提交代码。每次拉取一个新仓库之后，都要手动设置其邮箱和用户名，因为内部的代码平台有这方面的限制，否则代码无法提交。
而往 github 上提交代码，又需要将邮箱和用户设置成另外一套。所以，每次手动设置都甚是麻烦，而且经常会忘

于是，我就想有没有一种机制，可以根据仓库的远程地址，自动设置成不同的邮箱和用户呢？

> 要将 git 客户端升级到 2.36 及以后版本，否则不支持此功能

更新本地的 git 版本：

```
brew update

brew install git
```

git 的全局配置文件位于`/Users/{用户名}/.gitconfig`。在此目录中新建两个需要的配置文件：

`coding.jd.com`对应的配置文件`.coding`：

```
[user]
  email = zhangsan@jd.com
  name = zhangsan
```

`github.com`对应的配置文件`.github`

```
[user]
  email = lisi@gmail.com
  name = lisi
```

然后在全局的`.gitconfig`中添加根据 remote url 来适配不同的配置文件：

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
