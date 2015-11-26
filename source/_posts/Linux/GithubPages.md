title: 用Hexo部署GithubPages
author: Duanfeng Zhang
category: Tech & Tools
tag: [GithubPages,hexo]
date: 2014-10-10
---
##Hexo简介
Hexo是一个基于Node.js的静态博客程序，其编译上百篇文字只需要几秒。Hexo生成的静态网页可以直接放到GitHub Pages，BAE，SAE等平台上。Hexo主要有以下三个特点:
**1.风一般的速度:**Hexo基于Node.js，支持多进程，几百篇文章也可以秒生成。
**2.流畅的撰写:**支持GitHub Flavored Markdown和所有Octopress的插件。
**3.插件丰富和扩展性:**除了具备大量主题包,还可以通过引用成熟的插件库完成对评论系统,Latex公式等各类扩展应用支持

## 准备工作
**Node.js**
```bash
sudo  yum install g++ curl openssl openssl-devel make gcc-c++ glibc-devel -y #安装GCC编译环境
wget http://nodejs.org/dist/node-latest.tar.gz #下载NodeJs
tar -xvpzf node-latest.tar.gz
cd node-v*

sudo  ./configure  
sudo make & make install #编译与安装
```
**Github环境与帐号**
**npm环境**
```
su
curl http://npmjs.org/install.sh | sh 
node --version #查看NodeJS版本
```
## 本地搭建hexo
```bash
#npm安装需要su权限
npm install hexo-cli -g
mkdir blog
hexo init blog
cd blog
npm install
hexo server
```
## 更换皮肤

* 1.下载Theme包,hexo官网或其他论坛
* 2.加压到 /blog/themes文件夹下
* 3.更改 blog/_config.yml 中theme:name (name为theme包的文件夹名)
* 4.更新设置
```
hexo g
```
## 配置到GithubPages
**创建仓库**
```
username.github.io (username为github应用名,不能用其他字符串,否则打不开)
```
**配置blog/_config.yml**
```
#1.冒号后面有一个空格
#2.repository 用仓库的ssh
deploy:
  type: git
  repository: git@github.com:cynorr/cynorr.github.io.git
  branch: master
```
**hexo下配置git**
```
# npm install hexo-deployer-git --save
```
**更新上传**
```
hexo g
hexo d
```
## 设置域名
* 在namecheap网站购买域名,用途设置GithubPages(免域名解析的麻烦)
* 本地source文件夹下创建CNAME文件,内容为购买的域名,去掉www,例如:
```
cyno.me
```
* 刷新
```bash
hexo g
hexo d
```
