---
title: webpack概念
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-11-14 15:31:05
author:
img:
coverImg:
password:
summary:
keywords:
---

# 一、webpack概述
## 1. 什么是webpack？
webpack被定义为现代 JavaScript 应用程序的静态模块打包器(module bundler)，是目前最为流行的JavaScript打包工具之一。

webpack会以一个或多个js文件为入口，递归检查每个js模块的依赖，从而构建一个依赖关系图(dependency graph)，然后依据该关系图，将整个应用程序打包成一个或多个bundle。

由于webpack是用nodejs编写的，所以它依赖的运行环境就是nodejs。也正因为这一点，webpack只能识别JavaScript，所有非JavaScript（包括HTML，CSS，Typescript等）编写的文件都需要经过处理，这是借助对应的loader实现的。

webpack使用的是nodejs默认的模块系统：commonjs，借助nodejs提供的API来操作待打包项目的源文件（如fs模块、path模块等）。webpack将这些文件整合压缩后，输出到一个特定的目录下（通常是dist）。处理过的dist一般会被直接上传到静态资源服务器使用。

webpack 是一个开源的前端包工具。webpack 提供了前端开发缺乏的模块化开发方式,将各种静态资源视为模块,并从它生成优化过的代码。 要使用 webpack 前须先安装 node.js。

webpack是前端的一个自动化工具,有了它可以大大提高写项目的效率,可以对css,js文件进行自动压缩,把sass代码自动解析成对应的css文件,让你的代码和样式实时的显示在浏览器上,当然,我们使用webpack的目的还是为了项目完成后进行打包,webpack并不强制使用AMD或者CMD这之中的某一种方案,而是通过兼容所有模块化方案让你可以按需接入项目，有了webpack，你可以随意选择你喜欢的模块化方案，至于怎么处理模块之间的依赖关系及如何按需打包，webpack会帮你处理好。

## 2. 为什么要使用webpack？
第一，未打包的项目通常体积庞大，文件数量众多。如果将其直接上传到服务器，用户访问网站时，浏览器会发送大量的http请求来下载这些文件，这会给服务器带来很大的压力，同时客户端的体验也非常不好。

第二，浏览器本身不支持任何模块系统。因此，使用模块系统开发出的JavaScript代码无法直接在浏览器中运行，而模块系统对现代JavaScript开发是非常重要的。这样，我们需要有一个工具，将模块系统编写出的代码转化为浏览器所能识别的代码。webpack就可以完成这一任务。

第三，大多数情况下，我们不希望源代码暴露给用户，即使是保密性要求不那么高的前端代码。我们知道，PC端浏览器通常都提供开发者工具，可以方便地查看和调试前端代码，这在开发环境下意义重大。但对于生产环境，暴露源代码不仅没有太大意义，反而存在安全隐患（如果黑客比你更先发现代码中的bug，你可能面临严重损失）。因此，我们可以借助webpack重组和混淆源代码，增加黑客阅读源代码的难度，以提升系统的安全性。

第四，借助webpack提供的dev-server，可以实现前后端分离。dev-server本质上就是一个node服务。当通过命令行启用dev-server时，webpack会在本地启动一个node服务，将打包后的文件作为静态资源注入该服务，这样就可以在不依赖后台（这种说法并不完全准确，实际上webpack是通过node为你提供后台服务）的情况下进行前端开发了。

除了以上这些，webpack还有很多强大的功能，这里暂不详述。

## 3. 工作原理

简单的说就是分析代码，找到“require”、“exports”、“define”等关键词，并替换成对应模块的引用。

在一个配置文件中，指明对某些文件进行编译、压缩、组合等任务。把你的项目当成一个整体，通过一个给定的主文件 （index.js），webpack将从这个文件开始找到你的项目的所有的依赖文件，使用loaders处理他们，最后打包为一个浏览器可以识别的js文件。

流程细节
Webpack的构建流程可以分为以下三个阶段：
1. 初始化：启动构建，读取与合并配置参数，加载Plugin，实例化Complier.
2. 编译：从Entry出发，针对每个Module串行调用对应的Loader去翻译文件内容，再找到该Module依赖的Module，递归地进行编译处理。
3. 输出： 对编译后的Module组合成Chunk，把Chunk转换成文件，输出到文件系统。
 如果只执行一次构建，以上阶段将会按照顺序各执行一次。但在开启监听模式下，流程将变为如下：
 ![打包流程](http://hghqz.vip/webpack/%E6%89%93%E5%8C%85%E6%B5%81%E7%A8%8B.jpg "打包流程")

## 4. webpack的优缺点？
优点：
（1） webpack 是以 commonJS 的形式来书写脚本滴，但对 AMD/CMD 的支持也很全面，方便旧项目进行代码迁移。
（2）能被模块化的不仅仅是 JS 了。
（3） 开发便捷，能替代部分 grunt/gulp 的工作，比如打包、压缩混淆、图片转base64等。
（4）扩展性强，插件机制完善
缺点：
● 配置复杂
● 不分包bundle.js体积庞大
● 只能用于采用模块化开发的项目
● 打包慢
● ES模块除Module外全用babel转换，但是一部分ES2015 语法的 firefox 与 chrome 浏览器中能直接跑的代码，无法用 webpack 编译


## 5. 基本配置
要在项目中使用webpack，需要首先安装nodejs，它是webpack的运行环境。nodejs安装成功后，就可以通过npm install webpack -g来全局安装webpack。这样就可以在你的项目中使用webpack了。

在项目中使用webpack的核心是编写配置文件。配置文件通常命名为webpack.config.js，是一个符合commonjs规范的js文件。该文件通过module.exports暴露出一个js对象，我们称这个对象为webpack的配置对象（options）。webpack会根据这个配置对象来决定如何打包项目。

配置对象中包含四个核心参数：

入口（entry）Entry:入口指示webpack以哪个文件为入口起点开始打包，分析构建内部依赖图
出口（output）output:输出指示webpack的打包后的资源bundles输出到哪里去，以及如何命名
加载器（loader）Loader:让webpack能够去处理哪些非JavaScrip文件（webpack自身只理解javaScript)
插件（plugin） plugin:插件可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量等
插件（mode） mode:模式指示webpack使用相应模式的配置

### 1. 入口（entry）
顾名思义，它定义了webpack的打包入口，也就是webpack从哪个js开始打包。

一个应用程序可以有一个或多个入口，由entry属性指定，通常是一个对象。如果这个对象内只包含了一个入口，也可以简写为一个字符串（或字符串数组）。如：
```
module.exports = {
  entry: {
    main: "./src/main.js"
  }
}

// 可以简写为:
  entry: "./src/main.js"
```
上述配置定义src目录下的main.js为打包入口，webpack将从这个文件开始，构建整个项目的依赖关系图。

一个应用程序可以有多个打包入口，常见的场景如多页应用，独立打包第三方库等：

```
entry: {
  app: './src/app.js',
  vendors: './src/vendors.js'
}
```
上面的配置，要求webpack分别以app.js和vendors.js为打包入口，独立构建依赖关系图。最终，项目代码和第三方代码将被独立打包出来。构建多页应用时，也是分别为每个页面提供一个入口文件，独立构建依赖图。

此外，入口参数允许传入字符串数组。如：
```
entry: ["./src/main1.js", "./src/main2.js"]
```
这两个文件都是应用的主入口，它们会被打包生成到同一个chunk文件中。当主入口文件过于庞大，需要拆分成多个，但希望它们输出到同一个打包文件时可以使用。

### 2. 出口（output）
也就是webpack的输出，由output属性定义。

与入口不同的是，一个应用程序只能有一个出口。出口是一个对象，包含两个属性：filename和path，分别定义打包结果的文件名和输出位置。如：
```
module.exports = {
  entry: {
    main: "./src/main.js"
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname + '/dist')
  }
}
```
以上配置定义main.js为应用的入口文件，最终输出的文件名为bundle.js，输出位置是当前路径下的dist文件夹。

当应用程序由多个打包入口时，产生的输出结果也会有很多个，一一为每个文件指定文件名非常不灵活。为此，webpack允许使用占位符来定义文件名。如：

```
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname + '/dist')
  }
}

// 打包将输出app.js和search.js两个文件
```
这里filename的值中[name]就是使用了占位符，webpack会将其替换成入口文件的文件名。因此，app.js和search.js这两个入口文件在打包后会在dist文件夹下生成两个同名文件。

当然，我们几乎从来不会这样定义filename，因为固定的文件名无法用于热更新（HMR，Hot Module Replacement，直译为模块热替换）。热更新的实现机制如下：

在一份清单文件（manifest文件）中列举所有依赖的模块，每个模块对应的文件名中带有一个版本号，如chunk.1.0.0.js。
当某个模块发生修改，就重新打包该模块，并修改对应文件名中的版本号，如chunk.1.0.1.js。此时文件名就发生了变化。
热更新机制检测到清单文件中的文件名发生变化，就会重新下载和更新该模块，文件名没有变化的模块不会被重新下载。这样应用就得到了更新。
由于webpack不需要对每次的代码修改都进行版本管理，所以它只需要向文件名中插入一个随机的hash值即可。这个hash值每次重新打包都会变化，以保证热更新机制可以正确更新。假如某次打包后的文件名为app.23j3j2366842h76ewhd.js，随后我们对该模块进行了修改，重新打包后webpack插入了一个新的hash值，得到app.er234hh9hydyt586.js。热更新模块检测到文件名变化，就会自动下载这个新的js文件，来更新应用的状态。

此时的出口一般写成这样：
```
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].[hash].js',
    path: path.resolve(__dirname + '/dist')
  }
}
```
这会输出两个类似于app.57bjs8k8rfht7.js和search.su774fju83jur.js的打包结果，它们会被添加到一份清单文件。每当修改模块的内容，webpack都会重新打包，生成新的hash值，并更新清单文件，这样热更新机制就可以生效了。

注意，使用splitChunk进行代码分割时，被分割出来的代码默认命名为chunk.[hash].js。

### 3. 加载器（loader）
在介绍加载器之前，我们先来看webpack打包时会遇到的一个问题。

在概述中我们已经讲到，webpack的运行环境是nodejs，因此它只能识别JavaScript。但是我们的项目中可能存在大量的非JavaScript文件，如HTML、CSS、Typescript、txt，甚至图片文件等。

有人可能会说，webpack又不需要执行这些文件，直接输出到dist目录下不就行了吗？

如果这些非JavaScript文件只被js文件引用，而他们之间互相没有依赖关系，webpack确实没必要解析他们。但当它们存在依赖关系时，问题就不这么简单了，如：
index.css
```
@import "./color.css"
```
这里index.css中引入了color.css。我们假设index.css是在js中引入的，那么webpack在解析js时自然会把index.css添加到依赖关系图中。

可是webpack运行在nodejs环境下，它无法解析index.css的内容，因此它不知道index.css内部还依赖了color.css。这样，color.css就无法被添加到依赖关系图中，而不在依赖关系图中的文件在打包时将被舍弃。也即是说，webpack最终打包出的代码中不会包含color.css。

这显然是错误的，我们需要color.css。

为了解决这个问题，我们需要一些额外的代码帮助webpack识别css文件中的依赖。我们会编写一个函数，它将index.css读取为一个字符串，然后转化成js（注意，转化成js只是为了让webpack解析依赖关系，因此转化出的js与原css并不等价）输出出来，这样webpack就可以解析了。而这个用于转换的函数，就称为一个loader。

所以，一个加载器（loader）实际上就是一个将特定的字符串转化成JavaScript代码的函数。换个角度来说，一个loader就是一个字符串处理函数。

通常，为了保证loader便于测试和复用，每个loader不会写的很复杂，实现的功能也有限。所以一个文件通常需要多个loader来处理。比如对于一个css文件，我们至少需要两个loader：css-loader和style-loader。前者用于解析css文件，后者用于将css注入到HTML文件中。style-loader会把css添加为页内样式（即直接把样式放在head中的<style>标签内），如果你希望打包出单独的css文件，需要使用extract-loader。

如果你希望为css文件定义loader，可以这样写（当然你需要使用npm先安装这些loader）：
```
module.exports = {
  module: {
    rules: [
      { 
        test: /\.css$/, 
        use: ['style-loader', 'css-loader'] 
      },
    ]
  }
};
```
它的含义是，对.css结尾的文件，使用'css-loader'和'style-loader'这两个loader。webpack将依次从后向前执行每个loader。比如在解析到index.css时，它将经历以下步骤：

使用nodejs的fs模块读取index.css，将读取到的字符串交给css-loader。
执行css-loader。它是一个函数，将原始字符串进行一定的处理，输出一个新的字符串。
将上一步输出的字符串交给style-loader，进行第二步处理，最终仍然输出一个字符串。
由webpack解析最终的处理结果。
因为webpack采用的是流式处理，所以loader的书写顺序非常重要，最先需要执行的loader必须放在数组的最后。

基于这个原理，我们也可以自行手写loader，来满足特定的需求。比如官方没有关于.txt文本文件的loader，所以webpack不能解析文本文件中所包含的依赖（因为文本文件没有任何格式约定，所以无法定义一个普适的loader）。如果你的项目中有需要解析的文本文件，并且它们有严格的格式要求，那么你就可以自行实现一个loader，实现对这类资源的打包。具体实现方法见webpack中文网 - 编写一个 loader。

### 4. 插件（plugin）
一个插件就是一个对webpack功能的定制或扩展。

loader的使用场景是有限的，它只能用来帮助webpack加载非js文件。如果我们想在webpack打包的任何一个过程中添加某些特定的功能，就需要借助插件来实现。它是webpack灵活性的一大体现，也是webpack的支柱功能，因为webpack自身就是构建于插件系统之上的。

比如，我们想要在webpack开始构建时执行某些操作，就可以定义一个像下面的插件：
```
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

class ConsoleLogOnBuildWebpackPlugin {
    apply(compiler) {
        compiler.hooks.run.tap(pluginName, compilation => {
            console.log("webpack 构建过程开始！");
            console.log("当前时间：" + new Date());
        });
    }
}
```
在webpack配置文件中这样使用插件：
```
const ConsoleLogOnBuildWebpackPlugin = require('ConsoleLogOnBuildWebpackPlugin')
module.exports = {
  ...
  plugins: [
    new ConsoleLogOnBuildWebpackPlugin()
  ]
}
```

这样，webpack在开始构建时，就会执行我们的console.log方法。当然，你可以定制的功能远不止这些，这里只是向你展示插件的基本用法。

我们看到，一个插件就是一个带有apply原型方法的类（也可以是一个构造函数，并且它的原型对象上有apply方法，两者是等价的）。在配置文件中使用new关键字会创建一个插件实例，webpack将所有插件定义的回调函数注册到对应的生命周期钩子上。当webpack执行到对应的阶段时，就会调用这些钩子函数，实现插件定制的功能。

插件可以传入一个配置对象，用于构造插件实例。而插件上的原型方法apply会被webpack所调用，webpack会将编译器对象compiler传入apply方法。该对象在整个编译过程中都是可用的。如：
```
function HelloWorldPlugin(options) {
  // 使用 options 设置插件实例
}

HelloWorldPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function(compilation) {
    console.log('Hello World!');
  });
};

module.exports = HelloWorldPlugin;
```
我们在配置文件中传入的配置对象options会被构造函数接收，用于构造插件实例，在apply方法中可以通过this获得。

然后我们在插件的原型上定义了一个apply方法，webpack解析配置文件时会执行它，并传入webpack的编译器对象。我们通过语句compiler.plugin('done',function(compilation){...}为webpack的编译器对象注册了一个done阶段（即打包完成）的回调函数。当webpack打包完成时，会调用这个函数，并传入当前webpack的编译器状态对象：compilation。

我们可以借助compiler和compilation这两个对象，在任何一个阶段执行我们想执行的操作。前者是编译器对象，后者是当前状态对象。具体编写插件的方法请参考webpack中文网 - 编写一个插件。