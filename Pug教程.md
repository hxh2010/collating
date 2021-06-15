---
title: Pug从入门到入坟
date: 2020-12-16 11:11:11
tags:
  - Pug
categories:
  - 教程
cover: https://img-blog.csdnimg.cn/20201216200939970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxMzE2MDIwMjAx,size_16,color_FFFFFF,t_70#pic_center
---
![](https://img-blog.csdnimg.cn/20201216200939970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxMzE2MDIwMjAx,size_16,color_FFFFFF,t_70#pic_center)


#### 概要
Pug是一款健壮、灵活、功能丰富的HTML模板引擎,专门为 Node.js 平台开发。Pug是由Jade 改名而来。

是一种通过缩进（表示标签间的嵌套关系）的方式来编写代码的过程，在编译的过程中，不需要考虑标签是否闭合的问题。可以加快写代码速度，也为代码复用提供了便捷。

#### 安装
pug可以通过npm获得：
```bash
npm install pug -g
npm install pug-cli -g
```

#### 命令行
将pug格式转化为HTML的时候输入命令pug -P xxx.pug

自动更新 pug -P -w xxx.pug

有自定义目录的需求，则需要设置-o参数

#### 结构语法
##### 属性 
- 基本
```yaml
a(href='baidu.com') 百度
```
转化为

```html
<a href="baidu.com">百度</a>
```
- 多行
```yaml
input(
  type='checkbox'
  name='agreement'
  checked
)
```
转化为

```html
<input type="checkbox" name="agreement" checked="checked" />
```
- 括号引起来

```yaml
div(class='div-class', (click)='play()')
div(class='div-class' '(click)'='play()')
```
转化为

```html
<div class="div-class" (click)="play()"></div>
<div class="div-class" (click)="play()"></div>
```

##### 分支条件
- case

```yaml
- var friends = 10
case friends
  when 0
    p 您没有朋友
  when 1
    p 您有一个朋友
  default
    p 您有 #{friends} 个朋友
```
转化为

```html
<p>您有 10 个朋友</p>
```

##### 代码
- 不输出代码

```yaml
- for (var x = 0; x < 3; x++)
  li item

-
  var list = ["Uno", "Dos", "Tres",
          "Cuatro", "Cinco", "Seis"]
each item in list
  li= item
```
转化为

```html
<li>item</li>
<li>item</li>
<li>item</li>

<li>Uno</li>
<li>Dos</li>
<li>Tres</li>
<li>Cuatro</li>
<li>Cinco</li>
<li>Seis</li>
```

- 转义输出

```yaml
p= '这个代码被 <转义> 了！'
```
转化为

```html
<p>这个代码被 &lt;转义&gt; 了！</p>
```
- 不转义输出

```yaml
p!= '这段文字' + ' <strong>没有</strong> 被转义！'
```
转化为

```html
<p>这段文字 <strong>没有</strong> 被转义！</p>
```

##### 注释

```yaml
单行注释: // 一些内容 或者 <!-- 一些内容-->
块注释: 	//-
		    给模板写的注释
		    随便写多少字
		    都没关系。	 
条件注释: <!--[if IE 8]>
		    
```

##### 条件

```yaml
- var user = { description: 'foo bar baz' }
- var authorised = false
#user
  if user.description
    h2.green 描述
    p.description= user.description
  else if authorised
    h2.blue 描述
    p.description.
      用户没有添加描述。
      不写点什么吗……
  else
    h2.red 描述
    p.description 用户没有描述
```
转化为

```html
<div id="user">
  <h2 class="green">描述</h2>
  <p class="description">foo bar baz</p>
</div>
```

##### 包含
```yaml
include includes/head.pug
include style.css
```

#### 迭代
each 和 while

```yaml
ul
  each val in [1, 2, 3, 4, 5]
    li= val

- var n = 0;
ul
  while n < 4
    li= n++**
```
转化为

```html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
</ul>

<ul>
  <li>0</li>
  <li>1</li>
  <li>2</li>
  <li>3</li>
</ul>
```

##### Mixin

```yaml
//- 定义
mixin list
  ul
    li foo
    li bar
    li baz
//- 使用
+list
+list

mixin article(title)
  .article
    .article-wrapper
      h1= title
      if block
        block
      else
        p 没有提供任何内容。

+article('Hello world')

+article('Hello world')
  p 这是我
  p 随便写的文章
```

转化为

```html
<ul>
  <li>foo</li>
  <li>bar</li>
  <li>baz</li>
</ul>
<ul>
  <li>foo</li>
  <li>bar</li>
  <li>baz</li>
</ul>

<div class="article">
  <div class="article-wrapper">
    <h1>Hello world</h1>
    <p>没有提供任何内容。</p>
  </div>
</div>
<div class="article">
  <div class="article-wrapper">
    <h1>Hello world</h1>
    <p>这是我</p>
    <p>随便写的文章</p>
  </div>
</div>
```

> 参考文档：https://www.pugjs.cn/api/getting-started.html
