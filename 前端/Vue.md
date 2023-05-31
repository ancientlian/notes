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





# 





# 判断循环

if-else

for





# 事件处理

示例：

```html
<div id="example-2">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
</div>
```

```js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
})

// 也可以用 JavaScript 直接调用方法
example2.greet() // => 'Hello Vue.js!'
```



# 数据绑定



## v-bind 单项绑定



## v-model 双向绑定

Vue.js是一个MVVM框架，即数据双向绑定，即当数据发生变化的时候，视图也就发生变化，当视图发生变化的时候，数据也会跟着同步变化。这也算是Vue.js的精髓之处了。

值得注意的是，我们所说的数据双向绑定，一定是对于UI控件来说的，非UI控件不会涉及到数据双向绑定。单向数据绑定是使用状态管理工具的前提。如果我们使用`vuex` ，那么数据流也是单项的，这时就会和双向数据绑定有冲突。

v-model只能用在表单类元素（输入类元素上）

数据不仅能从data流向页面，还可以从页面流向data。

备注：

1. 双向绑定一般都应用在表单类元素上（如：input、select等）
2. v-model:value 可以简写为 v-model，因为v-model默认收集的就是value值。

# el与data的两种写法

```html
<body>
  <!-- 准备好一个容器-->
  <div id="root">
   <h1>你好，{{name}}</h1>
  </div>
 </body>

 <script type="text/javascript">
  Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。

  //el的两种写法
  /* const v = new Vue({
   //el:'#root', //第一种写法
   data:{
    name:'尚硅谷'
   }
  })
  console.log(v)
  v.$mount('#root') //第二种写法 */

  //data的两种写法
  new Vue({
   el:'#root',
   //data的第一种写法：对象式
   /* data:{
    name:'尚硅谷'
   } */

   //data的第二种写法：函数式
   data(){
    console.log('@@@',this) //此处的this是Vue实例对象
    return{
     name:'尚硅谷'
    }
   }
  })
 </script>
```

带$符的属性是抛出以供开发者使用.

未带的属性是Vue自己使用,底层使用的.

由Vue管理的函数，一定不要写箭头函数，一旦写了箭头函数，this就不再是Vue实例了





# MVVM模型

![img](https://img-blog.csdnimg.cn/img_convert/ac43527b06defd324a702aea4f7940a0.png)



- MVVM模型：
  - M：模型（Model），data中的数据
  - V：视图（View），模板代码
  - VM：视图模型（ViewModel），Vue实例

- data中所有的属性，最后都出现在了vm身上
- vm身上所有的属性 及 Vue原型身上所有的属性，在Vue模板中都可以直接使用



# Vue中的数据代理

**总结：**

1. Vue中的数据代理通过vm对象来代理data对象中属性的操作（读/写）
2. Vue中数据代理的好处：更加方便的操作data中的数据
3. 基本原理：
   - 通过`object.defineProperty()`把data对象中所有属性添加到vm上。
   - 为每一个添加到vm上的属性，都指定一个getter/setter。
   - 在getter/setter内部去操作（读/写）data中对应的属性。







# 事件处理

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>事件的基本用法</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h2>hello,{{name}}</h2>
        <button v-on:click="showInfo1">点我提示信息1</button>
        <button @click="showInfo2($event,66)">点我提示信息2</button>
    </div>

    <script>
        Vue.config.productionTip = false 
        new Vue({
            el:'#root', 
            data:{
                name:'JOJO'
            },
            methods:{
                showInfo1(event){
                    console.log(event)
                },
                showInfo2(evnet,num){
                    console.log(event,num)
                }
            }
        })
    </script>
</body>
</html>

```

**总结：**

1. 使用`v-on:xxx`或`@xxx`绑定事件，其中xxx是事件名
2. 事件的回调需要配置在methods对象中，最终会在vm上
3. ==methods中配置的函数，不要用箭头函数！否则this就不是vm了==，从vm变成了window
4. methods中配置的函数，都是被Vue所管理的函数，this的指向是vm或组件实例对象
5. `@click="demo"`和`@click="demo($event)"`效果一致，但后者可以传参



## 事件修饰符

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>事件修饰符</title>
		<script type="text/javascript" src="../js/vue.js"></script>
		<style>
			*{
				margin-top: 20px;
			}
			.demo1{
				height: 50px;
				background-color: skyblue;
			}
			.box1{
				padding: 5px;
				background-color: skyblue;
			}
			.box2{
				padding: 5px;
				background-color: orange;
			}
			.list{
				width: 200px;
				height: 200px;
				background-color: peru;
				overflow: auto;
			}
			li{
				height: 100px;
			}
		</style>
	</head>
	<body>
		<div id="root">
			<h2>欢迎来到{{name}}学习</h2>
			<!-- 阻止默认事件 -->
			<a href="http://www.atguigu.com" @click.prevent="showInfo">点我提示信息</a>

			<!-- 阻止事件冒泡 -->
			<div class="demo1" @click="showInfo">
				<button @click.stop="showInfo">点我提示信息</button>
			</div>

			<!-- 事件只触发一次 -->
			<button @click.once="showInfo">点我提示信息</button>

			<!-- 使用事件的捕获模式 -->
			<div class="box1" @click.capture="showMsg(1)">
				div1
				<div class="box2" @click="showMsg(2)">
					div2
				</div>
			</div>

			<!-- 只有event.target是当前操作的元素时才触发事件 -->
			<div class="demo1" @click.self="showInfo">
				<button @click="showInfo">点我提示信息</button>
			</div>

			<!-- 事件的默认行为立即执行，无需等待事件回调执行完毕 -->
			<ul @wheel.passive="demo" class="list">
				<li>1</li>
				<li>2</li>
				<li>3</li>
				<li>4</li>
			</ul>

		</div>
	</body>

	<script type="text/javascript">
		Vue.config.productionTip = false

		new Vue({
			el:'#root',
			data:{
				name:'尚硅谷'
			},
			methods:{
				showInfo(e){
					alert('同学你好！')
				},
				showMsg(msg){
					console.log(msg)
				},
				demo(){
					for (let i = 0; i < 100000; i++) {
						console.log('#')
					}
					console.log('累坏了')
				}
			}
		})
	</script>
</html>

```

**总结：**

Vue中的事件修饰符：

1. prevent：阻止默认事件（常用）
2. stop：阻止事件冒泡（常用）
3. once：事件只触发一次（常用）
4. capture：使用事件的捕获模式
5. self：只有`event.target`是当前操作的元素时才触发事件
6. passive：事件的默认行为立即执行，无需等待事件回调执行完毕

> 修饰符可以连续写，比如可以这么用：`@click.prevent.stop="showInfo"`



## 键盘事件

**总结：**

> 键盘上的每个按键都有自己的名称和编码，例如：Enter（13）。而Vue还对一些常用按键起了别名方便使用

Vue中常用的按键别名：

- 回车：enter
- 删除：delete (捕获“删除”和“退格”键)
- 退出：esc
- 空格：space
- 换行：tab (特殊，必须配合keydown去使用)
- 上：up
- 下：down
- 左：left
- 右：right

**注意：**

1. 系统修饰键（用法特殊）：ctrl、alt、shift、meta
   - 配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发
   - 配合keydown使用：正常触发事件
2. 可以使用keyCode去指定具体的按键，比如：`@keydown.13="showInfo"`，但不推荐这样使用
3. `Vue.config.keyCodes.自定义键名 = 键码`，可以自定义按键别名





# 计算属性

官方文档里说了，表达式太复杂就要用计算属性



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>计算属性</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        姓：<input type="text" v-model="firstName"><br><br>
        名：<input type="text" v-model="lastName"><br><br>
        姓名：<span>{{fullName}}</span>
    </div>

    <script>
        Vue.config.productionTip = false 

        new Vue({
            el:'#root', 
            data:{ 
                firstName:'张',
                lastName:'三'
            },
            computed:{
                fullName:{
                    get(){
                        return this.firstName + '-' + this.lastName
                    },
                    set(value){
						const arr = value.split('-')
						this.firstName = arr[0]
						this.lastName = arr[1]
                    }
                }
            }
        })
    </script>
</body>
</html>

```



**总结：**

- 计算属性：
  - 定义：**要用的属性不存在，需要通过已有属性计算得来**。
  - 原理：底层借助了`Objcet.defineproperty()`方法提供的getter和setter。
  - get函数什么时候执行？
    1. ==初次读取时会执行一次==
    2. ==当依赖的数据发生改变时会被再次调用==
  - 优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便
- 备注：
  - 计算属性最终会出现在vm上，直接读取使用即可
  - 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变
  - 如果计算属性确定不考虑修改，可以使用计算属性的简写形式

简写形式

```js
new Vue({
    el:'#root', 
    data:{ 
        firstName:'张',
        lastName:'三'
    },
    computed:{
    	fullName(){
		    return this.firstName + '-' + this.lastName
    	}
    }
})

```



# 监视属性

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>监视属性</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h2>今天天气好{{info}}!</h2>
        <button @click="changeWeather">点击切换天气</button>
    </div>

    <script>
        Vue.config.productionTip = false 

        new Vue({
            el:'#root', 
            data:{ 
                isHot:true,
            },
            computed:{
                info(){
                    return this.isHot ? '炎热' : '凉爽' 
                }
            },
            methods:{
				changeWeather(){
					this.isHot = !this.isHot
				}
			},
            watch:{
                isHot:{
                    immediate:true, //初始化时让handler调用一下
                    //handler什么时候调用？当isHot发生改变时
                    handler(newValue,oldValue){
						console.log('isHot被修改了',newValue,oldValue)
					}
                }
            }
        })
    </script>
</body>
</html>

```

**总结：**

监视属性watch：

1. 当被监视的属性变化时，回调函数自动调用，进行相关操作
2. 监视的属性必须存在，才能进行监视
3. 监视有两种写法：
   1. 创建Vue时传入watch配置
   2. 通过`vm.$watch`监视

```js
vm.$watch('isHot',{
	immediate:true,
	handler(newValue,oldValue){
		console.log('isHot被修改了',newValue,oldValue)
	}
})
```



## 深度监视

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>深度监视</title>
    <script src="../js/vue.js"></script>
</head>
<body>
    <div id="root">
        <h3>a的值是:{{numbers.a}}</h3>
		<button @click="numbers.a++">点我让a+1</button>
		<h3>b的值是:{{numbers.b}}</h3>
		<button @click="numbers.b++">点我让b+1</button>
    </div>

    <script>
        Vue.config.productionTip = false 

        new Vue({
            el:'#root', 
            data:{ 
                isHot:true,
                numbers:{
                    a:1,
                    b:1,
                }
            },
            watch:{
                //监视多级结构中所有属性的变化
                numbers:{
                    deep:true,
					handler(){
						console.log('numbers改变了')
					}
                }
                //监视多级结构中某个属性的变化
				/* 'numbers.a':{
					handler(){
						console.log('a被改变了')
					}
				} */
            }
        })
    </script>
</body>
</html>

```

**总结：**

- 深度监视：

1.Vue中的watch默认不监测对象内部值的改变（一层）

2.在watch中配置`deep:true`可以监测对象内部值的改变（多层）

- 备注：

Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以

使用watch时根据监视数据的具体结构，决定是否采用深度监视

## 监视属性简写

```html
<script type="text/javascript">
	Vue.config.productionTip = false //阻止 vue 在启动时生成生产提示。
		
    const vm = new Vue({
        el:'#root',
        data:{
            isHot:true,
        },
        computed:{
            info(){
                return this.isHot ? '炎热' : '凉爽'
            }
        },
        methods: {
            changeWeather(){
                this.isHot = !this.isHot
            }
        },
        watch:{
            //正常写法
            isHot:{
				handler(newValue,oldValue){
					console.log('isHot被修改了',newValue,oldValue)
				}
			}, 
            //简写
            isHot(newValue,oldValue){
				console.log('isHot被修改了',newValue,oldValue,this)
			}
        }
    })

    //正常写法
    vm.$watch('isHot',{
        handler(newValue,oldValue){
            console.log('isHot被修改了',newValue,oldValue)
        }
    })
    //简写
    vm.$watch('isHot',function(newValue,oldValue){
        console.log('isHot被修改了',newValue,oldValue,this)
    })
</script>

```



# 计算属性和监听属性比较

**总结：**

computed和watch之间的区别：

- computed能完成的功能，watch都可以完成
- watch能完成的功能，computed不一定能完成，**例如：watch可以进行异步操作**

两个重要的小原则：

- 所有被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象
- 所有**不被Vue所管理的函数**（定时器的回调函数、ajax的回调函数等、Promise的回调函数），==最好写成箭头函数==，这样this的指向才是vm 或 组件实例对象。





# 绑定样式

**总结：**

class样式：

- 写法：`class="xxx"`，xxx可以是字符串、对象、数组
- 字符串写法适用于：类名不确定，要动态获取
- 对象写法适用于：要绑定多个样式，个数不确定，名字也不确定
- 数组写法适用于：要绑定多个样式，个数确定，名字也确定，但不确定用不用

style样式：

- `:style="{fontSize: xxx}"`其中xxx是动态值
- `:style="[a,b]"`其中a、b是样式对象





# 条件渲染





