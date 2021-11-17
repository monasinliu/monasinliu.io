# Mac OS 安装 Nginx

## 1. 安装

几个教程都推荐用 homebrew 安装，但是我实际操作安装了几次 homebrew 都失败了，于是就搜了不需要 homebrew 安装的方法。

https://blog.csdn.net/weixin_42499379/article/details/113585696

这篇推荐是使用 homebrew 中科大的国内镜像

`/usr/bin/ruby -e "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install)"` 

以上的安装命令。

现在已经成功安装了，安装过程中镜像推荐使用 bash 安装

`/bin/bash -c "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install.sh)"` 

国内的镜像会在系统没有安装 xCode 的情况下安装 **Xcode Command Line Tools**

