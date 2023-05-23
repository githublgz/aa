---
title: webpack创建library
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-10-31 14:21:26
author:
img:
coverImg:
password:
summary:
keywords:
---
除了打包应用程序，webpack 还可以用于打包 JavaScript library。
例如：当我们想要自己开发一个组件库工具或者框架的时候也就是说我们自己造一个轮子给别人用的时候我们免不了要开发很多的模块，最终都可以请webpack来帮我们打包。

**创建一个 library**
假设我们正在编写一个名为 webpack-numbers 的小的 library，可以将数字 1 到 5转换为文本表示，反之亦然，例如将 2 转换为 'two'。
使用 npm 初始化项目，然后安装 webpack ， webpack-cli 和 lodash ：
```
npm i webpack webpack-cli lodash -D
```
我们将 lodash 安装为 devDependencies 而不是 dependencies ，因为我们不需要将其打包到我们的库中，否则我们的库体积很容易变大。

src/ref.json
```
[
{
  "num": 1,
  "word": "One"
},
{
  "num": 2,
  "word": "Two"
},
{
  "num": 3,
  "word": "Three"
},
{
  "num": 4,
  "word": "Four"
},
{
  "num": 5,
  "word": "Five"
},
{
  "num": 0,
  "word": "Zero"
}
]
```
src/index.js
```
import _ from 'lodash';
import numRef from './ref.json';
export function numToWord(num) {
 return _.reduce(
  numRef,
 (accum, ref) => {
   return ref.num === num ? ref.word : accum;
 },
  ''
);
}
export function wordToNum(word) {
 return _.reduce(
  numRef,
 (accum, ref) => {
   return ref.word === word && word.toLowerCase() ? ref.num :
accum;
 },
  -1
);
}
```

```
const path = require('path');
module.exports = {
 mode:'production',
 entry: './src/index.js',
 output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'webpack-numbers.js',
  library:{
    name:'webpackNumbers',
	type'umd'
  },
  globalObject:'globalThis'
},
 externals:{
 	lodash:{   lodash  引入包的名字
		commonjs:'lodash',   //  兼容引入形式
		commonjs2:'lodash',
		amd:'lodash',
		root:'_'
	}
 }
};
```

**发布到线上**
拥有npm账号
npm config get registry   // 一定要保证本地的registry的地址是https://registry.npmjs.org/    因为这样的话才是真正的连接到npm官网的地址上   有些人可能是淘宝  那么就访问不上去了
npm adduser  //添加用户   输入用户名密码
包的名字必须是全球唯一的。   npm publish    上传时package.json中的main入口必须对应：dist/文件名   因为别人通过 require去载入包的时候  会读取 这个main这是的暴露的包的名字
npm install 包名就可以下载了




**Webpack 配置**



webpack.config.js
```
output:{
	path:'',
	filname:'',
	library:'',    //我们只是定义了导出 并且没有使用它  所以webpack认为这个代码是没有用的。   如何让他作为一个library来进行一个对外的打包   让代码不被 Webpack Tree shaking   配置 library:'包的名字'
}
```

到目前为止，一切都应该与打包应用程序一样，这里是不同的部分 - 我们需要通过output.library 配置项暴露从入口导出的内容。
我们暴露了 webpackNumbers ，以便用户可以通过 script 标签使用。	
```
<script src="https://example.org/webpack-numbers.js"></script>
<script>
 window.webpackNumbers.wordToNum('Five');
</script>
```

然而它只能通过被 script 标签引用而发挥作用，它不能运行在 CommonJS、AMD、Node.js 等环境中。
作为一个库作者，我们希望它能够兼容不同的环境，也就是说，用户应该能够通过以下方式使用打包后的库：
CommonJS module require:
```
const webpackNumbers = require('webpack-numbers');
// ...
webpackNumbers.wordToNum('Two');
```
AMD module require:
```
require(['webpackNumbers'], function (webpackNumbers) {
 // ...
 webpackNumbers.wordToNum('Two');
});
```
script tag:
```
<!DOCTYPE html>
<html>
...
 <script src="https://example.org/webpack-numbers.js">
</script>
 <script>
  // ...
  // Global variable
  webpackNumbers.wordToNum('Five');
  // Property in the window object
  window.webpackNumbers.wordToNum('Five');
  // ...
 </script>
</html>
```

我们更新 output.library 配置项，将其 type 设置为 'umd' ：

```
const path = require('path');
module.exports = {
 entry: './src/index.js',
 output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'webpack-numbers.js',
  library: {
   name: 'webpackNumbers',
   type: 'umd',   //  window'(es module)   'commonjs'   'module' 它在experiments:{outputModule:true}才能使用，他是一个实验性的功能，就不需要这个name了。<script type="module">    'umd'支持所有的类型。  esmodule 有问题
  },
  globalObject:'globalThis'    //需要全局的this来去  代替self   否则浏览器会self  undefined报错
 },
};
```
现在 webpack 将打包一个库，其可以与 CommonJS、AMD 以及 script 标签使用