---
layout: post
title: docker容器取消link
date: 2022-02-22
tags: docker
---



# 一 ：问题

docker 应用服务器启动做了--link依赖其数据库DB ，能否启动不依赖本机DB

## 1.1 查看容器

```
[root@haproxy1 ~]# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED         STATUS         PORTS                                                 NAMES
0bb3d9fc0c18   odoo:10.0       "/entrypoint.sh odoo"    2 minutes ago   Up 2 minutes   8071/tcp, 0.0.0.0:8093->8069/tcp, :::8093->8069/tcp   ckxt_app
9be085956550   postgres:10.0   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   0.0.0.0:54323->5432/tcp, :::54323->5432/tcp           ckxt_db
```

### 1.1.1 查看桥接网络信息

```
[root@haproxy1 ~]# docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "6381e2a9fd2c46f48de47c3ad9df2110a496d93f2650492e63b7a04597fa43cb",
        "Created": "2022-02-22T23:29:37.514667889+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "0bb3d9fc0c18748ab860343513b04d314ca73daea39bd99400efe58108773783": {
                "Name": "ckxt_app",
                "EndpointID": "a109752c6f32f3776bcda7b5fc46e60c59a937c6d7dcd51512a053e8269f7777",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "9be085956550206828e11fd7936bd1c5bedc1b5009f52f133adbfa0422c6f98d": {
                "Name": "ckxt_db",
                "EndpointID": "2cc829b44a18f0b9d9bf8952ea7694b448d8a0c82e3f4a2a25f4ab4b8151c521",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]

```

### 1.1.2：查看docker网络

```
[root@haproxy1 ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
6381e2a9fd2c   bridge    bridge    local
2b0f5d61162c   host      host      local
8978a921e54a   none      null      local

```

## 1.2： 创建网络

```
[root@haproxy1 ~]# docker network create my-bridge
7fa1e9bee2bd4ca26833cab709153959e1d23bacf2694f66755ab7ce88aeec37
#查看创建的网络
[root@haproxy1 ~]# docker network ls
NETWORK ID     NAME        DRIVER    SCOPE
6381e2a9fd2c   bridge      bridge    local
2b0f5d61162c   host        host      local
7fa1e9bee2bd   my-bridge   bridge    local
8978a921e54a   none        null      local

```

### 1.2: 配置网络

```



[root@haproxy1 ~]# docker network disconnect bridge ckxt_app#配置容器到指定的网络
[root@haproxy1 ~]# docker network connect my-bridge ckxt_app

#查看容器是否添加到网络
[root@haproxy1 ~]# docker network inspect my-bridge
[
    {
        "Name": "my-bridge",
        "Id": "7fa1e9bee2bd4ca26833cab709153959e1d23bacf2694f66755ab7ce88aeec37",
        "Created": "2022-02-22T23:45:13.574190455+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "0bb3d9fc0c18748ab860343513b04d314ca73daea39bd99400efe58108773783": {
                "Name": "ckxt_app",
                "EndpointID": "b86e4eff60440b24a1c73eab23c578269d0501c982352da4a8e660fdf8a576b1",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]


#取消容器指定的网络
[root@haproxy1 ~]# docker network disconnect bridge ckxt_app

```

## 1.3：验证

```
#停止数据库容器
[root@haproxy1 ~]# docker stop ckxt_db
ckxt_db
#停掉应用容器
[root@haproxy1 ~]# docker stop ckxt_app
ckxt_app

#启动应用容器
[root@haproxy1 ~]# docker start ckxt_app
ckxt_app

#查看容器
[root@haproxy1 ~]# docker ps
CONTAINER ID   IMAGE       COMMAND                 CREATED          STATUS         PORTS                                                 NAMES
0bb3d9fc0c18   odoo:10.0   "/entrypoint.sh odoo"   11 minutes ago   Up 3 seconds   8071/tcp, 0.0.0.0:8093->8069/tcp, :::8093->8069/tcp   ckxt_app


```

## 1.4：结论

经过以上测试停掉数据库容器，应用容器可以正常启动