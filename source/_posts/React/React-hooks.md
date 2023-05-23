---
title: React hooks
cover: false
top: false
toc: false
mathjax: false
tags:
  - React
categories:
  - 框架
abbrlink: 23375
date: 2020-01-15 06:34:26
author:
img:
coverImg:
password:
summary:
keywords:
---

## 一、组件类的缺点

> `React` 的核心是组件。`v16.8`版本之前，组件的标准写法是类（class）。下面是一个简单的组件类

```
import React, { Component } from "react";

export default class Button extends Component {
  constructor() {
    super();
    this.state = { buttonText: "Click me, please" };
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    this.setState(() => {
      return { buttonText: "Thanks, been clicked!" };
    });
  }
  render() {
    const { buttonText } = this.state;
    return <button onClick={this.handleClick}>{buttonText}</button>;
  }
}
```

这个组件类仅仅是一个按钮，但可以看到，它的代码已经很”重”了。真实的 React App 由多个类按照层级，一层层构成，复杂度成倍增长。再加入 Redux，就变得更复杂



`Hook` 是 React 16.8 的新增特性。它可以让你在不编写 `class` 的情况下使用 `state` 以及其他的 `React` 特性

至于为什么引入`hook`，官方给出的动机是解决长时间使用和维护`react`过程中常遇到的问题，例如：

- 难以重用和共享组件中的与状态相关的逻辑
- 逻辑复杂的组件难以开发与维护，当我们的组件需要处理多个互不相关的 local state 时，每个生命周期函数中可能会包含着各种互不相关的逻辑在里面
- 类组件中的this增加学习成本，类组件在基于现有工具的优化上存在些许问题
- 由于业务变动，函数组件不得不改为类组件等等

在以前，函数组件也被称为无状态的组件，只负责渲染的一些工作

因此，现在的函数组件也可以是有状态的组件，内部也可以维护自身的状态以及做一些逻辑方面的处理

## 二、Hook 的含义

- `React Hooks` 的意思是，组件尽量写成纯函数，如果需要外部功能和副作用，就用钩子把外部代码”钩”进来。 `React Hooks` 就是那些钩子。
- 你需要什么功能，就使用什么钩子。`React` 默认提供了一些常用钩子，你也可以封装自己的钩子。
- 所有的钩子都是为函数引入外部功能，所以 `React` 约定，钩子一律使用use前缀命名，便于识别。你要使用 `xxx` 功能，钩子就命名为 `usexxx`

**React 默认提供的四个最常用的钩子**

- `useState()`
- `useContext()`
- `useReducer()`
- `useEffect()`
- `useCallback`
- `useMemo`

## 三、useState()：状态钩子

> `useState()`用于为函数组件引入状态（`state`）。纯函数不能有状态，所以把状态放在钩子里面。

用户点击按钮，会导致按钮的文字改变，文字取决于用户是否点击，这就是状态。使用`useState()`重写如下

```
import React, { useState } from "react";

export default function  Button()  {
  const  [buttonText, setButtonText] =  useState("Click me,   please");

  function handleClick()  {
    return setButtonText("Thanks, been clicked!");
  }

  return  <button  onClick={handleClick}>{buttonText}</button>;
}
```

上面代码中，Button 组件是一个函数，内部使用useState()钩子引入状态。

> `useState()`这个函数接受状态的初始值，作为参数，上例的初始值为按钮的文字。该函数返回一个数组，数组的第一个成员是一个变量（上例是`buttonText`），指向状态的当前值。第二个成员是一个函数，用来更新状态，约定是set前缀加上状态的变量名（上例是`setButtonText`）

## 四、useContext()：共享状态钩子

- 如果需要在组件之间共享状态，可以使用·useContext()·。
- 现在有两个组件 ·Navbar ·和 ·Messages·，我们希望它们之间共享状态

```
<div className="App">
  <Navbar/>
  <Messages/>
</div>
```

> 第一步就是使用 `React Context API`，在组件外部建立一个 `Context`。

```
const AppContext = React.createContext({});
```

组件封装代码如下。

```
<AppContext.Provider value={{
  username: 'superawesome'
}}>
  <div className="App">
    <Navbar/>
    <Messages/>
  </div>
</AppContext.Provider>
```

> 上面代码中，`AppContext.Provider`提供了一个 `Context` 对象，这个对象可以被子组件共享。

Navbar 组件的代码如下。

```
const Navbar = () => {
  const { username } = useContext(AppContext);
  return (
    <div className="navbar">
      <p>AwesomeSite</p>
      <p>{username}</p>
    </div>
  );
}
```

> 上面代码中，`useContext()`钩子函数用来引入`Context` 对象，从中获取`username`属性。

Message 组件的代码也类似。

```
const Messages = () => {
  const { username } = useContext(AppContext)

  return (
    <div className="messages">
      <h1>Messages</h1>
      <p>1 message for {username}</p>
      <p className="message">useContext is awesome!</p>
    </div>
  )
}
```

## 五、useReducer()：action 钩子

- React 本身不提供状态管理功能，通常需要使用外部库。这方面最常用的库是 Redux。
- Redux 的核心概念是，组件发出 `action` 与状态管理器通信。状态管理器收到 `action`以后，使用 `Reducer`函数算出新的状态，`Reducer` 函数的形式是`(state, action) => newState`。
- `useReducers()`钩子用来引入 `Reducer` 功能。

```
const [state, dispatch] = useReducer(reducer, initialState);
```

> 上面是`useReducer()`的基本用法，它接受`Reducer` 函数和状态的初始值作为参数，返回一个数组。数组的第一个成员是状态的当前值，第二个成员是发送 `action` 的`dispatch`函数。

下面是一个计数器的例子。用于计算状态的 Reducer 函数如下。

```
const myReducer = (state, action) => {
  switch(action.type)  {
    case('countUp'):
      return  {
        ...state,
        count: state.count + 1
      }
    default:
      return  state;
  }
}
function App() {
  const [state, dispatch] = useReducer(myReducer, { count:   0 });
  return  (
    <div className="App">
      <button onClick={() => dispatch({ type: 'countUp' })}>
        +1
      </button>
      <p>Count: {state.count}</p>
    </div>
  );
}
```

> 由于 Hooks 可以提供共享状态和 Reducer 函数，所以它在这些方面可以取代 Redux。但是，它没法提供中间件（middleware）和时间旅行（time travel），如果你需要这两个功能，还是要用 Redux

## 六、useEffect()：副作用钩子

> `useEffect()`用来引入具有副作用的操作，最常见的就是向服务器请求数据。以前，放在`componentDidMount`里面的代码，现在可以放在`useEffect()`。

`useEffect()`的用法如下。

```
useEffect(()  =>  {
  // Async Action
}, [dependencies])
```

> 上面用法中，`useEffect()`接受两个参数。第一个参数是一个函数，异步操作的代码放在里面。第二个参数是一个数组，用于给出 Effect 的依赖项，只要这个数组发生变化，`useEffect()`就会执行。第二个参数可以省略，这时每次组件渲染时，就会执行`useEffect()`。

```
const Person = ({ personId }) => {
  const [loading, setLoading] = useState(true);
  const [person, setPerson] = useState({});

  useEffect(() => {
    setLoading(true); 
    fetch(`https://swapi.co/api/people/${personId}/`)
      .then(response => response.json())
      .then(data => {
        setPerson(data);
        setLoading(false);
      });
  }, [personId])

  if (loading === true) {
    return <p>Loading ...</p>
  }

  return <div>
    <p>You're viewing: {person.name}</p>
    <p>Height: {person.height}</p>
    <p>Mass: {person.mass}</p>
  </div>
}
```

> 上面代码中，每当组件参数`personId`发生变化，`useEffect()`就会执行。组件第一次渲染时，`useEffect()`也会执行

## 七、解决什么

通过对上面的初步认识，可以看到`hooks`能够更容易解决状态相关的重用的问题：

- 每调用useHook一次都会生成一份独立的状态
- 通过自定义hook能够更好的封装我们的功能

编写`hooks`为函数式编程，每个功能都包裹在函数中，整体风格更清爽，更优雅

```
hooks`的出现，使函数组件的功能得到了扩充，拥有了类组件相似的功能，在我们日常使用中，使用`hooks`能够解决大多数问题，并且还拥有代码复用机制，因此优先考虑`hooks
```