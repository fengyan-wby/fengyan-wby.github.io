---
title: Ubuntu安装微信教程
date: 2023-10-12 19:04:14
categories: 工具
tags: ["Ubuntu"]
---

通过deepin-wine方式在Ubuntu22.04中安装微信。

<!-- more -->

## 安装

```bash
# 更新软件源
sudo apt update
```

```bash
# 添加deepin-wine仓库
wget -O- https://deepin-wine.i-m.dev/setup.sh | sh
```

上面的文件具体内容如下：
```bash
#!/bin/sh
set -e

# 添加架构
ARCHITECTURE=$(dpkg --print-architecture && dpkg --print-foreign-architectures)
if ! echo "$ARCHITECTURE" | grep -qE 'amd64|i386'; then
    echo "必须amd64/i386机型才能移植deepin-wine"
    return 1
fi
echo "$ARCHITECTURE" | grep -qE 'i386' || sudo dpkg --add-architecture i386
sudo apt-get update

LIST_FILE="/etc/apt/sources.list.d/deepin-wine.i-m.dev.list"

# 添加软件源
sudo tee "$LIST_FILE" >/dev/null << "EOF"
deb [trusted=yes] https://deepin-wine.i-m.dev /
EOF

# 添加XDG_DATA_DIRS配置，使得应用图标能正常显示
sudo tee "/etc/profile.d/deepin-wine.i-m.dev.sh" >/dev/null << "EOF"
XDG_DATA_DIRS=${XDG_DATA_DIRS:-/usr/local/share:/usr/share}
for deepin_dir in /opt/apps/*/entries; do
    if [ -d "$deepin_dir/applications" ]; then
        XDG_DATA_DIRS="$XDG_DATA_DIRS:$deepin_dir"
    fi
done
export XDG_DATA_DIRS
EOF

# 刷新软件源
sudo apt-get update --no-list-cleanup -o Dir::Etc::sourcelist="$LIST_FILE" -o Dir::Etc::sourceparts="-"

printf "
\033[32m大功告成，现在可以试试安装更新deepin-wine软件了，如：
微信：sudo apt-get install com.qq.weixin.deepin
QQ：sudo apt-get install com.qq.im.deepin
TIM：sudo apt-get install com.qq.office.deepin
钉钉：sudo apt-get install com.dingtalk.deepin
完整列表见 https://deepin-wine.i-m.dev/
\033[31;1m
\033[5m🌟\033[25m 安装后需要注销重登录才能显示应用图标。
\033[5m🌟\033[25m 无法安装？无法启动？无法正常使用？切记先去github主页看【常见问题】章节，再找找相关issue，也许早已经有了解决方案了。

\033[36;1m如果觉得有用，不妨来给项目加个star：\033[25mhttps://github.com/zq1997/deepin-wine
\033[0m"
```

自此，可以使用apt直接安装各个软件：
```bash
sudo apt install com.qq.weixin.deepin
```

将`com.qq.weixin.deepin`替换为下列包名，可以继续安装其他应用：

| 应用 | 包名 |
| ---- | ---- |
| 微信 | com.qq.weixin.deepin |
| QQ | com.qq.im.deepin |
| TIM | com.qq.office.deepin |
| 钉钉 | com.dingtalk.deepin |
| 阿里旺旺 | com.taobao.wangwang.deepin |
| QQ音乐 | com.qq.music.deepin |
| QQ视频 | com.qq.video.deepin |
| 爱奇艺 | com.iqiyi.deepin |

完整列表参见[https://deepin-wine.i-m.dev](https://deepin-wine.i-m.dev)

如果安装报错：`libsasl2***无法下载`
可以去这里手动下载：[http://mirrors.163.com/ubuntu/pool/main/c/cyrus-sasl2/](http://mirrors.163.com/ubuntu/pool/main/c/cyrus-sasl2/)

## 卸载

Deepin Wine平台位于`.deepinwine`文件夹中。由于它是一个隐藏的文件夹，因此您需要按Ctrl + H才能在文件浏览器中显示/隐藏它。您可以将其删除以清除Wine配置。

### 清理应用运行时目录

例如QQ/TIM会把帐号配置、聊天文件等保存`~/Documents/Tencent Files`目录下，而微信是`~/Documents/WeChat Files`，删除这些文件夹以移除帐号配置等数据。

### 清理wine容器

deepin-wine应用第一次启动后会在`~/.deepinwine/`目录下生成一个文件夹（名字各不相同）用于存储wine容器（可以理解为一个“Windows虚拟机”），如果使用出了问题，可以试试删除这个目录下对应的子文件夹。

### 卸载软件包

执行`sudo apt-get purge --autoremove <包名>`命令把你安装过的包给移除。

### 移除软件仓库

```bash
sudo rm /etc/apt/preferences.d/deepin-wine.i-m.dev.pref \
sudo rm /etc/apt/sources.list.d/deepin-wine.i-m.dev.list \
sudo rm /etc/profile.d/deepin-wine.i-m.dev.sh
sudo apt-get update
```