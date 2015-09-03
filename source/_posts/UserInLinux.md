title: Linux下用户管理
category: Fedora Customized
tag: [Fedora,用户管理]
date: 2013-09-10
---
##用户增删&密码管理
* root权限下
```bash
#增加用户
useradd guest
passwd guest

#删除用户
userdel guest
```
* 普通用户权限
```bash
#更改密码
passwd
```
##权限管理
**赋予普通用户root权限**
* 方法1:
```bash 
su
usermod -g root guest
```
* 方法2:
修改/etc/sudoers文件,先打开该文件,找到如下一行:
```bash
## Allow root to run any commands anywhere
root       ALL=(ALL)            ALL
```
在root下一行添加需要root权限的用户
```bash
## Allow root to run any commands anywhere
root       ALL=(ALL)            ALL
guest       ALL=(ALL)            ALL
```

