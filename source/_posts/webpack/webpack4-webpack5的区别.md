---
title: webpack4与webpack5的区别
cover: false
top: false
toc: false
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-10-14 9:36:54
author:
img:
coverImg:
password:
summary:
keywords:
---
# Webpack5 关注下面内容：
通过持久缓存提高构建性能.

使用更好的算法和默认值来改善长期缓存.

通过更好的树摇和代码生成来改善捆绑包大小.

清除处于怪异状态的内部结构，同时在 v4 中实现功能而不引入任何重大更改.

下载:
```
npm i webpack@5 webpack-cli -D
```
## 一、压缩代码
1.webpack4

webpack4 上需要下载安装 terser-webpack-plugin 插件，并且需要以下配置：
```
const TerserPlugin = require('terser-webpack-plugin')

module.exports = { 
// ...other config
optimization: {
  minimize: !isDev,
  minimizer: [
    new TerserPlugin({
      extractComments: false, 
      terserOptions: { 
        compress: { 
          pure_funcs: ['console.log'] 
        }
      }
    }) ]
 }
```
 2.webpack5
内部本身就自带 js 压缩功能，他内置了 terser-webpack-plugin 插件，我们不用再下载安装。而且在 mode=“production” 的时候会自动开启 js 压缩功能。

如果你要在开发环境使用，就用下面：
```
  // webpack.config.js中
  module.exports = {
     optimization: {
       usedExports: true, //只导出被使用的模块
       minimize : true // 启动压缩
     }
  }

```

## 二、webpack 缓存
1.webpack4 缓存配置
npm install hard-source-webpack-plugin -D
```
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin')

module.exports = {
plugins: [
  // 其它 plugin...
  new HardSourceWebpackPlugin(),
] }
```
 2. webpack5 缓存配置

webpack5 内部内置了 cache 缓存机制。直接配置即可。

cache 会在开发模式下被设置成 type： memory 而且会在生产模式把cache 给禁用掉。
```
// webpack.config.js
module.exports= {
  // 使用持久化缓存
  cache: {
    type: 'filesystem'，
    cacheDirectory: path.join(__dirname, 'node_modules/.cac/webpack')
  }
}
```
type 的可选值为： memory 使用内容缓存，filesystem 使用文件缓存。

## 三、启动服务的差别
1.webpack4 启动服务

通过 webpack-dev-server 启动服务

2.webpack5 启动服务

内置使用 webpack serve 启动，但是他的日志不是很好，所以一般都加都喜欢用 webpack-dev-server 优化。

## 四、devtool的差别
ourceMap需要在 webpack.config.js里面直接配置 devtool 就可以实现了。而 devtool有很多个选项值，不同的选项值，不同的选项产生的 .map 文件不同，打包速度不同。

一般情况下，我们一般在开发环境配置用“cheap-eval-module-source-map”，在生产环境用‘none’。

devtool在webpack4和webpack5上也是有区别的

v4: devtool: 'cheap-eval-module-source-map'

v5: devtool: 'eval-cheap-module-source-map'

## 五、自动删除 Node.js Polyfills

早期，webpack 的目标是允许在浏览器中运行大多数 node.js 模块，但是模块格局发生了变化，许多模块用途现在主要是为前端目的而编写的。webpack <= 4 附带了许多 node.js 核心模块的 polyfill，一旦模块使用任何核心模块（即 crypto 模块），这些模块就会自动应用。

尽管这使使用为 node.js 编写的模块变得容易，但它会将这些巨大的 polyfill 添加到包中。在许多情况下，这些 polyfill 是不必要的。

webpack 5 会自动停止填充这些核心模块，并专注于与前端兼容的模块。

迁移：

尽可能尝试使用与前端兼容的模块。

可以为 node.js 核心模块手动添加一个 polyfill。错误消息将提示如何实现该目标。

## 六、Chunk 和  Chunk ID
添加了用于长期缓存的新算法。在生产模式下默认情况下启用这些功能。

你可以不用使用 import(/* webpackChunkName: "name" */ "module") 在开发环境来为 chunk 命名，生产环境还是有必要的

webpack 内部有 chunk 命名规则，不再是以 id(0, 1, 2)命名了

## 七、Tree Shaking
果我们的项目中引入了 lodash 包，但是我只有了其中的一个方法。其他没有用到的方法是不是冗余的？此时 tree-shaking 就可以把没有用的那些东西剔除掉，来大大减少最终的bundle体积。

usedExports : true, 标记没有用的叶子
minimize: true, 摇掉那些没有用的叶子

```
 // webpack.config.js中

  module.exports = {

     optimization: {

       usedExports: true, //只导出被使用的模块

       minimize : true // 启动压缩

     }

  }
```

现在可以处理commonjs的tree shaking


## 八、默认值
entry: "./src/index.js

output.path: path.resolve(__dirname, "dist")

output.filename: "[name].js"

## 九、Caching
```
// 配置缓存
cache: {
  // 磁盘存储
  type: "filesystem",
  buildDependencies: {
    // 当配置修改时，缓存失效
    config: [__filename]
  }
}
```

## 十、SplitChunk
```
// webpack4
minSize: 30000;

// webpack5
minSize: {
  javascript: 30000,
  style: 50000,
}
```