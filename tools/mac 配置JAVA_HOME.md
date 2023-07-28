# 配置`.bash_profile`

```bash
# 编辑配置文件
vim ～/.bash_profile

# 添加配置
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_202.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

# 刷新生效
source ~/.bash_profile

# 测试
echo $JAVA_HOME
```

# 配置`.zshrc`

如果使用的是zsh，则需要同时将三个变量也配置到`~/.zshrc`中。
