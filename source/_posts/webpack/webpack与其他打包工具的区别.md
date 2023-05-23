---
title: webpack与其他打包工具的区别
cover: false
top: false
toc: false
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-12-12 10:43:28
author:
img:
coverImg:
password:
summary:
keywords:
---
## 一、模块化工具

模块化是一种处理复杂系统分解为更好的可管理模块的方式

可以用来分割，组织和打包应用。每个模块完成一个特定的子功能，所有的模块按某种方法组装起来，成为一个整体(`bundle`)

在前端领域中，并非只有`webpack`这一款优秀的模块打包工具，还有其他类似的工具，例如`Rollup`、`Parcel`、`snowpack`，以及最近风头无两的`Vite`

通过这些模块打包工具，能够提高我们的开发效率，减少开发成本

这里没有提及`gulp`、`grunt`是因为它们只是定义为构建工具，不能类比

### Rollup

`Rollup` 是一款 `ES Modules` 打包器，从作用上来看，`Rollup` 与 `Webpack` 非常类似。不过相比于 `Webpack`，`Rollup`要小巧的多

现在很多我们熟知的库都都使用它进行打包，比如：`Vue`、`React`和`three.js`等

举个例子：

```js
// ./src/messages.js
export default {
  hi: 'Hey Guys, I am zce~'
}

// ./src/logger.js
export const log = msg => {
  console.log('---------- INFO ----------')
  console.log(msg)
  console.log('--------------------------')
}

export const error = msg => {
  console.error('---------- ERROR ----------')
  console.error(msg)
  console.error('---------------------------')
}

// ./src/index.js
import { log } from './logger'
import messages from './messages'
log(messages.hi)
```

然后通过`rollup`进行打包

```js
$ npx rollup ./src/index.js --file ./dist/bundle.js
```

打包结果如下图![img](https://static.vue-js.com/8fe07830-b143-11eb-85f6-6fac77c0c9b3.png)

可以看到，代码非常简洁，完成不像`webpack`那样存在大量引导代码和模块函数

并且`error`方法由于没有被使用，输出的结果中并无`error`方法，可以看到，`rollup`默认开始`Tree-shaking` 优化输出结果

因此，可以看到`Rollup`的优点：

- 代码效率更简洁、效率更高
- 默认支持 Tree-shaking

但缺点也十分明显，加载其他类型的资源文件或者支持导入 `CommonJS` 模块，又或是编译 `ES` 新特性，这些额外的需求 `Rollup`需要使用插件去完成

综合来看，`rollup`并不适合开发应用使用，因为需要使用第三方模块，而目前第三方模块大多数使用`CommonJs`方式导出成员，并且`rollup`不支持`HMR`，使开发效率降低

但是在用于打包`JavaScript` 库时，`rollup`比 `webpack` 更有优势，因为其打包出来的代码更小、更快，其存在的缺点可以忽略

### Parcel

Parcel ，是一款完全零配置的前端打包器，它提供了 “傻瓜式” 的使用体验，只需了解简单的命令，就能构建前端应用程序

`Parcel` 跟 `Webpack` 一样都支持以任意类型文件作为打包入口，但建议使用`HTML`文件作为入口，该`HTML`文件像平时一样正常编写代码、引用资源。如下所示：

```js
<!-- ./src/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Parcel Tutorials</title>
</head>
<body>
  <script src="main.js"></script>
</body>
</html>
```

main.js文件通过`ES Moudle`方法导入其他模块成员

```js
// ./src/main.js
import { log } from './logger'
log('hello parcel')
// ./src/logger.js
export const log = msg => {
  console.log('---------- INFO ----------')
  console.log(msg)
}
```

运行之后，使用命令打包

```text
npx parcel src/index.html
```

执行命令后，`Parcel`不仅打包了应用，同时也启动了一个开发服务器，跟`webpack Dev Server`一样

跟`webpack`类似，也支持模块热替换，但用法更简单

同时，`Parcel`有个十分好用的功能：支持自动安装依赖，像`webpack`开发阶段突然使用安装某个第三方依赖，必然会终止`dev server`然后安装再启动。而`Parcel`则免了这繁琐的工作流程

同时，`Parcel`能够零配置加载其他类型的资源文件，无须像`webpack`那样配置对应的`loader`

打包命令如下：

```text
npx parcel src/index.html
```

由于打包过程是多进程同时工作，构建速度会比`Webpack` 快，输出文件也会被压缩，并且样式代码也会被单独提取到单个文件中

![img](https://static.vue-js.com/ec17e7a0-b1a2-11eb-85f6-6fac77c0c9b3.png)

可以感受到，`Parcel`给开发者一种很大的自由度，只管去实现业务代码，其他事情用`Parcel`解决

### Snowpack

Snowpack，是一种闪电般快速的前端构建工具，专为现代`Web`设计，较复杂的打包工具（如`Webpack`或`Parcel`）的替代方案，利用`JavaScript`的本机模块系统，避免不必要的工作并保持流畅的开发体验

开发阶段，每次保存单个文件时，`Webpack`和`Parcel`都需要重新构建和重新打包应用程序的整个`bundle`。而`Snowpack`为你的应用程序每个文件构建一次，就可以永久缓存，文件更改时，`Snowpack`会重新构建该单个文件

下图给出`webpack`与`snowpack`打包区别：

![img](https://static.vue-js.com/79197830-b1a3-11eb-85f6-6fac77c0c9b3.png)

在重新构建每次变更时没有任何的时间浪费，只需要在浏览器中进行HMR更新

### Vite

vite ，是一种新型前端构建工具，能够显著提升前端开发体验

它主要由两部分组成：

- 一个开发服务器，它基于 原生 ES 模块 提供了丰富的内建功能，如速度快到惊人的 [模块热更新HMR
- 一套构建指令，它使用 Rollup打包你的代码，并且它是预配置的，可以输出用于生产环境的优化过的静态资源

其作用类似`webpack`+ `webpack-dev-server`，其特点如下：

- 快速的冷启动
- 即时的模块热更新
- 真正的按需编译

`vite`会直接启动开发服务器，不需要进行打包操作，也就意味着不需要分析模块的依赖、不需要编译，因此启动速度非常快

利用现代浏览器支持`ES Module`的特性，当浏览器请求某个模块的时候，再根据需要对模块的内容进行编译，这种方式大大缩短了编译时间

原理图如下所示：

![img](https://static.vue-js.com/9f2eed30-b143-11eb-85f6-6fac77c0c9b3.png)

在热模块`HMR`方面，当修改一个模块的时候，仅需让浏览器重新请求该模块即可，无须像`webpack`那样需要把该模块的相关依赖模块全部编译一次，效率更高

### webpack

相比上述的模块化工具，`webpack`大而全，很多常用的功能做到开箱即用。有两大最核心的特点：**一切皆模块**和**按需加载**

与其他构建工具相比，有如下优势：

- 智能解析：对 CommonJS 、 AMD 、ES6 的语法做了兼容
- 万物模块：对 js、css、图片等资源文件都支持打包
- 开箱即用：HRM、Tree-shaking等功能
- 代码分割：可以将代码切割成不同的 chunk，实现按需加载，降低了初始化时间
- 插件系统，具有强大的 Plugin 接口，具有更好的灵活性和扩展性
- 易于调试：支持 SourceUrls 和 SourceMaps
- 快速运行：webpack 使用异步 IO 并具有多级缓存，这使得 webpack 很快且在增量编译上更加快
- 生态环境好：社区更丰富，出现的问题更容易解决







1. 除了Webpack外，你还了解哪些模块管理工具
    webpack:
    就目前而言，webpack已是最常用的打包工具，webpack是基于入口的。webpack会自动地递归解析入口所需要加载的所有资源文件，然后用不同的Loader来处理不同的文件，用Plugin来扩展webpack功能。
    gulp：
    gulp是一个前端自动化构建工具，强调的是前端开发的工作流程，可以通过配置一系列的task，第一task处理的事情（如代码压缩，合并，编译以及浏览器实时更新等）。然后定义这些执行顺序，来让glup执行这些task，从而构建项目的整个开发流程。自动化构建工具并不能把所有的模块打包到一起，也不能构建不同模块之间的依赖关系。

  grunt：
  是基于任务和流（Task、Stream）的。类似jQuery，找到一个（或一类）文件，对其做一系列链式操作，更新流上的数据， 整条链式操作构成了一个任务，多个任务就构成了整个web的构建流程。
  Rollup

2. Rollup 是一款 ES Modules 打包器， 从作用上来看，Rollup 与 Webpack 非常类似。不过相比于 Webpack，Rollup要小巧的多
    现在很多我们熟知的库都都使用它进行打包，比如：Vue、React和three.js等
    Parcel
    Parcel ，是一款完全零配置的前端打包器，它提供了 “傻瓜式” 的使用体验，只需了解简单的命令，就能构建前端应用程序
    Parcel 跟 Webpack 一样都支持以任意类型文件作为打包入口，但建议使用		HTML文件作为入口，该HTML文件像平时一样正常编写代码、引用资源。
    模块化是一种处理复杂系统分解为更好的可管理模块的方式。可以用来分割、组织和打包应用。每个模块完成一个特定的子功能，所有的模块按某种方法组装起来，成为一个整体。
    在前端领域中，除了Webpack外，比较流行的模块打包工具还包括Rollup、Parcel、snowpack和最近风靡的Vite。

  2.1. Rollup
  Rollup 是一款 ES Modules 打包器，可以将小块代码编译成大块复杂的代码，例如 library 或应用程序。从作用上来看，Rollup 与 Webpack 非常类似。不过相比于 Webpack，Rollup 要小巧的多。现在很多苦都使用它进行打包，比如：Vue、React和three.js等。
  使用之前，可以使用npm install --global rollup 命令进行安装。Rollup 可以通过命令行接口(command line interface)配合可选配置文件(optional configuration file)来调用，或者可以通过 JavaScript API来调用。运行 rollup --help 可以查看可用的选项和参数。
  2.2. Parcel
  Parcel ，是一款完全零配置的前端打包器，它提供了 “傻瓜式” 的使用体验，只需了解简单的命令，就能构建前端应用程序。
  2.3.  Vite
  Vite是Vue的作者尤雨溪开发的Web开发构建工具，它是一个基于浏览器原生ES模块导入的开发服务器，在开发环境下，利用浏览器去解析import，在服务器端按需编译返回，完全跳过了打包这个概念，服务器随启随用。同时不仅对Vue文件提供了支持，还支持热更新，而且热更新的速度不会随着模块增多而变慢。
  Vite具有以下特点： - 快速的冷启动 - 即时热模块更新（HMR，Hot Module Replacement） - 真正按需编译
  Vite由两部分组成： - 一个开发服务器，它基于 原生 ES 模块 提供了丰富的内建功能，如速度快到惊人的 [模块热更新HMR。 - 一套构建指令，它使用 Rollup打包你的代码，并且它是预配置的，可以输出用于生产环境的优化过的静态资源。
  Vite在开发阶段可以直接启动开发服务器，不需要进行打包操作，也就意味着不需要分析模块的依赖、不需要编译，因此启动速度非常快。当浏览器请求某个模块的时候，根据需要对模块的内容进行编译，大大缩短了编译时间。工作原理如下图所示。
  在热模块HMR方面，当修改一个模块的时候，仅需让浏览器重新请求该模块即可，无须像Webpack那样需要把该模块的相关依赖模块全部编译一次，因此效率也更高。
3. webpack，rollup，parcel优劣？(了解)
    对比

  Webpack
  Rollup
  Parcel
  功能


为处理资源管理和分割代码而生，可以用来处理任何类型的文件，灵活，插件多。
	用标准化的格式（es6）来写代码，通过减少死代码尽可能地缩小包体积。
	用标准化的格式（es6）来写代码，通过减少死代码尽可能地缩小包体积。
配置
	webpack需要配config文件，指明entry, output, plugin，transformations。
	rollup需要配config文件，指明entry, output, plugin，transformations。rollup 有对import/export所做的node polyfills，webpack没有。rollup支持相对路径，而webpack没有，所以得使用

	parcel则是完全开箱可用的，不用配置。
入口文件
	webpack只支持js文件作为入口文件，如果要以其他格式的文件作为入口，比如html文件为入口，如要加第三方Plugin。
	rollup可以用html作为入口文件，但也需要plugin，比如rollup-plugin-html-entry。
	parcel可以用index.html作为入口文件，而且它会通过看index.html的script tag里包含的什么自己找到要打包生成哪些js文件。
transformations
	transformations指的是把其他文件转化成js文件的过程，需要经过transformation才能够被打包。webpack使用Loaders来处理。
	rollup使用plugins来处理。
	parcel会自动去转换，当找到配置文件比如.babelrc, .postcssrc后就会自动转。
摇树优化
	摇树优化是webpack的一大特性。需要1，用import/export语法，2，在package.json中加副作用的入口，3，加上支持去除死代码的缩小器（uglifyjsplugin）。
	rollup会统计引入的代码并排除掉那些没有被用到的。这使您可以在现有工具和模块的基础上构建，而无需添加额外的依赖项或膨胀项目的大小。
	parcel不支持摇树优化。
dev server
	webpack用webpack-dev-server。
rollup用rollup-plugin-serve和rollup-plugin-livereload共同作用。
	parcel内置的有dev server
热更新
	webpack的 wepack-dev-server支持hot模式。
	rollup不支持hmr。
	parcel有内置的hmr。
代码分割
	webpack通过在entry中手动设置，使用CommonsChunkPlugin，和模块内的内联函数动态引入来做代码分割。
	rollup有实验性的代码分割特性。它是用es模块在浏览器中的模块加载机制本身来分割代码的。需要把experimentalCodeSplitting 和 experimentalDynamicImport 设为true。
	parcel支持0配置的代码分割。主要是通过动态improt。