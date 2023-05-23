---
title: webpack面试题总结
cover: false
top: false
toc: true
mathjax: false
tags:
  - webpack
categories:
  - 前端工程化
abbrlink: 23375
date: 2020-12-03 20:08:31
author:
img:
coverImg:
password:
summary:
keywords:
---

webpack 面试题

# 一、 概念

## 1. 什么是webpack，谈谈你对它的理解？

是一个模块化打包工具，将不同的资源和文件，进行打包，合并在一个文件里。
概念+打包流程+前端模块化
1、依赖管理：方便引用第三方模块，让模块更容易复用、避免全局注入导致的冲突、、避免重复加载或者加载不必要的模块
2、合并代码：把各个分散的模块集中打包成大文件，减少HTTP的链接的请求次数，配合uglify.js可以减少、优化代码的体积
3、各种插件：babel把ES6+转化为ES5-，eslint可以检查编译时的各种错误

(30条消息) 前端模块化理解*perwhy_wang的博客-CSDN博客*前端模块化的理解

## 2. webpack的工作原理?

### 工作原理概括

### 基本概念

在了解webpack原理前，需要掌握以下几个核心概念，以方便后面的理解：

1. Entry:入口指示webpack以哪个文件为入口起点开始打包，分析构建内部依赖图

2. output:输出指示webpack的打包后的资源bundles输出到哪里去，以及如何命名

3. Loader:让webpack能够去处理哪些非JavaScrip文件（webpack自身只理解javaScript)

4. plugin:插件可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量等

5. mode:模式指示webpack使用相应模式的配置

   ### 流程概括

   webpack的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

6. 初始化参数：从配置文件和Shell语句中读取与合并参数，得出最终的参数；

7. 开始编译： 用上一步得到的参数初始化Complier对象，加载所有配置的插件，执行对象的run方法开始执行编译；

8. 确定入口： 根据配置中的entry找出所有入口文件；

9. 编译模块：从入口文件出发，调用所有配置的Loader对模块进行翻译，再找出该模块依赖的模块，再递归本步骤知道所有入口依赖的文件都经过了本步骤的处理；

10. 完成模块编译： 在经过第4步使用Loader翻译完所有模块后，得到了每个模块被翻译后的最终内容以及他们之间的依赖关系；

11. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的Chunk，再把每个Chunk转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；

12. 输出完成： 在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

    在以上过程中，webpack会在特定的时间点广播出特定的时间，插件在监听到感兴趣的时间后会执行特定的逻辑，并且插件可以调用Webpack提供的API改变Webpack的运行结果。

    ### 流程细节

    Webpack的构建流程可以分为以下三个阶段：

13. 初始化：启动构建，读取与合并配置参数，加载Plugin，实例化Complier.

14. 编译：从Entry出发，针对每个Module串行调用对应的Loader去翻译文件内容，再找到该Module依赖的Module，递归地进行编译处理。

15. 输出： 对编译后的Module组合成Chunk，把Chunk转换成文件，输出到文件系统。
    如果只执行一次构建，以上阶段将会按照顺序各执行一次。但在开启监听模式下，流程将变为如下：

## 3. webpack4和webpack5的区别？

更快的构建速度
更高的版本要求
更灵活的模块组合
更智能的缓存优化
更小的体积
webpack4 上需要下载安装 terser-webpack-plugin 插件
webpack5 内部本身就自带 js 压缩功能，他内置了 terser-webpack-plugin 插件，我们不用再下载安装。而且在 mode=“production” 的时候会自动开启 js 压缩功能。
webpack4 缓存配置
● npm install hard-source-webpack-plugin -D
webpack5 缓存配置
● webpack5 内部内置了 cache 缓存机制。直接配置即可。
● cache 会在开发模式下被设置成 type： memory 而且会在生产模式把cache 给禁用掉。
webpack4 启动服务
● 通过 webpack-dev-server 启动服务
webpack5 启动服务
● 内置使用 webpack serve 启动，但是他的日志不是很好，所以一般都加都喜欢用 webpack-dev-server 优化。
devtool的区别
● sourceMap需要在 webpack.config.js里面直接配置 devtool 就可以实现了。而 devtool有很多个选项值，不同的选项值，不同的选项产生的 .map 文件不同，打包速度不同。
● 一般情况下，我们一般在开发环境配置用“cheap-eval-module-source-map”，在生产环境用‘none’。
v4: devtool: ‘cheap-eval-module-source-map’
v5: devtool: ‘eval-cheap-module-source-map’
打包：
● webpack4打包:即使后续没有使用到num1的函数，依然会将代码打包进去
● webpack5打包:后续没有使用到num1的函数，不会将代码打包进去
输出代码：
● webpack4只能输出es5的代码
● webpack5新增属性output.ecmaVersion，可以生成ES5和ES6的代码

## 4. 前端代码为何要进行构建和打包？

1. 代码方面体积更小，加载更快（tree-shaking，压缩合并）

   ```
    i. 编译高级语言和语法（ts，es6，模块化） ii. 兼容性和错误提示（polyfill，postcss，eslint）
   ```

2. 研发流程统一、高效的开发环境

   ```
    i. 统一的构建流程和产出标准 ii. 集成公司构建规范（提测，上线）
   ```

打包之后许多零碎的文件打包成一个整体，页面只需请求一次，js文件中使用模块化互相引用，这样能在一定程度上提供页面渲染效率，打包的同时会进行编译，将es6，sass等高级语法进行转换编译，以兼容高版本的浏览器

## 5. webpack的优缺点？

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

## 6. 什么是bundle，什么是chunk，什么是module

bundle： 是由webpack打包出来的文件
chunk： 是指webpack在进行模块依赖分析的时候，代码分割出来的代码块
module： 是开发中的单个模块

# 二、 loader

## 1. Loader机制的作用是什么？

webpack 本身只能处理 JavaScript 和 JSON 文件，而 loader 为 webpack 添加了处理其他类型文件的能力。
webpack默认只能打包js文件，配置里的module.rules数组配置了一组规则，告诉 Webpack 在遇到哪些文件时使用哪些 Loader 去加载和转换打包成js。
注意：use属性的值需要是一个由 Loader 名称组成的数组，Loader 的执行顺序是由后到前的；每一个 Loader 都可以通过 URL querystring 的方式传入参数，例如css-loader?minimize中的minimize告诉css-loader要开启 CSS 压缩。

## 2. 有哪些常见的Loader？他们是解决什么问题的？

● file-loader：把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
● url-loader：和 file-loader 类似，但是能在文件很小的情况下以 base64 的方式把文件内容注入到代码中去
● source-map-loader：加载额外的 Source Map 文件，以方便断点调试
● image-loader：加载并且压缩图片文件
● babel-loader：把 ES6 转换成 ES5
● css-loader：加载 CSS，支持模块化、压缩、文件导入等特性
● style-loader：把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS。
● eslint-loader：通过 ESLint 检查 JavaScript 代码

## 3. Webpack 的 Loader 是什么？

Webpack 只能理解 JavaScript 和 JSON 文件，这是 Webpack 开箱可用的自带能力。loader 可以让 Webpack 能够去处理其他类型的文件，比如 .scss 和 .ts，并将它们转换为有效的功能离散的 chunk 文件以供应用程序使用，以及被添加到依赖图中，也可将内联图像转换为 data URL。简单来说，loader 可以将一段代码转换成另一端代码，通常用来将一段特殊代码转换成一段浏览器可识别的代码。
loader从下到上地取值(evaluate)/执行(execute)，也就是是从后往前执行。在下面的示例中，从 ts-loader开始执行，然后继续执行 css-loader，最后以 raw-loader 为结束。loader 有两个属性：test，正则表达式，用于识别出哪些文件会被转换，use 定义在进行转换时应该使用哪个 loader，可以是字符串、数组和对象。

# 三、 plugin

## 1. Plugin（插件）的作用是什么？

通过安装和配置第三方的插件，可以拓展 webpack 的能力，从而让 webpack 用起来更方便。最常用的webpack 插件有如下两个：
① webpack-dev-server
类似于 node.js 阶段用到的 nodemon 工具
每当修改了源代码，webpack 会自动进行项目的打包和构建
② html-webpack-plugin
webpack 中的 HTML 插件（类似于一个模板引擎插件）
可以通过此插件自定制 index.html 页面的内容
webpack-dev-server 可以让 webpack 监听项目源代码的变化，从而进行自动打包构建。
运行如下的命令，即可在项目中安装webpack插件：
npm install webpack-dev-[server@3.11.0](mailto:server@3.11.0) -D

下面进行webpack-dev-server配置，修改package.json -> scripts中的dev命令：
“scripts”:{
“dev”：”webpack serve”，// script 节点下的脚本,可以通过 npm run 执行
}

再次运行npm run dev 命令，重新进行项目的打包，在浏览器中访问[http://localhost:8080](http://localhost:8080/) 地址，查看自动打包效果，注意webpack-dev-server 会启动一个实时打包的http 服务器。

## 2. 有哪些常见的Plugin？他们是解决什么问题的？

● define-plugin：定义环境变量
● commons-chunk-plugin：提取公共代码
● uglifyjs-webpack-plugin：通过UglifyES压缩ES6代码
● purgecss-webpack-plugin：擦除无用css
● happypack：多线程处理打包
● webpack-bundle-analyzer：打包分析
● speed-measure-webpack-plugin：构建速度分析
● html-webpack-plugin：
● 为html文件中引入的外部资源如script、link动态添加每次compile后的hash，防止引用缓存的外部文件问题

## 3. 是否写过Loader和Plugin？描述一下编写loader或plugin的思路？

Loader像一个”翻译官”把读到的源文件内容转义成新的文件内容，并且每个Loader通过链式操作，将源文件一步步翻译成想要的样子。
编写Loader时要遵循单一原则，每个Loader只做一种”转义”工作。 每个Loader的拿到的是源文件内容（source），可以通过返回值的方式将处理后的内容输出，也可以调用this.callback()方法，将内容返回给webpack。 还可以通过 this.async()生成一个callback函数，再用这个callback将处理后的内容输出出去。 此外webpack还为开发者准备了开发loader的工具函数集——loader-utils。
相对于Loader而言，Plugin的编写就灵活了许多。 webpack在运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

## 4. Webpack 的 Plugin 是什么？

plugin是插件的意思，通常是用于对某个现有的架构进行扩展。
webpack中的插件，就是对webpack现有功能的各种扩展，比如打包优化，文 件压缩等等。

## 5. Loader和Plugin的不同？

不同的作用
● Loader直译为”加载器”。Webpack将一切文件视为模块，但是webpack原生是只能解析js文件，如果想将其他文件也打包的话，就会用到loader。 所以Loader的作用是让webpack拥有了加载和解析非JavaScript文件的能力。
● Plugin直译为”插件”。Plugin可以扩展webpack的功能，让webpack具有更多的灵活性。 在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。
不同的用法

1. Loader在module.rules中配置，也就是说他作为模块的解析规则而存在。 类型为数组，每一项都是一个Object，里面描述了对于什么类型的文件（test），使用什么加载(loader)和使用的参数（options）

2. Plugin在plugins中单独配置。 类型为数组，每一项是一个plugin的实例，参数都通过构造函数传入。

   ## 6. 是否写过Loader和Plugin？描述一下编写loader或plugin的思路？

   Loader像一个”翻译官”把读到的源文件内容转义成新的文件内容，并且每个Loader通过链式操作，将源文件一步步翻译成想要的样子。

   编写Loader时要遵循单一原则，每个Loader只做一种”转义”工作。 每个Loader的拿到的是源文件内容（source），可以通过返回值的方式将处理后的内容输出，也可以调用this.callback()方法，将内容返回给webpack。 还可以通过 this.async()生成一个callback函数，再用这个callback将处理后的内容输出出去。 此外webpack还为开发者准备了开发loader的工具函数集——loader-utils。

   相对于Loader而言，Plugin的编写就灵活了许多。 webpack在运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

# 四、 优化

## 1、 如何提高webpack的构建速度？

1. 多入口情况下，使用CommonsChunkPlugin来提取公共代码
2. 通过externals配置来提取常用库
   利用DllPlugin和DllReferencePlugin预编译资源模块 通过DllPlugin来对那些我们引用但是绝对不会修改的npm包来进行预编译，再通过DllReferencePlugin将预编译的模块加载进来。
3. 使用Happypack 实现多线程加速编译
4. 使用webpack-uglify-parallel来提升uglifyPlugin的压缩速度。 原理上webpack-uglify-parallel采用了多核并行压缩来提升压缩速度
5. 使用Tree-shaking和Scope Hoisting来剔除多余代码

## 2、 如何利用webpack来优化前端性能？（提高性能和体验）!

前端性能优化方案都有哪些？ - 知乎 (zhihu.com)
(30条消息) 前端性能优化的方法_万般皆是你的博客-CSDN博客
用webpack优化前端性能是指优化webpack的输出结果，让打包的最终结果在浏览器运行快速高效。
● 压缩代码。删除多余的代码、注释、简化代码的写法等等方式。可以利用webpack的UglifyJsPlugin和ParallelUglifyPlugin来压缩JS文件， 利用cssnano（css-loader?minimize）来压缩css
● 利用CDN加速。在构建过程中，将引用的静态资源路径修改为CDN上对应的路径。可以利用webpack对于output参数和各loader的publicPath参数来修改资源路径
● 删除死代码（Tree Shaking）。将代码中永远不会走到的片段删除掉。可以通过在启动webpack时追加参数–optimize-minimize来实现
● 提取公共代码。
● 异步组件
● 异步图片
● 配置webpack对小图片打包成base64字符 减少io请求

## 3、 怎么配置单页应用？怎么配置多页应用？

单页应用可以理解为webpack的标准模式，直接在entry中指定单页应用的入口即可，这里不再赘述
多页应用的话，可以使用webpack的 AutoWebPlugin来完成简单自动化的构建，但是前提是项目的目录结构必须遵守他预设的规范。

## 4、 如何提升webpack的运行速度(开发环境) ,有哪些策略？

多入口情况下，使用CommonsChunkPlugin来提取公共代码
通过externals配置来提取常用库
利用DllPlugin和DllReferencePlugin预编译资源模块 通过DllPlugin来对那些我们引用但是绝对不会修改的npm包来进行预编译，再通过DllReferencePlugin将预编译的模块加载进来。
使用Happypack 实现多线程加速编译
使用webpack-uglify-parallel来提升uglifyPlugin的压缩速度。 原理上webpack-uglify-parallel采用了多核并行压缩来提升压缩速度
使用Tree-shaking和Scope Hoisting来剔除多余代码
● JS代码压缩
● terser是一个JavaScript的解释、绞肉机、压缩机的工具集，可以帮助我们压缩、丑化我们的代码，让bundle更小。在production模式下，webpack 默认就是使用 TerserPlugin 来处理我们的代码的。如果想要自定义配置它，配置方法如下。
● TerserPlugin常用的属性如下： - extractComments：默认值为true，表示会将注释抽取到一个单独的文件中，开发阶段，我们可设置为 false ，不保留注释 - parallel：使用多进程并发运行提高构建的速度，默认值是true，并发运行的默认数量： os.cpus().length - 1 - terserOptions：设置我们的terser相关的配置： compress：设置压缩相关的选项，mangle：设置丑化相关的选项，可以直接设置为true mangle：设置丑化相关的选项，可以直接设置为true toplevel：底层变量是否进行转换keep_classnames：保留类的名称 keep_fnames：保留函数的名称
● 代码压缩
● cssCSS压缩通常用于去除无用的空格等，不过因为很难去修改选择器、属性的名称、值等，所以我们可以使用另外一个插件：css-minimizer-webpack-plugin
● Html文件代码压缩
● 文件大小压缩
● 对文件的大小进行压缩，可以有效减少http传输过程中宽带的损耗，文件压缩需要用到 compression-webpack-plugin插件
● 图片压缩
● 如果我们对bundle包进行分析，会发现图片等多媒体文件的大小是远远要比 js、css 文件要大的，所以图片压缩在打包方面也是很重要的

● Tree Shaking
● Tree Shaking 是一个术语，在计算机中表示消除死代码，依赖于ES Module的静态语法分析。在webpack实现Trss shaking有两种不同的方案： - usedExports：通过标记某些函数是否被使用，之后通过Terser来进行优化的 - sideEffects：跳过整个模块/文件，直接查看该文件是否有副作用
● usedExports的配置方法很简单，只需要将usedExports设为true即可，如下。而sideEffects则用于告知webpack compiler在编译时哪些模块有副作用，配置方法是在package.json中设置sideEffects属性。如果sideEffects设置为false，就是告知webpack可以安全的删除未用到的exports，如果有些文件需要保留，可以设置为数组的形式。
● 代码分离
■ 默认情况下，所有的JavaScript代码（业务代码、第三方依赖、暂时没有用到的模块）在首页全部都加载，就会影响首页的加载速度。如果可以分出出更小的bundle，以及控制资源加载优先级，从而优化加载性能。
■ 代码分离可以通过splitChunksPlugin来实现，该插件webpack已经默认安装和集成，只需要配置即可。
■ splitChunks有如下几个属性： - Chunks：对同步代码还是异步代码进行处理 - minSize： 拆分包的大小, 至少为minSize，如何包的大小不超过minSize，这个包不会拆分 - maxSize： 将大于maxSize的包，拆分为不小于minSize的包 - minChunks：被引入的次数，默认是1
● 内联 chunk
■ 可以通过InlineChunkHtmlPlugin插件将一些chunk的模块内联到html，如runtime的代码（对模块进行解析、加载、模块信息相关的代码），代码量并不大但是必须加载的，比如：总结一下，Webpack对前端性能的优化，主要是通过文件体积大小入手，主要的措施有分包、减少Http请求次数等。

用webpack优化前端性能是指优化webpack的输出结果，让打包的最终结果在浏览器运行快速高效。
1、压缩代码:删除多余的代码、注释、简化代码的写法等等方式。可以利用webpack的 UglifyJsPlugin 和 ParallelUglifyPlugin 来压缩JS文件， 利用 cssnano （css-loader? minimize）来压缩css
2、利用CDN加速: 在构建过程中，将引用的静态资源路径修改为CDN上对应的路径。可以利用 webpack对于 output 参数和各loader的 publicPath 参数来修改资源路径
3、Tree Shaking: 将代码中永远不会走到的片段删除掉。可以通过在启动webpack时追加参数 — optimize-minimize 来实现
4、Code Splitting: 将代码按路由维度或者组件分块(chunk),这样做到按需加载,同时可以充分利用浏览 器缓存
5、提取公共第三方库: SplitChunksPlugin插件来进行公共模块抽取,利用浏览器缓存可以长期缓存这 些无需频繁变动的公共代码

## 5、 npm打包时需要注意哪些？如何利用webpack来更好的构建？

● Npm是目前最大的 JavaScript 模块仓库，里面有来自全世界开发者上传的可复用模块。你可能只是JS模块的使用者，但是有些情况你也会去选择上传自己开发的模块。关于NPM模块上传的方法可以去官网上进行学习，这里只讲解如何利用webpack来构建。
● NPM模块需要注意以下问题：要支持CommonJS模块化规范，所以要求打包后的最后结果也遵守该规则。Npm模块使用者的环境是不确定的，很有可能并不支持ES6，所以打包的最后结果应该是采用ES5编写的。并且如果ES5是经过转换的，请最好连同SourceMap一同上传。Npm包大小应该是尽量小（有些仓库会限制包大小）发布的模块不能将依赖的模块也一同打包，应该让用户选择性的去自行安装。这样可以避免模块应用者再次打包时出现底层模块被重复打包的情况。UI组件类的模块应该将依赖的其它资源文件，例如.css文件也需要包含在发布的模块里。

## 6、 什么是长缓存？在webpack中如何做到长缓存优化？

● 浏览器在用户访问页面的时候，为了加快加载速度，会对用户访问的静态资源进行存储，但是每一次代码升级或者更新，都需要浏览器去下载新的代码，最方便和最简单的更新方式就是引入新的文件名称。
● 在webpack中，可以在output给出输出的文件制定chunkhash，并且分离经常更新的代码和框架代码，通过NameModulesPlugin或者HashedModulesPlugin使再次打包文件名不变。

## 7、 在项目中tree-shaking摇树不是很干净，有什么解决方案？

在webpack.config.js中通过
来进行tree-shaking 但是单单指定这一个配置 不是很干净
有些模块导入，只要被引入，
就会对应用程序产生重要的影响。一个很好的例子就是全局样式表，或者设置全局配
置的JavaScript 文件。
如何告诉 Webpack 你的代码无副作用，可以通过 package.json 有一个特殊的属性
sideEffects，就是为此而存在的。
它有三个可能的值：
● true：如果不指定其他值的话。这意味着所有的文件都有副作用，也就是没有一个文件
可以 tree-shaking。
● false： 告诉 Webpack 没有文件有副作用，所有文件都可以 tree-shaking。
● 数组[…]：是文件路径数组。它告诉 webpack，除了数组中包含的文件外，你的任何文件
都没有副作用。因此，除了指定的文件之外，其他文件都可以安全地进行 tree-shaking。

## 8、 怎么提高webpack的打包效率？

(29条消息) 提高webpack的打包速度方法*孙德海想进阿里的博客-CSDN博客*提升webpack打包速度
如何提高webpack的构建速度 - 简书 (jianshu.com)
开发环境优化

1. 开启热模块替换（HMR）

2. 使用 source-map 进行源代码映射

3. 将只需要被loader执行一次的规则放到 oneOf 里面去
   生产环境优化

4. 对资源进行缓存

5. 使用tree shaking（树摇）

6. 使用code split 进行代码分割

7. 文件懒加载和预加载

8. 多进程打包

9. 使用PWA（离线加载）

10. 使用externals 忽略某些包，然后通过cdn引入

11. 使用dll 技术对某些库（第三方库）进行单独打包

    ## 9、 按需加载的原理

    ● 使用符合 ECMAScript 提案 的 import() 语法

    ● 使用 webpack 特定的 require.ensure

    ## 10、 预获取/预加载模块

    Webpack v4.6.0+ 增加了对预获取和预加载的支持。

    在声明 import 时，使用下面这些内置指令，可以让 webpack 输出 “resource hint(资源提示)”，来告知浏览器

    ● prefetch(预获取)：将来某些导航下可能需要的资源

    ● preload(预加载)：当前导航下可能需要资源

    添加第二句魔法注释： webpackPrefetch: true

    告诉 webpack 执行预获取。这会生成 <link rel="prefetch" href="math.js">

    ```
    并追加到页面头部，指示着浏览器在闲置时间预取 math.js 文件。
    ```

    ## 11、 懒加载

    懒加载或者按需加载，是一种很好的优化网页或应用的方式。这种方式实际上是先把

    你的代码在一些逻辑断点处分离开，然后在一些代码块中完成某些操作后，立即引用

    或即将引用另外一些新的代码块。这样加快了应用的初始加载速度，减轻了它的总体

    体积，因为某些代码块可能永远不会被加载。

    创建一个 math.js 文件，在主页面中通过点击按钮调用其中的函数：

    button.addEventListener(‘click’, () => {

    import(/

     webpackChunkName: ‘math’ 

    / ‘./math.js’).then(({ add

    }) => {

    console.log(add(4, 5))

    })

    })

这里有句注释，我们把它称为 webpack 魔法注释： webpackChunkName: ‘math’ ,告诉webpack打包生成的文件名为 math 。
第一次加载完页面， math.bundle.js 不会加载，当点击按钮后，才加载
math.bundle.js 文件。

1. 什么是Tree-sharking?
   Tree是树，sharking是摇晃的意思。那么树摇晃的时候，肯定会’摇’下来一些无用的叶子。从编程的角度思考，如果假设我们的代码是一棵树（Tree），那么摇下来的无用的的叶子是什么呢？当然是无用的代码啦，他有个专业的术语，叫做dead-code（死码）
   指打包中去除那些引入了但在代码中没用到的死代码。在wepack中js treeshaking通过UglifyJsPlugin来进行，css中通过purify-CSS来进行。

# 五、 构建流程

## 1. webpack的构建流程是什么?从读取配置到输出文件这个过程尽量说全

Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

1. 初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数；
2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译；
3. 确定入口：根据配置中的 entry 找出所有的入口文件；
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
5. 完成模块编译：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。
   在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

## 2. 解决ESLint报错常用的五种方案

（1）Strings must use singlequote quotes
表示变量使用了双引号，把表示变量的双引号改为单引号即可。
（2）Expected to be enclosed by double quotes (vue/html-quotes)
这个报错代表让你要将单引号改为双引号
（3）Trailing spaces not allowed no-trailing-spaces
代表有的地方空格多余，比如标签结尾处，只要删除多余空格即可
（4）Unexpected tab character’
面意思理解呢就是意想不到的制表符，当时出现的时候就是我习惯的使用Tab键去打空格，但是eslint默认不认可Tab，所以解决方法很简单：
在eslint的配置文件中（.eslintrc）rules项中添加一行：“no-tabs”:“off”。如下：
（5）‘expected indentation of 2 spaces but found 1 tab’
字面意思就是预期缩进2个空格，但找到1个Tab。说实话，我一开始找了半天，没发现原因，后来想到可能是eslint不认可tab开头，因此我找到了我使用的编辑器VSCord的设置，添加了相应的文字：

大概的意思就是在格式话保存的时候按照1tab=2space的计算量将tab替换成space，这样就不会有问题了。
（6）Unexpected trailing comma. (comma-dangle)
字面意思是尾随了一个多余的逗号
（7）There should be no space before this paren space-in-parens
结尾有多余的空格，去掉就好。

## 3. 什么是模块热更新？有什么优点？

HMR即Hot Module Replacement是指当你对代码修改并保存后，webpack将会对代码进行重新打包，并将改动的模块发送到浏览器端，浏览器用新的模块替换掉旧的模块，去实现局部更新页面而非整体刷新页面。
借助webpack.HotModuleReplacementPlugin()，devServer开启hot
场景1：实现只刷新css，不影响js场景2：js中实现热更新，只更新指定js模块
优点：
在开发阶段，可以提高开发效率，不用手动重新编译即可看到最新效果，webpack服务会通知页面驱动试图进行更新，不用在浏览器手动进行刷新页面

## 4. Webpack Proxy工作原理

4.1 代理
在项目开发中不可避免会遇到跨越问题，Webpack中的Proxy就是解决前端跨域的方法之一。所谓代理，指的是在接收客户端发送的请求后转发给其他服务器的行为，webpack中提供服务器的工具为webpack-dev-server。
4.1.1 webpack-dev-server
webpack-dev-server是 webpack 官方推出的一款开发工具，将自动编译和自动刷新浏览器等一系列对开发友好的功能全部集成在了一起。同时，为了提高开发者日常的开发效率，只适用在开发阶段。在webpack配置对象属性中配置代理的代码如下：
// ./webpack.config.js
const path = require(‘path’)

module.exports = {
// …
devServer: {
contentBase: path.join(__dirname, ‘dist’),
compress: true,
port: 9000,
proxy: {
‘/api’: {
target: ‘[https://api.github.com](https://api.github.com/)‘
}
}
// …
}
}

其中，devServetr里面proxy则是关于代理的配置，该属性为对象的形式，对象中每一个属性就是一个代理的规则匹配。
属性的名称是需要被代理的请求路径前缀，一般为了辨别都会设置前缀为 /api，值为对应的代理匹配规则，对应如下： - target：表示的是代理到的目标地址。 - pathRewrite：默认情况下，我们的 /api-hy 也会被写入到URL中，如果希望删除，可以使用pathRewrite。 - secure：默认情况下不接收转发到https的服务器上，如果希望支持，可以设置为false。 - changeOrigin：它表示是否更新代理后请求的 headers 中host地址。
4.2 原理
proxy工作原理实质上是利用http-proxy-middleware 这个http代理中间件，实现请求转发给其他服务器。比如下面的例子：
const express = require(‘express’);
const proxy = require(‘http-proxy-middleware’);
const app = express();
app.use(‘/api’, proxy({target: ‘[http://www.example.org](http://www.example.org/)‘, changeOrigin: true}));
app.listen(3000);
// http://localhost:3000/api/foo/bar -> http://www.example.org/api/foo/bar

在上面的例子中，本地地址为http://localhost:3000，该浏览器发送一个前缀带有/api标识的请求到服务端获取数据，但响应这个请求的服务器只是将请求转发到另一台服务器中。
4.3 跨域
在开发阶段， webpack-dev-server 会启动一个本地开发服务器，所以我们的应用在开发阶段是独立运行在 localhost 的一个端口上，而后端服务又是运行在另外一个地址上。所以在开发阶段中，由于浏览器同源策略的原因，当本地访问后端就会出现跨域请求的问题。
解决这种问题时，只需要设置webpack proxy代理即可。当本地发送请求的时候，代理服务器响应该请求，并将请求转发到目标服务器，目标服务器响应数据后再将数据返回给代理服务器，最终再由代理服务器将数据响应给本地，原理图如下：

在代理服务器传递数据给本地浏览器的过程中，两者同源，并不存在跨域行为，这时候浏览器就能

## 5. webpack-dev-server 和 http服务器的区别

webpack-dev-server使用内存来存储webpack开发环境下的打包文件，并且可以使用模块热更新，比传统的http服务对开发更加有效。提高Webpack的构建速度

## 6. webpack的热更新原理是怎样的？

1. Webpack 通过 Watch 模式可以侦听文件的变化，当文件发生改变时，会根据配置进行重新编译（Compile），并将编译后的代码保存在内存中。
2. webpack-dev-server 也会对文件变化进行监控（需要配置 devServer.watchContentBase = true），但不会进行重新编译，而是监听这些配置文件中静态文件的变化，变化后会通知浏览器进行直接刷新，而不是 HMR。
3. 在浏览器和服务端之间有一个通过 SocketJs 建立的 websocket 长连接。webpack-dev-server 会将 Webpack 编译打包时的各个阶段的状态信息和 hash 值一并告知 webpack-dev-server/client（位于浏览器端）。
4. 但是 webpack-dev-server/client 并不能够请求更新的代码，而是把这些工作交给了 webpack/hot/dev-server，webpack/hot/dev-server 的工作就是根据 webpack-dev-server/client 传来的信息以及 dev-server 的配置决定是刷新浏览器还是 HMR。
5. HotModuleReplacement.runtime 是客户端 HMR 的中枢，它接收到 webpack/hot/dev-server 传递的新模块的 hash 值，通过 JsonpMainTemplate.runtime 向 webpack-dev-server 发送 Ajax 请求获取到返回的 Json，该 Json 包含了所有要更新的模块的 hash 值，之后通过 Jsonp 请求，获取到最新的模块代码。
6. 接下来，HotModulePlugin 将会对新旧模块进行对比，决定是否更新模块，在决定更新模块后，检查模块之间的依赖关系，更新模块的同时更新模块间的依赖引用。
7. 如果 HMR 失败，则通过刷新浏览器来获取最新打包代码。

# 六、 基本配置

## 1. entry的值有几种数据类型？

字符串（单入口）
数组（多入口）
object(object中的key在webpack里相当于此入口的name)

## 2. webpack如何配置多入口文件？

● 配置多入口entry
○
● 配置出口output
○ filename 中的 [name] 对应入口的文件名；
○ contentHash 会命中缓存，提高性能；
○
● 配置插件htmlWebpackPlugin，生成多页面
○ htmlWebpackPlugin 插件会生成页面。
○ chunk 为代码块，默认引入 entry 中所有文件。
○

## 3. webpack.config.js能不能做拆分？

可以
将webpack.config.js 配置文件进行分开。分成三个配置文件，如下：

1. webpack.base.config.js：两个环境公共的部分

2. webpack.dev.config.js：开发环境独有的配置

   ```
    webpack.prod.config.js`：生产环境独有的配置
   ```

3. output的对象中的常见属性有哪些?

   output 是一个 object 对象，其中包含一系列的配置项，其中比较重要的是 filename 和 path。

   ● output.filename：配置输出文件的名称，指定一个 string 类型的值。如果只有一个输出文件，则可以把它写成静态不变的。

   ● output.path ：配置输出文件存放在本地的目录，是一个 string 类型的绝对路径。通常通过 Node.js 的 path 模块去获取绝对路径。

   在 webpack.config.js 配置文件中，一个 entry 对应一个 output。

   # 七、 区别

   ## 1. babel-runtime和babel-polyfill的区别

   ● babel-polyfill

   ○ 原理是当运行环境中并没有实现的一些方法，babel-polyfill 会给其做兼容。 但是这样做也有一个缺点，就是会污染全局变量，而且项目打包以后体积会增大很多，因为把整个依赖包也搭了进去。所以并不推荐在一些方法类库中去使用。

   ○ babel-polyfill 可以用来转码，因为 babel-polyfill 是直接在原型链上增加方法。

   ● babel-runtime

   ○ 为了不污染全局对象和内置的对象原型，但是又想体验使用新鲜语法的快感。就可以配合使用babel-runtime和babel-plugin-transform-runtime。 比如当前运行环境不支持promise，可以通过引入babel-runtime/core-js/promise来获取promise， 或者通过babel-plugin-transform-runtime自动重写你的promise。也许有人会奇怪，为什么会有两个runtime插件，其实是有历史原因的：刚开始开始只有babel-runtime插件，但是用起来很不方便，在代码中直接引入helper 函数，意味着不能共享，造成最终打包出来的文件里有很多重复的helper代码。所以，Babel又开发了babel-plugin-transform-runtime，这个模块会将我们的代码重写，如将Promise重写成_Promise（只是打比方），然后引入_Promise helper函数。这样就避免了重复打包代码和手动引入模块的痛苦。

   ○ babel-runtime 不能转码实例方法

## 2. 除了Webpack外，你还了解哪些模块管理工具

webpack:
就目前而言，webpack已是最常用的打包工具，webpack是基于入口的。webpack会自动地递归解析入口所需要加载的所有资源文件，然后用不同的Loader来处理不同的文件，用Plugin来扩展webpack功能。
gulp：
gulp是一个前端自动化构建工具，强调的是前端开发的工作流程，可以通过配置一系列的task，第一task处理的事情（如代码压缩，合并，编译以及浏览器实时更新等）。然后定义这些执行顺序，来让glup执行这些task，从而构建项目的整个开发流程。自动化构建工具并不能把所有的模块打包到一起，也不能构建不同模块之间的依赖关系。

grunt：
是基于任务和流（Task、Stream）的。类似jQuery，找到一个（或一类）文件，对其做一系列链式操作，更新流上的数据， 整条链式操作构成了一个任务，多个任务就构成了整个web的构建流程。
Rollup

1. Rollup 是一款 ES Modules 打包器， 从作用上来看，Rollup 与 Webpack 非常类似。不过相比于 Webpack，Rollup要小巧的多
   现在很多我们熟知的库都都使用它进行打包，比如：Vue、React和three.js等
   Parcel
   Parcel ，是一款完全零配置的前端打包器，它提供了 “傻瓜式” 的使用体验，只需了解简单的命令，就能构建前端应用程序
   Parcel 跟 Webpack 一样都支持以任意类型文件作为打包入口，但建议使用 HTML文件作为入口，该HTML文件像平时一样正常编写代码、引用资源。
   模块化是一种处理复杂系统分解为更好的可管理模块的方式。可以用来分割、组织和打包应用。每个模块完成一个特定的子功能，所有的模块按某种方法组装起来，成为一个整体。
   在前端领域中，除了Webpack外，比较流行的模块打包工具还包括Rollup、Parcel、snowpack和最近风靡的Vite。
   2.1. Rollup
   Rollup 是一款 ES Modules 打包器，可以将小块代码编译成大块复杂的代码，例如 library 或应用程序。从作用上来看，Rollup 与 Webpack 非常类似。不过相比于 Webpack，Rollup 要小巧的多。现在很多苦都使用它进行打包，比如：Vue、React和three.js等。
   使用之前，可以使用npm install —global rollup 命令进行安装。Rollup 可以通过命令行接口(command line interface)配合可选配置文件(optional configuration file)来调用，或者可以通过 JavaScript API来调用。运行 rollup —help 可以查看可用的选项和参数。
   2.2. Parcel
   Parcel ，是一款完全零配置的前端打包器，它提供了 “傻瓜式” 的使用体验，只需了解简单的命令，就能构建前端应用程序。
   2.3. Vite
   Vite是Vue的作者尤雨溪开发的Web开发构建工具，它是一个基于浏览器原生ES模块导入的开发服务器，在开发环境下，利用浏览器去解析import，在服务器端按需编译返回，完全跳过了打包这个概念，服务器随启随用。同时不仅对Vue文件提供了支持，还支持热更新，而且热更新的速度不会随着模块增多而变慢。
   Vite具有以下特点： - 快速的冷启动 - 即时热模块更新（HMR，Hot Module Replacement） - 真正按需编译
   Vite由两部分组成： - 一个开发服务器，它基于 原生 ES 模块 提供了丰富的内建功能，如速度快到惊人的 [模块热更新HMR。 - 一套构建指令，它使用 Rollup打包你的代码，并且它是预配置的，可以输出用于生产环境的优化过的静态资源。
   Vite在开发阶段可以直接启动开发服务器，不需要进行打包操作，也就意味着不需要分析模块的依赖、不需要编译，因此启动速度非常快。当浏览器请求某个模块的时候，根据需要对模块的内容进行编译，大大缩短了编译时间。工作原理如下图所示。
   在热模块HMR方面，当修改一个模块的时候，仅需让浏览器重新请求该模块即可，无须像Webpack那样需要把该模块的相关依赖模块全部编译一次，因此效率也更高。
2. webpack，rollup，parcel优劣？(了解)
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

```
parcel则是完全开箱可用的，不用配置。
```

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