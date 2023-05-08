layout: post
title: Prometheus监控平台
date: 2022-05-08

## tags: Prometheus

# 一：项目名称：Promehtues监控平台部署

## 1.2: 项目描述：

在公司提供一个可靠的Prometheus监控解决方案，以监视公司K8S集群和宿主机资源,该项目将包括以下组件:

* Prometheus服务器：负责收集和存储监控指标、并提供查询和警报功能
* Exporters: 用于从各种应用程序和服务中收集监控指标
* Alertmanager: 负责处理警报，通过渠道通知相关人员
* Grafana: 用于可视化监控数据和创建仪表板
* PrometheusAlert: 运维告警中心消息转发系统
* consul：用于服务的注册和发现



### 1.3：项目职责:

* 研究和评估prometheus监控解决方案，以确定最合适公司需求的配置和组件
* 安装和配置Prometheus服务器、Exporter Alertmanage、PromeehtuesAlert和Grafana，确保能够正常使用
* 实现Prometheus监控规则和警报
* 创建Grafana仪表盘，以便公司团队可以访问和分析监控数据
* 定期检查和优化Pormehtues监控解决方案的性能和可靠性

## 二：二进制部署Prometheus Server:

## 2.1：解压二进制程序：

```bash
root@prometheus-server:~# mkdir /apps
root@prometheus-server:/apps# wget https://github.com/prometheus/prometheus/releases/download/v2.33.4/prometheus-2.33.4.linux-amd64.tar.gz

root@prometheus-server:/apps# tar xvf prometheus-2.33.4.linux-amd64.tar.gz
root@prometheus-server:/apps# ln -sv /apps/prometheus-2.33.4.linux-amd64 /apps/prometheus
'/apps/prometheus' -> '/apps/prometheus-2.33.4.linux-amd64'

root@prometheus-server:/apps# cd prometheus
root@prometheus-server:/apps/prometheus# ll
total 196084
drwxr-xr-x 4 3434 3434      4096 2月  23 01:03 ./
drwxr-xr-x 3 root root      4096 4月  21 15:13 ../
drwxr-xr-x 2 3434 3434      4096 2月  23 00:59 console_libraries/
drwxr-xr-x 2 3434 3434      4096 2月  23 00:59 consoles/
-rw-r--r-- 1 3434 3434     11357 2月  23 00:59 LICENSE
-rw-r--r-- 1 3434 3434      3773 2月  23 00:59 NOTICE
-rwxr-xr-x 1 3434 3434 104419379 2月  23 00:54 prometheus* #prometheus服务可执行程序
-rw-r--r-- 1 3434 3434       934 2月  23 00:59 prometheus.yml #prometheus配置文件
-rwxr-xr-x 1 3434 3434  96326544 2月  23 00:57 promtool* #测试工具，用于测试配置prometheus配置文件、监测metrics数据等

#测试配置文件是否正常
root@prometheus-server:/apps/prometheus# ./promtool check config ./prometheus.yml 
Checking ./prometheus.yml
 SUCCESS: ./prometheus.yml is valid prometheus config file syntax


```



### 2.1.1: 创建prometheus service启动脚本:

```bash
root@prometheus-server:~# cat /etc/systemd/system/prometheus.service 
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network.target

[Service]
Restart=on-failure
WorkingDirectory=/apps/prometheus/
ExecStart=/apps/prometheus/prometheus --config.file=/apps/prometheus/prometheus.yml

[Install]
WantedBy=multi-user.target

```



### 2.1.2: 启动prometheus服务：

```bash
root@prometheus-server:~# systemctl daemon-reload && systemctl restart prometheus && systemctl enable prometheus
```



### 2.1.3: 验证prometheus web界面：



![](/images/posts/09_prometheus/01.png)





## 2.2: 二进制安装node-exporter:

```bash
root@prometheus-server2:~# cd /apps/
root@prometheus-server2:/apps# wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
root@prometheus-server2:/apps# tar zxvf node_exporter-1.3.1.linux-amd64.tar.gz

root@prometheus-server2:/apps# ln -sv /apps/node_exporter-1.3.1.linux-amd64 /apps/node_exporter
'/apps/node_exporter' -> '/apps/node_exporter-1.3.1.linux-amd64'

root@prometheus-server2:/apps# ll /apps/node_exporter/
total 17828
drwxr-xr-x 2 3434 3434     4096 12月  5 19:15 ./
drwxr-xr-x 3 root root     4096 4月  21 20:48 ../
-rw-r--r-- 1 3434 3434    11357 12月  5 19:15 LICENSE
-rwxr-xr-x 1 3434 3434 18228926 12月  5 19:10 node_exporter*
-rw-r--r-- 1 3434 3434      463 12月  5 19:15 NOTICE

```



### 2.2.1: 创建node-exporter service启动文件:

```bash
root@prometheus-server2:/apps# vim /etc/systemd/system/node-exporter.service


[Unit]
Description=Prometheus Node Exporter
After=network.target

[Service]
Type=simple
ExecStart=/apps/node_exporter/node_exporter
Restart=on-failure

[Install]
WantedBy=multi-user.target
```



## 2.2.2: 启动node exporter服务：

```bash
root@prometheus-server2:/apps# systemctl daemon-reload && systemctl restart node-exporter && systemctl enable node-exporter.service
```



### 2.2.3: 验证Node exporter web界面：



![](/images/posts/09_prometheus/02.png)



## 2.3：部署Grafana：

https://grafana.com/grafana/download/ #下载地址

https://grafana.com/docs/grafana/latest/installation/requirements/ #安装文档

```bash
部署要求：可以和prometheus server分离部署，只要网络通信正常即可
#sudo apt-get install -y adduser
root@prometheus-server:~# wget https://dl.grafana.com/enterprise/release/grafana-enterprise_8.3.7_amd64.deb
root@prometheus-server:~# dpkg -i grafana-enterprise_8.3.7_amd64.deb
```



### 2.3.1: grafana server配置文件：

```bash
root@prometheus-server:~# vim /etc/grafana/grafana.ini
[server]
# Protocol (http, https, h2, socket)
;protocol = http

# The ip address to bind to, empty will bind to all interfaces
;http_addr =

# The http port  to use
;http_port = 3000

```



## 2.3.2: 启动grafana: 

```bash
root@prometheus-server:~# systemctl restart grafana-server
root@prometheus-server:~# systemctl enable grafana-server

```

### 2.3.3: 验证grafana web界面：



![](/images/posts/09_prometheus/03.png)





## 2.4：部署consul集群：

https://releases.hashicorp.com/consul

```bash
环境：
公司两台阿里云服务器，下面以私有IP为例
192.168.48.164（主）
192.168.48.165

192.168.48.164服务器上操作
# wget https://releases.hashicorp.com/consul/1.13.1/consul_1.13.1_linux_amd64.zip
# unzip consul_1.13.1_linux_amd64.zip
# scp consul 192.168.48.165:/usr/local/bin/
# scp consul 192.168.48.164:/usr/local/bin/

分别创建数据目录
# mkdir /data/consul

192.168.48.164：
# nohup consul agent -server -bootstrap -bind=192.168.48.164 -client=192.168.48.164 -data-dir=/data/consul/ -ui -node=192.168.48.164 &

192.168.48.165
# nohup consul agent  -bind=192.168.48.165 -client=192.168.48.165 -data-dir=/data/consul/ -node=192.168.48.165 -join=192.168.48.164 &
```

### 2.4.1：验证集群：

日志：

```bash
# tail nohup.out 
2022-09-24T22:26:02.324+0800 [INFO]  agent.server: cluster leadership acquired
2022-09-24T22:26:02.326+0800 [INFO]  agent.server.autopilot: reconciliation now enabled
2022-09-24T22:26:02.327+0800 [INFO]  agent.leader: started routine: routine="federation state anti-entropy"
2022-09-24T22:26:02.327+0800 [INFO]  agent.leader: started routine: routine="federation state pruning"
2022-09-24T22:26:02.327+0800 [INFO]  agent.server: deregistering member: member=192.168.48.165 partition=default reason=reaped
2022-09-24T22:26:02.328+0800 [INFO]  agent.server: New leader elected: payload=192.168.48.164
2022-09-24T22:26:02.328+0800 [INFO]  agent: Synced node info
2022-09-24T22:26:10.625+0800 [INFO]  agent.server.serf.lan: serf: EventMemberJoin: 192.168.48.165 192.168.48.165
2022-09-24T22:26:10.626+0800 [INFO]  agent.server: member joined, marking health alive: member=192.168.48.165 partition=default
2022-09-24T22:26:26.348+0800 [INFO]  agent: Newer Consul version available: new_version=1.13.2 current_version=1.13.
```





web截图;



![](/images/posts/09_prometheus/04.png)



## 2.5：consul服务注册：



```bash
# curl -X PUT -d '{"id": "物流云门户-贸易云","name": "huabei-1","address":"192.168.48.165","port":9100,"tags": ["node-exporter"],"checks": [{"http":"http://192.168.48.165:9100/","interval":"15s"}]}' http://192.168.48.164:8500/v1/agent/service/register
```



## 2.5.1： 验证：



![](/images/posts/09_prometheus/05.png)



![](/images/posts/09_prometheus/06.png)

## 2.6：prometheus配置更改：



```bash
# cat prometheus.yml 
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
#          - 172.26.92.129:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  #- rules/*.yml
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
  - job_name: "huabei-1"
    honor_labels: true
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [huabei-1]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
  - job_name: "huabei-2"
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [huabei-2]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
  - job_name: "huabei-3"
    honor_labels: true
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [huabei-3]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
  - job_name: "cadvisor-containerd"
    honor_labels: true
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [hmm-container]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
    - source_labels: [__meta_consul_tags]
      regex: .*container.*
      action: keep
    - regex: __meta_consul_service_metadata_(.+)
      action: labelmap

```



### 2.6.1: prometheus界面验证:



![](/images/posts/09_prometheus/07.png)



# 三：Alertmanager部署:

```bash
# pwd
/apps
# wget https://github.com/prometheus/alertmanager/releases/download/v0.24.0/alertmanager-0.24.0.linux-amd64.tar.gz
# tar -xf alertmanager-0.24.0.linux-amd64.tar.gz
# ln -sv /apps/alertmanager-0.24.0.linux-amd64 /apps/alertmanager
'/apps/alertmanager' -> '/apps/alertmanager-0.24.0.linux-amd64'

# vim /etc/systemd/system/alertmanager.service
[Unit]
Description=Prometheus alertmanager
After=network.target

[Service]
ExecStart=/apps/alertmanager/alertmanager --config.file="/apps/alertmanager/alertmanager.yml"

[Install]
WantedBy=multi-user.target

# systemctl daemon-reload && systemctl restart alertmanager.service && systemctl enable alertmanager.service
```



## 3.1: PrometheusAlert部署:

```bash
#wget https://github.com/feiyu563/PrometheusAlert/releases/download/v4.7/linux.zip && unzip linux.zip &&cd linux/

#更改登录信息：
 # cat app.conf 
#---------------------↓全局配置-----------------------
appname = PrometheusAlert
#登录用户名
login_user=用户名
#登录密码
login_password=密码
#监听地址
httpaddr = "0.0.0.0"
#监听端口
httpport = 端口
runmode = dev
#设置代理 
proxy = http://IP:端口

 
```



### 3.1.1：PrometheusAlert界面验证:



![](/images/posts/09_prometheus/08.png)





### 3.1.2: 自定义模板:

```bash
{{ $var := .externalURL}}{{ range $k,$v:=.alerts }}
{{if eq $v.status "resolved"}}
#### [Prometheus恢复信息]({{$v.generatorURL}})

##### <font color="#02b340">【告警名称】</font>：[{{$v.labels.alertname}}]({{$var}})🔥{{if $v.labels.severity}}
#### <font color="#02b340">【告警级别】</font>：{{if eq $v.labels.severity "0"}}提示{{else if eq $v.labels.severity "1"}}警告{{else if eq $v.labels.severity "2"}}一般严重{{else if eq $v.labels.severity "3"}}严重{{else if eq $v.labels.severity "4"}}灾难{{else}}{{$v.labels.severity}}{{end}}{{end}}
#### <font color="#02b340">【当前状态】</font>：**<font color="#67C23A" size=4>已恢复</font>**
##### <font color="#02b340">【触发时间】</font>：{{GetCSTtime $v.startsAt}}
##### <font color="#02b340">【恢复时间】</font>：{{GetCSTtime $v.endsAt}}
##### <font color="#02b340">【告警IP】</font>：{{$v.labels.hmmname}}
##### <font color="#02b340">【告警主机】</font>：{{$v.labels.hmmid}}
#### =======<font color="info">告警详情</font>=======
**<font color="#FF0000">告警主题</font>：{{$v.annotations.summary}}**
**<font color="#FF0000">告警内容</font>：{{$v.annotations.description}}**
[prometheus](http://121.89.235.153:9090/alerts) | 📈[grafana](http://121.89.235.153:3000) | 🚨[alertmanager](http://121.89.235.153:9093)
![aeotrade](https://www.aeotrade.com/_nuxt/img/mainLogo.284fc34.png)
{{else}}
#### [Prometheus告警信息]({{$v.generatorURL}})

##### <font color="#FF0000">【告警名称】</font>：[{{$v.labels.alertname}}]({{$var}})✅{{if $v.labels.severity}}
#### <font color="#02b340">【告警级别】</font>：{{if eq $v.labels.severity "0"}}提示{{else if eq $v.labels.severity "1"}}警告{{else if eq $v.labels.severity "2"}}一般严重{{else if eq $v.labels.severity "3"}}严重{{else if eq $v.labels.severity "4"}}灾难{{else}}{{$v.labels.severity}}{{end}}{{end}}
####  <font color="#FF0000">【当前状态】</font>：**<font color="#E6A23C">需要处理</font>**
##### <font color="#02b340">【告警IP】</font>：{{$v.labels.hmmname}}
##### <font color="#02b340">【告警主机】</font>：{{$v.labels.hmmid}}
#### =======<font color="#FF0000">告警详情</font>=======
**<font color="#FF0000">告警主题</font>：{{$v.annotations.summary}}**
**<font color="#FF0000">告警内容</font>：{{$v.annotations.description}}**
[prometheus](http://121.89.235.153:9090/alerts) | 📈[grafana](http://121.89.235.153:3000) | 🚨[alertmanager](http://121.89.235.153:9093)
![aeotrade](https://www.aeotrade.com/_nuxt/img/mainLogo.284fc34.png)
{{end}}
{{ end }}

```



## 3.2: 配置Alertmanager钉钉告警：

```bash
# cat alertmanager.yml 
route:
  group_by: ['alertname']
  group_wait: 15s
  group_interval: 2m
  repeat_interval: 5m
  receiver: 'web.hook'
receivers:
  - name: 'web.hook'
    webhook_configs:
      - url: 'http://172.26.92.129:8080/prometheusalert?type=dd&tpl=dingding&ddurl=钉钉ID&at=@手机号'

```



### 3.3：配置告警规则：

```bash
# cat node_rule.yml 
groups:
  - name: Host 
    rules:
    - alert: 主机存活告警 #名命
      expr: up == 0 #表达式，分析指标判定告警
      for: 60s #出发告警持续时间
      labels: #自定义告警标签
        severity: critical
      annotations: #告警内容注释
        summary: "{{ $labels.instance}} 冗机超过1分钟"
    
    - alert: Host user Memory
      expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 80 
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "{{$labels.mountpoint}} 内存使用率过高!"
        description: "{{$labels.mountpoint}} 内存使用大于80%"
    - alert: Host user Disk 
      expr: 100 - (node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 80
      for: 5m
      labels:
        severity: critical
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

```



### 3.3.1: 配置prometheus告警配置:

```bash
# cat prometheus.yml 
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - 172.26.92.129:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - rules/*.yml
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
  - job_name: "huabei-1"
    honor_labels: true
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [huabei-1]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
  - job_name: "huabei-2"
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [huabei-2]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
  - job_name: "huabei-3"
    honor_labels: true
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [huabei-3]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
  - job_name: "cadvisor-containerd"
    honor_labels: true
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 172.26.92.129:8500
        services: [hmm-container]
    relabel_configs:
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
    - source_labels: ['__meta_consul_service_address']
      action: replace
      target_label: hmmname    
    - source_labels: ['__meta_consul_service_id']
      action: replace
      target_label: hmmid
    - source_labels: [__meta_consul_tags]
      regex: .*container.*
      action: keep
    - regex: __meta_consul_service_metadata_(.+)
      action: labelmap

```



# 四：告警效果:



![](/images/posts/09_prometheus/09.png)





## 4.1：Grafana界面效果:



![](/images/posts/09_prometheus/10.png)