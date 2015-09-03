title: Github使用详解
author: 张端风
date: 2014-02-01
category: Fedora Customized
tag: Github
---
##Git简介
Git 是 Linus 在开发 Linux 内核时用于替换 Bitkeeper 版本控制工具而写的一个开源的分布式版本控制软件。而Github 是一个代码托管网站。而代码托管的意思是允许人们把代码放到 Github ，并为团队开发提供一种比较简单的代码同步方法。在git里，每个代码库在相互独立的同时，又可以相互交换代码（通过push/pull）进行代码的交换。

##文件状态与实例
在Git中，文件只有三种状态，已修改，已暂存和已提交。这三个文件分别放在工作区，暂存区域和git目录。当一个文件修改完毕之后，它仍然在工作目录；只有当它被add之后，才会进入暂存区域，文件状态变为已暂存；最后，当文件被 commit之后，它就会进去git目录区域。下面我们举一个具体实例:

> 假设工作区中有main.cpp和ReadMe两个文件,需要把工作区同步到Git,并且新建模块function.cpp.

```
git add -A #添加所有文件到暂存区
git commit -m 'Main Function' #提交文件到Git目录
git status #查看将要提交的操作

git remote add origin git@github.com:cynorr/Test.git #Test.git的远程端
git push -u origin master #推送到Test所有分支,-u参数的作用是同时更新主分支和继承主分支的所有分支
```
至此,主函数的同步已经完成,下面添加模块function.cpp
```
vim function.cpp #在工作区创建模块function.cpp,模块内容自定,本文只作演示
git checkout -b func #新建本地func分支,并切换到func分支
git remote origin func #新建Git func分支

git add function.cpp  
git commit -m 'New Function' 
git push origin func #将新建模块推送到func分支

git checkout master
git merge func #合并func分支到主分支
git push origin master #把合并的分支推送到云

git branch -D func
git push origin :func #删除本地和Git上的func分支
```
##操作解析
###工作区状态
查看工作区操作和文件状态用命令**git status**.
```git

Untracked files: #Git无处理操作的文件
	文件列表:
	helloworld.cpp
	...

Changes to be committed #Git已处理但未提交的处理操作
	操作列表:
	new file:   .gitignore   #增加操作
	new file:   Firefox_wallpaper.png
	deleted:    nohup.out    #删除操作

nothing to commit, working directory clean #处理并提交完成,等待推送状态

```
###筛选
在与.git文件夹同级的目录中新建**.gitignore**文件,内容自定:
```
.gitignore   #同步时忽略.gitignore文件
bin/         #同步时忽略目录下所有bin文件夹及子文件(夹)
*.class   	 #同步时忽略所有后缀为.class的文件
```
###增
```bash
git add -A   #stages All  
			 #本地和Git完全同步

git add .    #stages new and modified, without deleted
    		 #Git只增不减,本地修改/增加文件Git同步,本地删除文件git不删除 

git add -u   #stages modified and deleted, without new
			 #Git只减不增,本地修改/删除文件Git同步,本地新建文件Git不新建
```
###删
主要区别在于参数**--cached**,本参数决定是否将本地的文件随Git一同删除
```bash
#git rm file/path 前提是git里存在file/path

git rm --cached  readme.txt  #只删除git里的readme.txt
git rm readme.txt #删除本地和git里的readme.txt
```
###提交
```
git commit -m  'This is my second modified'  	#-m参数后面是本次提交的解释信息,不限格式可有空格
git commit -am  = git add u + git commit -m     #两步合并成一步,只减不增
```
###对比
本地与Git的对比主要用**git diff**,该命令的使用方法非常灵活,我们只要记住原始命令,即可以查看目前本地与Git的不同处.
```
git diff
git diff --stat
```
###分支与合并
为保证工程的安全性和可扩展性,我们需要保持工程架构不变.Git的分支满足了这个需求,我们将工程架构放在主分支上,每调试或添加新的特性,我们都为其开辟一个分支,分支对主分支是继承关系,把主分支所有文件pull过去并添加新的特性.创建新分支的基本规则:
	* 1.创建新分支前确保主分支已经完整最新,满足新分支的基础需求.
	* 2.新分支尽量减少对pull过来的主分支部分进行删改(否则合并时会处问题).
	* 3.逐个添加新分支与合并,同时建多个新分支合并时容易发生冲突.
新建分支:
```
#条件:远程端test存在(远程端是本地和Git交互的桥梁,一般一个Git只需要一个远程端)
git remote add test git@github.com/cynorr:Test.git 


git branch host #新建host分支
git push test host #host分支同步到Git

git checkout host #切换到host分支
git branch    #查看所在分支

git push test host #对新分支进行修改操作
git branch -D host #删除本地host分支
git push test :host #删除Git host分支

```
合并:
```
git checkout master #切换到主分支后才能进行合并
git merge host

#新合并到主分支后,新后分支已没用,将其删除
git push test :host
```
###远程库
```
git remote add origin git@github.com/cynorr:Test.git #添加远程库
git remote rename origin test #重命名远程库
git remote -v #远程库详细信息
git remote rm test #删除远程库
```