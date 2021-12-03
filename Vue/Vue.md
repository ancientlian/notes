# 第一个vue-cli程序



# webpack





# vue-router路由

安装需要的包

```
npm install vue-router --save-dev
```



main.js主函数引入路由

```js
import Vue from 'vue'
import App from './App'

import router from './router'

Vue.config.productionTip = false


new Vue({
  el: '#app',
  //配置路由
  router,
  components: { App },
  template: '<App/>'
})
```



App.vue进行具体的路由配置申明

```js
<template>
  <div id="app">
    <h1>lianForTest</h1>
    <br>
    <br>
    <br>
    <router-link to="/main">首页</router-link>
    <router-link to="/content">内容页</router-link>
    <router-link to="/exam">exam页面</router-link>
    <router-view></router-view>
  </div>
</template>

<script>

export default {
  name: 'App',

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

进行/router/index.js的具体路由配置

```js
import Vue from 'vue'
import VueRouter from "vue-router";
import Content from "../components/Content";
import Main from "../components/Main";
import Exam from "../components/Exam";

//安装路由
Vue.use(VueRouter);

//配置导出路由
export default new VueRouter({
  routes:[  //这里吧routes打成了routers所以错误了
    {
      //路由的路径
      path: "/content",
      name: "Content",
      //跳转的组件
      component: Content
    },
    {
      //路由的路径
      path: "/main",
      name: "Main",
      //跳转的组件
      component: Main
    },
    {
      //路由的路径
      path: "/exam",
      //跳转的组件
      component: Exam
    }
  ]
});
```

Main.vue具体的路由界面编写

```vue
<template>
  <h1>首页</h1>
</template>

<script>
export default {
  name: "Main"
}
</script>

<style scoped>

</style>
```





# vue+elementUI

1、创建工程

```shell
vue init webpack hello-vue-elementui
```

2、安装依赖，注意先进到工程目录

```shell

# 注意先进到工程目录

npm install vue-router --save-dev

npm i element-ui -S

npm install

npm install sass-loader node-sass --save-dev

# 启动测试
npm run dev
```







---

1、配置登录页面

```

```







2、配置路由



错误

"sass-loader": "^7.3.1"需要降低版本

node sass版本太高需要降版本（[Error: Node Sass version 5.0.0 is incompatible with ^4.0.0]）





# 路由嵌套

index.js

```js
import Vue from 'vue'
import VueRouter from "vue-router";

import Main from "../views/Main";
import Login from "../views/Login";

import UserList from "../views/user/List";
import UserProfile from "../views/user/Profile";

Vue.use(VueRouter);

export default new VueRouter({
  routes:[
    {
      path: "/main",
      component: Main,
      //嵌套路由
      children: [
        {
          path: "/user/profile",
          component: UserProfile,
        },
        {
          path: "/user/list",
          component: UserList,
        }
      ]
    },
    {
      path: "/login",
      component: Login,

    }
  ]
})
```





# 参数传递以及重定向

前端传递参数

路由器接受参数

后台组件拿取参数







# 404和路由钩子

路由模式有两种

hash: 路径带#符号，如http://localhost/#/login 

history:路径不带#符号,如http://localhost/login





## 路由钩子与异步请求

beforeRouteEnter: 在进入路由前执行

beforeRouteLeave:在离开路由前执行





安装axios