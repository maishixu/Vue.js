## Vue组件化编程

定义：实现应用中局部功能**代码**(html、css、JS）和**资源**(mp3、.zip、ttf)的**集合**
优势：复用代码，代码依赖关系清晰，方便维护

### 一、非单文件组件

> 一个文件中包含n个组件

#### 1.基本使用

##### 1.1.定义组件

- 组件中的el无需定义，最终由vm决定服务哪个容器

- data必须定义为函数的形式，若定义为对象形式，组件复用时，数组修改时存在互相影响

- 使用template配置组件结构

  ```js
  const school = Vue.extend({
    template:`
      <div>
          <h1>{{school}}</h1>    
          <h1>{{address}}</h1>    
      </div>
    `,
    data() {
      return {
        school: "山东大学",
        address: "青岛",
      };
    },
  });
  ```
  
- 简写形式：`const school = { options }`

##### 1.2.注册组件

- 局部注册（更常用）

  ```js
  const vm = new Vue({
    el: "#root", //代理
    components: {
      school,
    },
  });
  ```

- 全局注册

  ```js
  Vue.component("school",school);
  ```


##### 1.3.使用组件

- 在html中引入组件标签

  ```html
  <div id="root">
      <school></school> <!-- 组件标签 -->
  </div>
  ```

#### 2.组件名

- 单个单词组成：正常大小写都可以
- 多个单词组成
  1. 用斜杠隔开`kebab-case`
  2. 大驼峰命名法`CamelCase`（需在Vue脚手架环境下）
- 组件标签
  1. `<tag></tag>`
  2. `<tag/>`（需在Vue脚手架环境下）
- 备注：可以在组件配置项中定义`name`属性指定开发者工具中的组件名

#### 3.组件的嵌套

- 创建一个student组件

  ```js
  //创建一个student组件
  const student = Vue.extend({
    template: `
      <div>
          <h1>{{name}}</h1>    
          <h1>{{age}}</h1>    
      </div>
    `,
    data() {
      return { name: "李华", age: 18 };
    },
  });
  ```

- 在school组件中注册student组件，完成组件嵌套

- 值得注意的是：student组件标签只能在school组件模板中使用

  ```js
  const school = Vue.extend({
    template: `
      <div>
          <h1>{{school}}</h1>    
          <h1>{{address}}</h1>  
          <student></student>  
      </div>
    `,
    components:{ student, },
    data() {
      return { school: "山东大学", address: "青岛" };
    },
  });
  ```

- 通常，我们在开发中，会定义一个app组件：掌管所有组件，而它由vm掌管（一个之下，万人之上）

#### 4.VueComponent

**组件归根结底就是VueComponent的实例对象！**

- 组件本质是一个`VueComponent`构造函数，由`Vue.extend`生成

- 当我们编写html标签时，Vue在解析的过程中会帮我们创建组件的实例对象

  > 即执行：`new VueComponent(options)`

- 每次调用`Vue.extend`时，返回的都是一个全新的`VueComponent`，**对于每个组件标签，组件实例都是互相独立的**

- 关于`this`指向

  1. new VueComponent组件配置中:

     >data函数、methods、watch、computed中的函数this均指向【VueComponent(vc)实例对象】

  2. new Vue配置中：

     >data函数、methods、watch、computed中的函数this均指向【vm实例对象】

#### 5.重要内置关系

- 先识：实例的原型属性(隐式)是其缔造者的原型对象(显示)
- 内置关系：**VueComponent.prototype._proto__ === Vue.prototype**
- 目的：让组件实例对象可以访问到Vue原型上的属性、方法

<img src="D:\WeChat Files\wxid_mjdtvhaj637c22\FileStorage\Temp\20c12337248366e34295e5d8e773646.png" alt="20c12337248366e34295e5d8e773646" style="zoom:50%;" />

- 看到这里我的疑问：为什么不将VueComponent.prototype直接等于Vue.prototype

  > 我的理解，因为VueComponent的原型对象上也要有自己与Vue原型对象所不同的东西，所以不能简单的全等

### 二、单文件组件

> 一个文件中只包含1个组件；文件后缀名.vue；用的更多，好维护。

#### 1.安装Vue脚手架

- 步骤
  1. 更换npm镜像：`npm config set registry https://registry.npm.**taobao.org`**
  2. 临时禁用SSL：`npm config set strict-ssl false`
  3. 全局安装：`npm install -g @vue/cli`
  4. 创建项目：`vue create vue-cli-test`
  5. 在VScode中打开，启动项目：`npm run serve`
  
- 脚手架文件结构

  ```json
  ├── node_modules 
  ├── public
  │   ├── favicon.ico: 页签图标
  │   └── index.html: 主页面
  ├── src
  │   ├── assets: 存放静态资源
  │   │   └── logo.png
  │   │── component: 存放组件
  │   │   └── HelloWorld.vue
  │   │── App.vue: 汇总所有组件
  │   │── main.js: 入口文件
  ├── .gitignore: git版本管制忽略的配置
  ├── babel.config.js: babel的配置文件
  ├── package.json: 应用包配置文件 
  ├── README.md: 应用描述文件
  ├── package-lock.json:包版本控制文件
  ├── vue.config.json:配置文件
  ```

- vue.config.js

  1. 使用命令`vue inspect > output.js`查看Vue脚手架的默认配置
  2. 使用vue.config.js修改默认配置（CommonJS模块化），详情见：https://cli.vuejs.org/zh
  
- 脚手架做什么？

  > 依托于webpack将我们写的代码(.vue)打包起来成浏览器能编译的语言（js、css、html）

#### 2.基本使用

##### 2.1.子组件

- School.vue

  > 放在`.src/components`目录下

  ```html
  <template>
    <!-- 组件的结构 -->
    <div>
      <h2 class="demo">学校：{{ name }}</h2>
      <h2>地址：{{ address }}</h2>
    </div>
  </template>
  
  <script>
  // 组件交互相关的代码
  export default { //默认暴露 组件的简写形式
    name: "School", //组件名
    data() {
      return { name: "山东大学", address: "青岛", };
    },
  };
  </script>
  
  <style>
  /* 组件的样式 */
  .demo{
    background-color: #bfa;
  }
  </style>
  ```

- Student.vue

  ```html
  <template>
    <div>
      <h2>学生姓名：{{ name }}</h2>
    </div>
  </template>
  <script>
  export default {
    name: "Student",
    data() {
      return { name: "李华", };
    },
  };
  </script>
  <style></style>
  ```

##### 2.2.父组件

- App.vue

  > 放在项目`src`目录下

  ```html
  <template>
    <div>
      <School></School>
      <Student></Student>
    </div>
  </template>
  
  <script>
  //引入子组件
  import School from "./components/School";
  import Student from "./components/Student";
  export default {
    components: {
      School,
      Student,
    },
  };
  </script>
  <style></style>
  ```

##### 2.3.入口文件

- main.js

  > 放在`src`目录下

  ```js
  import Vue from 'vue' //ES6模块化语法 在package.json中module配置了，引入的是vue.runtime.xxx.js
  import App from './App.vue' //引入父组件
  
  Vue.config.productionTip = false
  
  new Vue({
    el:"#App"
    render: h => h(App), //将app放入容器中，这段代码实现的功能相当于↓
    /* template:`<App></App>`
    	 components:{App} */
  })
  ```
  
- 关于不同版本的vue

  1. vue.js:完整版（包含核心＋模板解析器）

  2. vue.runtime.xxx.js:运行版（只包含核心）

    > 不能使用template配置项，需要用render收到的createElement函数指定渲染的具体内容

- 值得**注意**的是：**组件的模版**（template）**可以解析**，只有**`main.js`中的模板不能解析**

##### 2.4.模板文件

- index.html

  > 准备容器

  ```html
  <!DOCTYPE html>
  <html lang="">
    <head>
      <meta charset="utf-8">
      <!-- 针对IE浏览器的特殊配置，让IE以最高级别渲染页面 -->
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <!-- 开启移动端的理想视口 -->
      <meta name="viewport" content="width=device-width,initial-scale=1.0">
      <!-- 配置页签图标 -->
      <link rel="icon" href="<%= BASE_URL %>favicon.ico">
      <!-- 配置网页标题 -->
      <title><%= htmlWebpackPlugin.options.title %></title>
    </head>
    <body>
      <!-- 当浏览器不支持JS时，noscript标签中的内容就会渲染 -->
      <noscript>
        <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
      </noscript>
      <!-- 容器 -->
      <div id="App"></div>
      <!-- built files will be auto injected -->
    </body>A
  </html>
  ```


#### 3.ref属性

- 用来给元素或子组件注册引用信息（id的替代者)

- 要点：用在html标签上获取到的是DOM元素，用在组件标签上获取的是实例对象（VC）

  ```html
  <!-- 模板 -->
  <h1 ref = "demo">Hello_World<h1>
  <School ref = "demo2"></School>
  <School id = "demo3"></School>
  <!-- 组件 -->
  this.$refs.demo //获取到的是：<h1 ref = "demo">Hello_World<h1>
  this.$refs.demo2 //获取到的是School组件实例对象
  this.$refs.demo3 //获取到的是School标签（DOM元素）
  ```

#### 4.props配置

- prop（翻译：属性）

  > 功能：让组件接收外部传过来的数据

- 传递数据

  ```html
  <Student name="Frank" :age="18"></Student> 
  ```

  > 如果在数据名前加`:`那么`""`里的是表达式，而不是简单的字符串了

- 接收数据

  1. 简单接收（全部当作字符串处理）

     ```js
     props:['name','age']
     ```

  2. 限制类型

     ```js
     props: { name: String, age: Number }
     ```

  3. 限制类型、必要性、默认值

     ```js
     props: {
         name: { type: String, Required: true }, //必要
         age: { type: Number, default: 18 }, //默认18
     },
     ```

- 注意：

  1. props是只读的，若要修改需要复制到data中使用（进行修改再呈现到模板中）
  2. v-model绑定的值不能是props传过来的值，因为v-model是双向绑定，会修改props
  3. 若props传过来的是对象，虽然修改对象中的属性不会报错，但不推荐这样做


#### 5.mixin混合

- 功能：把多个组件共用的配置提取成一个混合对象

- 使用方式

  1. 定义：

     > 一个共用'提醒'方法，写在和main.js同级的目录下

     ```js
     export const remind = { //分别暴露（ES6）
       methods: {
         showHello() { alert("Hello"); },
       },
     };
     ```

  2. 引入：（在组件中引入）

     ```js
     import { remind } from '../remind';
     mixins:[remind] //在配置项中配置
     ```

     > 若要全局混入：`Vue.mixin(xxx)` 在main.js中

  3. 使用：至此，组件中就多了一个`sayHello`方法

     ```html
     <h2 @click="showHello">学校：{{ name }}</h2> <!-- 点击学校，弹出Hello -->
     ```

#### 6.插件

- 功能：类似Express里的中间件，用于增加Vue的功能

- 本质：包含install方法的对象，install第一个参数：Vue、第二个参数：插件使用者传递的参数

- 定义插件

  ```js
  const plugin = {install(vue,x,y,x)} //然后暴露出去使用
  ```

- 使用插件

  ```vue
  Vue.use()
  ```


#### 7.scoped样式

- 本来样式在哪个组件中写都可以全局生效，scoped可以让样式在局部生效，防止冲突
- 写法：`<style scoped>`

#### 8.浏览器本地存储

- 大小一般为5MB左右
- 浏览器通过`Window.sessionStorage`和`Window.localStorage`属性实现本地存储机制
- 相关API

  1. `.setItem('key','value)`添加键值对，若已存在，则更新
  2. `.getItem('key','value)`读取，若为空，返回null，为空的话JSON.parse的结果依然是null
  3. `.removeItem('key','value)`删除
  4. `.clearItem('key','value)`清空
- 区别
  1. sessionStorage：一次会话有效（浏览器窗口关闭）
    2. localStorage：需要手动清除


#### 9.TodoList案例

- 组件化的编码流程

  1. 拆分静态组件：按照功能点划分，命名不与html元素冲突

  2. 实现动态组件：考虑数组的存放位置

     > 数据只有一个组件在用：放在组件自身即可
     > 数据有很多组件在共用：放在共同的父组件（**状态提升**）

  3. 实现交互：从绑定事件开始

- props适用于：

  1. 父组件 -> 子组件 通信
  2. 子组件 -> 父组件 通信（要求 父 -> 子：一个函数）

- JavaScript中对象是通过引用传递的，而不是副本传递，所以：通过forEach方法遍历对象数组会成功修改数组中的对象

- Array.reduce方法

  > 传入的第一个参数是函数，第二参数是pre的初始值。
  > 传入函数第一个参数是上一次的返回值，第二个参数是当前遍历数组的实例对象

  ```js
  this.todos.reduce((pre, todo) => {
  	return pre + (todo.done ? 1 : 0); //累加符合条件的数组个数
  },0); //0是初始值
  ```


#### 10.自定义事件

- 子给父传递数据的另一种方式：通过父组件给子组件绑定一个自定义事件实现（**事件的回调在父组件中！**）

- 绑定方式

  1. 在父组件中：`<Demo @MyselfEvent="test"/>`

  2. 在父组件中：

     ```js
     <Demo ref = "demo"/>
     .....
     mounted(){
     	this.$refs.demo.$on('MyselfEvent',this.test)
     }
     ```

  3. 若事件只想触发一次，使用once修饰符

- 触发事件：`this.emit('MyselfEvent',参数)`

- 解绑

  1. 单个解绑:`this.$off('MyselfEvent')`
  2. 多个解绑:`this.$off(['MyselfEvent','other'])`
  3. 解绑全部:`this.$off`

- 注意：使用第二种方式绑定自定义事件时，要配置方法在methods中，否则this指向会出问题，要么用箭头函数

#### 11.全局事件总线

- 也是一种组件间通信的方式，适用于任意组件间的通信

- 使用步骤：

  1. 安装全局事件总线

     ```js
     new Vue(){
     	......
     	beforeCreated(){
     		Vue.prototype.$bus = this //往Vue原型上添加，则根据我们之前讲的VC和Vue的内置关系，所有的组件都能访问到这个$bus，然后关键是把bus赋值成一个具有$on $emit $off 属性的对象
     	}
     }
     ```

  2. 使用全局事件总线：

     i.绑定事件（需要接收数据的一方，**事件回调留在自身组件上**）

     ```js
     methods(){
         demo(data){ ... }
     }
     mounted(){
     	this.$bus.$on('xxx',this.demo) //触发'xxx'事件后，会执行this.demo函数，一般设置同名即可
     }
     ```

     > 这里值得注意的点：this.demo可以直接写成函数，但是，必须写成箭头函数箭头函数没有自己的this，会去外部找到接受组件作为this，若写成普通函数，则this会指向触发事件的组件（发送数据的一方）

     ii.触发事件（发送数据的一方）

     ```js
     this.$bus.$emit('xxx',数据)
     ```

  3. 在组件beforeDestroy钩子中解绑组件（接受数据的组件）用到的事件
  
     ```js
     this.$bus.$off('xxx')
     ```
  
     

#### 12.消息订阅与发布

- 一种组件间通信的方式，适用于任意组件间的通信

- 使用步骤：

  1. 安装`npm i pubsub-js`

  2. 引入`import pubsub from 'pubsub-js'`

  3. 订阅消息（接受数据的一方）

     ```js
     methods(){
     	demo(msgName,data){ ... }//注意，这里必须有两个参数，前一个占位（消息名），后一个才是收到的数据
     }
     ...
     mounted(){
     	this.pid  = pubsub.scribe('消息名',this.demo)
     }
     ```

  4. 发送消息（提供数据的一方）：`pubsub.publish('消息名',this.data)`

  5. 在beforeDestroy钩子中，用`PubSub.unsubscribe(pid)`取消订阅

#### 13.nextTick

- 作用：在下一次DOM更新结束后执行其指定的回调函数

- 语法：`this.$nextTick(回调函数)`

- 什么时候用？

  > 当改变数据后，要基于更新后的新DOM进行某些操作时，要在nextTick所指定的回调函数中执行

#### 14.动画和过渡

- 作用：在插入、更新或移除DOM元素时，在合适的时候给元素添加类名

- 图示：

  <img src="C:\Users\Mason\AppData\Roaming\Typora\typora-user-images\image-20241118141730188.png" alt="image-20241118141730188" style="zoom:51%;" />

##### 14.1.动画效果

- 动画效果写法：

  1. hello-enter-active 进入时的样式
  2. hello-leave-active 离开时的样式

  ```html
  <template>
    <div>
      <button @click="isShow = !isShow">显示/隐藏</button>
      <!-- 添加appear属性使一开始就触发动画效果 -->
      <Transition name="hello" :appear="true">
        <!-- 当状态变化为显示时自动添加v-enter-active类，变化为3隐藏时自动添加v-leave-active类 -->
        <h1 v-show="isShow">Vue_Animation</h1>
      </Transition>
    </div>
  </template>
  
  <script>
  export default {
    name: "MyAnimation",
    data() {
      return { isShow: true,};
    },
  };
  </script>
  
  <style scoped>
  h1 { background-color: #bfa; }
  .hello-enter-active { animation: MyTest 1s; }
  .hello-leave-active {
    animation: MyTest 1s reverse;  /* reverse：反转 */
  }
  @keyframes MyTest {
    from {
      transform: translateX(-100%);
    }
    to {
      transform: translateX(0);
    }
  }
  </style>
  ```

##### 14.2.过渡效果

- 过渡效果写法：

  1. 多个元素过渡必须指定key
  2. hi-enter,.hi-enter-to 进入的起点终点

  ```html
  <template>
      <div>
        <button @click = "isShow = !isShow">显示/隐藏</button>
        <!-- 多个元素过渡 -->
        <Transition-group name="hi" :appear="true">
          <h1 v-show="isShow" key="1">Vue_Transition</h1>
          <h1 v-show="isShow" key="2">Vue_Transition_Pro</h1>
        </Transition-group>
      </div>
  </template>
    
  <script>
    export default {
      name: 'MyTransition',
      data(){
        return { isShow:true, }
      }
    }
  </script>
    
  <style scoped>
      h1{
        background-color: #bfa;
      }
      /* 来的起点、离开终点 */
      .hi-enter,.hi-leave-to{
          transform: translateX(-100%);
      }
      /* 过渡时间 */
      .hi-enter-active,.hi-leave-active{
          transition: 1s;
      }
      /* 离开起点、来的终点 */
      .hi-leave,.hi-enter-to{
          transform: translateX(0);
      }
  </style>
    
  ```

##### 14.3.第三方库动画效果：

- 网址：https://animate.style/

- 写法：

  ```html
  <template>
    <div>
      <button @click="isShow = !isShow">显示/隐藏</button>
      <Transition
        name="animate__animated animate__bounce"
        enter-active-class="animate__backInUp"
        leave-active-class="animate__backOutUp"
        :appear="true"
      >
        <h1 v-show="isShow">Vue_Animation</h1>
      </Transition>
    </div>
  </template>
  
  <script>
  //引入第三方动画库
  import "animate.css";
  export default {
    name: "MyTest",
    data() {
      return { isShow: true,};
    },
  };
  </script>
  
  <style scoped>
  h1 {
    background-color: #bfa;
  }
  </style>
  ```

  

  
