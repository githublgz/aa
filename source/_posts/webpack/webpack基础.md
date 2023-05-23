---
title: webpack基础
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-11-24 10:08:48
author:
img:
coverImg:
password:
summary:
keywords:
---

## 一.基础感念
webpack是运行在nodejs中的
webpack 的 why 与how

为什么学   （原先script要按顺序）
构建打包？习惯使用开箱即用的脚手架来完成项目配置，对开发环境和生产环境的搭建了解知之甚少，前端架构最重要的点在于 前端工程化（webpack就是我们搭建前端环境的技术选型，也是最主流的）

为什么需要使用webpack
问题：作用域，文件大（网络瓶颈，短暂白屏），可读性差，可维护性弱

解决：
1.作用域：使用grunt和gulp管理项目资源，称之为任务执行器，将所有的项目文件拼接在一起，（利用js的立即调用表达式IIFE）
	当函数变成一个立即调用表达式时，表达式的变量是不能在外部访问的，不会污染我们的window环境，解决了作用域问题。
	可以通过赋值变量 函数中通过return暴露出来变量获取数据
![作用域问题](http://hghqz.vip/webpack/%E4%BD%9C%E7%94%A8%E5%9F%9F%E9%97%AE%E9%A2%98.jpg "作用域问题")

	问题使用十行代码可能就要加载万行代码（通过代码拆分解决,模块化，commonjs,amd,cmd,es6）
node环境语法在浏览器无法加载它不支持commonjs  以及一些其他的语法
早期解决方案（页面导入require  define,script标签data-main）
现在ECMAscript  通过es6拥有自己的模块化  export default  import from (script 标签指定type="module")
通过http-server搭建一个http请求

es6可能还不太兼容所以使用webpack帮我们打包（vs竞品 parcel称零配置用户一般无需做其他的配置开即聚用 rollup.js用标准化的格式来编写代码es6通过减少无用的代码来尽可能的缩小包的体积，一般只用来打包js  黑马vite vue3使用  有petite vue框架 从开发 编译 发布 demo 几乎全部都是Vite完成的 基于esmodule的构建方式 按需编译 热模块更新  丝滑体验 与vue3原理结合）
不会谁取代谁  各有各的应用场景
## 二.基础应用
**安装使用**（要注意webpack与nodejs版本兼容问题）
1.要有nodejs 运行环境
2.全局与工作目录下安装
	npm install webpack webpack-cli --global  全局 在任何目录都可以运行webpack(不推荐全局使用)
	本地安装需要先有npm的包管理的配置文件npm init -y  npm install webpack webpack-cli --save-dev
**运行**
1.直接使用 webpack  默认打包  （入口：./src/index 出口：dist/main.js）
2.webpack --stats detailed 可以看到详细的打包信息
npx 执行没有找到命令会到上层目录查找

**自定义webpack配置**
webpack --help查看 配置
npx 工具概念：
命令配置：
npx webpack --entry ./src/index.js --mode production

配置文件配置(webpack.config.js文件名不允许随意起，他是webpack自动读取的)
![出入口基本配置]( http://hghqz.vip/webpack/%E5%87%BA%E5%85%A5%E5%8F%A3%E5%9F%BA%E6%9C%AC%E9%85%8D%E7%BD%AE.jpg "出入口基本配置")
path需是绝对路径

## 三.自动引入资源（插件）（html中引入资源）
**什么是插件：**
webpack就像是一条生产线，需要经过一系列的流程后才能将源文件（入口文件，可以相互依赖（js,css-loaders加载器））经过编译（工具插件plugins 帮助webpack来执行一些特定的任务（打包优化，资源管理））转化成输出的结果

插件：三组（community社区插件   webpack内置官方插件   webpack contrib 第三方插件）

**如何使用htmlWebpackPlugin?**
npm install html-webpack-plugin -D

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin)
module.exports={
	plugins:[
		new HtmlWebpackPlugin({
			template:'./index.html',
			filename:'app.html',
			inject:'body'
		})
	]
}
```

**清理上次dist里的垃圾**
```javascript
output:{
	filename:    ,
	path:    ,
	clean:true
}
```
## 四.搭建开发环境
解决手工工作：复制index.html路径到地址栏才能访问页面，每次更新代码都需要刷新页面。通过搭建开发环境来解决，提高开发体验

mode:'development'  开发模式   通过代码对环境进行调试（core.js模块）
```javascript
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "cross-env NODE_ENV=production webpack --config webpack.config.js",
    "dev": "cross-env NODE_ENV=development webpack-dev-server --config webpack.config.js",
    "start": "cross-env NODE_ENV=development webpack serve"
  },
  const isDev = process.env.NODE_ENV === "development";
```

**精准锁定代码出错的位置**
```javascript
devtool:'inline-source-map',
```
**使用watch mode(观察模式)**
每次编译都要手动运行npx webpack
npx webpack --watch   实时监测编译变化 （需要重新刷新页面）

**webpack-dev-server**
为我们提供了一个最基本的web server，并具有live reloading（实时重新加载）功能。当页面修改了编译后浏览器会监听到文件的修改实现自动刷新
npm install webpack-dev-server -D
```javascript
plugins:{
	devServer:{
		static: './dist' #server的根目录
	}
}
```
npx debpack-dev-server --open(自动打开一个页面)  启动项目   实际是吧数据的打包以后的bundle文件放到了内存里。提高开发效率和webpack的编译效率

## 五.资源模块加载
内置资源模块asset modules来引入任何的其他类型资源   是一种模块类型  它允许我们用webpack来打包其他的资源文件 如：字体，图标等
资源模块类型：asset module type   会通过四种新的类型模块来替换所有的loader
1.asset/resource他会发送一个单独的文件并导出URL  2.asset/inline 它会导出一个资源的Data URL
3.asset/source 会导出资源的源代码
4.asset 通用资源类型 会在导出一个Data URL 和发送一个单独的文件之间自动进行选择

module配置模块
```javascript
output:{
	assetModuleFilename:'images/[contenthash].[ext]' //自动生成一个文件名//根据文件的内容来生成一个哈希的字符串
},
module:{
	rules:{ //规则
		{  //配置对象来去加载不同类型的文件
			test: '/\.png$/', //test 更正则表达式 来加载定义文件的类型
		 	type:'asset/resource',// 图片为URL格式
			generator:{ // 优先级高于在output 配置的优先级
				filename:'images/[contenthash].[ext]'
			}
		},
		{
			test: '/\.svg$/',
			type: 'asset/inline' //图片为base64格式
		},
		{
			test: '/\.txt$/',
			type: 'asset/source' // 加载文件的源内容
		},
		{
			test: '/\.jpg$/',
			type: 'asset', //会在导出一个Data URL 和发送一个单独的文件之间自动进行选择   小于8k生成base64  大于8k创建资源
			parser:{
				dataUrlCondition:{
					maxSize:4 * 1024 * 1024  // 当突变大于4兆在生成一个资源文件  否则的话就是base64
				}
			}
		}
	}
}
```

## 六.loader
上面采用四种资源模块来引入外部资源
webpack还可以通过loader引入其他类型的文件
webpack智能理解js和json这样的文件
loader可以让webpack去解析其他的类型的文件  并且将这些文件转化为有效的模块  以供我们应用程序的使用  例如加载css

**如何使用**
```javascript
module:{
	rules:{
		{ //当碰到通过require或import去解析 .txt文件时在对文件进行打包之前先使用raw-loader进行转化
			test: '/\.txt$/', // 定义那种类型的文件被转换
		 	use:'raw-loader',  // 在定义转化的时候应该使用哪个loader来进行转换
		}
	}
}
```

**如何配置**
```javascript
module:{
	rules:{
		{
			test: '/\.css$/',
		 	use:['style-loader','css-loader'],  // 自下而上 loader链式调用  先需要用css-loader让我们打包没有问题识别我们css文件   在通过style-loader把css放置到我们的页面上
		}，
		{
			test: '/\.(css|less)/$',
		 	use:['style-loader','css-loader','less-loader'],
		}，
	}
}
```
**抽离和压缩css**
抽离：npm install mini-css-extract-plugin -D   这个插件时基于webpack5构建的
压缩：npm install css-minimizer-webpack-plugin -D  这个属于优化插件在optimization选项配置

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const CssMinimizerWebpackPlugin = require('css-minimizer-webpack-plugin')

mode:'production',   //使用压缩环境必须时这个
plugins:{
	new MiniCssExtractPlugin({
		filename:'style/[contenthash].css'
	})
}
module:{
	rules:{
		{
			test: '/\.(css|less)/$',
		 	use:[MiniCssExtractPlugin.loader,'css-loader','less-loader'],  //之前通过style-loader 将样式放到页面的style 更改为通过link引入的css文件
		}
	}
}
optimization:{
	minimizer:[
		new CssMinimizerWebpackPlugin()
	]
}
```

**加载image图像**
如何在css里加载图片资源
mode:'development'   清除提示

**加载fonts字体**
css3中新增webfont功能可以在css3中去加载一个font字库  然后在代码中就可以定义自己的icon图标了
如何去加载字体这样的资源（asset module资源模块）
```javascript
{
	test:'/\.(woff|woff2|eot|ttf|otf)$/',  //字体文件会有很多种类型
	type:'asset/resource'  //载入任何类型的资源
}
```
![字体配置](http://hghqz.vip/webpack/%E5%AD%97%E4%BD%93%E9%85%8D%E7%BD%AE.jpg "字体配置")

之后向文本一样去加载一个文本代码  <span class='icon'>&#xe668;</span>(操作dom时必须是innerhtml不能使innertext)

**加载数据**
之前图片  cion图标  文本文件等等都数据
还有类似于json，csv，tsv，xml等，json和nodejs类似webpack可以直接支持但是像csv，tsv和xml我都没需要使用loader才能处理他们
npm install csv-loader xml-loader -D
```javascript
{
	text:'/\.(tsv|csv)$/', //会转换成 数组 
	use:'csv-loader'
}
{
	text:'/\.xml$/',  //会转换成 js对象
	use:'xml-loader'
}
```

**自定义JSON模块parser**
通过使用自定义parser替代指定的webpack loader，可以将任何toml，yaml，或json5文件作为JSON模块导入。
npm install toml yaml json5 -D

```
const toml = require('toml')
const yaml = require('yaml')
const json5 = require('json5')
{
	text:'/\.toml$/',  //会转换成 js对象
	type:'json',
	parser:{
		parse:toml.parse
	}
}
{
	text:'/\.yaml$/',  //会转换成 js对象
	type:'json',
	parser:{
		parse:yaml.parse
	}
}
{
	text:'/\.json5$/',  //会转换成 js对象
	type:'json',
	parser:{
		parse:json5.parse
	}
}
```

## 七.如何使用babel-loader
webpack默认转换  是源代码原封不动进行转换的  那么如果浏览器不兼容呢？
使用babel-loader将es6 转换成低版本浏览器能够识别的js代码
npm install babel-loader @babal/core @babel/preset-env -D
babel-loader: 解析es6的桥梁
@babal/core babel： 核心模块
@babel/preset-env： babel预设，一组Babel插件的集合
```javascript
{
	test:'/\.js$/',
	exclude:'/node_modules/',//业务里既可以加载本地js也可以加载全局里的node modules里面的js  对于全局的node modules里的js我们是不需要进行babel-loader的编译的  所以需要排除 node modules 里的代码
	use:{
		loader:'babel-loader',
			options:{
				presets:['@babel/preset-env'],
				plugins:[
					['@babel/plugin-transform-runtime']
				]
			}
	}
}
```

regeneratorRuntime是webpack打包生成的全局辅助函数，有babel生成，用于兼容async/await的语法
npm install @babel/runtime -D
插件： npm install @babel/plugin-transform-runtime -D
@babel/runtime： 包含了regeneratorRuntime运行时需要的内容
@babel/plugin-transform-runtime： 会在需要regeneratorRuntime的地方自动require打包然后编译的时候就会需要它
```
plugins:[
					['@babel/plugin-transform-runtime']
				]
```

## 八.代码分离
能够把代码分离到不同的bundle中
bundle就是我们打包分离出来的文件然后我们把这些文件按需加载或者是并行加载
代码分离可以用于获取更小的bundle以及控制资源加载的优先级，如果使用合理会极大的影响加载事件
常用的代码分离：1.配置入口节点，使用entry来手动配置分离代码（如果是多个入口那么这些多个入口共享的文件会分别在每个包里面去重复打包）
2.防止重的分离方法：在entry（入口）的地方通过entry dependencies或者 SplitChunksPlagin去重和分离代码
3.动态导入 通过模块的内联函数import来调用这个函数分离代码

**第一种**
```javascript
entry:{
	index:'./src/index',
	another:'./src/another-module.js'
}
output:{
	filename:'[name]'.bundle.js,   //方式多个入口打包出口文件冲突，使用['name']可以拿到我们入口里面的key的名字
}


import _ from 'lodash'
console.log（_.join(['Vue','React!'],'-')）
```
**第二种 防止重复**
```
// 1.entry dependencies
entry:{
	index:{
		import :'./src/index',
		dependOn:'shared',  //可以把一些共享的文件给定义出来
	},
	another:{
		import :'./src/another-module.js',
		dependOn:'shared',  //可以把一些共享的文件给定义出来
	}
	shared:'lodash' //当我们这两个模块里边 有lodash这个模块的时候  他就会把他抽离出来  并且把他取名为叫shared这样的一个chunk。
}
//2.SplitChunksPlagin插件
entry:{
	index:'./src/index',
	another:'./src/another-module.js'
}
optimization:{
	splitChunks:{
		chunks:'all'  //实现代码分割并把公共的代码 抽离到一个单独的文件里
	}
}

```

**第三种**
使用  ECMAScript提案的import()函数

```
function getComponent(){
	return import('lodash')   // 返回的是一个Promise对象
	.then(({default:_})=>{    // .then的回调函数拿到的是 载入的lodash的一个引用
		const element = document.createElement('div')
		element.innerHTML = _.join(['Vue','React!'],'-')
		return element
	})
}
 // 这样写的目的是为了让 import 来去帮助我们去抽离一个单独的lodash文件
getComponent().then((element)=>{
	document.body.appendChild(element)
})

// 静态导入的内容和动态导入的内容一起来 去做代码抽离
optimization:{
	splitChunks:{
		chunks:'all'  //实现代码分割并把公共的代码 抽离到一个单独的文件里
	}
}

```
**为什么使用动态导入，静态导入不就行了吗？动态导入的应用**
1.可以实现懒加载： 按需加载，是一种很好的优化页面或应用的方式。这种方式实际上是先把你的代码在一些逻辑断点处分离开，然后在一些代码块中完成某些操作后，立即引用或即将引用另外一些新的代码块。这样加快了应用的初始加载速度，减轻了它的总体体积，因为某些代码块可能永远不会加载。
![import懒加载](http://hghqz.vip/webpack/import%E6%87%92%E5%8A%A0%E8%BD%BD.jpg "import懒加载")
```
	//懒加载
	import(/* webpackChunkName: '打包后的文件名'*/'文件地址').then(回调)//通过注释 控制懒加载打包后的文件名  +bundle.js
```

2.可以实现预获取/预加载模块
webpack v4.6.0+ 增加了对预获取和预加载的支持。
在声明import时，使用下面这些内置指令，可以让webpack输出"resource hint(资源提示)",来告诉浏览器：
prefetch(预获取)：将来某些导航下可能需要的资源
preload(预加载)：当前导航下可能需要资源

```
	// 预获取   意义首页面的内容都加载完毕在网络空闲的时候再去加载我们打包好的 文件     比懒加载还要优秀
	import(/* webpackChunkName: '打包后的文件名', webpackPrefetch:true*/'文件地址')
	// 预加载   和懒加载类似   实现页面模块的并行加载
	import(/* webpackChunkName: '打包后的文件名', webpackPreload:true*/'文件地址')
	
```

## 九.缓存
以前我们使用webpack来打包我们模块化以后的应用程序，webpack会生成一个可以部署的dist目录。然后我们把打包好的内容放置到这个目录里将来我们把这个目录的内容部署到server上也就是服务器上，浏览器就可以访问我们这个服务器上的网站和资源了，而最后一步获取资源是比较耗时间的，这就是为什么浏览器会使用缓存的技术，可以通过命中缓存以降低网络流量，使网站加载速度更快，然而我们在部署新版本的时候不更改资源的文件名，浏览器可能会认为你没有更新，那就会使用它的缓存版本，由于缓存的存在，我们需要获取新的代码的时候，就会显的很棘手，
现在我们通过webpack配置来确保编译生成的文件能够被客户端缓存而在文件优化的时候又能够请求新的文件。
问题：在dist文件里看到打包好以后的文件，浏览器有缓存特性会把文件缓存下来，但是如果我们修改了这个文件里的内容，而文件名没有变，浏览器会认为我们没有修改这个文件，所以我们必须在打包的时候把这个文件的名字也一同给重新打包
```javascript
output:{
	filename:'[name].[contenthash].js'  //文件名 会跟随这我们文件内容的变化而变化，这样的话就不怕浏览器缓存了。
}
```
**缓存第三方库代码，如：lodash需不需要缓存**
缓存自己的业务逻辑代码，第三方的代码比如lodash需不需要缓存。
如果我们把lodash单独提取到一个vendor chunk里边，是比较推荐的方法，这是因为他们很少像本地源代码那样频繁修改。因此通过以上的步骤，我们利用client或浏览器的长效缓存的机制，命中的缓存来消除请求。从而减少向server获取资源的次数同时还能保证client代码和server代码版本的一致。
把第三方库的代码像lodash单独打包缓存到浏览器里，这样只有我们自己代码发生变化的时候我们可以去更新但是第三方的代码始终可以是浏览器缓存的内容，那就需要对第三方的代码做一个缓存。
对文件进行单独打包并缓存到浏览器里，代码（文件内容以及文件名都是不变的）
```javascript
optimization:{
	splitChunks：{
		cacheGroups:{    //定义缓存组    缓存第三方文件（node_modules里的）
			vendor:{
				test: /[\\/]node_modules[\\/]/  //通过文件目录的文件名去识别   这个目录的前面后面可能会有斜线
				name:'vendors',
				chunks:'all'         // 定义那些chunk做处理
			}
		}
	}
}
```

**将所有的js文件，放到一个文件夹中**
```
output:{
	filename:'scripts/[name].[contenthash].js'
}
```
小结：缓存业务代码：当我们的项目部署到服务器上的时候，浏览器加载完我们服务器上的文件会缓存我们打包好的模块。这样如果我们修改了js代码，文件名如果没变，浏览器会使用用户本地缓存的内容，就获取不到新的内容了。因此我们通过修改输出文件的文件名来解决   使用可替换模板字符串方法来定义contenthash   实现只要文件的内容不变哈希的字符串也不变
缓存第三方代码：这些文件不频繁更新  可以提高我们首屏的打开速度 节省我们网络流量

## 十.拆分开发环境和生产环境的配置  让打包变得更灵活
平常我们都是手动的调整mode选项  实现生产环境和开发环境的切换
很多配置在生产环境和开发环境存在不一致的情况如：开发环境没有必要设置缓存，生产环境还需要设置公共路径等

**公共路径**
publicPash 配置选项在各种场景中都非常有用，可以通过它来指定应用程序中所有资源的基础路径

现在所有的资源都是通过相对路径来去引用的。我们可不可以根据cdn的路径或者当前服务器的某个路径 来去修改我们link上面的这个路径的前缀。
```
output:{
	publicPash: 'http://localhost:8080/'    // 指定一个域名    这个域名可以指定为项目的前端域名，或者cdn服务器的域名
}
```

**环境变量**
可以帮助我们清除webpack.config.js这个配置文件里边的开发环境和生产环境之间的差异
在命令行 npx webpack --env production (--env goal=local)    知道用户在开发环境使用还是生产环境使用  配置对应的mode选项
将导出的对象变为函数，函数拿到env   生产环境代码压缩  开发环境代码不压缩
```
mode:env.production?'production':'development'
```
webpack开箱即用的terser插件进行压缩js代码  当我们配置minimizer压缩css代码时  terser功能失效
npm install terser-webpack-plugin -D
```
const TerserPlugin = require('terser-webpack-plugin')
optimization:{
	minimizer:{
		//此处时压缩css的插件
		new TerserPlugin()
	}
}
```

**拆分配置文件**
我们使用环境变量将webpack中的生产环境和开发环境的区别都通过三元运算符判断  很糟糕
我们将配置文件拆分成两个配置  一个生产环境  一个开发环境
只当启动配置文件 npx webpack -c ./config/webpack.config.dev.js

**npm脚本**
每次打包或者启动服务的时候 都需要在命令行里输入一长串的命令
```
package.json文件
{
	"scripts":{
		"start":"npx webpack serve -c ./config/webpack.config.dev.js --env development",
		"build":"npx wenpack -c ./config/webpack.config.prod.js --env production"
	}
}
```

生产环境进行打包  提示我们在打包的时候超过了打包的预期
```
performance:{   // 性能方面配置
	hints:false   //性能提示去掉
}
```

**提取公共配置**
开发环境和生产环境  配置有大量的重复代码   把这些重复代码单独抽到一个配置文件里
npm install webpack-merge -D
```
webpack.config.common.js   //通过深merge进行合并三个文件   插件：webpack-merge
webpack.config.js   //这里合并三个文件
const {merge} = require('webpack-merge')
const commonConfig = require('./webpack.config.common.js')
const developmentConfig = require('./webpack.config.dev.js')
const productionConfig = require('./webpack.config.prod.js')
module.exports = (env)=>{
	switch(true){
		case env.development:
			return merge(commonConfig,developmentConfig)
		case env.production:
			return merge(commonConfig,productionConfig)
		default:
			return new Error('没有找到对应的配置环境')
	}
}
```