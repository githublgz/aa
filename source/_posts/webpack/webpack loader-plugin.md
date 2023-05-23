---
title: webpack loader-plugin
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-09-17 11:35:48
author:
img:
coverImg:
password:
summary:
keywords:
---

# 一、loader
对于webpack，一切皆模块。webpack 只能理解 JavaScript 和 JSON 文件，其他类型/后缀的文件都需要经过 loader 处理，将它们转换为js可识别的有效模块 (webpack 天生支持 ECMAScript、CommonJS、资源模块等模块类型)。loader可以做语言翻译(比如将文件从 TypeScript 转换为 JavaScript) 或格式转换(将内联图像转换为 data URL)还有样式编译(允许直接在 JavaScript 模块中 import CSS文件)。
## 1.loader是什么？
每个 loader 本质上都是一个导出为函数的 JavaScript 模块。loader runner 会调用此函数，将资源文件或者上一个 loader 产生的结果传进去，经过编译转换把处理结果再输出去（如果后面还有 loader 就传给下一个）。函数中的 this 作为上下文会被 webpack 填充，并且 loader runner 中包含一些实用的方法，比如可以使 loader 调用方式变为异步，或者获取 query 参数。
简言之 loader 就是模块转换器。有点像 Vue 的过滤器。
loader从下到上地取值(evaluate)/执行(execute)，也就是是从后往前执行。

## 2. 有哪些常见的Loader？他们是解决什么问题的？
● source-map-loader：加载额外的 Source Map 文件，以方便断点调试
● image-loader：加载并且压缩图片文件
● eslint-loader：通过 ESLint 检查 JavaScript 代码

文件
url-loader 与 file-loader 类似，但当文件 size 小于设置的 limit 值，会返回 data URL
file-loader 将文件保存至输出文件夹中并返回 URL (默认是是绝对路径，可以 outputPath 和 publicPath 通过配置成相对路径)
语法转换
babel-loader 使用 Babel 加载 ES2015+ 代码并将其转换为 ES5
ts-loader 像加载 JavaScript 一样加载 TypeScript 2.0+
样式
style-loader 将样式模块导出的内容以往 <head> 中注入多个 <style> 的形式，添加到 DOM 中
css-loader 加载 CSS 文件并解析 @import 的 CSS 文件，将 url() 处理成 require() 请求，最终返回 CSS 代码
less-loader 加载并编译 LESS 文件
sass-loader 加载并编译 SASS/SCSS 文件
postcss-loader 使用 PostCSS 加载并转换 CSS/SSS 文件
stylus-loader 加载并编译 Stylus 文件
框架
vue-loader 加载并编译


## 3. 在config文件配置
在 module.rules 配置转换规则时，有两个必选属性 test 和 use。
像这样 module: { rules: [{ test: /\.txt$/, use: 'raw-loader' }] }
会告诉 webpack 编译器(compiler) ，当碰到「在 require()/import 语句中被解析为 '.txt' 的路径」时，在你对它打包之前，先用 raw-loader 转换一下(预处理)。

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: ['a-loader', 'b-loader', 'c-loader'], // 从右到左，c-loader -> b-loader -> a-loader
      },
      {
        test: /\.css$/, // test属性，规定哪些文件会被转换
        use: [ // use属性，在进行转换时，应用哪些 loader
          { loader: 'style-loader' }, 
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          },
          { loader: 'sass-loader' } 
        ] // 从下到上，sass-loader -> css-loader -> style-loader
      }
    ],
  },
}
```

## 4. loader 的执行顺序

每个 Loader 的功能都应是单一而专注的，这样不仅便于维护，还能让它们在更多场景中被串联应用。因此 Loader 通常是组合使用的。链式调用一组 loader 时 (无论是模块规则配置还是内联方式)，它们会按照相反的顺序执行。即从右到左(或从下到上)，依次将前一个 loader 转换后的结果传递给下一个 loader。直到最后一个 loader 返回 webpack 所期望的 JavaScript。有点像 Promise 的 then。
loader 可以用 String 或 Buffer 的形式传递它的处理结果，complier 会把它们在 loader 之间相互转换。最终结果也就是最后一个 loader 会返回一或两个值：第一个是代表模块的 JavaScript 源码的 String 或者 Buffer（这个结果会交给 webpack 的 require，因此一定是一段可执行的 node 模块的 JS 脚本[用字符串存储的]）；第二个是可选的 SourceMap (格式为 JSON 对象)。

一组 loader 的执行有两个阶段：Pitching 阶段 和 Normal 阶段，类似于js中的事件捕获、冒泡。
webpack 的 loader-runner 会按正序(从左到右) require 每个 loader，把这个 loader 的模块导出函数 和 pitch函数都存到 loaderContext 对象上，然后执行该 loader 的 pitch 方法（如果有的话）；如果一组 loader 的 pitch 都没有返回值，就开始 Normal阶段：反向(从右到左)执行 loader 的导出函数，依次进行模块源码的转换，直到拿到最后的处理结果；但是当 Pitching 阶段某个 loader 的 pitch 有返回值，那么就会跳过剩余未读取的 loader，直接进入执行 loader 的环节。从前一个 require 的 loader 开始执行，pitch 的返回值即是传入的第一个参数。除了 pitch 有返回的那个 loader，倒序执行已经 require 的每个 loader。
原理可参考：浅析 webpack 打包流程(原理) 二 之【执行 loader 阶段，初始化模块 module，并用 loader 倒序转译】部分

```
module: {
  rules: [{ test:/\.vue$/, use: ['a-loader', 'b-loader', 'c-loader'] }]
},
```
根据以上配置，*.vue文件在 loader 处理阶段将经历以下步骤：
```
|- a-loader `pitch` 方法
  |- b-loader `pitch` 方法
    |- c-loader `pitch` 方法
      |- 以模块依赖的形式即 import/require() 获取资源内容
    |- c-loader normal 执行
  |- b-loader normal 执行
|- a-loader normal 执行
```
|- a-loader normal 执行
如果 b-loader 的 pitch 方法有返回值，直接跳过 c-loader 进入 loader 执行阶段，并且 b-loader 也不会执行。整个过程就会变成这样：

```
|- a-loader `pitch` 方法
  |- b-loader `pitch` 方法 (有返回结果，则跳过后面未 require 的 loader，直接进入 loader 执行阶段)
|- a-loader normal 执行 (传入参数是 b-loader pitch 的返回值)
```
图解loader：
![loader图解](http://hghqz.vip/webpack/loader.jpg "loader图解")
loader 可以利用 pitch 阶段来做什么？
Pitch 方法是什么：每个 loader 可以挂载一个 pitch 函数，该函数主要用于利用 module 的 request 来提前做一些拦截处理的工作（后面会举例说明），并不实际处理模块内容。
事实上很多 loader 并未定义 pitch，一般定义了 pitch 就是某些情况要返回东西。
详情请看 Pitching Loader。

当一组 loader 被链式调用，像上面的例子，正常情况只有最后一个c-loader能获得资源文件(起始 loader 只有一个入参：资源文件的内容)，b-loader拿到的是c-loader处理结果，中间如果再多几个 loader 也是如此，只能拿到上一个传来的值，处理好再传递给下一个。直到第一个a-loader返回最终结果。
尽管 loaders 常被串联使用，但它们的功能仍旧是单一并独立的，且只关心自己的输入和输出。就像工厂流水线，一个区域的工人/机器只干一种类型的活。所以合理搭配并配置正确的顺序才能得到我们想要的结果。

它只想要 request 后面的 元数据(调用 loader时传入的第三个参数 metadata)。
但有时候我们需要把两个用来做最后处理的 loader 串起来，比如 style-loader 和 css-loader。
但 style-loader 并不需要 css-loader 的结果，它只需要 request 后的元数据。

# 二、plugin

plugin是什么？
plugin是插件的意思，通常是用于对某个现有的架构进行扩展
webpack中的plugin，就是对webpack现有功能的各种扩展，比如打包优化、文件压缩等等

loader和plugin的区别
loader主要用于加载/转换某些类型的模块，它是一个加载/转换器
plugin是插件，它是对webpack本身的扩展，它是一个扩展器

plugin的使用过程
步骤一：通过npm安装需要使用的plugins（某些webpack已经内置的插件不需要安装）
步骤二：在webpack.config.js中的plugins配置插件

## 1.是什么
Plugin（Plug-in）是一种计算机应用程序，它和主应用程序互相交互，以提供特定的功能

是一种遵循一定规范的应用程序接口编写出来的程序，只能运行在程序规定的系统下，因为其需要调用原纯净系统提供的函数库或者数据

webpack中的plugin也是如此，plugin赋予其各种灵活的功能，例如打包优化、资源管理、环境变量注入等，它们会运行在 webpack 的不同阶段（钩子 / 生命周期），贯穿了webpack整个编译周期
![webpack声明周期]( http://hghqz.vip/webpack/webpack%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.jpg "webpack声明周期")

关于整个编译生命周期钩子，有如下：
```
entry-option ：初始化 option
run
compile：真正开始的编译，在创建 compilation 对象之前
compilation ：生成好了 compilation 对象
make 从 entry 开始递归分析依赖，准备对每个模块进行 build
after-compile：编译 build 过程结束
emit ：在将内存中 assets 内容写到磁盘文件夹之前
after-emit ：在将内存中 assets 内容写到磁盘文件夹之后
done：完成所有的编译过程
failed：编译失败的时候
```

## 2. 常见的Plugin
![常见plugin](http://hghqz.vip/webpack/%E5%B8%B8%E8%A7%81plugin.jpg "常见plugin")

## 3. 是否写过Loader和Plugin？描述一下编写loader或plugin的思路？
Loader像一个"翻译官"把读到的源文件内容转义成新的文件内容，并且每个Loader通过链式操作，将源文件一步步翻译成想要的样子。
编写Loader时要遵循单一原则，每个Loader只做一种"转义"工作。 每个Loader的拿到的是源文件内容（source），可以通过返回值的方式将处理后的内容输出，也可以调用this.callback()方法，将内容返回给webpack。 还可以通过 this.async()生成一个callback函数，再用这个callback将处理后的内容输出出去。 此外webpack还为开发者准备了开发loader的工具函数集——loader-utils。
相对于Loader而言，Plugin的编写就灵活了许多。 webpack在运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

## 4. Loader和Plugin的不同？
不同的作用
● Loader直译为"加载器"。Webpack将一切文件视为模块，但是webpack原生是只能解析js文件，如果想将其他文件也打包的话，就会用到loader。 所以Loader的作用是让webpack拥有了加载和解析非JavaScript文件的能力。
● Plugin直译为"插件"。Plugin可以扩展webpack的功能，让webpack具有更多的灵活性。 在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。
不同的用法

1. Loader在module.rules中配置，也就是说他作为模块的解析规则而存在。 类型为数组，每一项都是一个Object，里面描述了对于什么类型的文件（test），使用什么加载(loader)和使用的参数（options）
2. Plugin在plugins中单独配置。 类型为数组，每一项是一个plugin的实例，参数都通过构造函数传入。