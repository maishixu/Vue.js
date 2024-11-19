## Vue中的AJAX

### 1.解决AJAX跨域问题

#### 1.1.配置方式一

- 前端服务器（8080）-> 后端服务器（5000）发送请求存在跨域问题（协议、主机、端口）

- 配置Vue脚手架即修改vue.config.js文件，添加一个代理，相当于一个中间服务器（8080），帮我们去请求5000端口

  ```js
  devServer:{
  	proxy:'http://localhost:5000'//写的后端服务器地址
  }
  ```

- 发送AJAX请求

  ```js
  axios.get('http://localhost:8080/student').then(
  	response => {
  		console.log('请求成功了',response.data)
  	},
  	error => {
  		console.log('请求失败了',error.message)
  	}
  )
  ```

- 配置一的缺点：

  - 只能向一个默认端口发请求，不能配置多个代理
  - 不能灵活控制走不走代理：若public目录下已经有所请求的数据，则不会发代理请求（优先匹配前端资源）

#### 1.2.配置方式二

- 优点：加上前缀，一定会走代理（灵活控制走不走代理）；可以配置多个代理

- vue.config.js配置

  ```js
  module.exports = {
    devServer: {
      proxy: {
        '/demo': { //带上请求前缀，能灵活控制走代理
          target: 'http://localhost:5000',//基础路径
          pathRewrite:{'^/demo'} //匹配所有的demo换成空字符串
          ws: true, //用于支持websocket
          changeOrigin: true //控制请求头的host值，即撒谎，欺骗服务器请求来自5000（实际8080）
        },
        '/test': { 
          target: 'http://localhost:5001/car',
          pathRewrite:{'^/test'} 
          ws: true, 
          changeOrigin: true 
        },
      }
    }
  }
  ```

- 发送AJAX请求

  ```js
  axios.get('http://localhost:8080/demo/students').then( //加上前缀
  	response => {
  		console.log('请求成功了',response.data)
  	},
  	error=> {
  		console.log('请求失败了',error.message)
  	}
  )
  ```

  

### 2.github案例记录

- 安装axios：`npm i axios`
- 别忘了上GitHub时要开代理
- 心得：数据驱动页面展示
- `{...obj1,...obj2}` 合并两个对象

### 3.Vue_resourse

- 安装 `npm i vue-resourse`
- 引入插件 `import vueResourse from 'vue-resourse'`
- 使用插件 `Vue.use(vueResourse)`
- 发送请求 `this.$http.get('url')`

