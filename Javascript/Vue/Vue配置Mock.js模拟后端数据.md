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

### 2. 新建一个mock.js的文件

![新建](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190215080658.png)

### 3. 在main.js中引入新建的mock.js文件和axios

```
import Mock from './mock/mock.js';
```

### 4. 在mock.js文件中编写响应数据

```
import Mock from 'mockjs';

const objTest = {
  a:"aa",
  b:"bb",
  c:"cc",
  d:"dd"
}
var arr = [11,22,33];

var fun = function(x){
  return x + 10;
}
Mock.mock("http://text.com",{
  "name1|1-3":  'a',
  "name2|2": 'b',
  "name3|+1": 3,
  "name4|1-4": 2,
  "name5|1-4.5-8": 1,
  "name6|1": true,
  "name7|1-3": true,
  "name8|1-4": objTest,
  "name9|2": objTest,
  "name10|1": arr,
  "name11|1-3": arr,
  "name12|2": arr,
  "name13": fun(10),
  "name14": /[a-z][A-Z]/,
  "name15": /\d{1,3}/,
  "name16": "@FIRST",
  "name17": "@LAST",
  "name18": "@email",
  "name19": "@date",
  "name20": "@image"
});
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
        this.$http.get('http://text.com').then(function(res){
            console.log(res.data);
        });
    }
  }
};
</script>
```

![效果](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190215081041.png)

例子中的[代码](https://github.com/wangdaodao/Vue-init)，我已经提交到GitHub上，克隆下来直接安装运行可以看到效果，欢迎Star。