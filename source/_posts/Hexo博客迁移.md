---
title: Hexo博客迁移
date: 2023-03-07 14:52:01
categories: 博客
tags: hexo
---

介绍如何将Hexo博客从旧电脑迁移到新电脑中。
<!-- more -->

{% note info %}
笔者的迁移主要是从一台旧的电脑迁移到另一台电脑，换句话说，我完全没有继续在旧电脑上维护博客的打算，因此迁移方法简单粗暴。我在网上搜索的时候，看到有人利用git的branch，来实现多台设备同时能够维护博客，这里之后再进行探究。
{% endnote %}

## 配置基础环境

要配置基础环境，需要做以下几个步骤

1. 安装`git`，并生成密钥，保存到github账号中
2. 下载并安装`Node.js`（`npm`会自己跟着装好）
3. 使用`npm`安装`hexo` ，具体指令为`npm install -g hexo-cli`

这些都在[GitHub+Hexo搭建个人博客](https://fengyan-wby.github.io/2023/02/02/GitHub-Hexo%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2/)

{% note warning %}
注意，安装完`hexo`之后不用`hexo init`
{% endnote %}

## 迁移相关文件

需要迁移的文件只有：

1. 博客配置文件`./_config.yml`
2. 主题配置文件夹`./theme/`
3. 文章及相关内容的文件夹`./source/`
4. 模板文件夹`./scaffolds/`
5. 记录博客所有的插件的文件`./package.json`

## 在新电脑中重新部署

还记得上一步中拷贝的`./package.json`嘛，只要在同一文件目录下运行

```bash
npm install
```

就会自动读取`package.json`文件中记录的插件列表，然后挨个安装，这样你在旧设备中安装的插件，在新设备中，都安装好了

之后的一切就照常，修改文章，生成静态文件，部署到git

```bash
hexo g
hexo d
```
