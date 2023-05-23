---
title: qiankun微前端
cover: false
top: false
toc: false
mathjax: false
tags:
  - qiankun
categories:
  - 微前端
abbrlink: 23375
date: 2022-04-12 15:28:31
author:
img:
coverImg:
password:
summary:
keywords:

---

微前端其实非常地简单，非常地容易落地，而且也非常不高大上~

那么就来一起看看什么是微前端吧：

## 一.为什么需要微前端?

这里我们通过3W(what,why,how)的方式来讲解什么是微前端：

### 1.What?什么是微前端?


微前端就是将不同的功能按照不同的维度拆分成多个子应用。通过主应用来加载这些子应用。

微前端的核心在于拆, 拆完后再合!

### 2.Why?为什么去使用他?

不同团队间开发同一个应用技术栈不同怎么破？
希望每个团队都可以独立开发，独立部署怎么破？
项目中还需要老的应用代码怎么破？
我们是不是可以将一个应用划分成若干个子应用，再将子应用打包成一个个的lib呢？当路径切换时加载不同的子应用，这样每个子应用都是独立的，技术栈也就不用再做限制了！从而解决了前端协同开发的问题。

### 3.How?怎样落地微前端?

2018年 Single-SPA诞生了， single-spa是一个用于前端微服务化的JavaScript前端解决方案  (本身没有处理样式隔离、js执行隔离)  实现了路由劫持和应用加载；

2019年 qiankun基于Single-SPA, 提供了更加开箱即用的 API  (single-spa + sandbox + import-html-entry)，它 做到了技术栈无关，并且接入简单(有多简单呢，像iframe一样简单)。

总结：子应用可以独立构建，运行时动态加载，主子应用完全解耦，并且技术栈无关，靠的是协议接入(这里提前强调一下：子应用必须导出 bootstrap、mount、unmount三个方法)。

### 这里先回答一下大家可能会有的疑问：

这不是iframe吗？

如果使用的是iframe，当iframe中的子应用切换路由时用户刷新页面就尴尬了。
应用间如何通信？

基于URL来进行数据传递，但是这种传递消息的方式能力较弱；

基于CustomEvent实现通信；

基于props主子应用间通信；

使用全局变量、Redux进行通信。

如何处理公共依赖？

CDN - externals

webpack联邦模块

## 二 .SingleSpa实战

### 1.构建子应用

首先创建一个vue子应用，并通过single-spa-vue来导出必要的生命周期：

```
vue create spa-vue  npm install single-spa-vue  
import singleSpaVue from 'single-spa-vue';const appOptions = {   el: '#vue',   router,   render: h => h(App)}// 在非子应用中正常挂载应用if(!window.singleSpaNavigate){ delete appOptions.el; new Vue(appOptions).$mount('#app');}const vueLifeCycle = singleSpaVue({   Vue,   appOptions});// 子应用必须导出以下生命周期：bootstrap、mount、unmountexport const bootstrap = vueLifeCycle.bootstrap;export const mount = vueLifeCycle.mount;export const unmount = vueLifeCycle.unmount;export default vueLifeCycle;
const router = new VueRouter({  mode: 'history',  base: '/vue',   //改变路径配置  routes})配置子路由基础路径
```

### 2.配置库打包

```
//vue.config.jsmodule.exports = {    configureWebpack: {        output: {            library: 'singleVue',            libraryTarget: 'umd'        },        devServer:{            port:10000        }    }}
```


将子模块打包成类库

### 3.主应用搭建



```
<div id="nav">    <router-link to="/vue">vue项目router-link>     <div id="vue">div>div>
```

将子应用挂载到id="vue"标签中

```
import Vue from 'vue'import App from './App.vue'import router from './router'import ElementUI from 'element-ui';import 'element-ui/lib/theme-chalk/index.css';Vue.use(ElementUI);const loadScript = async (url)=> {  await new Promise((resolve,reject)=>{    const script = document.createElement('script');    script.src = url;    script.onload = resolve;    script.onerror = reject;    document.head.appendChild(script)  });}import { registerApplication, start } from 'single-spa';registerApplication(    'singleVue',    async ()=>{        //这里通过协议来加载指定文件        await loadScript('http://localhost:10000/js/chunk-vendors.js');        await loadScript('http://localhost:10000/js/app.js');        return window.singleVue    },    location => location.pathname.startsWith('/vue'))start();new Vue({  router,  render: h => h(App)}).$mount('#app')
```

### 4.动态设置子应用publicPath

```
if(window.singleSpaNavigate){  __webpack_public_path__ = 'http://localhost:10000/'}
```

## 三.qiankun实战

qiankun是目前比较完善的一个微前端解决方案，它已在蚂蚁内部经受过足够大量的项目考验及打磨，十分健壮。这里附上官网。

### 1.主应用编写

```
<el-menu :router="true" mode="horizontal">    <el-menu-item index="/">首页el-menu-item>    <el-menu-item index="/vue">vue应用el-menu-item>    <el-menu-item index="/react">react应用el-menu-item>el-menu><router-view v-show="$route.name">router-view><div v-show="!$route.name" id="vue">div><div v-show="!$route.name" id="react">div>
```

### 2.注册子应用

```
import {registerMicroApps,start} from 'qiankun'const apps = [  {    name:'vueApp',    entry:'//localhost:10000',    container:'#vue',    activeRule:'/vue'  },  {    name:'reactApp',    entry:'//localhost:20000',    container:'#react',    activeRule:'/react'  }]registerMicroApps(apps);start();
```

### 3.子Vue应用

```
let instance = null;function render(){  instance = new Vue({    router,    render: h => h(App)  }).$mount('#app')}if(window.__POWERED_BY_QIANKUN__){  __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;}if(!window.__POWERED_BY_QIANKUN__){render()}export async function bootstrap(){}export async function mount(props){render();}export async function unmount(){instance.$destroy();}
```

这里不要忘记子应用的钩子导出。

```
module.exports = {    devServer:{        port:10000,        headers:{            'Access-Control-Allow-Origin':'*' //允许访问跨域        }    },    configureWebpack:{        output:{            library:'vueApp',            libraryTarget:'umd'        }    }}
```

### 4.子React应用

再起一个子应用，为了表明技术栈无关特性，这里使用了一个React项目：

```
import React from 'react';import ReactDOM from 'react-dom';import './index.css';import App from './App';function render() {  ReactDOM.render(    <React.StrictMode><App />React.StrictMode>,    document.getElementById('root')  );}if(!window.__POWERED_BY_QIANKUN__){  render()}export async function bootstrap() {}export async function mount() {render();}export async function unmount() {  ReactDOM.unmountComponentAtNode(document.getElementById("root"));}
```

重写react中的webpack配置文件 (config-overrides.js)

````
yarn add react-app-rewired --save-dev
```

```
module.exports = {  webpack: (config) => {    config.output.library = `reactApp`;    config.output.libraryTarget = "umd";    config.output.publicPath = 'http://localhost:20000/'    return config  },  devServer: function (configFunction) {    return function (proxy, allowedHost) {      const config = configFunction(proxy, allowedHost);      config.headers = {        "Access-Control-Allow-Origin": "*",      };      return config;    };  },};
```

配置.env文件

```
PORT=20000WDS_SOCKET_PORT=20000
```


React路由配置

```
import { BrowserRouter, Route, Link } from "react-router-dom"const BASE_NAME = window.__POWERED_BY_QIANKUN__ ? "/react" : "";function App() {  return (    <BrowserRouter basename={BASE_NAME}><Link to="/">首页Link><Link to="/about">关于Link><Route path="/" exact render={() => <h1>hello homeh1>}>Route><Route path="/about" render={() => <h1>hello abouth1>}>Route>BrowserRouter>  );}

```

## 四.CSS隔离方案

### 子应用之间样式隔离：

Dynamic Stylesheet动态样式表，当应用切换时移除掉老应用样式，再添加新应用样式，保证在一个时间点内只有一个应用的样式表生效

### 主应用和子应用之间的样式隔离：

BEM(Block Element Modifier)  约定项目前缀
CSS-Modules 打包时生成不冲突的选择器名
Shadow DOM 真正意义上的隔离
css-in-js

```
let shadowDom = shadow.attachShadow({ mode: 'open' }); // open/close设置可否从外部获取let pElement = document.createElement('p');pElement.innerHTML = 'hello world';let styleElement = document.createElement('style');styleElement.textContent = `  p{color:red}`shadowDom.appendChild(pElement);shadowDom.appendChild(styleElement)
```


shadow DOM 内部的元素始终不会影响到它的外部元素，可以实现真正意义上的隔离

## 五.JS沙箱机制


当运行子应用时应该跑在内部沙箱环境中

快照沙箱，当应用沙箱挂载或卸载时记录快照，在切换时依据快照恢复环境 (无法支持多实例)
Proxy 代理沙箱，不影响全局环境

## 1.快照沙箱

1.激活时将当前window属性进行快照处理

2.失活时用快照中的内容和当前window属性比对

3.如果属性发生变化保存到modifyPropsMap中，并用快照还原window属性

4.再次激活时，再次进行快照，并用上次修改的结果还原window属性

class SnapshotSandbox {    constructor() {        this.proxy = window;         this.modifyPropsMap = {}; // 修改了哪些属性        this.active();    }    active() {        this.windowSnapshot = {}; // window对象的快照        for (const prop in window) {            if (window.hasOwnProperty(prop)) {                // 将window上的属性进行拍照                this.windowSnapshot[prop] = window[prop];            }        }        Object.keys(this.modifyPropsMap).forEach(p => {            window[p] = this.modifyPropsMap[p];        });    }    inactive() {        for (const prop in window) { // diff 差异            if (window.hasOwnProperty(prop)) {                // 将上次拍照的结果和本次window属性做对比                if (window[prop] !== this.windowSnapshot[prop]) {                    // 保存修改后的结果                    this.modifyPropsMap[prop] = window[prop];                     // 还原window                    window[prop] = this.windowSnapshot[prop];                 }            }        }    }}
let sandbox = new SnapshotSandbox();((window) => {    window.a = 1;    window.b = 2;    window.c = 3    console.log(a,b,c)    sandbox.inactive();    console.log(a,b,c)})(sandbox.proxy);
快照沙箱只能针对单实例应用场景，如果是多个实例同时挂载的情况则无法解决，这时只能通过Proxy代理沙箱来实现

### 2.Proxy 代理沙箱

class ProxySandbox {    constructor() {        const rawWindow = window;        const fakeWindow = {}        const proxy = new Proxy(fakeWindow, {            set(target, p, value) {                target[p] = value;                return true            },            get(target, p) {                return target[p] || rawWindow[p];            }        });        this.proxy = proxy    }}let sandbox1 = new ProxySandbox();let sandbox2 = new ProxySandbox();window.a = 1;((window) => {    window.a = 'hello';    console.log(window.a)})(sandbox1.proxy);((window) => {    window.a = 'world';    console.log(window.a)})(sandbox2.proxy);
每个应用都创建一个proxy来代理window对象，好处是每个应用都是相对独立的，不需要直接更改全局的window属性。