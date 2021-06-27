---
title: 【温故知新】Vue 下
date: 2021-05-29 23:59:59
tags:
  - 面试
  - vue
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/vue.jpeg
---

![](https://hongxh.cn/img/study_img/vue.jpeg)

### vue 生命周期
- new Vue()
- init: Events & Lifecycle
- `beforeCreate`: 在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。
- init: injections & reactivity
- `created`: 在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：
  数据观测 (data observer)，property 和方法的运算，watch/event 事件回调。
  然而，挂载阶段还没开始，$el property 目前尚不可用。
- 判断 el template render，最后都转成 ast
- `beforeMount`: 在挂载开始之前被调用：相关的 render 函数首次被调用。
- `mounted`: 实例被挂载后调用，这时 el 被新创建的 vm.$el 替换了。
  如果根实例挂载到了一个文档内的元素上，当 mounted 被调用时 vm.$el 也在文档内。
  注意 mounted 不会保证所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，
  可以在 mounted 内部使用 vm.$nextTick：
- `beforeDestroy`: 实例销毁之前调用。在这一步，实例仍然完全可用。
- `destroyed`: 实例销毁后调用。该钩子被调用后，对应 Vue 实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁。
- 更新:
  - `beforeUpdate`: 数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。
  - `update`: 由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。
    当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。
    如果要相应状态改变，通常最好使用计算属性或 watcher 取而代之。 注意 updated 不会保证所有的子组件也都一起被重绘。
    如果你希望等到整个视图都重绘完毕，可以在 updated 里使用 vm.$nextTick

### vue 渲染流程
- new Vue()：初始化
- => $mount ：调用mount 挂载组件
- => compile()：
  - parse：解析，把模板解析为 ast 的抽象语法树，ast 类似 js 对象，它可以描述所有节点（特别像 vdom ）
  - optimize：优化，对 ast 做静态节点标记，跳过指令等
  - generate：代码生成，把 ast 生成代码字符串
- => render function：渲染函数
  - getter：在创建组件的时候对 data 进行遍历，创建 watcher 添加到对应的 dep 依赖收集中
  - set 的时候执行 notify 通知 dep 对应的 update 方法
  - 然后重新打补丁 patch 生成新的 vdom ，diff 比对映射到界面中
- => render：渲染函数通过 render 执行生成 vdom 虚拟 dom
- => patch()：把 vdom 打补丁生成真实 dom
  - patchVnode
  - updateChildren
- => DOM


### vue 的双向绑定
- Data 通过 observer 转换成了 getter/setter 的形式来追踪变化。
  当外界通过 Watcher 读取数据时，会触发 getter 从而将 Watch 添加到依赖中。
  当数据发生了变化时，会触发 setter,从而向 Dep 中的依赖（即 Watcher）发送通知
  Watcher 接收到通知后，会向外界发送通知，变化通知到外界后可能会触发视图更新，
  也有可能触发用户的某个回调函数等。


### 为什么重写数组方法
- 在 Vue 现有阶段中，对响应式处理利用的是 Object.defineProperty 对数据进行拦截，
  而这个方法并不能监听到数组内部变化，数组长度变化，数组的截取变化等，
  所以我们需要对这些操作进行 hack，让 vue 能监听到其中的变化。
- 为什么在 Vue3.0 采用了 Proxy，抛弃了 Object.defineProperty？
  原因如下：
  Object.defineProperty 无法低耗费的监听到数组下标的变化，导致通过数组下标添加元素，不能实时响应；
  Object.defineProperty 只能劫持对象的属性，从而需要对每个对象，每个属性进行遍历。如果属性值是对象，
  还需要深度遍历。 Proxy 可以劫持整个对象， 并返回一个新的对象。
  Proxy 不仅可以代理对象，还可以代理数组。还可以代理动态增加的属性。


### vue2 响应原理的弊端
- 响应化过程需要递归遍历，消耗较大；
- 属性的添加和删除无法监听；
- 数组响应化需要额外实现；
- map、set、class 等无响应式；
- 修改语法有限制；


### vue3 composition api 和 react 中 hooks 区别
- React 数据更改的时候，会导致重新 render，重新 render 又会重新把 Hooks 重新注册一次，所以 React 的上手难度更高一些
- Vue Hook 只会被注册调用一次，Vue 能避开这些麻烦的问题，原因在于它对数据的响应是基于 proxy 的，
  对数据直接代理观察。这种场景下，只要任何一个更改 data 的地方，
  相关的 function 或者 template 都会被重新计算，因此避开了 React 可能遇到的性能上的问题
- Vue Composition API 的 setup() 晚于 beforeCreate 钩子，早于 created 钩子被调用
- React Hooks 会在组件每次渲染时候运行，而 Vue setup() 只在组件创建时运行一次


### 口述 diff
- diff 是什么：算法
- diff 的来由：对比出差异
- diff 怎么做的：
