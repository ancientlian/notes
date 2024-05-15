# nginx 常用命令

```shell
## 启动
# cd /usr/local/nginx/sbin/ 到相应的安装目录下
./nginx

## 停止
./nginx -s stop

## 安全退出
./nginx -s quit

## 重新加载配置文件
./nginx -s reload

## 查看配置文件是否错误
./nginx -t

## 查看 nginx 进程
ps aux|grep nginx
```
