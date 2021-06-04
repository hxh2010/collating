---
title: 【温故知新】React 下
date: 2021-05-27 00:00:00
tags:
  - 面试
  - react
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/react.png
---

![](https://hongxh.cn/img/study_img/react.png)

### React 事件系统
- React 并不是将 click 事件绑定到了 div 的真实 DOM 上，而是在 document 处监听了所有的事件，
  当事件发生并且冒泡到 document 处的时候，React 将事件内容封装并交由真正的处理函数运行。
  这样的方式不仅仅减少了内存的消耗，还能在组件挂在销毁时统一订阅和移除事件。
- 在底层抹平了不同浏览器的差异，在上层面向开发者暴露统一的、稳定的、与 DOM 原生事件相同的事件接口。
- React 合成事件和 DOM 原生事件混用，先执行原生事件，再去执行合成事件  
- 从 v17 开始，e.persist() 将不再生效，因为 SyntheticEvent 不再放入事件池中。
- React 事件系统有独特合成事件，也有自己的事件源，而且还有对一些特殊情况的处理逻辑，比如冒泡逻辑等。
  React 的冒泡并不是原生的冒泡，所以阻止冒泡使用 stopPropagation() 无效，需要遵照 react 的原则实现，
  从 v0.14 开始，事件处理器返回 false 时，不再阻止事件传递。你可以酌情手动调用 e.stopPropagation() 或 e.preventDefault() 作为替代方案。
- React 为了防止每次事件都创建事件源对象，浪费性能，所以引入了事件池概念，
  每一次用户事件都会从事件池中取出一个 e，如果没有，就创建一个，然后赋值事件源，等到事件执行之后，重置事件源，放回事件池，借此做到复用。
  

# 渲染更新流程
### React 15主要流程：
1. Reconciler(协调器); 负责找出变化的组件,在 React 中可以
   通过 setState、forceUpdate、ReactDOM.render 来触发更新。
   每当有更新发生时，Reconciler 会做如下工作：
   - 调用组件的 render 方法，将返回的 JSX 转化为虚拟 DOM
   - 将虚拟 DOM 和上次更新时的虚拟 DOM 对比
   - 通过对比找出本次更新中变化的虚拟 DOM
   - 通知 Renderer 将变化的虚拟 DOM 渲染到页面上
2. Renderer(渲染器); 负责将变化的组件渲染到页面上,
   在对某个更新节点执行玩 Reconciler 之后，会通知 Renderer 根据不同的"宿主环境"进行相应的节点渲染/更新。

### React 15 的缺陷
- React15 架构不能支撑异步更新
- React 15 的 diff 过程是 递归执行更新 的。由于是递归，一旦开始就"无法中断" 。
  当层级太深或者 diff 逻辑(钩子函数里的逻辑)太复杂，导致递归更新的时间过长，
  Js 线程一直卡主，那么用户交互和渲染就会产生卡顿。


### React 16 主要流程
#### 三层结构
1. Scheduler(调度器);调度任务的优先级，高优任务优先进入 Reconciler
2. Reconciler(协调器);负责找出变化的组件
3. Renderer(渲染器);负责将变化的组件渲染到页面上

#### 主要流程
- reactDOM.render/setState/forceUpdate 执行之后，createUpdate 
  创建 update 存入 Fiber.updateQueue 里面，然后去执行 scheduleWork 进行调度 。
  React 渲染流程可以分为 Scheduler、Reconciliation、Commit 这三个阶段
  
##### Scheduler 阶段
1. Scheduler 进行的第一步是 addRootToScheduler，因为可能有多个 root 节点； 
2. 判断是否在 render 阶段或前后 root 不同； 
3. 执行 requestWork，判断 sync 同步的还是异步的，同步没有 deadline，执行 performSyncWork； 
4. 如果是异步的进行异步的调度，利用浏览器的 requestIdeaCallback 在碎片时间进行更新，因为兼容性等问题，
   react 内部自己模拟实现了这个 api，内部通过判断 deadline 是否超时进行执行。最后调用 performAsyncWork。 
5.同步或异步之后执行 perforWork 汇集，根据是否有 deadline 对 Fibre 树进行更新。

##### Reconciliation 阶段
- Fiber Reconciler：在子节点的渲染过程中执行优先级更高的操作，
  只能保留调用栈中子节点的渲染及子节点之前节点的渲染，
  这样是很复杂的，这种调和/渲染也叫做 Stack Reconciler。

##### Fiber
- Fiber 使用链表的结构去渲染节点，每一个节点都称之为 Fiber Node，
  每个节点会有三个属性： child 指向第一个子节点 、sibling 指向兄弟节点 、return 指向父节点
- Fiber 的渲染方式：从父节点开始，向下依次遍历子节点，深度优先渲染完子节点后，再回到其父节点去检查是否有兄弟节点，
  如果有兄弟节点，则从该兄弟节点开始继续深度优先的渲染，直到回退到根节点结束。
  重复遍历的节点并不会重复渲染，而是为了取到下一个可能需要渲染的节点。
  - 实际上，真实的 DOM 渲染过程是 diff 两棵 Fiber 节点树（current 和 workInProgress）得到 effect list，在 commit 阶段执行。
  - 双缓冲树：React 用 workInProgress 树(内存中构建的树) 和 current(渲染树) 来实现更新逻辑。
    我们 console.log 打印的 fiber 都是在内存中即将 workInProgress 的 fiber 树。
    双缓存一个在内存中构建，在下一次渲染的时候，直接用缓存树做为下一次渲染树，上一次的渲染树又作为缓存树，
    这样可以防止只用一颗树更新状态的丢失的情况，又加快了 dom 节点的替换与更新。
- Fiber 的更新机制：在一次更新中，首先会获取 current 树的 alternate 作为当前的 workInProgress，渲染完毕后，
  workInProgress 树变为 current 树。我们用如上的树 A 和树 B 和已经保存的 baseState 模型，来更形象的解释了更新机制 。 
  hooks 中的 useState 进行 state 对比，用的是缓存树上的 state 和当前最新的 state。
  所有就解释了为什么更新相同的 state，函数组件执行 2 次了。

##### commit 阶段
- 从字面意思来就可以知道， commit 阶段是将调和阶段的更新进行提交，即把更新操作反映到真实的 DOM 上。同时，commit 阶段是同步执行，不可被中断。
- Effect：Effect 定义了 Fiber Node 在 commit 阶段要做的事情

##### render 流程总结
- render 会调用 legacyRenderSubtreeIntoContainer 方法 。legacyRenderSubtreeIntoContainer 中， 
  如果是第一次渲染，会先初始化 FiberRoot，其为应用的起点。同时生成根节点的 Fiber 实例。
  这里 FiberRoot.current = Fiber; Fiber.stateNode = FiberRoot。 
  调用 updateContainer 会计算出此次更新的过期时间。并生成任务对象 update，
  将其插入 Fiber 中的更新队列，然后调用 scheduleUpdateOnFiber 触发任务调度 
  scheduleUpdateOnFiber 会更新以该 Fiber 节点为根节点的整棵 Fiber 树的过期时间。然后调用 ensureRootIsScheduled 进行调度 
  ensureRootIsScheduled 中会绑定任务与具体执行函数。然后交由 Scheduler 处理
