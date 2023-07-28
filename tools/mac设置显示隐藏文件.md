系统升级之后快捷键就不能用了，我目前的解决方法是创建了两个shell脚本：

[show.sh](http://show.sh)

```text
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder
```

[hide.sh](http://hide.sh)

```text
defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder
```

然后在.bash_profile中设置别名：

```text
alias show=/yourpath/show.sh
alias hide=/youpath/hide.sh
```

  

source之后可以在终端中通过show和hide来隐藏和显示隐藏文件。

  
  
作者：darkmi  
链接：https://www.zhihu.com/question/24635640/answer/127004307  
来源：知乎  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。