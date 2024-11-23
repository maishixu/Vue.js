## Vue核心基础

### 一、Vue简介

- VUE的特点：
  1. 采用组件化模式，提高代码复用率，让代码更好维护
  2. 声明式编码，程序员不用直接操作DOM
  3. 使用虚拟DOM＋Diff算法，尽量复用DOM结点
- 使用
  1. 下载源代码`vue.js`，在项目`html`中引入
  2. 配置浏览器开发者工具`vue_dev_tools.crx`
  3. 阻止vue在启动时显示生产提示`Vue.config.productionTip = false`

### 二、Hello_Vue

- 打印`hello world`

  ```html
  <body>
      <!-- 准备好一个容器 -->
      <div id="root">
          <h1>Hello,{{ name }}</h1>
      </div>
      <script type="text/javascript">
          Vue.config.productionTip = false // 阻止vue在启动时显示生产提示
          //创建vue实例
          new Vue({
              el: '#root', //el:指定服务的容器对象
              data: { //存储数据供el指定的容器使用
                  name: "Vue"
              }
          })
      </script>
  </body>
  ```

- 注意点
  1. 容器和Vue实例必须一一对应
  2. `{{xxx}}`中要写js表达式，且xxx会自动读取到data中的所有属性

### 三、Vue语法

#### 1.模板语法

##### 1.1.插值语法

- 用于解析标签体内容

  ```html
  <h1>Hello,{{ name.toUpperCase }}</h1>
  ```

##### 1.2.指令语法

- 用于解析标签（包括：标签属性、标签体内容、绑定事件...)；双引号中的字符串当表达式用

  ```html
  <a v-bind:href="url">跳转到百度首页</a>
  ```

#### 2.数据绑定

- 单项数据绑定

  1. 语法：v-bind:href ="xxx" 或简写为 :href
  2. 特点：数据只能从 data 流向页面

- 双向数据绑定

  1. 语法：v-model:value="xxx" 或简写为 v-model="xxx" ，默认是value了
  2. 特点：数据不仅能从 data 流向页面，还能从页面流向 data

  ```html
  <input type="text" v-model:value="name">
  ```
  
  > v-model.number：收到的数据强制转化为数字

#### 3.MVVM模型

- M：模型(Model) ：对应 data 中的数据
- V：视图(View) ：模板代码
- VM：视图模型(ViewModel) ： Vue 实例对象

<img src="C:\Users\Mason\AppData\Roaming\Typora\typora-user-images\image-20241113165041190.png" alt="image-20241113165041190" style="zoom:80%;" />

#### 4.数据代理

- 回顾`Object.defineProperty`方法

  > 为对象添加属性的更高级方法

  ```js
  let number = 18
  let Person = {
      name: "Frank",
  }
  Object.defineProperty(Person, "age", {
      get() { //读取Person.age时调用
          return number
      },
      set(value) { //修改Person.age时调用
          number = value
      }
      //整体思想，把Person.age交给number代理
  })
  ```

- 何为**数据代理**？

  >通过一个对象代理对另一个对象中的属性的操作（读/写）

- 对象代理的简单例子：

  ```js
  let obj1 = { x: 18 }
  let obj2 = { y: 28 }
  //把obj1的x属性交给obj2代理
  Object.defineProperty(obj2, "x", {
      get() {
          return obj1.x
      },
      set(value) {
          obj1.x = value
      }
  })
  ```

- 铺垫到这里：Vue实例中的`_data.name`实际上就是交给了`name`代理，你修改了`vm.name`就相当于修改了`vm._data.name`

- 更官方的说法：通过`vm`对象来代理`vm.data`对象中的属性

#### 5.数据监测

- 原理

  1. 检测data里所有层次的数据（即对象里的对象的属性，也能监测）

  2. 通过`setter`实现监测

     >- 自动监测`new Vue`时传入的所有数据
     >
     >- 以后要添加的数据，可以通过`Vue.set`API让Vue提供响应式处理。**但是，不能给vm根数据对象添加属性！**
     >
     >  ```vue
     >  Vue.set(target.propertyName,value)
     >  vm.$set(target.propertyName,value)
     >  ```
     >
  
- 特殊：如何检测数组中的数据

  1. 通过包裹原生数组方法实现

    > 调用原生模板更新数组 -> 重新解析模板

  2. 数组的方法

    - push、pop、shift、unshift、splice、sort、reverse ：都会改变数组

      ```js
      //从第0个开始删（不包括第1个）删完后插入一个元素
      this.student.hobby.splice(0,1,'newVal')
      Vue.set(this.student.hobby,0,'开车') //第二种方法
      ```

    - filter ：不改变数组，返回一个新数组 (若要修改，用新数组替换原数组)


#### 6.事件处理

- 在Vue实例中定义函数，来处理各种事件，用@符号将事件与Vue关联起来，有参数`"methodName(arg)"`，没有`"methodName"`

- 注意：Vue中事件处理方法中的this是Vue实例对象

  ```html
  <div id="root">
      <!-- $event：当前事件对象；给click添加响应函数  -->
      <button @click = "showInfo($event,'message123')">提示信息</button> 
  </div>
  <script type="text/javascript">
      Vue.config.productionTip = false
      new Vue({
          el: '#root',
          methods:{
              showInfo(event,info){
                  alert(info) //点击按钮，弹出警告框："message123"
              }
          }
      })
  </script>
  ```

- 冒泡与捕获

  1. 冒泡：由里往外；默认事件在这个阶段处理
  2. 捕获：由外往里

##### 5.1.事件修饰符

  - prevent：阻止事件的默认行为 `event.preventDefault()`

     ```html
      <a href="https://www.baidu.com" @click.prevent="showInfo">打开百度</a> <!--阻止网页跳转-->
     ```

  - stop：停止事件冒泡 `event.stopPropagation()`

  - once：事件只触发一次

  - capture：使用事件捕获模式

  - self：只有`event.target`是当前操作的元素的是侯才触发事件

##### 5.2.键盘事件

- 用法：给enter键绑定一个showInfo函数，当"按下enter并松开后触发事件"

  ```html
  <input type="text" placeholder="按下回车提示输入" @keyup.enter = "showInfo">
  ```

- 特殊情况

  - tab键，必须配合keydown去使用
  - 系统修饰键：ctrl、alt、shift、meta
    1. 配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
    2. 配合keydown使用：正常触发事件。

- 自定义按键别名

  > Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名

#### 7.计算属性与监视

##### 6.1.计算属性

- 定义：要显示的数据不存在，要通过计算得来。

- 优点：与`methods`实现相比，内部有缓存机制（读取一次，以后再读取时直接取缓存即可，除非有修改），且便于好调试

- 使用：

  1. 在 `computed` 对象中定义计算属性。

  2. get方法：返回计算属性的值

     - 何时被执行？

       >i.初次读取时会执行一次。
       >
       >ii.当依赖的数据发生改变时会被再次调用。

  3. set方法：修改计算属性的依赖项（注意：不是直接修改计算属性）

     - 何时被执行？

       > 当计算属性被"刻意"修改时

  4. 在页面中使用`{{方法名}}`来显示计算的结果。

- 示例：

  ```html
  <body>
      <div id="root">
          姓：<input type="text" v-model="firstName"> <br> 名：<input type="text" v-model="lastName">
          全名：<h1>{{fullName}}</h1>
      </div>
  </body>
  <script type="text/javascript">
      Vue.config.productionTip = false
      const vm = new Vue({
          el: "#root",//代理
          data: { firstName: "张", lastName: "三" },
          computed: {
              fullName: {
                  //读取
                  get() { return this.firstName + "-" + this.lastName },
                  set(value) { //获取要修改的值，但是注意修改的是计算属性的依赖
                      this.firstName = value.split("-")[0]
                      this.lastName = value.split("-")[1]  } 
              } 
          } 
      } )
  </script>
  ```

- 简写：没有`set`（修改）的情况下

  ```js
  fullName(){ return this.firstName + "-" + this.lastName },
  ```

##### 6.2.监视属性

当被监视的属性变化时, 回调函数自动调用, 进行相关操作

- 两种写法：

  1. `new Vue`时传入watch配置

     ```js
     watch:{
         isHot:{
             //当isHot发生变化时handler调用
             handler(newValue,oldValue){
                 console.log('isHot被修改了',newValue,oldValue)
             }
         }   
     }
     ```

  2. 通过`vm.$watch`监视

     ```js
     vm.$watch('isHot',{
     	immediate:true, //初始化时让handler调用一下
     	handler(newValue,oldValue){
     		console.log('isHot被修改了',newValue,oldValue)
     	}
     })
     ```

- 深度监视：`deep:true`

  > Vue默认不开启watch中多层级属性的监视，目的是提高效率

- 简写：没有其他配置项的时候

  ```vue
  watch:{
      isHot(newValue,oldValue){
          console.log('isHot被修改了',newValue,oldValue)
      }
  }
  ```

- 注意点：监视的属性必须存在，才能进行监视！！

##### 6.3.区别

- computed和watch之间的区别：
  1. computed能完成的功能，watch都可以完成；
  2. watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。

##### 6.4.再说this

- 普通函数：调用该函数的对象

- 箭头函数：继承自外部的作用域的this

- 何使用普通函数？箭头函数？

  >不是由Vue控制的回调：箭头函数
  >由Vue控制的回调：普通函数

<img src="C:\Users\Mason\AppData\Roaming\Typora\typora-user-images\image-20241111041505260.png" alt="image-20241111041505260" style="zoom: 50%;" />

#### 8.绑定样式

##### 7.1.class样式

- 写法:`:class="xxx"` xxx可以是字符串、对象、数组。

  1. 字符串：类名不确定，要动态获取。

  2. 对象：要绑定多个样式，个数不确定，名字也不确定。

     ```js
     //body
     <div class="basic" :class="classArr">{{name}}</div>
     //Vue中的data
     classArr:['atguigu1','atguigu2','atguigu3'],
     ```

  3. 数组：要绑定多个样式，个数确定，名字也确定，但不确定用不用。

##### 7.2.style样式

- 样式对象：`:style="{fontSize: xxx}"`其中xxx是动态值。

  ```js
  //body
  <div class="basic" :class="classObj">{{name}}</div>
  //Vue
  styleObj:{
  	fontSize: '40px',
  	color:'red',
  }
  ```

- 样式数组：`:style="[a,b]"`其中a、b是样式对象。

- 注意点：样式名要用驼峰命名法

#### 9.条件渲染

##### 8.1.v-if

- 写法：
  1. `v-if="表达式"` 
  2. `v-else-if="表达式"`
  3. `v-else="表达式"`
- 适用于：切换频率较低的场景。
- 特点：不展示的DOM元素直接被移除。
- 注意：`v-if`可以和`v-else-if`、`v-else`一起使用，但要求结构不能被“打断”。

##### 8.2.v-show

- 写法：v-show="表达式"
- 适用于：切换频率较高的场景。
- 特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉

- **注意点**

  >使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到。

#### 10.列表渲染

##### 9.1.v-for

- 用于展示列表数据，可遍历：数组、对象、字符串、指定次数

- 语法：

  ```html
  <ul v-for = "(p,index) in persons" :key="p.id">
      <li>{{p.name}}-{{p.age}}</li>
  </ul
  ```

##### 9.2.key原理

- 作用：Vue中虚拟DOM的对象唯一标识，当数据发生变化时，根据**旧虚拟DOM和新虚拟DOM比较**，将页面重新渲染

- 比较规则
  1. 找到相同的Key：若内容一样则直接使用原真实DOM；否则，生成新的真实DOM
  2. 找不到相同的Key：创建新的真实DOM
  
  ![7c32fa76fa0d4f8ed5587e8733b13bf](D:\WeChat Files\wxid_mjdtvhaj637c22\FileStorage\Temp\7c32fa76fa0d4f8ed5587e8733b13bf.png)
  
- 如果使用`index`作为Key，会引发什么问题？
  1. 结果中含输入类的DOM：在逆序添加时，产生错误的真实DOM更新
  2. 结果中不含输入类的DOM：在逆序添加时，虽然不会产生错误，但是会影响效率（生成很多的新的DOM，没有复用）
  
  ![f34d84b477764d999e38d46f13e9cc4](D:\WeChat Files\wxid_mjdtvhaj637c22\FileStorage\Temp\f34d84b477764d999e38d46f13e9cc4.png)

##### 9.3.案例:筛选排序

- 准备好模板

  ```html
  <div id="root">
    <h1>人员列表</h1>
    <button @click="sortType=0">原顺序</button>
    <button @click="sortType=1">降序</button>
    <button @click="sortType=2">升序</button> <br />
    <input type="text" placeholder="请输入人员姓名" v-model="keyName" />
    <ul v-for="(p,index) in filterPerson" :key="p.id">
      <li>{{p.name}}-{{p.age}}</li>
    </ul>
  </div>
  ```

- 使用Vue更新模板

  ```js
  computed: { 
    filterPerson() { //检测到依赖项改变就执行重新执行一次
      //先筛选数据
      const arr = this.persons.filter((p) => {
        return p.name.indexOf(this.keyName) !== -1;
      });
      //排序后再返回
      if (this.sortType) {
        arr.sort((p1, p2) => {
          return this.sortType === 1 ? p2.age - p1.age : p1.age - p2.age;
        });
      }
      return arr;
    },
  }
  ```

#### 11.收集表单数据

- `<input type="text"/>` v-model收集的是用户输入的value值

  ```html
  <input type="password" id="mima" v-model="userInfo.password"> <br>
  ```

- `<input type="radio"/>`v-model收集的是用户输入的value值（未配置则是undefined），并且要给标签配置value值和name（关联、单选）

  ```html
  男<input type="radio" name="sex" value="male" v-model="userInfo.sex">
  女<input type="radio" name="sex" value="female" v-model="userInfo.sex"> <br>
  ```

- `<input type="checkbox"/>`若未配置value：v-model收集的是checked(true/false)

- `<input type="checkbox"/>`若已配置value

  1. v-model是非数组：v-model收集的依然是checked(true/false)
  2. v-model是数组：v-model收集的是value组成的数组

  ```html
  爱好：
  学习<input type="checkbox" v-model="userInfo.hobby" value="study">
  睡觉<input type="checkbox" v-model="userInfo.hobby" value="sleep">
  上课<input type="checkbox" v-model="userInfo.hobby" value="class"><br>
  ```

- v-model的三个修饰符

  1. lazy：失去焦点再收集数据
  2. number：收集到的数据为有效数字
  3. trim：收集数据过滤首尾空格

#### 12.过滤器

- 对定义的数据进行特定的格式化再输出

  1. 全局过滤器：`Vue.filter(name,function(value{ }))`

  2. 局部过滤器

     ```js
     filters:{
       filterMessage(value){
           return value.slice(0,4) //结果：hell
       }
     }
     ```

  3. 使用：

     ```html
     <h1>{{ name | filterMessafe }}</h1>
     ```

     

- 注意点：

  1. 过滤器可接收额外参数，过滤器可以串联
  2. 并没有改变原数据，是产生对应的新数据然后渲染

#### 13.内置指令

##### 13.1.v-text

- 向所在节点中渲染（替换）文本内容，与插值语法的区别是`{{xxx}}`不会替换结点的内容
- 不会渲染html代码；指令是不能脱离标签存在的

##### 13.2.v-html

- 向指定节点渲染包含html结构内容，同样会替换掉结点中的内容

- 安全问题：

  > 当用户在输入框中嵌入Javascript代码时，会导致**XSS攻击**（跨站脚本攻击）。攻击者可能利用用户的输入框、URL 参数等不安全的数据源来注入脚本从而获取用户的隐私信息，比如用户的cookie。

- 什么时候可以用`v-html`：当确定内容不是来自外部（用户）输入的时候

##### 13.3.v-cloak

- 特殊属性：解决网速慢的问题

- 结合style使用：在script加载完（即Vue解析完成）之前，先将含有v-cloak的标签隐藏起来，当Vue解析完毕后才删掉v-cloak属性，正常渲染页面

  ```html
  <style>
    [v-cloak]{ //所有含v-cloak属性的标签都隐藏
  	display:none
    }
  </style>
  <h1 v-cloak>{{name}}<h1>//防止Vue实例没有渲染模板，直接呈现{{XXX}}
  ```

##### 13.4.v-once

- 使结点初次渲染后，就视为静态内容，以后数据更新不会引起v-once所在结点的结构更新，可节约性能

##### 13.5.v-pre

- 跳过当前结点的编译过程，利用其跳过不使用Vue指令语法/插值语法的标签，加快编译（性能好）

#### 14.自定义指令

##### 14.1.函数式

- 自定义指令函数中**`this`是window**

- 他要用到的属性已经通过指令后面的"值"传过去了，通过`binding.value`可取到

  ```js
  <h1 v-big="n">
  directives: {
  big(element, binding) {
    //第一个参数：标签实例 第二个参数：绑定的标签对象
    element.innerText = binding.value * 10;
  }
  ```

##### 14.2.对象式

- 三个回调函数调用的时机：

  1. `.bind:`指令与元素绑定成功时调用
  2. `.inserted`:指令所在元素被插入页面时调用
  3. `.update`:指令所在模板被重新解析时调用

- 实现功能：输入框一呈现就获得焦点

  ```html
  <input type="text" v-fbind:value = "n" /> 
  fbind:{
      bind(element,binding){ element.value = binding.value },
      inserted(element,binding){ element.focus() }, 
      update(element,binding){ element.value = binding.value },
  },
  ```

- 注意：不能使用驼峰命名法，多个单词用"-"隔开

#### 15.生命周期

- 常用的生命周期钩子

  1. mounted：中文翻译挂载；页面中初次呈现经过Vue解析的页面

     > 进行【初始化操作】：发送AJAX请求、启动定时器、绑定自定义事件、订阅消息

  2. beforeDestroy：濒临销毁之前的生命周期，**这个周期内就算操作数据也不会再引起`updated`了**

     > 进行【收尾操作】：清除定时器、绑定自定义事件、取消订阅消息

注：每个红色框里的都是一个生命周期；8个、4对。

![c59db1b36137a3c59f57c8113cf090f](D:\WeChat Files\wxid_mjdtvhaj637c22\FileStorage\Temp\c59db1b36137a3c59f57c8113cf090f.png)

