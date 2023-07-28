# docker 常用命令速查

```bash

docker ps

# -i:以交互模式启动; -t:以附加进程方式启动
docker start name

docker stop 容器ID或容器名

docker kill 容器ID或容器名 :直接关闭容器

docker restart 容器ID或容器名

#进入容器
docker exec -it mysql bash

# 拷贝 不管容器有没有启动，拷贝命令都会生效。
docker cp 容器名:{容器中要拷贝的文件名及其路径} {要拷贝到宿主机里面对应的路径}

```
