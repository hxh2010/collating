---
title: 【温故知新】JavaScript 下
date: 2021-05-24 00:00:00
tags:
  - 面试
  - javascript
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/js.jpg
---

![](https://hongxh.cn/img/study_img/js.jpg)

# 数据结构-Data Structures

#### 数组（Array） 
- 是一种很常见的数据结构。它是由相同类型的元素（element）的集合所组成，并且被分配一块连续的内存来存储（与链表对比）。 
  利用元素的索引（index）可以计算出该元素对应的存储地址。它的特点是提供随机访问并且容量有限。
  
#### 链表
- 链表（LinkedList） 利用零散的内存空间存储数据，链表在插入和删除的时候可以达到 O(1) 的复杂度，比数组快，
  但是查找一个节点或者访问特定编号的节点则需要 O(n) 的时间，而顺序表相应的时间复杂度分别是O(logn) 和O(1)。 
  使用链表结构可以克服数组需要预先知道数据大小的缺点，链表结构可以充分利用计算机内存空间,实现灵活的内存动态管理。
  但链表不会节省空间，相比于数组会占用更多的空间，因为链表中每个节点存放的还有指向其他节点的指针。
  链表不具有数组随机读取的优点，但是插入删除元素的时间复杂度为O(1)
- 链表分类：
  - 单链表：属性只有 val next
  - 双向链表：属性有 val next prev
  - 循环链表：一种单链，最后一个元素指向 head
  - 双向循环链表：双链，最后一个元素指向 head
- 数组 vs 链表
  - 数组使用的是连续内存空间对CPU的缓存机制友好，链表则相反。
  - 数组的大小固定，声明之后就要占用所需的连续内存空间。如果声明的数组过小的话，需要再申请一个更大的内存空间，然后将原数组拷贝进去。
    数组多的情况下，这将是非常耗时的。链表则天然支持动态扩容。

#### 栈（后进先出）
- 栈 (stack)只允许在有序的线性数据集合的一端（称为栈顶 top）进行加入数据（push）和移除数据（pop）。
  因而按照 后进先出（LIFO, Last In First Out） 的原理运作。在栈中，push 和 pop 的操作都发生在栈顶。 
  栈常用一维数组或链表来实现，用数组实现的队列叫作 顺序栈 ，用链表实现的队列叫作 链式栈 。
  
#### 队列（先进先出）
- 队列 是 先进先出( FIFO，First In, First Out) 的线性表。在具体应用中通常用链表或者数组来实现，
  用数组实现的队列叫作 顺序队列 ，用链表实现的队列叫作 链式队列 。队列只允许在后端（rear）进行插入操作也就是 
  入队 enqueue，在前端（front）进行删除操作也就是出队 dequeue
  
#### 散列表(Hash)
- 散列表，也叫哈希表，是根据关键码和值 (key和value) 直接进行访问的数据结构，通过key和value来映射到集合中的一个位置，这样就可以很快找到集合中的对应元素。

#### 堆（Heap）
- 堆是一种比较特殊的数据结构，可以被看做一棵树的数组对象，具有以下的性质： 堆中某个节点的值总是不大于或不小于其父节点的值； 堆总是一棵完全二叉树。

#### 二叉树
- 二叉树（binary tree）是指树中节点的度不大于2的有序树，它是一种最简单且最重要的树。

#### 总结
- 数组与链表：单 / 双向链表、跳舞链
- 栈与队列
- 树与图：最近公共祖先、并查集
- 哈希表
- 堆：大 / 小根堆、可并堆
- 字符串：字典树、后缀树

# 算法-Algorithms
#### 排序算法：快速排序、归并排序、计数排序
```javascript
// 快速排序
const quick = (arr) => {
  // 递归出口
  if (arr.length <= 1) {
    return arr;
  }
  let mid = Math.ceil(arr.length/2);
  let temp = arr[mid];
  let left = [];
  let right = [];

  for (var i = 0; i < arr.length; i++) {
    if (arr[i] > temp){
      right.push(arr[i])
    }else if (arr[i] < temp) {
      left.push(arr[i])
    }
  }
  return [...quick(left), temp, ...quick(right)]
}

// 归并排序
const mergeSort = (arr) => {
  const merge = (left, right) => {
    const result = [];
    while (left.length && right.length) {
      if (left[0] <= right[0]) {
        result.push(left.shift());
      } else {
        result.push(right.shift());
      }
    }
    while (left.length) result.push(left.shift());
    while (right.length) result.push(right.shift());
    return result;
  };

  const mergeSon = (list) => {
    const len = list.length;
    if (len < 2) {
      return list;
    }
    const middle = Math.floor(len / 2);
    const left = list.slice(0, middle);
    const right = list.slice(middle);
    return merge(mergeSon(left), mergeSon(right));
  };

  return mergeSon(arr);
};
```
#### 搜索算法：回溯、递归、剪枝技巧
#### 图论：最短路、最小生成树、网络流建模
#### 动态规划：背包问题、最长子序列、计数问题
#### 基础技巧：分治、倍增、二分、贪心





# 高阶函数、高阶组件、装饰器、柯里化、闭包之间的关系
- 高阶函数与高阶组件是一样的，只不过高阶组件接收的是组件，而高阶函数可以接收任意函数
- 修饰器是高阶函数或者高阶组件的另一个更为优雅的写法
- 柯里化是一种关于函数的高阶技术
- 闭包是柯里化的一种应用

### 1. 闭包
- 在 JavaScript 中，根据词法作用域的规则，内部函数总是可以访问其外部函数中声明的变量。
  当通过调用一个外部函数返回一个内部函数后，即使该外部函数已经执行结束了，但是内部函数引用外部函数的变量依然保存在内存中，
  我们就把这些变量的集合称为闭包。
- 作用：闭包的作用就是防止局部变量被回收
- 好处：
  - 缓存。将变量隐藏起来不被 GC 回收。
  - 实现柯里化。利用闭包特性完成柯里化。
- 坏处：
  - 内存消耗。闭包产生的变量无法被销毁。
  - 性能问题。由于闭包内部变量优先级高于外部变量，所以需要多查找作用域链的一个层次，一定程度影响查找速度。
- 简单实现
```javascript
function foo() {
  let num = 0;
  return function() {
    num++;
    console.log(num);
  };
}
const f = foo();
f(); // 1
f(); // 2
```


### 2. 柯里化
- 柯里化 是一种转换，将 f(a,b,c) 转换为可以被以 f(a)(b)(c) 的形式进行调用。
  JavaScript 实现通常都保持该函数可以被正常调用，并且如果参数数量不足，则返回偏函数。
  柯里化让我们能够更容易地获取偏函数。就像我们在日志记录示例中看到的那样，
  普通函数 log(date, importance, message) 在被柯里化之后，当我们调用它的时候传入一个参数（如 log(date)）
  或两个参数（log(date, importance)）时，它会返回偏函数。

1. 柯里化
- 柯里化（Currying）是一种关于函数的高阶技术。它不仅被用于 JavaScript，还被用于其他编程语言。
  柯里化是一种函数的转换，它是指将一个函数从可调用的 f(a, b, c) 转换为可调用的 f(a)(b)(c)。
  柯里化不会调用函数。它只是对函数进行转换。

```javascript
function curry(f) { // curry(f) 执行柯里化转换
  return function(a) {
    return function(b) {
      return f(a, b);
    };
  };
}

// 用法
function sum(a, b) {
  return a + b;
}

let curriedSum = curry(sum);

alert( curriedSum(1)(2) ); // 3
```

- 柯里化更高级的实现，例如 lodash 库的 _.curry，会返回一个包装器，该包装器允许函数被正常调用或者以偏函数（partial）的方式调用：

```javascript
function sum(a, b) {
  return a + b;
}

let curriedSum = _.curry(sum); // 使用来自 lodash 库的 _.curry

alert( curriedSum(1, 2) ); // 3，仍可正常调用
alert( curriedSum(1)(2) ); // 3，以偏函数的方式调用
```

2. 高级柯里化实现

```javascript
function curry(func) {
  return function curried(...args) {
    if (args.length >= func.length) {
      return func.apply({}, args);
    }
    return (...args2) => {
      return curried.apply({}, args.concat(args2));
    };
  };
}

const add = (a, b, c) => {
  return a + b + c;
};

const computed = curry(add);
console.log(computed(1, 2)(3));
console.log(computed(1)(2)(3));
console.log(computed(1, 2, 3));
console.log(computed(1)(2, 3));

function curryAdd(func) {
  let argArr = [];
  return function temp(...args) {
    if (!args.length) {
      const result = func.apply({}, argArr);
      argArr = [];
      return result;
    }
    argArr = argArr.concat(args);
    return temp;
  };
}

function addAll(...args) {
  return args.reduce((a, b) => a + b);
}
const curryAddAll = curryAdd(addAll);
const curryAddAll2 = curryAdd(add);
console.log(curryAddAll2(1, 2, 19)(11)());
console.log(curryAddAll(1)(2)(3)(4, 5, 5)());
console.log(curryAddAll(1)(2)(3)(4, 5, 5, 1)());
console.log(curryAddAll(1)(2)(3)(4, 5, 5, 1, 2, 3)(1)());

// 反柯里化
function uncurry(fn) {
  var args = [].slice.call(arguments, 1);

  return function() {
    var arg = [].slice.call(arguments);

    args = args.concat(arg);

    return fn.apply(this, args);
  }
}

var uncurryAdd = uncurry(curriedSum);
console.log(uncurryAdd(1, 2, 3)); // 6
```


### 3. 装饰器
- 装饰器是对类、函数、属性之类的一种装饰，可以针对其添加一些额外的行为。装饰器的本质是函数。

### 4. 高阶函数 （Higher-order function）
- 接收其它函数作为参数的函数就称之为高阶函数。常用的高阶函数有： map, sort, reduce, filter等等

### 5. 高阶组件（Higher-order component）
- 接收一个组件，并返回一个外包组件。
```javascript
import React from 'react';

const hoc = WrappedComponent => {
    console.log('load base');
    return class extends React.Component {
        // 很多业务逻辑
        render() {
            return <WrappedComponent {...this.props}/>
        }
    }
}

class Base extends React.Component {
  render() {
    return (
      <div> component </div>
    )
  }
}
export default hoc(Base);
```
#### 高阶组件的实现有两种方式：属性代理，反向继承
##### 属性代理
- 操作 props
- 抽象 state
- 获取 refs
- 通过 props 进行条件渲染
- 用其实元素包裹传入的组件
##### 反向继承
- 反向继承指的是使用一个函数接受一个组件作为参数传入，并返回一个继承了该传入组件的类组件， 且在返回组件的 render() 方法中返回 super.render() 方法，最简单的实现如下：
- 劫持原组件的生命周期方法
- 读取、操作原组合的state
- 渲染劫持
##### 总结  
- 属性代理是从“组合”的角度出发，这样有利于从外部去操作 WrappedComponent，
  可以操作的对象是 props，或者在 WrappedComponent 外面加一些拦截器，控制器等。
- 反向继承则是从“继承”的角度出发，是从内部去操作 WrappedComponent，也就是可以操作组件内部的 state ，生命周期，render函数等等。
- 通过反向继承方法实现的高阶组件相较于属性代理实现的高阶组件，功能更强大，个性化程度更高，因此能适应更多的场景。  

```javascript
// 属性代理
// 返回一个无状态的函数组件
function HOC(WrappedComponent) {
  const newProps = { type: 'HOC' };
  return props => <WrappedComponent {...props} {...newProps}/>;
}

// 返回一个有状态的 class 组件
function HOC(WrappedComponent) {
  return class extends React.Component {
    render() {
      const newProps = { type: 'HOC' };
      return <WrappedComponent {...this.props} {...newProps}/>;
    }
  };
}
```

```javascript
// 反向继承
function HOC(WrappedComponent){
  const didMount = WrappedComponent.prototype.componentDidMount;
  
  // 继承了传入组件
  return class HOC extends WrappedComponent {
    // 注意：这里将重写 componentDidMount 方法
    componentDidMount(){
      // 劫持原组件的生命周期方法
      if (didMount) {
        didMount.apply(this);
      }

      // 读取、操作原组合的state，将 state 中的 number 值修改成 2
      this.setState({ number: 2 });
    }
  
    render(){
      //使用 super 调用传入组件的 render 方法
      return super.render();

      // 另一种实现， 渲染劫持
      if (this.props.isRender) {
        return super.render();
      } else {
        return <div>暂无数据</div>;
      }
    }
  }
}
```
