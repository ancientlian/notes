# docekr 部署 jenkins 流水线

## docker 安装 jenkins

我使用的 maven 版本是 3.9.9，是 jenkins 最新的 maven
可以挂载外部的 maven 路径以及 nodejs 路径，我这里直接用 jenkins 内部的插件

## jenkins 安装插件

安装 maven 插件，后端打包用
安装 nodejs 插件，前端打包使用
gitlab 插件
git 插件

## 配置 全局工具配置

### Maven 安装

填写 Name，选择 自动安装，选择想要的版本号

### NodeJS 安装

填写别名，选择自动安装，选择想要安装的版本号

## 配置 系统配置

1. mavern 如果不需要指定目录，那就使用默认的配置，也就是在`~/.m2`
2. 配置 GitLab，输入 URL 地址以及对应认证，认证在`系统管理--凭据管理`中配置
3. 配置 SSH，输入远程主机相关信息

## 创建后端流水

1. 新建任务选择构建 maven 项目
2. 选择 `GitLab Connection`
3. 配置 源码管理 的对应仓库
4. 配置`Build->Goals and options`,指定打包的命令，如 `clean package -DskipTests=true -Pprod`，则是使用 prod 的配置文件进行打包，并且跳过 test 环节

## git 参数化构建（手动选择分支进行构建）（可选）

1. 安装 `Git Paramater` 插件
2. 配置项目 `General`
   勾选参数化构建过程（This project is parameterized）
   填写名称（如 branch ，下面会用到），参数类型选择分支，填写默认分支值
3. 配置`源码管理`中的 Git 参数
   在 `Branches to build` 中指定分支，填写`${branch}`，占位符当中的名字就是上面填写的配置名

## 打包后上传到服务器

选择构建后操作，选择`Send build artifacts over SSH`

在 `Source files` 设置需要上传的文件，如`**/*.jar`

在 `Remove prefix` 设置需要移除的前缀，如`/project-service/target`

在 `Remote directory` 设置服务器的上传路径，如`/home/people`

在 `Exec command` 设置服务器上的脚本操作，如

```shell
pwd
echo "============--- 上传结束 ---================"
mv zzcloud-projectmgt-*.jar project.jar
docker cp project.jar zz-project-backend:/opt
echo "============--- 拷贝进 docker ---================"

echo "============--- 执行 docker 内脚本 ---================"
docker exec -i zz-project-backend bash /opt/execWorks.sh
echo "============ 删除文件"
rm -fr ./*.jar

```

保存后就可以开始构建了
