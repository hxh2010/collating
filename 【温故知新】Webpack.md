---
title: 【温故知新】Webpack
date: 2021-05-30 00:00:00
tags:
  - 面试
  - webpack
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/webpack.png
---

![](https://hongxh.cn/img/study_img/webpack.png)

### bundle 、chunk 、module 的理解
1. bundle 是webpack打包出来的文件
  - bundle通常是由多个不同的模块产生，它是已经加载完毕和被编译后的源代码的最终版本。
  - bundle splitting: 这是webpack优化代码的一种方法，即将一个单独的应用拆解为多个 bundle。
    通过将多个bundle中公共引入的模块拆解出来，我们可以减少项目的代码量，
    从而减小应用的体积，并且通过浏览器缓存，我们可以将代码进一步优化。
2. chunk 是webpack在进行模块的依赖分析的时候，代码分割出来的代码块。
   - chunk 这个webpack中专用的术语用于管理webpack内部的打包进程。bundle由许多chunk组成，
     chunk有几种类型，比如说“入口”和“子块”。通常chunk和输出的bundle一一对应，但是，有些是一对多的关系。
3. module是开发中的单个模块，我自己的理解是单个文件。

- module 就是没有被编译之前的代码，通过 webpack 的根据文件引用关系生成 chunk 文件，
  webpack 处理好 chunk 文件后，生成运行在浏览器中的代码 bundle


### source map 的理解
- 简单说，Source map就是一个信息文件，里面储存着位置信息。也就是说，转换后的代码的每一个位置，
  所对应的转换前的位置。 有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码。这无疑给开发者带来了很大方便。
- source map 是将编译、打包、压缩后的代码映射回源代码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要 soucre map。
- map文件只要不打开开发者工具，浏览器是不会加载的。
- 线上环境一般有三种处理方案：
  - hidden-source-map：借助第三方错误监控平台 Sentry 使用
  - nosources-source-map：只会显示具体行数以及查看源代码的错误栈。安全性比 sourcemap 高
  - sourcemap：通过 nginx 设置将 .map 文件只对白名单开放(公司内网)
- 注意：避免在生产中使用 inline- 和 eval-，因为它们会增加 bundle 体积大小，并降低整体性能。


### loader 的理解
- loader本质上就是一个函数，这个函数会在我们在我们加载一些文件时执行
- loader 支持链式调用，所以开发上需要严格遵循“单一职责”，每个 Loader 只负责自己需要负责的事情。

- 实现自己的 loader (同步)
```javascript
module.exports = function (source) {
  console.log('source>>>>', source)
  source += '...'
  return source
}
```

### plugin 的理解
- webpack在运行的生命周期中会广播出许多事件，plugin 可以监听这些事件，在特定的阶段钩入想要添加的自定义功能。
  Webpack 的 Tapable 事件流机制保证了插件的有序性，使得整个系统扩展性良好。
- 一般都是new Plugin()这种形式使用，所以，首先应该明确的是，plugin应该是一个类。

```javascript
// 需要实现 apply 方法
class DemoWebpackPlugin {
    constructor () {
        console.log('plugin init')
    }
    apply (compiler) {

    }
}

module.exports = DemoWebpackPlugin
```


### babel 的理解
- 解析：将代码转换成 AST
- 转换：访问 AST 的节点进行变换操作生产新的 AST （Taro就是利用 babel 完成的小程序语法转换）
- 生成：以新的 AST 为基础生成代码


#### webpack 打包流程
1. entry-option
  - option 初始化
2. webpack 初始化
  -构建 compiler 编译对象；
  -调用基础插件；对 options 进行 require；
  - 初始化 compiler 上下文；
3. 编译
  - 找到 entry 入口；
  - 解析入口，通过工厂函数和单例创建模块；
  - 对模块 build 处理依赖 module，循环异步处理；
  - 针对不同的模块使用相应的 loader
  - 编译模块，生成抽象语法树 AST。
  - 循环遍历 AST 树，拼接输出 js。
4. 合并拆分
  - seal 方法封装
5. compiler 产生静态文件，插件做最后修改；
6. 输出 output


### webpack 的优化
1. 减少 Webpack 打包时间
- 优化 Loader：优化范围
  
```javascript
module.exports = {
  module: {
    rules: [
      {
        // js 文件才使用 babel
        test: /\.js$/,
        loader: 'babel-loader',
        // 只在 src 文件夹下查找
        include: [resolve('src')],
        // 不会去查找的路径
        exclude: /node_modules/
      }
    ]
  }
}
```

- HappyPack：HappyPack 可以将 Loader 的同步执行转换为并行的，这样就能充分利用系统资源来加快打包效率了

```javascript
export default {
  module: {
    loaders: [
      {
        test: /\.js$/,
        include: [resolve('src')],
        exclude: /node_modules/,
        // id 后面的内容对应下面
        loader: 'happypack/loader?id=happybabel'
      }
    ]
  },
  plugins: [
    new HappyPack({
      id: 'happybabel',
      loaders: ['babel-loader?cacheDirectory'],
      // 开启 4 个线程
      threads: 4
    })
  ]
}
```

- DllPlugin：DllPlugin 可以将特定的类库提前打包然后引入。这种方式可以极大的减少打包类库的次数，
  只有当类库更新版本才有需要重新打包，并且也实现了将公共代码抽离成单独文件的优化方案。

```javascript
// 单独配置在一个文件中
// webpack.dll.conf.js
const path = require('path')
const webpack = require('webpack')
module.exports = {
  entry: {
    // 想统一打包的类库
    vendor: ['react']
  },
  output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].dll.js',
    library: '[name]-[hash]'
  },
  plugins: [
    new webpack.DllPlugin({
      // name 必须和 output.library 一致
      name: '[name]-[hash]',
      // 该属性需要与 DllReferencePlugin 中一致
      context: __dirname,
      path: path.join(__dirname, 'dist', '[name]-manifest.json')
    })
  ]
}


// webpack.conf.js DllReferencePlugin 将依赖文件引入
module.exports = {
  // ...省略其他配置
  plugins: [
    new webpack.DllReferencePlugin({
      context: __dirname,
      // manifest 就是之前打包出来的 json 文件
      manifest: require('./dist/vendor-manifest.json'),
    })
  ]
}
```

2. 减少打包体积
- 使用 webpack-bundle-analyzer 进行分析
- uglifyjs 对js、css、进行压缩
- 按需加载
- Scope Hoisting：Scope Hoisting 直译过来就是「作用域提升」，可以分析出模块之间的依赖关系，尽可能把打包出来的模块合并到一个函数中
- Tree Shaking：Tree Shaking 可以删除项目中未被使用的代码
- 动态Polyfill

3. 缩小构建目标、减少文件搜索范围
- exclude 与 include的使用：
- 这个主要是resolve相关的配置，用来设置模块如何被解析。通过resolve的配置，可以帮助Webpack快速查找依赖，也可以替换对应的依赖。
- resolve.modules：告诉 webpack 解析模块时应该搜索的目录
- resolve.mainFields：当从 npm 包中导入模块时（例如，import * as React from 'react'），
  此选项将决定在 package.json 中使用哪个字段导入模块。根据 webpack 配置中指定的 target 不同，默认值也会有所不同
- resolve.mainFiles：解析目录时要使用的文件名，默认是index
- resolve.extensions：文件扩展名

4. 利用缓存提升二次构建的速度
- babel-loader开启缓存 
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: [{
          loader: "babel-loader",
          options: {
            cacheDirectory: true
          }
        }],
      }
    ]
  }
}
```
- 使用cache-loader 
```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.ext$/,
        use: [
          'cache-loader',
          ...loaders
        ],
        include: path.resolve('src')
      }
    ]
  }
}
```
- 使用hard-source-webpack-plugin：
  配置 hard-source-webpack-plugin后，首次构建时间并不会有太大的变化，
  但是从第二次开始，构建时间大约可以减少 80%左右。


