---
title: webpack高级
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-11-28 11:57:49
author:
img:
coverImg:
password:
summary:
keywords:
---
# webpack高级篇
完善配置以及常用并强大的工具
## 一.如何提高开发效率与完善团队开发规范

**source-map**
debug  将打包好的bundle.js里的报错内容和我们源代码index.js文件进行关联
webpack已经内置了source-map的功能   只要我们简单的配置一下就可以开启它了
```
devtool:"inline-source-map"  //通过devtool去复制一个值就可以开启 source-map
```
七种source-map：
![source-map](http://hghqz.vip/webpack/source-map.jpg "source-map")
```
npm init -y
npm install webpack webpack-cli webpack-dev-server html-webpack-plugin -D
```
eval模式：每个module会封装到eval里包裹起来执行，并且会在末尾追加注释 //@sourceURL
默认情况下 没有配置source-map  webpack会帮助我们在开发环境下面去设置一个source-map默认值，eval。所以我们在浏览器上才能精准的锁定我们代码的行数。
```
devtool:false      // 关闭source-map   浏览器锁定的是在打包后bundle.js文件中的位置
devtool:'eval'
devtool:'source-map'    //它可以生成一个SourceMap我文件其他的功能会保持eval的功能
devtool:'hidden-source-map'  //和source-map一样，但不会在bundle末尾追加注释      那么就不能有锁定代码行数了   但是它会生成一个.map文件  连着不关联了
devtool:'inline-source-map'   //生成一个DataUrl形式的SourceMap文件  没有map文件了  指向data64格式
devtool:'eval-source-map'   // 每个module会通过eval()来执行，并且生成一个DataUrl形式的SourceMap
devtool:'cheap-source-map'   // 它会生成一个没有列信息的SourceMaps文件，不包含loader的sourcemap    只保留代码的行数不去记录代码的列数    可以减少生成的map文件的大小
devtool:'cheap-module-source-map' //生成一个没有列信息的SourceMaps文件，同时loader的sourcemap也被简化为只包含对应行的信息。       **推荐使用**
```

**devServer**
在开发环境下我们往往要启动一个web服务，方便我们模拟一个用户从浏览器中访问我们的web服务，读取我们的打包产物，以观测我们的代码在客户端的表现。webpack内置了这样的功能，我们只需要简单的配置就可以开启它了。
npm install webpack-dev-server -D
```
const path = require('path')
//为了在浏览器看到效果配置插件
const HtmlWebpackPlugin = require('html-webpack-plugin')
devServer:{
	static: path.resolve(__dirname,'./dist')  // 指向我们当前服务的物理路径
	compress:true  // 可以设置我们是不是在服务器端进行代码压缩 使他在传输过程中可以减少传输的这个数据的大小浏览器请求头   Content-Encoding:gzip  保证我们从服务器到浏览器传输的过程中这个文件是压缩的。从而提高我们的传输效率。
	port :3000  //  配置端口号
	// 添加响应头 在有些场景需求下，我们需要通过http传输给我们浏览器，为所有响应添加headers，来对资源的请求和响应打入标志，以便做一些安全规范，或者方便发生异常后做请求的链路追踪
	headers:{
		'X-Access-token':'啊师傅撒大附件是辣的回复'
	}
	//开启代理，在我们打包出来的js bundle里有时会含有一些对特定接口的网络请求(ajax/fetch),比如：我们的客户端地址实在http://localhost:3000下，加入我们的接口来自http://localhost:4001/,那么毫无疑问，此时的控制台会报错来提示你跨域。  解决：在开发环境下，我们可以使用devServer自带的proxy功能。
	const http = require('http')
	cosnt app = http.createServer((request,response)=>{
		if (request.url==='/api/hello'){
			response.end('hello mode')
		}
	}) 创建服务
	app.listen(9000，'服务器的名字域名localhost可以省略',()=>{
		console.log('提醒用户  9000端口开放')
	})  //监听app
	node server.js启动服务
	fetch('/api/hello') //返回一个promise
	.then(response=>response.text) // 将返回内容变成一个文本
	.then(reuslt=>{
		console.log('result')
	})
	peoxy解决跨域
	proxy:{  //proxy对象 里边可以写很多的所谓的访问的路径（暗号）
		'/api':'http://localhost:9000'  //当用户请求资源为/api时  把它指向到一个新的服务器上去
	}
	//  https配置  我们在本地访问时将http变为https
	https:true   //由于默认配置使用的时自签名证书  所以有得浏览器会告诉你时不安全的，但我们依然可以继续访问它。当然我们也可以提供自己的证书
	https:{
		cacert:"./server.pem",
		pfx:"./server.pfx",
		key:"./server.key",
		cert:'./server.crt',
		passphrase:"webpack-dev-server",
		requestCert:true
	}
	// http2 自带https的自签名证书  仍然可以通过https来访问我们的项目
	http2:true
	// historyApiFallback  如果我们的应用是个SPA（单页面应用），但路由到/some时（可以直接在地址栏里输入），会发现此时刷新页面后，控制台会报错。  原因：浏览器把这个路由当作了静态资源地址去请求，然后我们并没有打包出/some这样的资源，所以这个访问时404。  解决：可以通过配置来提供页面代替任何404的静态资源。
	historyApiFallback:true
	// 此时重启服务刷新后发现请求变成了index.html。当然，在多数业务场景下，我们需要根据不同的访问路径定制替代的页面，  可以使用rewrites这个配置项
	historyApiFallback:{
		rewrites:[
			{form:/^\/$/,to:'asd.html'},  //正则获取路径
		]
	}
	//开发服务器主机   如果你在开发环境中起了一个devserver服务，并期望你的同事能访问它，
	devServer:{
		host:0.0.0.0
	}//这是如果你的同事和你处于一个局域网的话，就可以通过局域网ip来访问你的服务了
}
plugins:[
	new HtmlWebpackPlugin()
]
```

**模块热替换与热加载**
模块热替换：(HMR - hot module replacement)功能会在应用程序运行过程中，替换 添加或删除 模块，而无需重新加载整个页面
```
devServer:{
	hot:true
}
if (module.hot){
	module.hot.accept('./input.js',()=>{
		console.log(123)
	})
}
//css并不需要做这个开关  是因为css-loader已经帮助我们完成这个工作了
```
热加载（文件更新时，自动刷新我们的服务和页面）新版的webpack-dev-server默认开启热加载的功能，它对应的参数是devServer.liveReload ,默认为true。  如果想要关掉它,liveReload这是为false同时也要关掉hot

**Eslint**
eslint是用来扫描我们所写的代码是否符合规范的工具，往往我们的项目是多人协作开发的，我们期望同一的代码规范，这时候可以让eslint来对我们进行约束。严格意义上来说，eslint配置跟webpack无关，但在工程化环境中，它往往是不可或缺的。
npx exlint --init
会给我们三个选项（说明我们在使用的时候用那种方式）：1.仅仅检查语法错误，2.不但可以检查语法而且还可以发现问题，3.既可以检查语法，发现问题，还可以规范我们代码的格式。
之后是问我们在项目中到底使用那种模块化的开发方式：   es6  commonJS  什么都不用
我们在项目中使用的是那种框架：React  Vue.js  没有使用   第三篇讲解webpack如何与react  vue进行合作
项目中是否使用ts
代码是运行在哪里的   browser浏览器  还是node后端
在项目中到底如何去配置代码规范   1.选择一些流行的代码格式（使用第一个）， 2.实现一问一答的方式来配置，3.导入一个我们自有的文件
配置文件放在什么地方  javascript  yaml   json
生成.eslintrc.json文件
![exlint配置选项](http://hghqz.vip/webpack/exlint%E9%85%8D%E7%BD%AE%E9%80%89%E9%A1%B9.jpg "exlint配置选项")
env：脚本的运行环境
extends  ：检查代码格式的文件
parserOptions ： 指定ecmaVersion的版本  sourceType：module
rules：启用规则以及各自的错误级
globals：可以在执行脚本的期间访问一些额外的全局变量，这些全局变量是不在环境中定义的变量。
npx eslint 文件夹或文件
使用vscode 的eslint插件  就会告诉我们哪里不符合规范 出现了问题
```
{
	"rules":{
		"no-console":0, //关闭no-console提示
	}
}
```
结合webpack来实现eslint
我们期望eslint能够实时提示报错而不必等待执行命令，这个功能可以通过给自己的IDE（代码编辑器）安装对应的eslint插件来实现，然而，不是每个IDE都有插件，如果不想使用插件，又想实现实时提示报错，那么我们可以结合webpack的打包编译功能来实现。
在打包js文件之前，通过eslint-loader以及babel-loader来进行处理
```
devServer:{
	client:{
		overlay:false,   // 浏览器不在有覆盖层eslint错误提示
	}
}
module:{
	rules:[
		{
			test:/\.js$/,
			use:['babel-loader','eslint-loader']
		}
	]
}
```

**git-hooks与husky**
为了保证团队里的开发人员提交的代码符合规范，我们可以在开发者上传代码的时候进行校验。我们常用husky来协助进行代码提交时的eslint校验。在使用husky前，我们先来研究一下git-hooks.
husky时基于git-hooks git的钩子来实现的

git --version
git init
ls -la
.gitignore  在git提交时有一些文件不需要提交
**/node_modules
git status  查看当前git的状态   可以看到文件都没有进行本地的仓库的添加的            可以查看到修改了那些文件
git add . 添加到缓存区   git commit -m 'init'  所有代码添加到了本地仓库   并没有关联远端的仓库
cd .git  ls -la   cd hooks  ls -la
.sample的扩展名文件，这些文件都是git的hooks    hook就是我们在执行命令的时候需要提前或者之后去执行的一些命令这些命令时自动执行的   只要配置好   git会帮助我们运行。
需求：在每次git提交的时候来检查代码的问题。   可以运行eslint  钩子可以采用pre-commit-sample
cat pre-commit-sample   浏览文件   如果想要指定它或者时让他生效我们可以修改文件名  .sample是不能起作用的我们想要起作用必须重新创建一个文件pre-commit
touch  pre-commit   这个文件我们就可以事先写一写shell脚本  当commit实行的时候这些脚本就会被提前运行
vim pre=commit       //编辑这个文件   i进行输入  echo pre-commit   esc退出  :wq
修改文件的读写权限  chmod +x ./pre-commit   //添加写权限
脚本  ：  d+d删除当前行  i（insert）插入   npx eslint ./src
在团队开发时不对代码做任何处理    只有在我们代码提交到git仓库时在进行校验
问题：如果把git的配置放在.git文件夹里那个文件夹每个人的配置都是不一样的   也没有办法把配置放到git仓库里   那我们需要把这个配置放置到项目的根目录下
文件以.开头表示这个文件是隐藏的
.mygithooks  .pre-commit   希望我们在git提交的时候读取的不是我们在git默认的hooks里面配置的pre-commit 而是读取我们项目中的pre-commit   通过git配置实现   git config core.hooksPash .mygithooks  会自动读取文件夹中约定好的pre-commit这个文件名
vim .git/config   //查看我们刚才通过命令行添加的命令
chmod +x .mygithooks/pre-commit   ls -la查看文件权限
现在都是通过手工去完成的    我们可以通过现成的工具(husky)来完成
vim .git/config   删除刚才的配置
npm install husky -D
npx husky install //执行  让我们的命令行hook生效   会在当天目录下面创建.husky文件夹
自己配置一个脚本 "prepare" : husky install  它会实现到我们的一些命令执行之前去安装我们的husky
npx husky add .husky/per-commit 在.husky下创建pre-commit文件   npx eslint ./src
添加权限

## 二.模块与依赖
在模块化的编程中  开发者会将程序分解为功能离散的一些文件   我们把这些文件称之为模块  每个模块都很轻量  这使得我们项目的验证  调试以及测试会变得轻而易举。  这些精心编写得模块提供了可靠的抽象和封装界限。使得我们应用程序的每个模块都具备了调理清晰的设计和明确的目的。
nodejs从一开始就支持模块化的编程  但是浏览器端的模块化还在缓慢的支持中。大多数浏览器支持esm模块化。
能在webpack工程化的环境里成功导入的模块都应该视做为webpack模块。与nodejs项目webpack模块能以各种方式来表达它的利害关系。
es6 import from          nodejs  module  require              AMD   define  require
css,scss,less,assets这些文件里的@import语句     和样式里的url资源  这些都是webpack应用的模块
webpack模块解析简易原理：webpack将js,css,less,scss,img,html等文件  通过loader+module(内置模块)  的方法解析成模块化的文件。
这个打包编译的解析过程是怎么完成的：webpack执行会返回一个描述webpack打包编译的整个流程的一个对象我们将这个对象称之为compiler,compiler对象描述的是整个webpack打包的流程，它内置打包状态，随着打包过程的进行  状态也实施的发生变化。同时会触发相应状态的webpack生命周期钩子，我们可以将它类比成一个promise对象，这个状态从打包前，打包中到打包完成或者打包失败都是通过这个过程完成的，每一个webpack打包都是创建一个compiler对象，它会走完整个声明周期的过程。而webpack中所有的模块解析都是compiler对象内置模块的解析器去做的，通过这个对象的属性resolvers解析器主题主题功能就是解析模块它是基于enhanced-resolve这个包来实现的，在webpack中无论使用怎样的模块引用语句本质其实都是调用这个包的api来进行模块的构建解析的

**模块解析(resolve)**
webpack是通过Resolves实现了模块之间的依赖和引用。在打包的时候，webpack使用enhanced-resolve来解析文件路径（webpack_resolver的代码实现很有思想，webpack基于次进行treeshaking。）
webpack可以解析三种文件路径：1.绝对路径（相对于项目的根目录/）    2.相对路径（相对于当前文件./）    3.模块路径（全局的node_modules文件）(不用/  或  ./它会自动到node_modules中找模块)
可以给某个路径或某个文件夹下的目录去起个别名
```
const path = require('path')
resolve:{
	alias:{
		'@': path.resulve(__dirname,'./src')   // @指向src目录
	},
	extensions: ['.json','js','.vue']   //优先请求 .json的后缀。(不配置该选项默认请求.js)
}
```

**外部扩展(Externals)**
有时候我们会为了减小bundle的体积，从而把一些不变的第三方库用cdn的形式引入进来，比如jquery：index.html。
```
externalsType:'script',
externals:{   //他是一个对象   可以去定义我们的外部一些第三方包
	jquery:'jQuery($)'      //key 的名字一定要和  其他地方引用的报名一样，值是我们在window对象上面去暴露的一个对象        // 无法正常运行  jquery没有定义    需要在index.html收到导入script标签
	jquery:[
		'cdn地址',   //jquery  script标签  将来要放到的页面的连接
		'jQuery($)'   //表示  这个script在浏览器上面暴露的一个对象
	]
}
```

**依赖图(dependency graph)**
每当一个文件依赖另一个文件时，webpack会直接将文件视为存在依赖关系。这使得webpack可以获取非代码资源，如image或web字体等，并会把他们作为 依赖 提供给应用程序，当webpack开始工作时，它会根据我们写好的配置，从 入口（entry）开始，webpack会递归的构建一个 依赖关系图，这个依赖关系图包含着应用程序中所需的每个模块，然后将所有模块打包为bundle(也就是output配置项)
单纯将可能很抽象，我们更期望能够可视化打包产物的依赖图，  bundle分析工具：
官网分析工具   第三方工具：webpack-chart   webpack-visualizer    webpack-bundle-analyzer    webpack bundle  optimize helper       bundle-stats
![bundle分析工具](http://hghqz.vip/webpack/bundle%E5%88%86%E6%9E%90%E5%B7%A5%E5%85%B7.jpg "bundle分析工具")
npm install webpack-bundle-analyzer -D
```
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer')
plugins:{
	new BundleAnalyzerPlugin()
}
```

## 三.扩展功能
PostCSS和css模块，Web Works,Typescript
![PostCSS与css模块](http://hghqz.vip/webpack/PostCSs%E4%B8%8Ecss%E6%A8%A1%E5%9D%97.jpg "PostCSS与css模块")

postcss : 1.可以给样式添加前缀兼容浏览器   2.可以在样式里书写一些关于嵌套的功能  它可以编译成浏览器能够识别的样式。
css模块：解决页面class引用次数多，或引用别人的样式 名重复，可以使用css模块

```
npm install postcss-loader -D
npm install autoprefixer -D   //帮助我们去加载一些样式的前缀
webpack.config.js
module:{
	rules:[
		{
			test:/\.css$/,
			use:[
				'style-loader',
				'css-loader',
				'postcss-loader'
			]
		}
	]
}
postcss.config.js
module.exports = {
	plugins:[
		require('autoprefixer'),
		require('postcss-nested')   //嵌套
	]
}
package.json
"browerlist":[
	">1%",   //应用在全球浏览器大于1%的浏览器上
	"last 2 versions"  //每个浏览器的最新的两个版本
]
// css模块实现
rules:[
	{
		test:/\.css$/,
		use:[
			'tyle-loader',
			{
				loader:'css-loader',
				options:{
					modules:true        //开启css模块
				}
			},
			'postcss-loader',
		]
	}
]

//获取hash字符串  将css文件当成一个模块
import style from './asasd.css'
console.log(style)
样式为：style.class名
配置部分开启css模块  css module模式  ，全局样式.global不开启css模块 不同模式。

// css module
{
 test: new RegExp(`^(?!.*\\.global).*\\.css`),
 use: [
 {
   loader: 'style-loader'
 }，
 {
   loader: 'css-loader',
   options: {
    modules: true,
    localIdentName: '[hash:base64:6]'
  }
 },
 {
   loader: 'postcss-loader'
 }
],
 exclude:[path.resolve(__dirname, '..', 'node_modules')]
}
// 普通模式
{
 test: new RegExp(`^(.*\\.global).*\\.css`),
 use: [
 {
   loader: 'style-loader'
 }，
 {
   loader: 'css-loader',
 },
 {
   loader: 'postcss-loader'
 }
],
 exclude:[path.resolve(__dirname, '..', 'node_modules')]
}
```

**Web Works**
有时我们需要在客户端进行大量的运算，但又不想让它阻塞我们的js主线程。你可能
第一时间考虑到的是异步。
但事实上，运算量过大(执行时间过长)的异步也会阻塞js事件循环，甚至会导致浏览
器假死状态。
这时候，HTML5的新特性 WebWorker就派上了用场。
在此之前，我们简单的了解下这个特性。
html5之前，打开一个常规的网页，浏览器会启用几个线程？
一般而言，至少存在三个线程(公用线程不计入在内):
分别是js引擎线程(处理js)、GUI渲染线程(渲染页面)、浏览器事件触发线程(控制交
互)。
当一段JS脚本长时间占用着处理机,就会挂起浏览器的GUI更新，而后面的事件响应也
被排在队列中得不到处理，从而造成了浏览器被锁定进入假死状态。
现在如果遇到了这种情况，我们可以做的不仅仅是优化代码————html5提供了解
决方案，webworker。
webWorkers提供了js的后台处理线程的API，它允许将复杂耗时的单纯js逻辑处理放
在浏览器后台线程中进行处理，让js线程不阻塞UI线程的渲染。
多个线程间也是可以通过相同的方法进行数据传递。
它的使用方式如下：
也就是说，需要单独写一个js脚本，然后使用new Worker来创建一个Work线程实
例。
这意味着并不是将这个脚本当做一个模块引入进来，而是单独开一个线程去执行这个
脚本。
```
   loader: 'css-loader',
 },
 {
   loader: 'postcss-loader'
 }
],
 exclude:[path.resolve(__dirname, '..', 'node_modules')]
}
```
//new Worker(scriptURL: string | URL, options?: WorkerOptions)
new Worker("someWorker.js");

我们知道，常规模式下，我们的webpack工程化环境只会打包出一个bundle.js，那
我们的worker脚本怎么办？
也许你会想到设置多入口(Entry)多出口(ouotput)的方式。
事实上不需要那么麻烦，webpack4的时候就提供了worker-loader专门配置
webWorker。
令人开心的是，webpack5之后就不需要用loader啦，因为webpack5内置了这个功
能。
我们来试验一下：
第一步
创建一个work脚本 work.js,我们甚至不需要写任何内容，我们的重点不是
webWorker的使用，而是在webpack环境中使用这个特性。
当然，也可以写点什么，比如：
```
self.onmessage = ({ data: { question } }) => {
 self.postMessage({
  answer: 42,
})
}
```
在 index.js 中使用它
```
// 下面的代码属于业务逻辑
const worker = new Worker(new URL('./work.js', import.meta.url));
worker.postMessage({
 question:
  'hi，那边的workder线程，请告诉我今天的幸运数字是多少？',
});
worker.onmessage = ({ data: { answer } }) => {
 console.log(answer);
};
千
```
(import.meta.url这个参数能够锁定我们当前的这个模块——注意，它不能在
commonjs中使用。)
这时候我们执行打包命令，会发现,dist目录下除了bundle.js之外，还有另外一个
xxx.bundle.js!
这说明我们的webpack5自动的将被new Work使用的脚本单独打出了一个bundle。
我们加上刚才的问答代码，执行npm run dev，发现它是能够正常工作。
并且在network里也可以发现多了一个src_worker_js.bundle.js。
总结：
webpack5以来内置了很多功能，让我们不需要过多的配置，比如之前讲过的hot模
式，和现在的web workder。

**Typescript**

在前端生态里，TS扮演着越来越重要的角色。
我们直入正题，讲下如何在webpack工程化环境中集成TS。
首先，当然是安装我们的ts和对应的loader。
npm install --save-dev typescript ts-loader

接下来我们需要在项目根目录下添加一个ts的配置文件————tsconfig.json，我们
可以用ts自带的工具来自动化生成它。
npx tsc --init
我们发现生成了一个tsconfig.json，里面注释掉了绝大多数配置。
现在，根据我们想要的效果来打开对应的配置。
```
{
 "compilerOptions": {
  "outDir": "./dist/",
  "noImplicitAny": true,
  "sourceMap": true,
  "module": "es6",
  "target": "es5",
  "jsx": "react",
  "allowJs": true,
  "moduleResolution": "node" 
}
}
 const path = require('path');
 module.exports = {
 entry: './src/index.ts',
 devtool: 'inline-source-map',
千
```
好了，接下来我们新增一个src/index.ts，内置一些内容。
然后我们别忘了更改我们的entry及配置对应的loder。
当然，还有resolve.extensions，将.ts放在.js之前，这样它会先找.ts。
注意，如果我们使用了sourceMap，一定记得和上面的ts配置一样，设置
sourcemap为true。
也别忘记在我们的webpack.config.js里，添加sourcemap,就像我们之前课程里讲的
那样。
更改如下：

```
const path = require('path');
 module.exports = {
 entry: './src/index.ts',
 devtool: 'inline-source-map',

  module: {
   rules: [
   {
     test: /\.(ts|tsx)$/,
     use: 'ts-loader',
     exclude: /node_modules/,
   },
  ],
 },
  resolve: {
   extensions: [ '.tsx', '.ts', '.js' ],
 },
  output: {
   filename: 'bundle.js',
   path: path.resolve(__dirname, 'dist'),
 },
};
```

运行我们的项目，我们发现完全没有问题呢！
使用第三方类库
在从 npm 上安装第三方库时，一定要记得同时安装这个库的类型声明文件(typing
definition)。
我们可以从 TypeSearch中找到并安装这些第三方库的类型声明文件(https://www.ty
pescriptlang.org/dt/search?search=) 。
举个例子，如果想安装 lodash 类型声明文件，我们可以运行下面的命令：
npm install --save-dev @types/lodash

eslint & ts
注意，如果要使用eslint，使用初始化命令的时候，记得选择“使用了typesctipt”。
如果已经配置了eslint，但没有配置ts相关的配置，那么我们需要先安装对应的
plugin
注意如果需要用到react的话，记得也要安装

yarn add -D  @typescript-eslint/eslint-plugin@latest

vue或者其他常用框架同样如此，一般都会有专门的plugin。
然后我们队.esilntrc进行更改~
```
{
  "env": {
    "browser": true,
    "es2021": true
 },
  "extends": [
    "eslint:recommended", // 如果需要react的话
    "plugin:react/recommended",
    "plugin:@typescript-eslint/recommended"
 ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
   }, // 如果需要react的话
    "ecmaVersion": 13,
    "sourceType": "module"
 },
  "plugins": [
    "react",
    "@typescript-eslint"
 ],
  "rules": {
   // ...一些自定义的rules
    "no-console": "error"
 }
};
```
执行npm run eslint试一下！


## 四.多页面应用

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

## 五.Tree Shaking
tree shaking 是一个术语，通常用于描述移除 JavaScript 上下文中的未引用代码(dead-code)。它依赖于 ES2015 模块语法的 静态结构 特性，例如 import 和export 。这个术语和概念实际上是由 ES2015 模块打包工具 rollup 普及起来的。webpack 2 正式版本内置支持 ES2015 模块（也叫做 harmony modules）和未使用模块检测能力。新的 webpack4 正式版本扩展了此检测能力，通过 package.json的 "sideEffects" 属性作为标记，向 compiler 提供提示，表明项目中的哪些文件是 "pure(纯正 ES2015 模块)"，由此可以安全地删除文件中未使用的部分

```
optimization: {
 usedExports: true,
},
```
webpack5打包更加精简化           只打包用到的方法或属性      tree shaking  优化到了极质，把一些没有用的代码全部摇掉只是给我们用到了的数据
理论：只要是我认为这个代码在项目中没有使用  那么就给摇掉。它基于es  module

**sideEffects**
注意 Webpack 不能百分百安全地进行 tree-shaking。有些模块导入，只要被引入，就会对应用程序产生重要的影响。一个很好的例子就是全局样式表，或者设置全局配置的JavaScript 文件。

Webpack 认为这样的文件有“副作用”。具有副作用的文件不应该做 tree-shaking，因为这将破坏整个应用程序。
Webpack 的设计者清楚地认识到不知道哪些文件有副作用的情况下打包代码的风险，因此webpack4默认地将所有代码视为有副作用。这可以保护你免于删除必要的文件，但这意味着 Webpack 的默认行为实际上是不进行 tree-shaking。值得注意的是webpack5默认会进行 tree-shaking。
如何告诉 Webpack 你的代码无副作用，可以通过 package.json 有一个特殊的属性
sideEffects，就是为此而存在的。
它有三个可能的值：
true
如果不指定其他值的话。这意味着所有的文件都有副作用，也就是没有一个文件
可以 tree-shaking。
false
告诉 Webpack 没有文件有副作用，所有文件都可以 tree-shaking。
数组[…]
```
package.json
'sideEffects':true/false,
'sideEffects':['*.css','*.global.js'],
```
是文件路径数组。它告诉 webpack，除了数组中包含的文件外，你的任何文件
都没有副作用。因此，除了指定的文件之外，其他文件都可以安全地进行 tree-
shaking。
webpack4 曾经不进行对 CommonJs 导出和 require() 调用时的导出使用分析。
webpack 5 增加了对一些 CommonJs 构造的支持，允许消除未使用的 CommonJs
导出，并从 require() 调用中跟踪引用的导出名称。

## 六.渐进式网络应用程序PWA
渐进式网络应用程序(progressive web application - PWA)，是一种可以提供类似于native app(原生应用程序) 体验的 web app(网络应用程序)，就是说我们在浏览器端能够实现类似于原生应用程序的体验。PWA 可以用来做很多事。其中最重要的是，在离线(offline)时应用程序能够继续运行功能。这是通过使用名为 Service Workers 的 web 技术来实现的。

**非离线环境下运行**

到目前为止，我们一直是直接查看本地文件系统的输出结果。通常情况下，真正的用户是通过网络访问 web app；用户的浏览器会与一个提供所需资源（例如， .html ,.js 和 .css 文件）的 server 通讯。
我们通过搭建一个拥有更多基础特性的 server 来测试下这种离线体验。这里使用http-server package： npm install http-server --save-dev 。还要修改package.json 的 scripts 部分，来添加一个 start script：

不使用webpack-dev-server，使用第三方server，相当于我们把dist已经打包出来准备发布了
npm install http-server -D

```
{
 ...
 "scripts": {
  "start": "http-server dist"
},
 ...
}
//注意：webpack-dev-server 是一个非离线 在线的server。 默认情况下，webpack DevServer 会写入到内存（当我们修改代码的时候重新启动服务它并不能够把我们的文件打包到dist下面，因为他是直接放到内存里的。）。我们需要启用devserverdevmiddleware.writeToDisk 配置项，来让 http-server 处理 ./dist 目录中的文件。
```
```
devServer: {
 devMiddleware: {    //开发环境的server的中间件
  writeToDisk:true,    // 写入到硬盘里
  index: true,
  writeToDisk: true,
},
}
```
如果你打开浏览器访问 http://localhost:8080 (即 http://127.0.0.1 )，你应该会看到 webpack 应用程序被 serve 到 dist 目录。如果停止 server 然后刷新，则 webpack 应用程序不再可访问。这就是我们为实现离线体验所需要的改变。在本章结束时，我们应该要实现的是，停止 server 然后刷新，仍然可以看到应用程序正常运行。

**添加 Workbox**
添加 workbox-webpack-plugin 插件，然后调整 webpack.config.js 文件：    实现PWA
npm install workbox-webpack-plugin --save-dev

```
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const WorkboxPlugin = require('workbox-webpack-plugin');

module.exports = {
 entry: {
  app: './src/index.js',
},

 plugins: [
  new HtmlWebpackPlugin(),
  new WorkboxPlugin.GenerateSW({
   // 这些选项帮助快速启用 ServiceWorkers
   // 不允许遗留任何“旧的” ServiceWorkers
   clientsClaim: true,  // 快速的启用ServiceWorkers
   skipWaiting: true,     // 跳出等待
 }),
],

 output: {
  filename: '[name].bundle.js',
  path: path.resolve(__dirname, 'dist'),
  clean: true,
},
}
}
```
现在你可以看到，生成了两个额外的文件： service-worker.js 和名称冗长的workbox-718aa5be.js 。 service-worker.js 是 Service Worker 文件， workbox-718aa5be.js 是 service-worker.js 引用的文件，所以它也可以运行。你本地生成的文件可能会有所不同；但是应该会有一个 service-worker.js 文件。所以，值得高兴的是，我们现在已经创建出一个 Service Worker。接下来该做什么？

**注册 Service Worker  实现离线浏览页面的功能**
接下来我们注册 Service Worker，使其出场并开始表演。通过添加以下注册代码来
完成此操作：
index.js

```
if ('serviceWorker' in navigator) {    //浏览器是否支持serviceWorker
 window.addEventListener('load', () => {   //绑定事件load在页面加载完之后执行
  navigator.serviceWorker.register('/service-     // 方法传入刚刚打包好的service-worker.js。
worker.js').then(registration => {                  // 拿到注册以后的成功的结果
   console.log('SW 注册成功: ', registration);
 }).catch(registrationError => {
   console.log('SW 注册失败: ', registrationError);
 });
});
}
```
相当于在浏览器里我们的页面被缓存下来了
chrome://serviceworker-internals    清除http-server缓存。   服务就跑不起来了。

再次运行 npx webpack 来构建包含注册代码版本的应用程序。然后用 npm start启动服务。访http://localhost:8080并查看 console 控制台。在那里你应该看到：
SW registered
现在来进行测试。停止 server 并刷新页面。如果浏览器能够支持 Service Worker，应该可以看到你的应用程序还在正常运行。然而，server 已经停止 serve 整个 dist文件夹，此刻是 Service Worker 在进行 serve。

## 七.Shimming预置依赖
不导入直接就可以使用一些变量

webpack compiler 能够识别遵循 ES2015 模块语法、CommonJS 或 AMD 规范编写的模块。然而，一些 third party(第三方库) 可能会引用一些全局依赖（例如 jQuery中的 $ ）。因此这些 library 也可能会创建一些需要导出的全局变量这些 "brokenmodules(不符合规范的模块)" 就是 shimming(预置依赖) 发挥作用的地方。
shim 另外一个极其有用的使用场景就是：当你希望 polyfill 扩展浏览器能力，来支持到更多用户时。在这种情况下，你可能只是想要将这些 polyfills 提供给需要修补(patch)的浏览器（也就是实现按需加载）。

**Shimming 预置全局变量**

让我们开始第一个 shimming 全局变量的用例。还记得我们之前用过的 lodash吗？出于演示目的，例如把这个应用程序中的模块依赖，改为一个全局变量依赖。要实现这些，我们需要使用 ProvidePlugin 插件。使用 ProvidePlugin 后，能够在 webpack 编译的每个模块中，通过访问一个变量来获取一个 package。如果 webpack 看到模块中用到这个变量，它将在最终bundle 中引入给定的 package。让我们先移除 lodash 的 import 语句，改为通过插件提供它：
src/index.js

```
console.log(_.join(['hello', 'webpack'], ' '))
webpack.config.js

const webpack = require('webpack')
module.exports = {
 mode: 'development',
 entry: './src/index.js',
 plugins: [
  new webpack.ProvidePlugin({
   _: 'lodash'     // lodash变成全局  遇到_将lodash包引进来
 })
]
}
```
我们本质上所做的，就是告诉 webpack……如果你遇到了至少一处用到 _ 变量的模块实例，那请你将 lodash package 引入进来，并将其提供给需要用到它的模块。运行我们的构建脚本，将会看到同样的输出：

```
[felix] 01-third-party-shimming $ npx webpack
asset main.js 549 KiB [emitted] (name: main)
runtime modules 344 bytes 2 modules
cacheable modules 528 KiB
./src/index.js 46 bytes [built] [code generated]
../../../../../node_modules/lodash/lodash.js 528 KiB [built]
[code generated]
webpack 5.61.0 compiled successfully in 275 ms
```
还可以使用 ProvidePlugin 暴露出某个模块中单个导出，通过配置一个“数组路径”（例如 [module, child, ...children?] ）实现此功能。所以，我们假想如下，无论 join 方法在何处调用，我们都只会获取到 lodash 中提供的 join 方法。
```
src/index.js
console.log(join(['hello', 'webpack'], ' '))
webpack.config.js
const webpack = require('webpack')
module.exports = {
 mode: 'development',
 entry: './src/index.js',
 plugins: [
  new webpack.ProvidePlugin({
   // _: 'lodash'
   join: ['lodash', 'join'],
 })
]
}
```
这样就能很好的与 tree shaking 配合，将 lodash library 中的其余没有用到的导出
去除

**细粒度 Shimming**
一些遗留模块依赖的 this 指向的是 window 对象。在接下来的用例中，调整我们的 index.js ：
npm istall imports-loader -D
```
this.alert('hello webpack')
```
当模块运行在 CommonJS 上下文中，这将会变成一个问题，也就是说此时的 this指向的是 module.exports 。在这种情况下，你可以通过使用 imports-loader 覆盖 this 指向：
```
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
 mode: 'production',
 entry: './src/index.js',
 module: {
  rules: [
  {
    test: require.resolve('./src/index.js'),     // 调用require.resolve去调用  文件。
    use: 'imports-loader?wrapper=window',     //webpack解析到  这个文件时使用的loader  wrapper=window  指明包里面的this指向浏览器的window。
  },
 ]
},
 plugins: [
  new webpack.ProvidePlugin({
   _: 'lodash'
 }),
  new HtmlWebpackPlugin()
]
}
```

**全局 Exports**

让我们假设，某个 library 创建出一个全局变量，它期望 consumer(使用者) 使用这个变量。为此，我们可以在项目配置中，添加一个小模块来演示说明：

```
src/globals.js
const file = 'example.txt';
const helpers = {
 test: function () {
  console.log('test something')
},
 parse: function () {
  console.log('parse something')
},
}
npm install exports-loader -D

webpack.config.js
const webpack = require('webpack')
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
 mode: 'production',
 entry: './src/index.js',
 module: {
  rules: [
  {
    test: require.resolve('./src/index.js'),
    use: 'imports-loader?wrapper=window',
  },
  {
    test: require.resolve('./src/globals.js'),
    use: 'exports-loader?type=commonjs&exports=file,multiple|helpers.parse（value）|parse（key）',   //  type：类型为模块导出的类型commonjs    导出的变量file  js文件中的File    ，multiple表示导出一个key|value的形式。
  },
 ]
},
 plugins: [
  new webpack.ProvidePlugin({
   _: 'lodash'
 }),
  new HtmlWebpackPlugin()
]
}

const {file,parse} = require('./globals')   //  那我直接导入不就行了，前提条件globals.js是外部的一个文件，一般情况下我们并不知道它是如何导出的   所有我们在配置文件里边做一个  一些内容的导出   这样我们就可以单独的去使用我们想要的一些模块了
```

此时，在我们的 entry 入口文件中（即 src/index.js ），可以使用 const {file, parse } = require('./globals.js'); ，可以保证一切将顺利运行。



**加载 Polyfills**
目前为止，我们讨论的所有内容 都是处理那些遗留的 package，让我们进入到第二个话题：polyfill。
有很多方法来加载 polyfill。例如，想要引入 @babel/polyfill 我们只需如下操作：
npm install --save @babel/polyfill
然后，使用 import 将其引入到我们的主 bundle 文件：
```
index.js
import '@babel/polyfill'    //polyfill俗称垫片   在执行下面的语句之前，把所有的可能需要降级的代码都放在了我们代码的前面
console.log(Array.from([1, 2, 3], x => x + x))    //from方法不是所有的浏览器都支持的   如果是我们不告诉webpack那么他就纯粹八Array.from直接的打到我们的页面上去了，即使是我们可能会使用一些@babal/loader,但是他也不会  100%的将我们的代码全部转换为低版本的浏览器能够识别的所有代码。所以我们得告诉浏览器 在某些版本里面得把他转成低版本的代码比如  es6=>es3.1  这是我们就需要一个垫片来完成。
```
注意，这种方式优先考虑正确性，而不考虑 bundle 体积大小。为了安全和可靠，polyfill/shim 必须运行于所有其他代码之前，而且需要同步加载，或者说，需要在所有 polyfill/shim 加载之后，再去加载所有应用程序代码。 社区中存在许多误解，即现代浏览器“不需要”polyfill，或者 polyfill/shim 仅用于添加缺失功能 - 实际上，它们通常用于修复损坏实现(repair broken implementation)，即使是在最现代的浏览器中，也会出现这种情况。 因此，最佳实践仍然是，不加选择地和同步地加载所有polyfill/shim，尽管这会导致额外的 bundle 体积成本。

**进一步优化 Polyfills**
不建议使用 import @babel/polyfilll 。因为这样做的缺点是会全局引入整个polyfill包，比如 Array.from 会全局引入，不但包的体积大，而且还会污染全局环境。
babel-preset-env package 通过 browserslist 来指定来对那些浏览器那些浏览器的版本进行转换，来转译那些你浏览器中不支持的特性。这个 preset 使用 useBuiltIns 选项，默认值是 false ，这种方式可以将全局babel-polyfill 导入，改进为更细粒度的 import 格式：
```
import 'core-js/modules/es7.string.pad-start';
import 'core-js/modules/es7.string.pad-end';
import 'core-js/modules/web.timers';
import 'core-js/modules/web.immediate';
import 'core-js/modules/web.dom.iterable';
```
//安装 @babel/preset-env 及 相关的包
npm i babel-loader @babel/core @babel/preset-env -D
// webpack.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
 mode: 'production',
 entry: './src/index.js',
 plugins: [
  new HtmlWebpackPlugin()
],
 module: {
  rules: [
  {
    test: /\.js$/,
    exclude: /node_modules/,
    use: {
     loader: 'babel-loader',
     options: {
      presets: [
      [
        '@babel/preset-env',
       {
         targets: [  //也可以顶知道package.json中  符合这个条件的代码都需要通过Polyfill来进行垫片的添加
          "last 1 version",
          "> 1%",
         ],
         useBuiltIns: 'usage' ，    //  这里这个@babel/preset-env  后我们就不需要手动的导入  import '@babel/polyfill'了。        使用这个选项需要 Core.js翻译器，这是优雅降级的一些库
		 //添加corejs配置
           corejs: 3,     //定义core.js的版本魏三
       }
      ]
     ]
    }
   }
  }
 ]
}
}
useBuiltIns: 参数有 “entry”、”usage”、false 三个值默认值是 false ，此参数决定了babel打包时如何处理@babel/polyfilll 语句。
“entry”: 会将文件中 import @babel/polyfilll 语句 结合 targets ，转换为一系列引入语句，去掉目标浏览器已支持的 polyfilll 模块，不管代码里有没有用到，只要目标浏览器不支持都会引入对应的 polyfilll 模块。
“usage”: 不需要手动在代码里写 import @babel/polyfilll ，打包时会自动根据实际代码的使用情况，结合 targets 引入代码里实际用到部分 polyfilll 模块
false: 对 import‘@babel/polyfilll’不作任何处理，也不会自动引入 polyfilll 模块。需要注意的是在 webpack 打包文件配置的 entry 中引入的 @babel/polyfill 不会根据useBuiltIns 配置任何转换处理。

由于@babel/polyfill在7.4.0中被弃用，我们建议直接添加corejs并通过corejs选项设置版本。
执行编译 npx webpack
```
[felix] 02-polyfill $ npx webpack
WARNING (@babel/preset-env): We noticed you're using the
`useBuiltIns` option without declaring a core-js version.
Currently, we assume version 2.x when no version is passed. Since
this default version will likely change in future versions of
Babel, we recommend explicitly setting the core-js version you
are using via the `corejs` option.
You should also be sure that the version you pass to the `corejs`
option matches the version specified in your `package.json`'s
`dependencies` section. If it doesn't, you need to run one of the
following commands:
npm install --save core-js@2   npm install --save core-js@3
yarn add core-js@2       yarn add core-js@3
More info about useBuiltIns: https://babeljs.io/docs/en/babel-
preset-env#usebuiltins
More info about core-js: https://babeljs.io/docs/en/babel-preset-
env#corejs
asset main.js 16.7 KiB [emitted] [minimized] (name: main)
asset index.html 214 bytes [compared for emit]
runtime modules 663 bytes 3 modules
modules by path ./node_modules/core-js/modules/*.js 38.9 KiB 68
modules
./src/index.js 374 bytes [built] [code generated]
webpack 5.61.0 compiled successfully in 1613 ms
```
提示我们需要安装 core-js 。
npm i core-js@3 -S
此时还需要 添加一个配置：
// 添加corejs配置
corejs: 3,
成功优化

## 八.创建 library
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

## 九.模块联邦(Module Federation)
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
## 十. 提升构建性能
澄清：有时候我们会笼统的说webpack的性能提升，实际上webpack的性能提升可以分为两类  第一类是通过webpack来提升我们项目性能  如：网站的首屏到达时间，这个优化的受益者是c端的用户  ，2.提升webpack的构建编译性能，如：提高打包速度，降低打包时间，这个优化的受益者是我们的开发人员。   本章讲的是第二种
把官网上提出的webpack5 提升构建性能的点  列：
**注意：**  webpack 每个版本的优化点都是不一样的
通用环境：这些优化既适用于开发化境也适用于生产环境。 
开发环境：
生产环境：
**通用环境**
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


**开发环境**
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
**生产环境**
以下步骤对于 生产环境 特别有帮助。
Source Maps
source map 相当消耗资源。你真的需要它们？


## -本篇完--

**三、项目实战篇**

Webpack与React
Webpack与Vue
Webpack与jQuery
Webpck与Node/Express

**四、内部原理篇**
webpack原理
开发loader plugin