title: Linux下Github的配置与使用
author: Duanfeng Zhang
category: Fedora Customized
tag: Github
date: 2014-01-21
---
##简介

github是备份代码首选工具，工作机制如下:
![](http://7xjfxk.com1.z0.glb.clouddn.com/github.png)

working dir是本地目录，HEAD是云目录，中间的index是缓存区。若要实现本地与github上同步，首先把要同步或要修改的文件或代码在缓存区（index）整理，最后统一提交（commit）到云。
##准备工作

准备工作比较麻烦，但一劳永逸.
**1.申请帐号：[Click Here](https://github.com/)**

* 邮箱：cynorr@sina.com
* 用户名：sinorr

**2.创建Repository**

* 点击 **New Repository**  
* 创建一个名为HelloWorld的Repository。

**3.配置SSH keys**
在本地终端下:
```
$ ssh-keygen -t rsa -C "cynorr@sina.com"    #引号里面填写你的github邮箱

Creates a new ssh key using the provided email
Generating public/private rsa key pair.
Enter file in which to save the key (/home/.ssh/id_rsa): #直接点回车

$ Enter passphrase (empty for no passphrase):            #设置密码，这是以后在终端下链接github的密码
$ Enter same passphrase again:                              

$ eval  "$(ssh-agent -s)"           
Agetn pid xxxxx   
$ ssh-add ~/.ssh/id_rsa
$ gedit ~/.ssh/id_rsa.pub  #然后把里面的内容全选，复制到剪切板
```
进入你的github主页,依次进入
```
设置 -> SSH keys -> Add SSH key
```
标题自定，将剪切板上内容粘贴进去。

**4.全局配置**
```
git config --global user.name "sinorr"         #引号里填自己github的用户名
git config --global user.email cynorr@sina.com #田写github邮箱
```


##上传文件

```
$ cd workspace/
$ git init        #初始化本地文件夹
$ git add HelloWorld/      #将文件放到缓存区。
$ git commit -m 'all file'  #commit是从缓存区到github上的操作集合，例如本次的操作只是add HelloWorld/
$ git remote add test git@github.com:sinorr/HelloWorld.git   #指定要存的Repository
$ git push -u test master # 最后一步，执行同步命令。
```

##下载文件


```
$ git clone git@github.com:sinorr/spider.git
```
