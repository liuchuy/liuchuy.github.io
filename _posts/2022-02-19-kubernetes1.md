---
layout: post
title: ansible初始化kubernetes集群
date: 2022-02-19
tags: kubernetes
---
# 一：基础集群环境搭建

k8s基础集群环境主要运行kubernetes管理端服务以及node节点上的服务部署及使用。

Kubernetes设计架构：

```
https://www.kubernetes.org.cn/kubernetes%E8%AE%BE%E8%AE%A1%E6%9E%B6%E6%9E%84
```

CNCF云原生容器生态系统概要：

```
http://dockone.io/article/3006
```

## 1.1：k8s高可用集群环境规划信息：

安装实际需求，进行规划于部署相应的单master或者多master的高可用k8s运行环境

### 1.1.1：单master

见kubeadm安装k8s

![](/images/posts/02_k8s\00\1.png)


### 1.1.3: 服务器统计：

| 类型            | 服务器IP地址       | 备注                               |
| --------------- | ------------------ | ---------------------------------- |
| K8S Master(2台) | 192.168.48.158/159 | k8s控制端，通过一个VIP做主备高可用 |
| Ansible(2台)    | 192.168.48.158/159 | k8s集群部署服务器                  |
| Etcd（2台）     | 192.168.48.158/159 | 保存k8s集群数据的服务器            |
| Node节点（2台） | 192.168.48.160/161 | 真正运行容器的服务器，高可用环境   |
| Hproxy(2台)     | 192.168.48.158/159 | 高可用etcd代理服务器               |
| Harbor（1台)    | 192.168.48.141     | 高可用镜像服务器                   |



## 1.2：主机名设置：



| 类型        | 服务器IP       | 主机名      | VIP           |
| ----------- | -------------- | ----------- | ------------- |
| K8S Master1 | 192.168.48.158 | k8s-master1 | 192.168.48.88 |
| K8S Master2 | 192.168.48.159 | k8s-master2 | 192.168.48.88 |
| Habor1      | 192.158.48.141 | k8s-harbor1 |               |
| etcd节点1   | 192.168.48.158 | k8s-etcd1   |               |
| etcd节点2   | 192.168.48.159 | k8s-etcd2   |               |
| Haproxy1    | 192.168.48.158 | k8s-ha1     |               |
| Haproxy2    | 192.168.48.159 | k8s-ha2     |               |
| Node节点1   | 192.168.48.160 | k8s-node1   |               |
| Node节点2   | 192.168.48.161 | k8s-node2   |               |

## 1.3: 软件清单：

见当前目录下的kubernetes软件清单

API端口：

```
端口：192.168.48.88：6443 #需要配置在负载均衡上实现反向代理，dashboard的端口为8443
操作系统：Ubuntu server 1804
k8s版本：1.13.5
calico: 3.4.4
```

## 1.4:基础环境准备

http://releases.ubuntu.com/

系统主机名配置、IP配置、系统参数优化，以及依赖的负载均衡和Harbor部署

### 1.4.1：系统配置：

主机名登系统配置



### 1.4.2：高可用负载均衡

k8s高可用反向代理

http://blogs.studylinux.net/?p=4579



#### 1.4.2.1：keepalived



```
vrrp_instance VI_1 {
	state MASTER
	interface ens33
	virtual_router_id 1
	priority 100
	advert_int 1
	authentication {
		auth_type PASS
		auth_pass 123
}
	virtual_ipaddress {
		192.168.48.99 dev ens33 label ens33:1
}	

}

```

#### 14.2.2：haproxy

```
listen k8s-api_nodes_6443
        bind 192.168.48.99:6443
        mode tcp
        server 192.168.48.166 192.168.48.166:6443 check inter 2000 fall 3 rise 5
        server 192.168.48.163 192.168.48.163:6443 check inter 2000 fall 3 rise 5


BACKUO没有监听需要修改参数
root@k8s-master2:cat /etc/sysctl.conf
root@k8s-master2:net.ipv4.ip_nonlocal_bind = 1
root@k8s-master2:sysctl -p
root@k8s-master2:systemctl daemon-reload
root@k8s-master2:systemctl restart haproxy
```



## 1.6：ansible部署：

### 1.6.1：基础环境准备：

```
root@k8s-master1:~# apt-get install git python3-pip -y
root@k8s-master1:~# pip3 install ansible -i https://mirrors.aliyun.com/pypi/simple
root@k8s-master1:~# ansible --version


#生成密钥
root@k8s-master1:~# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:LWaZ9kyDcjELyVI2aMqA3+dGlKq0pOgLf7fTpKoIuLA root@k8s-master1
The key's randomart image is:
+---[RSA 2048]----+
|.    .+.         |
|o   o+oo         |
| + +.o+ o        |
|  * o.o. O       |
|.+ o +. S +      |
|+ o   o*.= .     |
|*    . +  o      |
|+*  . + .        |
|E.+o.o.o         |
+----[SHA256]-----+

#设置免密登录
#修改ssh配置文件需要root远程登录权限
vim /etc/ssh/sshd_config
找到PermitRootLogin without-password 
修改为PermitRootLogin yes
root@k8s-master1:~# systemctl restart ssh
#安装sshpass命令用于同步公钥到各k8s服务器
root@k8s-master1:~# apt-get install sshpass 
root@k8s-master1:~# cat scp-key.sh
IP="192.168.48.166
192.168.48.163
192.168.48.164
192.168.48.165
192.168.48.167
"
for node in ${IP};do
	sshpass -p 111111 ssh-copy-id ${node} -o StrictHostKeyChecking=no
	if [ $? -eq 0 ];then
		echo "${node} 密钥copy完成"
	else
		echo "${node} 密钥copy失败"
	fi
done
root@k8s-master1:~# bash scp-key.sh

```

## 1.7: 部署节点下载部署项目及组件：

实验master1做为部署节点

```
root@k8s-master1:~# export release=3.1.1
root@k8s-master1:~# wget https://github.com/easzlab/kubeasz/releases/download/${release}/ezdown
root@k8s-master1:~# chmod a+x ezdown 
root@k8s-master1:~# vim ezdown
DOCKER_VER=19.03.15
KUBEASZ_VER=3.1.1
K8S_BIN_VER=v1.22.2
root@k8s-master1:~# ./ezdown -D

root@k8s-master1:~# ll /etc/kubeasz/down/
total 1211692
drwxr-xr-x  2 root root      4096 2月   9 22:04 ./
drwxrwxr-x 11 root root      4096 2月   9 21:55 ../
-rw-------  1 root root 399721984 2月   9 21:58 calico_v3.19.2.tar
-rw-------  1 root root  47692288 2月   9 21:59 coredns_1.8.4.tar
-rw-------  1 root root 223074304 2月   9 22:01 dashboard_v2.3.1.tar
-rw-r--r--  1 root root  62436240 2月   2  2021 docker-19.03.15.tgz
-rw-------  1 root root  58150912 2月   9 22:02 flannel_v0.13.0-amd64.tar
-rw-------  1 root root 124833792 2月   9 22:00 k8s-dns-node-cache_1.17.0.tar
-rw-------  1 root root 179037696 2月   9 22:04 kubeasz_3.1.1.tar
-rw-------  1 root root  34566656 2月   9 22:02 metrics-scraper_v1.0.6.tar
-rw-------  1 root root  64775168 2月   9 22:03 metrics-server_v0.5.0.tar
-rw-------  1 root root  45063680 2月   9 22:03 nfs-provisioner_v4.0.1.tar
-rw-------  1 root root    692736 2月   9 22:03 pause_3.5.tar
-rw-------  1 root root    692736 2月   9 22:03 pause.tar
```

### 1.7.1: 生成ansible hosts文件：

```
root@k8s-master1:/etc/kubeasz# cd /etc/kubeasz/
root@k8s-master1:/etc/kubeasz# ./ezctl new k8s-01
2022-02-09 22:05:33 DEBUG generate custom cluster files in /etc/kubeasz/clusters/k8s-01
2022-02-09 22:05:33 DEBUG set version of common plugins
2022-02-09 22:05:33 DEBUG cluster k8s-01: files successfully created.
2022-02-09 22:05:33 INFO next steps 1: to config '/etc/kubeasz/clusters/k8s-01/hosts'
2022-02-09 22:05:33 INFO next steps 2: to config '/etc/kubeasz/clusters/k8s-01/config.yml'

```

#### 1.7.1.1: 编辑ansible hosts文件：

指定etcd节点、master节点、node节点、VIP、网络组件类型、service IP与Pod IP范围等配置信息

```
root@k8s-master1:/etc/kubeasz# cat /etc/kubeasz/clusters/k8s-01/hosts 
# 'etcd' cluster should have odd member(s) (1,3,5,...)
[etcd]
192.168.48.163
192.168.48.166

# master node(s)
[kube_master]
192.168.48.166
192.168.48.163

# work node(s)
[kube_node]
192.168.48.164
192.168.48.165

# [optional] harbor server, a private docker registry
# 'NEW_INSTALL': 'true' to install a harbor server; 'false' to integrate with existed one
[harbor]
#192.168.1.8 NEW_INSTALL=false

# [optional] loadbalance for accessing k8s from outside
[ex_lb]
192.168.48.163 LB_ROLE=backup EX_APISERVER_VIP=192.168.48.99 EX_APISERVER_PORT=6443
192.168.48.166 LB_ROLE=master EX_APISERVER_VIP=192.168.48.99 EX_APISERVER_PORT=6443

# [optional] ntp server for the cluster
[chrony]
#192.168.1.1

[all:vars]
# --------- Main Variables ---------------
# Secure port for apiservers
SECURE_PORT="6443"

# Cluster container-runtime supported: docker, containerd
CONTAINER_RUNTIME="docker"

# Network plugins supported: calico, flannel, kube-router, cilium, kube-ovn
CLUSTER_NETWORK="calico"

# Service proxy mode of kube-proxy: 'iptables' or 'ipvs'
PROXY_MODE="ipvs"

# K8S Service CIDR, not overlap with node(host) networking
SERVICE_CIDR="10.100.0.0/16"

# Cluster CIDR (Pod CIDR), not overlap with node(host) networking
CLUSTER_CIDR="10.200.0.0/16"

# NodePort Range
NODE_PORT_RANGE="30000-65535"

# Cluster DNS Domain
CLUSTER_DNS_DOMAIN="magedu.local"

# -------- Additional Variables (don't change the default value right now) ---
# Binaries Directory
bin_dir="/usr/bin"

# Deploy Directory (kubeasz workspace)
base_dir="/etc/kubeasz"

# Directory for a specific cluster
cluster_dir="{{ base_dir }}/clusters/k8s-01"

# CA and other components cert/key Directory
ca_dir="/etc/kubernetes/ssl"

```

#### 1.7.1.2：编辑config.yml文件：

```
root@k8s-master1:/etc/kubeasz# cat /etc/kubeasz/clusters/k8s-01/config.yml 
############################
# prepare
############################
# 可选离线安装系统软件包 (offline|online)
INSTALL_SOURCE: "online"

# 可选进行系统安全加固 github.com/dev-sec/ansible-collection-hardening
OS_HARDEN: false

# 设置时间源服务器【重要：集群内机器时间必须同步】
ntp_servers:
  - "ntp1.aliyun.com"
  - "time1.cloud.tencent.com"
  - "0.cn.pool.ntp.org"

# 设置允许内部时间同步的网络段，比如"10.0.0.0/8"，默认全部允许
local_network: "0.0.0.0/0"


############################
# role:deploy
############################
# default: ca will expire in 100 years
# default: certs issued by the ca will expire in 50 years
CA_EXPIRY: "876000h"
CERT_EXPIRY: "438000h"

# kubeconfig 配置参数
CLUSTER_NAME: "cluster1"
CONTEXT_NAME: "context-{{ CLUSTER_NAME }}"


############################
# role:etcd
############################
# 设置不同的wal目录，可以避免磁盘io竞争，提高性能
ETCD_DATA_DIR: "/var/lib/etcd"
ETCD_WAL_DIR: ""


############################
# role:runtime [containerd,docker]
############################
# ------------------------------------------- containerd
# [.]启用容器仓库镜像
ENABLE_MIRROR_REGISTRY: true

# [containerd]基础容器镜像
SANDBOX_IMAGE: "easzlab/pause-amd64:3.5"

# [containerd]容器持久化存储目录
CONTAINERD_STORAGE_DIR: "/var/lib/containerd"

# ------------------------------------------- docker
# [docker]容器存储目录
DOCKER_STORAGE_DIR: "/var/lib/docker"

# [docker]开启Restful API
ENABLE_REMOTE_API: false

# [docker]信任的HTTP仓库
INSECURE_REG: '["127.0.0.1/8"]'


############################
# role:kube-master
############################
# k8s 集群 master 节点证书配置，可以添加多个ip和域名（比如增加公网ip和域名）
MASTER_CERT_HOSTS:
  - "10.1.1.1"
  - "k8s.test.io"
  #- "www.test.com"

# node 节点上 pod 网段掩码长度（决定每个节点最多能分配的pod ip地址）
# 如果flannel 使用 --kube-subnet-mgr 参数，那么它将读取该设置为每个节点分配pod网段
# https://github.com/coreos/flannel/issues/847
NODE_CIDR_LEN: 24


############################
# role:kube-node
############################
# Kubelet 根目录
KUBELET_ROOT_DIR: "/var/lib/kubelet"

# node节点最大pod 数
MAX_PODS: 110

# 配置为kube组件（kubelet,kube-proxy,dockerd等）预留的资源量
# 数值设置详见templates/kubelet-config.yaml.j2
KUBE_RESERVED_ENABLED: "no"

# k8s 官方不建议草率开启 system-reserved, 除非你基于长期监控，了解系统的资源占用状况；
# 并且随着系统运行时间，需要适当增加资源预留，数值设置详见templates/kubelet-config.yaml.j2
# 系统预留设置基于 4c/8g 虚机，最小化安装系统服务，如果使用高性能物理机可以适当增加预留
# 另外，集群安装时候apiserver等资源占用会短时较大，建议至少预留1g内存
SYS_RESERVED_ENABLED: "no"

# haproxy balance mode
BALANCE_ALG: "roundrobin"


############################
# role:network [flannel,calico,cilium,kube-ovn,kube-router]
############################
# ------------------------------------------- flannel
# [flannel]设置flannel 后端"host-gw","vxlan"等
FLANNEL_BACKEND: "vxlan"
DIRECT_ROUTING: false

# [flannel] flanneld_image: "quay.io/coreos/flannel:v0.10.0-amd64"
flannelVer: "v0.13.0-amd64"
flanneld_image: "easzlab/flannel:{{ flannelVer }}"

# [flannel]离线镜像tar包
flannel_offline: "flannel_{{ flannelVer }}.tar"

# ------------------------------------------- calico
# [calico]设置 CALICO_IPV4POOL_IPIP=“off”,可以提高网络性能，条件限制详见 docs/setup/calico.md
CALICO_IPV4POOL_IPIP: "Always"

# [calico]设置 calico-node使用的host IP，bgp邻居通过该地址建立，可手工指定也可以自动发现
IP_AUTODETECTION_METHOD: "can-reach={{ groups['kube_master'][0] }}"

# [calico]设置calico 网络 backend: brid, vxlan, none
CALICO_NETWORKING_BACKEND: "brid"

# [calico]更新支持calico 版本: [v3.3.x] [v3.4.x] [v3.8.x] [v3.15.x]
calico_ver: "v3.15.3"

# [calico]calico 主版本
calico_ver_main: "{{ calico_ver.split('.')[0] }}.{{ calico_ver.split('.')[1] }}"

# [calico]离线镜像tar包
calico_offline: "calico_{{ calico_ver }}.tar"

# ------------------------------------------- cilium
# [cilium]CILIUM_ETCD_OPERATOR 创建的 etcd 集群节点数 1,3,5,7...
ETCD_CLUSTER_SIZE: 1

# [cilium]镜像版本
cilium_ver: "v1.4.1"

# [cilium]离线镜像tar包
cilium_offline: "cilium_{{ cilium_ver }}.tar"

# ------------------------------------------- kube-ovn
# [kube-ovn]选择 OVN DB and OVN Control Plane 节点，默认为第一个master节点
OVN_DB_NODE: "{{ groups['kube_master'][0] }}"

# [kube-ovn]离线镜像tar包
kube_ovn_ver: "v1.5.3"
kube_ovn_offline: "kube_ovn_{{ kube_ovn_ver }}.tar"

# ------------------------------------------- kube-router
# [kube-router]公有云上存在限制，一般需要始终开启 ipinip；自有环境可以设置为 "subnet"
OVERLAY_TYPE: "full"

# [kube-router]NetworkPolicy 支持开关
FIREWALL_ENABLE: "true"

# [kube-router]kube-router 镜像版本
kube_router_ver: "v0.3.1"
busybox_ver: "1.28.4"

# [kube-router]kube-router 离线镜像tar包
kuberouter_offline: "kube-router_{{ kube_router_ver }}.tar"
busybox_offline: "busybox_{{ busybox_ver }}.tar"


############################
# role:cluster-addon
############################
# coredns 自动安装
dns_install: "no"
corednsVer: "1.8.4"
ENABLE_LOCAL_DNS_CACHE: false
dnsNodeCacheVer: "1.17.0"
# 设置 local dns cache 地址
LOCAL_DNS_CACHE: "169.254.20.10"

# metric server 自动安装
metricsserver_install: "no"
metricsVer: "v0.5.0"

# dashboard 自动安装
dashboard_install: "no"
dashboardVer: "v2.3.1"
dashboardMetricsScraperVer: "v1.0.6"

# ingress 自动安装
ingress_install: "no"
ingress_backend: "traefik"
traefik_chart_ver: "9.12.3"

# prometheus 自动安装
prom_install: "no"
prom_namespace: "monitor"
prom_chart_ver: "12.10.6"

# nfs-provisioner 自动安装
nfs_provisioner_install: "no"
nfs_provisioner_namespace: "kube-system"
nfs_provisioner_ver: "v4.0.1"
nfs_storage_class: "managed-nfs-storage"
nfs_server: "192.168.1.10"
nfs_path: "/data/nfs"

############################
# role:harbor
############################
# harbor version，完整版本号
HARBOR_VER: "v2.1.3"
HARBOR_DOMAIN: "harbor.yourdomain.com"
HARBOR_TLS_PORT: 8443

# if set 'false', you need to put certs named harbor.pem and harbor-key.pem in directory 'down'
HARBOR_SELF_SIGNED_CERT: true

# install extra component
HARBOR_WITH_NOTARY: false
HARBOR_WITH_TRIVY: false
HARBOR_WITH_CLAIR: false
HARBOR_WITH_CHARTMUSEUM: true

```

### 1.7.2: 部署k8s集群

通过ansible脚本初始化环境及部署K8s高可用集群

#### 1.7.2.1：步骤1-基础环境初始化

```
root@k8s-master1:/etc/kubeasz# ./ezctl help setup
Usage: ezctl setup <cluster> <step>
available steps:
    01  prepare            to prepare CA/certs & kubeconfig & other system settings 
    02  etcd               to setup the etcd cluster
    03  container-runtime  to setup the container runtime(docker or containerd)
    04  kube-master        to setup the master nodes
    05  kube-node          to setup the worker nodes
    06  network            to setup the network plugin
    07  cluster-addon      to setup other useful plugins
    90  all                to run 01~07 all at once
    10  ex-lb              to install external loadbalance for accessing k8s from outside
    11  harbor             to install a new harbor server or to integrate with an existed one

examples: ./ezctl setup test-k8s 01  (or ./ezctl setup test-k8s prepare)
	  ./ezctl setup test-k8s 02  (or ./ezctl setup test-k8s etcd)
          ./ezctl setup test-k8s all
          ./ezctl setup test-k8s 04 -t restart_master
root@k8s-master1:/etc/kubeasz# vim playbooks/01.prepare.yml #系统基础初始化主机配置
root@k8s-master1:/etc/kubeasz# ./ezctl setup k8s-01 01 #准备CA和基础系统设置

```

#### 1.7.2.2：步骤2-部署etcd集群：

可更改启动脚本路径及版本等自定义配置

```
root@k8s-master1:/etc/kubeasz# ./ezctl setup k8s-01 02 #部署etcd集群
```

验证各etcd节点服务状态

```
root@k8s-master1:/etc/kubeasz# ./ezctl setup k8s-01 02 #部署etcd集群 
```

#### 1.7.2.3：步骤3-部署docker:

```
root@k8s-master1:/etc/kubeasz# ./ezctl setup k8s-01 03  #部署docker
```



#### 1.7.2.5: 步骤4-部署master节点：

```
root@k8s-master1:/etc/kubeasz# ./ezctl setup k8s-01 04

#验证服务器
root@k8s-master1:/etc/kubeasz# kubectl get node
NAME             STATUS                     ROLES    AGE     VERSION
192.168.48.163   Ready,SchedulingDisabled   master   7m35s   v1.22.2
192.168.48.166   Ready,SchedulingDisabled   master   7m33s   v1.22.2

```

#### 1.7.2.6： 步骤5-部署node节点：

```
root@k8s-master1:/etc/kubeasz# ./ezctl setup k8s-01 05

#验证服务器

root@k8s-master1:/etc/kubeasz# kubectl get node
NAME             STATUS                     ROLES    AGE   VERSION
192.168.48.163   Ready,SchedulingDisabled   master   11m   v1.22.2
192.168.48.164   Ready                      node     38s   v1.22.2
192.168.48.165   Ready                      node     39s   v1.22.2
192.168.48.166   Ready,SchedulingDisabled   master   11m   v1.22.2

```

#### 1.7.2.7: 步骤6-部署网络服务

网络组件可以使用calico或者flannel

##### 1.7.2.7.1: 使用calico网络组件：

```
root@k8s-master1:/etc/kubeasz# vim clusters/k8s-01/config.yml
# ------------------------------------------- calico
# [calico]设置 CALICO_IPV4POOL_IPIP=“off”,可以提高网络性能，条件限制详见 docs/setup/calico.md
CALICO_IPV4POOL_IPIP: "Always"

# [calico]设置 calico-node使用的host IP，bgp邻居通过该地址建立，可手工指定也可以自动发现
IP_AUTODETECTION_METHOD: "can-reach={{ groups['kube_master'][0] }}"

# [calico]设置calico 网络 backend: brid, vxlan, none
CALICO_NETWORKING_BACKEND: "brid"

# [calico]更新支持calico 版本: [v3.3.x] [v3.4.x] [v3.8.x] [v3.15.x]
calico_ver: "v3.19.2"

root@k8s-master1:/etc/kubeasz# grep image roles/calico/templates/calico-v3.15.yaml.j2 
          image: calico/cni:v3.15.3
          image: calico/pod2daemon-flexvol:v3.15.3
          image: calico/node:v3.15.3
          image: calico/kube-controllers:v3.15.3
          
          root@k8s-master1:/etc/kubeasz# docker login harbor.magedu.net
Username: admin
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

root@k8s-master1:/etc/kubeasz# docker pull calico/cni:v3.15.3
root@k8s-master1:/etc/kubeasz# docker tag calico/cni:v3.15.3 harbor.magedu.net/baseimages/calico-cni:v3.15.3
root@k8s-master1:/etc/kubeasz# docker push harbor.magedu.net/baseimages/calico-cni:v3.15.3

root@k8s-master1:/etc/kubeasz# docker pull calico/pod2daemon-flexvol:v3.15.3
docker tag calico/pod2daemon-flexvol:v3.15.3 harbor.magedu.net/baseimages/calico-pod2daemon-flexvol:v3.15.3
root@k8s-master1:/etc/kubeasz# docker push harbor.magedu.net/baseimages/calico-pod2daemon-flexvol:v3.15.3

root@k8s-master1:/etc/kubeasz# docker pull calico/node:v3.15.3
root@k8s-master1:/etc/kubeasz# docker tag calico/node:v3.15.3 harbor.magedu.net/baseimages/calico-node:v3.15.3
root@k8s-master1:/etc/kubeasz# docker push harbor.magedu.net/baseimages/calico-node:v3.15.3

root@k8s-master1:/etc/kubeasz# docker pull calico/kube-controllers:v3.15.3
root@k8s-master1:/etc/kubeasz# docker tag calico/kube-controllers:v3.15.3 harbor.magedu.net/baseimages/calico-kube-controllers:v3.15.3
root@k8s-master1:/etc/kubeasz# docker push harbor.magedu.net/baseimages/calico-kube-controllers:v3.15.3

#修改镜像地址：
root@k8s-master1:/etc/kubeasz#  vim roles/calico/templates/calico-v3.15.yaml.j2

root@k8s-master1:/etc/kubeasz# grep image roles/calico/templates/calico-v3.15.yaml.j2 
          image: harbor.magedu.net/baseimages/calico-cni:v3.15.3
          image: harbor.magedu.net/baseimages/calico-pod2daemon-flexvol:v3.15.3
          image: harbor.magedu.net/baseimages/calico-node:v3.15.3 
          image: harbor.magedu.net/baseimages/calico-kube-controllers:v3.15.3


root@k8s-master1:/etc/kubeasz# ./ezctl setup k8s-01 06
```

验证calico:

```
root@k8s-master1:/etc/kubeasz# calicoctl node status
Calico process is running.

IPv4 BGP status
+----------------+-------------------+-------+----------+-------------+
|  PEER ADDRESS  |     PEER TYPE     | STATE |  SINCE   |    INFO     |
+----------------+-------------------+-------+----------+-------------+
| 192.168.48.163 | node-to-node mesh | up    | 09:41:41 | Established |
| 192.168.48.165 | node-to-node mesh | up    | 09:41:41 | Established |
| 192.168.48.164 | node-to-node mesh | up    | 10:19:09 | Established |
+----------------+-------------------+-------+----------+-------------+

IPv6 BGP status
No IPv6 peers found.

```

#### 1.7.3.1: 创建容器测试网络通信：

```
root@k8s-master2:~# docker pull harbor.magedu.net/library/alpine
#创建Pod测试跨主机网络通信是否正常
root@k8s-master2:~# kubectl run net-test1 --image=harbor.magedu.net/library/alpine sleep 36000
pod/net-test1 created
root@k8s-master2:~# kubectl run net-test2 --image=harbor.magedu.net/library/alpine sleep 36000
pod/net-test2 created
root@k8s-master2:~# kubectl run net-test3 --image=harbor.magedu.net/library/alpine sleep 36000
pod/net-test3 created


root@k8s-master1:~# kubectl get po -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP               NODE             NOMINATED NODE   REA
net-test1   1/1     Running   0          12s   10.200.36.69     192.168.48.164   <none>           <no
net-test2   1/1     Running   0          15m   10.200.169.129   192.168.48.165   <none>           <no
net-test3   1/1     Running   0          15s   10.200.36.68     192.168.48.164   <none>           <n
```
