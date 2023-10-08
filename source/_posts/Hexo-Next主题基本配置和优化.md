---
title: Hexo-Next主题基本配置和优化
date: 2023-02-02 20:29:04
categories: 博客
tags: ["hexo"]
---

Hexo-Next主题基本配置和优化。
环境：NexT version 8.14.2

<!-- more -->

## 基本信息配置

Next主题默认风格为Muse，可以在主题目录的配置文件themes/next/_config.yml

```yaml
# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```

而根目录下的_config.yml文件负责站点的相关配置，如网站标题、网站描述、网站语言等

```yaml
# Site
title: 
subtitle: ''
description: ''
keywords:
author: 
language: zh-CN
```

## 生成标签、分类、归档页面

主题首页的默认页面中是没有标签、分类、归档页面的，需要手动生成一下。先在博客根目录下输入以下命令

```bash
hexo new page tags
hexo new page categories
hexo new page archives
```

然后打开新增的source/tags/index.md，修改如下：

```diff
  title: 标签
  date: ****-**-** **:**:**
+ type: tags
```

同理修改categories和archives文件夹下的index.md文件

最后修改主题配置文件的menu字段：

```diff
menu:
  home: / || home
  about: /about/ || user
+ tags: /tags/ || tags
+ categories: /categories/ || th
+ archives: /archives/ || archive
```

## 配置 hexo 本地搜索

安装插件

```bash
npm install hexo-generator-searchdb --save
```

修改站点配置文件

```yaml
# 本地搜索
search:
  path: search.xml  # 索引文件路径
  field: post  # 搜索范围，默认是 post，还可以选择 page、all，设置成 all 表示搜索所有页面
  format: html
  limit: 100  # 限制搜索的条目数
```

然后修改主题配置文件

```yaml
# Local Search
# Dependencies: https://github.com/theme-next/hexo-generator-searchdb
local_search:
  enable: true
  # If auto, trigger search by changing input.
  # If manual, trigger search by pressing enter key or search button.
  trigger: auto
  # Show top n results per article, show all results by setting to -1
  top_n_per_article: 1
  # Unescape html strings to the readable one.
  unescape: false
  # Preload the search data when the page loads.
  preload: false
```

## 首页显示文章摘要

默认的主题配置里，首页会显示每一篇文章的全文，如果想只显示文章摘要，对主题配置文件做如下更改：

```yaml
# Use `description` in front-matter to specify post excerpt.
excerpt_description: true
```

用户可以在文章中通过`<!-- more -->`标记来精确划分摘要信息，标记之前的段落将作为摘要显示在首页。

## 头像信息设置

```yaml
avatar:
  url: /images/avatar.jpg  # 设置头像资源的位置
  rounded: true            # 开启圆形头像
  opacity: 1               # 不透明的比例：0就是完全透明
  rotated: false           # 不开启旋转
```

## 修改站点页脚

```yaml
  footer:  # 底部信息区
    # Specify the year when the site was setup. If not defined, current year will be used.
    #since: 2021

    # Icon between year and copyright info.
    icon:
        # Icon name in Font Awesome. See: https://fontawesome.com/icons
        name: fa fa-heart  # 图标名称
        # If you want to animate the icon, set it to true.
        animated: false
        # Change the color of icon, using Hex Code.
        color: "#ff0000"

    # If not defined, `author` from Hexo `_config.yml` will be used.
    copyright:

    # Powered by Hexo & NexT
    powered: false

    # Beian ICP and gongan information for Chinese users. See: https://beian.miit.gov.cn, http://www.beian.gov.cn
    beian:
        enable: false
        icp:
        # The digit in the num of gongan beian.
        gongan_id:
        # The full num of gongan beian.
        gongan_num:
        # The icon for gongan beian. See: http://www.beian.gov.cn/portal/download
        gongan_icon_url:
```

## 添加社交链接

```yaml
  social:
    GitHub: https://github.com/fengyan-wby || fab fa-github
    E-Mail: mailto:wubangyu1993@gmail.com || fa fa-envelope
    #Weibo: https://weibo.com/yourname || fab fa-weibo
    #Twitter: https://twitter.com/yourname || fab fa-twitter
    #FB Page: https://www.facebook.com/yourname || fab fa-facebook
    #StackOverflow: https://stackoverflow.com/yourname || fab fa-stack-overflow
    #YouTube: https://youtube.com/yourname || fab fa-youtube
    #Instagram: https://instagram.com/yourname || fab fa-instagram
    #Skype: skype:yourname?call|chat || fab fa-skype

  social_icons:
    enable: true
    icons_only: false
    transition: false
```

## 页面阅读数量统计

```yaml
busuanzi_count:
  enable: false              # 设true 开启
  total_visitors: true       # 总阅读人数（uv数）
  total_visitors_icon: user  # 阅读总人数的图标
  total_views: true          # 总阅读次数（pv数）
  total_views_icon: eye      # 阅读总次数的图标
  post_views: true           # 开启内容阅读次数
  post_views_icon: eye       # 内容页阅读数的图标
```

## 字数、阅读时长统计

```bash
# 安装插件
npm install hexo-symbols-count-time --save
```

站点配置文件，修改如下：

```yaml
symbols_count_time:
  #文章内是否显示
  symbols: true
  time: true
 # 网页底部是否显示
  total_symbols: true
  total_time: true
```

主题配置文件，修改如下：

```yaml
symbols_count_time:
  separated_meta: true  # false会显示一行
  item_text_post: true  # 显示属性名称,设为false后只显示图标和统计数字,不显示属性的文字
  item_text_total: true # 底部footer是否显示字数统计属性文字
  awl: 4                # 计算字数的一个设置,没设置过
  wpm: 275              # 一分钟阅读的字数
```

## 代码块样式设置

```yaml
codeblock:
  # Code Highlight theme
  # All available themes: https://theme-next.js.org/highlight/
  theme:
    #light: default
    light: stackoverflow-dark
    dark: stackoverflow-dark
  prism:
    light: prism
    dark: prism-dark
  # Add copy button on codeblock
  copy_button:
    enable: true
    # Available values: default | flat | mac
    style: mac
```
