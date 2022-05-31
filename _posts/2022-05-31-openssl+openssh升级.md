# 升级操作系统OpenssSSH极其OpenSSL

# 1:需求说明

OpenSSL 发布安全通告，修复了 OpenSSL 产品中多个安全漏洞。OpenSSL 是一个开放源代码的软件库包，应用 程序可以使用这个包来进行安全通信，避免窃听，同时确认另 一端连接者的身份，该软件库包被广泛应用在互联网的网页服 务器上

有时，可能由于审计需要或修复漏洞的需要，我们可能会遇到这么一个需求：升级操作系统的 openssl

## 1.2：查看本机版本环境

```
[root@s1 ~]# openssl version
OpenSSL 1.0.1e-fips 11 Feb 2013
[root@s1 ~]# ssh -V
OpenSSH_5.3p1, OpenSSL 1.0.1e-fips 11 Feb 2013
```

### 1.2.1 升级说明

就因为 ssh -V 命令执行后显示出来的 openssl 版本较低，就说要 (跨版本) 升级操作系统的 openssl。这里面存在逻辑问题

先，我给个结论：跨版本升级操作系统的 openssl 是不可能的。

你可以在系统中尝试执行下 yum remove openssl 命令，你就可以看到，非常非常多的软件是依赖于 openssl 软件。openssl 是一个非常基础的软件。假设你升级了操作系统的 openssl，比如说，编译安装一个新版本的 openssl 覆盖掉操作系统自带的 openssl。这就会导致那些依赖于 openssl 的软件的 openssl 相关的功能变得不可用，比如说，某软件原本是支持 https 的，现在可能就不支持了。除非你能将系统中所有依赖于 openssl 的软件基于新版本的 openssl 全都编译一遍，而这通常是不可能的。

其次，我们并不需要升级操作系统的 openssl。

我想，很多人在碰到这个需求时，可能都有去百度过，然后百度出了一大堆文章，然后了解到也需要重新编译安装 openssh。但是，几乎我看到的所有文章，都采用的是错误的做法，他们的做法虽然各不相同，但大概也可以概括为：先强制卸载操作系统的 openssl、openssh，再编译安装一个新版本的 openssl (和其它可能的附带软件)，然后各种莫名其妙的操作，最后编译安装一个新版本的 openssh。这种做法无法达到目的吗？那倒也不是。但可能代价就是，所有其它依赖于操作系统的 openssl 的软件的 openssl 相关的功能都不能用了。

 因此来说，正确的做法是什么？这就是本文所要介绍的。

ssh 命令是 openssh 软件的一部分。我们无法升级操作系统的 openssl，但是我们可以另外编译一个 openssl，放到单独的应用目录中，与操作系统的 openssl 互不干涉。再基于新编译出来的 openssl，将新的 openssh 软件编译出来。 而操作系统的 openssh 是可以被替换的。如果你尝试执行 yum remove openssh* 命令就可以看到，没有其它软件依赖于 openssh。此外，openssh 软件提供了 sshd 服务。所以，我们只要还要配置并搭建好 sshd 服务，就可以替代操作系统自带的 openssh 了。



OpenSSH 升级思路

要升级 openssh，我们需要先搞懂 openssl 是怎么安装的

我使用的操作系统是 centos 6 的，我以安装 openssh 8.9.p1 为例。

从 openssh 官网下载 openssh 8.9.p1 源码包，查看里面的 INSTALL 文件，里面有对它的依赖关系做说明。

## 1.3：环境准备

### 1.3.1：安装zlib

Zlib 用于提供压缩和解压缩功能。操作系统已经自带了 zlib，版本也符合要求。实际上，openssl 和 openssh 都依赖于 zlib。执行下面的命令，安装 zlib 开发包

```
[root@s1 apps]# yum -y install zlib-devel
```

### 1.3.2: 安装RAM

PAM (Pluggable Authentication Modules，可插拔认证模块) 用于提供安全控制。操作系统也已经自带了 PAM，版本也是可以的。执行下面的命令，安装 PAM 开发包：

```
[root@s1 apps]# yum -y install pam-devel
```

### 1.3.3：安装tcp_warappers(可选)

tcp_wrappers 是一种安全工具，通常，我们在 /etc/hosts.allow 或 /etc/hosts.deny 文件中配置的过滤规则就是使用的 tcp_wrappers 的功能了。openssh 在编译时的确是可以选择支持 tcp_wrappers 的，但我不知道为什么它的安装要求里面没有体现。操作系统自带的 tcp_wrappers 的版本是可以的。执行下面的命令，安装 tcp_wrappers 开发包：

```
[root@s1 apps]# yum -y install tcp_wrappers-devel
```



### 1.3.4：编译安装OpenSSL

下载链接：

https://www.openssl.org/source/

```
[root@s1 ~]# mkdir /apps
 [root@s1 apps]# wget https://www.openssl.org/source/openssl-1.1.1o.tar.gz --no-check-certificate
 #备份ssl
[root@s1 openssl-1.1.1o]# mv /usr/bin/openssl{,.bak}
[root@s1 openssl-1.1.1o]#mv /usr/include/openssl{,.bak}
#编译安装ssl
[root@s1 apps]# tar -xf openssl-1.1.1o.tar.gz 
[root@s1 apps]# cd openssl-1.1.1
[root@s1 openssl-1.1.1o]# ./config 
[root@s1 openssl-1.1.1o]# make && make install
#配置ssl
[root@s1 openssl-1.1.1o]# ln -s /usr/local/bin/openssl /usr/bin/openssl
[root@s1 openssl-1.1.1o]# ln -s /usr/local/include/openssl/ /usr/include/openssl
[root@s1 openssl-1.1.1o]# echo "/usr/local/lib64" >> /etc/ld.so.conf
[root@s1 openssl-1.1.1o]# /sbin/ldconfig

#版本查看
[root@s1 openssl-1.1.1o]# openssl version
OpenSSL 1.1.1o  3 May 2022


```

### 1.3.5：编译安装openssh

下载链接：

https://mirrors.aliyun.com/pub/OpenBSD/OpenSSH/portable/

```
[root@s1 apps]# wget https://mirrors.aliyun.com/pub/OpenBSD/OpenSSH/portable/openssh-8.9p1.tar.gz
#备份ssh
[root@s1 openssl-1.1.1o]# mv /etc/ssh{,.bak}
[root@s1 openssh-8.8p1]# mkdir /usr/local/openssh
#编译安装
[root@s1 apps]# tar -xf openssh-8.8p1.tar.gz
[root@s1 apps]# cd openssh-8.8p
[root@s1 openssh-8.9p1]# ./configure --prefix=/usr/local/openssh --sysconfdir=/etc/ssh --with-openssl-includes=/usr/local/include --with-ssl-dir=/usr/local/lib64 --with-zlib --with-md5-passwords --with-pam
[root@s1 openssh-8.8p1]# make && make install

#配置ssh
[root@s1 openssh-8.9p1]#echo "UseDNS no" >> /etc/ssh/sshd_config
[root@s1 openssh-8.9p1]#echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config
[root@s1 openssh-8.9p1]#echo 'PubkeyAuthentication yes' >> /etc/ssh/sshd_config
[root@s1 openssh-8.9p1]#echo 'PasswordAuthentication yes' >> /etc/ssh/sshd_config
[root@s1 openssh-8.9p1]# mv /usr/sbin/sshd{,.bak}
[root@s1 openssh-8.9p1]# mv /usr/bin/ssh{,.bak}
[root@s1 openssh-8.9p1]# mv /usr/bin/ssh-keygen{,.bak}
[root@s1 openssh-8.9p1]# ln -s /usr/local/openssh/bin/ssh /usr/bin/ssh
[root@s1 openssh-8.9p1]# ln -s /usr/local/openssh/bin/ssh-keygen /usr/bin/ssh-keygen
[root@s1 openssh-8.9p1]# ln -s /usr/local/openssh/sbin/sshd /usr/sbin/sshd
[root@s1 openssh-8.9p1]# mv /usr/lib/systemd/system/sshd.service /usr/lib/systemd/system/sshd.service.bak
[root@s1 openssh-8.9p1]# cp contrib/redhat/sshd.init /etc/init.d/sshd
[root@s1 openssh-8.9p1]# cp contrib/redhat/sshd.pam /etc/pam.d/sshd.pam

[root@s1 openssh-8.8p1]# chmod +x /etc/init.d/sshd
[root@s1 openssh-8.8p1]# chkconfig --add sshd
[root@s1 openssh-8.8p1]# chkconfig sshd on
[root@s1 openssh-8.8p1]# service  sshd restart
```

## 2：版本验证

## 2.1：openssl版本验证



```
[root@s1 openssh-8.8p1]# openssl version
OpenSSL 1.1.1o  3 May 2022
```

## 2.2：OpenSSh版本验证

```
[root@s1 openssh-8.8p1]# ssh -V
OpenSSH_8.8p1, OpenSSL 1.1.1o  3 May 2022
```

