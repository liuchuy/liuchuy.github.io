一：DevOps简介

DevOps是Development和operations的组合，也就是开发和运维的简写

DevOps是针对企业中的研发人员、运维人员和测试人员的工作理念，是他们在应用开发、代码部署和质量测试等整条生命周期中协作和沟通的最佳实践，DevOps强调整个组织的合作以及交付和基础设施变更的自动化、从而实现持续集成、持续部署和持续交付

DevOps四大平台：代码托管（gitlab/svc）、项目管理（jira）、运维平台（腾讯蓝鲸/开源平台），持续交付（jenkins/gitlab）



## 1.1 什么是DevOps

![](/images/posts/04_Jenkins/00/1.png)



## 1.2: 为什么要推广DevOps？

DevOps强调团队协作、相互协助、持续发展，然而传统的模式是开发人员只顾开发程序，运维只负责基础环境管理和代码部署及监控等，启时不是为了一个共同的目标而共同实现最终的目的，而DevOps则实现团队作战，既无论是开发、运维还是测试，都是为了最终的代码发布，持续部署和业务稳定而付出各自的努力，从而实现产品设计、开发、测试和部署的良性循环，实现传品的最终持续交付



![](/images/posts/04_Jenkins/00/2.png)



## 1.3：DevOps技术团队





![](/images/posts/04_Jenkins/00/3.png)



## 1.4：什么是持续集成（CI-Continuous integration）

持续集成是指多名开发者在开发不同功能代码的过程中，可以频繁的将代码行合并到一起并且相互不影响工作

## 1.5: 什么是持续部署（CD-continuouts deployment）

是某于某种工具或平台实现代码自动化的构建、测试和部署到线上环境以实现交付高质量的产品，持续部署在某种成都上代表了一个开发团队的更新迭代速率

## 1.6: 什么是持续交付（Continuous Delivery）

持续交付实在持续部署的基础上，将产品交付到线上环境，因此持续交付时产品价值的一种交付，是产品价值的一种盈利的实现。



![](/images/posts/04_Jenkins/00/4.png)



## 1.7：常见的部署方式

开发自己上传-最原始的方案

开发给运维手动上传-运维自己手动部署

运维使用脚本复制-半自动化

结合web界面一键部署-自动化

## 1.8：常见的持续集成开源工具

在公司的服务器安装某种程序，改程序用于按照特定格式和方式记录和保存公司多名开发人员不定期提交的源代码，且后期可以按照某种标记方式对用户提交的数据进行还原

### 1.8.1：SVN-集中式版本控制系统

2000年开始开发，目标就是替代CVS集中式管理，依赖于网络，一台服务器集中管理目前依然有部分公司在使用

### 1.8.2：Gitlib-分布式版本控制系统

linus在1991年创建了开源的Linux内核，从此linux便不断快速发展，不过Linux的壮大是离不开全世界的开发者的参与，这么多人在时间各地为Liunx编写代码，那linux内核的代码时如何管理的呢？事实是，在2002年以前，时间各地的志愿者把员源代码文件通过diff的方式发给linus，然后由linus本人通过手工方式合并代码！你也许会想，为什么linus不把linux代码放到版本控制系统里呢？不是有CVS和SNV，这些集中式的版本控制系统不但速冻慢，且必须联网才能使用，但是也有一些商用的版本控制系统，虽然比CVS、SNV好用，但那是付费的，和linux的开源精神不符，不过，到了2002年，linux系统以及发展了十来年了，代码库之大让linus很难继续通过手工方式管理了，社区的弟兄们也对这种方式表达了强烈的不满，于是linus选择了一个商业的版本控制系统BitKeeper,BitKpper的东家BitMover公司出于人道主义精神，授权linux社区免费使用这个版本控制系统，但是安定团结的大好局面在2005年就被打破，原因是linux社区牛人聚集，不免沾染了一些梁山好汉的江湖习气，开发Samba的Andrew视图破解BitKeeper的协议，被BitMover公司发现了，于是BitMover公司努了，要收回Linux社区的免费使用权，这时候启时linus可以向BitMover公司道个歉，保证以后严格管教弟兄们，但这是不可能的，而且实际情况是linus自己花了两周的时间自己用c写了一个分部署版本控制系统，这就是git，一个月之内，linux内核的源码已经由git管理了，大家可以体会一下，然后git迅速成为最流行的分部署版本控制系统，尤其是2008年，Github网站上线了，它为开源项目免费提供Git存储，无数开源项目开始迁移至GitHub,包括jQuery,PHP,Puby等





![](/images/posts/04_Jenkins/00/5.png)



## 1.9：版本控制系统分类

### 1.9.2：集中式版本控制系统

![](/images/posts/04_Jenkins/00/6.png)

任何的提交和回滚都依赖于连接服务器SVN服务器是单点



### 1.9.3：分布式版本控制系统

Git在每个用户都有一个完整的服务器，然后在有一个中英服务器，用户可以先将代码提交到本地，没有网络也可以提交到本地，然后在有网络的时候在提交到中英服务器，这样就大大方柏霓了开发者的代码提交和回滚，而相比CVS和SVN都是集中式的版本控制系统，工作的时候需要从中央服务器获取最新的代码，改完之后需要提交，如果是一个比较大的文件则需要足够快的网络才能快速提交完成，而使用分布式的版本控制系统，每个用户都是一个完整的版本库，即使没有中央服务器也可以提交代码或者回滚，最终再把改好的代码提交至中央服务器进行合并即可

# 二：Gltlab部署于使用：

https://about.gitlab.com/install/ #Gitlab 服务的安装文档

https://docs.gitlab.com/ce/install/requirements.html #安装环境要求

## 2.1：下载并部署gitlab:

### 2.1.1: Ubuntu 系统环境准备

#### 2.1.1.1: 配置ubuntu 远程连接：

```
root@gitlab:/apps# vim /etc/ssh/sshd_config 
PermitRootLogin yes
PasswordAuthentication yes
```

#### 2.1.1.2: 配置ubuntu网卡和主机名

```
root@gitlab:/apps# cat /etc/netplan/01-network-manager-all.yaml 
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
network:
  version: 2
  renderer: NetworkManager
  ethernets:
     ens33:
       dhcp4: no
       addresses: [192.168.48.166/24]
       gateway4: 192.168.48.2
       nameservers:
         addresses: [8.8.8.8,114.114.114.114]

root@gitlab:/apps# cat /etc/hostname
gitlab

```

#### 2.1.1.3: 配置ubuntu仓库

```
root@gitlab:/apps# cat /etc/apt/sources.list
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse

root@gitlab:apt update


root@gitlab:/var/lib/dpkg# apt-get install iproute2 ntpdate tcpdump nfs-common lrzsz tree openssl libssl-dev libpcre3 libpcre3-dev zlib1g-dev iotop unzip zip ipmitool telnet traceroute gcc openssh-server
```

### 2.1.2: gitlab 安装及使用

安装包下载地址： https://packages.gitlab.com/gitlab/gitlab-ce

rpm包国内下载地址：https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/

ubuntu国内下载地址：https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu/pool



#### 2.1.2.1: gitlab 配置使用：

```
root@gitlab:/apps# dpkg -i gitlab-ce_14.6.7-ce.0_amd64.deb
root@gitlab:/apps# vim /etc/gitlab/gitlab.rb
external_url 'http://192.168.48.166'

#可选邮件通知设置
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.qq.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "510587596@qq.com"
gitlab_rails['smtp_password'] = "xxxxx"
gitlab_rails['smtp_domain'] = "qq.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true

```

#### 2.1.3.2：初始化服务

执行配置并启动服务

```
root@gitlab:/apps# gitlab-ctl reconfigure #修改完配置文件要执行此操作

#gitlab相关的目录
/etc/gitlba #配置文件目录
/run/gitlab #运行pid目录
/opt/gitlab #安装目录
/var/opt/gitlab #数据目录
/var/log/gitlab #日志目录
```

#### 2.1.3.3 常用命令

gitlab-rails #用于启动控制台进行特殊操作，比如修改管理员密码，打开数据库控制台（gitlab-rails dbconsole）等

```

#gitlba-psql #数据库命令行
root@gitlab:~# gitlab-psql
psql (12.7)
Type "help" for help.

gitlabhq_production=# \db
         List of tablespaces
    Name    |    Owner    | Location 
------------+-------------+----------
 pg_default | gitlab-psql | 
 pg_global  | gitlab-psql | 
(2 rows)


#gitlab-rake #数据库备份恢复等数据操作
#gitlab-ctl #客户端命令行操作行
#gitlba-ctl stop #停止gitlab
#gitlba-ctl start #启动gitlab
#gitlba-ctl restart #重启gitlab
#gitlba-ctl status #查看组件运行状态gitlab
#gitlba-ctl tail nginx #查看某个组件的日志

```

#### 2.1.3.4：验证gitlab启动完成

![](/images/posts/04_Jenkins/00/7.png)



#### 2.1.3.5：验证端口及状态

80端口是在初始化gitlab的时候启动的，因此如果之前有程序占用导致初始化失败或无法访问

![](/images/posts/04_Jenkins/00/8.png)



#### 2.1.3.6：登录gitlab web界面：

http://ip/

登录web页面并设置密码最少8位

![](/images/posts/04_Jenkins/00/9.png)



#### 2.1.3.7：登录gitlab

登录，默认用户名位root

![](/images/posts/04_Jenkins/00/10.png)



#### 2.1.3.8: 默认首页

![](/images/posts/04_Jenkins/00/11.png)

#### 2.1.3.9：创建组

使用管理员root创建组，一个组里面可以有多个项目分支，可以将分开发添加到组里面进行设置权限，不同组的就是公司不同的开发项目或者服务模块，不同的组添加不同的开发即可实现对开发设置权限的管理



![](/images/posts/04_Jenkins/00/12.png)



使用管理员创建项目

![](/images/posts/04_Jenkins/00/13.png)

创建后的项目效果

![](/images/posts/04_Jenkins/00/14.png)



#### 2.1.3.10: 新建用户

![](/images/posts/04_Jenkins/00/15.png)



![](/images/posts/04_Jenkins/00/16.png)

#### 2.1.3.11 将用户添加到组

https://docs.gitlab.com/ee/user/permissions.html (更多权限)



![](/images/posts/04_Jenkins/00/17.png)



#### 2.1.3.12：创建一个测试页面

找到项目界面

![](/images/posts/04_Jenkins/00/18.png)





![](/images/posts/04_Jenkins/00/19.png)



添加一个页面:



![](/images/posts/04_Jenkins/00/20.png)



#### 2.1.3.13: git客户端测试clone项目

```
root@jenkinis:/apps# git clone http://192.168.48.166/test-service/test-service.git
Cloning into 'test-service'...
Username for 'http://192.168.48.166': test
Password for 'http://test@192.168.48.166': 
remote: Enumerating objects: 13, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 13 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (13/13), done.

root@jenkinis:/apps# cat test-service/index.html 
<h1>v1111111111</h1>

#编辑文件并测试提交
root@jenkinis:/apps/test-service# vim index.html
root@jenkinis:/apps/test-service# cat index.html 
<h1>v1111111111</h1>
<h1>v2222222222</h2>
root@jenkinis:/apps/test-service# git config --global user.name "test"
root@jenkinis:/apps/test-service# git config --global user.email "510587596@qq.com"
root@jenkinis:/apps/test-service# git add .
root@jenkinis:/apps/test-service# git commit -m "v1 version"
[main 0936a0b] v1 version
 1 file changed, 1 insertion(+)

```

![](/images/posts/04_Jenkins/00/21.png)



#### 2.1.3.14：git web端验证数据



![](/images/posts/04_Jenkins/00/22.png)



#### 2.1.3.15：gitlab使用

##### 2.1.3.15.1: 数据保存方式

SNV与CVS

每次提交的文件都单独保存，既按照文件的提交时间区分不同的版本，保存至不同的逻辑存储区域，后期恢复的时候直接基于之前版本恢复



![](/images/posts/04_Jenkins/00/23.png)



Gitlab:

Gitlab与SVN的数据保存方式不一样，gitlab虽然也会在内部对数据进行逻辑划分保存，但是后期提交的数据如果和之前提交过的数据没有变化，其就直接快照之前得文件，而不是在将文件重新上传一份在保存一遍，这样既节省了空间又加快了代码提交速度



![](/images/posts/04_Jenkins/00/24.png)



##### 2.1.3.15.2：常用git命令

使用git命令下载代码与提交代码等操作

![](/images/posts/04_Jenkins/00/25.png)

```
git config --global user.name "name" #设置全局用户名
git config --global user.email xxxx@xx.com #设置全局邮箱
git config --global --list #列出全局设置
git add index.html . #添加 指定文件、目录或当前目录下所有数据到暂存区
git comm it -m "v1" #提交到工作区
git status #查看工作区状态
git push 	#提交代码到服务器
git pull	#获取代码到本地
git log		#查看操作日志
git branch -avv  #查看所有分支
git branch -r #查看项目远程分支
git push origin -d master #删除远程分支
git reset --hard HEAD^^ #git 版本回滚，HEAD为当前本本，加一个^为上一个，^^为上上一个版本
git reflog #获取每次提交的ID，可以使用--hard根基提交的ID进行版本回退
git reset --hard 5ae4b06 #回退到指定的id的版本
git branch #查看当前所处的分支
git checkout -b devlop #创建并切换到一个新分支
git	checkout	devlop #切换分支
```

##### 2.1.3.15.3：git缓存区于工作区等概念

工作区：clone的代码或者开发自己编写的代码文件所在的目录，通常是代码所在的一个服务的目录名称

暂存区：用于存储在工作区种对代码进行修改后的文件所保存的地方，使用git add添加

本地仓库：用于提交存储在工作区和暂存区中改过的文件地方，使用git commit提交

远程仓库：多个开发共同协作提交代码的仓库，既gitlab服务器

![](/images/posts/04_Jenkins/00/26.png)



#### 2.1.3.16: gitlab数据备份恢复

##### 2.1.3.16.1：停止gitlab数据服务

```
root@gitlab:~# gitlab-ctl stop unicorn
root@gitlab:~# gitlab-ctl stop sidekiq
```

##### 2.1.3.16.2: 手动备份数据

```
root@gitlab:~# gitlab-rake gitlab:backup:create #在任意目录即可备份当前gitlab数据
root@gitlab:~# gitlab-ctl start #备份完成后启动gitlab
```

##### 2.1.3.16.3：查看要恢复的文件

```
/var/opt/gitlab/backups/	#数据备份目录，需要使用命令备份的
/var/opt/gitlab/nginx/conf/ #nginx配置文件
/etc/gitlab/gitlab.rb #gitlab配置文件
/etc/gitlab/gitlab-secrets.json	#key文件

root@gitlab:~# ll /var/opt/gitlab/backups/
1649226048_2022_04_06_14.6.7_gitlab_backup.tar
```

##### 2.1.3.16.4: 执行恢复

删除一些数据，测试能否恢复

```
root@gitlab:~# gitlab-ctl stop unicorn #停止写入
root@gitlab:~# gitlab-ctl stop sidekiq #恢复数据之前停止服务
root@gitlab:~# gitlab-rake gitlab:backup:restore BACKUP=备份文件名
```

![](/images/posts/04_Jenkins/00/27.png)





##### 2.1.3.16.5: 启动服务

```
root@gitlab:~# gitlab-ctl start sidekiq
ok: run: sidekiq: (pid 54382) 0s
root@gitlab:~# gitlab-ctl start unicorn
```

##### 2.1.3.16.6: Web界面更改语言

在右上角的账户下拉框选Preferences然后左侧Preferences设置项，然后语言选择中文

![](/images/posts/04_Jenkins/00/28.png)



![](/images/posts/04_Jenkins/00/29.png)

保存后刷新界面：

## 2.2：常见的代码部署方式:

### 2.2.1: 蓝绿部署:

蓝绿部署指的是不停老版本代码（不影响上一个版本访问），而是在另外一套环境部署新版本然后进行测试，测试通过后将用户流量切到新版本，其特点为业务无中断，升级风险相对较小

具体过程

```
1.当前版本业务正常访问（v1）
2.在另外一套环境部署新代码（v2），代码可能是增加了功能或者是修复某些bug
3.测试通过之后将用户请求流量切到新版本环境
4.观察一段时间，如有异常直接切换旧版本
5.下次升级，将旧版本升级到新版本（v3）
```



蓝绿部署适用的场景：

```
1.不停止老版本，额外部署一套新版本，等测试发现新版本OK后，删除老版本
2.蓝绿发布时一种用于升级与更新的发布策略，部署的最小维度时容器，而发布的最小维度是应用
3.蓝绿发布对于增量升级有比较好的支持，但是对于涉及数据表结构变更等等不可逆转的升级，并不完全适用蓝绿发布来实现，需要结合一些业务的逻辑以及数据迁移与回滚的策略才可以完全满足需求
```

![](/images/posts/04_Jenkins/00/30.png)





### 2.2.2: 金丝雀发布：

金丝雀发布也叫灰度发布，是指在黑与白之间，能够平滑过渡的一种发布方式，灰度发布是增量发布的一种类型，灰度发布是在原有版本可用的情况下，同时部署一个新版本应用作为“金丝雀”(小白鼠)，测试新版本的性能和表现，以保障整体系统稳定的情况下，尽早发现，调整问题

```
“金丝雀”的由来：17世纪，英国矿井工人发现，金丝雀对瓦斯这种气体十分敏感，空气中哪怕有极其微量的瓦斯，金丝雀也会停止歌唱，而当瓦斯含量超过一定限度时，随人鲁钝的人类毫无观察觉，金丝雀却早已毒发身亡，当时在采矿设备相对简陋的条件下，工人们每次下井都会带上一只金丝雀作为“瓦斯检测标志”，以便在危险状况下紧急撤离
```



金丝雀发布、灰度发布步骤组成：

```
1.准备好部署各个阶段的工作，包括：构建工作，测试脚本，配置文件和部署清单文件。
2.从负载均衡列表中移除掉“金丝雀”服务器
3.升级“金丝雀”应用（排掉原有流量并进行部署）
4.对应用进行自动化测试
5.将“金丝雀”服务器重新添加到负载均衡列表中（连通性和健康检查）
6.如果“金丝雀”在线使用测试成功，升级剩余的其他服务器（否正就回滚）
灰度发布可以保证整体系统的稳定，在初始灰度的时候就可以发现，调整问题，以保证其影响度
```



灰度发布/金丝雀部署适用的场景

```
1.不停止老版本，额外搞一套新版本，不同版本应用共存
2.灰度发布中，常常按照用户设置路由权重，例如90%的用户维持使用老版本，10%的用户尝鲜新版本
3.经常与A/B测试一起使用，用于测试选择多种方案
```



![](/images/posts/04_Jenkins/00/31.png)





### 2.2.3：滚动发布:

滚动发布，一般是取出一个或者多个服务器停止服务，执行更新，并重新将其投入使用。周而复始，直到集群中所有的实例都更新成新版本



### 2.2.4：A/B测试:

A/B测试也是同时运行两个APP环境，但是蓝绿部署完全是两码事，A/B测试是用来测试应用功能表现得方法，例如可用性，受欢迎程度，可见性等等，蓝绿部署的目的是安全稳定发布新版本应用，并在必要时回滚，既蓝绿部署时一套正式环境在线，而A/B测试是两套正式环境在线

# 三：部署web服务器环境:

## 3.1：java环境:

各web服务器准备tomcat运行环境：

```
[root@jenkins-web1 ]# useradd www -u 2000
[root@jenkins-web1 ]#mkdir /apps && cd /apps
[root@jenkins-web1 apps]# tar xvf jdk-8u191-linux-x64.tar.gz
[root@jenkins-web1 apps]# ln -sv /apps/jdk1.8.0_191/ /apps/jdk
[root@jenkins-web1 apps]# vim /etc/profile
export exportLANG="en_US.utf-8"
export JAVA_HOME=/apps/jdk
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/.dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$PATH:$JAVA_HOME/bin
[root@jenkins-web1 apps]# source /etc/profile
[root@jenkins-web1 apps]# java -version
java version "1.8.0_191"
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)


```

## 3.2: web部署：

部署web服务器并确认各web服务器访问正常:

```
创建web账户
[root@jenkins-web1 apps]# groupadd -g 2020 test && useradd -m -g test -u 2020 -s /bin/bash test
[root@jenkins-web1 apps]# mkdir /data/tomcat_appdir -p #保存web压缩包
[root@jenkins-web1 apps]# mkdir /data/tomcat_webdir #保存解压后的web目录
[root@jenkins-web1 apps]# mkdir /data/tomcat_webapps #tomcat app加载目录，在server.xml定义
[root@jenkins-web1 apps]# mkdir /data/tomcat_webdir/myapp #Java代码目录
[root@jenkins-web1 apps]# echo web1 app > /data/tomcat_webdir/myapp/index.html
[root@jenkins-web1 apps]# ln -sv /data/tomcat_webdir/myapp /data/tomcat_webapps/myapp
[root@jenkins-web1 apps]# ln -sv /apps/apache-tomcat-9.0.6 /apps/tomcat
```

## 3.3：配置tomcat配置文件：

```
 [root@jenkins-web1 conf]# vim server.xml
 151行 <Host name="localhost"  appBase="/data/tomcat_webapps/"   unpackWARs="false" #自动解压 autoDeploy="false" #自动部署>

```

## 3.4: 启动tomcat 验证：

```
 [root@jenkins-web1 conf]#chown test.test /apps/ /data/ -R 
[root@jenkins-web1 conf]# su - test
[test@jenkins-web1 bin]$ ./catalina.sh start

```

![](/images/posts/04_Jenkins/00/32.png)

## 3.5：部署keepalived：

```
[root@haproxy2 ~]# yum -y install gcc openssl-devel libnl3-devel net-snmp-devel libnfnetlink-devel curl readline-devel systemd-devel make pcre-devel
[root@haproxy2 ~]# tar xvf keepalived-2.0.4.tar.gz
[root@haproxy2 ~]# cd keepalived-2.0.4 && ./configure --prefix=/usr/local/keepalived --disable-fwmark
[root@haproxy2 keepalived-2.0.4]# make && make install
[root@haproxy2 keepalived-2.0.4]# mkdir /etc/keepalived
[root@haproxy2 keepalived]# cat keepalived.conf 
! Configuration File for keepalived
global_defs {
router_id 1
}
vrrp_instance VI_I {
	state MASTER
	interface ens33
	virtual_router_id 51
	priority 100
	advert_int 1
	nopreempt
	authentication {
	auth_type PASS
	auth_pass 1111
}
	virtual_ipaddress {
	192.168.48.88
}	
}

[root@haproxy2 keepalived]# systemctl start keepalived && systemctl enable keepalived
```

## 3.6：部署lua环境:

由于centos自带的lua版本比较低并不符合HAProxy要求的lua最低版本（5.3）的要求，因此需要编译安装较新版本的lua环境，然后才能编译安装HAProxy

```
[root@haproxy2 ~]# tar -xf lua-5.3.5.tar.gz
[root@haproxy2 lua-5.3.5]# make linux test
[root@haproxy2 lua-5.3.5]# lua -v
Lua 5.1.4  Copyright (C) 1994-2008 Lua.org, PUC-Rio
[root@haproxy2 lua-5.3.5]# pwd
/usr/local/src/lua-5.3.5

```

## 3.7：部署HAProxy:

```
[root@haproxy2 ~]# tar xf haproxy-2.2.11.tar.gz
[root@haproxy2 ~]# cd haproxy-2.2.11
[root@haproxy2 haproxy-2.2.11]# make ARCH=X86_64 TARGET=linux-glibc USE_PCRE=1 USE_OPENSSL=1 USE_ZLIB=1 USE_SYSTEMD=1 USE_CPU_AFFINITY=1 USE_LUA=1 LUA_INC=/usr/local/src/lua-5.3.5/src/ LUA_LIB=/usr/local/src/lua-5.3.5/src/ PREFIX=/usr/local/haproxy
[root@haproxy2 haproxy-2.2.11~]#make install PREFIX=/usr/local/haproxy
[root@haproxy2 haproxy-2.2.11]# cp haproxy /usr/sbin/
[root@haproxy2 ~]# groupadd -r haproxy
[root@haproxy2 ~]# useradd -g haproxy -M -s /sbin/nologin haproxy
```

### 3.7.1: HAProxy启动脚本:

```
[root@haproxy2 haproxy-2.2.11]# cat /usr/lib/systemd/system/haproxy.service
[Unit]
Description=HAProxy Load Balancer
After=syslog.target network.target

[Service]
ExecStartPre=/usr/sbin/haproxy -f /etc/haproxy/haproxy.cfg  -c -q
ExecStart=/usr/sbin/haproxy -Ws -f /etc/haproxy/haproxy.cfg -f /etc/haproxy/conf -p /var/lib/haproxy/haproxy.pid
ExecReload=/bin/kill -USR2 $MAINPID

[Install]
WantedBy=multi-user.target

[root@haproxy2 haproxy-2.2.11]# mkdir /etc/haproxy
[root@haproxy2 haproxy-2.2.11]# mkdir /var/lib/haproxy
```

### 3.7.2: 配置文件：

```
[root@haproxy2 haproxy]# cat haproxy.cfg 
global
    log         127.0.0.1 local2
    chroot      /usr/local/haproxy
    pidfile     /var/lib/haproxy/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon

   stats socket /var/lib/haproxy/haproxy.sock mode 600 level admin
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000

listen admin_stats 
    bind 0.0.0.0:9000
    stats enable
    mode http
    log global
    stats uri  /haproxy-status
  #  stats realm Haproxy\ Statistics
    stats auth  admin:admin
    #stats hide-version   #隐藏统计页面上HAProxy的版本信息
   # stats admin if TRUE
   # stats refresh 30s
   
   
  #开启路由转发
[root@haproxy2 ~]# vim /etc/sysctl.conf 

net.ipv4.ip_forward = 1
net.ipv4.ip_nonlocal_bind = 1
[root@haproxy2 ~]# sysctl -p
[root@haproxy2 ~]# systemctl start haproxy && systemctl enable haproxy
```

### 3.7.3: 添加后端配置:

```
[root@haproxy2 haproxy]# vim haproxy.cfg
listen web_port
     bind 192.168.48.88:80
     mode http
     log global
     server 192.168.48.141 192.168.48.141:8080 check inter 3000 fall 2 rise 5
     server 192.168.48.143 192.168.48.143:8080 check inter 3000 fall 2 rise 5
     
#重启服务，浏览器验证
http://192.168.48.88:9000/haproxy/haproxy-status
```



![](/images/posts/04_Jenkins/00/33.png)



### 3.7.4: 验证haproxy代理web服务器：



![](/images/posts/04_Jenkins/00/34.png)



![](/images/posts/04_Jenkins/00/35.png)

# 四：Jenkins部署于基础配置：

https://jenkins.io/zh/

## 4.1: 配置java环境并部署jenkins:

### 4.1.1: 使用war包部署（可选）

```
root@jenkins-2:/usr/local/src# apt install openjdk-8-jdk
root@jenkins-2：  wget https://mirrors.tuna.tsinghua.edu.cn/jenkins/war-stable/2.332.2/jenkins.war

#启动
root@jenkins-2:/usr/local/src# java -jar jenkins-2.332.2.war &
```

### 4.1.2: 浏览器验证

![](/images/posts/04_Jenkins/00/36.png)





### 4.1.3:java环境配置：

```
root@jenkinis:~# tar xvf jdk-8u191-linux-x64.tar.gz
root@jenkinis:/usr/local# ln -sv /root/jdk1.8.0_191/ /usr/local/jdk
root@jenkinis:/usr/local# ln -sv /usr/local/jdk/bin/java /usr/bin/

root@jenkinis:/usr/local# vim /etc/profile
export JAVA_HOME=/usr/local/jdk
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
export CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar
root@jenkinis:/usr/local# source /etc/profile
root@jenkinis:/usr/local# java -version
java version "1.8.0_191"
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)


```

### 4.1.4: deb下载jenkins:

```
root@jenkins-2:/usr/local/src# wget https://mirrors.tuna.tsinghua.edu.cn/jenkins/war-stable/2.332.2/jenkins.war
root@jenkinis:~# apt install daemon
root@jenkins:/usr/local/src# dpkg -i jenkins_2.332.2_all.deb

root@jenkins:~# netstat -tlnp | grep 8080
tcp6       0      0 :::8080                 :::*                    LISTEN      7930/java           


```



![](/images/posts/04_Jenkins/00/37.png)





### 4.1.5: 创建jenkins管理员：

![](/images/posts/04_Jenkins/00/38.png)



### 4.1.6：配置jenkins URL:

![](/images/posts/04_Jenkins/00/39.png)



### 4.1.7: 配置完成并登录jenkins:



![](/images/posts/04_Jenkins/00/40.png)



### 4.1.8: 登录jenkins界面：



![](/images/posts/04_Jenkins/00/41.png)



### 4.1.8: jenkins插件管理及安装：

#### 4.1.8.1：插件安装目录：

插件下载地址：http://updates.jenkins-ci.org/download/plugins

![](/images/posts/04_Jenkins/00/42.png)



#### 4.1.8.2: 安装插件

搜索需要gitlab得插件并安装

git和Blue Ocean

![](/images/posts/04_Jenkins/00/43.png)



![](/images/posts/04_Jenkins/00/44.png)





![](/images/posts/04_Jenkins/00/45.png)





![](/images/posts/04_Jenkins/00/46.png)



![](/images/posts/04_Jenkins/00/47.png)



### 4.1.9：配置Jenkins权限管理：

基于角色的权限管理，先创建角色和用户，给角色授权，然后把用户管理到角色

#### 4.1.9.1：安装插件：

Role-based: 基于角色的认证策略

![](/images/posts/04_Jenkins/00/48.png)



#### 4.1.9.2：创建新用户

jenkins-系统管理-管理用户



![](/images/posts/04_Jenkins/00/49.png)



#### 4.1.9.3：更改认证方式：

Jenkins-系统管理-全局安全配置

默认创建的用户登录后可以做任何操作，取决于默认的认证授权方式

改成角色认证

![](/images/posts/04_Jenkins/00/50.png)



#### 4.1.9.4：创建角色：

jenkins-系统管理-Manage and Assign Roles(管理和分配角色)

![](/images/posts/04_Jenkins/00/51.png)

#### 4.1.9.5： 添加角色：

填写角色名称并点击add:

![](/images/posts/04_Jenkins/00/52.png)



#### 4.1.9.6:  对角色分配权限

授予Global roles只读权限及item的操作权限：

单用户授权

![](/images/posts/04_Jenkins/00/53.png)



#### 4.1.9.7： 叫用户关联到角色：

单用户：

![](/images/posts/04_Jenkins/00/54.png)



#### 4.1.9.8：测试用户登录：

![](/images/posts/04_Jenkins/00/55.png)



![](/images/posts/04_Jenkins/00/56.png)



## 4.2：基于ssh key 拉取代码：

### 4.2.1：获取公钥信息

```
root@jenkins:~# cat /root/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCxFKLx+h7K23InQwyTecR+T2xBzb4kMjgI8G1NXcS0zHoYccCDiu7junFy0rKeAxhMQi6VmPoYN9REqN6e+ccoQKcp03J+BaJ1zNGnIL1VqNpZL+FRFdfC6HGWdIS18TELYFx02NtpLAZZvAxiBli2fvam5gGejccOO171w8dracG5SoOEjE3KAexgN1C0f2AF2XeBjFzB0xPQllMJImlh0En1CJdf1tyBq3c46aNUbRXNAmUWfzEtGBUxFKP56LaFT+ujgcy9Xnb1xOYpSeO1H9y7f3llQVdj/RYhCgZfCZanTdXhD6Dkt2mOUVIqEmTqwnc+kI+8defSiY1KXNOj root@jenkins

```

### 4.2.2：添加ssh key:



![](/images/posts/04_Jenkins/00/57.png)

### 4.2.3: 创建ssh key

ssh key只用于免认证获取代码



![](/images/posts/04_Jenkins/00/58.png)





### 4.2.4：测试ssh key:

测试可以不使用用户名密码后直接获取代码

![](/images/posts/04_Jenkins/00/59.png)



## 4.3：配置jenkins到gitlab非交互拉取代码

### 4.3.1：配置git项目地址和用户：

![](/images/posts/04_Jenkins/00/60.png)





![](/images/posts/04_Jenkins/00/61.png)



![](/images/posts/04_Jenkins/00/62.png)



![](/images/posts/04_Jenkins/00/63.png)



选择实际场景的分支

![](/images/posts/04_Jenkins/00/64.png)



### 4.3.2：测试构建项目：

#### 4.3.2.1：点击立即构建

#### 4.3.2.2：验证构建结果：

![](/images/posts/04_Jenkins/00/65.png)

#### 4.3.2.3：服务器验证数据：

![](/images/posts/04_Jenkins/00/66.png)



#### 4.3.2.4：将代码部署至后端服务器：



构建->执行shell脚本，脚本内容



![](/images/posts/04_Jenkins/00/67.png)



![](/images/posts/04_Jenkins/00/68.png)

## 4.4：构建触发器（钩子）：

构建触发器（webhook）,有的人称为钩子，实际上是一个HTTP回调，启用于在开发人向gitlab提交代码后能够触发jenkins自动执行代码构建操作

以下为新建一个开发分支，只有在开发人员向开发（develop）分支提交代码的时候才会触发构建，而向主分支提交的代码不会自动构建，需要运维人员手动部署代码到生产环境。







### 4.4.1：gitlab新建develop分支：



![](/images/posts/04_Jenkins/00/69.png)



### 4.4.2：gitlab定义分支名称并创建:

![](/images/posts/04_Jenkins/00/70.png)



### 4.4.3: jenkins安装插件：

早期版本需要安装插件

#系统管理-管理插件-可选插件-Gitlab Hook和Gitlab Authentication

注意事项：

https://www.jenkins.io/security/advisory/2018-05-09/#SECURITY-263

* 在jenkins系统管理-全局配置，认证改为登录用户可以做任何事情
* 取消跨站请求伪造保护的勾选项
* Gitlab Hook Plugin 以纯文本形式存储和显示GitLab API 令牌



### 4.4.4：jenkins 修改登录认证方式：

系统管理-全局安全设置---



![](/images/posts/04_Jenkins/00/71.png)



### 4.4.5: jenkins新建develop job:

![](/images/posts/04_Jenkins/00/72.png)



### 4.4.6：jenkins构建shell命令：

构建命令为简单的测试命令，比如输出当前的账户信息：



![](/images/posts/04_Jenkins/00/73.png)



### 4.4.7：jenkins配置构建触发器：

生产token认证：

```
root@jenkins:~# openssl rand -hex 12
7506dcfcf8baf073a9e8eff1
```

![](/images/posts/04_Jenkins/00/74.png)

### 4.4.8：jenkins验证分支job配置文件：

```
root@jenkins:~# vim /var/lib/jenkins/jobs/python-app1/config.xml
```

![](/images/posts/04_Jenkins/00/75.png)



### 4.4.9：curl命令测试出发并验证远程触发构建:

* 使用浏览器直接访问URL地址
* 使用curl命令访问URL



```
root@jenkins:~# curl http://192.168.48.163:8080/view/all/job/python-app1/build?token=7506dcfcf8baf073a9e8eff1

```



### 4.4.10：jenkins验证job是否自动构建



![](/images/posts/04_Jenkins/00/76.png)



## 4.5: jenkins分布式：

在众多Job的场景下，单台jenkins master同时执行代码clone、编译、打包及构建其性能可能会出现瓶颈从而会影响代码部署效率，影响jenkins官方提供了jenkins分布式构建，将众多job分散运行到不同的jenkins slave节点，大幅提高并行job的处理能力

### 4.5.1：配置slave节点java环境：

slave服务器创建工作目录，如果slave需要执行编译Job，则需要配置java环境并且按照git.svn.maven等于master相同的基础运行环境，另外也要创建与master相同的数据目录，因为脚本中调用的路径只有相对一master的一个路径，此路径在master于哥node节点必须保持一致

```
root@k8s-node1:~# mkdir -p /var/lib/jenkins  #创建数据目录
root@jenkins-slave:~# vim /etc/profile
export JAVA_HOME=/usr/local/jdk
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
export CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar

```

### 4.5.2：添加slave节点：

jenkins-系统管理-节点管理-新建节点：

添加slave节点



![](/images/posts/04_Jenkins/00/77.png)

部分jenkins-slave信息

![](/images/posts/04_Jenkins/00/78.png)



启动方式选择ssh-添加jenkins

![](/images/posts/04_Jenkins/00/79.png)



### 4.5.3：添加slave认证凭据:

![](/images/posts/04_Jenkins/00/80.png)





选择不验证证书

![](/images/posts/04_Jenkins/00/81.png)

### 4.5.4: jenkins slave创建日志



![](/images/posts/04_Jenkins/00/82.png)



### 4.5.5：如果slave 没有java环境则报错如下：



![](/images/posts/04_Jenkins/00/83.png)



### 4.5.6：验证slave web状态：

正常状态



![](/images/posts/04_Jenkins/00/84.png)



### 4.5.7：验证slave 进程状态：



![](/images/posts/04_Jenkins/00/85.png)



## 4.6：pipline

官方接受：https://jenkins.io/2.0 #jenkins 2.x 官方介绍

https://jenkins.io/zh/doc/book/pipeline #官方pipline示例

pipline是帮助jnekins实现CI到CD转变的重要角色，是运行在jenkins 2.x版本的核心插件，简单来说Pipline就是一套运行于jenkins上的工作流程框架，将原本独立运行于单个或者多个节点的任务连接起来，实现单个任务难以完成的复杂发布流程，从而实现单个任务很难实现的复杂流程编排和任务可视化，pipeline的实现方式是一套Groovy DSL，任何发布流程都是可以表述一段Groovy脚本



### 4.6.1：pipline 语法：

Stage: 阶段，一个Pipline可以划分若干个stage，每个stage都是一个操作步骤，比如clone代码，代码编译，代码测试和代码部署，阶段是一个逻辑分组，可以跨多个node执行

Node：节点，每个node都是一个jenkins节点，可以是jenkins master也可以是jenkins agent，node是执行step的具体服务器

step：步骤，step是jenkins pipline是基本的操作单元，从在服务器创建目录到构建容器镜像，由各类jenkins插件提供实现，一个stage中可以由多个step，例如：sh "make"



### 4.6.2：pipline优势：

可持续性：jenkins的重启或者中断后不影响已经执行的pipline job

支持暂停：pipline可以选择停止并等待人工输入或批准后在继续执行

可扩展：通过groovy的编程更容器的扩展插件

并行执行：通过groovy脚本可以实现strp,stage间的并行执行，和更复杂的相互依赖关系

### 4.6.3：pipline job测试

#### 4.6.3.1：创建Pipline job:



![](/images/posts/04_Jenkins/00/86.png)



#### 4.6.3.2: 测试简单pipline job运行：



![](/images/posts/04_Jenkins/00/87.png)

#### 4.6.3.3：执行Pipline job:



![](/images/posts/04_Jenkins/00/88.png)



#### 4.6.3.4: 自动拉取代码的pipline脚本：

点击流水线语法跳转至生成脚本URL



![](/images/posts/04_Jenkins/00/89.png)



生成流水线脚本：

![](/images/posts/04_Jenkins/00/90.png)



#### 4.6.3.5：更改pipline job：

```
node {

        stage('clone 代码') {
            git branch: 'main', credentialsId: '9c3534b6-96b3-4e74-9bda-0e9cf447e66b', url: 'git@192.168.48.166:port/port.git'
	}
        stage('代码构建') {
		echo "代码 构建"
	}
 
        stage('代码测试') {
		echo "代码测试"
	}
         stage('代码部署') {
		echo "代码部署"
	}
}
```



#### 4.6.3.6：执行jenkins job:



![](/images/posts/04_Jenkins/00/91.png)



#### 4.6.3.7: 验证git clone 日志：



![](/images/posts/04_Jenkins/00/92.png)



#### 4.6.3.8： jenkins服务器验证clone代码数据



![](/images/posts/04_Jenkins/00/93.png)



#### 4.6.3.9： pipline中执行shell命令打包代码

```
node {

        stage('clone 代码') {
		echo "clone"
            git branch: 'main', credentialsId: '9c3534b6-96b3-4e74-9bda-0e9cf447e66b', url: 'git@192.168.48.166:port/port.git'
	}
        stage('代码打包') {
	sh "cd /var/lib/jenkins/workspace/pipline-test1 &&  tar zcvf myapp1.tar.gz ./index.html "
	}

        stage('代码测试') {
		echo "代码测试"
	}
         stage('代码部署') {
		echo "代码部署"
	}
}
```

#### 4.6.3.10: pipline部署示例：

```
node {

        stage('clone 代码') {
		echo "clone"
            git branch: 'main', credentialsId: '9c3534b6-96b3-4e74-9bda-0e9cf447e66b', url: 'git@192.168.48.166:port/port.git'
	}
        stage('代码打包') {
	sh "cd /var/lib/jenkins/workspace/pipline-test1 &&  tar zcvf myapp1.tar.gz ./index.html "
	}
     stage('停止tomcat') {
		echo "停止tomcat"
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	}
        stage('代码替换') {
		echo "代码替换"
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.143:/data/tomcat_webdir/myapp'
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.141:/data/tomcat_webdir/myapp'
	sh 'ssh test@192.168.48.143 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	sh 'ssh test@192.168.48.141 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	}
         stage('启动tomcat') {
		echo "启动tomcat"
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat start"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat start "'
	}
}
```

#### 4.6.3.11：执行并验证pipline job:

在gitlba提交代码，执行pipline job并验证代码是否最终部署到了web服务器



![](/images/posts/04_Jenkins/00/94.png)



#### 4.6.3.12：指定node节点运行job：

jenkins node1

node节点需要安装git命令

```
root@jenkins-slave:~# apt-get install git
```

node节点需要打通于web server免密钥登录

```
root@jenkins-slave:~# ssh-keygen
root@jenkins-slave:~# ssh-copy-id test@192.168.48.141
root@jenkins-slave:~# ssh-copy-id test@192.168.48.143

```

jenkins pipline代码:

```
node ("jenkins-slave1") {

        stage('clone 代码') {
		echo "clone"
            git branch: 'main', credentialsId: '9c3534b6-96b3-4e74-9bda-0e9cf447e66b', url: 'git@192.168.48.166:port/port.git'
	}
        stage('代码打包') {
	sh "cd /var/lib/jenkins/workspace/pipline-test1 &&  tar zcvf myapp1.tar.gz ./index.html "
	}
     stage('停止tomcat') {
		echo "停止tomcat"
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	}
        stage('代码替换') {
		echo "代码替换"
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.143:/data/tomcat_webdir/myapp'
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.141:/data/tomcat_webdir/myapp'
	sh 'ssh test@192.168.48.143 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	sh 'ssh test@192.168.48.141 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	}
         stage('启动tomcat') {
		echo "启动tomcat"
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat start"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat start "'
	}
}
```





#### 4.6.3.13：验证slave执行构建

![](/images/posts/04_Jenkins/00/95.png)



#### 4.6.1.15: 声明式pipline

```
pipline-声明式：
pipeline{
    //agent any  //全局必须带有agent,表明此pipeline执行节点
    agent { label 'jenkins-slave1' }
    stages{
        stage('clone 代码') {
        //#agent { label 'master' }  //具体执行的步骤节点，非必须
		echo "clone"
         steps {
         git branch: 'main', credentialsId: '9c3534b6-96b3-4e74-9bda-0e9cf447e66b', url: 'git@192.168.48.166:port/port.git'
	}
	}
        stage('代码打包') {
	steps {
	sh "cd /var/lib/jenkins/workspace/pipline-test1 &&  tar zcvf myapp1.tar.gz ./index.html "
	}
		}
     stage('停止tomcat') {
		steps{
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	}
	}
        stage('代码替换') {
	steps{	
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.143:/data/tomcat_webdir/myapp'
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.141:/data/tomcat_webdir/myapp'
	sh 'ssh test@192.168.48.143 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	sh 'ssh test@192.168.48.141 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	}
}
         stage('启动tomcat') {
		steps{
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat start"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat start "'
			}
		}
	}
}
```



#### 4.6.1.14: 使用jenkinsfile

定义jenkinsfile 提交代码，pipline也随着提交到gitlab中

 

```
root@jenkins-slave:~# git clone -b main git@192.168.48.166:port/port.git
root@jenkins-slave:~# cd port
root@jenkins-slave:~/port# cat Jenkinsfile 
node ("jenkins-slave1") {

        stage('clone 代码') {
		echo "clone"
            git branch: 'main', credentialsId: '9c3534b6-96b3-4e74-9bda-0e9cf447e66b', url: 'git@192.168.48.166:port/port.git'
	}
        stage('代码打包') {
	sh "cd /var/lib/jenkins/workspace/pipline-test1 &&  tar zcvf myapp1.tar.gz ./index.html "
	}
     stage('停止tomcat') {
		echo "停止tomcat"
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat stop && rm -rf /data/tomcat_webapps/myapp/index.html"'
	}
        stage('代码替换') {
		echo "代码替换"
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.143:/data/tomcat_webdir/myapp'
	sh 'scp /var/lib/jenkins/workspace/pipline-test1/myapp1.tar.gz test@192.168.48.141:/data/tomcat_webdir/myapp'
	sh 'ssh test@192.168.48.143 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	sh 'ssh test@192.168.48.141 "tar zxvf /data/tomcat_webdir/myapp/myapp1.tar.gz -C /data/tomcat_webapps/myapp"'
	}
         stage('启动tomcat') {
		echo "启动tomcat"
	sh 'ssh test@192.168.48.143 "/etc/init.d/tomcat start"'
	sh 'ssh test@192.168.48.141 "/etc/init.d/tomcat start "'
	}
}
root@jenkins-slave:~/port# git commit -m "v5 add add Jenkinsfile"
root@jenkins-slave:~/port# git push
```

![](/images/posts/04_Jenkins/00/96.png)



![](/images/posts/04_Jenkins/00/97.png)



## 4.7：视图

视图可以用于归纳job进行分组显示，比如将一个业务的视图放在一个视图显示，安装完成build pipeline插件之后将会有一个+号用于创建视图

![](/images/posts/04_Jenkins/00/98.png)

### 4.7.1: 安装build pipeline插件：



![](/images/posts/04_Jenkins/00/99.png)



插件安装完成



![](/images/posts/04_Jenkins/00/100.png)



### 4.7.2：创建新的视图：

![](/images/posts/04_Jenkins/00/101.png)



### 4.7.3：创建pipline视图：



![](/images/posts/04_Jenkins/00/102.png)



![](/images/posts/04_Jenkins/00/103.png)



### 4.7.4：web显示界面



![](/images/posts/04_Jenkins/00/104.png)



### 4.7.5：列表视图



列表视图使用场景比较多，用于将一个业务的job保存至一个列表视图进行分类管理，及不同业务的job放在不同的列表视图中

列表视图是对众多job推荐使用的分类功能

#### 4.7.5.1：定义视图名称

![](/images/posts/04_Jenkins/00/105.png)



#### 4.7.5.2：选择任务：



![](/images/posts/04_Jenkins/00/105-1.png)

#### 4.7.5.3：最终状态：



![](/images/posts/04_Jenkins/00/106.png)



# 五：代码质量测试：

官方网站：http://www.sonarqube.org/

SonarQube是一个用于代码质量管理的开放平台，通过插件机制，SonarQube可以集成不同的测试工具，代码分析工具，以及持续集成工具，例如Hudson/jenkins登，下载地址：https:///sonarqube.org/downloads/



七个维度检测代码质量

```
复杂度分布：代码复杂度过高将难以理解
重复代码：程序中包含大量复制、粘贴的代码而导致代码臃肿，sonar可以展示源码中重复严重的地方
单元测试统计：统计并展示单元测试覆盖率，开发或测试可以清楚测试得到覆盖情况
代码规则检查：检查代码是否符合规范
注释率：若代码注释过少，特别是人员变动后，其他人接手比较难接手，若过多，又不利于阅读潜在的Bug：检测在的bug
结构于设计：找出循环，展示包于包、类于类之间的依赖、检查程序之间的耦合度
```





![](/images/posts/04_Jenkins/00/107.png)



## 5.1：PostgreSQL及SonarQube7.9x/8.9x部署：

### 5.1.1：安装JDK 11：

```
root@jenkins-slave:~# apt-get install openjdk-11-jdk
root@k8s-node2:~# java --version
openjdk 11.0.14.1 2022-02-08
OpenJDK Runtime Environment (build 11.0.14.1+1-Ubuntu-0ubuntu1.18.04)
OpenJDK 64-Bit Server VM (build 11.0.14.1+1-Ubuntu-0ubuntu1.18.04, mixed mode, sharing)

```

### 5.1.2：安装PostgreSQL服务器：

```
root@k8s-node2:~# apt-get install postgresql
```

#### 5.1.2.1：配置postgresql:

切换到postgres操作，postgresql安装后会自动创建postgres用户且没有密码

```
root@k8s-node2:~# su - postgres
postgres@k8s-node2:~$ 
登录postgres数据库
postgres@k8s-node2:~$ psql -U postgres
psql (10.19 (Ubuntu 10.19-0ubuntu0.18.04.1))
Type "help" for help.

创建数据库并授权普通用户访问：
postgres=# create database sonar; #创建数据库
CREATE DATABASE
postgres=# create user sonar with encrypted password '123456'; #创建用户
CREATE ROLE
postgres=# grant all privileges on database sonar to sonar; #授权用户
GRANT
postgres=# alter database sonar owner to sonar; #执行变更
ALTER DATABASE
postgres=# \q
修改监听地址
postgres@k8s-node2:~$ vim /etc/postgresql/10/main/postgresql.conf
listen_addresses = '*'

开启远程访问
postgres@k8s-node2:~$ vim /etc/postgresql/10/main/pg_hba.conf

local   all     postgres          peer

local   all      all              peer

host    all      all             0.0.0.0/0            md5

host    all      all             ::1/128              md5


root@k8s-node2:~# systemctl restart postgresql
```

#### 5.1.2.2: 系统及内核参数：

```

root@k8s-node2:~# vim /etc/sysctl.conf
vm.max_map_count=262144
fs.file-max=65536
root@sonar:~# vim /etc/security/limits.conf 
sonarqube           -       nofile          65536
sonarqube           -       nproc           4096

root@sonar:~# sysctl -p
```



#### 5.1.2.3：部署8.9.x sonarQube:

```
root@sonar:/apps# unzip sonarqube-8.9.0.43852.zip
root@sonar:/apps# ln -sv /apps/sonarqube-8.9.0.43852 /apps/sonarqube
'/apps/sonarqube' -> '/apps/sonarqube-8.9.0.43852'
root@sonar:/apps# useradd -r -m -s /bin/bash sonarqube

root@sonar:/apps# chown sonarqube.sonarqube /apps/ -R
root@sonar:/apps# su - sonarqube
sonarqube@sonar:~$ cd /apps/sonarqube
sonarqube@sonar:/apps/sonarqube$ vim conf/sonar.properties 
sonar.jdbc.username=sonar
sonar.jdbc.password=123456
sonar.jdbc.url=jdbc:postgresql://localhost/sonar
sonarqube@sonar:/apps/sonarqube$ grep -v "^#" conf/sonar.properties | grep -v "^$"
sonar.jdbc.username=sonar
sonar.jdbc.password=123456
sonar.jdbc.url=jdbc:postgresql://localhost/sonar
sonarqube@sonar:/apps/sonarqube$ ./bin/linux-x86-64/sonar.sh start
Starting SonarQube...
Started SonarQube.
```



![](/images/posts/04_Jenkins/00/108.png)



#### 5.1.2.4: 验证sonarqube



![](/images/posts/04_Jenkins/00/109.png)



#### 5.1.2.5: 访问sonaruqbe web界面：

http://192.168.48.165:9000

登录账户名和密码都是admin



![](/images/posts/04_Jenkins/00/110.png)

#### 5.1.2.6: 安装中文插件



![](/images/posts/04_Jenkins/00/111.png)



```
root@sonar:/apps/sonarqube$ ./bin/linux-x86-64/sonar.sh restart
Gracefully stopping SonarQube...
Starting SonarQube...
Started SonarQube.

```

## 5.2: jenkins 服务器部署扫描器sonar-scanner:



官方文档：https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/

### 5.2.1: 部署sonar-scanner:

sonarqube通过调用扫描器sonnar-scanner进行代码质量分析，既扫描器的具体工作是扫描代码

```
jenkins服务器：
root@jenkins:/apps/sonarqube# cd /usr/local/src/  
root@jenkins:/usr/local/src# unzip sonar-scanner-cli-4.6.2.2472-linux.zip
root@jenkins:/usr/local/src# ln -sv /usr/local/src/sonar-scanner-4.6.2.2472-linux /usr/local/sonarscanner
'/usr/local/sonarscanner' -> '/usr/local/src/sonar-scanner-4.6.2.2472-linux'
root@jenkins:/usr/local/src# cd /usr/local/sonarscanner
root@jenkins:/usr/local/sonar-scanner# vim conf/sonar-scanner.properties 
#----- Default SonarQube server
sonar.host.url=http://192.168.48.165:9000

#----- Default source code encoding
sonar.sourceEncoding=UTF-8


```



### 5.2.3：准备测试代码

8.9.x关闭强制认证：



![](/images/posts/04_Jenkins/00/112.png)



```
root@jenkins:/usr/local/src# unzip sonar-examples-master.zip
root@jenkins:/usr/local/src# cd sonar-examples-master/
root@jenkins:/usr/local/src/sonar-examples-master# cd projects/languages/php/php-sonar-runner

oot@jenkins:/usr/local/src/sonar-examples-master/projects/languages/php/php-sonar-runner# cat sonar-project.properties  #以下为默认生成的配置
# Required metadata
sonar.projectKey=org.sonarqube:php-simple-sq-scanner #自定义项目key 
sonar.projectName=PHP :: Simple Project :: SonarQube Scanner #项目名称，会显示在web
sonar.projectVersion=1.0 #项目版本

# Comma-separated paths to directories with sources (required)
sonar.sources=src #源代码目录

# Language
sonar.language=php  #代码语言类型

# Encoding of the source files
sonar.sourceEncoding=UTF-8	#编码格式

```



### 5.2.4：在源代码目录执行扫描：

#手动在当前项目代码目录执行扫描，以下是扫描过程的提示信息，扫描的配置文件sonar-project.propertie每个项目都要有

```
root@jenkins:/usr/local/src/sonar-examples-master/projects/languages/php/php-sonar-runner# /usr/local/sonar-scanner/bin/sonar-scanner

```



![](/images/posts/04_Jenkins/00/113.png)



### 5.2.5：sonarqube web界面验证扫描结果



![](/images/posts/04_Jenkins/00/114.png)



### 5.2.6：自定义python代码扫描

```
# cp sonar-project.properties /tmp/
root@jenkins:/opt# mkdir m43-app1
root@jenkins:/opt# cd m43-app1/
root@jenkins:/opt/m43-app1# vim test.py
#!/usr/bin/env python
#coding:utf-8

from jenkinsapi.jenkins import Jenkins
conn = Jenkins('http://192.168.48.163:8080',username='jenkins',password='jenkins')
conn.build_job('megedu-app3')
root@jenkins:/opt/m43-app1# cp /tmp/sonar-project.properties .
root@jenkins:/opt/m43-app1# vim sonar-project.properties
# Required metadata
sonar.projectKey=app1
sonar.projectName=app1-name
sonar.projectVersion=1.0

# Comma-separated paths to directories with sources (required)
sonar.sources=./

# Language
sonar.language=py

# Encoding of the source files
sonar.sourceEncoding=UTF-8
root@jenkins:/opt/m43-app1# /usr/local/sonarscanner/bin/sonar-scanner

```



![](/images/posts/04_Jenkins/00/115.png)



## 5.3：jenkins 执行代码扫描

### 5.3.1：jenkins安装SonarQube 插件：

安装插件SonarQube Scanner, 然后配置SonarQube server，系统管理-插件管理

![](/images/posts/04_Jenkins/00/116.png)



### 5.3.2：让jenkins添加Sonarscanner扫描器:

添加扫描器：

jenkins-系统管理-系统设置-sonarQube servers:



![](/images/posts/04_Jenkins/00/117.png)



### 5.3.2: 让jenkins添加sonarscanner扫描器

添加扫描器：

jenkins-系统管理-全局工具配置：

### 5.3.2.1：手动指定绝对路径：

![](/images/posts/04_Jenkins/00/118.png)

### 5.3.3：配置扫描

选择自己的项目（sonar-scannet-app1）-构建-execute sonarqube scnaner，讲配置文件的内容修改如下格式填写完成点保存：



```
# Required metadata
sonar.projectKey=app3
sonar.projectName=app3-name
sonar.projectVersion=3.0

# Comma-separated paths to directories with sources (required)
sonar.sources=./

# Language
sonar.language=py

# Encoding of the source files
sonar.sourceEncoding=UTF-8
```



### 5.3.4：配置项目进行扫描：



![](/images/posts/04_Jenkins/00/119.png)





![](/images/posts/04_Jenkins/00/120.png)



![](/images/posts/04_Jenkins/00/121.png)





```
root@jenkins:/data/magedu# cat scripts.sh 
#!/bin/bash
BRANCH=$1
ssh test@192.168.48.143 "/etc/init.d/tomcat stop"
ssh test@192.168.48.141 "/etc/init.d/tomcat stop"

scp /var/lib/jenkins/workspace/port/index.html test@192.168.48.143:/data/tomcat_webapps/myapp/index.html
scp /var/lib/jenkins/workspace/port/index.html test@192.168.48.141:/data/tomcat_webapps/myapp/index.html

ssh test@192.168.48.143 "/etc/init.d/tomcat start"
ssh test@192.168.48.141 "/etc/init.d/tomcat start"

```





### 5.3.5：构建项目并测试sonar-scanner是否生效：

点击项目的立即构建，下图是执行程序的信息：



![](/images/posts/04_Jenkins/00/122.png)





![](/images/posts/04_Jenkins/00/123.png)





![](/images/posts/04_Jenkins/00/124.png)



![](/images/posts/04_Jenkins/00/125.png)



![](/images/posts/04_Jenkins/00/126.png)



# 六：实战案例



