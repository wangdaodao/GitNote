使用 `VueJS` 大多是为了前后端分离，提高开发效率。如果 `API` 采用 `OAuth` 或者类似在请求头部添加一个键值的方式，只需要解决跨域就行了。如果是采用 `Cookies` 授权，相对来说就比较麻烦了。早期我们采用了 `nginx` 反代，保证 `VueJS` 开发地址和后端接口在同一个域下的方法，可以实现但是相对来说很麻烦。

## Vue CLI Proxy

`Vue CLI 3` 提供了一个 `proxy` 选项，用来代理接口转发流量。我们可以在根目录下新增 `vue.config.js` 文件，添加如下的配置：

```
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'http://xxx.com',
        changeOrigin: true
      }
    }
  }
}
```

现在在本地请求 `http://127.0.0.1:8080/api/xxx` 就会被自动转发到 `http://xxx.com/api/xxx`，这样就免去了在服务端设置跨域。

## Cookies 解决方案

解决 `Cookies` 只需要在登录时将 `Vue CLI Proxy` 代理的 `API` 返回头部中 `Cookies` 写到本地 `http://127.0.0.1:8080` 这个域下。之后再请求其他接口时，读取请求头里面的 `Cookies` 的值，并附带在实际请求接口的头部，就完成了我们的设想。

```
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'https://xxx.com',
        changeOrigin: true,
        onProxyReq (proxyReq, req, res) {
          originHost = req.headers['x-forwarded-for']
          const cookie = req.headers['cookie']

          if (cookie) {
            proxyReq.setHeader('cookie', cookie)
          }
        },
        onProxyRes(proxyRes, req, res) {
          if (proxyRes.headers['set-cookie']) {
            // 域名信息与实际业务相关
            proxyRes.headers['set-cookie'] = proxyRes.headers['set-cookie'].map(v => {
              return v.replace('domain=.xxx.com', 'domain=' + originHost.split(':')[0])
            })
          }
        }
      }
    }
  }
}
```