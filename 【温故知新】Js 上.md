---
title: 【温故知新】JavaScript 上
date: 2021-05-23 00:00:00
tags:
  - 面试
  - javascript
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/js.jpg
---

![](https://hongxh.cn/img/study_img/js.jpg)

### 手写 promise 
```javascript
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED';
const REJECTED = 'REJECTED';

class Promise {
  constructor(executor) {
    this.status = PENDING;
    this.value = undefined;
    this.reason = undefined;
    // 存放成功的回调
    this.onResolvedCallbacks = [];
    // 存放失败的回调
    this.onRejectedCallbacks= [];

    let resolve = (value) => {
      if(this.status ===  PENDING) {
        this.status = FULFILLED;
        this.value = value;
        // 依次将对应的函数执行
        this.onResolvedCallbacks.forEach(fn=>fn());
      }
    }

    let reject = (reason) => {
      if(this.status ===  PENDING) {
        this.status = REJECTED;
        this.reason = reason;
        // 依次将对应的函数执行
        this.onRejectedCallbacks.forEach(fn=>fn());
      }
    }

    try {
      executor(resolve,reject)
    } catch (error) {
      reject(error)
    }
  }

  then(onFulfilled, onRejected) {
    if (this.status === FULFILLED) {
      onFulfilled(this.value)
    }

    if (this.status === REJECTED) {
      onRejected(this.reason)
    }

    if (this.status === PENDING) {
      // 如果promise的状态是 pending，需要将 onFulfilled 和 onRejected 函数存放起来，等待状态确定后，再依次将对应的函数执行
      this.onResolvedCallbacks.push(() => {
        onFulfilled(this.value)
      });

      // 如果promise的状态是 pending，需要将 onFulfilled 和 onRejected 函数存放起来，等待状态确定后，再依次将对应的函数执行
      this.onRejectedCallbacks.push(()=> {
        onRejected(this.reason);
      })
    }
  }
}


// all
Promise.prototype.all = function (list) {
  return new Promise((resolve, reject) => {
    let resValues = [];
    let counts = 0;
    for (let [i, p] of list) {
      resolve(p).then(res => {
        counts++;
        resValues[i] = res;
        if (counts === list.length) {
          resolve(resValues)
        }
      }, err => {
        reject(err)
      })
    }
  })
}
```

### 以下输出
```javascript
  var a = { n: 1};
  var b = a;
  a.x = a = { n: 2 }; //方式1
  console.log(a, b)

  var c = { n: 1};
  var d = c;
  c.x = d = { n: 2 }; //方式2
  console.log(c, d)

  var e = { n: 1};
  var f = e;
  e.x = f.x = { n: 2 }; //方式3
  console.log(e, f)

  var g = { n: 1};
  var h = g;
  g = h.x = { n: 2 }; //方式4
  console.log(g, h)

  var i = { n: 1};
  var j = i;
  i = j.x = { n: 2 }; //方式5
  console.log(i, j)
```


### 其他值到数字值的转换规则
- 为了将值转换为相应的基本类型值，抽象操作 ToPrimitive 会首先（通过内部操作 DefaultValue）检查该值是否有valueOf()方法。
  如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用 toString() 的返回值（如果存在）来进行强制类型转换。

- Undefined 类型的值转换为 NaN。
- Null 类型的值转换为 0。
- Boolean 类型的值，true 转换为 1，false 转换为 0。
- String 类型的值转换如同使用 Number() 函数进行转换，如果包含非数字值则转换为 NaN，空字符串为 0。
- Symbol 类型的值不能转换为数字，会报错。
- 对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。



### 介绍 JavaScript 的原型，原型链？有什么特点？
- 原型：
  - JavaScript 的所有对象中都包含了一个 `__proto__` 内部属性，这个属性所对应的就是该对象的原型
  - JavaScript 的函数对象，除了原型 `__proto__` 之外，还预置了 prototype 属性
  - 当函数对象作为构造函数创建实例时，该 prototype 属性值将被作为实例对象的原型 `__proto__`。
- 原型链：
  - 当一个对象调用的属性/方法自身不存在时，就会去自己 `__proto__` 关联的前辈 prototype 对象上去找
  - 如果没找到，就会去该 prototype 原型 `__proto__` 关联的前辈 prototype 去找。依次类推，直到找到属性/方法或 undefined 为止。从而形成了所谓的“原型链”
- 原型特点：
  - JavaScript 对象是通过引用来传递的，当修改原型时，与之相关的对象也会继承这一改变


### 谈谈 this 对象的理解
- this 总是指向函数的直接调用者
- 如果有 new 关键字，this 指向 new 出来的实例对象
- 在事件中，this 指向触发这个事件的对象
- 严格模式下，function 中的 this 指向 undefined
- 箭头函数中的 this 指向外层


### babel 编译原理
- babylon 将 ES6/ES7 代码解析成 AST
- babel-traverse 对 AST 进行遍历转译，得到新的 AST
- 新 AST 通过 babel-generator 转换成 ES5


### 简述节流和防抖
1. 节流
- 将多次高频操作优化为按一定间隔时间执行，
- 函数节流用于 onresize, onscroll 等短时间内会多次触发的事件

```javascript
function throttle(fn, wait, immediate) {
  let timer = null;
  let callNow = immediate;

  return function () {
    let context = this,
      args = arguments;

    if (callNow) {
      fn.apply(context, args);
      callNow = false;
    }

    if (!timer) {
      timer = setTimeout(() => {
        fn.apply(context, args);
        timer = null;
      }, wait);
    }
  };
}
```

2. 防抖
- 将多次高频操作优化为只在最后一次执行。
- 用户输入，只需再输入完成后做一次输入校验即可

```javascript
function debounce(fn, wait, immediate) {
  let timer = null;

  return function () {
    let args = arguments;
    let context = this;

    if (immediate && !timer) {
      fn.apply(context, args);
    }

    if (timer) clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(context, args);
    }, wait);
  };
}
```
