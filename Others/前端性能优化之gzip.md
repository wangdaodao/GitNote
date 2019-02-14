`gzip`是`GNUzip`的缩写，它是一个GNU自由软件的文件压缩程序。它最早由Jean-loup Gailly和Mark Adler创建，用于UNⅨ系统的文件压缩。我们在Linux中经常会用到后缀为.gz的文件，它们就是GZIP格式的。现今已经成为Internet 上使用非常普遍的一种数据压缩格式，或者说一种文件格式。

HTTP协议上的GZIP编码是一种用来改进WEB应用程序性能的技术。大流量的WEB站点常常使用GZIP压缩技术来让用户感受更快的速度。减少文件大小有两个明显的好处，一是可以减少存储空间，二是通过网络传输文件时，可以减少传输的时间。

当然WEB服务器和客户端（浏览器）必须共同支持gzip。目前主流的浏览器Chrome,firefox,IE等都支持该协议。常见的服务器如Apache，Nginx，IIS同样支持gzip。

下面就以Vue项目为例，介绍一下gzip的使用（vue-cli 2.*）

1. 在`/config/index.js`中，修改配置开启gzip

```
// Gzip off by default as many popular static hosts such as
// Surge or Netlify already gzip all static assets for you.
// Before setting to `true`, make sure to:
// npm install --save-dev compression-webpack-plugin
productionGzip: true,
productionGzipExtensions: ['js', 'css'],
```

在修改`productionGzip`的默认值（`false`）为`true`之前，先安装所需的依赖`npm install --save-dev compression-webpack-plugin`。

2. 在nginx中开启gzip，`/nginx/conf/nginx.conf`中添加gzip配置

```
http:{ 
  #启用或禁用gzipping响应。#
  gzip on; 
  #设置用于压缩响应的缓冲区number和size。默认情况下，缓冲区大小等于一个内存页面。这是4K或8K，具体取决于平台。#
  gzip_static on;
  #启用或禁用gzipping响应。#
  gzip_buffers 4 16k;
  #设置level响应的gzip压缩。可接受的值范围为1到9。#
  gzip_comp_level 5;
  #设置将被gzip压缩的响应的最小长度。长度仅由“Content-Length”响应头字段确定。#
  gzip_min_length 100;
  #匹配MIME类型进行压缩，text/html默认被压缩。#
  gzip_types text/plain application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
}
```

修改完nginx配置，重启服务。

关于gzip详细的配置和描述，请查阅 [Module ngx_http_gzip_module](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)

打开`Chrome`控制台，可以看到`Network`下的`Response Headers`中返回了`Content-Encoding: gzip`，表明gzip开启成功。
而`Request Headers`里面的`Accept-Encoding`: gzip只是表示前端（用户浏览器）支持gzip的压缩方式。

服务器支持gzip的方式可以有两种：

1. 打包的时候生成对应的.gz文件，浏览器请求xx.js时，服务器返回对应的xxx.js.gz文件
2. 浏览器请求xx.js时，服务器对xx.js进行gzip压缩后传输给浏览器