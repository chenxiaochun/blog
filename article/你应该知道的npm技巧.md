![image](https://cloud.githubusercontent.com/assets/1744713/26188494/138abbf0-3bd2-11e7-8a7b-2569cbd88769.png)

npm中内置了大量的实用技巧，如何高效的使用它们是一件具有挑战性的事情。学会下面这些技巧，将会让你在任何项目中使用npm都会事半功倍。

### 1、打开package的主页
```vim
npm home $package
```
运行`home`命令可以打开此package的主页，例如：`npm home lodash`，会打开`lodash`的主页。不管你机器中是否全局安装了此package，甚至是不存在于你当前项目中都可以打开。

### 2、打开package的Github地址
```vim
npm repo $package
```
和`home`命令类似，运行`repo`命令可以打开此package的Github仓库地址，并且它也不要求你必须安装了此package。例如：`npm repo express`就打开了`express`的Github仓库地址。

### 3、检查`package.json`中的那些已经过时的依赖
```vim
npm outdated
```
运行此命令，它会去检查npm上的注册信息，看你当前项目中有哪些依赖已经过时了，并以列表的形式展示出来。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/271512-e188b97bc325204e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4、揪出`package.json`中没有被用到的packages
```vim
npm prune
```
运行此命令，npm会拿你的`package.json`和`node_modules`目录进行比对，然后把那些在`package.json`中没有引用到的package列出来。
还有那些你没有手动添加到`package.json`或者是执行`npm install $package`时没有加`--save`参数的，都会被删掉。

### 5、锁定你的依赖版本
```vim
npm shrinkwrap
```
使用`shrinkwrap`命令会在你当前项目中生成一个`npm-shrinkwrap.json`文件。它会将你当前`package.json`中引用的依赖版本锁定，当下次执行`npm install`时，它默认安装的其实是`shrinkwrap.json`中锁定的依赖版本号。
注意：如果你想让`package.json`、`shrinkwrap.json`和`node_modules`中的依赖版本号保持一致的话，请慎用此功能。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/271512-580882f53c21026b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6、使用npm v3 以及 Node.js v4 LTS
```vim
npm install -g npm@3
```
首先你要知道，`nodejs v4`自带的npm默认版本是v2。执行此命令会将你的npm从v2升级到v3，并且安装的是最新的稳定版本，这样你就可以在`nodejs v4`的环境中使用`npm v3`了。

### 7、如何在`npm install -g`时，不用再加sudo
```vim
npm config set prefix $dir
```
运行此命令之后，`$dir`目录就变成了你全局安装依赖时的默认目录，也就是`bin`目录，以后也就不再需要输入`sudo`命令了。
需要注意的是，你可能需要使用`chown -R $USER $dir`命令改变一下此目录的操作权限，并把`$dir/bin`加入到`PATH`环境变量中。

### 8、修改依赖包的默认保存前缀
```vim
npm config set save-prefix="~"
```
波浪号`~`：表示当信赖的副版本号有更新时，允许使用`npm update`进行安装。
脱字符`^`：表示当依赖的主版本号有更新时，允许使用`npm update`进行安装。

### 9、如何在生产环境中剥离所有的devDependencies
当你的项目准备好要切换到生产环境时，安装依赖包时一定要带上`--production`参数，使用此参数意味着将只会安装你的`dependencies`依赖，而忽略你的`devDependencies`依赖，这样可以确保你开发阶段的工具包不会进入到生产环境中。
此外，你也可以设置`NODE_ENV`环境变量的值为`production`，这样就可以完全确保项目的`devDependencies`绝对不会被安装了。

### 10、请谨慎使用`.npmignore`
如果你的项目中没有使用`.npmignore`文件，那么它默认匹配的是`.gitignore`中的规则以及一些额外的默认配置。
可是如果你在项目中添加了`.npmignore`文件，`.gitignore`中的规则就会被忽略，而且这时候你还需要维护两份儿规则文件。

### 11 、给`npm init`命令设置自动执行的默认值
我们通常在一个项目中使用`npm init`来初始化`package.json`文件。可是每次都填写那些配置信息又很麻烦，有一个一步到位的方式：
```vim
npm init -y
```
如果你想把经常用到的一些值设置成默认配置，可以使用`config set`命令。
```vim
npm config set init.author.name $name
npm config set init.author.email $email
```
相应的，如果你想完整的自定义一份儿`init`脚本，可以这样用：
```vim
npm config set init-module ~/.npm-init.js
```
下面提供一份儿示例供大家参考：
```vim
var cp = require('child_process');
var priv;
var USER = process.env.GITHUB_USERNAME || 'YOUR_GITHUB_USERNAME';
module.exports = {
    name: prompt('name', basename || package.name),
    version: '0.0.1',
    private: prompt('private', 'true', function(val) {
        return priv = (typeof val === 'boolean') ? val : !!val.match('true')
    }),
    create: prompt('create github repo', 'yes', function(val) {
        val = val.indexOf('y') !== -1 ? true : false;
        if (val) {
            console.log('enter github password:');
            cp.execSync("curl -u '" + USER + "' https://api.github.com/user/repos -d " + "'{\"name\": \"" + basename + "\", \"private\": " + ((priv) ? 'true' : 'false') + "}' ");
            cp.execSync('git remote add origin ' + 'https://github.com/' + USER + '/' + basename + '.git');
        }
        return undefined;
    }),
    main: prompt('entry point', 'index.js'),
    repository: {
        type: 'git',
        url: 'git://github.com/' + USER + '/' + basename + '.git'
    },
    bugs: {
        url: 'https://github.com/' + USER '/' + basename + '/issues'
    },
    homepage: "https://github.com/" + USER + "/" + basename,
    keywords: prompt(function(s) {
        return s.split(/\s+/)
    }),
    license: 'MIT',
    cleanup: function(cb) {
        cb(null, undefined)
    }
}
```

### 12、`npm -it`命令
执行`npm -it`命令，相当于同时执行了两条命令：
```vim
npm install
npm test
```

### 13、查看依赖包的全局默认安装目录
```vim
npm root -g
```

### 14、查看当前目录安装了哪些package
```vim
npm ls
```
如果后面加上一个package的名称，就是查看此package的被依赖信息。注意：是**被**依赖信息。
```vim
npm ls express
```
只想查看最近的层级：
```vim
npm ls --depth 0
```

### 15、查看package的相关信息
* `npm view express`，显示包的所有信息
* `npm view express dependencies`，查看包的依赖关系
* `npm view express engines`，查看包依赖的node版本
* `npm view express repository.url`，查看包的源文件地址
* `npm view express version`，查看包的版本信息

以上命令中的`view`都可以换成`info`，而且执行结果是一样的。

### 16、更新package
```vim
npm update express
```

### 17、卸载package
```vim
npm uninstall package
```

### 18、`npm prefix`命令
npm官方说这个是用来显示最近的包含`package.json`文件的父级目录。但是我实际测试觉得这种说法并不准确，它实际显示的应该是包含`node_modules`的最近父级目录。
如果是`npm prefix -g`，显示的就是全局的目录前缀，其实是来源于`npm config get prefix`的配置。

### 19、版本号管理
* `npm version patch`，增加修订版本号，例如：`1.1.1`=>`1.1.2`
* `npm version minor`，增加次要版本号，例如：`1.1.1`=>`1.2.0`
* `npm version major`，增加主版本号，例如：`1.1.1`=>`2.0.0`

注意，这几个命令都会自动将修改的文件执行`commit`操作，你接下来直接执行`git push`即可。前提是你的版本号必须遵守[语义版本说明](http://semver.org/)。

### 20、列出所有可用的脚本列表
```vim
npm run
```

### 21、查看当前安装源

```vim
npm get registry
```

### 22、`npm init [-f|--force|-y|--yes]`

系统不会进行任何询问，而是自动生成一个默认的`package.json`文件

### 参考资料：
* https://nodesource.com/blog/eleven-npm-tricks-that-will-knock-your-wombat-socks-off/
