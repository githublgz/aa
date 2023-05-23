---
title: webpack多页面应用
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-11-09 23:48:17
author:
img:
coverImg:
password:
summary:
keywords:
---
**entry配置**
在实际项目的开发中一个完整的系统不会将所有的功能都放在一个网页上，因为这样会导致网页的性能不佳，实际可以按照功能模块划分多个单页应用每个单页应用又生处一个html文件并且随着业务的发展更多的单页应用可以被逐渐的加入到这个项目里。
```
entry: ['./src/file_1.js', './src/file_2.js'，'node_modules里的模块 lodash'],
entry:{
	main:['./src/file_1.js', './src/file_2.js'],    //这里使用lodash  还会打包在这里打包一遍
	lodash:'lodash',
}
entry:{
	main:{
	import :['./src/file_1.js', './src/file_2.js'],  //这两个文件可能依赖于lodash，而lodash单独打包了，所以dependOn这个依赖可以把公共的lodash给抽离出来            lodash  就不会在打包一遍了
	dependOn:'lodash',           // 做依赖  这里的lodash  是下面的对应名  可以随意起
	},    //这里使用lodash  还会打包在这里打包一遍
	lodash:'lodash',
}
```
对象语法会比较繁琐。然而，这是应用程序中定义入口的最可扩展的方式。
描述入口的对象：
用于描述入口的对象。你可以使用如下属性：
dependOn : 当前入口所依赖的入口。它们必须在该入口被加载前被加载。
filename : 指定要输出的文件名称。
import : 启动时需加载的模块。
library : 指定 library 选项，为当前 entry 构建一个 library。
runtime : 运行时 chunk 的名字。如果设置了，就会创建一个新的运行时
chunk。在 webpack 5.43.0 之后可将其设为 false 以避免一个新的运行时
chunk。
publicPath : 当该入口的输出文件在浏览器中被引用时，为它们指定一个公共
URL 地址。请查看 output.publicPath。
webpack.config.js
runtime 和 dependOn 不应在同一个入口上同时使用，所以如下配置无效，并且会
抛出错误：
```
module.exports = {
 entry: {
  a2: 'dependingfile.js',
  b2: {
   dependOn: 'a2',
   import: './src/app.js',
 },
},
};
```
webpack.config.js
```
module.exports = {
 entry: {
  a2: './a',
  b2: {
   runtime: 'x2',
   dependOn: 'a2',
   import: './b',
 },
},
};
```
确保 runtime 不能指向已存在的入口名称，例如下面配置会抛出一个错误：
```
module.exports = {
 entry: {
  a1: './a',
  b1: {
   runtime: 'a1',
   import: './b',
 },
},
};
```
另外 dependOn 不能是循环引用的，下面的例子也会出现错误
```
module.exports = {
 entry: {
  a3: {
   import: './a',
   dependOn: 'b3',
 },
  b3: {
   import: './b',
   dependOn: 'a3',
 },
},
};
```

**配置index.html模板**
要生成多个HTML文件，请在插件数组中多次声明插件。
```
{
 entry: {
 	main:{
		import :[],
		dependOn:'lodash2',
		filename:'chanel1.[name].js'
	},
	lodash2:{
		import :'lodash',
		filename:'common/[name].js'
	}
 },

 plugins: [
  new HtmlWebpackPlugin(), // Generates default index.html
  new HtmlWebpackPlugin({  // Also generate a test.html
   title:   'ejs',     //在页面可以使用ejs模板语法获取数据
   filename: 'chanel1/test.html',                      //执行打包后的页面文件     输出的文件名
   template: 'src/assets/test.html'       //指定模板的路径
   inject:'body/head',                       // 定义当前所生成的script标签的位置
   chunks:['自定义那个入口  如：main.js']，              //规定当前页面到底打包那些chunk   如何实现多个页面去载入不同的chunk   chunk就是我们在路口配置的项    每一项就是一个chunk  默认会把所有chunk都放进去
   publicPash:'http://www.a.com/'        //包的前缀
 })
 ]
}


<title><%= htmlWebpackPlugin.options.title  %></title>  options.就是我们在HtmlWebpackPlugin定制的选项
index.html
<!DOCTYPE html>
<html>
 <head>
  <meta charset="utf-8"/>
  <title><%= htmlWebpackPlugin.options.title %></title>
 </head>
 <body>
 </body>
</html>
```

**多页面应用**
```
module.exports = {
 entry: {
  pageOne: './src/pageOne/index.js',
  pageTwo: './src/pageTwo/index.js',
  pageThree: './src/pageThree/index.js',
},
};
```
这是什么？ 我们告诉 webpack 需要三个独立分离的依赖图（如上面的示例）

为什么？ 在多页面应用程序中，server 会拉取一个新的 HTML 文档给你的客户端。
页面重新加载此新文档，并且资源被重新下载。然而，这给了我们特殊的机会去做很
多事，例如使用 optimization.splitChunks 为页面间共享的应用程序代码创建
bundle。由于入口起点数量的增多，多页应用能够复用多个入口起点之间的大量代
码/模块，从而可以极大地从这些技术中受益。