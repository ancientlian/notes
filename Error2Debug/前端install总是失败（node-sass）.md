解决步骤

1、首先需要设置淘宝的镜像源

```bash
npm config set sass_binary_site=https://npm.taobao.org/mirrors/node-sass
```



2、==移除之前安装错的node-sass，这一步非常重要==

```
npm uninstall node-sass
```



3、老的版本的node-sass需要设置Python27的路径，不然使用的是python37

```bash
// 设置config
npm config set python C:\Python27
```



4、==注意，如果直接安装node-sass，那么会安装的是最新的。但是有的开源项目会出现依赖不兼容的情况，所以我们需要指定版本==

```bash
# 我一开始安装4.13.0版本，一直不能成功，换成4.13.1版本就可以了
npm install node-sass@4.13.1
```



其他，还是不行就使用cnpm命令