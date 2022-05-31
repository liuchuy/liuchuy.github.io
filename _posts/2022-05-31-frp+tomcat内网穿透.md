# frp实现端口映射（本地tomcat可以被外网访问）

## 1.1 ：frp工作原理

 frp 的应用程序是分服务端和客户端的，服务器端的运行在有公网 ip 的服务器上，客户端就运行在需要穿透的内网机器上就行。

两端的程序运行起来之后，就会建立好通信的隧道，当我们访问公网 ip+端口 时，就会映射到我们内网的机器上了。



# 2：下载frp项目

github地址：

https://github.com/fatedier/frp/releases

![](/images/posts/07_frp/0/1.png)



## 2.1: frp服务端部署

```
root@hecs-299670:~# wget https://github.com/fatedier/frp/releases/download/v0.42.0/frp_0.42.0_linux_amd64.tar.gz
root@hecs-299670:/data# tar xf frp_0.42.0_linux_amd64.tar.gz
root@hecs-299670:/data/frp_0.42.0_linux_amd64# cat frps.ini 
[common]
bind_port = 7000 #与客户端绑定的进行通信的端口
vhost_http_port = 7071 #访问客户端web服务自定义的端口
dashboard_port = 7500 #web页面端口

frpc 开头的代表着客户端使用
frps 开头的代表服务端使用
.ini结尾的文件事frp的配置文件，也是需要我们进行修改的文件
```

![](/images/posts/07_frp/0/2.png)



```
#放入后台运行
root@hecs-299670:/data/frp_0.42.0_linux_amd64# nohup ./frps -c frps.ini & 
```



## 2.2：验证



![](/images/posts/07_frp/0/3.png)



## 2.3：nginx配置

```
server {
	listen 80;
	server_name 114.116.10.132;
	location / {
	  proxy_pass http://127.0.0.1:7071;
       proxy_set_header X-Forwarded-For                proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_max_temp_file_size 0;
        proxy_redirect off;
        proxy_read_timeout 240s;

	}

```



# 3：客户端部署

```
[root@s1 ~]# wget https://github.com/fatedier/frp/releases/download/v0.42.0/frp_0.42.0_linux_amd64.tar.gz
[root@s1 ~]# tar xf frp_0.42.0_linux_amd64.tar.gz
root@hecs-299670:/data/frp_0.42.0_linux_amd64# cat frps.ini 
[root@s1 frp_0.42.0_linux_amd64]# cat frpc.ini 
[common]
server_addr = 114.116.10.132 #公网的IP
server_port = 7000   #和你在服务器配置文件binder_port的一致

[web]
type = http  #选择的类型，如果你是tcp就写tcp
local_ip = 127.0.0.1   
local_port = 8080   #你要映射的端口，tomcat默认是8080
remote_port = 8080
custom_domains = 114.116.10.132  #公共IP
```

### 3.1：后台启动

```
[root@s1 frp_0.42.0_linux_amd64]# nohup ./frpc -c frpc.ini &
```

### 3.2：验证



![](/images/posts/07_frp/0/4.png)



### 3.3：浏览器验证

公网IP+端口



![](/images/posts/07_frp/0/5.png)
