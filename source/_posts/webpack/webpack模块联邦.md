---
title: webpack模块联邦
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-12-07 15:24:58
author:
img:
coverImg:
password:
summary:
keywords:
---
多个独立的构建可以组成一个应用程序，这些独立的构建之间不应该存在依赖关系，因此可以单独开发和部署它们。
这通常被称作微前端，但并不仅限于此。
Webpack5 模块联邦可以让 Webpack 达到了线上 Runtime 的效果，让代码直接在项目间利用 CDN 直接共享，不再需要本地安装 Npm 包、构建再发布了！
我们知道 Webpack 可以通过 DLL 或者 Externals 做代码共享时 Common Chunk，但不同应用和项目间这个任务就变得困难了，我们几乎无法在项目之间做到按需热插拔。

早期NPM方式共享模块   代码的共享是将依赖作为library安装到我们的项目里进行webpack打包并且构建上线
对于项目 Home 与 Search，需要共享一个模块时，最常见的办法就是将其抽成通用依赖并分别安装在各自项目中。虽然 Monorepo 可以一定程度解决重复安装和修改困难的问题，但依然需要走本地编译。

真正 Runtime 的方式可能是 UMD 方式共享代码模块，即将模块用 Webpack UMD模式打包，并输出到其他项目中。这是非常普遍的模块共享方式：
对于项目 Home 与 Search，直接利用 UMD 包复用一个模块。但这种技术方案问题也很明显，就是包体积无法达到本地编译时的优化效果，且库之间容易冲突。

微前端：micro-frontends (MFE) 也是最近比较火的模块共享管理方式，微前端就是要解决多项目并存问题，多项目并存的最大问题就是模块共享，模块之间是不能有冲突。  对于微前端我们还要考虑样式冲突，声明周期管理冲突等问题，我们先不考虑这些   想把问题聚焦在资源加载的方式上   微前端一般有两种打包方式：1.子应用独立打包，模块实现解耦，但这种方式无法抽取公共的依赖，2.整体应用打一个打包 很好的解决我们上面第一种方式的问题，但是打包效率速度实在是太慢了。不具备水平的扩展能力。
由于微前端还要考虑样式冲突、生命周期管理，所以本文只聚焦在资源加载方式上。微前端一般有两种打包方式：
1. 子应用独立打包，模块更解耦，但无法抽取公共依赖等。
2. 整体应用一起打包，很好解决上面的问题，但打包速度实在是太慢了，不具备水平扩展能力。


终于提到本文的主角了，模块联邦方式作为 Webpack5 内置核心特性之一的 FederatedModule：
这个方案是直接将一个应用的包应用于另一个应用，同时具备整体应用一起打包的公共依赖抽取能力。  比如：我们直接可以在Search应用里直接使用已经发布到线上的Home应用的组件。

**应用案例**
本案例模拟三个应用： Nav 、 Search 及 Home 。每个应用都是独立的，又通过模块邦联系到了一起。
比如Home需要使用Nav组件共享出来的header，Search可能要使用Header和Home组件构建出来的HomeList。\
模块联邦将他们共享的模块暴露出来进行引用。
1、Nav 导航
src/header.js
```
const Header = () => {
 const header = document.createElement('h1')
 header.textContent = '公共头部内容'
 return header
}
export default Header
```
src/index.js
```
import Header from './Header'
const div = document.createElement('div')
div.appendChild(Header())
document.body.appendChild(div)
```
webpack.config.js
```
const HtmlWebpackPlugin = require('html-webpack-plugin')
const {
 ModuleFederationPlugin
} = require('webpack').container
module.exports = {
 mode: 'production',
 entry: './src/index.js',
 plugins: [
  new HtmlWebpackPlugin(),
  new ModuleFederationPlugin({
   // 模块联邦名字
   name: 'nav',
   // 外部访问的资源名字
   filename: 'remoteEntry.js',
   // 引用的外部资源列表
   remotes: {},
   // 暴露给外部资源列表
   exposes: {
    './Header': './src/Header.js',   // 暴露 Header组件  key：可以定义成./Header 这个./Header并不代表是我当前引用下的某个路径   而是将来在别人用的时候基于这个路径来拼接url，值是正真的我们本地项目的应用
  },
   // 共享模块，如lodash
   shared: {},   // 如果我们的 header模块里有共享的第三方模块比如：lodash等，我们可以把他放到这里在打包的时候可以把第三方的共享的模块打到单独的一个包里。
 }),
]
}
```
应用 webpack 运行服务：
```
[felix] nav $ npx webpack serve --port 3003
```

2、Home 首页
src/HomeList
```
const HomeList = (num) => {
 let str = '<ul>'
 for (let i = 0; i < num; i++) {
  str += '<li>item ' + i + '</li>'
}
 str += '</ul>'
 return str
}
export default HomeList
```
src/index.js
```
import HomeList from './HomeList'
remotes: {
    nav
	('nav/Header')：remotes: { nav }Header    exposes: {./Header':''}
import('nav/Header').then((Header) => {  //引用模块联邦的组件  这样导入别人组件的时候需要通过异步的方式因为 网络共享或者是模块载入 是由延迟的，所以要通过promise的方式（异步模块加载的形式）去引用它。
 const body = document.createElement('div')
 body.appendChild(Header.default())
 document.body.appendChild(body)
 document.body.innerHTML += HomeList(5)
})
```
webpack.config.js
```
const HtmlWebpackPlugin = require('html-webpack-plugin')
const {
 ModuleFederationPlugin
} = require('webpack').container
module.exports = {
 mode: 'production',
 entry: './src/index.js',
 plugins: [
  new HtmlWebpackPlugin(),
  new ModuleFederationPlugin({
   name: "home",
   filename: "remoteEntry.js",
   remotes: {
    nav: "nav@http://localhost:3003/remoteEntry.js",    // 引用第三方或别人写好的应用的路径。远端的服务路径
  },
   exposes: {
    './HomeList': './src/HomeList.js',
  },
   shared: {},
 }),
]
}
```
应用 webpack 运行服务：
```
[felix] nav $ npx webpack serve --port 3001
```

3、search 搜索
src/index
```
Promise.all([import('nav/Header'), import('home/HomeList')])
.then(([{
  default: Header
}, {
  default: HomeList
}]) => {
  document.body.appendChild(Header())
  document.body.innerHTML += HomeList(4)
})
```
webpack.config.js
```
const HtmlWebpackPlugin = require('html-webpack-plugin')
const {
 ModuleFederationPlugin
} = require('webpack').container
module.exports = {
 mode: 'production',
 entry: './src/index.js',
 plugins: [
  new HtmlWebpackPlugin(),
  new ModuleFederationPlugin({
   name: 'search',
   filename: 'remoteEntry.js',
   remotes: {
    nav: "nav@http://localhost:3003/remoteEntry.js",
    home: "home@http://localhost:3001/remoteEntry.js"
  },
   exposes: {},
   shared: {},
 }),
]
}
```
应用 webpack 运行服务：
```
[felix] nav $ npx webpack serve --port 3002
```