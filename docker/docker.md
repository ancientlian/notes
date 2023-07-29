# docker

## 概念

一个容器进程本质上是一个运行在沙盒中的隔离进程，由Linux系统本身负责隔离，Docker只是提供了一系列工具，帮助我们设置好隔离环境后，启动这个进程。
最基本的隔离就是进程之间看不到彼此，这是由Linux的Cgroup机制实现的。
第二种隔离就是隔离系统真实的文件系统。
第三种隔离就是网络协议栈的隔离

## Getting started

```Docker
docker run -d -p 80:80 docker/getting-started
```

* `-d` - run the container in detached mode (in the background) 在后台运行
* `-p 80:80` - map port 80 of the host to port 80 in the container 将主机的端口80映射到容器中的端口80
* `docker/getting-started` - the image to use 要使用的图像

可以组合单字符标志来缩短完整命令。例如，上面的命令可以写成：

```Docker
docker run -dp 80:80 docker/getting-started
```

### 容器

Simply put, a container is simply another process on your machine that has been isolated from all other processes on the host machine. 沙箱隔离机制

That isolation leverages [kernel namespaces and cgroups](https://medium.com/@saschagrunert/demystifying-containers-part-i-kernel-space-2c53d6979504), features that have been in Linux for a long time.

## 镜像

When running a container, it uses an isolated filesystem. This custom filesystem is provided by a **container image**. Since the image contains the container's filesystem, it must contain everything needed to run an application - all dependencies, configuration, scripts, binaries, etc.The image also contains other configuration for the container, such as environment variables, a default command to run, and other metadata.

## 配置阿里云镜像加速器

1. 访问这个地址，使用自己的阿里云账号登录，查看菜单栏左下角，发现有一个镜像加速器:[https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors) 找到自己的镜像加速器的地址链接
2. 打开Mac中Docker的配置选项，并添加；点击apply & restart
3. 最后用docker info命令检查配置是否生效

## docker安装nginx配置

```docker
docker run -d -p 80:80 --name nginx02 -v F:\WorkSpace\nginx\nginx.conf:/etc/nginx/conf.d/default.conf nginx
```

> [Error - nginx: [emerg\] "server" directive is not allowed here in /etc/nginx/nginx.conf:1](https://stackoverflow.com/questions/59848507/error-nginx-emerg-server-directive-is-not-allowed-here-in-etc-nginx-ngin)
>
> In your context you need to wrap server in http block. Or move you config file not by replacing the default config file, but rather replacing a default server config located in /etc/nginx/conf.d/ So you can use something like
>  `./nginx.conf:/etc/nginx/conf.d/default.conf`
> for volumes for nginx. That will rewrite your default server config. You can also copy it to sites-enabled folder if you planning to use multiple config files.
>
> Or you can modify your nginx config, by starting with wrapping it with http block

## 访问挂载目录

这是对win10下如何进入docker挂载的`/var/lib/docker/volumes/nginxconfig/_data`目录的解释
<https://stackoverflow.com/questions/60408574/how-to-access-var-lib-docker-in-windows-10-docker-desktop>

Docker Desktop for Windows（WSL 2 方式）数据卷存放位置及如何访问
<https://blog.csdn.net/u013568383/article/details/113888776>

## 针对于run不同的命令，每次创建新的容器，占用内存？

```Bash
PS C:\Users\XX> docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:ro nginx
a7b24843f6be8e3d55536eccc31654a65f67dcf41ade68662cffc85bf6295064

PS C:\Users\XX> docker stop a7
a7

PS C:\Users\XX> docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx:rw nginx
docker: Error response from daemon: Conflict. The container name "/nginx02" is already in use by container "a7b24843f6be8e3d55536eccc31654a65f67dcf41ade68662cffc85bf6295064". You have to remove (or rename) that container to be able to reuse that name.
See 'docker run --help'.

PS C:\Users\XX> docker run -d -P --name nginx02rw -v nginxconfig:/etc/nginx:rw nginx
190696cfbdd7584becae6c1d1494ecb3cd35c89ce4033abf64eab6815f8410fc

```

此时可以发现只要是改变任何一个的run参数，其实都会启动一个新的容器；
容器是不占用存储的，只会在运行时占用运行内存，只有images会占用内存

run的命令很多，要是之后忘了配置一些参数，需要重新run新的容器

## /bin/bash  docker后台必须运行一个进程

`docker run -i -t tomcat /bin/bash`中的`/bin/bash`的作用是因为docker后台必须运行一个进程，否则容器就会退出，在这里表示启动容器后启动bash。
