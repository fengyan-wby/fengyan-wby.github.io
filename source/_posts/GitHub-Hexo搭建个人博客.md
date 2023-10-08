---
title: GitHub+Hexo搭建个人博客
date: 2023-02-02 20:28:25
categories: 博客
tags: ["hexo"]
---
Hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub和Heroku上，是搭建博客的首选框架。<!-- more -->
这里我们选用的是GitHub，Hexo同时也是GitHub上的开源项目，参见：[GitHub Hexo](https://github.com/hexojs/hexo)
如果想要更加全面的了解Hexo，可以到其官网[Hexo](https://hexo.io/zh-cn/)了解更多的细节，因为Hexo的创建者是台湾人，对中文的支持很友好，可以选择中文进行查看。

## GitHub创建个人账户

1. 登录到GitHub，点击New repository创建新仓库，仓库名应该为'用户名.github.io'。
2. 生成ssh密钥并上传公钥到GitHub中。

## 安装Node.js

Hexo基于Node.js，[下载地址](https://nodejs.org/en/download/)。安装后，在命令行中输入node -v和npm -v查看是否安装完成。

```bash
node -v
# v18.13.0

npm -v
# 8.19.3
```

## 安装Hexo

Hexo就是我们的个人博客网站的框架， 这里需要自己在电脑常里创建一个文件夹，Hexo框架与以后你自己发布的网页都在这个文件夹中。
  
```bash
# 使用npm命令安装Hexo（权限不够可以在前面加上sudo）
npm install -g hexo-cli

# 初始化博客
hexo init ${dir_name}

# 进入目录，并对博客进行操作,完成下面操作后，可在localhost:4000中看到我们写出的第一篇博客
cd ${dir_name}
hexo new ${blog_name}
hexo generate
hexo server
```

## 推送网站

上面只是在本地预览，接下来要做的就是就是推送网站，也就是发布网站，让我们的网站可以被更多的人访问。

```bash
# 安装插件
npm install hexo-deployer-git --save
```

```yaml
# 将${dir_name}/${blog_name}/_config.yml文件中的配置进行修改，修改如下
# 其实就是给hexo d 这个命令做相应的配置，让hexo知道你要把blog部署在哪个位置
deploy:
  type: git
  repo: https://github.com/用户名/项目名.git
  branch: main
```

```bash
# 进入目录，并对博客进行操作,完成下面操作后，可在“用户名.github.io”中查看博客
cd ${dir_name}
hexo new ${blog_name}
hexo generate
hexo deploy
```

## 更换主题

如果你不喜欢Hexo默认的主题，可以更换不同的主题，主题传送门：[Themes](https://hexo.io/themes/)。
现在把默认主题更改成Next主题：

```bash
# Downloading Next
cd ${dir_name}
git clone https://github.com/next-theme/hexo-theme-next themes/next

# Upgrading Next
cd ${dir_name}
$ cd themes/next
$ git pull origin master
```

```yaml
# 将${dir_name}/${blog_name}/_config.yml文件中的配置进行修改，修改如下
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next

```
