---
title: 【温故知新】Node
date: 2021-05-25 00:00:00
tags:
  - 面试
  - node
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/node.png
---

![](https://hongxh.cn/img/study_img/node.png)

### 什么时候用 Node.js？
- Node.js 是异步的、事件驱动的、非阻塞的和单线程的，使得它成为开发下面应用程序的完美候选：
- 实时应用程序，如聊天和提供实时更新的应用程序
- 将视频或其他多媒体内容流式传输给大量观众的流式应用程序
- 其他 I/O 密集型应用程序，吞吐量大
- 遵循微服务架构的网络后端


### node里的模块是什么
- 每个module里面都会传入__filename, __dirname参数，这两个参数并不是module本身就有的，是外界传入的
- Node中，每个文件模块都是一个对象，它的定义如下：
```javascript
function Module(id, parent) {
this.id = id;
this.exports = {};
this.parent = parent;
this.filename = null;
this.loaded = false;
this.children = [];
}

module.exports = Module;

var module = new Module(filename, parent);
```


### require的模块加载机制
1. 先计算模块路径
2. 如果模块在缓存里面，取出缓存
3. 加载模块
4. 的输出模块的exports属性即可


### require 与 import 的区别
1. 原生浏览器不支持 require/exports，可使用支持 CommonJS 模块规范的 Browsersify、webpack 等打包工具，它们会将 require/exports 转换成能在浏览器使用的代码。
2. import/export 在浏览器中无法直接使用，即使 Node.js 13.2+ 已经支持 import/export，Node.js 官方不建议在正式环境使用，
   目前可以使用 babel 将 ES6 的模块系统编译成 CommonJS 规范（注意：语法一样，但具体实现还 是 require/exports）
4. require/exports 是运行时动态加载，import/export 是静态编译
5. require/exports 输出的是一个值的拷贝，import/export 模块输出的是值的引用
6. ES6 模块可以在 import 引用语句前使用模块，CommonJS 则需要先引用后使用
7. import/export 只能在模块顶层使用，不能在函数、判断语句等代码块之中引用；require/exports 可以
8. import/export 导出的模块默认调用严格模式。 require/exports 默认不使用严格模式，可以自定义是否使用严格模式


### package.json 文件
- version 表明了当前的版本。
- name 设置了应用程序/软件包的名称。
- description 是应用程序/软件包的简短描述。
- main 设置了应用程序的入口点。
- private 如果设置为 true，则可以防止应用程序/软件包被意外地发布到 npm。
- scripts 定义了一组可以运行的 node 脚本。
- dependencies 设置了作为依赖安装的 npm 软件包的列表。
- devDependencies 设置了作为开发依赖安装的 npm 软件包的列表。
- engines 设置了此软件包/应用程序在哪个版本的 Node.js 上运行。
- browserslist 用于告知要支持哪些浏览器（及其版本）。
- author 列出软件包的作者名称。
- contributors 除作者外，该项目可以有一个或多个贡献者。
- bugs 链接到软件包的问题跟踪器，最常用的是 GitHub 的 issues 页面。
- homepage 设置软件包的主页。
- license 指定软件包的许可证。
- keywords 此属性包含与软件包功能相关的关键字数组。
- repository 此属性指定了此程序包仓库所在的位置。
