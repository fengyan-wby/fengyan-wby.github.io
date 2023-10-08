---
title: Ubuntu&Mac安装配置Oh My Zsh
date: 2023-02-10 14:49:03
categories: 工具
tags: ["Ubuntu", "zsh"]
---

`Oh My Zsh`是一款社区驱动的命令行工具，是基于`Zsh`命令行的一个扩展工具集，提供了丰富的扩展功能，如：主题配置，插件机制，内置的便捷操作等，可以给我们一种全新的命令行使用体验。下文对`Oh My Zsh`的安装及配置方法进行总结，只总结最佳的实践。<!-- more -->

## 安装Oh My Zsh

第一步：安装`Zsh`

```bash
# 安装Zsh
sudo apt install zsh  # Mac使用brew install zsh

# 将Zsh设置为默认Shell
chsh -s /bin/zsh

# 可以通过echo $SHELL 查看当前默认的Shell，如果没有改为/bin/zsh，那么需要重启Shell。
```

第二步：安装Oh My Zsh

```bash
# 安装Oh My Zsh
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
# 以上命令可能不好使，可使用如下两条命令
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh
bash ./install.sh
```

## Zsh配置

### 字体的安装

推荐在终端使用`Powerline`类型的主题，该类型主题可以使用图形表示尽可能多的信息，方便用户的使用。推荐安装用户量最大的`Powerlevel9k`。

`Powerlevel9k`中需要使用较多的图形符号，字体大多不会自带这些符号，所以需要使用专门的`Powerline`字体。

不推荐安装官方默认的`Powerline Fonts`，理由是图形符号不全，符号处会有乱码。推荐安装`Nerd-Fonts`系列字体，因为该系列字体附带有尽可能全的符号，并且更新非常频繁，项目地址在这里。例如直接下载 `Ubuntu Font Family`中的`Ubuntu Nerd Font Complete.ttf`，然后直接在Ubuntu下安装。

### 主题的配置

如果要在Oh My Zsh中安装Powerlevel9k ，只需执行如下指令：

```bash
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```

注：由于Powerlevel9k( 已经被弃用 )升级到Powerlevel10k，更新安装：

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

### 修改配置文件

```bash
# 修改～/.zshrc
ZSH_THEME="powerlevel10k/powerlevel10k"
```

## 插件配置

### autojump

```bash
sudo apt install autojump  # mac使用brew install autojump
```

### fasd

```bash
sudo apt install fasd  # mac使用brew install fasd
```

### zsh-autosuggestions

命令行命令键入时的历史命令建议插件
按照官方文档提示，直接执行如下命令安装：

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### zsh-syntax-highlighting

命令行语法高亮插件
按照官方文档提示，直接执行如下命令安装：

```bash
 git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
 ```

### 修改配置

```bash
# 修改～/.zshrc
# autojump 功能弱，fasd 功能强，但是没 autojump 实用
# 值得注意的是，根据官方文档，zsh-syntax-highlighting 插件需放在最后
plugins=(
  git extract autojump zsh-autosuggestions zsh-syntax-highlighting
)
```
