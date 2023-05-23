---

title: React-Redux原理
cover: false
top: false
toc: false
mathjax: false
tags:
  - React
categories:
  - 框架
abbrlink: 23375
date: 2020-02-21 10:08:19
author:
img:
coverImg:
password:
summary:
keywords:
---

## （一）Redux 特点

Redux顾名思义（Reducer+Flux）

### 1.唯一数据源

唯一数据源指的是应用的状态数据应该只存储在唯一的一个 Store 上 。 
我们已经知道，在 Flux 中，应用可以拥有多个 Store ，往往根据功能把应用的状态数据划分给若干个 Store 分别存储管理 。

如果状态数据分散在多个 Store 中，容易造成数据冗余，这样数据一致性方面就会出问题 。 虽然利用 Dispatcher 的 waitFor 方法可以保证多个 Store 之间的更新顺序，但是这又产生了不同 Store 之间的显示依赖关系，这种依赖关系的存在增加了应用的复杂度，容 
易带来新的问题。

### 2.保持状态只读

保持状态只读，就是说不能去直接修改状态，要修改 Store 的状态，必须要通过派发一个 action 对象完成，这一点 ，和 Flux 的要求并没有什么区别 。

### 3.数据改变只能通过纯函数完成

Reducer 不是一个 Redux 特定的术语，就以 JavaScript 为例，在ES5的数组高阶API



reduce最后一个为默认参数, 如果不设置就将数组第一个为默认值。

在 Redux 中， 每个 reducer 的函数签名如下所示 ：

```
reducer(state , action)
```



第一个参数 state 是当前的状态，第二个参数 action 是接收到的 action 对象，而 reducer函数要做的事情，就是根据 state 和 action 的值产生一个新的对象返回，注意 reducer 必须是纯函数，也就是说函数的返回结果必须完全由参数 state 和 action 决定，而且不产生任何副作用，也不能修改参数 state 和 action 对象。

比对Flux和Redux处理Store的差别



```
CounterStore.dispatchToken = AppDispatcher.register((action ) => {
  if (action.type === ActionTypes .INCREMENT) {
        counterValues[action.counterCaption) ++;
        CounterStore.emitChange() ;
   } else if (action.type === ActionTypes.DECREMENT) {
        counterValues[action.counterCaption]
        CounterStore.emitChange() ;
   }
});
```



Flux 更新状态的函数只有一个参数 action ，因为状态是由 Store 直接管理的，所以 
处理函数中会看到代码直接更新 state ；

在 Redux 中，一个实现同样功能的 reducer 代码 
如下：



```
function reducer(state , action) => {
    const {counterCaption} = action;
    switch (action.type) {
        case ActionTypes.INCREMENT :
            return {... state , [counterCaption] : state [counterCaption ] + 1};
        case ActionTypes.DECREMENT:
            return {... state, [counterCaption] : state [counterCaption) - 1};
    default :
        return state
    }
}
```



可以看到 reducer 函数不光接受 action 为参数，还接受 state 为参数。 
也就是说， Redux的 reducer 只负责计算状态，却并不负责存储状态 。

## （二）组件 Context

相关代码已经推送到

我的github的simple-provider分支

react-redux 顶层封装了一个Provider组件。现在我们来看下他的内部实现原理。

### （1）为什么需要context

Redux的基本慨念

这里再次提及下Redux的Store上的3个api，



```
   store.getState()  // 获得 store 上存储的所有状态
   store.dispatch() // View 发出 Action 的唯一方法
   store.subscribe() // 订阅 store上State 发生变化
```



虽然 Redux 应用全局就一个 Store 这样的直接导入依然有问题，因为在组件中直接导人 Store 是非常不利于组件复用的 。

所以，一个应用中，最好只有一个地方需要直接导人 Store ，这个位置当然应该是在调用最顶层 React 组件的位置 。 但是这样需要把Store，从顶层一层层往下传递，首先我们想到的就是props（父子组件通信方案）。这种方法有一个很大的缺陷，就是从上到下，所有的组件都要帮助传递这个 props 。

设想在一个嵌套多层的组件结构中，只有最里层的组件才需要使用 store ，但是为了把 store 从最外层传递到最里层，就要求中间所有的组件都需要增加对这个 store prop 的支持，即使根本不使用它，这无疑增加程序的耦合度，复杂度和不可维护性 。

React 提供了一个 叫 Context 的功能，能够完美地解决这个问题 。

### （2）Context 基本慨念

所谓 Context ，就是“上下文环境”，让一个树状组件上所有组件都能访问一个共同的对象，为了完成这个任务，需要上级组件和下级组件配合 。



首先，上级组件要宣称自己支持 context，并且提供一个函数来返回代表 Context 的对象。

然后，这个上级组件之下的所有子孙组件，只要宣称自己需要这个 context ，就可以通过 this.context 访问到这个共同的环境对象。

### （3）Context 组件实现

这里为了演示，顶层容器为ControlPanel，一般我们会将起作为容器组件，将Summary和Counter设置为傻瓜组件。为了演示context可以不通过props传递，分别为Summary，Counter增加外层容器。

首先是增加Provider 组件

```
import React, { Component } from 'react';
import PropTypes from 'prop-types';

class Provider extends Component {

  getChildContext() {
    return {
      store: this.props.store
    };
  }

  render() {
    return this.props.children;
  }

}

Provider.propTypes = {
  store: PropTypes.object.isRequired
}

// 指定 Provider 的 childContextTypes 属性
Provider.childContextTypes = {
  store: PropTypes.object
};

export default Provider;
```



我们创建了一个通用的 context 提供者 Provider 组件。 
注意一下几点： 

1. 提供－个函数 getChildContext ，这个函数返回的就是代表 Context 的对象 。 
2. 通过 this.props.children 渲染子组件 
3. 指定 Provider 的childContextTypes 属性，让其能被React 认可为一个 Context 的提供者。 
4. 定义类 的 childContextTypes ，必须和 getChildContext 对应，只有这 
两者都齐备， Provider 的子组件才有可能访问到 context。

然后修改应用的入口 src/index.js



```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import registerServiceWorker from './config/registerServiceWorker';
import {Provider} from './components/Common'
import ControlPanel from './views/SimpleProvider'
import StoreConfig  from './store';

const store = StoreConfig();

ReactDOM.render(
    <Provider store={store}>
      <ControlPanel />
    </Provider>,
    document.getElementById('root')
);
registerServiceWorker();
```



上面代码， Provider 成为了顶层组件 。 当然，如同我们上面看到 的， Provider 只是把渲染工作完全交给子组件，它扮演的角色只是提供Context ，包住了最顶层 的 ControlPanel ，也就让 context 覆盖了整个应用中所有组件 。

然后为Counter.js 增加容器组件

```
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import * as Actions from '../../actions/demoActions';

const buttonStyle = {
  margin: '10px'
};
//presentational component
const Counter = ({caption, onIncrement, onDecrement, value}) => {
  return (
    <div>
      <button style={buttonStyle} onClick={onIncrement}>+</button>
      <button style={buttonStyle} onClick={onDecrement}>-</button>
      <span>{caption} count: {value}</span>
    </div>
  );
}

Counter.propTypes = {
  caption: PropTypes.string.isRequired,
  onIncrement: PropTypes.func.isRequired,
  onDecrement: PropTypes.func.isRequired,
  value: PropTypes.number.isRequired
};

// Counter 容器
class CounterContainer extends Component {
  constructor (props, context) {
    super(props, context);
    this.state = this.getOwnState();
  }

  // 箭头函数, 不用在构造器bind this
  getOwnState = () => {
    const { demo } = this.context.store.getState();
    const { caption } = this.props;
    return {
      value: demo[caption]
    }
  }

  onIncrement = () => {
    const { caption } = this.props;
    this.context.store.dispatch(Actions.increment(caption));
  }

  onDecrement = () => {
    const { caption } = this.props;
    this.context.store.dispatch(Actions.decrement(caption));
  }

  onChange = () => {
    this.setState(this.getOwnState());
  }

  shouldComponentUpdate(nextProps, nextState) {
    return (nextProps.caption !== this.props.caption) ||
        (nextState.value !== this.state.value);
  }

  componentDidMount() {
    this.context.store.subscribe(this.onChange);
  }

  componentWillUnmount() {
    this.context.store.unsubscribe(this.onChange);
  }

  render() {
    const { caption } = this.props;
    const onIncrement = this.onIncrement;
    const onDecrement = this.onDecrement;
    const { value } =this.state;
    const counterProps = { caption, value, onIncrement, onDecrement };
    return <Counter {...counterProps}/>
  }

}

CounterContainer.propTypes = {
  caption: PropTypes.string.isRequired
};

CounterContainer.contextTypes = {
  store: PropTypes.object
}
export default CounterContainer;
```



最后为Summary.js 增加容器组件



```
import React, { Component } from 'react';
import PropTypes from 'prop-types';

const Summary = ({sum}) => {
  return (
    <div>Total Count: {sum}</div>
  );
}

Summary.propTypes = {
  sum: PropTypes.number.isRequired
};

class SummaryContainer extends Component {
  constructor(props, context) {
    super(props, context);
    this.state = this.getOwnState();
  }

  onChange = () => {
    this.setState(this.getOwnState());
  }

  getOwnState = () => {
    const { demo } = this.context.store.getState();
    let sum = 0;
    for (const key in demo) {
      if (demo.hasOwnProperty(key)) {
        sum += demo[key];
      }
    }

return { sum: sum };



  }

  shouldComponentUpdate(nextProps, nextState) {
    return nextState.sum !== this.state.sum;
  }

  componentDidMount() {
    this.context.store.subscribe(this.onChange);
  }

  componentWillUnmount() {
    this.context.store.unsubscribe(this.onChange);
  }

  render() {
    const sum = this.state.sum;
    return (
        <Summary sum={sum} />
    );
  }
}

SummaryContainer.contextTypes = {
  store: PropTypes.object
}

export default SummaryContainer
```



## 总结：

上面代码中，我们可以发现一下几点问题：

在调用 super 的时候，一定要带上 context 参数，这样才能让 React 组件初始化实例中的 context ，不然组件的其他部分就无法使用 this.context。
必须给 CounterContainer和 SummaryContainer 类的 contextTypes 赋值和 Provider. childContextTypes 一样的值，两者必须一致，不然就无法访问到context
我们为CounterContainer和 SummaryContainer 类重复的获取state，重复的订阅store，绑定生命周期，完全需要抽离出来，而且这种是不兼容React-Router的写法
总结一下：

Context 这个功能相当于提供了一个全局可以访问的对象但是全局对象或者说全局变量肯定是我们应该避免的用法，只要有一个地方改变了全局对象的值，应用中其他部分就会受影响，那样整个程序的运行结果就完全不可预期了 。

所以，单纯来看 React 的这个 Context 功能的话，必须强调这个功能要谨慎使用，只有对那些每个组件都可能使用，但是中间组件又可能不使用的对象才有必要使用Context ，千万不要滥用 。

但是，对于 Redux ，因为 Redux 的 Store 封装得很好，没有提供直接修改状态的功能，就是说一个组件虽然能够访问全局唯一的 Store ，却不可能直接修改 Store 中的状态，这样部分克服了作为全局对象的缺点 。

而且，一个应用只有一个 Store ，这个 Store 是 Context 
里唯一需要的东西，并不算滥用，所以，使用 Context 来传递 Store 是一个不错的选择 。
