# docker

## 概念

一个容器进程本质上是一个运行在沙盒中的隔离进程，由Linux系统本身负责隔离，Docker只是提供了一系列工具，帮助我们设置好隔离环境后，启动这个进程。
最基本的隔离就是进程之间看不到彼此，这是由Linux的Cgroup机制实现的。
第二种隔离就是隔离系统真实的文件系统。
第三种隔离就是网络协议栈的隔离

## docker安装nginx配置

```bash
docker run -d -p 80:80 --name nginx02 -v F:\WorkSpace\nginx\nginx.conf:/etc/nginx/conf.d/default.conf nginx
```

> [Error - nginx: [emerg\] "server" directive is not allowed here in /etc/nginx/nginx.conf:1](https://stackoverflow.com/questions/59848507/error-nginx-emerg-server-directive-is-not-allowed-here-in-etc-nginx-ngin)
>
> In your context you need to wrap server in http block. Or move you config file not by replacing the default config file, but rather replacing a default server config located in /etc/nginx/conf.d/ So you can use something like
>
> ```text
>  - ./nginx.conf:/etc/nginx/conf.d/default.conf
> ```
>
> for volumes for nginx. That will rewrite your default server config. You can also copy it to sites-enabled folder if you planning to use multiple config files.
>
> Or you can modify your nginx config, by starting with wrapping it with http block
