---
title: Hexo+Butterfly 快速搭建个人博客【附源码】
date: 2020-12-4 11:11:11
tags:
  - nginx
  - linux
categories:
  - 教程
cover: https://img-blog.csdnimg.cn/2020120409565217.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxMzE2MDIwMjAx,size_16,color_FFFFFF,t_70#pic_center
---


### 先上效果
>  [在线查看:https://hongxh.cn/](https://hongxh.cn/)

### Github 源码
>[https://github.com/hxh2010/hexo-butterfly](https://github.com/hxh2010/hexo-butterfly)
>
![](https://img-blog.csdnimg.cn/2020120409565217.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxMzE2MDIwMjAx,size_16,color_FFFFFF,t_70#pic_center)

### Hexo
> [hexo在线文档](https://hexo.io/zh-cn/docs/)

- 安装：
```bash
$ npm install -g hexo-cli
```
- 建站：
```bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```

## Butterfly
> [Butterfly github](https://github.com/jerryc127/hexo-theme-butterfly)
> [Butterfly 文档](https://jerryc.me/)

- 下载主题，拷贝至 **themes** 目录
```yaml
git clone https://github.com/jerryc127/hexo-theme-butterfly.git
```
- 根据 butterfly 文档修改 **_config.yml** 及 **themes/_config.yml** 文件
不建议采用butterfly文档上一个配置文件的方式，否则你配置项找半天
1._config.yml (删除默认部分)

```bash
#网站标题
title: 记忆碎片 
#副标题
subtitle: '博客' 
#头像下方签名
description: 玻璃晴朗，橘子辉煌。 
keywords: ''
#作者 显示在头像下方、页脚、版权声明等处
author: 半个夕阳 
#修改为中文
language: zh-CN 
timezone: ''

#若网站为子路径时配置
root: /   

......

#修改为butterfly主题
theme: butterfly 
```
2. themes/_config.yml (删除默认部分)

```yaml
menu:
  首页: / || fas fa-home
  标签: /tags/ || fas fa-tags
  分类: /categories/ || fas fa-folder-open
  留言板: /messageboard/ || fa fa-paper-plane
  关于我: /about/ || fas fa-heart
...
social:
  fab fa-github:
    https://github.com/hxh2010 || Github
  fas fa-envelope:
    mailto:316020201@qq.com || Email

# Local search
local_search:
  enable: true

# Favicon（網站圖標）
favicon: https://hongxh.cn/images/favicon.ico

# Avatar (頭像)
avatar:
  img: https://hongxh.cn/images/header.jpg
  effect: false #头像会一直转圈

# 主页大图
index_img: https://hongxh.cn/images/blog/index.jpg

# If the banner of page not setting, it will show the top_img
default_top_img: https://hongxh.cn/images/blog/index.jpg

# note: tag page, not tags page (子標籤頁面的 top_img)
tag_img: https://hongxh.cn/images/blog/index.jpg

category_img: https://hongxh.cn/images/blog/index.jpg

cover:
  # display the cover or not (是否顯示文章封面)
  index_enable: true
  aside_enable: true
  archives_enable: true
  position: both
  default_cover: https://hongxh.cn/images/blog/index.jpg

# Display the article introduction on homepage
# 1: description
# 2: both (if the description exists, it will show description, or show the auto_excerpt)
# 3: auto_excerpt (default)
# false: do not show the article introduction
index_post_content:
  method: 3
  length: 500 # if you set method to 2 or 3, the length need to config


# Related Articles
related_post:
  enable: true
  limit: 6 # Number of posts displayed
  date_type: created # or created or updated 文章日期顯示創建日或者更新日

# valine
# https://valine.js.org
valine:
  appId: XXXXXXXXXXXXXXXXX # leancloud application app id
  appKey: XXXXXXXXXXXXXXXX # leancloud application app key
  pageSize: 10 # comment list page size
  avatar: mp # gravatar style https://valine.js.org/#/avatar
  lang: zh-CN # i18n: zh-CN/zh-TW/en/ja
  placeholder: 留下你的想法，已读必回！ # valine comment input placeholder (like: Please leave your footprints)
  guest_info: nick,mail # valine comment header info (nick/mail/link)
  recordIP: false # Record reviewer IP
  serverURLs: # This configuration is suitable for domestic custom domain name users, overseas version will be automatically detected (no need to manually fill in)
  bg: # valine background
  emojiCDN: # emoji CDN
  enableQQ: false # enable the Nickname box to automatically get QQ Nickname and QQ Avatar
  requiredFields: nick,mail # required fields (nick/mail)
  option:

# the subtitle on homepage (主頁subtitle)
subtitle:
  enable: true
  effect: true
  loop: false
  source: false
  sub: "一念山河成，一念百草生。"

# Loading Animation (加載動畫)
preloader: false

# aside (側邊欄)
# --------------------------------------

aside:
  enable: true
  hide: false
  button: true
  mobile: true # display on mobile
  position: right # left or right
  card_author:
    enable: true
    description:
    button:
      icon: fas fa-home
      text: 个人主页
      link: https://hongxh.cn/me/
  card_announcement:
    enable: true
    content: 念念不忘，必有回响。
  card_recent_post:
    enable: true
    limit: 5 # if set 0 will show all
    sort: date # date or updated
  card_categories:
    enable: true
    limit: 8 # if set 0 will show all
    expand: none # none/true/false
  card_tags:
    enable: true
    limit: 40 # if set 0 will show all
    color: false
  card_archives:
    enable: true
    type: monthly # yearly or monthly
    format: MMMM YYYY # eg: YYYY年MM月
    order: -1 # Sort of order. 1, asc for ascending; -1, desc for descending
    limit: 8 # if set 0 will show all
  card_webinfo:
    enable: true
    post_count: true
    last_push_date: true
```

### 结语
- 文档有的内容就不赘述了，如果对你有帮助，点个星吧！
- 有什么需要可以留言！
