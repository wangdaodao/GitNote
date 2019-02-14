`axios` 是一个基于 `promise` 的 `HTTP` 库，`axios`并没有`install`方法，所以是不能使用`vue.use()`方法的。

## 结合`vue-axios`使用

看了[vue-axios](https://www.npmjs.com/package/vue-axios)的源码说明，它是按照`vue`插件的方式去写的。那么结合`vue-axios`，就可以去使用`vue.use()`方法了。

### 安装：

```
npm install axios vue-axios --save
```

### `main.js`引用

```
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
 
Vue.use(VueAxios, axios)
```

### 项目组件上面使用

```
Vue.axios.get(api).then((response) => {
  console.log(response.data)
}).catch((response)=>{
  console.log(response.data);
})
 
this.axios.get(api).then((response) => {
  console.log(response.data)
}).catch((response)=>{
  console.log(response.data);
})
 
this.$http.get(api).then((response) => {
  console.log(response.data)
}).catch((response)=>{
  console.log(response.data);
})
```

## `axios` 改写为 `Vue` 的原型属性

首先在主入口文件`main.js`中引用，之后挂在`vue`的原型链上：

```
import axios from 'axios'
Vue.prototype.$ajax= axios
```

使用：

```
this.$ajax.get('api')
.then((response)=>{
    console.log(response.data)
}).catch((response)=>{
    console.log(response);
})
```