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

## 高阶函数、高阶组件、装饰器、柯里化、闭包之间的关系
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
      return func.apply(this, args);
    } else {
      return function(...args2) {
        return curried.apply(this, args.concat(args2));
      }
    }
  };

}

// 用例
function sum(a, b, c) {
    return a + b + c;
}

let curriedSum = curry(sum);

console.log( curriedSum(1, 2, 3) ); // 6，仍然可以被正常调用
console.log( curriedSum(1)(2,3) ); // 6，对第一个参数的柯里化
console.log( curriedSum(1)(2)(3) ); // 6，全柯里化

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
- 属性代理是从“组合”的角度出发，这样有利于从外部去操作 WrappedComponent，可以操作的对象是 props，或者在 WrappedComponent 外面加一些拦截器，控制器等。
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
