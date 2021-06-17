---
title: 【温故知新】TypeScript
date: 2021-05-28 00:00:00
tags:
  - 面试
  - ts
  - typescript  
categories:
  - 温故知新
cover: https://hongxh.cn/img/study_img/ts.jpeg
---

![](https://hongxh.cn/img/study_img/ts.jpeg)

### Ts 是什么
- TypeScript 是 JavaScript 的一个超集，其静态类型检查可以让开发者在开发阶段就可以发现代码中隐藏的错误，弥补了 JS 的不足。
  而且 TypeScript 提供最新的和不断发展的 JavaScript 特性，开发的时候你可以愉快使用这些新特性，
  TS 编译后会转为浏览器可以直接运行的 JavaScript 代码


### TS 基础类型
- `boolean`、`number`、`string`、`[]`、`元组`（定义数组各位置类型）、`null`、`undefined`
- `enum` 枚举，下标从 0 开始
- `any`
- `unknown` 所有类型都可以分配给 unknown，我们只能将 unknown 类型的变量赋值给 any 和 unknown。
- `never` 不可到达的类型， never 仅能被赋值给另外一个 never 类型
- `void` void 类型像是与 any 类型相反，它表示没有任何类型。void 类型的变量没有什么大用，因为你只能为它赋予 undefined 和 null。


### 类型断言
- as 尖括号`<>`


### 类型守卫
- 联合类型中我们只能访问共同拥有的成员。
- 处理方法，断言
```typescript
interface Bird {
  fly();
  layEggs();
}

interface Fish {
  swim();
  layEggs();
}

function getSmallPet(): Fish | Bird {}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim(); // errors

// JavaScript里常用来区分2个可能值的方法是检查成员是否存在
// 这里的访问都会报错
if (pet.swim) {
  pet.swim();
} else if (pet.fly) {
  pet.fly();
}

// 使用断言
if ((<Fish>pet).swim) {
  (<Fish>pet).swim();
} else {
  (<Bird>pet).fly();
}
```

- 自定义类型保护
```typescript
function isFish(pet: Fish | Bird): pet is Fish {
  return (<Fish>pet).swim !== undefined;
}

// 'swim' 和 'fly' 调用都没有问题了
if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}
```

- `instanceof` / `in` / `typeof` 关键字


### 交叉类型和联合类型
- 联合类型 `let a: number | string | undefined`
- 类型别名 `type A = number | string | undefined`
- 交叉类型 交叉类型是将多个类型合并为一个类型。 `Person & Serializable & Loggable`
  这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了所有类型的特性。


### 类型 type 和接口 interface 的区别
- interface 能够声明合并，type 不能同名
```typescript
interface User {
  name: string;
  age: number;
}

interface User {
  sex: string;
}

/*
User 接口为 {
  name: string
  age: number
  sex: string 
}
*/
```

- type 可以声明基本类型别名，联合类型，元组等类型
```typescript
// 基本类型别名
type Name = string;

// 联合类型
interface Dog {
  wong();
}
interface Cat {
  miao();
}

type Pet = Dog | Cat;

// 具体定义数组每个位置的类型
type PetList = [Dog, Pet];
```


### 什么是泛型
- 泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。
- 泛型可以理解为多个类型，通过一些类型变量增加重用性
- 也可以通过 `extends` 关键字来约束
- 泛型参数默认类型 `interface A<T=string> { name: T; }`
- 泛型条件类型 `T extends U ? X : Y`


### 关键字 `in` `keyof` `typeof`
- keyof 获取接口或类型的键名，返回的是联合类型
- typeof 获取常量的类型
- in 遍历联合类型
- infer

```typescript
type ParamType<T> = T extends (...args: infer P) => any ? P : T;

interface User {
  name: string;
  age: number;
}

type Func = (user: User) => void;

type Param = ParamType<Func>; // Param = User
type AA = ParamType<string>; // string
```


### 内置类型别名
#### Partial 改所有键值为可选 `type TPartialUser = Partial<IUser>;`

```typescript
// ts 中的实现方式
type Partial<T> = {
[P in keyof T]?: T[P];
};

// 使用方式
interface IUser {
name: string;
age?: number;
}
type TPartialUser = Partial<IUser>;
// 输出的 TPartialUser 结果
// type TPartialUser = {
// name?: string | undefined;
// age?: number | undefined;
// }
```  

#### Required 改所有键值为必选 `type TRequiredUser = Required<IUser>;`

```typescript
// ts 中的实现方式
type Required<T> = {
[P in keyof T]-?: T[P];
};
// 使用方式
interface IUser {
name: string;
age?: number;
}
// 改 IUser 的接口值为均必选
type TRequiredUser = Required<IUser>;
// 输出的 TRequiredUser 结果
// type TRequiredUser = {
// name: string;
// age: number;
// }
```  
#### Readonly 将传入属性变为只读

```typescript
type Readonly<T> = {
readonly [P in keyof T]: T[P];
};
```  

#### Pick 将某个类型中的子属性挑出来，变成包含这个类型部分属性的子类型。
  
```typescript
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
};

interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo, 'title' | 'completed'>;

const todo: TodoPreview = {
  title: 'Clean room',
  completed: false,
};
```

#### Record 的作用是将 K 中所有的属性的值转化为 T 类型。

```typescript
type Record<K extends keyof any, T> = {
  [P in K]: T;
};

interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

const x: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" },
};
```

#### Exclude 将某个类型中属于另一个的类型移除掉
```typescript
type Exclude<T, U> = T extends U ? never : T;

type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
```

#### Extract 与 Exclude 恰好相反, 代表取出对应值
```typescript
type Extract<T, U> = T extends U ? T : never;

type T0 = Extract<"a" | "b" | "c", "a">; // "a"
```

#### ReturnType 的作用是用于获取函数 T 的返回类型。
```typescript
type ReturnType<T extends (...args: any[]) => any> = T extends (
  ...args: any[]
) => infer R
  ? R
  : any;  
```



### 其它
- 显式赋值断言
```typescript
let x!: number[];
initialize();
x.push(4);

function initialize() {
  x = [0, 1, 2, 3];
}
```

- 非空断言
```typescript
type A = {
  key1?: {
    key1_1: number;
  };
};
const a: A = {
  key1: {
    key1_1: 1,
  },
};
console.log(a.key1!.key1_1);
```

- 可选链
```typescript
type A = {
  key1?: {
    key1_1: number;
  };
};
const a: A = {
  key1: {
    key1_1: 1,
  },
};
console.log(a.key1?.key1_1); // 仅判断 undefined 与 null 这两个值
// console.log((_a = a.key1) === null || _a === void 0 ? void 0 : _a.key1_1);
```

- 空值合并运算
```typescript
let x = foo ?? bar();
// let x = foo !== null && foo !== void 0 ? foo : bar();
// 与 || 几乎一样，只是帮助排除 0、""、NaN、false
// 不能与 && 或 || 操作符共用
// 短路
function A() { console.log('A was called'); return undefined;}
function B() { console.log('B was called'); return false;}
function C() { console.log('C was called'); return "foo";}

console.log(A() ?? C());
console.log(B() ?? C());

// A was called
// C was called
// foo
// B was called
// false
```


### .d.ts 声明文件作用
- 当使用第三方库时，我们需要引用它的声明文件，才能获得对应的代码提示等功能。
  - declare var 声明全局变量
  - declare function 声明全局方法
  - declare class 声明全局类
  - declare enum 声明全局枚举类型
  - declare namespace 声明（含有子属性的）全局对象
  - interface 和 type 声明全局类型
  - export 导出变量
  - export namespace 导出（含有子属性的）对象
  - export default ES6 默认导出
  - export = commonjs 导出模块
  - export as namespace UMD 库声明全局变量
  - declare global 扩展全局变量
  - declare module 扩展模块
  - /// <reference /> 三斜线指令
  

### tsconfig 配置文件
```javascript
module.exports = {
  "compilerOptions": {
    /* 基本选项 */
    "target": "es5",                       // 指定 ECMAScript 目标版本: 'ES3' (default), 'ES5', 'ES6'/'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'
    "module": "commonjs",                  // 指定使用模块: 'commonjs', 'amd', 'system', 'umd' or 'es2015'
    "lib": [],                             // 指定要包含在编译中的库文件
    "allowJs": true,                       // 允许编译 javascript 文件
    "checkJs": true,                       // 报告 javascript 文件中的错误
    "jsx": "preserve",                     // 指定 jsx 代码的生成: 'preserve', 'react-native', or 'react'
    "declaration": true,                   // 生成相应的 '.d.ts' 文件
    "sourceMap": true,                     // 生成相应的 '.map' 文件
    "outFile": "./",                       // 将输出文件合并为一个文件
    "outDir": "./",                        // 指定输出目录
    "rootDir": "./",                       // 用来控制输出目录结构 --outDir.
    "removeComments": true,                // 删除编译后的所有的注释
    "noEmit": true,                        // 不生成输出文件
    "importHelpers": true,                 // 从 tslib 导入辅助工具函数
    "isolatedModules": true,               // 将每个文件作为单独的模块 （与 'ts.transpileModule' 类似）.
    /* 严格的类型检查选项 */
    "strict": true,                        // 启用所有严格类型检查选项
    "noImplicitAny": true,                 // 在表达式和声明上有隐含的 any类型时报错
    "strictNullChecks": true,              // 启用严格的 null 检查
    "noImplicitThis": true,                // 当 this 表达式值为 any 类型的时候，生成一个错误
    "alwaysStrict": true,                  // 以严格模式检查每个模块，并在每个文件里加入 'use strict'
    /* 额外的检查 */
    "noUnusedLocals": true,                // 有未使用的变量时，抛出错误
    "noUnusedParameters": true,            // 有未使用的参数时，抛出错误
    "noImplicitReturns": true,             // 并不是所有函数里的代码都有返回值时，抛出错误
    "noFallthroughCasesInSwitch": true,    // 报告 switch 语句的 fallthrough 错误。（即，不允许 switch 的 case 语句贯穿）
    /* 模块解析选项 */
    "moduleResolution": "node",            // 选择模块解析策略： 'node' (Node.js) or 'classic' (TypeScript pre-1.6)
    "baseUrl": "./",                       // 用于解析非相对模块名称的基目录
    "paths": {},                           // 模块名到基于 baseUrl 的路径映射的列表
    "rootDirs": [],                        // 根文件夹列表，其组合内容表示项目运行时的结构内容
    "typeRoots": [],                       // 包含类型声明的文件列表
    "types": [],                           // 需要包含的类型声明文件名列表
    "allowSyntheticDefaultImports": true,  // 允许从没有设置默认导出的模块中默认导入。
    /* Source Map Options */
    "sourceRoot": "./",                    // 指定调试器应该找到 TypeScript 文件而不是源文件的位置
    "mapRoot": "./",                       // 指定调试器应该找到映射文件而不是生成文件的位置
    "inlineSourceMap": true,               // 生成单个 soucemaps 文件，而不是将 sourcemaps 生成不同的文件
    "inlineSources": true,                 // 将代码与 sourcemaps 生成到一个文件中，要求同时设置了 --inlineSourceMap 或 --sourceMap 属性
    /* 其他选项 */
    "experimentalDecorators": true,        // 启用装饰器
    "emitDecoratorMetadata": true          // 为装饰器提供元数据的支持
  },
  // 指定需要编译的文件
  "files": [],
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules",
    "**/*.spec.ts"
  ],
  // 继承其它文件配置
  "extends": "./configs/base",
}
```


### @type / d.ts 区别
- @type 第三方包提供的 ts 声明文件
- d.ts 自行声明的











### 资料
- 1.2W 字 | 了不起的 TypeScript 入门教程 https://juejin.im/post/5edd8ad8f265da76fc45362c
- 一文读懂 TypeScript 泛型及应用（ 7.8K 字）https://juejin.im/post/5ee00fca51882536846781ee#heading-0
- TypeScript 中高级应用与最佳实践 https://juejin.im/post/5d4285ddf265da03dd3d514b#heading-0
- TypeScript 强大的类型别名 https://juejin.im/post/5c2f87ce5188252593122c98#heading-0
