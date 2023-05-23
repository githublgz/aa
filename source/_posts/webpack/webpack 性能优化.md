---
title: webpack性能优化
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-10-04 14:09:48
author:
img:
coverImg:
password:
summary:
keywords:
---
# 提升构建性能
澄清：有时候我们会笼统的说webpack的性能提升，实际上webpack的性能提升可以分为两类  第一类是通过webpack来提升我们项目性能  如：网站的首屏到达时间，这个优化的受益者是c端的用户  ，2.提升webpack的构建编译性能，如：提高打包速度，降低打包时间，这个优化的受益者是我们的开发人员。   本章讲的是第二种
把官网上提出的webpack5 提升构建性能的点  列：
**注意：**  webpack 每个版本的优化点都是不一样的
通用环境：这些优化既适用于开发化境也适用于生产环境。 
开发环境：
生产环境：

### **通用环境**

无论你是在 开发环境 还是在 生产环境 下运行构建脚本，以下最佳实践都会有所帮助。

1、更新到最新版本
使用最新的 webpack 版本。我们会经常进行性能优化。webpack 的最新稳定版本是：
将 Node.js 更新到最新版本，也有助于提高性能。除此之外，将你的 package 管理工具（例如 npm 或者 yarn ）更新到最新版本，也有助于提高性能。较新的版本能够建立更高效的模块树以及提高解析速度。

2、loader
将 loader 应用于最少数量的必要模块。而非如下:
```
module.exports = {
 //...
 module: {
  rules: [
  {
    test: /\.js$/,
    loader: 'babel-loader',
  },
 ],
},
};
```
通过使用 include 字段，仅将 loader 应用在实际需要将其转换的模块：
```
const path = require('path');
module.exports = {
 //...
 module: {
  rules: [
  {
    test: /\.js$/,
    include: path.resolve(__dirname, 'src'),
    loader: 'babel-loader',
  },
 ],
},
};
```
3、引导(bootstrap)
每个额外的 loader/plugin 都有其启动时间。尽量少地使用工具。

4、解析
以下步骤可以提高解析速度：
减少 resolve.modules , resolve.extensions , resolve.mainFiles ,resolve.descriptionFiles 中条目数量，因为他们会增加文件系统调用的次数。

如果你不使用 symlinks（例如 npm link 或者 yarn link ），可以设置resolve.symlinks: false 。

如果你使用自定义 resolve plugin 规则，并且没有指定 context 上下文，可以设置 resolve.cacheWithContext: false 。

5、小即是快(smaller = faster)
减少编译结果的整体大小，以提高构建性能。尽量保持 chunk 体积小。使用数量更少/体积更小的 library。

在多页面应用程序中使用 SplitChunksPlugin 。
在多页面应用程序中使用 SplitChunksPlugin ，并开启 async 模式。
移除未引用代码。
只编译你当前正在开发的那些代码。

6、持久化缓存
在 webpack 配置中使用 cache 选项。使用 package.json 中的 "postinstall"清除缓存目录。
将 cache 类型设置为内存或者文件系统。 memory 选项很简单，它告诉 webpack在内存中存储缓存，不允许额外的配置：
webpack.config.js
```
module.exports = {
 //...
 cache: {
  type: 'memory',
},
};
```
7、自定义 plugin/loader
对它们进行概要分析，以免在此处引入性能问题。
8、dll
使用 DllPlugin 为更改不频繁的代码生成单独的编译结果。这可以提高应用程序的编译速度，尽管它增加了构建过程的复杂度。

webpack.all.config.js  配置关于dll相关的内容
```
const path = require('path')
const webpack = require('webpack')
module.exports = {
	mode:'production'   // 在生产环境下面去做事情
	entry:{
		jquery:['jquery'],
	},  //注意：这个entry不是配置本地的包，而是要配置我们在node_modules里面安装的第三方的包
	output:{
		filename:'[name].js'     // [name]  取到的是本身的jquery的chunk的名字
		path:path.resolve(__dirname,'dll'),
		library:'[name]_[hash]'    // 把他导出一个库    给这个包起了一个名字，打他导出一个第三方的包。
	},
	plugins:[
		new webpack.DllPlugin({
			name:'[name]_[hash]',     //名字和上面library取的名字是一样的
			path:path.resolve(__dirname,'dll/manifest.json')      // 把一个manifest的文件给生成出来
		})
	]
}

package.json
{
	"scripts":{
		"all":"webpack --config ./webpack.dll.config.js"
	}
}

webpack.config.js
const webpack = require('webpack')
const path = require('path')
plugins:[
	new webpack.DllReferencePlugin({
		manifest:path.resolve(__dirname,'./all/manifest.json')     // 它的值就是刚刚生成的manifest
	})    //和刚才的DllPlugin做了一个呼应
]
```
仅仅是提高了构建速度  如果想把jquery放到页面上显示的话还是有些问题的。   还需要对dll文件进行一次打包   可以使用插件来完成
npm install add-asset-html-webpack-plugin -D
webpack.config.js
```
const AddAssetHtmlPlugin = require('add-asset-html-webpack-plugin')
plugins:[
	new AddAssetHtmlPlugin({
		filepath:path.resolve(__dirname,'./dll/jquery.js'),
		publicPath:'./'
	})
]
```
编译大小有右边回来之前的大小，但是jquery会单独做成一个文件打出来，那我们可以在最后的时候在做一次打包，之前打包的时候可以使用dll这个所谓的link，实时上他还是通过link去加载的我们的jquery.js文件


9、worker 池(worker pool)
thread-loader 可以将非常消耗资源的 loader 分流给一个 worker pool。

该怎么去测试worker pool  ，定义：它的实现原理就是我们可以把他定义在其他的loader前面然后把其他的loader放在另外一个worker pool的池里面去运行来提高我们的打包速度，其实就是把他放到另外一个cpu去运行了，来利用我们电脑的cpu。
npm install thread-loader -D
```
module:{
	rules:[
		{
			test:/\.js$/,
			exclude:/node_modules/,
			use:[
				{
					loader:'babel-loader'   //问了提高Babel-loader的打包速度可以把他放在 worker pool里运行（可以在单独的cpu里去运行）
				options:{
					presets:['@babel/preset-env']   //这样他就可以解析我们js里面的一些es6的代码了
					}
				},
				{  // thread-loader启动需要大概耗费600ms的时间（有开销），这个loader对耗时非常打的loader有意义。
					loader:'thread-loader',
					options:{
						workers:2    // cpu的数量
					}
				}
			]
		}
	]
}
```

不要使用太多的 worker，因为 Node.js 的 runtime 和 loader 都有启动开销。最小化 worker 和 mainprocess(主进程) 之间的模块传输。进程间通讯(IPC,inter process communication)是非常消耗资源的。

10、Progress plugin
将 ProgressPlugin 从 webpack 中删除，可以缩短构建时间。请注意，
ProgressPlugin 可能不会为快速构建提供太多价值，因此，请权衡利弊再使用。

### **开发环境**

1、代码压缩
Webpack 默认使用的是 TerserWebpackPlugin，默认开启了多进程和缓存，构建时，你的项目中可以看到 terser 的缓存文件 node_modules/.cache/terser-webpack-plugin。
2、抽离公共代码
主要是配置 optimazation.splitChunks
//webpack.config.js

```
module.exports = {
optimization: {
		 runtimeChunk: {
            name: 'manifest'
        }，
        splitChunks: {//分割代码块
            cacheGroups: {
                vendor: {
                    //第三方依赖
                    priority: 1, //设置优先级，首先抽离第三方模块
                    name: 'vendor',
                    test: /node_modules/,
                    chunks: 'initial',
                    minSize: 0,
                    minChunks: 1 //最少引入了1次
                },
                //缓存组
                common: {
                    //公共模块
                    chunks: 'initial',
                    name: 'common',
                    minSize: 100, //大小超过100个字节
                    minChunks: 3 //最少引入了3次
                }
            }
        }
    }
   }
```



以下步骤对于 开发环境 特别有帮助。
1、增量编译
使用 webpack 的 watch mode(监听模式)。而不使用其他工具来 watch 文件和调用webpack 。内置的 watch mode 会记录时间戳并将此信息传递给 compilation 以使缓存失效。

在某些配置环境中，watch mode 会回退到 poll mode(轮询模式)。监听许多文件会导致 CPU 大量负载。在这些情况下，可以使用 watchOptions.poll 来增加轮询的间隔时间。

2、在内存中编译
下面几个工具通过在内存中（而不是写入磁盘）编译和 serve 资源来提高性能：

webpack-dev-server
webpack-hot-middleware
webpack-dev-middleware
3、stats.toJson 加速
webpack 4 默认使用 stats.toJson() 输出大量数据。除非在增量步骤中做必要的统计，否则请避免获取 stats 对象的部分内容。 webpack-dev-server 在 v3.1.3 以后的版本，包含一个重要的性能修复，即最小化每个增量构建步骤中，从 stats 对象获取的数据量。

4、Devtool
需要注意的是不同的 devtool 设置，会导致性能差异。

"eval" 具有最好的性能，但并不能帮助你转译代码。
如果你能接受稍差一些的 map 质量，可以使用 cheap-source-map 变体配置来提高性能
使用 eval-source-map 变体配置进行增量编译。

在大多数情况下，最佳选择是 eval-cheap-module-source-map 。

5、避免在生产环境下才会用到的工具
某些 utility, plugin 和 loader 都只用于生产环境。例如，在开发环境下使用TerserPlugin 来 minify(压缩) 和 mangle(混淆破坏) 代码是没有意义的。通常在开发环境下，应该排除以下这些工具：

TerserPlugin
[fullhash] / [chunkhash] / [contenthash]
AggressiveSplittingPlugin
AggressiveMergingPlugin
ModuleConcatenationPlugin

6、最小化 entry chunk
Webpack 只会在文件系统中输出已经更新的 chunk。某些配置选项（HMR,output.chunkFilename 的 [name] / [chunkhash]/[contenthash] ，[fullhash] ）来说，除了对已经更新的 chunk 无效之外，对于 entry chunk 也不会生效。
确保在生成 entry chunk 时，尽量减少其体积以提高性能。下面的配置为运行时代码创建了一个额外的 chunk，所以它的生成代价较低：
```
module.exports = {
 // ...
 optimization: {
  runtimeChunk: true,
},
};
```
7、避免额外的优化步骤
Webpack 通过执行额外的算法任务，来优化输出结果的体积和加载性能。这些优化适用于小型代码库，但是在大型代码库中却非常耗费性能：
```
module.exports = {
 // ...
 optimization: {
  removeAvailableModules: false,
  removeEmptyChunks: false,
  splitChunks: false,
},
};
```

8、输出结果不携带路径信息
Webpack 会在输出的 bundle 中生成路径信息。然而，在打包数千个模块的项目中，这会导致造成垃圾回收性能压力。在 options.output.pathinfo 设置中关闭：
```
module.exports = {
 // ...
 output: {
  pathinfo: false,
},
};
```
9、Node.js 版本 8.9.10-9.11.1
Node.js v8.9.10 - v9.11.1 中的 ES2015 Map 和 Set 实现，存在 性能回退。Webpack 大量地使用这些数据结构，因此这次回退也会影响编译时间。
之前和之后的 Node.js 版本不受影响。

10、TypeScript loader
你可以为 loader 传入 transpileOnly 选项，以缩短使用 ts-loader 时的构建时间。使用此选项，会关闭类型检查。如果要再次开启类型检查，请使用
ForkTsCheckerWebpackPlugin 。使用此插件会将检查过程移至单独的进程，可以加快 TypeScript 的类型检查和 ESLint 插入的速度。
```
module.exports = {
 // ...
 test: /\.tsx?$/,
 use: [
 {
   loader: 'ts-loader',
   options: {
    transpileOnly: true,
  },
 },
],
};
```
11.预获取，预加载

12.CDN

13.Tree Shaking

14.postCSS

15.gzip压缩

16.InlineChunkHtmlPlugin

17.webpack-bundle-analyzer

### **生产环境**

以下步骤对于 生产环境 特别有帮助。
Source Maps
source map 相当消耗资源。你真的需要它们？