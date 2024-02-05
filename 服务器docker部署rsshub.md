# 自有服务器 docker 部署 rsshub

## 准备服务器、准备 docker 环境

阿里云服务器，配置公网 ip，开放 1200 默认端口入网安全组策略

![开放 1200 默认端口1](https://ucc.alicdn.com/pic/developer-ecology/6cab819a907049dcb7544c98cc8f0c7b.png)

![开放 1200 默认端口2](https://ucc.alicdn.com/pic/developer-ecology/501a659b728f41f18a0b1ffa5c235de4.png)

![开放 1200 默认端口3](https://ucc.alicdn.com/pic/developer-ecology/501a659b728f41f18a0b1ffa5c235de4.png)

安装 docker：略

## 部署容器

### 拉取 RSSHub 的镜像

```shell
docker pull diygod/rsshub
```

### 运行容器

```shell
docker run -d --name rsshub -p 1200:1200 diygod/rsshub
```

在浏览器中打开 http://127.0.0.1:1200，enjoy it!

### 关闭

```shell
docker stop rsshub
```

### 更新

删除旧容器，然后重复安装步骤

```shell
docker stop rsshub
docker rm rsshub
```

## 配置
