# [Mac m1 卡死假死，鼠标一直卡住转圈圈的快速解决方法（使用快捷指令） - 知乎](https://zhuanlan.zhihu.com/p/613662902?utm_id=0)

杀掉系统默认输入法

```bash
ps aux | grep '[S]CIM_Extension' | awk '{print $2}' | xargs kill -9
```
