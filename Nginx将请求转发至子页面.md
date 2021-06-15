---
title: Nginx将请求转发至子页面
date: 2020-12-3 11:11:11
tags:
  - nginx
  - linux
categories:
  - 教程
cover: https://img-blog.csdnimg.cn/20201203134235819.png#pic_center
---

> Nginx是俄罗斯人Igor Sysoev编写的轻量级Web服务器，它的发音为 [ˈendʒɪnks] ，它不仅是一个高性能的HTTP和反向代理服务器，同时也是一个IMAP/POP3/SMTP 代理服务器。
![](https://img-blog.csdnimg.cn/20201203134235819.png#pic_center)

### 默认配置
```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    access_log  /var/log/nginx/host.access.log  main;
    error_log  /var/log/nginx/error.log  error;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

### 代理转发
 在nginx中配置proxy_pass代理转发时，如果在proxy_pass后面的url加/，表示绝对根路径；如果没有/，表示相对路径，把匹配的路径部分也给代理走。

##### 第一种
- 代理至地址，proxy_pass 后面没有 “ / ”
 ```
 location /api {
 	proxy_pass http://localhost:3000;
 }
 ```
 访问：http://localhost/api/xxx
 转至：http://localhost:3000/xxx

##### 第二种
- 代理至地址，proxy_pass 后面有 “ / ”
 ```
 location /api {
 	proxy_pass http://localhost:3000/;
 }
 ```
 访问：http://localhost/api/xxx
 转至：http://localhost:3000/api/xxx

##### 第三种
- 代理本地静态页面
```
location /page {
    alias   /usr/share/nginx/html/page/;
    index  index.html index.html;
}
```
