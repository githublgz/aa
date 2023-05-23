---
title: React Fiber
cover: false
top: false
toc: false
mathjax: false
tags:
  - React
categories:
  - 框架
abbrlink: 23375
date: 2020-01-07 22:17:46
author:
img:
coverImg:
password:
summary:
keywords:
---

> `React Fiber`是对核心算法的一次重新实现。`React Fiber`把更新过程碎片化，把一个耗时长的任务分成很多小片，每一个小片的运行时间很短，虽然总时间依然很长，但是在每个小片执行完之后，都给其他任务一个执行的机会，这样唯一的线程就不会被独占，其他任务依然有运行的机会

1. 在`React Fiber`中，一次更新过程会分成多个分片完成，所以完全有可能一个更新任务还没有完成，就被另一个更高优先级的更新过程打断，这时候，优先级高的更新任务会优先处理完，而低优先级更新任务所做的工作则会完全作废，然后等待机会重头再来
2. 因为一个更新过程可能被打断，所以`React Fiber`一个更新过程被分为两个阶段(`Phase`)：第一个阶段`Reconciliation Phase`和第二阶段`Commit Phase`
3. 在第一阶段`Reconciliation Phase`，`React Fiber`会找出需要更新哪些`DOM`，这个阶段是可以被打断的；但是到了第二阶段`Commit Phase`，那就一鼓作气把`DOM`更新完，绝不会被打断
4. 这两个阶段大部分工作都是`React Fiber`做，和我们相关的也就是生命周期函数

> `React Fiber`改变了之前`react`的组件渲染机制，新的架构使原来同步渲染的组件现在可以异步化，可中途中断渲染，执行更高优先级的任务。释放浏览器主线程

**关键特性**

- 增量渲染（把渲染任务拆分成块，匀到多帧）
- 更新时能够暂停，终止，复用渲染任务
- 给不同类型的更新赋予优先级
- 并发方面新的基础能力

> 增量渲染用来解决掉帧的问题，渲染任务拆分之后，每次只做一小段，做完一段就把时间控制权交还给主线程，而不像之前长时间占用

## 二、组件的渲染顺序

> 假如有A,B,C,D组件，层级结构为：

![img](https://poetries1.gitee.io/img-repo/2019/10/414.png)

我们知道组件的生命周期为：

**挂载阶段**：

- `constructor()`
- `componentWillMount()`
- `render()`
- `componentDidMount()`

**更新阶段为**：

- `componentWillReceiveProps()`
- `shouldComponentUpdate()`
- `componentWillUpdate()`
- `render()`
- `componentDidUpdate`

> 那么在挂载阶段，`A,B,C,D`的生命周期渲染顺序是如何的呢？

那么在挂载阶段，A,B,C,D的生命周期渲染顺序是如何的呢？

![img](https://poetries1.gitee.io/img-repo/2019/10/415.png)

> 以`render()`函数为分界线。从顶层组件开始，一直往下，直至最底层子组件。然后再往上

组件`update`阶段同理

前面是`react16`以前的组建渲染方式。这就存在一个问题

> 如果这是一个很大，层级很深的组件，`react`渲染它需要几十甚至几百毫秒，在这期间，`react`会一直占用浏览器主线程，任何其他的操作（包括用户的点击，鼠标移动等操作）都无法执行

**Fiber架构就是为了解决这个问题**

> 看一下fiber架构 组建的渲染顺序

> 加入`fiber`的`react`将组件更新分为两个时期

**这两个时期以render为分界**

- `render`前的生命周期为`phase1`,
- `render`后的生命周期为`phase2`

> - `phase1`的生命周期是可以被打断的，每隔一段时间它会跳出当前渲染进程，去确定是否有其他更重要的任务。此过程，`React`在 `workingProgressTree` （并不是真实的`virtualDomTree`）上复用 `current` 上的 `Fiber` 数据结构来一步地（通过`requestIdleCallback`）来构建新的 tree，标记处需要更新的节点，放入队列中
> - `phase2`的生命周期是不可被打断的，`React` 将其所有的变更一次性更新到`DOM`上

**这里最重要的是phase1这是时期所做的事。因此我们需要具体了解phase1的机制**

- 如果不被打断，那么`phase1`执行完会直接进入`render`函数，构建真实的`virtualDomTree`
- 如果组件再`phase1`过程中被打断，即当前组件只渲染到一半（也许是在`willMount`,也许是`willUpdate`~反正是在render之前的生命周期），那么`react`会怎么干呢？ `react`会放弃当前组件所有干到一半的事情，去做更高优先级更重要的任务（当然，也可能是用户鼠标移动，或者其他react监听之外的任务），当所有高优先级任务执行完之后，`react`通过`callback`回到之前渲染到一半的组件，从头开始渲染。（看起来放弃已经渲染完的生命周期，会有点不合理，反而会增加渲染时长，但是`react`确实是这么干的）

**所有phase1的生命周期函数都可能被执行多次，因为可能会被打断重来**

> 这样的话，就和`react16`版本之前有很大区别了，因为可能会被执行多次，那么我们最好就得保证`phase1`的生命周期每一次执行的结果都是一样的，否则就会有问题，因此，最好都是纯函数

- 如果高优先级的任务一直存在，那么低优先级的任务则永远无法进行，组件永远无法继续渲染。这个问题facebook目前好像还没解决
- 所以，facebook在`react16`增加`fiber`结构，其实并不是为了减少组件的渲染时间，事实上也并不会减少，最重要的是现在可以使得一些更高优先级的任务，如用户的操作能够优先执行，提高用户的体验，至少用户不会感觉到卡顿

## 一、问题

`JavaScript`引擎和页面渲染引擎两个线程是互斥的，当其中一个线程执行时，另一个线程只能挂起等待

如果 `JavaScript` 线程长时间地占用了主线程，那么渲染层面的更新就不得不长时间地等待，界面长时间不更新，会导致页面响应度变差，用户可能会感觉到卡顿

而这也正是 `React 15` 的 `Stack Reconciler`所面临的问题，当 `React`在渲染组件时，从开始到渲染完成整个过程是一气呵成的，无法中断

如果组件较大，那么`js`线程会一直执行，然后等到整棵`VDOM`树计算完成后，才会交给渲染的线程

这就会导致一些用户交互、动画等任务无法立即得到处理，导致卡顿的情况

![img](https://static.vue-js.com/5eb3a850-ed24-11eb-ab90-d9ae814b240d.png)

## 二、是什么

React Fiber 是 Facebook 花费两年余时间对 React 做出的一个重大改变与优化，是对 React 核心算法的一次重新实现。从Facebook在 React Conf 2017 会议上确认，React Fiber 在React 16 版本发布

在`react`中，主要做了以下的操作：

- 为每个增加了优先级，优先级高的任务可以中断低优先级的任务。然后再重新，注意是重新执行优先级低的任务
- 增加了异步任务，调用requestIdleCallback api，浏览器空闲的时候执行
- dom diff树变成了链表，一个dom对应两个fiber（一个链表），对应两个队列，这都是为找到被中断的任务，重新执行

从架构角度来看，`Fiber` 是对 `React`核心算法（即调和过程）的重写

从编码角度来看，`Fiber`是 `React`内部所定义的一种数据结构，它是 `Fiber`树结构的节点单位，也就是 `React 16` 新架构下的虚拟`DOM`

一个 `fiber`就是一个 `JavaScript`对象，包含了元素的信息、该元素的更新操作队列、类型，其数据结构如下：

```js
type Fiber = {
  // 用于标记fiber的WorkTag类型，主要表示当前fiber代表的组件类型如FunctionComponent、ClassComponent等
  tag: WorkTag,
  // ReactElement里面的key
  key: null | string,
  // ReactElement.type，调用`createElement`的第一个参数
  elementType: any,
  // The resolved function/class/ associated with this fiber.
  // 表示当前代表的节点类型
  type: any,
  // 表示当前FiberNode对应的element组件实例
  stateNode: any,

  // 指向他在Fiber节点树中的`parent`，用来在处理完这个节点之后向上返回
  return: Fiber | null,
  // 指向自己的第一个子节点
  child: Fiber | null,
  // 指向自己的兄弟结构，兄弟节点的return指向同一个父节点
  sibling: Fiber | null,
  index: number,

  ref: null | (((handle: mixed) => void) & { _stringRef: ?string }) | RefObject,

  // 当前处理过程中的组件props对象
  pendingProps: any,
  // 上一次渲染完成之后的props
  memoizedProps: any,

  // 该Fiber对应的组件产生的Update会存放在这个队列里面
  updateQueue: UpdateQueue<any> | null,

  // 上一次渲染的时候的state
  memoizedState: any,

  // 一个列表，存放这个Fiber依赖的context
  firstContextDependency: ContextDependency<mixed> | null,

  mode: TypeOfMode,

  // Effect
  // 用来记录Side Effect
  effectTag: SideEffectTag,

  // 单链表用来快速查找下一个side effect
  nextEffect: Fiber | null,

  // 子树中第一个side effect
  firstEffect: Fiber | null,
  // 子树中最后一个side effect
  lastEffect: Fiber | null,

  // 代表任务在未来的哪个时间点应该被完成，之后版本改名为 lanes
  expirationTime: ExpirationTime,

  // 快速确定子树中是否有不在等待的变化
  childExpirationTime: ExpirationTime,

  // fiber的版本池，即记录fiber更新过程，便于恢复
  alternate: Fiber | null,
}
```

## 三、如何解决

`Fiber`把渲染更新过程拆分成多个子任务，每次只做一小部分，做完看是否还有剩余时间，如果有继续下一个任务；如果没有，挂起当前任务，将时间控制权交给主线程，等主线程不忙的时候在继续执行

即可以中断与恢复，恢复后也可以复用之前的中间状态，并给不同的任务赋予不同的优先级，其中每个任务更新单元为 `React Element` 对应的 `Fiber`节点

实现的上述方式的是`requestIdleCallback`方法

`window.requestIdleCallback()`方法将在浏览器的空闲时段内调用的函数排队。这使开发者能够在主事件循环上执行后台和低优先级工作，而不会影响延迟关键事件，如动画和输入响应

首先 React 中任务切割为多个步骤，分批完成。在完成一部分任务之后，将控制权交回给浏览器，让浏览器有时间再进行页面的渲染。等浏览器忙完之后有剩余时间，再继续之前 React 未完成的任务，是一种合作式调度。

该实现过程是基于 `Fiber`节点实现，作为静态的数据结构来说，每个 `Fiber` 节点对应一个 `React element`，保存了该组件的类型（函数组件/类组件/原生组件等等）、对应的 DOM 节点等信息。

作为动态的工作单元来说，每个 `Fiber` 节点保存了本次更新中该组件改变的状态、要执行的工作。

每个 Fiber 节点有个对应的 `React element`，多个 `Fiber`节点根据如下三个属性构建一颗树：

```javascript
// 指向父级Fiber节点
this.return = null
// 指向子Fiber节点
this.child = null
// 指向右边第一个兄弟Fiber节点
this.sibling = null
```

通过这些属性就能找到下一个执行目标

