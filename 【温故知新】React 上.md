---
title: 【温故知新】React 上
date: 2021-05-26 00:00:00
tags:
  - 面试
  - react
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/react.png
---

![](https://hongxh.cn/img/study_img/react.png)

# React

### react 一个专注于构建用户界面的 JavaScript 库
- virtual Dom 模型
  - 虚拟 dom 其实就是对真实 dom 的一个抽象，是一个 js 对象，react 所有的表层操作其实都是在修改 vdom
- 生命周期管理
- setState 机制
- react patch、事件系统


###  jsx 如何转换为 js 的
- 依靠 Babel 或 TypeScript 来将 JSX 代码转换为 JavaScript。许多包含预配置的工具，
  例如 Create React App 或 Next.js，在其内部也引入了 JSX 转换。
- 当你使用 JSX 时，编译器会将其转换为浏览器可以理解的 React 函数调用。
  旧的 JSX 转换会把 JSX 转换为 React.createElement(...) 调用。
- react 17 与 babel 合作，提供新的转换


### React 中可以在 render 访问 refs 吗
- 不可以，render 阶段 DOM 还没有生成，无法获取 DOM。DOM 的获取需要在 pre-commit 阶段和 commit 阶段


### React 与 Vue 的 diff 算法有何不同
- 自 React 16 起，引入了 Fiber 架构。
  为了使整个更新过程可随时暂停恢复，节点与树分别采用了 FiberNode 与 FiberTree 进行重构。
  fiberNode 使用了双链表的结构，可以直接找到兄弟节点与子节点。
  整个更新过程由 current 与 workInProgress 两株树双缓冲完成。
  workInProgress 更新完成后，再通过修改 current 相关指针指向新节点。
- Vue 的整体 diff 策略与 React 对齐，虽然缺乏时间切片能力，
  但这并不意味着 Vue 的性能更差，因为在 Vue3 初期引入过，
  后期因为收益不高移除掉了。除了高帧率动画，在 Vue 中其他的场景几乎都可以使用防抖和节流去提高响应性能。


### React 的状态提升是什么
- 把共用的属性提升到共同父组件管理


### props.children 和 React.Children 有什么区别
- 在 React 中，当涉及组件嵌套，在父组件中使用 props.children 把所有子组件显示出来
- React.Children.map 方法让我们对父组件的所有子组件又更灵活地控制。另外还有 foreach 等方法
- JavaScript 中的 map 不会对为 null 或者 undefined 的数据进行处理，
  而 React.Children.map 中的 map 可以处理 React.Children 为 null 或者 undefined 的情况。


### setState 是同步还是异步
- 在React中，**如果是由React引发的事件处理（比如通过onClick引发的事件处理），
  调用 setState 不会同步更新 this.state，除此之外的 setState 调用会同步执行 this.state** 。
  所谓“除此之外”，指的是绕过 React 通过 addEventListener 直接添加的事件处理函数，
  还有通过 setTimeout / setInterval 产生的异步调用。
- 原因：在 React 的 setState 函数实现中，会根据一个变量 isBatchingUpdates 判断是直接更新
  this.state 还是放到队列中回头再说，而 isBatchingUpdates 默认是 false，
  也就表示 setState 会同步更新 this.state，但是，**有一个函数 batchedUpdates，
  这个函数会把 isBatchingUpdates 修改为true，而当 React 在调用事件处理函数之前就会调用这个 batchedUpdates，
  造成的后果，就是由 React 控制的事件处理过程 setState 不会同步更新 this.state**。
- **注意：** setState的“异步”并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，
  只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，
  形式了所谓的“异步”，当然可以通过第二个参数 setState(partialState, callback) 中的callback拿到更新后的结果。

  
### 说说 Render Props、HOC
- HOC
  - HOC 是 React 中用于复用组件逻辑的一种高级技巧。
- RenderProps
  - 指一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术
  - 更具体地说，render prop 是一个用于告知组件需要渲染什么内容的函数 prop。
  - 优点：数据共享、代码复用，将组件内的 state 作为 props 传递给调用者，将渲染逻辑交给调用者。
  - 缺点：无法在 return 语句外访问数据、嵌套写法不够优雅


### component 和 PureComponent 的区别
- pureComponent 通过 prop 和 state 的浅比较（shallowEqual）来实现 shouldComponentUpdate
- component 是需要开发者在 shouldComponentUpdate 钩子函数中自己写 render 逻辑的，在某些情况下可以使用 pureComponent 来提升性能。
- pureComponent 的优点：不需要开发者使用 shouldComponentUpdate 就可使用简单的判断来提升性能；
- pureComponent 的缺点：由于进行的是浅比较，可能由于深层的数据不一致导致而产生错误的否定判断，从而导致页面得不到更新；


### function 和 class component 的区别
- fun 没有实例，没有 this ，没有生命周期
- class 生命周期执行顺序
  - 挂载：constructor() => static getDerivedStateFromProps() => render() => componentDidMount()
  - 卸载：componentWillUnmount()
  - 错误处理：getDerivedStateFromError() => componentDidCatch()
  - 更新：
    - static getDerivedStateFromProps()
    - shouldComponentUpdate()
    - render()
    - getSnapshotBeforeUpdate()
    - componentDidUpdate()




# Hooks

### 对 React Hook 的理解，它的实现原理是什么
- class Component，由于继承 React.Component，可以使用很多现成的东西，state 和 生命周期，但是这样也造成使用的繁琐，难以拆分和复用
- function Component 由于没有实例，没有 this 无法维护 state，在 hooks 之前也被称为无状态组件。


### useEffect 与 useLayoutEffect 的区别
- 同步异步，防止首页闪屏


### 为什么第二个参数是空数组，相当于 componentDidMount ？
- 因为依赖一直不变化，callback 不会二次执行。


### setState 和 useState 区别
- useState 和 this.setState 区别之处在于，前者每次更新后 state 都是新值，换而言之其实是不可变数据的概念。
  而后者使用后，其实更新 state 部分的值，引用本身并无改变。
- useState 内部基于 useReducer 实现，方法返回 state 本身以及一个修改 state 的方法。
- 通过 setXXX 修改数据，不会和 setState 一样进行对象属性合并，会直接覆盖。
- Hooks 函数组件中，存在渲染闭包的概念，在一次渲染闭包中，state 是固定不变的。
- Hooks 函数组件，默认开启 类 Object.is 的浅层比较，类似默认开启 PureComponent 的优化方式。


### 为什么只能在最外层使用 hooks
- 官网：确保总是在你的 React 函数的最顶层以及任何 return 之前调用他们。
  遵守这条规则，你就能确保 Hook 在每一次渲染中都按照同样的顺序被调用。
- 因为底层存放 hooks 的是在 fiber 对象的 memoizedState 
  数组里按照 hooks 的定义顺序存入，每一个 hooks 的 next 都会指向下一个，如果 hooks 顺序变化，memoizedState 并不会感知


### 自定义的 Hook 是如何影响使用它的函数组件的？
- 共享同一个 memoizedState，共享同一个顺序。


### “Capture Value” 特性是如何产生的？
- Capture Value 概念的解释：每次 Render 的内容都会形成一个快照并保留下来，因此当状态变更而 Rerender 时，
  就形成了 N 个 Render 状态，而每个 Render 状态都拥有自己固定不变的 Props 与 State。
- 每一次 ReRender 的时候，都是重新去执行函数组件了，对于之前已经执行过的函数组件，并不会做任何操作。


### React Hooks 在平时开发中需要注意的问题和原因
- 必须始终在 React 函数的顶层使用 Hook
- 使用 useState 时候，使用 push，pop，splice 等直接更改数组对象的坑(使用解构)，this.setState 却没有问题。
- useState 设置状态值通过 props 时，只有第一次生效，后期需要更新状态，必须通过 useEffect。
- React.memo 等效于 PureComponent，它只浅比较 props。这里也可以使用 useMemo 第二个参数进行优化




# React-Router

### React-Router 的实现原理是什么？
- 客户端路由实现的思想：
  - 基于 hash 的路由：通过监听 hashchange 事件，感知 hash 的变化，改变 hash 可以直接通过 location.hash=xxx
  - 基于 H5 history 路由： 
    改变 url 可以通过 history.pushState 和 resplaceState 等，
    会将 URL 压入堆栈，同时能够应用 history.go() 等 API 监听 url 的变化可以通过自定义事件触发实现
- 基于 history 库来实现上述不同的客户端路由实现思想，并且能够保存历史记录等，磨平浏览器差异，上层无感知
- 通过维护的列表，在每次 URL 发生变化的回收，通过配置的 路由路径，匹配到对应的 Component，并且 render


### router 里 Link 标签和 a 标签有什么区别
- 从最终渲染的 DOM 来看，这两者都是链接，都是 a 标签，区别是：
   Link 是 react-router 里实现路由跳转的链接，一般配合 Route 使用，
  react-router 接管了其默认的链接跳转行为，区别于传统的页面跳转，
   Link 的“跳转”行为只会触发相匹配的 Route 对应的页面内容更新，而不会刷新整个页面。
- 而 a 标签就是普通的超链接了，用于从当前页面跳转到 href 指向的另一个页面（非锚点情况）。




# Redux

### 对 Redux 的理解
- 集中管理管理数据状态，并进行可回溯，可预测更新的 js 应用工具，单纯的 redux 是一个状态机，没有 UI 呈现。
- Redux 提供了一个叫 store 的统一仓储库，
  组件通过 dispatch 将 state 直接传入 store，不用通过其他的组件。
  并且组件通过 subscribe 从 store 获取到 state 的改变。
  使用了 Redux，所有的组件都可以从 store 中获取到所需的 state，他们也能从 store 获取到 state 的改变。
- react-redux 作用就是将 redux 的状态机和 react 的 UI 呈现绑定在一起，当你 dispatch action 改变 state 的时候，更新页面。


### Redux 的工作流程
- view -→> action -> reducer -> store
- 有中间件：view -> action -> middleware -> reducer -> store
- const store= createStore（fn）生成数据;
- action: {type: Symble('action01), payload:'payload' }定义行为;
- dispatch 发起 action：store.dispatch(doSomething('action001'));
- reducer：处理 action，返回新的 state;


### redux 什么是纯函数 为什么 reducer 是纯函数
- 因为比较两个 javascript 对象中所有的属性是否完全相同，唯一的办法就是深比较，
  然而，深比较在真实的应用中代码是非常大的，非常耗性能的，需要比较的次数特别多，
  所以一个有效的解决方案就是做一个规定，当无论发生任何变化时，
  开发者都要返回一个新的对象，没有变化时，开发者返回就的对象，
  这也就是 redux 为什么要把 reducer 设计成纯函数的原因。


### Redux 和 Vuex 有什么区别，它们的共同思想
1. Redux 和 Vuex 区别
- Vuex 改进了 Redux 中的 Action 和 Reducer 函数，以 mutations 变化函数取代 Reducer，
  无需 switch，只需在对应的 mutation 函数里改变 state 值即可
- Vuex 由于 Vue 自动重新渲染的特性，无需订阅重新渲染函数，只要生成新的 State 即可
- Vuex 数据流的顺序是 ∶View 调用 store.commit 提交对应的请求到 Store 中对应的
  mutation 函数->store 改变（vue 检测到数据变化自动渲染）
- 通俗点理解就是，vuex 弱化 dispatch，通过 commit 进行 store 状态的一次更变;
  取消了 action 概念，不必传入特定的 action 形式进行指定变更;
  弱化 reducer，基于 commit 参数直接对数据进行转变，使得框架更加简易; 2. 共同思想

- 单—的数据源
- 变化可以预测
  本质上 ∶ redux 与 vuex 都是对 mvvm 思想的服务，将数据从视图中抽离的一种方案。
