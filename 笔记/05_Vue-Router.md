## Vue-Router

### 1.简介

- SPA：single page web application

  > 整个应用只有一个页面，导航链接**不会刷新**页面，只会做**局部更新**，数据请求通过ajax

- 路由：一组映射关系 key-value

  > 这里的 key：路径 value：展示的组件（在后端中是function）

### 2.基本使用

1. 安装：`npm i vue-router@3`

   > 现在vue-router的默认版本是4，若要在vue2项目中使用，必须指定路由版本为3，在vue3中则使用4版本

2. 在 scr/router/index.js 下编写路由器配置：

   ```js
   //引入VueRouter
   import VueRouter from "vue-router";
   //引入组件
   import About from "../components/About.vue";
   import Home from "../components/Home.vue";
   //创建路由实例对象，管理路由规则
   export default new VueRouter({
     routes: [
       {
         path: '/about',
         component: About,
       },
       {
           path:'/home',
           component:Home
       }
     ],
   });
   ```

3. 在 main.js 中引入`VueRouter`和编写的路由器，然后应用插件和配置连路由器

   ```js
   ......
   //引入VueRouter
   import VueRouter from 'vue-router'
   //引入路由器
   import router from './router'
   //应用插件
   Vue.use(VueRouter)
   
   //创建vm
   new Vue({
   	el:'#app',
   	render: h => h(App),
   	router:router //配置路由器
   })
   ```

4. 实现切换路由

   > 当点击router-link时，会切换到`to`属性指定的`/xxx`路径，然后把那个路径对应的组件展示到指定位置
   > Tip：active-class 当前组件被激活时添加该样式，不激活时则去除

   ```vue
   <router-link class="list-group-item" active-class= "active" to="/about">About</router-link>
   <router-link class="list-group-item" active-class= "active" to="./home">Home</router-link>
   ```

5. 指定展示位置（占位）

   ```vue
   <router-view></router-view>
   ```

### 3.几个注意点

- 路由组件通常放在`src/page`文件夹，一般组件放在`component`文件夹
- 通过切换路径，隐藏的路由组件，默认被销毁掉，需要切换回来的时候重新挂载
- 每个组件身上都有自己的`$route`属性，用于存储自己的路由信息
- 整个应用只有一个`router`，组件可以通过$router获取到

### 4.多级路由

- 定义：在某个路由组件中再配置其他路由组件(嵌套)

- 使用：在其父路由中的`children`配置项添加子路由组件的规则

  > 嵌套路由的path不能写'/' !!

  ```js
  ......
  routes: [
      {
        path: "/home",
        component: Home,
        children: [
          {
            path: "news",
            component: News,
          },
        ],
      },
    ],
  ```

- 跳转（一定记得写完整路径）

  ```vue
   <router-link active-class= "active" to="/home/news">News</router-link>
  ```

### 5.路由中的query参数

1. 传递参数：两种写法

   - 跳转并携带query参数，to的字符串写法

     > to要加双引号，里面是JS表达式，里面要用传参所以还要用模板字符串

     ```vue
     <router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`">跳转</router-link>
     ```

   - 跳转并携带query参数，to的对象写法

     ```vue
     <router-link :to="{
           path:'/home/message/detail',
           query:{
             id:m.id,
             title:m.title,
           }
     }">跳转</router-link>
     ```

2. 接收参数

   ```vue
   $route.query.id
   $route.query.title
   ```

### 6.路由的params参数

1. 配置路由：提前在path声明接收params参数

   ```js
   path:'detail/:id/:title'
   ```

2. 传递参数

   - 字符串写法

     ```vue
     <router-link :to="/home/message/detail/12345/YourTitle`">跳转</router-link>
     ```

   - 对象写法（**不能用path**，必须**用name**）

     ```vue
     <router-link :to="{
           name:'提前配置的name'
           params:{
             id:12345,
             title:YourTitle,
           }
     }">跳转</router-link>
     ```

3. 接收参数

   ```vue
   $route.params.id
   $route.params.title
   ```

   

### 7.命名路由

- 作用：简化路由跳转时要写完整的路径

- 使用方法

  1. 给路由命名：直接在路由的配置项里加一个name属性即可

     ```js
     route:[
     	name:'YourNmae',
     	path:'/home',
     	......
     ]
     ```

  2. 跳转路由：必须把to写成对象形式

     ```
     <router-link :to="{name:'Yourname'}">跳转<router-link>
     ```

### 8.路由中的props配置

- 作用：让路由组件更方便收到参数

- 配置路由：三种写法(在store/index.js文件)

  1. props值为对象

     > 缺点：数据是不可变的

     ```js
     props:{id:12345,title:'name'}
     ```

  2. props值为布尔值

     > ture:路由收到的所有**param**参数传给组件。缺点：不能收query参数

     ```js
     props:true
     ```

     > 路由组件接收：props：[id]，//此处 `id = $route.params.id`

  3. props值为函数**（最常用）**

     > 函数返回的每一组key-value通过props传给Detail组件

     ```js
     path:'detail',
     component:Detail,
     props($route){
         return { id:$route.query.id,title:$route.query.title}
     }
     ```

### 9.replace属性

- 作用：控制路由跳转时操作浏览器的历史记录模式
- 浏览器的历史记录模式
  1. push：追加历史记录，默认
  2. replace：覆盖历史记录
- 开启replace模式：`<router-link replace ....></router-link>`

### 10.编程式路由导航

- 作用：不借助`<router-link>`实现路由跳转，让路由跳转更灵活

- 五个常用API

  ```js
  //追加式跳转
  this.$router.push({
      path: "/home/message/detail",
      query: {
        id: m.id,
        title: m.title,
      },
    });
  //覆盖式跳转
  this.$router.replace
  //往前跳转
  this.$router.forward()
  //向后跳转
  this.$router.back()
  //指定跳转步数 eg：-2表示往后跳转两步
  this.$router.go(-2)
  ```

### 11.缓存路由组件

- 作用：让不展示的路由组件保持挂载，不被销毁，比如输入框，当前组件被跳转走后，返回来时仍然存有输入的内容

- 具体使用：

  > include里包含的是你想挂载的组件名

  ```vue
  <keep-alive :include="['News','其他路由组件']">
  	<router-view></router-view>
  </keep-alive>
  ```

### 12.两个新生命周期

- 路由组件独有的两个生命周期钩子，用于捕获路由的激活状态，在切换路由的时候做一些工作
- `activated`路由组件被激活时触发
- `deactivated`路由组件失活时触发

### 13.路由守卫

> 在切换路由之前/之后做一些工作，主要是进行**权限校验**

#### 13.1.全局路由守卫

- 全局前置守卫：初始化、切换路由前执行

  > 回调里的三个参数 to:切换到的路由 from：引起切换的路由 next：继续执行函数
  > 注意：下面的**meta**是路由的一个配置项，它允许程序员在meta对象里添加一些信息，比如isAuth：是否需要权限校验

  ```js
  const router = new VueRouter({ ...})
  router.beforeEach((to,from,next)=>{
      if(to.meta.isAuth){ //如果需要校验
          if(localStorage.getItem('name')==='maishixu'){
              next()
          }else{
              alert('无权限查看')
          }
      }else{ //不需要校验
          next()
      }
  })
  ```

- 全局后置守卫：初始化、切换路由后执行

  > 配置路由后修改页签名字，若当前路由没有页签名则默认为'首页'

  ```js
  router.afterEach((to,from)=>{
      document.title = to.meta.title || '首页'
  })
  export default router
  ```


#### 13.2.独享路由守卫

- 直接在某个路由配置项中配置，只管理某个路由初始化、路由切换前的工作

  ```js
  beforeEnter((to,from,next)=>{
      if(to.meta.isAuth){ //如果需要校验
          if(localStorage.getItem('name')==='maishixu'){
              next()
          }
      }else{ //不需要校验
          next()
      }
  })
  ```

  > 注意：没有 afterEnter

#### 13.3.组件内路由守卫

- 通过路由规则，进入该组件时被调用

  ```vue
  beforeRouteEnter(to,from,netx){ ... }
  ```

- 通过路由规则，离开该组件时被调用

  ```vue
  beforeRouteLeave(to,from,netx){ ... }
  ```


### 14.路由器的两种工作模式

> 对于一个url来说，什么是hash值？--- '#'及后面的的内容
> 作用：hash值不会随http请求带给服务器

#### 14.1.hash模式

- 如果是路由组件，则通过#来分割，以防浏览器引起歧义，出现404的问题
- 缺点：
  1. 地址中带着'#'，不美观
  2. 若以后降低至通过第三方app分享，如果app校验严格，则地址则会被标记为不合法
- 优点：兼容性好

#### 14.1.history模式

- 没有#，会出现404的问题

- 优点：地址干净，美观，没有 #

- 缺点：兼容性较差

- 这种模式是开发中常用的，但如何解决路径404问题？

  > 应用部署上线时，后端工程师应该解决的问题

