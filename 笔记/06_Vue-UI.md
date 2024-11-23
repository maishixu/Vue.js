## Vue-UI

### 1.移动端常用的UI组件库

- Vant https://youzan.github.io/vant
- Cube UI https://didi.github.io/cube-ui
- Mint UI http://mint-ui.github.io

### 2.PC端常用的UI组件库

- Element UI https://element.eleme.cn
- IView UI https://www.iviewui.com

### 3.使用Element UI

#### 3.1.完整引入

1. 安装：npm i element-ui
2. 在main.js引入Element UI：`import ElementUI from 'element-ui'`
3. 在main.js引入Element UI样式：`import 'element-ui/lib/theme-chalk/index.css'`
4. 使用Element UI：`Vue.use(ElementUI)`

### 3.2.按需引入

1. 安装：`npm install babel-plugin-component -D`

2. 修改babel.config.js文件

   ```js
   module.exports = {
     presets: [
       '@vue/cli-plugin-babel/preset',
       ['@babel/preset-env', { "modules": false }]
     ],
     "plugins": [
       [
         "component",
         {
           "libraryName": "element-ui",
           "styleLibraryName": "theme-chalk"
         }
       ]
     ]
   }
   ```

3. 只引入部分组件

   ```js
   import { Button, Select } from 'element-ui';
   //全局注册组件，第一个参数为组件名，第二个参数为注册的组件
   Vue.component(Button.name, Button);
   Vue.component(Select.name, Select);
   ```

   