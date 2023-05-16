-   先查 cache，cache 中不存在，直接更新 DB。
-   cache 中存在，则先更新 cache，然后 cache 服务自己更新 DB（**同步更新 cache 和 DB**）。

简单画了一张图帮助大家理解写的步骤。

![](https://img-blog.csdnimg.cn/20210201100340808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MzM3Mjcy,size_16,color_FFFFFF,t_70)

**读(Read Through)：**