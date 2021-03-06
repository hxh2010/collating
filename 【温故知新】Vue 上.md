---
title: 【温故知新】Vue 上
date: 2021-05-29 00:00:00
tags:
  - 面试
  - vue
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/vue.jpeg
---

![](https://hongxh.cn/img/study_img/vue.jpeg)

# Vue

### 组件通信
- 父子：prop 、事件
- 祖先后代：provide(使用类似 data)/inject(使用类似 mixin)
- 兄弟：利用共同祖辈搭桥$parent或$root，使用方法： $parent.$on(name, fn)/$parent.$emit(name, args)
- refs: 获取子组件引用  
- $attrs/$listeners
  - $attrs: 排除class 和 style 之外的在其它属性
  - $listeners: 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用
```html
<!--v-bind 可以把 attrs 展开-->
<div v-bind="$attrs"></div>
```
- 兄弟(任意两个组件)：eventbus、vuex
```javascript
// bus 事件派发、监听和回调管理
// 经典的发布订阅模式
class Bus {
  constructor(){
    this.callbacks = {}
  }
  
  $on(name, fn){
    this.callbacks[name] = this.callbacks[name] || []
    this.callbacks[name].push(fn)
  }
  
  $emit(name, args){
    if(this.callbacks[name]){
      this.callbacks[name].forEach(d=>{
        d(args)
      })
    }
  }
}
```


### 插槽
- 匿名
- 具名
- 作用域
```html
<!--son-->
<div>
  <!--匿名-->
  <slot></slot>
  <!--具名-->
  <slot name="header"></slot>
  <!--作用域-->
  <slot name="footer" :hello="helloContent"></slot>
</div>

<!--parent-->
<Son>
  <!--匿名-->
  <template>hello</template>
  <!--具名-->
  <template v-slot:header>hello</template>
  <!--作用域-->
  <template v-slot:footer="ctx">{{ctx.hello}}</template>
</Son>
```


### 弹窗的实现
- 完成弹窗页面 com.vue
- vue.extend 创建构造函数: const Con = Vue.extent(com)
- new Con() 创建实例，得到虚拟 dom: const vm = new Con({propsData})
- 挂载得到真实 dom，注意不要指定挂载点: vm.$mount()  
- vm.$el 可获取组件真实 dom， 然后 append 到页面: document.body.appendChild(vm.$el)
- 添加remove 方法：vm.remove = function(){document.body.removeChild(vm.$el);vm.$destroy}


### MVVM和MVC的区别
- MVC：Model、View 和 Controller
- MVVM：Model、View 和 ViewModel


### 常见的事件修饰符
- click.stop - 调用 event.stopPropagation() 阻止冒泡
- click.prevent - 调用 event.preventDefault() 阻止默认行为
- click.self - 只触发自身，不包含子元素。
- click.once - 只触发一次回调。
- click.native – 监听原生事件


### 项目部署子路径配置
- 修改静态文件的路径：assetsPublicPath


### V-model 实现
- 子组件通过 v-bind:value 加上 v-on:input 实现


### V-bind 绑定动态参数
- 中括号


### Computed 计算属性
1. 赋值：Setter 方法
2. 特点：
  - 它支持缓存，只有依赖的数据发生了变化，才会重新计算
  - 不支持异步，当Computed中有异步操作时，无法监听数据的变化
  - computed的值会默认走缓存，计算属性是基于它们的响应式依赖进行缓存的，
    也就是基于data声明过，或者父组件传递过来的props中的数据进行计算的。
  - 如果一个属性是由其他属性计算而来的，这个属性依赖其他的属性，一般会使用computed
  - 如果computed属性的属性值是函数，那么默认使用get方法，函数的返回值就是属性的属性值；
    在computed中，属性有一个get方法和一个set方法，当数据发生变化时，会调用set方法。
    
    
### Watch
- 它不支持缓存，数据变化时，它就会触发相应的操作 
- 支持异步监听 
- 监听的函数接收两个参数，第一个参数是最新的值，第二个是变化之前的值 
- 当一个属性发生变化时，就需要执行相应的操作 
- 监听数据必须是data中声明的或者父组件传递过来的props中的数据，当发生变化时，会出大其他操作，函数有两个的参数：
  - immediate：组件加载立即触发回调函数
  - deep：深度监听，发现数据内部的变化，在复杂数据类型中使用，例如数组中的对象发生变化。
    需要注意的是，deep无法监听到数组和对象内部的变化。
  - watch 可以监听对象的单个属性  
  
  
### Computed 计算属性和 Methods 方法的区别
- 同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的
- 计算属性是基于它们的响应式依赖进行缓存的，方法访问多少次就执行多少次


### Watch 和 Computed 的区别
- computed 只有当依赖的数据变化时才会计算, 当数据没有变化时, 它会读取缓存数据。 
- watch 每次都需要执行函数。 watch 更适用于数据变化时的异步操作。


### keep-alive
- 用法：如果需要在组件切换的时候，保存一些组件的状态防止多次渲染，就可以使用 keep-alive 组件包裹需要保存的组件。
- keep-alive 属性：include 字符串或正则表达式，只有名称匹配的组件会被匹配；exclude 字符串或正则表达式。任何名称匹配的组件都不会被缓存；max 数字。最多可以缓存多少组件实例；
- keep-alive 生命周期：activated、deactivated  
- 步骤
  - 获取 keep-alive 下第一个子组件的实例对象，通过他去获取这个组件的组件名
  - 通过当前组件名去匹配原来 include 和 exclude，判断当前组件是否需要缓存，不需要缓存，直接返回当前组件的实例vNode 
  - 需要缓存， 判断他当前是否在缓存数组里面： 存在，则将他原来位置上的 key 给移除，同时将这个组件的 key 放到数组最后面（LRU） 
  - 不存在，将组件 key 放入数组，然后判断当前 key数组是否超过 max 所设置的范围，超过，那么削减未使用时间最长的一个组件的 key 最后将这个组件的 keepAlive 设置为 true


### $nextTick 原理及作用
- nextTick 的核心是利用了如 Promise 、MutationObserver、setImmediate、setTimeout的原生 JavaScript 方法来模拟对应的微/宏任务的实现，
  本质是为了利用 JavaScript 的这些异步回调任务队列来实现 Vue 框架中自己的异步回调队列。
  

### Vue 中给 data 中的对象属性添加一个新的属性时会发生什么
- 给 obj 添加 b 属性 ，obj.b 可以成功添加，但是视图并未刷新。
  这是因为在Vue实例创建时，obj.b并未声明，因此就没有被Vue转换为响应式的属性，自然就不会触发视图的更新，
  这时就需要使用Vue的全局 api $set()


### Vue template 到 render 的过程
- vue的模版编译过程主要如下：template -> ast -> render函数


### Vue自定义指令
- 全局定义：Vue.directive("focus",{})
- 局部定义：directives:{focus:{}}
- 钩子函数
  - bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
  - inSerted：被绑定元素插入父节点时调用（仅保证父节点存在，但不一定已被插入文档中）。
  - update：所在组件的VNode更新时调用，但是可能发生在其子VNode更新之前调用。指令的值可能发生了改变，也可能没有。但是可以通过比较更新前后的值来忽略不必要的模板更新。
  - ComponentUpdate：指令所在组件的 VNode及其子VNode全部更新后调用。
  - unbind：只调用一次，指令与元素解绑时调用。


### assets 和 static 的区别
- 相同点： assets 和 static 两个都是存放静态资源文件。项目中所需要的资源文件图片，字体图标，样式文件等都可以放在这两个文件下，这是相同点
- 不相同点：assets 中存放的静态资源文件在项目打包时，
  也就是运行 npm run build 时会将 assets 中放置的静态资源文件进行打包上传，所谓打包简单点可以理解为压缩体积，代码格式化。
  而压缩后的静态资源文件最终也都会放置在 static 文件中跟着 index.html 一同上传至服务器。
  static 中放置的静态资源文件就不会要走打包压缩格式化等流程，而是直接进入打包好的目录，直接上传至服务器。
  因为避免了压缩直接进行上传，在打包时会提高一定的效率，
  但是 static 中的资源文件由于没有进行压缩等操作，所以文件的体积也就相对于 assets 中打包后的文件提交较大点。
  在服务器中就会占据更大的空间。
  

### V-for 和 v-if 优先级
- v-for 优先级更高


### v-if、v-show、v-html 的原理
- v-if会调用addIfCondition方法，生成vnode的时候会忽略对应节点，render的时候就不会渲染；
- v-show会生成vnode，render的时候也会渲染成真实节点，只是在render过程中会在节点的属性中修改show属性值，也就是常说的display；
- v-html会先移除节点下的所有节点，调用html方法，通过addProp添加innerHTML属性，归根结底还是设置innerHTML为v-html的值。


### v-if和v-show的区别
- 手段：v-if是动态的向DOM树内添加或者删除DOM元素；v-show是通过设置DOM元素的display样式属性控制显隐；
- 编译过程：v-if切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；v-show只是简单的基于css切换；
- 编译条件：v-if是惰性的，如果初始条件为假，则什么也不做；只有在条件第一次变为真时才开始局部编译; v-show是在任何条件下，无论首次条件是否为真，都被编译，然后被缓存，而且DOM元素保留；
- 性能消耗：v-if有更高的切换消耗；v-show有更高的初始渲染消耗；
- 使用场景：v-if适合运营条件不大可能改变；v-show适合频繁切换。


### vue 性能优化
- 尽量减少data中的数据，data中的数据都会增加getter和setter，会收集对应的watcher
- v-if和v-for不能连用
- 如果需要使用v-for给每项元素绑定事件时使用事件代理
- SPA 页面采用keep-alive缓存组件
- 在更多的情况下，使用v-if替代v-show
- key保证唯一
- 使用路由懒加载、异步组件
- 防抖、节流
- 第三方模块按需导入
- 长列表滚动到可视区域动态加载
- 图片懒加载


### template和jsx的有什么分别
- template和jsx的都是render的一种表现形式
- 不同的是：JSX相对于template而言，具有更高的灵活性，在复杂的组件中，更具有优势，而 template 虽然显得有些呆滞。
  但是 template 在代码结构上更符合视图与逻辑分离的习惯，更简单、更直观、更好维


### vue初始化页面闪动问题
- v-cloak


### eventBus事件总线
- new Vue() 创建事件中心，$emit / $on


### provide / inject
- project 钩子用来发送数据或方法
- inject钩子用来接收数据或方法
````javascript
// 父
{
  provide:() => {
   return {
      num: this.num
   };
  }
}
// 子
{inject: ['num']}
````


### $attrs / $listeners
- $attrs：继承所有的父组件属性（除了prop传递的属性、class 和 style ），一般用在子组件的子元素上
- $listeners：该属性是一个对象，里面包含了作用在这个组件上的所有监听器，
  可以配合 v-on="$listeners" 将所有的事件监听器指向这个组件的某个特定的子元素。
  （相当于子组件继承父组件的事件）



# Vue-route
### hash 和 history 原理及区别
- 同 react


### Vue-router跳转和location.href有什么区别
- 静态跳转，刷新跳转，vue-router 可对相关路由模式进行实现


### params 和 query 的区别
- 用法：query要用path来引入，params要用name来引入，
  接收参数都是类似的，分别是 this.$route.query.name 和 this.$route.params.name 。
- url地址显示：query更加类似于我们ajax中get传参，
  params则类似于post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示


### Vue-router 导航守卫有哪些
- 全局前置/钩子：beforeEach、beforeResolve、afterEach
- 路由独享的守卫：beforeEnter
- 组件内的守卫：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave



# VueX

### vuex
- Vue Components∶ Vue组件。HTML页面上，负责接收用户操作等交互行为，执行dispatch方法触发对应action进行回应。
- dispatch∶操作行为触发方法，是唯一能执行action的方法。
- actions∶ 操作行为处理模块。负责处理Vue Components接收到的所有交互行为。
  包含同步/异步操作，支持多个同名方法，按照注册的顺序依次触发。向后台API请求的操作就在这个模块中进行，
  包括触发其他action以及提交mutation的操作。该模块提供了Promise的封装，以支持action的链式触发。
- commit∶状态改变提交操作方法。对mutation进行提交，是唯一能执行mutation的方法。
- mutations∶状态改变操作方法。是Vuex修改state的唯一推荐方法，其他修改方式在严格模式下将会报错。
  该方法只能进行同步操作，且方法名只能全局唯一。操作之中会有一些hook暴露出来，以进行state的监控等。
- state∶ 页面状态管理容器对象。集中存储Vuecomponents中data对象的零散数据，
  全局唯一，以进行统一的状态管理。页面显示所需的数据从该对象中进行读取，
  利用Vue的细粒度数据响应机制来进行高效的状态更新。
- getters∶ state对象读取方法。图中没有单独列出该模块，应该被包含在了render中，Vue Components通过该方法读取全局state对象。


### 双向绑定和 vuex 是否冲突
- 当在严格模式中使用 `Vuex` 时，在属于 `Vuex` 的 `state` 上使用 `v-model` 会导致出错。
- 解决方案：1. 给 `<Input>` 中绑定 value，然后侦听 `input` 或者 `change` 事件，
  在事件回调中调用一个方法；2. 使用带有 `setter` 的双向绑定计算属性；
