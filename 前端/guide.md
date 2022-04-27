# @符号在vue路径中的作用和自定义设置

@符号在vue路径中直指src文件夹，这是构建项目时默认设置好的。具体设置如下：
`build/webpack.base.conf.js`

```js
resolve: {
extensions: ['.js', '.vue', '.json'],
alias: {
'vue$': 'vue/dist/vue.esm.js',
'@': resolve('src'),
}
},
```

可以自定义修改styles文件夹为直接路径，方法如下：
```js
 resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
      'styles': resolve('src/assets/styles')
    }
  },
```


# vue中：key的作用
https://www.jianshu.com/p/0044532e4a93


key的作用就是更新组件时判断两个节点是否相同。相同就复用，不相同就删除旧的创建新的。如果不添加key组件默认都是就地复用，不会删除添加节点，只是改变列表项中的文本值，要知道节点操作是十分耗费性能的。而添加了key之后，当对比内容不一致时，就会认为是两个节点，会先删除掉旧节点，然后添加新节点。
> https://blog.csdn.net/weixin_42657492/article/details/106142225

# axios

http://www.axios-js.com/zh-cn/docs/
http://www.axios-js.com/zh-cn/docs/vue-axios.html



# ajax

## 优点与缺点



没有浏览历史，不能回退

存在跨域问题

SEO搜索引擎优化不友好
