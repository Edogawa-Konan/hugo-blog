---
title: "Manjaro"
date: 2019-11-24T11:20:49+08:00
lastmod: 2019-11-24T11:20:49+08:00
draft: false
tags: ['manjaro']
categories: ['Linux']
---

# manjaro

## 配置源

### 包仓库的源

```bash
sudo pacman-mirrors -i -c China -m rank
```

### Archlinuxcn

Arch Linux 中文社区仓库 是由 Arch Linux 中文社区驱动的非官方用户仓库。包含中文用户常用软件、工具、字体/美化包等。

使用方法：在 `/etc/pacman.conf` 文件末尾添加以下两行：

```sh
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

之后安装 `archlinuxcn-keyring` 包导入 GPG key。

```bash
sudo pacman -S archlinuxcn-keyring
```

## 启动ssh

```bash
sudo systemctl start sshd #立刻启动
sudo systemctl enable sshd.service #添加到开机自启服务
```

## 包管理

在Arch下安装软件包时，未更新系统前（`-u`），**不要**更新软件包数据库(`-y`)（例如，可能出现某软件包不再出现在官方库）。

```bash
sudo pacman -Syu # 升级系统，-u(--sysupgrade)更新本地所有包。
sudo pacman -Syy  # 更新包数据库，两次yy是强制刷新本地包数据库，一次y（--refresh）表示下载最新包数据库从源中，应该在每次-u前使用。
```

## 安装搜狗输入法

```bash
sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-im     # 全部安装
sudo pacman -S fcitx-configtool     # 图形化配置工具
```

然后要将fcitx加入到环境变量中去，不然就不能激活fcitx输入法。

```bash
sudo vi ~/.xprofile  # vi可以换成系统现有的文本编辑器
```

```bash
# 然后在里面添加：
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=”@im=fcitx”
```

## 配置shadowsocks

首先在应用中心中安装程序shadowsocks-qt。

然后配置相关的账户信息。

之后需要使用chrome内的proxy switchomega插件在chrome内完成翻墙。（由于现在chrome不能离线安装插件了，可以先把crx文件改为zip等，解压后用chrome加载）。

然后参考[wiki](https://github.com/FelisCatus/SwitchyOmega/wiki/GFWList)配置即可完成！

最后，可以删掉加载的扩展程序，重新在chrome商店中install一遍。

## 安装yay

yay是一个pacman的包装，让其可以支持AUR（Arch User Repository）仓库中包的安装，这部分包在官方的pacman中是不存在的。

可以直接通过manjaro自带的Octopi图形化包管理工具安装yay。



