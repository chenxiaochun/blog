```
docker run -d -p 80:80 docker/getting-started
```

* `-d`，表示在后台运行容器
* `-p 80:80`，表示将`80`端口映射到容器的`80`端口上。也就是前面的`80`指的就是在浏览器中访问时的端口
* `docker/getting-started`，表示要使用的镜像名称

根据 Dockerfile 构建一个镜像：
```
docker build -t getting-started .
```

* `-t`，表示给当前构建的镜像起一个名字
* 最后的`.`表示在当前目录中寻找 Dockerfile 文件

查看镜像列表：

```
docker image ls
```

查看当前正在运行的容器列表：

```
docker ps
```

停止指定的容器：
```
# Swap out <the-container-id> with the ID from docker ps
docker stop <the-container-id>
```

容器只有停止了，才可以进行删除：
```
docker rm <the-container-id>
```

容器之间共享数据：

1. 创建公共容积
```
docker volume create todo-db
```

2. 创建容器时，使用`-v`参数指定公共容积名称，以及要加载的位置

```
docker run -dp 3000:3000 -v todo-db:/etc/todos getting-started
```