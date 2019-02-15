## mockjs的简介：

使用`mockjs`可以事先模拟数据，前提是和后端约定好了数据接口，怎样的数据。使用`mock`就可以生成你要的数据了，从而实现开发时前后端分离。

### 其主要功能是：
1. 基于数据模板生成模拟数据。  
2. 基于HTML模板生成模拟数据。  
3. 拦截并模拟 ajax 请求。  

## 使用背景

在我们的生产实际中，后端的接口往往是较晚才会出来，并且还要写接口文档，于是我们的前端的许多开发都要等到接口给我们才能进行，这样对于我们前端来说显得十分的被动，于是有没有可以制造假数据来模拟后端接口呢，答案是肯定的。应该有人通过编写`json`文件来模拟后台数据，但是很局限，比如增删改查这些接口怎么实现呢，于是今天我们来介绍一款非常强大的插件[Mock.js](http://mockjs.com/)，可以非常方便的模拟后端的数据，也可以轻松的实现增删改查这些操作。

### 1. mockjs的安装：

```
npm install mockjs --save-dev
```

### 2. 配置

为了只在开发环境使用`mock.js`，而打包到生产环境时自动不使用`mock.js`，做以下配置：

`config`目录下`dev.env.js`

```
'use strict'
const merge = require('webpack-merge')
const prodEnv = require('./prod.env')

module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  Mock: true
})
```

`config`目录下`prod.env.js`

```
'use strict'

module.exports = {
  NODE_ENV: '"production"',
  Mock: false
}
```

`src`目录下`main.js`

```
process.env.Mock && require('./mock/mock.js')
```

### 3. 新建一个mock.js的文件

![新建](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190215080658.png)

### 4. 在mock.js文件中编写响应数据

```
 // 引入mockjs
 const Mock = require('mockjs')
 // 获取 mock.Random 对象
 const Random = Mock.Random
 // mock一组数据
 const produceNewsData = function () {
  let articles = []
  for (let i = 0; i < 100; i++) {
    let newArticleObject = {
      title: Random.csentence(5, 30), //  Random.csentence( min, max )
      thumbnail_pic_s: Random.dataImage('300x250', 'mock的图片'), // Random.dataImage( size, text ) 生成一段随机的 Base64 图片编码
      author_name: Random.cname(), // Random.cname() 随机生成一个常见的中文姓名
      date: Random.date() + ' ' + Random.time() // Random.date()指示生成的日期字符串的格式,默认为yyyy-MM-dd；Random.time() 返回一个随机的时间字符串
    }
    articles.push(newArticleObject)
  }
  return {
    data: articles
  }
 }
 // 拦截ajax请求，配置mock的数据
 Mock.mock('/api/test', 'get', produceNewsData)
```

### 5. 使用axios请求数据
```
<script>
export default {
  mounted: function() {
    this.getTest();
  },
  methods: {
    getTest(){
        this.$http.get('/api/test').then(function(res){
            console.log(res.data);
        });
    }
  }
};
</script>
```

![效果](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190215084305.png)

例子中的[代码](https://github.com/wangdaodao/Vue-init)，我已经提交到GitHub上，克隆下来直接安装运行可以看到效果，欢迎Star。