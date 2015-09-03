title: VMware WorkStation for linux
author: 张端风
date: 2015-06-16
category: Fedora Customized
tag: VMware虚拟机
---
##简介
Linux系统是程序员主要开发环境,但是linux在办公应用方面做的并不完美,比如部分打印机不支持和QQ版本不稳定.为避免双系统频繁切换问题,有必要在linux下安装虚拟街.VMware是目前最优秀的虚拟机,内存读取速度,网速,硬盘读取速度等各个参数完胜VirtualBox,本文介绍Fedora下VMware虚拟机的安装.

##准备工作
* 安装文件:[VmwareStation11 for Linux](1F04Z-6D111-7Z029-AV0Q4-3AEH8)
* VMware11永久序列号:1F04Z-6D111-7Z029-AV0Q4-3AEH8
* 配置gtk-2.0
```
su
#安装gtk
yum install gtk* -y
locate pk-gtk-module.so
> /usr/lib/gtk-2.0/modules/pk-gtk-module.so #根据查找,确定gtk-2.0目录

#添加modules路径
vim /etc/ld.so.conf.d/gtk-2.0.conf #新建gtk-2.0-conf文件
/usr/lib/gtk-2.0/modules               #添加modules目录
ldconfig
```
* 链接kernel headers
```
su
#查找linux version目录
locate version.h | more
> /usr/include/linux/version.h

ln -s /usr/include/linux/version.h /lib/modules/3.17.04.fc21.x86_64/build/include/linux/ 
#上文build是/usr/src/kernel/3.17.04.fc21.x86_64的链接,如果失效青重建链接
```

##安装VMware
```
chmod u+x VMware-Workstation-Full-11.1.2-2780323.x86_64.bundle
su
./VMware-Workstation-Full-11.1.2-2780323.x86_64.bundle
```
##安装Win8.1
```
新建虚拟机
配置类型:自定义(高级)
硬件兼容性:workstation11
安装客户机操作系统:稍后安装系统
选择客户机操作系统:Win8.1选windows8
命名虚拟机:默认
处理器配置:处理器数量1,每个处理器核心数量2(个人主机几乎都只有1各处理器,核心2个或4个,区核心数量一半即可)
内存:3G
网络类型:NAT(第二个)
I/O控制器:默认
磁盘类型:SATA(A)
选择磁盘:创建新的虚拟磁盘
容量:自定,打勾立即分配磁盘,选择存储为单个文件
自定义硬件 -> 新CD/DVD -> 使用ISO镜像文件 -> 选择win8镜像
```
