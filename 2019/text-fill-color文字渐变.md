![](https://raw.githubusercontent.com/wangdaodao/GitImg/master/img/20190213084649.png)

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>text-fill-color css web前端开发</title>
<style>
html{background: #000;}
.text1{
    margin:60px auto;
    font-size: 120px;
    text-align: center;
    font-weight: bold;
    background:-webkit-gradient(linear,30% 20%,80% 80%,from(#088df3),to(#6a38ec));
    -webkit-background-clip:text;
    -webkit-text-fill-color:transparent;
}
.text2{
    margin:60px auto;
    font-size: 120px;
    text-align: center;
    font-weight: bold;
    background: url(https://www.apple.com/v/iphone-xs/a/images/overview/copy_texture_1_medium.jpg) repeat center center;
    -webkit-background-clip:text;
    -webkit-text-fill-color:transparent;
}
.record{
    color: #737373;text-align: center;font-size: 24px;position: fixed;bottom: 0%;left: 0;width: 100%;padding: 20px 0;
}
</style>
</head>
<body>
    <div class="text1">文字颜色渐变</div>
    <div class="text2">文字遮罩照片</div>
    <p class="record">by Jerry yu </p>
</body>
</html>
```