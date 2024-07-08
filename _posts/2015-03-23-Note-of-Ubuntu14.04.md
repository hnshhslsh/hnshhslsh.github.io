---
layout: post
title: "ubuntu14.04配置个人笔记"
categories:
- Linux
tags:
- Linux
- Ubuntu
last_modified_at: 2018-06-08 15:31:24 +08:00
---

* content
{:toc}

本文记录了我的Ubuntu配置笔记，文末已经更新到了关于 18.04 TLS 的内容。本文在本人新博客的链接：[http://www.myblog.link/2015/03/23/Note-of-Ubuntu14.04/](http://www.myblog.link/2015/03/23/Note-of-Ubuntu14.04/)

------

## 解决安装中文后字体变化
``` shell
sudo apt-get remove fonts-arphic-ukai fonts-arphic-uming
```

## 添加右键打开终端
``` shell
sudo apt-get install nautilus-open-terminal
```

【Update】16.04 之后右键已有打开终端。





## WPS 32位环境
``` shell
sudo apt-get install libgtk2.0-0:i386
```

【Update】WPS已有64位版本。

## JDK设置环境变量

```shell
vim ~/.bashrc
```

在底部加入

``` shell
export JAVA_HOME=安装目录  
export JRE_HOME=${JAVA_HOME}/jre   
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib   
export PATH=${JAVA_HOME}/bin:$PATH
```
使用命令
``` shell
source ~/.bashrc
```

## 安装搜狗拼音
安装后，终端中输入`im-config`，修改ibus为fcitx，重启。

## 安装 Oracle Datebase instant client
下载：
http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html
解压后，
``` shell
vim ~/.bashrc
```
添加
``` shell
export ORACLE_HOME=解压目录
export PATH=$PATH:$ORACLE_HOME
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME
```
执行
``` shell
source ~/.bashrc
```
如果提示缺少libaio则执行：
``` shell
sudo apt-get install libaio-dev
```
## 添加exFat支持
``` shell
sudo apt-get install exfat-fuse
```

## 下载工具安装
``` shell
sudo add-apt-repository ppa:plushuang-tw/uget-stable
sudo apt-get update
sudo apt-get install uget aria2
```
安装后运行uGet，设置启用aria2插件，还可以右击分类里的home选属性将每台服务器的连接加到16

## Qualcomm Atheros AR9485 无线网卡驱动问题
使用的是华硕笔记本电脑，系统能识别网卡，但搜素不到 WiFi 信号（硬件开关无法打开）
可以通过设置“asus_nb_wmi”驱动参数来解决，执行下面的命令：
``` shell
echo "options asus_nb_wmi wapf=1" | sudo tee /etc/modprobe.d/asus_nb_wmi.conf
```
也可能是驱动冲突所致，通过屏蔽“acer-wmi”可以解决：
``` shell
echo "blacklist acer-wmi" | sudo tee -a /etc/modprobe.d/blacklist.conf
```

【Update】16.04 之后貌似已经解决了该问题。

## 修改各用户文件夹位置

/home/你的用户文件夹/.config/user-dirs.dirs

## 开机自动挂载磁盘
修改配置文件`/etc/fstab`，配置文件包含以下几项：  
- `<file system>` ：分区定位，可以给磁盘号，UUID或LABEL，例如：/dev/sda2，UUID=6E9ADAC29ADA85CD或LABEL=software  
- `<mount point>` : 具体挂载点的位置，例如：/media/C 
- `<type>` : 挂载磁盘类型，linux分区一般为ext4，windows分区一般为ntfs 
- `<options>` : 挂载参数，一般为defaults 
- `<dump>` : 磁盘备份，默认为0，不备份
- `<pass>` : 磁盘检查，默认为0，不检查 

检查并挂载新添项：
``` shell
sudo mount -a
```
mount -a会将/etc/fstab中的项全部挂载，如果有错，则会提示错误，然后根据错误找出原因修改。  
注意：千万不要挂载到当前用户的根目录，因为挂载的分区会覆盖当前分区内容  

参考：http://www.linuxidc.com/Linux/2013-02/79679.htm

## 添加程序快捷方式到桌面
``` shell
sudo nautilus
```
进入/usr/share/applications，然后复制粘贴

## 将Caps Lock 映射为Esc
``` shell
xmodmap -e 'clear Lock' -e 'keycode 0x42 = Escape' 
```

## 64位gcc编译32位程序
``` shell
sudo apt-get install g++-multilib libc6-dev-i386
```
然后编译的时候加上-m32，用CodeBlocks的话记得把linker也设置一下这个参数。

## 安装windows软件
- wine安装：http://wiki.winehq.org/Ubuntu 

  就可以直接运行部分exe了  

- 一个配合wine的实用的工具https://github.com/hillwoodroc/winetricks-zh 

  可自动安装QQ、网易云等常用windows应用 

----

## Ubuntu 18.04 添加显示桌面

其实系统自带了一个快捷键【Super】+ 【Ctrl】 + 【D】。（【Super】就是微软徽标键，快捷键组合比16.04多了一个【Ctrl】。）

在“Ubuntu软件”里搜索“Show Desktop Button”，安装较新的那个。装好之后会在左上角找到一个房子图标，点击可以显示桌面。（描述是“I had the need of a 'show-desktop-button'. I found an extension that does exactly what I want, but it was quite old. I tried to update it a little bit and make it work……”的那个。）

## Ubuntu 18.04 安装WPS

1.进入[WPS官网](http://wps-community.org/download.html)下载最新版安装包及[字体文件](http://wps-community.org/download.html?vl=fonts#download)，当前最新的是`wps-office_10.1.0.5707~a21_amd64.deb`和`wps-office-fonts_1.0_all.deb`； 
2.下载[libpng12-0](https://packages.debian.org/zh-cn/wheezy/amd64/libpng12-0/download)；

```shell
$ wget http://ftp.cn.debian.org/debian/pool/main/libp/libpng/libpng12-0_1.2.49-1+deb7u2_amd64.deb1
```

3.终端下安装：

```shell
$ sudo dpkg -i libpng12-0_1.2.49-1+deb7u2_amd64.deb
$ sudo dpkg -i wps-office_10.1.0.5707-a21_amd64.deb
$ sudo dpkg -i wps-office-fonts_1.0_all.deb123
```

 参考自 [https://blog.csdn.net/gongchenyu/article/details/80500150](https://blog.csdn.net/gongchenyu/article/details/80500150)

## Ubuntu 18.04 Anaconda中的Spyder无法启动

运行Anaconda自带的 Spyder ，报Segmentation fault (core dumped)，解决方案：

``` shell
pip3 install pyopengl
```

如果提示pip3未安装，则先执行

``` shell
sudo apt install python3-pip
```

参考自 [https://blog.csdn.net/qq_41765457/article/details/80531753](https://blog.csdn.net/qq_41765457/article/details/80531753)