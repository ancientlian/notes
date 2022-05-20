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







# 路由跳转的两种方式与路由传参

在非路由组件中存在登录以及注册两个链接，为了实现点击链接实现页面跳转，需要用到路由跳转。路由跳转分为声明式导航和编程式导航。编程式导航可以实现声明式导航的全部功能。并且编程式导航可以书写自己的业务逻辑。



## 声明式导航（务必有to）

为了实现点击登录链接跳转到登录组件，点击注册链接跳转到注册组件。此处采用声明式导航，因为此处没有其他业务逻辑。因此采用声明式导航。

注意：声明式导航必须有to ,如果没有to，那么该标签则无法在页面中显示，会直接在页面中消失。声明式导航类似a标签，主要用于页面跳转，无法书写其他逻辑



## 编程式导航

利用`push||replace`进行跳转
在页面中存在button的搜索按钮，当点击时并非只是完成跳转，还需有其他的业务逻辑，因此在此使用编程式导航。



## 路由传参

在使用搜索框的时候，需要获取非路由组件中搜索框的输入内容，传递给路由组件search中，此时需要路由传参。路由传参有三种方法，第一种字符串形式，第二种模板字符串形式，第三章对象写法（通常给路由设置名字）。其中前两种在开发中不常用，以第三种为例进行介绍。
首先如果想通过对象写法进行传参，那么通常在路由配置的时候给路由设置名字，因此需要先给路径为/search的路由设置名字，名字为search。设置完成之后，当搜索按钮被点击时，通过名称进行传参，可以传params形式参数，也可传query参数。

![在这里插入图片描述](https://img-blog.csdnimg.cn/903251f663c34a94a9cb1538bb9934d4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUmV2aW4wNTA=,size_15,color_FFFFFF,t_70,g_se,x_16)

![在这里插入图片描述](https://img-blog.csdnimg.cn/9d8681ee40214998b5f84ca975383f02.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUmV2aW4wNTA=,size_20,color_FFFFFF,t_70,g_se,x_16)

# ref介绍

ref被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 `$refs`对象上。如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向该子组件实例

通俗的讲，ref特性就是为元素或子组件赋予一个ID引用,通过`this.$refs.refName`来访问元素或子组件的实例

## `this.$refs`介绍

this.$refs是一个对象，持有当前组件中注册过 ref特性的所有 DOM 元素和子组件实例

> **注意：** $refs只有在组件渲染完成后才填充，在初始渲染的时候不能访问它们，并且它是非响应式的，因此不能用它在模板中做数据绑定



# this.$nextTick()的用法

`this.$nextTick` 将回调延迟到下次DOM更新循环之后执行。在修改数据之后立即使用它，然后等待DOM更新。

`this.$nextTick` 跟全局方法 vue.nextTick 一样，不同的是，回调的 this 自动绑定到调用它的实例上。



# nprogress进度条的使用

打开一个页面时，往往会伴随一些请求，并且会在页面上方出现进度条。它的原理时，在我们发起请求的时候开启进度条，在请求成功后关闭进度条，所以只需要在request.js中进行配置。
如下图所示，我们页面加载时发起了一个请求，此时页面上方出现蓝色进度条
![在这里插入图片描述](https://img-blog.csdnimg.cn/f0df5bccfaee4274b45755b52bf40b60.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5q-b5q-b6Jmr5ZGc5ZGc,size_20,color_FFFFFF,t_70,g_se,x_16)
对应的request.js设置

```js
import axios from "axios";
//引入进度条
import nprogress from 'nprogress';
//引入进度条样式
import "nprogress/nprogress.css";
//1、对axios二次封装
const requests = axios.create({
    //基础路径，requests发出的请求在端口号后面会跟改baseURl
    baseURL:'/api',
    timeout: 5000,
})

//2、配置请求拦截器
requests.interceptors.request.use(config => {
    //config内主要是对请求头Header配置
    //比如添加token

    //开启进度条
    nprogress.start();
    return config;
})

//3、配置相应拦截器
requests.interceptors.response.use((res) => {
    //成功的回调函数

    //响应成功，关闭进度条
    nprogress.done()
    return  res.data;
},(error) => {
    //失败的回调函数
    console.log("响应失败"+error)
    return Promise.reject(new Error('fail'))
})

//4、对外暴露
export default requests;
```

可以通过修改nprogress.css文件的background来修改进度条颜色。（最好不要这样改，因为是对源码文件的修改，是不允许的）
![在这里插入图片描述](https://img-blog.csdnimg.cn/e66d5f5d851a4839810c34ad234f7c0a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5q-b5q-b6Jmr5ZGc5ZGc,size_11,color_FFFFFF,t_70,g_se,x_16)





# vuex

一般是在store文件夹下面

首先确保安装了vuex,根目录创建store文件夹，文件夹下创建index.js，内容如下：

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

//对外暴露store的一个实例
export default new Vuex.Store({
    
    
    state:{
    
    },
    mutations:{
    
    },
    actions:{
    
    },
    
})
```

```js
import Vue from 'vue';
import Vuex from 'vuex';
//需要使用插件一次
Vue.use(Vuex);

//state:仓库存储数据的唯一手段
const state={

};
//mutation:修改state的唯一手段
const mutations={

}
//action:处理action，可以书写自己的业务逻辑，也可以处理异步
const actions={

}
//getters:理解为计算属性，用于简化仓库数据，让组件获取仓库的数据更方便
const getters={

}


//对外暴露Store类的一个实例
export default new Vuex.Store({
    state,
    mutations,
    actions,
    getters,
    modules:{
        
    }
});
```





如果想要使用vuex，还要再main.js中引入
main.js:
(1) 引入文件
(2) 注册store
**但凡是在main.js中的Vue实例中注册的实体，在所有的组件中都会有（this.$.实体名）属性**

```js
import store from './store'
new Vue({
    
    
  render: h => h(App),
  //注册路由，此时组件中都会拥有$router $route属性
  router,
  //注册store,此时组件中都会拥有$store
  store
}).$mount('#app')
```



## 模块式开发

在store文件夹里面，新建不同的页面文件夹，来分别保存不同页面的数据。最后在index里面导入







# 三级联动

动态背景颜色

通过JS控制二三级分类和隐藏

```vue
<template>
  <!-- 商品分类导航 -->
  <div class="type-nav">
    <div class="container">
        <!-- 鼠标移出 -->
      <div @mouseleave="leaveIndex" @mouseenter="listEnter">
        <h2 class="all">全部商品分类</h2>
        <div class="sort" v-show="show">
          <div class="all-sort-list2" @click="goSearch">
            <div
              class="item"
              v-for="(c1, index) in categoryList"
              :key="c1.categoryId"
              :class="{ cur: currentIndex == index }"
            >
              <h3 @mouseenter="changeIndex(index)">
                <a
                  :data-categoryName="c1.categoryName"
                  :data-category1Id="c1.categoryId"
                  >{{ c1.categoryName }}</a
                >
              </h3>
              <!-- 二、三级分类 通过js控制二三级分类显示和隐藏-->
              <div
                class="item-list clearfix"
                :style="{ display: currentIndex == index ? 'block' : 'none' }"
              >
                <div
                  class="subitem"
                  v-for="c2 in categoryList"
                  :key="c2.categoryId"
                >
                  <dl class="fore">
                    <dt>
                      <a
                        :data-categoryName="c2.categoryName"
                        :data-category2Id="c2.categoryId"
                        >{{ c2.categoryName }}</a
                      >
                    </dt>
                    <dd>
                      <em v-for="c3 in categoryList" :key="c3.categoryId">
                        <a
                          :data-categoryName="c3.categoryName"
                          :data-category3Id="c3.categoryId"
                          >{{ c3.categoryName }}</a
                        >
                      </em>
                    </dd>
                  </dl>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <nav class="nav">
        <a href="###">服装城</a>
        <a href="###">美妆馆</a>
        <a href="###">尚品汇超市</a>
        <a href="###">全球购</a>
        <a href="###">闪购</a>
        <a href="###">团购</a>
        <a href="###">有趣</a>
        <a href="###">秒杀</a>
      </nav>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";
import throttle from "lodash/throttle";
export default {
  name: "TypeNav",
  data() {
    return {
      currentIndex: -1,
      show: true,
    };
  },
  mounted() {
    if (this.$route.path != "/home") {
      this.show = false;
    }
  },
  computed: {
    ...mapState({
      categoryList: (state) => state.home.categoryList,
    }),
  },
  methods: {
    /* changeIndex(index) {
      this.currentIndex = index;
    }, */
    changeIndex: throttle(function (index) {
      this.currentIndex = index;
    }, 50),

    goSearch(event) {
      let element = event.target;
      let { categoryname, category1id, category2id, category3id } =
        element.dataset;
      if (categoryname) {
        let location = { name: "search" };
        let query = { categoryName: categoryname };
        if (category1id) {
          query.category1Id = category1id;
        } else if (category2id) {
          query.category2Id = category2id;
        } else {
          query.category3Id = category3id;
        }
        if (this.$route.params) {
          location.params = this.$route.params;
          location.query = query;
          this.$router.push(location);
        }
      }
    },
    listEnter() {
      if (this.$route.path != "/home") {
        this.show = true;
      }
    },
    leaveIndex() {
      this.currentIndex = -1;
      if (this.$route.path != "/home") {
        this.show = false;
      }
    },
  },
};
</script>

<style scoped lang="less">
.type-nav {
  border-bottom: 2px solid #e1251b;

  .container {
    width: 1200px;
    margin: 0 auto;
    display: flex;
    position: relative;

    .all {
      width: 210px;
      height: 45px;
      background-color: #e1251b;
      line-height: 45px;
      text-align: center;
      color: #fff;
      font-size: 14px;
      font-weight: bold;
    }

    .nav {
      a {
        height: 45px;
        margin: 0 22px;
        line-height: 45px;
        font-size: 16px;
        color: #333;
      }
    }

    .sort {
      position: absolute;
      left: 0;
      top: 45px;
      width: 210px;
      height: 461px;
      position: absolute;
      background: #fafafa;
      z-index: 999;

      .all-sort-list2 {
        .item {
          h3 {
            line-height: 30px;
            font-size: 14px;
            font-weight: 400;
            overflow: hidden;
            padding: 0 20px;
            margin: 0;

            a {
              color: #333;
            }
          }

          .item-list {
            display: none;
            position: absolute;
            width: 734px;
            min-height: 460px;
            background: #f7f7f7;
            left: 210px;
            border: 1px solid #ddd;
            top: 0;
            z-index: 9999 !important;

            .subitem {
              float: left;
              width: 650px;
              padding: 0 4px 0 8px;

              dl {
                border-top: 1px solid #eee;
                padding: 6px 0;
                overflow: hidden;
                zoom: 1;

                &.fore {
                  border-top: 0;
                }

                dt {
                  float: left;
                  width: 54px;
                  line-height: 22px;
                  text-align: right;
                  padding: 3px 6px 0 0;
                  font-weight: 700;
                }

                dd {
                  float: left;
                  width: 415px;
                  padding: 3px 0 0;
                  overflow: hidden;

                  em {
                    float: left;
                    height: 14px;
                    line-height: 14px;
                    padding: 0 8px;
                    margin-top: 5px;
                    border-left: 1px solid #ccc;
                  }
                }
              }
            }
          }
        }
        .cur {
          background-color: skyblue;
        }
      }
    }
  }
}
</style>
```

















































