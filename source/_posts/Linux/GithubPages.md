title: 用Hexo部署GithubPages
author: Cyno
category: Tech & Tools
tag: [GithubPages,hexo]
date: 2014-10-10
---
### Hexo简介
Hexo是一个基于Node.js的静态博客程序，以闪电版的生成速度博得个人博主喜爱。其工作原理非常简单，首先在本地编写博客，然后将博客转化成静态网页，最有将静态网页上传到所需平台。Hexo采用极为高效的markdown格式，避免了排版的繁琐，让各博主专心书写内容。所生成的静态网页风格支持各个种类的主题提供选择，也可自行修改CSS等。更重要的是其生成速度，其编译上百篇文字只需要几秒。除此外，Hexo还可以通过引用成熟的插件库，如评论系统,和Latex公式的扩展。

### 安装和配置Hexo
#### 1.Node.js
```bash
 #安装GCC编译环境
sudo  yum install g++ curl openssl openssl-devel make gcc-c++ glibc-devel -y

#下载并安装NodeJs
wget http://nodejs.org/dist/node-latest.tar.gz
tar -xvpzf node-latest.tar.gz
cd node-v*

./configure  
make
sudo make install #编译与安装
```
#### 2.npm环境
```
sudo curl http://npmjs.org/install.sh | sh
node --version #查看NodeJS版本
```
#### 3.本地搭建hexo
```bash
# as root
npm install hexo-cli -g
mkdir blog
hexo init blog
cd blog
npm install
hexo s
```

#### 4.配置到GithubPages
* 4.1创建仓库**username.github.io**(username必须为github应用名,不能用其他字符串,否则网页打不开)
* 4.2安装Hexo git插件
```bash
npm install hexo-deployer-git --save
```
* 4.3配置**_config.yml**
```
#1.冒号后面有一个空格
#2.repository 用仓库的ssh
deploy:
  type: git
  repository: git@github.com:username/username.github.io.git (username为你github用户名)
  branch: master
```
#### 5.设置域名（optional）
* 在namecheap网站购买域名,用途设置GithubPages(免域名解析的麻烦)
* 本地source文件夹下创建CNAME文件,内容为购买的域名,去掉www,例如:
```
cyno.me
```
#### 6.Enjoy it
```bash
hexo g
hexo d
```

#### 7.更换皮肤

* 1.下载Theme包,hexo官网或其他论坛
* 2.解压到 /blog/themes文件夹下
* 3.更改 blog/_config.yml 中theme:name (name为theme包的文件夹名)

**安装过程结束，以下是博主主题CSS备份，无关安装**
### CSS备份

---

#### 1.调整语法高亮配色和边缘
** /themes/freemind/source/css/highlight.css**
更改了background，margin，padding
```
pre, .highlight, .gist {  
  background: #1a1a1a !important;
  margin: 0.1em 2%;
  padding: 0.3em 2%;
  overflow: auto;
  color: #fffafa;  
  font-size: 14px;
  text-shadow: none;
  -webkit-border-radius: 4px;
  -moz-border-radius: 4px;
  border-radius: 4px;  
  border-style: solid;
  border-color: #ddd;
  border-width: 1px 0;
  line-height: 20px;
}
```
#### 2.调整categories样式
** /themes/freemind/source/css/style.css**
添加链接里a下的b样式，主要更改padding右值，margin右值，border宽度
```
.tag_box a.b {
  padding: 4px 50px 2px 6px;
  margin: 2px 40px 2px 2px;
  background: #e5e5e5;
  color:#555;
  border-radius: 3px;
  text-decoration:none;
  border:0px dashed #bbb;
}
.tag_box a.b span{
  vertical-align:super;
  font-size:0.8em;
}
.tag_box a.b:hover {
  background-color:#397bdd;
  color:#FFF;
}
.tag_box a.b.active {
  background:#57A957;
  border:1px solid #4C964D;
  color:#FFF;
}
```
然后，在框架设置里使用该设置，即在<a href="..." >中添加 class="b",确定格式：
**/themes/freemind/layout/_widget/category.ejs**
```
<li><a href="<%- config.root %><%- item.path %> class="b""><%= item.name %><span><%= item.   posts.length %></span></a></li>

```
#### 3.更改header渐变颜色
** /themes/freemind/source/css/style.css**
更改background: -moz-linear-gradient(top, #333, #1a1a1a 50%); 即header背景从上到下333到1a1a1a渐变，333同主标题背景，1a1a1a同语法高亮背景。
```
.page-header {
  -webkit-background-clip: border-box;
  -webkit-background-origin: padding-box;
  -webkit-background-size: cover;
  /* background-color: #f5f5f5; */
  padding: 10px 20px 0px 20px;
  margin: -20px -20px 20px;
  background: #333;
  background: -moz-linear-gradient(top, #333, #1a1a1a 50%);
  background: -webkit-gradient(linear, 0 0, 0 50%, from(#333), to(#000));
  color: #e9e9e9;
  border-bottom-left-radius: 0px;
  border-bottom-right-radius: 0px;
  border-top-left-radius: 6px;
  border-top-right-radius: 6px;  
}
```
*参考* [CSS < a  href>链接样式冲突解决方法！](http://blog.sina.com.cn/s/blog_60f01ce50100ffeo.html)
