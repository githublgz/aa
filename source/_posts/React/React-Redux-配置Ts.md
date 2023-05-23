---
title: React-Redux总结-配置Ts
cover: false
top: false
toc: false
mathjax: false
tags:
  - React
categories:
  - 框架
abbrlink: 23375
date: 2020-02-14 18:41:28
author:
img:
coverImg:
password:
summary:
keywords:
---

# 第二部分 结合React实践

## 一、环境配置

### 1.1 初始化项目

- 生成一个目录`ts_react_demo`，输入`npm init -y`初始化项目
- 然后在项目里我们需要一个`.gitignore`来忽略指定目录不传到`git`上
- 进入`.gitignore`输入我们需要忽略的目录，一般是`node_modules`

```
// .gitignore
node_modules
```

### 1.2 安装依赖

> 接下来我们准备下载相应的依赖包，这里需要了解一个概念，就是类型定义文件

#### 1.2.1 类型定义文件

> 因为目前主流的第三方库都是以`javascript`编写的，如果用`typescript`开发，会导致在编译的时候会出现很多找不到类型的提示，那么如果让这些库也能在`ts`中使用呢？

- 类型定义文件(`*.d.ts`)就是能够让编辑器或者插件来检测到第三方库中`js`的静态类型，这个文件是以`.d.ts`结尾- 比如说`react`的类型定义文件：https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react
- 在`typescript2.0`中，是使用`@type`来进行类型定义，当我们使用`@type`进行类型定义，`typescript`会默认查看`./node_modules/@types`文件夹，可以通过这样来安装这个库的定义库`npm install @types/react --save`

#### 1.2.2 相关依赖包

**React相关**

```
- react // react的核心文件
- @types/react // 声明文件
- react-dom // react dom的操作包
- @types/react-dom 
- react-router-dom // react路由包
- @types/react-router-dom
- react-redux
- @types/react-redux
- redux-thunk  // 中间件
- @types/redux-logger
- redux-logger // 中间件
- connected-react-router
## 执行安装依赖包

npm i react react-dom @types/react @types/react-dom react-router-dom @types/react-router-dom react-redux @types/react-redux redux-thunk redux-logger @types/redux-logger connected-react-router -S
```

**webpack相关**

```
- webpack // webpack的核心包
- webpack-cli // webapck的工具包
- webpack-dev-server // webpack的开发服务
- html-webpack-plugin // webpack的插件，可以生成index.html文件
npm i webpack webpack-cli webpack-dev-server html-webpack-plugin -D
```

> 这里的`-D`相当于`--save-dev`的缩写，下载开发环境的依赖包

**typescript相关**

```
- typescript // ts的核心包
- ts-loader // 把ts编译成指定语法比如es5 es6等的工具，有了它，基本不需要babel了，因为它会把我们的代码编译成es5
- source-map-loader // 用于开发环境中调试ts代码
npm i typescript ts-loader source-map-loader -D
```

- 从上面可以看出，基本都是模块和声明文件都是一对对出现的，有一些不是一对对出现，就是因为都集成到一起去了
- 声明文件可以在`node_modules/@types/xx/xx`中找到

### 1.3 Typescript config配置

> 首先我们要生成一个`tsconfig.json`来告诉`ts-loader`怎样去编译这个`ts`代码

```
tsc --init
```

> 会在项目中生成了一个`tsconfig.json`文件，接下来进入这个文件，来修改相关配置

```
// tsconfig.json
{
  // 编译选项
  "compilerOptions": {
    "target": "es5", // 编译成es5语法
    "module": "commonjs", // 模块的类型
    "outDir": "./dist", // 编译后的文件目录
    "sourceMap": true, // 生成sourceMap方便我们在开发过程中调试
    "noImplicitAny": true, // 每个变量都要标明类型
    "jsx": "react", // jsx的版本,使用这个就不需要额外使用babel了，会编译成React.createElement
  },
  // 为了加快整个编译过程，我们指定相应的路径
  "include": [
    "./src/**/*"
  ]
}
```

### 1.4 webpack配置

> 在`./src/`下创建一个`index.html`文件，并且添加``标签

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <div id='app'></div>
</body>
</html>
```

> 在`./`下创建一个`webpack`配置文件`webpack.config.js`

```
// ./webpack.config.js
// 引入webpack
const webpack = require("webpack");
// 引入webpack插件 生成index.html文件
const HtmlWebpackPlugin = require("html-webpack-plugin");
const path = require("path")

// 把模块导出
module.exports = {
  // 以前是jsx，因为我们用typescript写，所以这里后缀是tsx
  entry:"./src/index.tsx",
  // 指定模式为开发模式
  mode:"development",
  // 输出配置
  output:{
    // 输出目录为当前目录下的dist目录
    path:path.resolve(__dirname,'dist'),
    // 输出文件名
    filename:"index.js"
  },
  // 为了方便调试，还要配置一下调试工具
  devtool:"source-map",
  // 解析路径，查找模块的时候使用
  resolve:{
    // 一般写模块不会写后缀，在这里配置好相应的后缀，那么当我们不写后缀时，会按照这个后缀优先查找
    extensions:[".ts",'.tsx','.js','.json']
  },
  // 解析处理模块的转化
  module:{
    // 遵循的规则
    rules:[
      {
        // 如果这个模块是.ts或者.tsx，则会使用ts-loader把代码转成es5
        test:/\.tsx?$/,
        loader:"ts-loader"
      },
      {
        // 使用sourcemap调试
        // enforce:pre表示这个loader要在别的loader执行前执行
        enforce:"pre",
        test:/\.js$/,
        loader:"source-map-loader"
      }
    ]
  },
  // 插件的配置
  plugins:[
    // 这个插件是生成index.html
    new HtmlWebpackPlugin({
      // 以哪个文件为模板，模板路径
      template:"./src/index.html",
      // 编译后的文件名
      filename:"index.html"
    }),
    new webpack.HotModuleReplacementPlugin()
  ],
  // 开发环境服务配置
  devServer:{
    // 启动热更新,当模块、组件有变化，不会刷新整个页面，而是局部刷新
    // 需要和插件webpack.HotModuleReplacementPlugin配合使用
    hot:true, 
    // 静态资源目录
    contentBase:path.resolve(__dirname,'dist')
  }
}
```

> 那么我们怎么运行这个`webpack.config.js`呢？这就需要我们在`package.json`配置一下脚本

- 在`package.json`里的`script`，添加`build`和`dev`的配置

```
{
  "name": "ts_react_demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "webpack",
    "dev":"webpack-dev-server"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@types/react": "^16.7.13",
    "@types/react-dom": "^16.0.11",
    "@types/react-redux": "^6.0.10",
    "@types/react-router-dom": "^4.3.1",
    "connected-react-router": "^5.0.1",
    "react": "^16.6.3",
    "react-dom": "^16.6.3",
    "react-redux": "^6.0.0",
    "react-router-dom": "^4.3.1",
    "redux-logger": "^3.0.6",
    "redux-thunk": "^2.3.0"
  },
  "devDependencies": {
    "html-webpack-plugin": "^3.2.0",
    "source-map-loader": "^0.2.4",
    "ts-loader": "^5.3.1",
    "typescript": "^3.2.1",
    "webpack": "^4.27.1",
    "webpack-cli": "^3.1.2",
    "webpack-dev-server": "^3.1.10"
  }
}
```

- 因为入口文件是`index.tsx`，那么我们在`./src/`下创建一个`index.tsx`，并且在里面写入一段代码，看看`webpack`是否能够正常编译
- 因为我们在`webpack.config.js`中`entry`设置的入口文件是`index.tsx`，并且在`module`中的`rules`会识别到`.tsx`格式的文件，然后执行相应的`ts-loader`

```
// ./src/index.tsx
console.log("hello poetries")
```

- 接下来我们`npm run build`一下，看看能不能正常编译
- 编译成功，我们可以看看`./dist/`下生成了`index.html index.js index.js.map`三个文件
- 那么我们在开发过程中，不会每次都`npm run build`来看修改的结果，那么我们平时开发过程中可以使用`npm run dev`。这样就启动成功了一个`http://localhost:8080/`的服务了。
- 接下来我们看看热更新是否配置正常，在`./src/index.tsx`中新增一个`console.log('hello poetries')`，我们发现浏览器的控制台会自动打印出这一个输出，说明配置正常了

## 二、React组件

### 2.1 写一个计数器组件

> 首先我们在`./src/`下创建一个文件夹`components`，然后在`./src/components/`下创建文件`Counter.tsx`

```
// ./src/components/Counter.tsx
// import React from "react"; // 之前的写法
// 在ts中引入的写法
import * as React from "react";

export default class CounterComponent extends React.Component{
  // 状态state
  state = {
    number:0
  }
  render(){
    return(
      <div>
        <p>{this.state.number}</p>
        <button onClick={()=>this.setState({number:this.state.number + 1})}>+</button>
      </div>
    )
  }
}
```

> 我们发现，其实除了引入`import * as React from "react"`以外，其余的和之前的写法没什么不同。

- 接下来我们到`./src/index.tsx`中把这个组件导进来

```
// ./src/index.tsx
import * as React from "react";
import * as ReactDom from "react-dom";
import CounterComponent from "./components/Counter";
// 把我们的CounterComponent组件渲染到id为app的标签内
ReactDom.render(<CounterComponent />,document.getElementById("app"))
```

> 这样我们就把这个组件引进来了，接下来我们看下是否能够成功跑起来

![image.png](https://upload-images.jianshu.io/upload_images/1480597-86ad083c5149a72e.png)

> 到目前为止，感觉用`ts`写`react`还是跟以前差不多，没什么区别，要记住，`ts`最大的特点就是类型检查，可以检验属性的状态类型

假设我们需要在`./src/index.tsx`中给``传一个属性`name`，而`CounterComponent`组件需要对这个传入的`name`进行类型校验，比如说只允许传字符串

- `./src/index.tsx`中修改一下

```
ReactDom.render(<CounterComponent name="poetries" />,document.getElementById("app"))
```

> 然后需要在`./src/components/Counter.tsx`中写一个接口来对这个`name`进行类型校验

```
// import React from "react"; // 之前的写法
// 在ts中引入的写法
import * as React from "react";

// 写一个接口对name进行类型校验
// 如果我们不写校验的话，在外部传name进来会报错的
interface IProps{
    name:string,
}

// 我们还可以用接口约束state的状态
interface IState{
    number: number
}

// 把接口约束的规则写在这里
// 如果传入的name不符合类型会报错
// 如果state的number属性不符合类型也会报错
export default class CounterComponent extends React.Component<IProps,IState>{
  // 状态state
  state = {
    number:0
  }
  render(){
    return(
        <div>
        <p>{this.state.number}</p>
        <p>{this.props.name}</p>
        <button onClick={()=>this.setState({number:this.state.number + 1})}>+</button>
      </div>
    )
  }
}
```

### 2.2 结合Redux使用

#### 2.2.1 基础使用

- 上面`state`中的`number`就不放在组件里了，我们放到`redux`中，接下来我们使用`redux`
- 首先在`./src/`创建`store`目录，然后在`./src/store/`创建一个文件`index.tsx`

```
// .src/store/index.tsx
import { createStore } from "redux";

// 引入reducers
import reducers from "./reducers";

// 接着创建仓库
let store = createStore(reducers);

// 导出store仓库
export default store;
```

- 然后我们需要创建一个`reducers`，在`./src/store/`创建一个目录`reducers`，该目录下再创建一个文件`index.tsx`。
- 但是我们还需要对`reducers`中的函数参数进行类型校验，而且这个类型校验很多地方需要复用，那么我们需要把这个类型校验单独抽离出一个文件。
- 那么我们需要在`./src/`下创建一个`types`目录，该目录下创建一个文件`index.tsx`

```
// ./src/types/index.tsx
// 导出一个接口
export interface Store{
  // 我们需要约束的属性和类型
  number:number
}
```

> 回到`./src/store/reducers/index.tsx`

```
// 导入类型校验的接口
// 用来约束state的
import { Store } from "../../types/index"
// 我们需要给number赋予默认值
let initState:Store = { number:0 }
// 把接口写在state:Store
export default function (state:Store=initState,action) {
  // 拿到老的状态state和新的状态action
  // action是一个动作行为，而这个动作行为，在计数器中是具备 加 或 减 两个功能
}
```

- 上面这段代码暂时先这样，因为需要用到`action`，我们现在去配置一下`action`相关的，首先我们在`./src/store`下创建一个`actions`目录，并且在该目录下创建文件`counter.tsx`
- 因为配置`./src/store/actions/counter.tsx`会用到动作类型，而这个动作类型是属于常量，为了更加规范我们的代码，我们在`./src/store/`下创建一个`action-types.tsx`，里面写相应常量

```
// ./src/store/action-types.tsx
export const ADD = "ADD";
export const SUBTRACT = "SUBTRACT";
```

> 回到`./src/store/actions/counter.tsx`

```
// ./src/store/actions/counter.tsx
import * as types from "../action-types";
export default {
  add(){
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.ADD}
  },
  subtract(){
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.SUBTRACT}
  }
}
```

> 我们可以想一下，上面`return { type:types.ADD }`实际上是返回一个`action`对象，将来使用的时候，是会传到`./src/store/reducers/index.tsx`的`action`中，那么我们怎么定义这个`action`的结构呢？

```
// ./src/store/actions/counter.tsx
import * as types from "../action-types";

// 定义两个接口，分别约束add和subtract的type类型
export interface Add{
  type:typeof types.ADD
}
export interface Subtract{
  type:typeof types.SUBTRACT
}

// 再导出一个type
// type是用来给类型起别名的
// 这个actions里是一个对象，会有很多函数，每个函数都会返回一个action
// 而 ./store/reducers/index.tsx中的action会是下面某一个函数的返回值

export type Action = Add | Subtract

// 把上面定义好的接口作用于下面
// 约束返回值的类型
export default {
  add():Add{
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.ADD}
  },
  subtract():Subtract{
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.SUBTRACT}
  }
}
```

> 接着我们回到`./store/reducers/index.tsx`

经过上面一系列的配置，我们可以给`action`使用相应的接口约束了并且根据不同的`action`动作行为来进行不同的处理

```
// ./store/reducers/index.tsx
// 导入类型校验的接口
// 用来约束state的
import { Store } from "../../types/index"

// 导入约束action的接口
import { Action } from "../actions/counter"

// 引入action动作行为的常量
import * as types from "../action-types"

// 我们需要给number赋予默认值
let initState:Store = { number:0 }

// 把接口写在state:Store
export default function (state:Store=initState,action:Action) {
  // 拿到老的状态state和新的状态action
  // action是一个动作行为，而这个动作行为，在计数器中是具备 加 或 减 两个功能
  // 判断action的行为类型
  switch (action.type) {
    case types.ADD:
        // 当action动作行为是ADD的时候，给number加1
        return { number:state.number + 1 }
      break;
    case types.SUBTRACT:
        // 当action动作行为是SUBTRACT的时候，给number减1
        return { number:state.number - 1 }
      break;
    default:
        // 当没有匹配到则返回原本的state
        return state
      break;
  }
}
```

> 接下来，我们怎么样把组件和仓库建立起关系呢

首先进入`./src/index.tsx`

```
// ./src/index.tsx
import * as React from "react";
import * as ReactDom from "react-dom";

// 引入redux这个库的Provider组件
import { Provider } from "react-redux";

// 引入仓库
import store from './store'

import CounterComponent from "./components/Counter";

// 用Provider包裹CounterComponent组件
// 并且把store传给Provider
// 这样Provider可以向它的子组件提供store
ReactDom.render((
  <Provider store={store}>
    <CounterComponent name="poetries"/>
  </Provider>
),document.getElementById("app"))
```

> 我们到组件内部建立连接，`./src/components/Counter.tsx`

```
// import React from "react"; // 之前的写法
// 在ts中引入的写法
import * as React from "react";

// 引入connect，让组件和仓库建立连接
import { connect } from "react-redux";

// 引入actions，用于传给connect
import actions from "../store/actions/counter";

// 引入接口约束
import { Store } from "../types";

// 接口约束
interface IProps{
  number:number,

  name:string, //如果我们不写校验的话，在外部传name进来会报错的

  // add是一个函数
  add:any,

  // subtract是一个函数
  subtract:any
}

// 我们还可以用接口约束state的状态
interface IState{
    number: number
}

// 把接口约束的规则写在这里
// 如果传入的name不符合类型会报错
// 如果state的number属性不符合类型也会报错
class CounterComponent extends React.Component<IProps,IState>{
  // 状态state
  state = {
    number:0
  }
  render(){
    // 利用解构赋值取出
    // 这里比如和IProps保持一致，不对应则会报错，因为接口约束了必须这样
    let { number,add,subtract } = this.props

    return(
        <div>
            <h1>{this.props.name}</h1>
            <button onClick={add}>+</button><br />
            <button onClick={subtract}>-</button>  
            <p>{number}</p>
      </div>
    )
  }
}

// 这个connect需要执行两次，第二次需要我们把这个组件CounterComponent传进去
// connect第一次执行，需要两个参数，

// 需要传给connect的函数
let mapStateToProps = function (state:Store) {
    return state
}
  
export default connect(
    mapStateToProps,
    actions
)(CounterComponent);
```

这时候看到成功执行了

![image.png](https://upload-images.jianshu.io/upload_images/1480597-32fc82d43dadb063.png)

- 其实搞来搞去，跟原来的写法差不多，主要就是`ts`会进行类型检查。
- 如果对`number`进行异步修改，该怎么处理？这就需要我们用到`redux-thunk`

> 接着我们回到`./src/store/index.tsx`

```
// 需要使用到thunk，所以引入中间件applyMiddleware
import { createStore, applyMiddleware } from "redux";

// 引入reducers
import reducers from "./reducers";

// 引入redux-thunk，处理异步
// 现在主流处理异步的是saga和thunk
import thunk from "redux-thunk";

// 引入日志
import logger from "redux-logger";

// 接着创建仓库和中间件
let store = createStore(reducers, applyMiddleware(thunk,logger));

// 导出store仓库
export default store;
```

> 接着我们回来`./src/store/actions`，新增一个异步的动作行为

```
// ./src/store/actions/counter.tsx
import * as types from "../action-types";

// 定义两个接口，分别约束add和subtract的type类型
export interface Add{
  type:typeof types.ADD
}

export interface Subtract{
  type:typeof types.SUBTRACT
}
// 再导出一个type
// type是用来给类型起别名的
// 这个actions里是一个对象，会有很多函数，每个函数都会返回一个action
// 而 ./store/reducers/index.tsx中的action会是下面某一个函数的返回值

export type Action = Add | Subtract

// 把上面定义好的接口作用于下面
// 约束返回值的类型
export default {
  add():Add{
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.ADD}
  },
  subtract():Subtract{
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.SUBTRACT}
  },
  // 一秒后才执行这个行为
  // ++
  addAsync():any{
    return function (dispatch:any,getState:any) {
      setTimeout(function(){
        // 当1秒过后，会执行dispatch，派发出去，然后改变仓库的状态
        dispatch({type:types.ADD})
      }, 1000);
    }
  }
}
```

> 到`./src/components/Counter.tsx`组件内，使用这个异步

#### 2.2.2 合并reducers

> 假如我们的项目里面，有两个计数器，而且它俩是完全没有关系的，状态也是完全独立的，这个时候就需要用到合并`reducers`了

- 首先我们新增`action`的动作行为类型，在`./src/store/action-types.tsx`
- 然后修改接口文件，`./src/types/index.tsx`
- 然后把`./src/store/actions/counter.tsx`文件拷贝在当前目录并且修改名称为`counter2.tsx`
- 然后把`./src/store/reduces/index.tsx`拷贝并且改名为`counter.tsx`和`counter2.tsx`

> 我们多个`reducer`是通过`combineReducers`方法，进行合并的，因为我们一个项目当中肯定是存在非常多个`reducer`，所以统一在这里处理。

```
// ./src/store/reducers/index.tsc

// 引入合并方法
import { combineReducers } from "redux";

// 引入需要合并的reducer
import counter from "./counter";

// 引入需要合并的reducer
import counter2 from "./counter2";

// 合并
let reducers = combineReducers({
  counter,
  counter2,
});
export default reducers;
```

> 最后修改组件，进入`./src/components/`,其中

到目前为止，我们完成了`reducers`的合并了，那么我们看看效果如何，首先我们给`./src/index.tsc`添加`Counter2`组件，这样的目的是与`Counter`组件完全独立，互不影响，但是又能够最终合并到`readucers`

![img](https://poetries1.gitee.io/img-repo/2019/10/548.png)

### 2.3 路由

#### 2.3.1 基本用法

> 首先进入`./src/index.tsx`导入我们的路由所需要的依赖包

```
// ./src/index.tsx
import * as React from "react";
import * as ReactDom from "react-dom";

// 引入redux这个库的Provider组件
import { Provider } from "react-redux";

// 引入路由
// 路由的容器:HashRouter as Router
// 路由的规格:Route
// Link组件
import { BrowserRouter as Router,Route,Link } from "react-router-dom"

// 引入仓库
import store from './store'

import CounterComponent from "./components/Counter";
import CounterComponent2 from "./components/Counter2";
import Counter from "./components/Counter";

function Home() {
    return <div>home</div>
}

// 用Provider包裹CounterComponent组件
// 并且把store传给Provider
// 这样Provider可以向它的子组件提供store
ReactDom.render((
  <Provider store={store}>
    {/* 路由组件 */}
    <Router>
      {/*  放两个路由规则需要在外层套个React.Fragment */}
        <React.Fragment>
            {/* 增加导航 */}
            <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/counter">Counter</Link></li>
            <li><Link to="/counter2">Counter2</Link></li>
            </ul>
            {/* 当路径为 / 时是home组件 */}
            {/* 为了避免home组件一直渲染，我们可以添加属性exact */}
            <Route exact path="/" component={Home}/>
            <Route path="/counter" component={CounterComponent}/>
            <Route path="/counter2" component={CounterComponent2} />
        </React.Fragment>
        </Router>
  </Provider>
),document.getElementById("app"))
```

![img](https://poetries1.gitee.io/img-repo/2019/10/549.png)

> 但是有个很大的问题，就是我们直接访问`http://localhost:8080/counter`会找不到路由

- 因为我们的是单页面应用，不管路由怎么变更，实际上都是访问`index.html`这个文件，所以当我们访问根路径的时候，能够正常访问，因为`index.html`文件就放在这个目录下，但是当我们通过非根路径的路由访问，则出错了，是因为我们在相应的路径没有这个文件，所以出错了
- 从这一点也可以衍生出一个实战经验，我们平时项目部署上线的时候，会出现这个问题，一般我们都是用`nginx`来把访问的路径都是指向`index.html`文件，这样就能够正常访问了。
- 那么针对目前我们这个情况，我们可以通过修改`webpack`配置，让路由不管怎么访问，都是指向我们制定的`index.html`文件。

> 进入`./webpack.config.js`，在`devServer`的配置对象下新增一些配置

```
// ./webpack.config.js
...

  // 开发环境服务配置
  devServer:{
    // 启动热更新,当模块、组件有变化，不会刷新整个页面，而是局部刷新
    // 需要和插件webpack.HotModuleReplacementPlugin配合使用
    hot:true, 
    // 静态资源目录
    contentBase:path.resolve(__dirname,'dist'),
    // 不管访问什么路径，都重定向到index.html
    historyApiFallback:{
      index:"./index.html"
    }
  }

...
```

> 修改`webpack`配置需要重启服务，然后重启服务，看看浏览器能否正常访问`http://localhost:8080/counter`

#### 2.3.2 同步路由到redux

> 路由的路径，如何同步到仓库当中。以前是用一个叫`react-router-redux`的库，把路由和`redux`结合到一起的，`react-router-redux`挺好用的，但是这个库不再维护了，被废弃了，所以现在推荐使用`connected-react-router`这个库，可以把路由状态映射到仓库当中

> 首先我们在`./src`下创建文件`history.tsx`

假设我有一个需求，就是我不通过`Link`跳转页面，而是通过编程式导航，触发一个动作，然后这个动作会派发出去，而且把路由信息放到`redux`中，供我以后查看。

> 我们进入`./src/store/reducers/index.tsx`

```
// 引入合并方法
import { combineReducers } from "redux";

// 引入需要合并的reducer
import counter from "./counter";

// 引入需要合并的reducer
import counter2 from "./counter2";

// 引入connectRouter
import { connectRouter } from "connected-react-router";
import history from "../../history";

// 合并
let reducers = combineReducers({
  counter,
  counter2,
  // 把history传到connectRouter函数中
  router: connectRouter(history)
});
export default reducers;
```

> 我们进入`./src/store/index.tsx`来添加中间件

```
// 需要使用到thunk，所以引入中间件applyMiddleware
import { createStore, applyMiddleware } from "redux";

// 引入reducers
import reducers from "./reducers";

// 引入redux-thunk，处理异步
// 现在主流处理异步的是saga和thunk
import thunk from "redux-thunk";

// 引入日志
import logger from "redux-logger";

// 引入中间件
import { routerMiddleware } from "connected-react-router";
import history from "../history";

// 接着创建仓库和中间件
let store = createStore(reducers, applyMiddleware(routerMiddleware(history),thunk,logger));

// 导出store仓库
export default store;
```

> 我们进入`./src/store/actions/counter.tsx`加个`goto`方法用来跳转

```
// ./src/store/actions/counter.tsx
import * as types from "../action-types";

// 引入push方法
import { push } from "connected-react-router";

// 定义两个接口，分别约束add和subtract的type类型
export interface Add{
  type:typeof types.ADD
}

export interface Subtract{
  type:typeof types.SUBTRACT
}
// 再导出一个type
// type是用来给类型起别名的
// 这个actions里是一个对象，会有很多函数，每个函数都会返回一个action
// 而 ./store/reducers/index.tsx中的action会是下面某一个函数的返回值

export type Action = Add | Subtract

// 把上面定义好的接口作用于下面
// 约束返回值的类型
export default {
  add():Add{
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.ADD}
  },
  subtract():Subtract{
    // 需要返回一个action对象
    // type为动作的类型
    return { type: types.SUBTRACT}
  },
  // 一秒后才执行这个行为
  addAsync():any{
    return function (dispatch:any,getState:any) {
      setTimeout(function(){
        // 当1秒过后，会执行dispatch，派发出去，然后改变仓库的状态
        dispatch({type:types.ADD})
      }, 1000);
    }
  },
  goto(path:string){
    // 派发一个动作
    // 这个push是connected-react-router里的一个方法
    // 返回一个跳转路径的action
    return push(path)
  }
}
```

> 我们进入`./src/components/Counter.tsx`中加个按钮，当我点击按钮的时候，会向仓库派发`action`，仓库的`action`里有中间件，会把我们这个请求拦截到，然后跳转