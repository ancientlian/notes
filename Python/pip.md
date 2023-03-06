# pip

mac中安装

1、安装homebrew

2、安装Python

3、验证安装成功 `python3` ， `pip3`

## pip 与 pip3 的区别

- 只是安装位置不一样，没有本质的区别。可以通过pip show SomePackage查看安装位置。

- 如果系统中只安装了Python2，那么就只能使用pip。

- 如果系统中只安装了Python3，那么既可以使用pip也可以使用pip3，二者是等价的。

- 如果系统中同时安装了Python2和Python3，则pip默认给Python2用，pip3指定给Python3用。

## pip 换源

常见的国内源：
清华：<https://pypi.tuna.tsinghua.edu.cn/simple/>

阿里云：<http://mirrors.aliyun.com/pypi/simple/>

豆瓣：<http://pypi.douban.com/simple/>

中国科学技术大学 ：<https://pypi.mirrors.ustc.edu.cn/simple/>

Python官方：<https://pypi.python.org/simple/>

v2ex：<http://pypi.v2ex.com/simple/>

## 查看当前源

```bash
pip config list
```

## 更换源

```bash
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 通过配置文件的形式更换源

```bash
mkdir ~/.pip
vim ~/.pip/pip.conf
```

编辑

```bash
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host = mirrors.aliyun.com
```

windows 下配置文件名后缀是ini，不是conf,与linux不同,是pip.ini
目录为C:\Users\你的帐户名\pip

## 其他命令

```bash

pip3 list
# 安装最新版本
pip install SomePackage 
# 安装指定版本
pip install SomePackage==1.0.4 
# 安装的最小版本
pip install ‘SomePackage >= 1.0.4’ 
# 升级包
pip install --upgrade SomePackage 
# 卸载包
pip uninstall SomePackage 
# 搜索包
pip search SomePackage 
# 显示安装包信息
pip show SomePackage 
# 查看指定包的详细信息
pip show -f SomePackage 

# 更新 pip
python3.11 -m pip install --upgrade pip


```
