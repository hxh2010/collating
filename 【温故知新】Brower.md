---
title: 【温故知新】Browser
date: 2021-05-08 00:00:00
tags:
  - 面试
  - browser
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/browser.jpg
---

![](https://hongxh.cn/img/study_img/browser.jpg)


### 首屏时间和白屏时间
- 首屏时间 = 白屏时间 + 首屏渲染时间
- window.performance 用来测量网页和 web 应用程序的性能 api 
```javascript
// window.performance 相关字段说明
// memory 代表JavaScript对内存的占用。
// navigation 字段统计的是一些网页导航相关的数据：
// 相关时间计算:
// DNS查询耗时 = domainLookupEnd - domainLookupStart
// TCP链接耗时 = connectEnd - connectStart
// request请求耗时 = responseEnd - responseStart
// 解析dom树耗时 = domComplete - domInteractive
// 白屏时间 = domloading - fetchStart
// domready可操作时间 = domContentLoadedEventEnd - fetchStart
// onload总下载时间 = loadEventEnd - fetchStart
```
- widow.performance.getEntries() 统计静态资源相关时间


### 首屏优化
1. **CDN** 内容分发：提取第三方库，静态资源到 cdn
   CDN是构建在现有网络基础之上的智能虚拟网络，依靠部署在各地的边缘服务器， 
   通过中心平台的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容， 
   降低网络拥塞，提高用户访问响应速度和命中率。CDN的关键技术主要有内容存储和分发技术。
2. 设置缓存：浏览器一般不会对content-type: application/json 的接口数据进行缓存，需要手动设置。
   抽取缓存层，请求接口只执行动作，如果缓存有数据先从缓存取。
3. 静态文件添加缓存：hash + 强缓存 => hash + cache control:max-age = 1年
- nginx 的话使用 **proxy_cache**
```shell
    #要缓存文件的后缀，可以在以下设置。
    location ~ .*\.(gif|jpg|png|css|js)(.*) {
            proxy_pass http://ip地址:90;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_cache cache_one;
            proxy_cache_valid 200 302 24h;
            proxy_cache_valid 301 30d;
            proxy_cache_valid any 5m;
            expires 90d;
            add_header wall  "hey!guys!give me a star.";
    }
    ...

    # 无nginx缓存的blog端口
    server {
        listen  90;
        server_name localhost;
        root /mnt/blog/;

        location / {

        }
    }
```
4. 前端资源动态加载：路由（懒加载）、组件库（按需引入）、图片（懒加载 loading="lazy"）
5. 减少请求的数量，如果携带 cookie ，尽量减少 cookie 内容
6. 利用http压缩：nginx 配置 gzip
7. 引入 http 2.0 提升传输性能。
8. 图片尽量使用 webp 格式
9. 利用好script标签的async和defer这两个属性。功能独立且不要求马上执行的js文件，可以加入async属性。如果是优先级低且没有依赖的js，可以加入defer属性。


### 获取页面阅读进度
```ecmascript 6
  const { height } = window.screen;
  const totalHeight = document.documentElement.scrollHeight;
  const { clientHeight } = document.documentElement;
  if (totalHeight > height) {
    const surplus = totalHeight - clientHeight;
    // 当前滑动高度
    const scrollY = document.documentElement.scrollTop || document.body.scrollTop;
    // 当前位置百分比n小数
    let currentPercentage = Math.ceil((scrollY / surplus) * 100 || 0);
  }

  // clientHeight: 包括padding但不包括border、水平滚动条、margin的元素的高度。
  // offsetHeight：包括padding、border、水平滚动条，但不包括margin的元素的高度。
  // scrollHeight代表包括当前不可见部分的元素的高度。
  // 而可见部分的高度其实就是clientHeight，也就是scrollHeight>=clientHeight恒成立。
  // 在有滚动条时讨论scrollHeight才有意义，在没有滚动条时scrollHeight==clientHeight恒成立。
  // offsetTop: 当前元素顶部距离最近父元素顶部的距离,和有没有滚动条没有关系。
  console.log("document.body.scrollTop:", document.body.scrollTop);
  console.log("document.documentElement.scrollTop:", document.documentElement.scrollTop);
  console.log("document.documentElement.scrollHeight:", document.documentElement.scrollHeight);
  console.log("window.screenTop:", window.screenTop);
  console.log("屏幕高度: window.screen.height:", window.screen.height);
  console.log("浏览器总高度: window.outerHeight:", window.outerHeight);
  console.log("浏览器页面高度: window.innerHeight:", window.innerHeight);
  console.log("body总高度: document.body.offsetHeight:", document.body.offsetHeight);
  console.log("body高度: document.body.clientHeight:", document.body.clientHeight);
  console.log("页面可用高度: window.screen.height:", window.screen.height);
  console.log("------------------------------------------------------");
```


### Event Loop
- Event Loop即事件循环，是指浏览器或Node的一种解决javaScript单线程运行时不会阻塞的一种机制，也就是我们经常使用异步的原理。
- 在JavaScript中，任务被分为两种，一种宏任务（MacroTask）也叫Task，一种叫微任务（MicroTask）。

### 跨域 CORS 
- 定义：跨域资源共享(CORS) 是一种机制，它使用额外的 HTTP 头来告诉浏览器 
  让运行在一个 origin (domain)上的Web应用被准许访问来自不同源服务器上的指定的资源。
  当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域HTTP 请求。
- 浏览器将 cors 分为简单请求和非简单请求，简单请求不会触发 cors


### JSONP 解决跨域
- 具有局限性， 仅支持get方法
- 不安全，可能会遭受XSS攻击
- 原生实现
```javascript
  var script = document.createElement('script');
  script.type = 'text/javascript';
  // 传参一个回调函数名给后端，方便后端返回时执行这个在前端定义的回调函数
  script.src = 'url/login?callback=handleCallback';
  document.head.appendChild(script);
  // 回调执行函数
  function handleCallback(res) {
      alert(JSON.stringify(res));
  }
```
- axios 实现
```javascript
  axios.jsonp('url/login', {
      params: {},
      jsonp: 'handleCallback'
  }).then((res) => {
      console.log(res); 
  })
```

### postMessage 解决跨域
- HTML5 XMLHttpRequest Level 2中的API，且是为数不多可以跨域操作的window属性之一。


### Nginx 正向代理和反向代理的区别
- 正向代理和反向代理的结构是一样的，都是 client-proxy-server 的结构。
  它们主要的区别就在于中间这个 proxy 是哪一方设置的。
  在正向代理中，proxy 是 client 设置的，用来隐藏 client；
  而在反向代理中，proxy 是 server 设置的，用来隐藏 server。


### Nginx 工作原理
- Nginx 是一款轻量级的 Web 服务器，也可以用于反向代理、负载平衡和 HTTP 缓存等。
  Nginx 使用异步事件驱动的方法来处理请求，是一款面向性能设计的 HTTP 服务器。 
  传统的 Web 服务器如 Apache 是 process-based 模型的，
  而 Nginx 是基于event-driven模型的。正是这个主要的区别带给了 Nginx 在性能上的优势。
- Nginx 架构的最顶层是一个 master process，
  这个 master process 用于产生其他的 worker process，
  这一点和Apache 非常像，但是 Nginx 的 worker process 可以同时处理大量的HTTP请求，
  而每个 Apache process 只能处理一个。




### XSS 攻击
- XSS 攻击指的是跨站脚本攻击，是一种代码注入攻击。攻击者通过在网站注入恶意脚本，使之在用户的浏览器上运行，从而盗取用户的信息如 cookie 等。


### CSRF 攻击
- 概念：CSRF 攻击指的是跨站请求伪造攻击，攻击者诱导用户进入一个第三方网站，然后该网站向被攻击网站发送跨站请求。如果用户在被攻击网站中保存了登录状态，那么攻击者就可以利用这个登录状态，绕过后台的用户验证，冒充用户向服务器执行一些操作。
- 类型：
  - GET 类型的 CSRF 攻击，比如在网站中的一个 img 标签里构建一个请求，当用户打开这个网站的时候就会自动发起提交。
  - POST 类型的 CSRF 攻击，比如说构建一个表单，然后隐藏它，当用户进入页面时，自动提交这个表单。
  - 链接类型的 CSRF 攻击，比如说在 a 标签的 href 属性里构建一个请求，然后诱导用户去点击。
- 防御：
  - 第一种是同源检测的方法
  - 第二种方法是使用 CSRF Token 来进行验证
  - 第三种方式使用双重 Cookie 验证的办法
  - 第四种方式是使用在设置 cookie 属性的时候设置 Samesite


### 常见的浏览器内核有哪些
- Trident 内核：IE,MaxThon,TT,The World,360,搜狗浏览器等。[又称 MSHTML]
- Gecko 内核：Netscape6 及以上版本，FF,MozillaSuite/SeaMonkey 等
- Presto 内核：Opera7 及以上。 [Opera 内核原为：Presto，现为：Blink;]
- Webkit 内核：Safari,Chrome 等。 [ Chrome 的 Blink（WebKit 的分支）]


### 浏览器事件
- addEventListener(type, listener, options, useCapture)
  - type: 事件名称
  - listener：监听事件调用的方法
  - options：一个指定有关 listener 属性的可选参数对象。capture/once/passive...
  - useCapture: 默认为 false。是否要先于它下面的 EventTarget，调用该 listener
- `visibilitychange`：判断页面是否在前台显示：
- `orientationchange`：设备的纵横方向改变时触发
- `DOMContentLoaded`：当初始的 HTML 文档被完全加载和解析完成之后触发，而无需等待样式表、图像和子框架的完全加载。


### WebAssembly
- WebAssembly 是一种新的编码方式，可以在现代的网络浏览器中运行 - 它是一种低级的类汇编语言，具有紧凑的二进制格式，可以接近原生的性能运行，并为诸如 C/C++ 等语言提供一个编译目标，以便它们可以在 Web 上运行。它也被设计为可以与 Javascript 共存，允许两者一起工作。
- WebAssembly 提供了一条途径，以使得以各种语言编写的代码都可以以接近原生的速度在 Web 中运行。
