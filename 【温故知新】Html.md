---
title: 【温故知新】Html
date: 2021-05-21 00:00:00
tags:
  - 面试
  - html
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/html.png
---

![](https://hongxh.cn/img/study_img/html.png)

### 行内元素和行内块元素的区别
- 行内：
  - 设置宽高无效
  - 对 margin 仅设置左右方向有效，上下无效；padding 设置上下左右都有效
  - 不会自动进行换行


### html5 有哪些新特性、移除了那些元素？如何处理 HTML5 新标签的浏览器兼容问题？如何区分 HTML 和 HTML5？
- HTML5 现在已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等功能的增加
  - 绘画 canvas
  - 用于媒介回放的 video 和 audio 元素
  - 本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失
  - sessionStorage 的数据在浏览器关闭后自动删除
  - 语意化更好的内容元素，比如 article、footer、header、nav、section
  - 表单控件，calendar、date、time、email、url、search
  - 新的技术 webworker, websocket, Geolocation
- 移除的元素：
  - 纯表现的元素：basefont，big，center，font, s，strike，tt，u
  - 对可用性产生负面影响的元素：frame，frameset，noframes
- 支持 HTML5 新标签：
  - IE8/IE7/IE6 支持通过 document.createElement 方法产生的标签
  - 可以利用这一特性让这些浏览器支持 HTML5 新标签
  - 浏览器支持新标签后，还需要添加标签默认的样式
- 当然也可以直接使用成熟的框架、比如 html5shim


### iframe 有哪些优点和缺点？
- 缺点：
  • iframe 会阻塞主页面的 Onload 事件
  • 搜索引擎的检索程序无法解读这种页面，不利于 SEO
  • iframe 和主页面共享连接池，而浏览器对相同域的连接有限制，所以会影响页面的并行加载
  • 使用 iframe 之前需要考虑这两个缺点。如果需要使用 iframe，最好是通过 javascript 动态给 iframe 添加 src 属性值，这样可以绕开以上两个问题
- 优点：
  • 用来加载速度较慢的内容（如广告）
  • 可以使脚本可以并行下载
  • 可以实现跨子域通信


### Service Workers/Web Workers/WebSockets 的区别
- Service Worker:处理网络请求的后台服务。完美的离线情况下后台同步或推送通知的处理方案。不能直接与 DOM 交互。通信（页面和 ServiceWorker 之间）得通过 postMessage 方法
- Web Worker:模仿多线程，允许复杂的脚本在后台运行，所以它们不会阻止其他脚本的运行。是保持您的 UI 响应的同时也执行处理器密集型功能的完美解决方案。不能直接与 DOM 交互。通信必须通过 postMessage 方法
- WebSocket:在客户端和服务器之间创建一个开放的连接，允许在一个连接上进行双向通信。是你目前使用长轮询的任何情况，比如聊天软件、在线游戏，或运动代码的完美解决方案。可以直接与 DOM 交互。通信是通过 WebSocket 的 send 方法。



### 什么是 CSP？
- CSP（Content-Security-Policy）指的是内容安全策略，它的本质是建立一个白名单，告诉浏览器哪些外部资源可以加载和执行。我们只需要配置规则，如何拦截由浏览器自己来实现。
  通常有两种方式来开启 CSP，一种是设置 HTTP 首部中的 Content-Security-Policy，一种是设置 meta 标签的方式 `<meta http-equiv="Content-Security-Policy">`
  CSP 也是解决 XSS 攻击的一个强力手段。
