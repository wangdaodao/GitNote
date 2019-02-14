## 问题描述

vue cli脚手架前端调后端数据接口时候的本地代理跨域问题：

如在本地 `localhost` 访问接口 `http://www.exaple.com:8602/` 是要跨域的，会报错：

```
XMLHTTPRequest can not load http://www.exaple.com:8602/. Response to preflight request doesn’t pass access control…. 
```

## 解决方案

在webpack配置一下proxyTable，config/index.js代码如下：

```
dev: {
  proxyTable: {
    //将www.exaple.com印射为/api
    '/api': {
      //设置调用的接口域名和端口号
      target: 'http://www.exaple.com:8602/',
      // 如果是https接口，需要配置这个参数
      secure: false,  
      //是否跨域
      changeOrigin: true,
      pathRewrite: {
        //这里理解成用'/api'代替'target'里面的地址，后面组件中我们调接口时直接用'api'代替 比如我要调用'http://www.exaple.com:8602/login/login'，直接写'/api/login/login'即可
        '^/api': '/'
        }
      }
    },
```

跨域成功了，但是注意了，这只是开发环境（dev）中解决了跨域问题，生产环境中真正部署到服务器上如果还是存在跨域问题，需要前后端联调：

### 第一步

前端部分可以分`生产production`和`开发development`两种环境分别测试，在`config/dev.env.js`(开发环境)和`prod.env.js`(生产环境)分别配置一下请求的地址`API_HOST`，开发环境中我们用上面配置的代理地址`api`，生产环境下用正常的接口地址，如下配置：

`config/dev.env.js`配置如下

```
module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',//开发环境
  API_HOST:"/api/"
})
```

`prod.env.js`配置如下

```
module.exports = {
  NODE_ENV: '"production"',//生产环境
  API_HOST:'"http://www.exaple.com:8602/"'
}
```

配置好之后测试时程序会自动判断当前是开发还是生产环境，然后自动匹配`API_HOST`，我们在任何组件里都能用`process.env.API_HOST`来使用地址如
```
axios.post(process.env.API_HOST+'user/login', .then(res => {
    console.log(res.data)
  }).catch(err =>{
  console.log(err);
}))
```

## 第二步：

后端服务器配置一下`cros`跨域即可，就是`access-control-allow-origin：*`允许所有访问的意思。