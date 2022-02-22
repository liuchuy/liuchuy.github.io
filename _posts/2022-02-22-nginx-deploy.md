---
layout: post
title: nginx离线安装
date: 2022-02-22
tags: nginx
---

# 一：离线安装nginx

## 1.1：环境

CentOS Linux release 7.7.1908 (Core)

nginx-1.8.0

## 1.1.1：准备离线包

```
#创建目录用于放包
[postgres@nginx ~]$ mkdir down
[postgres@nginx ~]$ cd down/
[postgres@nginx down]$ 
[postgres@nginx down]$ ll
total 49092
-rw-rw-r-- 1 postgres postgres  6230552 Nov 12 18:10 cpp-4.8.5-44.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres 16963328 Nov 26 15:24 gcc-4.8.5-44.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres  7531804 Nov 26 15:25 gcc-c++-4.8.5-44.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres  3815880 Nov 12 18:11 glibc-2.17-317.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres  1127364 Nov 26 15:54 glibc-devel-2.17-317.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres   706340 Nov 12 18:11 glibc-headers-2.17-317.el7.x86_64.rpm
-rw-r--r-- 1 postgres postgres  9449344 Nov 12 18:38 kernel-headers-3.10.0-1160.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres   105308 Nov 26 15:53 libgcc-4.8.5-44.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres   162384 Nov 26 15:54 libgomp-4.8.5-44.el7.x86_64.rpm
-rw-rw-r-- 1 postgres postgres  1039530 Nov 12 16:48 nginx-1.18.0.tar.gz
-rw-rw-r-- 1 postgres postgres  9834044 Nov 12 19:13 openssl-1.1.1l.tar.gz
-rw-rw-r-- 1 postgres postgres  2096552 Dec  2 16:48 pcre-8.45.tar.gz
-rw-rw-r-- 1 postgres postgres   583782 Jan 13 17:57 zlib-1.2.3.7.tar.gz
-rw-rw-r-- 1 postgres postgres    51128 Nov 12 18:48 zlib-devel-1.2.7-18.el7.x86_64.rpm

```

## 1.2 安装

```
[postgres@nginx down]$ sudo rpm -ivh gcc-c++-4.8.5-44.el7.x86_64.rpm --nodeps --force
[postgres@nginx down]$ sudo rpm -ivh gcc-4.8.5-44.el7.x86_64.rpm --nodeps --force
[postgres@nginx down]$ sudo rpm -ivh gcc-4.8.5-44.el7.x86_64.rpm --nodeps --force 
[postgres@nginx down]$ sudo rpm -ivh glibc-2.17-317.el7.x86_64.rpm --nodeps --force 
[postgres@nginx down]$ sudo rpm -ivh cpp-4.8.5-44.el7.x86_64.rpm  --nodeps --force
[postgres@nginx down]$ sudo rpm -ivh glibc-devel-2.17-317.el7.x86_64.rpm  --nodeps --force
[postgres@nginx down]$ sudo rpm -ivh glibc-headers-2.17-317.el7.x86_64.rpm  --nodeps --force
[postgres@nginx down]$ sudo rpm -ivh kernel-headers-3.10.0-1160.el7.x86_64.rpm  --nodeps --force
[postgres@nginx down]$ sudo rpm -ivh libgcc-4.8.5-44.el7.x86_64.rpm --nodeps --force
[postgres@nginx down]$ sudo rpm -ivh libgomp-4.8.5-44.el7.x86_64.rpm --nodeps --force

```

### 1.2.3 安装prce

```
[postgres@nginx down]$ tar xf pcre-8.45.tar.gz 
[postgres@nginx down]$ cd pcre-8.45
[postgres@nginx pcre-8.45]$ ./configure
[postgres@nginx pcre-8.45]$ make
#此步骤需要root
[root@nginx pcre-8.45]# make install
```



### 1.2.4 安装openssl

```
[postgres@nginx down]$ tar -xf openssl-1.1.1l.tar.gz
[postgres@nginx down]$ cd openssl-1.1.1l
[postgres@nginx openssl-1.1.1l]$ ./config
[postgres@nginx openssl-1.1.1l]$ make
#此步骤需要root
[root@nginx openssl-1.1.1l]# make install
```

### 1.2.5：安装zlib

```
[postgres@nginx down]$ tar xf zlib-1.2.3.7.tar.gz
[postgres@nginx down]$ cd zlib-1.2.3.7
[postgres@nginx zlib-1.2.3.7]$ ./configure
[postgres@nginx zlib-1.2.3.7]$ make
#此步骤需要root
[root@nginx zlib-1.2.3.7]# make install

```

### 1.2.6：安装nginx

```
[postgres@nginx down]$ tar xf nginx-1.18.0.tar.gz 
[postgres@nginx down]$ cd nginx-1.18.0
[postgres@nginx nginx-1.18.0]$ ./configure --prefix=/home/postgres/nginx --with-pcre=/home/postgres/down/pcre-8.45 --with-zlib=/home/postgres/down/zlib-1.2.3.7 --with-openssl=/home/postgres/down/openssl-1.1.1l --with-http_stub_status_module --with-http_ssl_module
[postgres@nginx down]$ make
[postgres@nginx down]$ make install
```



### 1.3：启动nginx

```
[postgres@nginx nginx-1.18.0]$ cd /home/postgres/nginx/
[postgres@nginx nginx]$ cd sbin/
[postgres@nginx sbin]$ sudo ./nginx 
[postgres@nginx sbin]$ ps -ef | grep nginx
root      58878      1  0 01:07 ?        00:00:00 nginx: master process ./nginx
nobody    58879  58878  0 01:07 ?        00:00:00 nginx: worker process
postgres  60213  29424  0 01:07 pts/0    00:00:00 grep --color=auto nginx
[postgres@nginx sbin]$ ss -tlnp | grep 80            
LISTEN     0      128          *:80                       *:*                  
```