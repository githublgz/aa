---
title: React性能优化
cover: false
top: false
toc: false
mathjax: false
tags:
  - React
categories:
  - 框架
abbrlink: 23375
date: 2020-04-20 10:38:18
author:
img:
coverImg:
password:
summary:
keywords:
---

## 一、重新认识render

> `react`的组件渲染分为初始化渲染和更新渲染

- 在初始化渲染的时候会调用根组件下的所有组件的`render`方法进行渲染，如下图（绿色表示已渲染，这一层是没有问题的）

![img](https://poetries1.gitee.io/img-repo/2019/10/420.png)

但是当我们要更新某个子组件的时候，如下图的绿色组件（从根组件传递下来应用在绿色组件上的数据发生改变）

![img](https://poetries1.gitee.io/img-repo/2019/10/421.png)

我们的理想状态是只调用关键路径上组件的render

![img](https://poetries1.gitee.io/img-repo/2019/10/422.png)

但是`react`的默认做法是调用所有组件的`render`，再对生成的虚拟`DOM`进行对比，如不变则不进行更新。这样的`render`和虚拟`DOM`的 对比 明显是在浪费，如下图（黄色表示浪费的`render`和虚拟`DOM`对比）

![img](https://poetries1.gitee.io/img-repo/2019/10/423.png)

**Tips**

- 拆分组件是有利于复用和组件优化的
- 生成虚拟`DOM`并进行比对发生在`render()`后，而不是`render()`前

## 二、更新阶段的生命周期

- `componentWillReceiveProps(object nextProps)` ：当挂载的组件接收到新的`props`时被调用。此方法应该被用于比较`this.props` 和 `nextProps`以用于使用`this.setState()`执行状态转换。（组件内部数据有变化，使用`state`，但是在更新阶段又要在`props`改变的时候改变`state`，则在这个生命周期里面）
- `shouldComponentUpdate(object nextProps, object nextState)` ： -`boolean` 当组件决定任何改变是否要更新到`DOM`时被调用。作为一个 优化 实现比较`this.props` 和 `nextProps` 、`this.state` 和 `nextState` ，如果`React`应该跳过更新，返回`false`
- `componentWillUpdate(object nextProps, object nextState`) ：在更新发生前被立即调用。你不能在此调用 `this.setState()`
- `componentDidUpdate(object prevProps, object prevState`) ： 在更新发生后被立即调用。（可以在`DOM`更新完之后，做一些收尾的工作）

**Tips**

- `React`的优化是基于 `shouldComponentUpdate` 的，该生命周期默认返回`true`，所以一旦`prop`或`state`有任何变化，都会引起重新`render`

## 三、shouldComponentUpdate

> `react`在每个组件生命周期更新的时候都会调用一个`shouldComponentUpdate(nextProps, nextState)`函数。它的职责就是返回`true`或`false`，true表示需要更新，`false`表示不需要，默认返回为`true`，即便你没有显示地定义 `shouldComponentUpdate` 函数。这就不难解释上面发生的资源浪费了

**带坑的写法**

- `{...this.props}` (不要滥用，请只传递`component`需要的`props`，传得太多，或者层次传得太深，都会加重`shouldComponentUpdate`里面的数据比较负担，因此，也请慎用`spread attributes（）)`
- `::this.handleChange()。(请将方法的bind一律置于constructor)`
- 复杂的页面不要在一个组件里面写完
- 请尽量使用`const element`
- `map`里面添加`key`，并且`key`不要使用`index`（可变的)
- 尽量少用`setTimeOut`或不可控的`refs`、`DOM`操作
- 数据尽可能简单明了，扁平化

## 四、性能检测工具

**React.addons.Perf**

> `react`官方提供一个插件 `React.addons.Perf` 可以帮助我们分析组件的性能，以确定是否需要优化

`react16`以前需要在项目中配置，`react16`以后请看这篇文章，直接打开控制台的`perf`选项测试 https://reactjs.org/docs/optimizing-performance.html#profiling-components-with-the-chrome-performance-tab

**react16之前配置**

- 安装 `react` 性能检测工具 `npm i react-addons-perf --save`，然后在`./app/index.js`中

```
// 性能测试
import Perf from 'react-addons-perf'
if (__DEV__) {
    window.Perf = Perf
}
```

- 打开`console`面板，先输入 `Perf.start()` 执行一些组件操作，引起数据变动，组件更新，然后输入 `Perf.stop()` 。（建议一次只执行一个操作，好进行分析）
- 再输入 `Perf.printInclusive` 查看所有涉及到的组件`render`，如下图（官方图片）

![img](https://poetries1.gitee.io/img-repo/2019/10/424.png)

> 或者输入`Perf.printWasted()`查看下不需要的的浪费组件`render`

![img](https://poetries1.gitee.io/img-repo/2019/10/425.png)

优化前

![img](https://poetries1.gitee.io/img-repo/2019/10/426.png)

优化后

![img](https://poetries1.gitee.io/img-repo/2019/10/427.png)

## 五、其他优化

1、**前端通用优化。**这类优化在所有前端框架中都存在，重点就在于如何将这些技巧应用在 React 组件中。

2、**减少不必要的组件更新。**这类优化是在组件状态发生变更后，通过减少不必要的组件更新来实现，对应到 React 中就是：**减少渲染的节点 、降低组件渲染的复杂度、充分利用缓存避免重新渲染**（利用缓存可以考虑使用PureComponent、React.memo、hook函数useCallback、useMemo等方法）

> PureComponent 是对**类组件**的 Props 和 State 进行浅比较；React.memo 是对**函数组件**的 Props 进行浅比较

3、**提交阶段优化。**这类优化的目的是减少提交阶段耗时。

### 1、组件按需加载

组件按需加载优化又可以分为：**懒加载、懒渲染、虚拟列表** 三类。

**懒加载**

在 SPA 中，懒加载优化一般用于从一个路由跳转到另一个路由。还可用于用户操作后才展示的复杂组件，比如点击按钮后展示的弹窗模块。在这些场景下，可以结合 Code Split 实现。

懒加载的实现主要是通过 Webpack 的动态导入和 `React.lazy` 方法。注意，实现懒加载优化时，不仅要考虑加载态，还需要对加载失败进行容错处理。

```js
import { lazy, Suspense, Component } from "react"
import "./styles.css"

// 对加载失败进行容错处理
class ErrorBoundary extends Component {
  constructor(props) {
    super(props)
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(error) {
    return { hasError: true }
  }

  render() {
    if (this.state.hasError) {
      return <h1>这里处理出错场景</h1>
    }

    return this.props.children
  }
}

const Comp = lazy(() => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (Math.random() > 0.5) {
        reject(new Error("模拟网络出错"))
      } else {
        resolve(import("./Component"))
      }
    }, 2000)
  })
})

export default function App() {
  return (
    <div className="App">
      <div style={{ marginBottom: 20 }}>
        实现懒加载优化时，不仅要考虑加载态，还需要对加载失败进行容错处理。
      </div>
      <ErrorBoundary>
        <Suspense fallback="Loading...">
          <Comp />
        </Suspense>
      </ErrorBoundary>
    </div>
  )
}
```

**懒渲染**

懒渲染指当组件进入或即将进入可视区域时才渲染组件。常见的组件 Modal/Drawer 等，当 visible 属性为 true 时才渲染组件内容，也可以认为是懒渲染的一种实现。

懒渲染的使用场景有：

1. 页面中出现多次的组件，且组件渲染费时、或者组件中含有接口请求。如果渲染多个带有请求的组件，由于浏览器限制了同域名下并发请求的数量，就可能会阻塞可见区域内的其他组件中的请求，导致可见区域的内容被延迟展示。
2. 需用户操作后才展示的组件。这点和懒加载一样，但懒渲染不用动态加载模块，不用考虑加载态和加载失败的兜底处理，实现上更简单。

判断组件是否出现在可视区域内是通过 [react-visibility-observer](https://link.zhihu.com/?target=https%3A//link.juejin.cn/%3Ftarget%3Dhttps%3A%2F%2Fwww.npmjs.com%2Fpackage%2Freact-visibility-observer) 进行监听。

```js
import { useState, useEffect } from "react"
import VisibilityObserver, {
  useVisibilityObserver,
} from "react-visibility-observer"

const VisibilityObserverChildren = ({ callback, children }) => {
  const { isVisible } = useVisibilityObserver()
  useEffect(() => {
    callback(isVisible)
  }, [callback, isVisible])

  return <>{children}</>
}

export const LazyRender = () => {
  const [isRendered, setIsRendered] = useState(false)

  if (!isRendered) {
    return (
      <VisibilityObserver rootMargin={"0px 0px 0px 0px"}>
        <VisibilityObserverChildren
          callback={isVisible => {
            if (isVisible) {
              setIsRendered(true)
            }
          }}
        >
          <span />
        </VisibilityObserverChildren>
      </VisibilityObserver>
    )
  }

  console.log("滚动到可视区域才渲染")
  return <div>我是 LazyRender 组件</div>
}

export default LazyRender
```

**虚拟列表**

虚拟列表是懒渲染的一种特殊场景。实现虚拟列表的组件有 [react-window](https://link.zhihu.com/?target=https%3A//link.juejin.cn/%3Ftarget%3Dhttps%3A%2F%2Freact-window.now.sh%2F%23%2Fexamples%2Flist%2Ffixed-size) 和 react-virtualized。react-window 是 react-virtualized 的轻量版本，其 API 和文档更加友好。新项目中推荐使用 react-window。

使用 react-window 很简单，只需要计算每项的高度即可。如果每项的高度是变化的，可给 itemSize 参数传一个函数。

```js
import { FixedSizeList as List } from "react-window"
const Row = ({ index, style }) => <div style={style}>Row {index}</div>

const Example = () => (
  <List
    height={150}
    itemCount={1000}
    itemSize={35} // 每项的高度为 35
    width={300}
  >
    {Row}
  </List>
)
```

### 2、批量更新

### 3、利用防抖、节流 避免重复回调

### 4、缓存优化

缓存优化往往是最简单有效的优化方式，在 React 组件中常用 useMemo 缓存上次计算的结果。当 useMemo 的依赖未发生改变时，就不会触发重新计算。一般用在「计算派生状态的代码」非常耗时的场景中，如：遍历大列表做统计信息。

1. React 官方并不保证 useMemo 一定会进行缓存，所以可能在依赖不改变时，仍然执行重新计算。参考 [How to memoize calculations](https://link.zhihu.com/?target=https%3A//link.juejin.cn/%3Ftarget%3Dhttps%3A%2F%2Freactjs.org%2Fdocs%2Fhooks-faq.html%23how-to-memoize-calculations)
2. useMemo 只能缓存最近一次函数执行的结果，如果想缓存更多次函数执行的结果，可使用 [memoizee](https://link.zhihu.com/?target=https%3A//link.juejin.cn/%3Ftarget%3Dhttps%3A%2F%2Fwww.npmjs.com%2Fpackage%2Fmemoizee)。

### 5、列表项使用 key 属性



