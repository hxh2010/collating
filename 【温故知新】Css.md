---
title: 【温故知新】Css
date: 2021-05-20 00:00:00
tags:
  - 面试
  - css
categories:
  - 温故知新 
cover: https://hongxh.cn/img/study_img/css.png
---

![](https://hongxh.cn/img/study_img/css.png)

### css 实现三角形
```css
/* 4个三角形 */
.square{
  width:0;
  height: 0;
  border-width:100px;
  border-style: solid;
  border-color: red yellow green blue;
}

/*正三角*/
.triangle {
  width: 0;
  height: 0;
  border-right: 50px solid transparent;
  border-left: 50px solid transparent;
  border-bottom: 100px solid purple;
}
```


### css 实现扇形
```css
/*pieChart 饼状图 4个扇形 */
.pieChart {
  width: 0;
  height: 0;
  border-width: 100px;
  border-style: solid;
  border-color: red yellow green blue;
  border-radius: 100px;
}
```


### 实现 div 的固定宽高比
- height: auto:若div中只有img标签,且需要div按照图片长宽比自动缩放，则只需将img和div的height均设置为auto即可
- height + padding-bottom
```css
.box{
  width: 50%;
  height: 0;
  padding-bottom: 30%;
  /*宽高比为5:3*/
  background: #000;
}
```


### `position` 的值及用法
- absolute :生成绝对定位的元素， 相对于最近一级的 定位不是 static 的父元素来进行定位。
- fixed （老 IE 不支持）生成绝对定位的元素，通常相对于浏览器窗口或 frame 进行定位。
- relative 生成相对定位的元素，相对于其在普通流中的位置进行定位。
- static 默认值。没有定位，元素出现在正常的流中
- sticky 生成粘性定位的元素，容器的位置根据正常文档流计算得出


### 常用 css 属性
- 首行缩进：text-indent 5px
- 字间距：letter-spacing: .3px;
- 虚线：border-top 1px dashed #2b6cb0
- 下划线：text-decoration:none 无 underline 下划线 line-through 删除线 overline 上划线
- 盒子阴影：box-shadow: 0 2px 2px 0 rgba(0,0,0,0.16), 0 0 0 1px rgba(0,0,0,0.08);


### 介绍一下标准的 CSS 的盒子模型？低版本 IE 的盒子模型有什么不同的？
- 有两种， IE 盒子模型、W3C 盒子模型；
- 盒模型： 内容(content)、填充(padding)、边界(margin)、 边框(border)；
- 区 别： IE 的 content 部分把 border 和 padding 计算了进去;
- box-sizing: content-box; // 默认的标准(W3C)盒模型元素效果
- box-sizing: border-box; // 触发怪异(IE)盒模型元素的效果
- box-sizing: inherit; // 继承父元素 box-sizing 属性的值


### css 定义的权重
- 标签的权重为 1，class 的权重为 10，id 的权重为 100
- 如果权重相同，则最后定义的样式会起作用，但是应该避免这种情况出现
- 选择器优先级: 行内样式[1000] > id[100] > class[10] > Tag[1]
- 在同一组属性设置中，!important 优先级最高，高于行内样式


### rgba() 和 opacity 的透明效果有什么不同？
- opacity 作用于元素以及元素内的所有内容（包括文字）的透明度
- rgba() 只作用于元素自身的颜色或其背景色，子元素不会继承透明效果


### CSS3 有哪些新特性？
- 新增选择器 p:nth-child(n){color: rgba(255, 0, 0, 0.75)}
- 弹性盒模型 display: flex;
- 多列布局 column-count: 5;
- 媒体查询 @media (max-width: 480px) {.box: {column-count: 1;}}
- 个性化字体 @font-face{font-family: BorderWeb; src:url(BORDERW0.eot);}
- 颜色透明度 color: rgba(255, 0, 0, 0.75);
- 圆角 border-radius: 5px;
- 渐变 background:linear-gradient(red, green, blue);
- 阴影 box-shadow:3px 3px 3px rgba(0, 64, 128, 0.3);
- 倒影 box-reflect: below 2px;
- 文字装饰 text-stroke-color: red;
- 文字溢出 text-overflow:ellipsis;
- 背景效果 background-size: 100px 100px;
- 边框效果 border-image:url(bt_blue.png) 0 10;
- 转换
- 旋转 transform: rotate(20deg);
- 倾斜 transform: skew(150deg, -10deg);
- 位移 transform: translate(20px, 20px);
- 缩放 transform: scale(.5);
- 平滑过渡 transition: all .3s ease-in .1s;
- 动画 @keyframes anim-1 {50% {border-radius: 50%;}} animation: anim-1 1s;


### chrome 支持小于 12px 的字体
- 缩放


### flex：一跳轴线排不下如何换行
- flex-warp


### 页面导入样式时，使用 link 和 @import 有什么区别。
- 从属关系区别。@import 只能导入样式表，link 还可以定义 RSS、rel 连接属性、引入网站图标等；
- 加载顺序区别；加载页面时，link 标签引入的 CSS 被同时加载；@import 引入的 CSS 将在页面加载完毕后被加载；
- 兼容性区别；


### 什么是 webp？
- 歌开发的一种新图片格式，它是支持有损和无损两种压缩方式的使用直接色的点阵图。
  使用 webp 格式的最大优点是是，在相同质量的文件下，它拥有更小的文件体积。
  因此它非常适合于网络图片的传输，因为图片体积的减少，意味着请求时间的减少，
  这样会提高用户的体验。这是谷歌开发的一种新的图片格式。
- 通过创建 Image 对象，将其 src 属性设置为 webp 格式的图片，
  然后在 onload 事件中获取图片的宽高，如果能够获取，
  则说明浏览器支持 webp 格式图片。如果不能获取或者触发了 onerror 函数，那么就说明浏览器不支持 webp 格式的图片。


### 介绍下 BFC 及其应用
- BFC（Block Format Context）块级格式化上下文，是页面盒模型中的一种 `CSS` 渲染模式，
  相当于一个独立的容器，里面的元素和外部的元素相互不影响。
- 创建 `BFC` 的方式有：
  1. `html` 根元素
  2. `float` 浮动
  3. 绝对定位
  4. `overflow` 不为 `visible`
  5. `display` 为表格布局或者弹性布局；
- `BFC` 主要的作用是：
  1. 清除浮动
  2. 防止同一 `BFC` 容器中的相邻元素间的外边距重叠问题


### 浏览器渲染机制
- 浏览器采用流式布局模型（Flow Based Layout）；
- 浏览器会把 `HTML` 解析成 `DOM`，把 `CSS` 解析成 `CSSOM`,`DOM` 和 `CSSOM` 合并就产生了渲染树（`Render Tree`）；
- 有了 `RenderTree`，我们就知道了所有节点的样式，然后计算他们在页面上的大小和位置，最后把节点绘制到页面上；
- 由于浏览器使用流式布局，对 `Render Tree` 的计算通常只需要遍历一次就可以完成，
  但 `table` 及其内部元素除外，他们可能需要多次计算，通常要花 3 倍于同等元素的时间，
  这也是为什么要避免使用 `table` 布局的原因之一；


### 浏览器优化
- 现代浏览器大多是通过队列机制来批量更新布局，浏览器会把修改操作放在队列中，
  至少一个浏览器刷新（即 16.6ms）才会清空队列，但当你获取布局信息的时候，
  队列中可能会有影响这些属性或方法返回值的操作，即使没有，浏览器也会强制清空队列，
  触发回流和重绘来确保返回正确的值。 例如 `offsetTop、clientTop、scrollTop、getComputedStyle()、
  width、height、getBoundingClientRect()`，应避免频繁使用这些属性，他们都会强制渲染刷新队列。


### 介绍下重绘和回流（Repaint & Reflow），以及如何进行优化？
- 重绘:由于节点的集合属性发生改变或者由于样式改变而不会影响布局的，
  成为重绘，例如 `outline、visibility、color、background-color` 等，
  重绘的代价是高昂的，因此浏览器必须验证 `DOM` 树上其他节点元素的可见性。
- 回流:回流是布局或者几何属性需要改变就称为回流。
  回流是影响浏览器性能的关键因素，因为其变化涉及到部分页面（或是整个页面）的布局更新。
  一个元素的回流可能会导致其素有子元素以及 `DOM` 中紧随其后的节点、祖先节点元素的随后的回流。
  大部分的回流将导致页面的重新渲染。
- 回流必定会发生重绘，重绘不一定会引发回流。


### 减少重绘和回流
1. CSS
- 使用 `transform` 代替 `top`；
- 使用 `visibility` 替换 `display: none`，前者引起重绘，后者引发回流；
- 避免使用 `table` 布局；
- 尽可能在 `DOM` 树的最末端改变 `class`；
- 避免设置多层内联样式，CSS 选择符从右往左匹配查找，避免节点层级过多；
- 将动画效果应用到 `position` 属性为 `absolute` 或 `fixed` 的元素上，避免影响其他元素的布局；
- 避免使用 `CSS` 表达式，可能会引发回流；
- CSS 硬件加速；

2. Javascript
- 避免频繁操作样式，修改 `class` 最好；
- 避免频繁操作 `DOM`，合并多次修改为一次；
- 避免频繁读取会引发回流/重绘的属性，将结果缓存；
- 对具有复杂动画的元素使用绝对定位，使它脱离文档流；
