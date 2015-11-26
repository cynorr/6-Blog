title: Rsync+Sersync实现服务器数据单向触发式同步
author: 张端风
category: Tech & Tools
tag: rsync + sersync
date: 2015-06-26
---
##简介
触发式同步也称为实时同步,即当源端文件改变时,监控工具立刻触发同步程序,精确快速的将改变的文件推送到目标端,保证最小运行消耗的前提下实现目标端与源端统一.搭建实时同步架构是搭建多人协作平台的主要途径,也是数据备份避免无法补救灾难的重要措施.本文介绍基于rsync同步算法的触发实时同步组合Rsync+Sersync,实现大数量级多文件层数据高速灵敏实时同步.

Rsync 是用于取代rcp的一个工具，使用Rsync 算法来使本地和远程两个主机之间的文件统一，该算法只传送源端与目标端的不同部分，而不是每次都整份传送，因此提高了同步速度。sersync是基于inotify的监听工具,可以记录下被监听目录中发生变化的（包括增加、删除、修改）具体文件或目录，源端数据发生改变时触发推送,达到实时同步。除本文介绍的组合,还有另一种主流同步组合Rsync+Inotify,两个组合主要区别在于监控触发工具Inotify和Sersync,下面阐述两个监控触发工具的不同点:

* 1.Inotify只能记录下被监听的目录变化,sersync可以记录下被监听目录下具体文件变化,Inotify是对被修改的文件直属文件夹整个重新备份,造成资源浪费,而Sersync能精确到具体文夹.
* 2.sersync是使用c++编写，Inotify为脚本语言,两者相比sersync具有更高的规范性和执行效率.
* 3.sersync支持同时向多个服务器(vps)实时同步.
综上,在性能,速度,功能上Rsync+Sersync组合都优于Rsync+Inotify.

##主要步骤
**源端:**源端不需要ip地址,所以其应用范围比较广,即可以实现静态ip的服务器,也可以实现动态ip的个人主机.
**目标端:**务必具备局域网和公网的IP,目标端可以是本地服务器也可以是vps.
**同步方式**:支持一个源端向多个目标端同步,也支持多个源端向一个目标端同步.
**权限:**源端和目标端均没有强制root权限要求,但是为了保证安全性,优先使用root权限.

下面用下列配置进行方法说明:

|         | 源端           | 目标端  |
| ------------- |:-------------:| -----:|
| 系统      | Fedora21 | CentOS|
|性质|个人主机/服务器| 服务器/VPS|
|权限|Root|普通用户/root|
| IP      | 动态/静态 |   107.170.213.95|
| 路径|    /home/user1/source/   |   /home/user2/target/   |

* Step1:建立源端向目标端的SSH信任,实现单向免密码直连
* Step2:在目标端设立监听与接收端口
* Step3:在源端建立实时推送

## 具体实现
###Step1:建立源端到目标端SSH信任

源端:

```bash
cd 
rm -rf .ssh
ssh-keygen -t rsa
Enter file in which to save the key (/user1/.ssh/id_rsa):   #回车
Enter passphrase (empty for no passphrase):    #回车
eval `ssh-agent`  #是～键上的那个`
ssh-add

#此时本机SSH Key已经生成,默认公钥位置: .ssh/id_rsa.pub,将其拷贝至目标端
scp .ssh/id_rsa.pub user2@107.170.213.95:.ssh/authorized_keys  #如果目标端无.ssh目录,自行在目标端建立在执行此命令
```
本文使用DigitalOcean虚拟机作目标端,因此除了向系统里添加公钥,还需向DigitalOcean官网设置里添加公钥,此公钥也可用于Github.
###Step2:在目标端设立监听与接收端口
登录至目标端:

* 2.1关闭SELINUX
* 2.2关闭ip防火墙

```bash
su  #需要借用root权限
vim /etc/selinux/config  

#找到以下两行并注释掉:
SELINUX=enforcing
SELINUXTYPE=targeted 

#添加下列一行:
SELINUX=disabled


#关闭ip防火墙
systemctl stop firewalld.service (暂时关闭)
#或者
systemctl disable firewalld.service(永久关闭)
```

* 2.3安装Rsync

```
sudo yum install rsync -y  #安装
```

* **2.4配置rsync文件
配置参数是关键步骤,务必仔细.**rsync的配置文件分全局参数和模块参数,其中模块就是指仓库,一个库只允许一个源端同步,但是rsync可以建立多个库,实现多个源端同时向一个目标端同步.每个库主要由库名,存放路径,库管理员组成.全局参数决定rsync运行的维护者和性能.参数文件具体如下:
文件存放位置: **/etc/rsyncd.conf**,如果没自动生成,请手动创建

```bash
#全局参数
 uid = root 
 gid = root
 use chroot = yes 
 max connections = 4
 log file = /home/user2/log       # <-----日志文件,用于调试,路径自设

#模块,可以设置多个
 [myhub1]                         # <-----(重要)库名,作源端参数
 path = /home/cyno/target/        # <-----库位置,打开读写权限
 comment = PC back up             # <-----库说明
 ignore errors = yes                
 read only = no                   # <-----(重要)关闭只读
 auth users = admin1              # <-----(重要)管理员姓名,下一步为管理员设置密码,作源端参数
 secrets file = /etc/rsyncd.secret# <-----(重要)存储管理员和密码,做源端参数
```

* **2.5设置库管理员密码**

```
echo "admin1:1234"  >> /etc/rsyncd.secret # <-----路径为配置文件里的secrets file
                                          # <-----格式为"name:key",中间用冒号隔开
```

* **2.6配置文件设置权限**
否则rsync会因安全问题启动失败.两个配置文件的读写权只属于uid和gid用户,本次uid和gid用户设为root所以配置文件的读写权管理者为root,如果uid和gid用户为某个普通用户,则读写权限只属于该普通用户.

```
sudo chmod 600 rsyncd.conf rsyncd.secret  
```

* 2.7启动目标端rsync监听

```
su
rsync --daemon --config=/etc/rsyncd.conf   #可以添加的开机自启动
```

###Step3:在源端建立实时推送

* 3.1安装rsync和配置文件:
源端安装rsync与目标端不同,目标端安装rsync是为了建立仓库和传送,需要配置仓库参数,而源端安装rsync只为传送,只需配置全局变量.

```
#安装
sudo yum install rsync -y
```

	配置文件路径(/etc/rsyncd.conf),内容如下:

```
#全局参数
 uid = root       
 gid = root
 use chroot = yes 
 max connections = 4
 log file = /home/user2/log  #日志文件,用于调试,路径自设
```

* 3.2安装Sersync:

```bash
wget http://sersync.googlecode.com/files/sersync2.5.4_64bit_binary_stable_final.tar.gz #需要翻墙
tar -zxvf sersync2.5.4_64bit_binary_stable_final.tar.gz   

#得到GUN-linux-x86文件夹(很奇怪的命名),文件夹里只有两个文件,参数文件*confxml.xml*和可执行文件*sersync2*.

mv GUN-Linux-x86 /uer/local/sersync
```

* **3.3配置Sersync参数文件
配置参数是关键步骤,务必仔细.**参数文件很长,但是需要设置的地方却很少,主要实现和Step2的目标仓库端口进行对接.为了方便观看,本文只列出需要修改的地方,内容如下:

```bash
<sersync>
	<localpath watch="/home/user1/source/">    # <---源端需要同步的文件夹
	    <remote ip="107.170.213.95" name="myhub1"/> # <---目标端Ip,目标端模块name,与Step2.4模块名统一
	    <!--<remote ip="192.168.8.39" name="tongbu"/>-->  # <---可以设置多个目标端,多份备份,
	    <!--<remote ip="192.168.8.40" name="tongbu"/>-->
	</localpath>
	<rsync>
	    <commonParams params="-artuz"/>
	    <auth start="true" users="admin1" passwordfile="/etc/rsync.pas"/>  # <---users为库管理员,与Step2.4库管理员统一,passwordfile为该管理员的密码,每个密码一个文件,详见Step3.4
	    <userDefinedPort start="false" port="874"/><!-- port=874 -->
	    <timeout start="false" time="100"/><!-- timeout=100 -->
	    <ssh start="false"/>
	</rsync>
```

* **3.4配置库管理员密码文件**

```
echo "1234" >> /etc/rsync.pas  # <-----密码与Step2.5统一
                               # <-----密码文件路径与Step2.4 passwordfile统一

chmod 600 /etc/rsync.pas       # <-----(重要)设置权限,否则sersync会因安全问题无法启动
```

* 3.5启动Sersync

```bash
#可以添加到开机自启动
sudo /usr/local/sersync/sersync2 -r -d -o  /usr/local/sersync/confxml.xml  
```

###Step4:测试
在源端向source文件夹下,添加或修改文件,观察目标端target文件夹内容变化.

##参考
* 1.rsync参数:[CentOS 6.3下rsync服务器的安装与配置](http://www.cnblogs.com/mchina/p/2829944.html)
* 2.sersync应用:[sersync - Google Code](http://code.google.com/p/sersync/)
