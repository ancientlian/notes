
```bash
docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS...]
```
- `-f, --file FILE` 指定使用的 Compose 模板文件，**默认为 `docker-compose.yml`**，可以多次指定。
- `-p, --project-name NAME` 指定项目名称，**默认将使用所在目录名称作为项目名**。
- `--verbose` 输出更多调试信息。
- `-v, --version` 打印版本并退出。


### build

构建（重新构建）项目中的服务容器
```bash
docker-compose build [options] [SERVICE...]
```

- `--force-rm` 删除构建过程中的临时容器。
- `--no-cache` 构建镜像过程中不使用 cache（这将加长构建过程）。 
- `--pull` 始终尝试通过 pull 来获取更新版本的镜像。




[命令说明 - Docker — 从入门到实践](https://yeasy.gitbook.io/docker_practice/compose/commands)