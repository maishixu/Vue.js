## Vuex

### 1.简介

- 是什么？

  > **一种任意组件间通信的方式。**
  >
  > 专门在Vue中实现集中式状态（数据）管理的一个Vue插件，对多个组件共享状态进行集中管理（读/写）

- 什么时候用？

  > i. 多个组件依赖同一个状态
  > ii. 来自不同组件的行为需要变更同一状态

### 2.工作原理

- Actions：用于响应组建的动作，需要与后端交互或者其他业务逻辑时使用（服务员）

- Mutaions：真正操作数据；如果不需与后端交互或者其他业务逻辑时，可以直接从组件到Mutation（后厨）

- State：存储状态，即数据

- Store：统一管理Actions、Mutations、State

  > API:dispatch，commit

<img src="C:\Users\Mason\AppData\Roaming\Typora\typora-user-images\image-20241121194111613.png" alt="image-20241121194111613" style="zoom:53.4%;" />

### 3.搭建Vuex环境

- 注意：版本问题

  > Vue2中，要用Vuex3；Vue3中，要用Vuex4。必须严格遵循。

- 使用步骤

  1. 安装:`npm i vuex@3`（必须严格指定版本）

  2. 创建store：两种方法

     - 在src目录下创建vuex文件夹，然后在该文件夹下创建store.js
     - 在src目录下创建store文件夹，然后在该文件夹下创建index.js(官方文档)

  3. 编写store核心文件（index.js这里采用官方写法）

     > 为什么不在main.js中引入和使用Vuex，而在index.js中呢？
     > 因为创建store之前必须先使用Vuex

     ```js
     //引入Vue核心库
     import Vue from 'vue'
     //引入Vuex
     import Vuex from 'vuex'
     //应用Vuex插件
     Vue.use(Vuex)
     
     const actions = {}
     const mutations = {}
     const state = {}
     
     //创建并暴露store
     export default new Vuex.Store({
         actions,
         mutations,
         state
     })
     ```

  4. 在main.js中引入store:`import store from './store/index.js'`

  5. 在创建vm时直接传入store

### 4.基本使用(案例)

- 组件.vue

  > 若不需要其他逻辑，可以直接使用commit，越过actions。
  >
  > 1. 读取vuex中的数据：`this.$store.数据名`(所有组件实例对象身上都有)
  > 2. 修改vuex中的数据：`this.$sotre.dispatch('方法名'，数据) `或者`this.$store.commit('方法名'，数据)`

  ```vue
  <template>
    <div class="container">
      <h2>当前的数字和为：{{$store.state.sum}}</h2>
      <!-- 强制转化为数字 -->
      <select v-model.number="n">
        <option value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
      </select>
      <button @click="add">+</button>
      <button @click="addOdd">当前和为奇数再加</button>
    </div>
  </template>
  <script>
  export default {
    name: "Count",
    data(){
      return{
        n:1,
      }
    },
    methods:{
      add(){
        this.$store.commit('ADD',this.n)
      },
      addOdd(){
        this.$store.dispatch('addOdd',this.n)
      },
    },
  };
  ```

- src/store/index.js

  > actions中函数接收到的两个参数：1.上下文，相当于缩小版的store 2.调用者传过来的值
  > mutations中函数接收到的两个参数：1.store中的state数据 2.调用者传过来的值
  > 注意：为了区分 actions中的函数一般小写 mutations中的函数一般大写

  ```js
  import Vue from "vue";
  import Vuex from "vuex";
  Vue.use(Vuex);
  const actions = {
      addOdd(context,value){
          if(context.state.sum % 2){
              context.commit('ADD',value)
          }
      },
  };
  const mutations = {
    ADD(state, value) { 
      state.sum += value;
    },
  };
  const state = {
    sum: 0,
  };
  export default new Vuex.Store({
    actions,
    mutations,
    state,
  });
  ```
  

### 5.getters配置项

- 概念：当state中的数据需要加工后再使用（极似computed），可以使用getters

- 使用：在store/index.js中追加getters配置

  ```js
  ......
  const getters = {
    bigSum(state){
      return state.sum*10
    }
  }
  export default new Vuex.Store({
    ......
    getters
  });
  ```

- 组件.vue中读取数据:`$store.getters.bigSum`

### 6.map方法使用

- Vue的思想：

  > 使模板尽可能简单，所以可以用map将$store中的数据起个'别名'（其实也可以在computed中手写）

- 先在组件中引入:`import{mapState,mapGetters,mapActions,mapMutations} from 'vuex'`

#### 6.1.mapState

- 帮助映射state中的数据为计算属性

- 基本使用：

  > 注意：对象写法不能简写，因为是去state里找key（键），而不是找变量，如果简写则是去找变量

  ```
    computed:{
    	//对象写法
    	...mapState({sum:'sum',其他:'其他数据'}),
  	//数组写法
      ...mapState(['sum','其他数据']),
    },
  ```

- 在组件模板中直接`sum`即可拿到数据`$store.state.sum`

#### 6.2.mapGetters

- 帮助映射getters中的数据为计算属性

- 基本使用：

  ```
    computed:{
    	//对象写法
    	...mapGetters({bigSum:'bigSum',其他:'其他数据'}),
  	//数组写法
      ...mapGetters(['bigSum','其他数据']),
    },
  ```

- 在组件模板中直接`bigSum`即可拿到数据`$store.getters.bigSum`

#### 6.3.mapActions

- 帮助生成与Actions对话的方法，即包含$store.dispatch(xxx)的函数,俗话就是能帮我调用Actions中的函数

- 基本使用：

  ```js
  //对象写法
  ...mapActions({addOdd:'addOdd',sub:'addWait'})
  //数组写法，前提是方法名相同
  ...mapActions(['addOdd','subWait']),
  ```

- 注意在模板中的事件方法要传入参数，否则参数就是事件对象

  ```html
  <button @click="addOdd(n)">+</button>
  ```

#### 6.4.mapMutations

- 帮助生成与mutations对话的方法，即包含$store.commit(xxx)的函数

- 基本使用：

  ```js
  //对象写法
  ...mapMutations({add:'ADD',sub:'SUB'})
  //数组写法，前提是方法名相同
  ...mapMutations(['add','sub']),
  ```

- 注意在模板中的事件方法要传入参数

  ```html
  <button @click="add(n)">+</button>
  ```

### 7.模块化+命名空间

- 把不同类别的数据在Vuex中分为几个模块，让代码更好维护

- 将上述store/index.js中的代码修改为：

  > 注意，开启命名空间后，每个模块内只能看到自己的state

  ```js
  ......
  const countOptions = {
    namespaced: true,//开启命名空间
    actions: {
      addOdd(context,value){
          if(context.state.sum % 2){
              context.commit('ADD',value)
          }
      },
    },
    mutations:{
      ADD(state, value) { 
        state.sum += value;
      },
    },
    getters : {
      bigSum(state){ return state.sum*10 }
    },
    state:{ sum: 0 }
  };
  export default new Vuex.Store({
    modules:{
      countOptions
    }
  });
  ```

- 开启命名空间后，读取state中数据

  - `this.$store.state.countOptions.sum`
  - `...mapState('count',['sum',...])`

- 读取getters中数据

  - `this.$store.getters['countOptions/sum']`
  - `...mapGetters('countOptions',['bigSum'])`

- 开启命名空间后，调用dispatch

  - `this.$store.dispatch('countOptions/addOdd','数据')`
  - `...mapActions('countOptions',['addOdd'])`

- 开启命名空间后，调用commit
  - `this.$store.commit('countOptions/add','数据')`
  - `...mapActions('countOptions',['add'])`

