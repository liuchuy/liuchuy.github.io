layout: post
title: Prometheus
date: 2024-04-08

tags: Prometheus
--- 



### 一：监控及promethues简介：

监控的重要性：

通过业务监控系统，全面掌握业务环境的运行状态，通过白盒监控能够提前预知业务瓶颈，通过黑盒监控能够第一时间发现业务故障并通过告警通知运维人员进行紧急恢复，从而将业务影响降到最低。

```
黑盒监控,关注的是时时的状态，一般是正在发生的事件，比如nginx web界面打开的界面报错503、磁盘无法报错数据等，既黑盒监控重点在于能对正在发生的故障通知告警

白盒监控，关注的是原因，也就是系统内部暴露的一些指标数据，比如Nignx 后端服务器的响应时长，磁盘的I/O负载值等
```

监控系统需要能够有效的支持白盒监控和黑盒监控，通过白盒能够了解其内部的实际运行状态，以及对监控的指标的观察能够预判可能出现的潜在问题，从而对潜在的不确定因素进行提前优化并避免问题的发生，而通过黑盒监控，比如常见的如HTTP探针、TCP探针等，可以在系统或者服务发生故障时能够快速通知相关的人员进行处理，通过建立完善的监控体系，从而达到以下目的：

```
长期皱势分析：通过对监控样本数据的持续收集和统计，对监控指标进行长期皱势分析，例如，通过对磁盘空间增长率的判断，我们可以提前预测在未来什么时间节点上需要对资源进行扩容

对照分析：两个版本的系统运行资源使用情况的差异如何？在不同容量情况下系统的并发和负载变化如何？通过监控能够方便的系统进行跟踪和比较

告警：当系统出现或者即将出现故障时，监控系统需要迅速反应并通知管理员，从而能够对问题进行快速的处理或者提前预防问题的发生，避免出现对业务的影响

故障分析与定位：当问题发生后，需要对问题进行调查和处理，通过对不同监控监控以及历史数据的分析，能够找到并解决根源问题

数据可视化：通过可视化仪表盘能够直接获取系统的运行状态、资源使用情况、以及服务运行状态等直观的信息
```



## 1.1：监控逻辑布局：

![](/images/posts/11_prometheus/1.png)



## 1.2：常见的监控方案：

开源监控软件：cacti、nagios、zabbix、smokeping、open-falcon等

### 1.2.3：Cacti:

https://www.cacti.net/

https://github.com/Cacti/cacti

```
Cacti是基于LAMP平台展现的网络流量监测及分析工具，通过SNMP技术或自定义脚本从目标设备/主机获取监控指标信息。其次进行数据存储，调用模板将数据存到数据库，使用rrdtool存储和更新数据，通过rrdtools绘制结果图形，最后进行数据展现，通过web方式将监控结果呈现出来，常在与数据中心监控网络设备
```



### 1.2.4：Nagios:

https://www.nagios.org/

```
Nagios用来监视系统和网络的开源应用软件，利用其众多的插件实现对本机和远端服务的监控，当被监控对象发生异常时，会及时向管理员告警，提供一批预设好的监控插件，用户可以之间调用，也可以自定义shell脚本来监控服务，适合企业的业务监控，可通过wbe页面显示对象状态、日志、告警信息，分层告警机制及自定义监控相对薄弱
```

### 1.2.5：SmokePing:

https://oss.oetiker.ch/smokeping/

http://blogs.studylinux.net/?p=794

```
Smokeping是一款用于网络性能监测的开源监控软件，主要用于IDC的网络状态，网络质量，稳定性能等监测，通过rrdtool制图方式，图形化地展示网络的时延情况，进而能够的判断出网络的即使通信情况
```

### 1.2.6：Zabbix:

https://www.zabbix.com/cn/

```
目前使用较多的开源监控软件，可横向扩展，自定义监控、支持多种监控方式、可监控网络与服务等
```

### 1.2.7：Prometheus:

```
针对容器环境的开源监控软件
```

## 1.3：prometheus简介：

https://prometheus.io/

Prometheus受启发于Google的Brogmon监控系统（相似的Kubernetes是从Google的Brog系统演变而来），基于go语言开发的一套开源的监控、报警和时间序列数据库的组合，从 2012年开始由前Google工程师在Soundcloud以开源软件的形式进行研发，并且于2015年早期对外发布早期版本，2016年5月继Kubernetes之后成为第二个正式加入CNCF基金会的项目，同年6月正式发布1.0版本。2017年底发布 了基于全新存储层的2.0版本，能更好地与容器平台、云平台配合。 

Prometheus作为新一代的云原生监控系统，目前已经有超过650+位贡献者参与到Prometheus的研发工作上，并且 超过120+项的第三方集成。

```
使用key-value的多维度（多个角度，多个层面，多个方面）格式保存数据
数据不使用MySQL这样的传统数据库，而实使用时序数据库，目前是使用的TSDB
支持第三方dashboard实现更高的图形界面，如grafana(Grafana 2.5.0版本及以上)组件模块发
不需要依赖存储，数据可以本地保存也可以远程保存
平均每个采样点仅占3.5 byted 且一个prometheus server可以处理数百万级别的metrics指标数据
支持服务自动发现（基于consul等方式动态发现被监控的目标服务）
强大的数据查询语句（PromQL,Prometheus Query Language）
数据可以直接进行算数运算
易于横向伸缩
众多官方和第三方的exporter实现不同的指标数据收集
```

### 1.3.1：为什么使用Prometheus:

容器监控的实现方对比虚拟机或者物理机来说比大的区别，比如容器在k8s环境中可以任意而横向扩容与缩容，那么就需要监控服务能够自动对新创建的容器进行监控，当容器删除后又能够即使的从监控服务中删除，而传统的zabbix的监控方式需要在每一个容器中安装启动agent，并且在容器自动发现注册及模板关联方面没有比较好的实现方式。

### 1.3.2：Prometheus架构图：

```
prometheus server: 主服务，接受外部的Http请求，收集，存储与查询数据等
prometheus targets:静态收集的目标服务数据
service discovery:动态发现服务
prometheus alerting: 报警通知
push gateway: 数据收集代理服务器（类似于zabbix proxy）
data visualization and export: 数据可视化于数据导出（访问客户端）
```



![](/images/posts/11_prometheus/2.png)



## 1.4: prometheus组件

### 1.4.1：prometheus Server

prometheus server是prometheus组件中的核心不分，负载实现对监控数据的获取，存储以及查询，prometheus server可以通过静态配置管理监控目标，也可以配合使用Service Discovery的方式动态管理监控目标，并从这些目标中获取数据，其次prometheus server需要对采集到的监控数据进行存储，prometheus server本身就是一个时序数据库，将采集到的监控数据按照时间序列的方式存储在本地磁盘中，最后prometheus server对方提供了自定义的PromQL语言，实现对数据的查询以及分析



prometheus server内置的Express Browser UI 通过这个UI可以直接通过PromQL实现数据的查询以及可视化

Prometheus server的联邦集群能力可以使从其他的prometheus server实例中获取数据，因此在大规模监控的情况下，可以通过联邦集群以及功能分区的方式对prometheus server进行扩展



### 1.4.2：Exporters

Exporter将监控数据采集的端点通过HTTP服务的形式暴露给prometheus server,Prometheus server通过访问该Exporter提供的Endpoint端点，既可以获取到需要采集的监控数据

一般来说可以将Exporter分为2类

* 直接采集：这一类Exporter直接内置了对Prometheus监控的支持，比如cAdvisor.kubernets,Etcd,Gokit等，直接内置了用于想prometheus暴漏监控数据的端点
* 间接采集：间接采集，原有监控目标并不直接支持prometheus,因此我们需要通过prometheus提供的client libray编写该监控目标的监控采集程序。



### 1.4.3：AlertManager

在prometheus server中支持基于promQL创建告警规则，如果满足PromQL定义的规则，则会产生一条告警，而告警的后续处理流程则由AlertManager进行管理，在AlertManager中我们可以与邮件，Slack等等内置的通知方式进行集成，也可以通过webhook自定义告警处理方式，AlertManager既Prometheus体系中的告警处理中心



### 1.4.4：PushGateway

由于prometheus数据采集基于pull模型进行设计，因此在网络环境的配置上必须要让prometheus server能够直接与Exporter进行通信，当这种网络需求无法直接满足时，可以利用PushGateway来进行只能够转，可以通过pushGateway将不网络的监控数据主动Push到Gateway当中，而peometheus server则可以次啊用同样的pull的方式从pushgateway中获取到监控数据

### 1.4.5: Client Library

客户端库，目的在于为那些期望原生提供Instrumentation功能的应用程序提供便捷的开发途径



### 1.4.6：Data Visulization

Prometheus WEB UI (Prometheus Server内建)，及Grafana登

### 1.4.7：Service Discovery

动态发现待监控的Target,从而完成监控配置的重要组件，在容器化环境中尤为有用，改组件目前由Prometheus Server内建支持



## 二：部署Prometheus监控系统：



## 2.1: Operator部署prometheus监控系统

Operator部署器基于已经编写好的yaml文件，可以将prometheus server 、altermanager、grafana、node-exporter等组件一键批量部署

```
部署环境：在当前已有的kubernetes环境部署
```



### 2.2.1：clone项目部署

```bash
# git clone -b release-0.11 https://github.com/prometheus-operator/kube-prometheus.git
# cd kube-prometheus/
# kubectl apply --server-side -f manifests/setup
# grep image: manifests/ -R | grep "k8s.gcr.io #有部分镜像无法下载
manifests/kubeStateMetrics-deployment.yaml:        image: k8s.gcr.io/kube-state-metrics/kube-state-metrics:v2.5.0
manifests/prometheusAdapter-deployment.yaml:        image: k8s.gcr.io/prometheus-adapter/prometheus-adapter:v0.9.1

准备kube-state-metrics镜像：
# docker pull bitnami/kube-state-metrics:2.5.0
# docker tag bitnami/kube-state-metrics:2.5.0 harbor.magedu.net/prod/kube-state-metrics:2.5.0
# docker push harbor.magedu.net/prod/kube-state-metrics:2.5.0
# vim manifests/kubeStateMetrics-deployment.yaml

准备prometheus-adapter镜像：
manifests/prometheusAdapter-deployment.yaml:        image: k8s.gcr.io/prometheus-adapter/prometheus-adapter:v0.9.1

# docker pull willdockerhub/prometheus-adapter:v0.9.1
#docker tag willdockerhub/prometheus-adapter:v0.9.1 harbor.magedu.net/prod/prometheus-adapter:v0.9.1
# docker push harbor.magedu.net/prod/prometheus-adapter:v0.9.1

# vim manifests/prometheusAdapter-deployment.yaml

# kubectl apply -f manifests/ #有些镜像无法下载需要自行解决
```

### 2.2.2: 验证pod状态

```bash
root@s3:~# kubectl get po -n monitoring
NAME                                   READY   STATUS    RESTARTS   AGE
alertmanager-main-0                    2/2     Running   0          56m
alertmanager-main-1                    2/2     Running   0          56m
alertmanager-main-2                    2/2     Running   0          56m
blackbox-exporter-5c545d55d6-kj4rd     3/3     Running   0          58m
grafana-7945df75ff-pcqj9               1/1     Running   0          58m
kube-state-metrics-67575d856c-mfd5p    3/3     Running   0          40m
node-exporter-hslqz                    2/2     Running   0          58m
node-exporter-j9wlv                    2/2     Running   0          58m
node-exporter-w7ddb                    2/2     Running   0          58m
prometheus-adapter-7454586bbb-bqbkq    1/1     Running   0          58m
prometheus-adapter-7454586bbb-mlrhn    1/1     Running   0          58m
prometheus-k8s-0                       2/2     Running   0          56m
prometheus-k8s-1                       2/2     Running   0          56m
prometheus-operator-54dd69bbf6-6hzd7   2/2     Running   0          58m

```

### 2.2.3: 通过NodePort访问Prometheus Server:

```bash
# vim manifests/prometheus-service.yaml
# cat manifests/prometheus-service.yaml 
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/component: prometheus
    app.kubernetes.io/instance: k8s
    app.kubernetes.io/name: prometheus
    app.kubernetes.io/part-of: kube-prometheus
    app.kubernetes.io/version: 2.36.1
  name: prometheus-k8s
  namespace: monitoring
spec:
  type: NodePort
  ports:
  - name: web
    port: 9090
    targetPort: web
  - name: reloader-web
    port: 8080
    targetPort: reloader-web
  selector:
    app.kubernetes.io/component: prometheus
    app.kubernetes.io/instance: k8s
    app.kubernetes.io/name: prometheus
    app.kubernetes.io/part-of: kube-prometheus
  sessionAffinity: ClientIP

# kubectl apply -f manifests/prometheus-service.yaml 
service/prometheus-k8s configured

root@s3:/apps/kube-prometheus# kubectl get svc -n monitoring
NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
alertmanager-main       ClusterIP   10.100.131.87    <none>        9093/TCP,8080/TCP               61m
alertmanager-operated   ClusterIP   None             <none>        9093/TCP,9094/TCP,9094/UDP      59m
blackbox-exporter       ClusterIP   10.100.226.3     <none>        9115/TCP,19115/TCP              61m
grafana                 ClusterIP   10.100.131.38    <none>        3000/TCP                        61m
kube-state-metrics      ClusterIP   None             <none>        8443/TCP,9443/TCP               61m
node-exporter           ClusterIP   None             <none>        9100/TCP                        61m
prometheus-adapter      ClusterIP   10.100.80.232    <none>        443/TCP                         61m
prometheus-k8s          NodePort    10.100.200.205   <none>        9090:30049/TCP,8080:30349/TCP   61m
prometheus-operated     ClusterIP   None             <none>        9090/TCP                        59m
prometheus-operator     ClusterIP   None             <none>        8443/TCP                        61m

```



### 2.2.4: 验证prometheus web界面：



![](/images/posts/11_prometheus/3.png)



### 2.2.5：通过NodePort访问Grafana:



```bash
# cat manifests/grafana-service.yaml 
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/component: grafana
    app.kubernetes.io/name: grafana
    app.kubernetes.io/part-of: kube-prometheus
    app.kubernetes.io/version: 8.5.5
  name: grafana
  namespace: monitoring
spec:
  type: NodePort
  ports:
  - name: http
    port: 3000
    targetPort: http
  selector:
    app.kubernetes.io/component: grafana
    app.kubernetes.io/name: grafana
    app.kubernetes.io/part-of: kube-prometheus
# kubectl apply -f manifests/grafana-service.yaml 
service/grafana configure
```



```
https://prometheus.io/download/ #官方二进制下载及安装，prometheus server的监听端口9090
https://prometheus.io/docs/prometheus/latest/installation/ #docker镜像直接启动
https://github.com/coreos/kube-prometheus #operator部署
```



### 2.2.6: 验证grafana web界面



![](/images/posts/11_prometheus/4.png)



## 2.2: 二进制部署Prometheus Server:



![](/images/posts/11_prometheus/5.png)



### 2.2.1: 解压二进制程序：

```
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

### 2.2.2：创建prometheus service启动脚本

```
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

### 2.2.3：启动prometheus服务：

```
root@prometheus-server:~# systemctl daemon-reload && systemctl restart prometheus && systemctl enable prometheus
```



### 2.2.4: 验证prometheus web界面：



![](/images/posts/11_prometheus/6.png)



### 2.2.5: 动态（热）加载配置:

```bash
vim /etc/systemd/system/prometheus.service
--web.enable-lifecycle

#systemctl daemon-reload
#systemctl restart prometheus.service
#curl -X POST http://IP:9090/-/reload
```



## 2.3: 二进制安装node-exporter:

k8s各node节点使用二进制或者daemonset方式安装node_exporter，用于收集各k8s node节点宿主机的监控指标数据，默认监听端口为9100



![](/images/posts/11_prometheus/7.png)



### 2.3.1: 解压二进制程序：

```
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



### 2.3.2: 创建node-exporter service启动文件

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



### 2.3.3: 启动node exporter服务：

```bash
root@prometheus-server2:/apps# systemctl daemon-reload && systemctl restart node-exporter && systemctl enable node-exporter.service
```



### 2.3.4： 验证Node exporter web界面



![](/images/posts/11_prometheus/8.png)



![](/images/posts/11_prometheus/9.png)



### 2.3.4：node-exporter指标数据：

```
root@prometheus-server2:/apps# curl 192.168.48.163:9100/metrics
node-export默认的监听端口为9100，可以使用浏览器或curl访问数据

常见的指标：
node_boot_time: 系统自启动以后的总结时间
node_cpu: 系统CPU使用量
nodedisk*: 磁盘IO
nodefilesystem*: 系统文件系统用量
node_loadl:系统cpu负载
nodememeory*:内存使用量
nodenetwork*: 网络带宽指标
node_time: 当前系统时间
go_*: node exporter中go相关指标
process_*: node exporter自身进程相关运行指标
```



## 2.4：配置prometheus server收集node-exporter指标数据：

### 2.4.1：prometheus默认配置文件：

```
该配置文件分为四个模块：global(全局配置）、alerting（告警配置）、rule_files（规则配置）、scrape_configs（目标拉取配置），本文将分别对其进行讲解介绍。

root@prometheus-server:~# vim /apps/prometheus/prometheus.yml 

# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute. #数据收集间隔时间，如果不配置默认为一分钟
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute. #用于指定检测告警规则的时间间隔，每15s重新检测告警规则，并对变更进行更新，如果不配置默认为一分钟
  # scrape_timeout is set to the global default (10s).


#alerting用于设置Prometheus与Alertmanager的通信，在Prometheus的整体架构中，Prometheus会根据配置的告警规则触发警报并发送到独立的Alertmanager组件，Alertmanager将对告警进行管理并发送给相关的用户。
# Alertmanager configuration
alerting:	#报警通知设置
  alertmanagers:
    - static_configs: #配置alertmanager的地址信息
        - targets:
          # - alertmanager:9093



# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:	#用于指定告警规则的文件路径，文件格式为yml
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:	#数据采集目标配置
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
    - targets: ["localhost:9090"]


```



### 2.4.2：添加node节点数据收集：

```
 root@prometheus-server:~# vim /apps/prometheus/prometheus.yml 
 - job_name: "promethues-node"
    static_configs:
      - targets: ["192.168.48.163:9100"]
      
```



### 2.4.3：重启并验证prometheus service状态：

```
 root@prometheus-server:~# systemctl restart prometheus.service
```



### 2.4.4：验证Prometheus server数据采集状态：



![](/images/posts/11_prometheus/10.png)



### 2.4.5：验证node数据：



![](/images/posts/11_prometheus/11.png)



## 2.5：Grafana展示监控数据



### 2.5.1 Grafana简介 

Grafana是一个开源的度量分析与可视化套件，它基于go语言开发。经常被用作基础设施的时间序列数

据和应用程序分析的可视化，应用场景非常多。

Grafana不仅仅支持很多类型的时序数据库数据源，比如Graphite、InfluxDB、Prometheus、

Elasticsearch等，虽然每种数据源都有独立的查询语言和能力，但是Grafana的查询编辑器支持所有的

数据源，而且能很好的支持每种数据源的特性。通过该查询编辑器可以对所有数据进行整合，而且还可

以在同一个dashboard上进行综合展示。

尤其是Grafana最具特色的就是各种各样漂亮的可视化界面，在Grafana提供了各种定制好的，可以直接

给各种软件直接使用的展示界面模板，

默认监听于TCP协议的3000端口，支持集成其他认证服务，且能够通过/metrics输出内建指标；

可以在 https://grafana.com/dashboards/ 页面查询到我们想要的各种dashboard模板。

https://grafana.com/ 官网



![](/images/posts/11_prometheus/12.png)



### 2.5.2：安装Grafana Server:

https://grafana.com/grafana/download/ #下载地址

https://grafana.com/docs/grafana/latest/installation/requirements/ #安装文档

```
部署要求：可以和prometheus server分离部署，只要网络通信正常即可
#sudo apt-get install -y adduser
root@prometheus-server:~# wget https://dl.grafana.com/enterprise/release/grafana-enterprise_8.3.7_amd64.deb
root@prometheus-server:~# dpkg -i grafana-enterprise_8.3.7_amd64.deb
```



### 2.5.3：grafana server配置文件：

```
root@prometheus-server:~# vim /etc/grafana/grafana.ini
[server]
# Protocol (http, https, h2, socket)
;protocol = http

# The ip address to bind to, empty will bind to all interfaces
;http_addr =

# The http port  to use
;http_port = 3000

```

### 2.5.4：启动grafana:

```
root@prometheus-server:~# systemctl restart grafana-server
root@prometheus-server:~# systemctl enable grafana-server

```

### 2.5.5: 验证grafana web界面：

#### 2.5.5.1：登录界面：



![](/images/posts/11_prometheus/13.png)



默认账户密码
admin

admin



#### 2.5.5.2: 添加prometheus数据源：



![](/images/posts/11_prometheus/14.png)



![](/images/posts/11_prometheus/15.png)





![](/images/posts/11_prometheus/16.png)



![](/images/posts/11_prometheus/17.png)



## 2.6：grafana导入模板：

https://grafana.com/grafana/dashboards/11074

### 2.6.1: 查找目的模板：



![](/images/posts/11_prometheus/18.png)



### 2.6.2：模板详细信息



![](/images/posts/11_prometheus/19.png)



### 2.6.3：模板导入方式：



![](/images/posts/11_prometheus/20.png)



### 2.6.4：grafana导入模板步骤

#### 2.6.4.1：点击import



![](/images/posts/11_prometheus/21.png)



#### 2.6.4.2: 输入模板ID：



![](/images/posts/11_prometheus/22.png)



#### 2.6.4.3：选择目的数据源：



![](/images/posts/11_prometheus/23.png)



#### 2.6.4.4：验证模板图形信息：



![](/images/posts/11_prometheus/24.png)



#### 2.6.4.5：插件管理

饼图插件未安装，需要提前安装

https://grafana.com/grafana/plugins/grafana-piechart-panel

```
在线安装
root@prometheus-server:/apps/prometheus# grafana-cli plugins install grafana-piechart-panel

离线安装:
#pwd
/var/lib/grafana/plugins
#unzip grafana-piechart-panel-v1.3.8.0-qa.zio
#mv grafana-piechar-panne-v1.3.8 grafana-piechart-panel
#systemctl restart grafana

```

# 三：PromQL语句简介：

https://prometheus.io/docs/prometheus/latest/querying/basics

Prometheus提供了一个函数式的表达语言PromQL(Prometheus Query Language)，可以使用用户实时地查找和聚合时间序列数据，表达式计算结果可以在图表中展示，也可以在Prometheus表达式浏览器中以表格形式展示，或者作为数据源，以HTTP API的方式提供给外部系统使用。

## 3.1：PromQL数据基础

### 3.1.1：PromQL查询数据类型：

https://prometheus.io/docs/prometheus/latest/querying/basics

```bash
瞬时向量、顺势数据（instant vector）:是对目标实例查询到同一个时间戳的一组时间序列数据（按照时间的推移对数据进行存储和展示），每个时间序列包含单个数据样本，比如node_memory_MemTotal_bytest查询当前剩余内存就是一个瞬时向量，该表达式的返回值中只会包含该时间序列中的最新一个样本值，而相应的这样的表达式称之为瞬时向量表达式，例如：prometheus_http_requests_total
prometheus API查询瞬时数据命令，在没有指定匹配条件的前提下，会返回所有包含此指标数据的实例数据：
#curl 'http://192.168.48.164:9090/api/v1/query' --data 'query=node_memory_MemFree_bytes' --data time=1662992450

查询指定实例的范围数据：
范维向量、范围数据（range vector）:是指在任何一个时间范围内，抓取的所有度量指标数据，比如最近一天的网卡流量趋势图
例如：prometheus_http_requests_total[5m]
# curl 'http://192.168.48.164:9090/api/v1/query' --data 'query=node_memory_MemFree_bytes{instance="192.168.48.165:9100"}[1m]' --data time=1662992450


标量、纯量数据（scaler）:是一个浮点数型的数据值，使用node_loadl获取到时一个瞬时向量，但是可用使用内置函数scalar（）将瞬时向量转换为标量，例如：scalar(sum(node_loadl))
# curl 'http://192.168.48.164:9090/api/v1/query' --data 'query=scalar(sum(node_load1{instance="192.168.48.165:9100"}))' --data time=1662992450


字符串（string）:字符串类型的数据，目前未使用
```



### 3.1.2：指标数据类型：

https://prometheus.io/docs/concepts/metric_types

#### 3.1.2.1：Counter:

Counter:计数器，Counter类型代表一个累积的指标数据，在没有被重置（如服务器重启，应用重启）的前提下只增不减，比如磁盘I/O总数、nginx的请求总数、网卡流经的报文总数等。



![](/images/posts/11_prometheus/25.png)



#### 3.1.2.2：Gauge:

Gauge:仪表盘，Gauge类型代表一个可以任意变化的指标数据，值可以随时增高或减少，如带宽速录、CPU负载、内存利用率、nginx活动连接数等



![](/images/posts/11_prometheus/26.png)



#### 3.1.2.3：Histogram:

```
Histogram:累计直方图，Histogram会在一段时间范围对数据进行采样（通常是请求持续时间或相应大小等），假如每分钟产生一个当前的活动连接数，那么一天24小时*60分支=1440分钟就会产生1440个数据，查看数据的每间隔的绘图跨度为2小时，那么2点的柱状图（bucket）会包含0到2点既两个小时的数据，而4点的柱状图（bucker）则会包含0点到4点的数据，而6点的柱状图（bucket）则会包含0点到6点的数据，可用于统计从当天零点开始到当前时间的数据统计结果，如http请求成功率、丢包率
```



#### 3.1.2.4: Summary:

```
Summary:摘要，也是一组数据，默认统计选中的指标数据的最近10分钟内的数据的分位数，可以指定数据统计范围。

分位数（Quantile），称为分点数，是只用分割点（cpu point）将随机数据统计并划分为几个具有相同概率的连续区间，常见的为四分位，四分位是将数据样本统计后分为四个区间，将范围内的数据进行百分比的占比统计，从0到1表示0%-100%，（0%~25%，%25-50%，50%~75%，75~100%），利用四分数，可以快速了解数据的大概统计结果

如下统计的是0、0.25、0.5、0.75的数据量分别是多少
go_gc_duration_seconds
# HELP go_gc_duration_seconds A summary of the pause duration of garbage collection cycles.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 2.9706e-05
go_gc_duration_seconds{quantile="0.25"} 3.7571e-05 #25%的go_gc_duration_seconds持续时间
go_gc_duration_seconds{quantile="0.5"} 4.781e-05 #50%的go_gc_duration_seconds持续时间
go_gc_duration_seconds{quantile="0.75"} 8.6986e-05%75的go_gc_duration_seconds持续时间
go_gc_duration_seconds{quantile="1"} 0.000572646 %100的go_gc_duration_seconds持续时间
go_gc_duration_seconds_sum 0.085983167 #数据总和
go_gc_duration_seconds_count 1148 #数据个数
```



![](/images/posts/11_prometheus/27.png)



## 3.2：PromQL-指标数据格式简介：

### 3.2.1：指标数据格式：

prometheus指标数据格式为：

```bash
#没有标签的
metric_name metric_value
#TYPE node_load15 gauge
node_load15 0.1

#一个标签的
metric_name{labell_name="labell-value"} metric_value
#TYPE node_network_receive_bytes_total counter
node_network_receive_bytes_total{device="eth0"} 1.44096e+0.7

#多个标签的
metric_name{label_name="labell-value","label_name="label-varl"} metric_value
#Type node_filesystem_files_free gauge
node_filesystem_files_free{device="/dev/sda2",fstype="xfs",mountpoint="/boot"} 523984
```



### 3.2.2: 指标数据格示例：

```bash
node_memory_MemTotal_bytes #查询node节点总内存大小
node_memory_MemFree_bytes	#查询node节点剩余可用内存
node_memory_MemTotal_bytes{instance="192.168.48.163:9100"} #基于标签查询指定节点的总内存
node_memory_MemFree_bytes{instance="192.168.48.163:9100"}#基于标签查询指定节点的可用内存

node_disk_io_time_seconds_total{device="sda"} #查询指定磁盘的每秒磁盘io
node_filesystem_free_bytes{device="/dev/sda1", fstype="ext4",mountpoint="/"} #查看指定磁盘的磁盘剩余空间

# HELP node_load1 1m load average. #CPU负载
# TYPE node_load1 gauge
node_load1 1
# HELP node_load15 15m load average.
# TYPE node_load15 gauge
node_load15 1
# HELP node_load5 5m load average.
# TYPE node_load5 gauge
node_load5 1
```



## 3.3：PromQL-标签匹配：

```bash
= :选择与提供的字符串完全相同的标签，精确匹配
！= :选择与提供的字符串不相同的标签，去反。
=~ :选择正则表达式与提供的字符串（或子字符串）相匹配的标签。
！~ :选择正则表达式于提供的字符串（或子字符串）不匹配的标签

#查询格式<metric name>{<label name>=<label value>,...}
node_load1{instance="192.168.48.163:9100"}
node_load1{job="promethues-node"}

node_load1{instance="192.168.48.163:9100", job="promethues-node"} #精确匹配
node_load1{instance!="192.168.48.163:9100", job="promethues-node"} #取反

node_load1{instance=~"192.168.48.*:9100"} #包含正则匹配
node_load1{instance!~"192.168.48.*:9100"} #包含正则取反
```

![](/images/posts/11_prometheus/28.png)



## 3.4：PromQL-时间范围：

```bash
s - 秒
m - 分钟
h - 小时
d - 天
w - 周
y - 年

#瞬时向量表达式，选择当前最新的数据
node_memory_MemTotal_bytes{}

#区间向量表达式，选择以当前时间为准，查询所有节点node_memory_MemTotal_bytes指标5分钟内的数据
node_memory_MemTotal_bytes{}[5m]

#区间向量表达式，选择以当前时间为基准，查询指定节点node_memory_MemTotal_bytes指标5分钟内的数据
node_memory_MemTotal_bytes{instance="192.168.48.163:9100"}[5m]
```



![](/images/posts/11_prometheus/29.png)



## 3.5：PromQL-运算符：

```bash
+ 加法
- 减法
* 乘法
/ 除法
& 模
^ 冥等

node_memory_MemTotal_bytes/1042/1024 #将内存进行单位从字节转行为兆
node_disk_read_bytes_total{device="sda"} + node_disk_written_bytes_total{device="sda"} #计算磁盘读写数据量
(node_disk_read_bytes_total{device="sda"} + node_disk_written_bytes_total{device="sda"}) / 1024 / 1024 #单位转换
```



![](/images/posts/11_prometheus/30.png)



## 3.6：PromQL-聚合运算

### 3.6.1：max、min、avg:

```bash
max() #最大值
min() #最小值
avg() #平均值

计算每个节点的最大的流量值
max(node_network_receive_bytes_total) by (instance)

计算每个节点最近5分钟每个device的最大流量
max(rate(node_network_receive_bytes_total[5m])) by (device)
```



![](/images/posts/11_prometheus/31.png)



### 3.6.2：sum、count:

```bash
sum() #求数据值相加的和（总数）
sum(prometheus_http_requests_total)

{} 1623 #最近总共请求数1623，用于计算返回值的总数（如http请求次数）

count() #统计返回值的条数
count(node_os_version)
{} 1 #一共一条返回的数据，可以用于统计节点数、pod数量等

count_values() #对value的个数（行数）进行计数
count_values("node_version",node_os_version)#统计不同的系统版本节点有多少

{node_version="18.04"} 1
```



![](/images/posts/11_prometheus/32.png)





![](/images/posts/11_prometheus/33.png)



### 3.6.3：abs、absent:

```bash
abs() #返回指标数据的值
abs(sum(prometheus_http_requests_total{handler="/metrics"}))

absent() #如果指标有数据就返回空，如果监控项没有数据就返回1，可用于对监控项设置告警通知

```



![](/images/posts/11_prometheus/34.png)



### 3.6.4：stddev、stdvar

```bash
stddev() #标准差
	stddev(prometheus_http_requests_total) #5+5=10,1+9=10,1+9这一组的数据差异就大，在相同是数据波动较大，不稳定
	
stdvar() #求方差	
	stdvar(prometheus_http_requests_total)
```



![](/images/posts/11_prometheus/35.png)





### 3.6.5：topk、bottomk:

```bash
topk()	#样本值排名最大的N个数据
	#取从大到小的前6个
	topk(6, prometheus_http_requests_total)
	
bottomk() #样本值排名最小的N个数据
	#取从小到大的前6个
	bottomk(6, prometheus_http_requests_total)
```



![](/images/posts/11_prometheus/36.png)



![](/images/posts/11_prometheus/37.png)



### 3.6.6：rate、irate:

```bash
rate() #函数专门搭配counter数据类型使用函数，rate会取指定时间范围内所有数据点，算出一组速率，然后取平均值作为结果，适合用于计算数据相对平稳的数据

	rate(prometheus_http_requests_total[5m])
	rate(node_network_receive_bytes_total[5m])
	
irate() 函数是专门搭配counter数据类型使用函数，irate取得是在指定时间范围内得最近两个数据点来算速率，适合计算变化较大得数据，显示得数据相对比较准确，所有官方文档说：irate适合快速变化得计数器（counter），而rate适合缓慢变化得计数器（counter）
	irate(prometheus_http_requests_total[5m])
	irate(node_network_receive_bytes_total[5m])
```



![](/images/posts/11_prometheus/38.png)





![](/images/posts/11_prometheus/39.png)



### 3.6.7: by、without:

```bash
#by 在计算结果中，只保留by指定的标签的值，并移除其它所有的
sum(rate(node_network_receive_packets_total{instance=~".*"}[5m])) by (instance)
sum
sum(rate(node_memory_MemFree_bytes[5m])) by (instance)

#without 从计算结果中移除列举的instance,job标签,保留其它标签
sum(rate(node_memory_MemFree_bytes[5m])) without (instance)
```



![](/images/posts/11_prometheus/40.png)





# 四：cadvisor监控Pod、daemonset部署node-exporter、deployment部署prometheus server:

监控Pod指标数据需要使用cadvisor，cadvisor由谷歌开源,在Kubernetes v1.11及之前得版本内置在kubelet中并监听在4194端口（http://github.com/kubernetes/kubernetes/pull/65707）从v1.12开始kubelet中得 cadvisor被移除，因此需要通过daemonset等方式部署

cadvisor(容器顾问)不仅可以搜集一台机器上所有运行得容器信息，还提供基础查询界面和http接口，方便其它组件如prometheus进行数据抓取，cadvisor开源对节点机器上得容器进行实时监控和性能采集，包括容器得CPU使用情况，内存使用情况，网络吞吐量及文件系统使用情况

https://github.com/google/cadvisor

## 4.1：cadvisor部署方式1-docker命令：

### 4.1.1：cadvisor镜像准备：

```bash
# docker pull zcube/cadvisor:v0.39.3
v0.39.3: Pulling from zcube/cadvisor
530afca65e2e: Pull complete 
4aee20e58856: Pull complete 
34343cfb6977: Pull complete 
4f4fb700ef54: Pull complete 
4c4049926032: Pull complete 
Digest: sha256:51646477464e61a6b1f9f32a9af3a7dc6e1b066eaaec204ea295c01d83fef647
Status: Downloaded newer image for zcube/cadvisor:v0.39.3
docker.io/zcube/cadvisor:v0.39.3
```



### 4.1.2: 启动cadvisor容器：

```bash
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --volume=/dev/disk/:/dev/disk:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  --privileged=true \
  --device=/dev/kmsg \
  zcube/cadvisor:v0.39.3
```

#### 4.1.3: 验证cadvisor web界面：

访问cadvisor监听端口：http://192.168.48.164:8080/



![](/images/posts/11_prometheus/41.png)





![](/images/posts/11_prometheus/42.png)





### 4.1.4: cadvisor指标数据:

```bash
指标名称	                              类型	                    含义
container_cpu_load_average_10s	       gauge           	过去10秒容器CPU的平均负载
container_cpu_usage_seconds_total	   counter 	        容器在每个CPU内核上的累积占用时间 (单位：秒)
container_cpu_system_seconds_total	   counter 	        System CPU累积占用时间（单位：秒）
container_cpu_user_seconds_total	   counter 	        User CPU累积占用时间（单位：秒）
container_fs_usage_bytes	           gauge           	容器中文件系统的使用量(单位：字节)
container_fs_limit_bytes	           gauge           	容器可以使用的文件系统总量(单位：字节)
container_fs_reads_bytes_total	       counter 	        容器累积读取数据的总量(单位：字节)
container_fs_writes_bytes_total  	   counter          容器累积写入数据的总量(单位：字节)
container_memory_max_usage_bytes	   gauge           	容器的最大内存使用量（单位：字节）
container_memory_usage_bytes	       gauge           	容器当前的内存使用量（单位：字节）
container_spec_memory_limit_bytes	   gauge           	容器的内存使用量限制
machine_memory_bytes 	               gauge           	当前主机的内存总量
container_network_receive_bytes_total  counter 	        容器网络累积接收数据总量（单位：字节）
container_network_transmit_bytes_total  counter 	    容器网络累积传输数据总量（单位：字节）



当能够正常采集到cAdvisor的样本数据后，可以通过以下表达式计算容器的CPU使用率：

（1）sum(irate(container_cpu_usage_seconds_total{image!=""}[1m])) without (cpu)
容器CPU使用率

（2）container_memory_usage_bytes{image!=""}
查询容器内存使用量（单位：字节）:

（3）sum(rate(container_network_receive_bytes_total{image!=""}[1m])) without (interface)
查询容器网络接收量（速率）（单位：字节/秒）：

（4）sum(rate(container_network_transmit_bytes_total{image!=""}[1m])) without (interface)
容器网络传输量 字节/秒

（5）sum(rate(container_fs_reads_bytes_total{image!=""}[1m])) without (device)
容器文件系统读取速率 字节/秒

（6）sum(rate(container_fs_writes_bytes_total{image!=""}[1m])) without (device)
容器文件系统写入速率 字节/秒

cadvisor 常用容器监控指标
（1）网络流量
sum(rate(container_network_receive_bytes_total{name=~".+"}[1m])) by (name)
##容器网络接收的字节数（1分钟内），根据名称查询 name=~".+"

sum(rate(container_network_transmit_bytes_total{name=~".+"}[1m])) by (name)
##容器网络传输的字节数（1分钟内），根据名称查询 name=~".+"


（2）容器 CPU相关
sum(rate(container_cpu_system_seconds_total[1m]))
###所用容器system cpu的累计使用时间（1min钟内）

sum(irate(container_cpu_system_seconds_total{image!=""}[1m])) without (cpu)
###每个容器system cpu的使用时间（1min钟内）


sum(rate(container_cpu_usage_seconds_total{name=~".+"}[1m])) by (name) * 100
#每个容器的cpu使用率


sum(sum(rate(container_cpu_usage_seconds_total{name=~".+"}[1m])) by (name) * 100)
#总容器的cpu使用率
```

### 4.1.5： prometheus采集cadvisor数据：

```bash
#vim /usr/local/prometheus/prometheus.yml
  - job_name: "prometheus-containers"
    static_configs:
      - targets: ["192.168.48.169:8080"]
## systemctl restart prometheus.service
```



### 4.1.6: 验证prometheus数据：



![](/images/posts/11_prometheus/43.png)



### 4.1.7： grafana添加pod监控模板：

```bash
14282 #模板ID
```



![](/images/posts/11_prometheus/44.png)



## 4.2: cadvisor部署方式2-通过daemonset部署：

### 4.2.1：删除使用docker命令创建得cadvisor:

```bash
# docker rm -fv cadvisor
```



### 4.2.1: 通过daemonset部署cadvisor:

```bash
# kubectl create ns monitor
# cat case1-daemonset-deploy-cadvisor.yaml 
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: cadvisor
  namespace: monitor
spec:
  selector:
    matchLabels:
      app: cAdvisor
  template:
    metadata:
      labels:
        app: cAdvisor
    spec:
      tolerations:    #污点容忍,忽略master的NoSchedule
        - effect: NoSchedule
          key: node-role.kubernetes.io/master
      hostNetwork: true
      restartPolicy: Always   # 重启策略
      containers:
      - name: cadvisor
        image: harbor.magedu.net/baseimages/cadvisor:v0.39.2
        imagePullPolicy: IfNotPresent  # 镜像策略
        ports:
        - containerPort: 8080
        volumeMounts:
          - name: root
            mountPath: /rootfs
          - name: run
            mountPath: /var/run
          - name: sys
            mountPath: /sys
          - name: docker
            mountPath: /var/lib/docker
      volumes:
      - name: root
        hostPath:
          path: /
      - name: run
        hostPath:
          path: /var/run
      - name: sys
        hostPath:
          path: /sys
      - name: docker
        hostPath:
          path: /var/lib/docker

# kubectl apply -f case1-daemonset-deploy-cadvisor.yaml 
daemonset.apps/cadvisor created


root@s3:/data/case# kubectl get po -n monitor
NAME             READY   STATUS    RESTARTS   AGE
cadvisor-9qxch   1/1     Running   0          41s
cadvisor-lqh95   1/1     Running   0          41s
cadvisor-q4t5l   1/1     Running   0          41s
```



### 4.2.3: 验证web界面及指标数据：



![](/images/posts/11_prometheus/45.png)





![](/images/posts/11_prometheus/46.png)



## 4.3：daemonset部署node-exporter:

### 4.3.1: 部署node-exporter:

```bash
# cat case2-daemonset-deploy-node-exporter.yaml 
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
  namespace: monitor
  labels:
    k8s-app: node-exporter
spec:
  selector:
    matchLabels:
        k8s-app: node-exporter
  template:
    metadata:
      labels:
        k8s-app: node-exporter
    spec:
      tolerations:
        - effect: NoSchedule
          key: node-role.kubernetes.io/master
      containers:
      - image: prom/node-exporter:v1.3.1 
        imagePullPolicy: IfNotPresent
        name: prometheus-node-exporter
        ports:
        - containerPort: 9100
          hostPort: 9100
          protocol: TCP
          name: metrics
        volumeMounts:
        - mountPath: /host/proc
          name: proc
        - mountPath: /host/sys
          name: sys
        - mountPath: /host
          name: rootfs
        args:
        - --path.procfs=/host/proc
        - --path.sysfs=/host/sys
        - --path.rootfs=/host
      volumes:
        - name: proc
          hostPath:
            path: /proc
        - name: sys
          hostPath:
            path: /sys
        - name: rootfs
          hostPath:
            path: /
      hostNetwork: true
      hostPID: true
---
apiVersion: v1
kind: Service
metadata:
  annotations:
    prometheus.io/scrape: "true"
  labels:
    k8s-app: node-exporter
  name: node-exporter
  namespace: monitor
spec:
  type: NodePort
  ports:
  - name: http
    port: 9100
    nodePort: 30009
    protocol: TCP
  selector:
    k8s-app: node-exporter


# kubectl apply -f case2-daemonset-deploy-node-exporter.yaml 
daemonset.apps/node-exporter unchanged
```



### 4.3.2: 验证pod:

```bash
# kubectl get po -n monitor
NAME                  READY   STATUS    RESTARTS   AGE
cadvisor-9qxch        1/1     Running   0          30m
cadvisor-lqh95        1/1     Running   0          30m
cadvisor-q4t5l        1/1     Running   0          30m
node-exporter-p2b4x   1/1     Running   0          22m
node-exporter-pm5tp   1/1     Running   0          22m
node-exporter-zmrvz   1/1     Running   0          22m

```



### 4.3.3: 验证node-exporter指标 数据：



![](/images/posts/11_prometheus/47.png)



## 4.4：deployment部署prometheus server:

### 4.4.1：部署Prometheus server:

```bash
创建configmap:
# cat case3-1-prometheus-cfg.yaml 
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
    app: prometheus
  name: prometheus-config
  namespace: monitor 
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      scrape_timeout: 10s
      evaluation_interval: 1m
    scrape_configs:
    - job_name: 'kubernetes-node'
      kubernetes_sd_configs:
      - role: node
      relabel_configs:
      - source_labels: [__address__]
        regex: '(.*):10250'
        replacement: '${1}:9100'
        target_label: __address__
        action: replace
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
    - job_name: 'kubernetes-node-cadvisor'
      kubernetes_sd_configs:
      - role:  node
      scheme: https
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      relabel_configs:
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
      - target_label: __address__
        replacement: kubernetes.default.svc:443
      - source_labels: [__meta_kubernetes_node_name]
        regex: (.+)
        target_label: __metrics_path__
        replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor

    - job_name: 'kubernetes-service-endpoints'
      kubernetes_sd_configs:
      - role: endpoints
      relabel_configs:
      - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
        action: keep
        regex: true
      - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
        action: replace
        target_label: __scheme__
        regex: (https?)
      - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
        action: replace
        target_label: __metrics_path__
        regex: (.+)
      - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
        action: replace
        target_label: __address__
        regex: ([^:]+)(?::\d+)?;(\d+)
        replacement: $1:$2
      - action: labelmap
        regex: __meta_kubernetes_service_label_(.+)
      - source_labels: [__meta_kubernetes_namespace]
        action: replace
        target_label: kubernetes_namespace
      - source_labels: [__meta_kubernetes_service_name]
        action: replace
        target_label: kubernetes_name



    - job_name: 'kubernetes-apiserver'
      kubernetes_sd_configs:
      - role: endpoints
      scheme: https
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: default;kubernetes;https
        
        
# kubectl apply -f case3-1-prometheus-cfg.yaml 
configmap/prometheus-config created




#创建监控账户
# kubectl create serviceaccount monitor -n monitor
serviceaccount/monitor created

#对monitor账户授权
# kubectl create clusterrolebinding monitor-clusterrolebinding -n monitor --clusterrole=cluster-admin --serviceaccount=monitor:monitor
clusterrolebinding.rbac.authorization.k8s.io/monitor-clusterrolebinding created

#将prometheus运行在192.168.48.170node节点上，提前准备数据目录并授权
# mkdir -p /data/prometheusdata
# chmod 777 /data/prometheusdata/
# chown 65534.65534 /data/prometheusdata -R

# cat case3-2-prometheus-deployment.yaml 
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-server
  namespace: monitor
  labels:
    app: prometheus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
      component: server
  template:
    metadata:
      labels:
        app: prometheus
        component: server
      annotations:
        prometheus.io/scrape: 'false'
    spec:
      nodeName: 192.168.48.170
      serviceAccountName: monitor
      containers:
      - name: prometheus
        image: prom/prometheus:v2.31.2
        imagePullPolicy: IfNotPresent
        command:
          - prometheus
          - --config.file=/etc/prometheus/prometheus.yml
          - --storage.tsdb.path=/prometheus
          - --storage.tsdb.retention=720h
        ports:
        - containerPort: 9090
          protocol: TCP
        volumeMounts:
        - mountPath: /etc/prometheus/prometheus.yml
          name: prometheus-config
          subPath: prometheus.yml
        - mountPath: /prometheus/
          name: prometheus-storage-volume
      volumes:
        - name: prometheus-config
          configMap:
            name: prometheus-config
            items:
              - key: prometheus.yml
                path: prometheus.yml
                mode: 0644
        - name: prometheus-storage-volume
          hostPath:
           path: /data/prometheusdata
           type: Directory


# kubectl apply -f  case3-2-prometheus-deployment.yaml 
deployment.apps/prometheus-server created


验证pod
# kubectl get po -n monitor
NAME                                 READY   STATUS    RESTARTS   AGE
cadvisor-9qxch                       1/1     Running   0          62m
cadvisor-lqh95                       1/1     Running   0          62m
cadvisor-q4t5l                       1/1     Running   0          62m
node-exporter-p2b4x                  1/1     Running   0          54m
node-exporter-pm5tp                  1/1     Running   0          54m
node-exporter-zmrvz                  1/1     Running   0          54m
prometheus-server-5c6f65598d-hcljz   1/1     Running   0          18s

创建service
#

# cat case3-3-prometheus-svc.yaml 
---
apiVersion: v1
kind: Service
metadata:
  name: prometheus
  namespace: monitor
  labels:
    app: prometheus
spec:
  type: NodePort
  ports:
    - port: 9090
      targetPort: 9090
      nodePort: 30090
      protocol: TCP
  selector:
    app: prometheus
    component: server
# kubectl apply -f case3-3-prometheus-svc.yaml 
service/prometheus created
```



### 4.4.2: 验证prometheus server界面：

![](/images/posts/11_prometheus/48.png)



# 五：prometheus的服务发现机制：

prometheus默认是采用Pull方式拉取监控数据，也就是定时去目标主机上抓取metics数据，每一个被抓取的目标需要暴漏一个HTTP接口，prometheus通过这个暴漏的接口就可以获取到相应的指标数据，这种方式需要由目标服务决定采集的目标有哪些，通过配置在scrape_configs中的各种job来实现，无法动态感知新服务，如果后面增加了节点或者组件，就得手动休改prometheus配置，并重启prometheus，很不方便，所有出现了动态服务发现，动态服务发现能够自动发现集群中的新端点， 并加入到配置中，通过服务发现，prometheus能查询到需要监控的Target列表，然后轮询这些Target获取监控数据。

prometheus获取数据源target的方式有多种，如静态配置和动态服务发现配置，prometheus目前支持的服务发现有很多种，常用的主要分为以下几种：

https://prometheus.io/docs/prometheus/latest/configuration/configuration/#configuration-file

```bash
kubernetes_sd_configs: 基于kubernetes API实现的服务发现，让prometheus动态发现kubernetes中被监控的目标
static_configs: 静态服务发现，基于prometheus配置文件指定的监控目标
dns_sd_configs:DNS服务发现监控目标
consul_sd_configs: Consul服务发现，基于consul服务动态发现监控目标
file_sd_configs: 基于指定的文件实现服务发现，基于指定的文件发现监控目标
```

prometheus的静态服务发现static_configs，每当有一个新的目标实例需要监控，都需要手动修改配置文件配置目标target

prometheus的consul服务发现consul_sd_configs：Prometheus一直监视consul服务，当发现在consul中注册的服务有变化，prometheus就会自动监控到所有注册到consul中的目标资源

prometheus的k8s服务发现kubernetes_sd_configs：prometheus与kuernetes的API进行交互，动态的发现kubernetes中部署的所有监控的目标资源

## 5.2：relabeling简介及kubernetes_sd_configs:

https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config

```bash
prometheus的relabeling(重新修改标签)功能很强大，它能够在抓取到目标实例之前把目标实例的元数据标签动态重新修改，动态添加或者覆盖标签
prometheus从kubernetes API动态发现目标（target）之后，在被发现的target实例中，都包含一些原始的Metadata标签信息，默认的标签有：
_address_: 以<host>:<port>格式显示目标targets的地址
_scheme_:采集的目标服务地址的Scheme形式，HTTP或者HTTPS
_metrics_path_: 采集的目标服务的访问路径
```



![](/images/posts/11_prometheus/49.png)



### 5.1.1：基础功能-重新标记目的：

为了更好的识别监控指标，便于后期调用数据绘图、告警等需求，prometheus支持对发现的目标进行label修改，在两个阶段可以重新标记：

```bash
relabel_configs: 在对targer进行数据采集之前（比如在采集数据之前定义标签信息，如目的IP、目的端口等信息），可以使用relabel_configs添加，修改或删除一些标签，也可以只采集特定目标或过滤目标
metric_relabel_configs: 在对targer进行数据采集之后，既如果是已经抓取到指标数据时，可以使用metric_relabel_configs做之后的重新标记和过滤
```



![](/images/posts/11_prometheus/50.png)



```bash
#静态配置：
- job_name: "prometheus-node"
    static_configs:
      - targets: ["192.168.48.165:9100"]

基于API Server的动态发现：
  - job_name: "kubernetes-apiserver" #jon名称
      kubernetes_sd_configs: #基于kubernetes_sd_configs实现服务发现
      - role: endpoints #发现endpoints
      scheme: https #当前job使用的发现协议
      tls_config: #证书配置
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt #容器里的证书路径
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token #容器里的token路径
        relabel_configs: #重新re修改标签label配置configs
        - source_labels: [_meta_kubernetes_namespace, meta_kubernetes_service_name, _meta_kubernetes_endpoint_port_name]#源标签，既对哪些标签进行操作
        action: keep #action 定义了relabel的具体动作，action支持多种
        regex: default;kubernetes;https #指定匹配条件，只发现default名命空间的kubernetes服务后面的endpoint并且时https协议

```

### 5.1.2：label详解：

```bash
source_labels: 源标签，没有经过relabel处理之前的标签名字
target_label: 通过action处理之后的新的标签名称
regex: 给定的值或正则表达式匹配，匹配源标签的值,默认是（.*）
replacement: 通过分组替换后标签（target_label）队形的/()/() $1:$2
```

### 5.1.3: action详解：

https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config

```bash
replace: 替换标签值，根据regex正则匹配到源标签的值，使用replacement来引用表达式匹配的分组
keep: 满足regex正则条件的实例进行采集，把source_labels中没有匹配到regex正则内容的Target实例丢掉，既之采集匹配成功的实例。
drop: 满足regex正则条件的实例不采集，把source_labels中匹配到regex正则内容的Target实例丢掉，既之采集没有匹配到的实例。

hashmod: 使用hashmod计算source_labels的hash值进行对比，基于自定义的模数取模，以实现对目标进行分类、重新赋值等功能：

scrape_configs:
  - job_nameL: ip_job
    relabel_configs:
    - source_labels: [_address_]
        modules: 4
        targer_label: _ip_hash
        action: hashmod
     - source_labels: [_ip_hash]
        regex: ^1$
        action: keep
labelmap: 匹配regex所有标签名称，然后复制匹配标签的值进行分组，可以通过replacement分组引用（$(),$(),...）替代

labelkeep: 匹配regex所有标签名称，其它不匹配的标签都将从标签集中删除
labeldrop: 匹配regex所有标签名称，其它匹配的标签都将从标签集中删除
```



### 5.1.4： 测试label功能



```bash
# vim case3-1-prometheus-cfg.yaml
      #     relabel_configs:
      # - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
      # action: keep
      #  regex: default;kubernetes;https
                     


# kubectl apply -f case3-1-prometheus-cfg.yaml 
configmap/prometheus-config configured
# kubectl delete -f case3-2-prometheus-deployment.yaml 
deployment.apps "prometheus-server" deleted
# kubectl apply -f case3-2-prometheus-deployment.yaml 
deployment.apps/prometheus-server created
```



如下图：

好多Down的，是因为做服务发现的时候没有进行过滤，pod也被匹配成功并进行监控，但是pod并没有提供metrics指标数据，所以不通：



![](/images/posts/11_prometheus/51.png)



### 5.1.5：查看peometheus server容器证书：



```bash
# kubectl get po -n monitor
# kubectl exec -it prometheus-server-5c6f65598d-c22g7 ls /var/run/secrets/kubernetes.io/serviceaccount/ -n monitor
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
ca.crt     namespace  token

#对比容器的ca.crt与宿主机/etc/kubernetes/ssl/ca.pem文件md5值是一样的

# kubectl exec -it prometheus-server-5c6f65598d-c22g7 md5sum /var/run/secrets/kubernetes.io/serviceaccount/ca.crt -n monitor
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
aba140e477a1f8ff163b746ccfe6042a  /var/run/secrets/kubernetes.io/serviceaccount/ca.crt


# md5sum /etc/kubernetes/ssl/ca.pem 
aba140e477a1f8ff163b746ccfe6042a  /etc/kubernetes/ssl/ca.pem

```



### 5.1.6：支持的发现目标类型：

发现类型可以配置以下类型之一来发现目标：

https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config



```bash
node #node节点
service #发现service
pod#发现pod
endpoints #基于svc发现endpoints(pod)
Endpointslice #对endpoint进行切片
ingress#发现ingress
```

### 5.1.7: api-server的服务器发现

apiserver作为Kubernetes最核心的组件，它的监控也是非常有必要的，对于apiserver的监控，我们可以直接通过kubernetes的service来获取

```bash
# kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   106d
root@s2:/data/prometheusdata# kubectl get ep
NAME         ENDPOINTS             AGE
kubernetes   192.168.48.166:6443   106d

# vim case3-1-prometheus-cfg.yaml 
    - job_name: 'kubernetes-apiserver'
      kubernetes_sd_configs:
      - role: endpoints
      scheme: https
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: default;kubernetes;https#含义为匹配default的namespace,svc名称是kubernetes并且协议是Https，匹配成功后进行保留，并且把regex作为source_labels相对应的值，既labels为key、regex为值。
label替换方式如下：
__meta_kubernetes_namespace=default,__meta_kubernetes_service_name=kubernetes,__meta_kubernetes_endpoint_port_name=https
最终，匹配到api-server的地址：
# kubectl get ep
NAME         ENDPOINTS             AGE
kubernetes   192.168.48.166:6443   106d
```



![](/images/posts/11_prometheus/52.png)





### 5.1.8：api-server指标数据：

Apiserver组件是看集群的入口，所以请求都是从apiserver进来的，所以对apiserver指标做监控可以用来判断集群的监控状况

#### 5.1.8.1：apiserver_request_total:

以下promQL语句查询apiserver最近一分钟不同方法的请求数量统计：

```bash
sum(rate(apiserver_request_total[10m])) by (resource,subresource,verb)
```



![](/images/posts/11_prometheus/53.png)



#### 5.1.8.2: 关于annotation_prometheus_io_scrape:

在k8s中，基于prometheus的发现规则，需要在被发现的目的target定义注解匹配annotation_prometheus_io_scrape=true，且必须匹配成功该注解才会保留监控target，然后在进行数据抓取并进行标签替换，如annotation_prometheus_io_scheme标签为http或https:

```bash
    - job_name: 'kubernetes-service-endpoints' #job名称
      kubernetes_sd_configs: #sd_configs发现
      - role: endpoints #角色 endpoints
      relabel_configs: #标签重写配置
#annotation_prometheus_io_scrape的值为true，保留标签然后在向下执行   
      - source_labels:     [__meta_kubernetes_service_annotation_prometheus_io_scrape]
        action: keep
        regex: true
        
 #将__meta_kubernetes_service_annotation_prometheus_io_scheme修改为__schemem_
      - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
        action: replace
        target_label: __scheme__
        regex: (https?)#正则匹配协议http或https(?匹配前面的字符0次或一次)，既其它协议不替换
 #将__meta_kubernetes_service_annotation_prometheus_io_pat替换为__metrics_path-
      - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
        action: replace
        target_label: __metrics_path__
        regex: (.+)#路径为1到任意长度（.为匹配除\n之外的任意单个字符，+为匹配一次或多次）
        
  #地址发现及标签重写
      - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
        action: replace
        target_label: __address__
        regex: ([^:]+)(?::\d+)?;(\d+)
        replacement: $1:$2#格式为地址：端口
#匹配regex所匹配的标签，然后进行应用：
     - action: labelmap
        regex: __meta_kubernetes_service_label_(.+)#通过正则匹配名称
      - source_labels: [__meta_kubernetes_namespace]


1） prometheus. io/ scrape： 用于 标识 是否 需要 被 采集 指标 数据， 布尔 型 值， true 或 false。
2） prometheus. io/ path： 抓取 指标 数据 时 使用 的 URL 路径， 一般 为/ metrics。
3） prometheus. io/ port： 抓取 指标 数据 时 使 用的 套 接 字 端口， 如 8080。

匹配之前的数据：
```

![](/images/posts/11_prometheus/54.png)



```bash
 #将__meta_kubernetes_namespace替换为kubernetes_namespace
    - source_labels: [__meta_kubernetes_namespace]
        action: replace
        target_label: kubernetes_namespace
        
 #将__meta_kubernetes_service_name替换为kubernetes_name
      - source_labels: [__meta_kubernetes_service_name]
        action: replace
        target_label: kubernetes_name

```



### 5.1.9: kube-dns的服务发现

#### 5.1.9.1： 查看kube-dns状态：

```bash
# kubectl describe svc kube-dns -n kube-system
Name:              kube-dns
Namespace:         kube-system
Labels:            addonmanager.kubernetes.io/mode=Reconcile
                   k8s-app=kube-dns
                   kubernetes.io/cluster-service=true
                   kubernetes.io/name=CoreDNS
Annotations:       prometheus.io/port: 9153 #注解标签，用于prometheus匹配发现端口
                   prometheus.io/scrape: true #注解标签，用于prometheus匹配抓取数据
Selector:          k8s-app=kube-dns
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.100.0.2
IPs:               10.100.0.2
Port:              dns  53/UDP
TargetPort:        53/UDP
Endpoints:         10.200.152.193:53,10.200.152.194:53,10.200.78.138:53 + 1 more...
Port:              dns-tcp  53/TCP
TargetPort:        53/TCP
Endpoints:         10.200.152.193:53,10.200.152.194:53,10.200.78.138:53 + 1 more...
Port:              metrics  9153/TCP
TargetPort:        9153/TCP
Endpoints:         10.200.152.193:9153,10.200.152.194:9153,10.200.78.138:9153 + 1 more...
Session Affinity:  None
Events:            <none>



     #kubernetes-coredns服务发现
    - job_name: "kubernetes-coredns"
      kubernetes_sd_configs:
      - role: endpoints
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: kube-system;kube-dns;metrics
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_scrape"]	#
        regex: true
        action: keep
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_scheme"]
        regex: (https?)
        action: replace
        target_label: __scheme__
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_path"]
        regex: (.+)
        action: replace
        target_label: __metrics_path__


```

#### 5.1.9.2：修改副本数验证发现：

新添加一个node节点或修改deployment控制器的副本数，以上endpoint数量发生变化，能否自动发现新添加的pod:

```bash
# kubectl edit deploy coredns -n kube-system
4
```



![](/images/posts/11_prometheus/55.png)



#### 5.1.9.3: grafana导入coredns模板：

14981

![](/images/posts/11_prometheus/56.png)



### 5.1.10：node节点发现及指标：

#### 5.1.10.1：配置详解：

```bash
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
    app: prometheus
  name: prometheus-config
  namespace: monitor
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      scrape_timeout: 10s
      evaluation_interval: 1m
    scrape_configs:
    - job_name: 'kubernetes-node' #job name
      kubernetes_sd_configs: #发现配置
      - role: node #发现角色
      relabel_configs: #标签重写配置
      - source_labels: [__address__] #源标签
        regex: '(.*):10250' #通过正则匹配后缀为：10250的实例，10250是kubelet端口
        replacement: '${1}:9100' #重写为IP:9100,既端口替换为prometheus node-exporter 的端口 
        target_label: __address__ #将[__address__]替换为__address
        action: replace #将[_address__]的值依然赋值给__address__
        
#发现lable并引用
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
匹配的 目的数据：
```



![](/images/posts/11_prometheus/57.png)

引用之后的效果：



![](/images/posts/11_prometheus/58.png)



#### 5.1.10.2：kubelet监听端口：

```bash
root@s3:~# lsof -i:10250
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
kubelet    616 root   15u  IPv4  85444      0t0  TCP s3:10250->s3:45126 (ESTABLISHED)
kubelet    616 root   27u  IPv4  59426      0t0  TCP s3:10250 (LISTEN)

```

#### 5.1.10.3: node节点指标数据：

```bash
# curl 192.168.48.166:9100/metrics

#HELP:解释当前指标含义，上面表示在每种模式下node节点的cpu花费的时间，以s为单位
# TYPE node_load1 gauge 说明当前指标的数据类型，如：
node_load1 1.93
# HELP node_load15 15m load average.
# TYPE node_load15 gauge
node_load15 1.88
# HELP node_load5 5m load average.
# TYPE node_load5 gauge
node_load5 1.59
```

#### 5.1.10.4：常见监控指标：

```bash
node_cpu_:CPU相关指标
node_load1: load average #系统负载指标
node_load5:
node_load15:

node_memory: 内存相关指标

node_network_: 网关指标

node_disk_:磁盘IO相关指标
node_filesystem_:文件系统相关指标

node_boot_time_seconds: 系统启动时间监控

go_*: node exporte运行过程中go相关指标
process_*: node exporter运行时进程内部进程指标
```

### 5.1.11: Cadvisor发现：

#### 5.1.11.1：prometheus job配置:

```bash
   - job_name: 'kubernetes-node-cadvisor' #job名称
      kubernetes_sd_configs: #基于k8s的服务发现
      - role:  node #角色
      scheme: https#协议
      tls_config:#证书配置
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt #默认证书路径
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token #默认token路径
      relabel_configs: #标签重写配置
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
        
#repliacement指定的替换后的标签（target_label）对应的值为kubernetes.default.svc:443
      - target_label: __address__
        replacement: kubernetes.default.svc:443
        
#将[__meta_kubernetes_node_name]重写为_metrics_path_
      - source_labels: [__meta_kubernetes_node_name]
        regex: (.+) #至少1位长度以上
        target_label: __metrics_path__
        action: replace
        replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor #指定值cadivisord的DPI路径
        
 
 
 
 tls_config配置的证书地址是每个POD连接apiserveer所使用的地址，无论正式是否用的上，在POD启动的时候Kubelet都会给每一个pod自动注入ca的公钥，既所有的Pod启动都会有一个ca公钥被注入进去用于在访问apiserver的时候被调用
 

```



### 5.1.11：peometheus在k8s内部实现pod发现：

在内部直接发现Pod并执行监控数据采集

#### 5.1.11.1：发现配置：

```bash
   - job_name: 'kubernetes-pods' #job名称
      kubernetes_sd_configs: #基于k8s的服务发现
      - role:  node #角色
        namespaces: #可选指定namespace,如不指定就是发现所有的namespace中的Pod
          names:
          - myserver
          - magedu
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_pod_label_(.+)
        - source_labels:[__meta_kubernetes_namespace]
          action: replace
          target_label: kubernetes_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: kubernetes_pod_name

```

#### 5.1.11.2：pod监控指标：

CPU

内存

磁盘

网卡

```bash
sum(rate(container_cpu_usage_seconds_total{image=""}[1m])) without (instance)

sum(rate(container_cpu_usage_seconds_total{image!=""}[1m])) without (instance)

sum(rate(container_fs_io_current{image!=""}[1m])) without (device)
sum(rate(container_fs_writes_bytes_total{image!=""}[1m])) without (device)
sum(rate(container_fs_reads_bytes_totall{image!=""}[1m])) without (device)

sum(rate(container_network_receive_bytes_total{image!=""}[1m])) without (interface)
```



#### 5.1.11.3: 验证数据：



![](/images/posts/11_prometheus/59.png)



### 5.1.12：prometheus部署在k8s集群以外实现服务发现：

在namespace monitor 创建服务发现账户prometheus并授权

#### 5.1.13.1：创建用户并授权：

```bash
# cat case4-prom-rbac.yaml 
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
  namespace: monitor
---
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: monitor-token
  namespace: monitor
  annotations:
    kubernetes.io/service-account.name: "prometheus"
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: prometheus
rules:
- apiGroups:
  - ""
  resources:
  - nodes
  - services
  - endpoints
  - pods
  - nodes/proxy
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - "extensions"
  resources:
    - ingresses
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - configmaps
  - nodes/metrics
  verbs:
  - get
- nonResourceURLs:
  - /metrics
  verbs:
  - get
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
- kind: ServiceAccount
  name: prometheus
  namespace: monitor



# kubectl apply -f case4-prom-rbac.yaml 
serviceaccount/prometheus created
clusterrole.rbac.authorization.k8s.io/prometheus created
clusterrolebinding.rbac.authorization.k8s.io/prometheus created

将token保存至prometheus server节点的k8s.token文件，后期用于权限验证。

```

#### 5.1.13.2：获取token:

```bash
# kubectl get secret -n monitor
monitor-token            kubernetes.io/service-account-token   3      50s


# # kubectl describe secret monitor-token -n monitor



# cat k8s.token 
eyJhbGciOiJSUzI1NiIsImtpZCI6InQ2U2Njd0ZXTk9OYlR1alM4S3R3X29nV1dYdFhMLWdPLVJWaVNxWXE1ZWMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJtb25pdG9yIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im1vbml0b3ItdG9rZW4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoicHJvbWV0aGV1cyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjNmMDA0NmNkLTQ5MTYtNDY2YS1hYTFmLTMxNzY2MDBlZWM2ZCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDptb25pdG9yOnByb21ldGhldXMifQ.VdSEym4GGbFJzAm6BLJEoq1jag3XxEnic0Z7dcalVEr0SrLbW-gbRvoOyRJ3U811o5XHszBenEl__Tgw13Fqc5V_HSCDb4PhLrUJZfM_eZEU_Gqu_Jn90HRQKjnirGQPxmX3zoGykk1AaeVUtTeuqSGz3cjYReRPGHmZnUA1mk2F6Kn7tT_UF62YwUiPThYgQ8tTt3krn0iUnGSzbjVudDXSVYc3E8VyCRhAyECLGq856-mybVKYB-4FhgY0d19sLkANfWbeh041vD62FumRLIXyNGiQp5F8UzbQN6owMR8hky2KKsfSTRsrJxo10AbIL1CBBVHGXBxHvMTrBQJ-8Q
```



#### 5.1.13.3: prometheus添加job:



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
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
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
  - job_name: "promteheus-worknode"
    static_configs:
      - targets: ["192.168.48.164:9100","192.168.48.170:9100"]
  - job_name: "promteheus-pod"
    static_configs:
            - targets: ["192.168.48.166:8080","192.168.48.163:8080","192.168.48.169:8080","192.168.48.170:8080"]
#API Server节点发现
  - job_name: "kubernetes-apiserver-monitor"
    kubernetes_sd_configs:
    - role: endpoints 
      api_server: https://192.168.48.166:6443
      tls_config:
        insecure_skip_verify: true
      bearer_token_file: /apps/prometheus/k8s.token
    scheme: https
    tls_config:
      insecure_skip_verify: true
    bearer_token_file: /apps/prometheus/k8s.token
    relabel_configs:
    - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name] 
      action: keep
      regex: default;kubernetes;https
    - source_labels: [__address__]
      regex: '(.*):6443'
      replacement: '${1}:9100'
      target_label: __address__
      action: replace
    - source_labels: [__scheme__]
      regex: https
      replacement: http
      target_label: __scheme__
      action: replace  #node节点发现
  - job_name: "kubernetes-nodes-monitor"
    scheme: http
    tls_config: 
      insecure_skip_verify: true
    bearer_token_file: /apps/prometheus/k8s.token
    kubernetes_sd_configs:
    - role: node
      api_server: https://192.168.48.166:6443
      tls_config:
        insecure_skip_verify: true
      bearer_token_file: /apps/prometheus/k8s.token
    relabel_configs:
    - source_labels: [__address__]
      regex: '(.*):10250'
      replacement: '${1}:9100'
      target_label: __address__
      action: replace
    - source_labels: [__meta_kubernetes_node_label_failure_domain_beta_kubernetes_io_region]
      regex: '(.*)'
      replacement: '${1}'
      action: replace
      target_label: LOC
    - source_labels: [__meta_kubernetes_node_label_failure_damain_beta_kubernetes_io_region]
      regex: '(.*)'
      replacement: 'NODE'
      action: replace
      target_label: Type
    - source_labels: [__meta_kubernetes_node_label_failure_domain_beta_kubernetes_io_region]
      regex: '(.*)'
      replacement: 'K8S-test'
      action: replace
      target_label: Env
    - action: labelmap
      regex: __meta_kubernetes_node_label_(.+)
 
#只当namespace的pod
  - job_name: 'kubernetes-namespace'
    kubernetes_sd_configs:
    - role: pod
      api_server: https://192.168.48.166:6443
      tls_config:
        insecure_skip_verify: true
      bearer_token_file: /apps/prometheus/k8s.token
      namespaces:
        names:
        - default
        - monitoring
    relabel_configs:
    - action: labelmap
      regex: __meta_kubernetes_pod_label_(.+)
    - source_labels: [__meta_kubernetes_namespace]
      action: replace
      target_label: kubernetes_pod_name
#指定Pod发现条件
  - job_name: 'kubernetes-pod'
    kubernetes_sd_configs:
    - role: pod
      api_server: https://192.168.48.166:6443
      tls_config:
        insecure_skip_verify: true
      bearer_token_file: /apps/prometheus/k8s.token
    relabel_configs:
    - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
      action: keep
      regex: true
    - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
      action: replace
      target_label: __metrics_path__
      regex: (.+)
    - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
      action: replace
      regex: ([^:]+)(?::\d+)?;(\d+)
      replacement: $1:$2
      target_label: __address__
    - action: labelmap
      regex: __meta_kubernetes_pod_label_(.+)
    - source_labels: [__meta_kubernetes_pod_name]
      action: replace
      target_label: kubernetes_pod_name
    - source_labels: [__meta_kubernetes_pod_label_pod_template_hash]
      regex: '(.*)'
      replacement: 'K8S-test'
      action: replace
      target_label: Env

```



![](/images/posts/11_prometheus/60.png)











## 5.2: static_configs:

后续都是二进制部署的prometheus环境

### 5.2.1：prometheus配置文件：

```bash
# A scrape configuration containing exactly one endpoint to scrape:#端点抓取配置
# Here it's Prometheus itself. #prometheus的默认配置
scrape_configs:
#作业名称job=<job_name>会自动添加到此配置的时间序列数据中
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus" #job名称

    # metrics_path defaults to '/metrics' #默认uri
    # scheme defaults to 'http'. #协议

    static_configs: #静态服务配置
      - targets: ["localhost:9090"] #目标端点地址
 - job_name: "prometheus-node"
   static_configs:
      - targets: ["192.168.48.165:9100"]

```



## 5.3：consul_sd_configs:

https://www.consul.io/

```bash
Consul是分布式k/v数据存储集群，目前常用于服务的服务注册和发现
```

### 5.3.1: 部署consul集群：

https://releases.hashicorp.com/consul

环境

```bash
192.168.48.164
192.168.48.165

node1:
# wget https://releases.hashicorp.com/consul/1.13.1/consul_1.13.1_linux_amd64.zip
# unzip consul_1.13.1_linux_amd64.zip
# scp consul 192.168.48.165:/usr/local/bin/
# scp consul 192.168.48.164:/usr/local/bin/

# consul -h #验证可执行

分别创建数据目录
# mkdir /data/consul
consul agent-server #使用server模式运行consul服务
-bootstrap #首次部署使用初始化模式
-bind #设置群集通信的监听地址
-client #设置客户端访问的监听地址
-data-dir #指定数据保存路径
-ui #启用内置静态web UI服务器
-node #此节点的名称，集群中必须唯一
-datacenter=dc1 #集群名称，默认是dc1
-join #加入到已有consul环境

启动服务：
node1:
# nohup consul agent -server -bootstrap -bind=192.168.48.164 -client=192.168.48.164 -data-dir=/data/consul/ -ui -node=192.168.48.164 &

node2:
# nohup consul agent  -bind=192.168.48.165 -client=192.168.48.165 -data-dir=/data/consul/ -node=192.168.48.165 -join=192.168.48.164 &
```



### 5.3.2：验证集群:

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



web截图：



![](/images/posts/11_prometheus/61.png)





### 5.3.3: 测试写入数据：

通过consul的API写入数据

```bash
# curl -X PUT -d '{"id": "node-exporter165","name": "node-exporter165","address":"192.168.48.165","port":9100,"tags": ["node-exporter"],"checks": [{"http":"http://192.168.48.165:9100/","interval":"5s"}]}' http://192.168.48.164:8500/v1/agent/service/register

# curl -X PUT -d '{"id": "node-exporter169","name": "node-exporter169","address":"192.168.48.169","port":9100,"tags": ["node-exporter"],"checks": [{"http":"http://192.168.48.169:9100/","interval":"5s"}]}' http://192.168.48.164:8500/v1/agent/service/register

# curl -X PUT -d '{"id": "node-exporter170","name": "node-exporter170","address":"192.168.48.170","port":9100,"tags": ["node-exporter"],"checks": [{"http":"http://192.168.48.170:9100/","interval":"5s"}]}' http://192.168.48.164:8500/v1/agent/service/register


{
    "ID":"prometheus-143", #自定义
    "Name":"monitor",	#自定义
    "Address":"192.168.153.143",  #写自己服务的ip
    "Port": 9090,	#写自己服务的port
    "Tags": ["prometheus"],
    "Check":{
        "HTTP":"http://192.168.153.143:9090/-/healthy",  #写自己服务的ip和端口
        "Interval":"10s" #每10s一检测
    }
}


注：
name:consul的service注册名称
id:consul的实例名称
address:监控地址ip
port:监控的端口号
tags:标签名
checks:检查的节点的路径
```



### 5.3.4: consul验证数据：



![](/images/posts/11_prometheus/62.png)



### 5.3.5：配置prometheus 到consul发现服务：

#### 5.3.5.1：主要配置字段:

```bash
static_configs: #配置数据源
consul_sd_configs: #指定基于consul服务发现的配置
rebel_configs: #重新标记
services: [] #表示匹配consul中所有的service
```



#### 5.3.5.2: 二进制-prometheus配置文件：

```bash
 # cat prometheus.yml
 - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
  - job_name: consul
    honor_labels: true
    metrics_path: /metrics
    scheme: http
    consul_sd_configs:
      - server: 192.168.48.164:8500
        services: [] #发现的目标服务名称，名为所有服务，可以写servicea,servieb,servicec
      - server: 192.168.48.165:8500
    relabel_configs:
    - source_labels: ['__meta_consul_tags']
      target_label: 'product'
    - source_labels: ['__meta_consul_dc']
      target_label: 'idc'
    - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
#systemctl restart prometheus.service
```



![](/images/posts/11_prometheus/63.png)



```bash
   匹配删除__meta_consul_service位consul的发现结果（删除consul本机监控）：
   - source_labels: ['__meta_consul_service']
      regex: "consul"
      action: drop
```



![](/images/posts/11_prometheus/64.png)





### 5.3.6：consul服务注册与删除：

```bash
注册：
# curl -X PUT -d '{"id": "node-exporter165","name": "node-exporter165","address":"192.168.48.165","port":9100,"tags": ["node-exporter"],"checks": [{"http":"http://192.168.48.165:9100/","interval":"5s"}]}' http://192.168.48.164:8500/v1/agent/service/register

删除：
# curl --request PUT http://192.168.48.164:8500/v1/agent/service/deregister/node-exporter165
```



## 5.4：file_sd_configs:

### 5.4.1: 编辑sd_configs文件：

```bash
# pwd
/apps
# mkdir file_sd
# cat sd_my_server.json 
[
	{
	
	"targets": ["192.168.48.165:9100","192.168.48.169:9100"]
	
	}
]

```



### 5.4.2: prometheus调用sd_configs:

```bash
# vim prometheus.yml
  - job_name: 'file_sd_my_server'
    file_sd_configs:
    - files:
      - /apps/file_sd/sd_my_server.json
      refresh_interval: 10s
#systemctl restart prometheus
```





### 5.4.3: 验证数据：



![](/images/posts/11_prometheus/65.png)





## 5.5：DNS服务发现：

```bash
基于DNS的服务发现允许配置指定一组DNS域名，这些域名会定期查询以发现目标列表，域名需要可以配置的DNS服务解析为IP

此服务发现方法仅支持基本的DNS A、AAAA和SRV记录查询的
A记录：域名为IP
SRV： SRV记录了哪台计算机提供了具体哪个服务，格式为：自定义的服务的名字，协议的类型，域名（例如：_example-server._tcp.www.mydns.com）

prometheus会对手机的指标数据重新打标，重新标记期间，可以使用以下元标签：
__meta_dns_name: 产剩发现目标的记录名称
__meta_dns_srv_record_target:SRV 记录的目标字段
__meta_dns_srv_record_port:SRV记录的端口字段
```

### 5.5.1：A记录服务发现：

```bash
#vim /etc/hosts
192.168.48.165 node1.example.com
# vim prometheus.yml
  - job_name: 'dns-server-name-monitor'
    metrics_path: "/metrics"
    dns_sd_configs:
    - names: ["node1.example.com"]
      type: A
      port: 9100
#systemctl restart prometheus.service
```



### 5.5.2: 验证服务状态：

![](/images/posts/11_prometheus/66.png)





### 5.5.3：SRV服务发现：

需要有DNS服务器实现域名解析

```bash
# vim prometheus.yml
 - job_name: 'dns-node-name-srv'
    metrics_path: "/metrics"
    dns_sd_configs:
    - names: ["_prometheus._tcp.node.example.com"]
      type: SRV
      port: 9100
```



# 六：kube-state-metrics组件介绍：

https://github.com/kubernetes/kube-state-metrics

```bash
kube-state-metrics:通过监听API Server生成有关资源对象的状态指标，比如Deployment,Node、Pod,需要注意的是kube-state-metrics的使用场景不是用于监控对方是否存活，而实用于周期性获取目标对象的metrics指标数据并在web界面进行显示或被prometheus抓取（如pod的状态是running还是Terminating、pod的创建时间等），目前的kube-state-metics收集的指标数据可参见官方的文档，https://github.com/kubernetes/kube-state-metrics/tree/master/docs,并不会存储这些指标数据，所有我们可以使用Prometheus来抓取这些数据然后存储，主要关注的是业务相关的一些元数据，比如Deployment、Pod、副本状态等，调度了多少个replicas? 限制可用的有几个？多少个pod是running/stopped/terminated状态？pod重启了多少次？目前有多个job在运行中
```



Github:

https://github.com/kubernetes/kube-state-metrics



镜像：

https://hub.docker.com/r/bitnami/kube-state-metrics

https://quay.io/repository/coreos/kube-state-metrics?tag=latest&tab=tags



#指标

https://xie.infoq.cn/article/9e1fff6306649e65480a96bb1



## 6.1: 部署kube-state-metrics:

```bash
root@k8s-master-2:/apps/metrics-server# cat kube-metrics-deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kube-state-metrics
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kube-state-metrics
  template:
    metadata:
      labels:
        app: kube-state-metrics
    spec:
      serviceAccountName: kube-state-metrics
      containers:
      - name: kube-state-metrics
        image: harbor.aeotrade.net/base/kube-state-metrics:2.5.0 
        ports:
        - containerPort: 8080

---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: kube-state-metrics
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: kube-state-metrics
rules:
- apiGroups: [""]
  resources: ["nodes", "pods", "services", "resourcequotas", "replicationcontrollers", "limitranges", "persistentvolumeclaims", "persistentvolumes", "namespaces", "endpoints"]
  verbs: ["list", "watch"]
- apiGroups: ["extensions"]
  resources: ["daemonsets", "deployments", "replicasets"]
  verbs: ["list", "watch"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["list", "watch"]
- apiGroups: ["batch"]
  resources: ["cronjobs", "jobs"]
  verbs: ["list", "watch"]
- apiGroups: ["autoscaling"]
  resources: ["horizontalpodautoscalers"]
  verbs: ["list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: kube-state-metrics
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: kube-state-metrics
subjects:
- kind: ServiceAccount
  name: kube-state-metrics
  namespace: kube-system

---
apiVersion: v1
kind: Service
metadata:
  annotations:
    prometheus.io/scrape: 'true'
    prometheus.io/port: "8080"
  name: kube-state-metrics
  namespace: kube-system
  labels:
    app: kube-state-metrics
spec:
  type: NodePort
  ports:
  - name: kube-state-metrics
    port: 8080
    targetPort: 8080
    nodePort: 31666
    protocol: TCP
  selector:
    app: kube-state-metrics

# kubectl apply -f case5-kube-state-metrics-deploy.yaml 
deployment.apps/kube-state-metrics created
serviceaccount/kube-state-metrics created
clusterrole.rbac.authorization.k8s.io/kube-state-metrics created
clusterrolebinding.rbac.authorization.k8s.io/kube-state-metrics created
service/kube-state-metrics created
```

## 6.2: 验证数据：



![](/images/posts/11_prometheus/67.png)





![](/images/posts/11_prometheus/68.png)



## 6.3：prometheus采集数据：

```bash
#外部prometheus采集
# vim prometheus.ym
  - job_name: "kube-syste-metrics"
    static_configs:
    - targets: ["192.168.48.169:31666"]
    
#内部prometheus采集
# cat configmap.yaml
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
    app: prometheus
  name: prometheus-config
  namespace: monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      scrape_timeout: 10s
      evaluation_interval: 1m
    scrape_configs:
    ##kuberntes-api服务发现
    - job_name: kubernetes-apiserver
      kubernetes_sd_configs:
      - role: endpoints
      scheme: https
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: default;kubernetes;https
     #kubernetes-coredns服务发现
    - job_name: "kubernetes-coredns"
      kubernetes_sd_configs:
      - role: endpoints
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: kube-system;kube-dns;metrics
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_scrape"]	#
        regex: true
        action: keep
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_scheme"]
        regex: (https?)
        action: replace
        target_label: __scheme__
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_path"]
        regex: (.+)
        action: replace
        target_label: __metrics_path__
      - source_labels: ["__address__", "__meta_kubernetes_service_annotation_prometheus_io_port"]
        regex: ([^:]+)(?::\d+)?;(\d+)
        action: replace
        target_label: __address__
        replacement: $1:$2
      - regex: __meta_kubernetes_service_label_(.+)
        action: labelmap
      - source_labels: ["__meta_kubernetes_service_name"]
        action: replace
        target_label: kubernetes_service_name
      - source_labels: ["__meta_kubernetes_namespace"]
        action: replace
        target_label: kubernetes_namespace
    #kubernetes-node服务发现
    - job_name: "kubernetes-nodes"
      kubernetes_sd_configs:
      - role: node
      relabel_configs:
      - source_labels: ["__address__"]
        regex: '(.*):10250'
        action: replace
        target_label: __address__
        replacement: '${1}:9100'
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
    #kubernetes-cadvisor服务发现
    - job_name: kubernetes-node-cadvisor
      kubernetes_sd_configs:
      - role: node
      scheme: https	#获取监控数据时使用的协议
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      relabel_configs:
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
      - target_label: __address__
        replacement: kubernetes.default.svc:443
      - source_labels: [__meta_kubernetes_node_name]
        regex: (.+)
        target_label: __metrics_path__
        replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
    #kubernetes-consul服务发现
    - job_name: "consul-node"
      consul_sd_configs:
      - server: '192.168.1.145:8500'
        services: ["harbor-144","hengshi-69","kvm-185"]
    - job_name: "consul-etcd"
      consul_sd_configs:
      - server: "192.168.1.145:8500"
        services: ["etcd"]
    #kubernetes-service-endponts
    - job_name: "kubernetes-kube-metrics"
      kubernetes_sd_configs:
      - role: endpoints
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: kube-system;kube-state-metrics;kube-state-metrics
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)

```



![](/images/posts/11_prometheus/69.png)



## 6.5: grafana导入模板：

### 6.5.1：13332：



![](/images/posts/11_prometheus/70.png)



### 6.5.2：13824：



![](/images/posts/11_prometheus/71.png)



14518

![](/images/posts/11_prometheus/72.png)







# 七：监控发展：

基于第三方exporter实现对目的服务的监控

## 7.1：监控tomcat:

https://github.com/nlighten/tomcat_exporter

监控tomcat的活跃连接数、堆栈内存等信息：

```bash
#TYPE tomcat_connections_active_total gauge
tomcat_connections_active_total{name="http-nio-8080",}2.0

#TYPE jvm_memory_bytes_used gauge
jvm_memory_bytes_used{area="heap",} 2.445126E7
```

### 7.1.1: 自定义镜像：

```bash
# cat Dockerfile 
FROM tomcat:8.5.73

ADD server.xml /usr/local/tomcat/conf/server.xml 

RUN mkdir /data/tomcat/webapps -p
ADD myapp /data/tomcat/webapps/myapp
ADD metrics.war /data/tomcat/webapps 
ADD simpleclient-0.8.0.jar  /usr/local/tomcat/lib/
ADD simpleclient_common-0.8.0.jar /usr/local/tomcat/lib/
ADD simpleclient_hotspot-0.8.0.jar /usr/local/tomcat/lib/
ADD simpleclient_servlet-0.8.0.jar /usr/local/tomcat/lib/
ADD tomcat_exporter_client-0.0.12.jar /usr/local/tomcat/lib/

EXPOSE 8080 8443 8009

```

#### 7.1.1.1: server.xml文件内容：

```bash
# cat server.xml 
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<!-- Note:  A "Server" is not itself a "Container", so you may not
     define subcomponents such as "Valves" at this level.
     Documentation at /docs/config/server.html
 -->
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <!-- Security listener. Documentation at /docs/config/listeners.html
  <Listener className="org.apache.catalina.security.SecurityListener" />
  -->
  <!--APR library loader. Documentation at /docs/apr.html -->
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <!-- Prevent memory leaks due to use of particular java/javax APIs-->
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <!-- Global JNDI resources
       Documentation at /docs/jndi-resources-howto.html
  -->
  <GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <!-- A "Service" is a collection of one or more "Connectors" that share
       a single "Container" Note:  A "Service" is not itself a "Container",
       so you may not define subcomponents such as "Valves" at this level.
       Documentation at /docs/config/service.html
   -->
  <Service name="Catalina">

    <!--The connectors can use a shared executor, you can define one or more named thread pools-->
    <!--
    <Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="150" minSpareThreads="4"/>
    -->


    <!-- A "Connector" represents an endpoint by which requests are received
         and responses are returned. Documentation at :
         Java HTTP Connector: /docs/config/http.html
         Java AJP  Connector: /docs/config/ajp.html
         APR (HTTP/AJP) Connector: /docs/apr.html
         Define a non-SSL/TLS HTTP/1.1 Connector on port 8080
    -->
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    <!-- A "Connector" using the shared thread pool-->
    <!--
    <Connector executor="tomcatThreadPool"
               port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    -->
    <!-- Define an SSL/TLS HTTP/1.1 Connector on port 8443
         This connector uses the NIO implementation. The default
         SSLImplementation will depend on the presence of the APR/native
         library and the useOpenSSL attribute of the
         AprLifecycleListener.
         Either JSSE or OpenSSL style configuration may be used regardless of
         the SSLImplementation selected. JSSE style configuration is used below.
    -->
    <!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true">
        <SSLHostConfig>
            <Certificate certificateKeystoreFile="conf/localhost-rsa.jks"
                         type="RSA" />
        </SSLHostConfig>
    </Connector>
    -->
    <!-- Define an SSL/TLS HTTP/1.1 Connector on port 8443 with HTTP/2
         This connector uses the APR/native implementation which always uses
         OpenSSL for TLS.
         Either JSSE or OpenSSL style configuration may be used. OpenSSL style
         configuration is used below.
    -->
    <!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11AprProtocol"
               maxThreads="150" SSLEnabled="true" >
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
            <Certificate certificateKeyFile="conf/localhost-rsa-key.pem"
                         certificateFile="conf/localhost-rsa-cert.pem"
                         certificateChainFile="conf/localhost-rsa-chain.pem"
                         type="RSA" />
        </SSLHostConfig>
    </Connector>
    -->

    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <!--
    <Connector protocol="AJP/1.3"
               address="::1"
               port="8009"
               redirectPort="8443" />
    -->

    <!-- An Engine represents the entry point (within Catalina) that processes
         every request.  The Engine implementation for Tomcat stand alone
         analyzes the HTTP headers included with the request, and passes them
         on to the appropriate Host (virtual host).
         Documentation at /docs/config/engine.html -->

    <!-- You should set jvmRoute to support load-balancing via AJP ie :
    <Engine name="Catalina" defaultHost="localhost" jvmRoute="jvm1">
    -->
    <Engine name="Catalina" defaultHost="localhost">

      <!--For clustering, please take a look at documentation at:
          /docs/cluster-howto.html  (simple how to)
          /docs/config/cluster.html (reference documentation) -->
      <!--
      <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"/>
      -->

      <!-- Use the LockOutRealm to prevent attempts to guess user passwords
           via a brute-force attack -->
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <!-- This Realm uses the UserDatabase configured in the global JNDI
             resources under the key "UserDatabase".  Any edits
             that are performed against this UserDatabase are immediately
             available for use by the Realm.  -->
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="localhost"  appBase="/data/tomcat/webapps"  unpackWARs="true" autoDeploy="true">

        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
    </Engine>
  </Service>
</Server>

```



### 7.1.2: 构建并测试镜像：

```bash
# cat build-command.sh 
#!/bin/bash

docker build -t harbor.magedu.net/prod/tomcat-app1:v1 .

docker push harbor.magedu.net/prod/tomcat-app1:v1
# bash build-command.sh
# docker run -it 8081:8080 harbor.magedu.net/prod/tomcat-app1:v1
```

![](/images/posts/11_prometheus/73.png)

![](/images/posts/11_prometheus/74.png)





### 7.1.3: 创建pod并验证：

```bash
# cat tomcat-deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment
  namespace: default
spec:
  selector: 
    matchLabels: 
     app: tomcat
  replicas: 1 
  template: 
    metadata:
      labels:
        app: tomcat
      annotations:
        prometheus.io/scrape: 'true'
    spec:
      containers:
      - name: tomcat
        image: harbor.magedu.net/prod/tomcat-app1:v1
        ports:
        - containerPort: 8080
        securityContext: 
          privileged: true

# kubectl apply -f tomcat-deploy.yaml 
deployment.apps/tomcat-deployment created

# cat tomcat-svc.yaml 
kind: Service  #service 类型
apiVersion: v1
metadata:
#  annotations:
#    prometheus.io/scrape: 'true'
  name: tomcat-service
spec:
  selector:
    app: tomcat
  ports:
  - nodePort: 31080
    port: 80
    protocol: TCP
    targetPort: 8080
  type: NodePort

# kubectl apply -f tomcat-svc.yaml 
service/tomcat-service created

```

![](/images/posts/11_prometheus/75.png)





### 7.1.4: prometheus采集并验证数据：

```bash
#vim prometheus.yml
- job_name: "tomcat-metrics"
    static_configs:
      - targets: ["192.168.48.169:31080"]
#systemctl restart prometheus
```



![](/images/posts/11_prometheus/76.png)



### 7.1.5: grafana导入模板：

https://github.com/nlighten/tomcat_exporter

https://github.com/nlighten/tomcat_exporter/tree/master/dashboard





![](/images/posts/11_prometheus/77.png)



## 7.2：监控Redis:

通过redis_exporter监控redis服务状态

https://github.com/oliver006/redis_exporter

### 7.2.1: 部署Redis

```bash
# kubectl create ns studylinux-net
namespace/studylinux-net created
# cat redis-deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: studylinux-net 
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:4.0.14 
        resources:
          requests:
            cpu: 50m
            memory: 50Mi
        ports:
        - containerPort: 6379
      - name: redis-exporter
        image: oliver006/redis_exporter:latest
        resources:
          requests:
            cpu: 50m
            memory: 50Mi
        ports:
        - containerPort: 9121


# kubectl apply -f redis-deployment.yaml 
deployment.apps/redis created

# cat redis-exporter-svc.yaml 
kind: Service  #service 类型
apiVersion: v1
metadata:
  annotations:
    prometheus.io/scrape: 'true'
    prometheus.io/port: "9121"
  name: redis-exporter-service
  namespace: studylinux-net 
spec:
  selector:
    app: redis
  ports:
  - nodePort: 31082
    name: prom
    port: 9121
    protocol: TCP
    targetPort: 9121
  type: NodePort

# kubectl apply -f redis-exporter-svc.yaml 
service/redis-exporter-service created

# cat redis-redis-svc.yaml 
kind: Service  #service 类型
apiVersion: v1
metadata:
#  annotations:
#    prometheus.io/scrape: 'false'
  name: redis-redis-service
  namespace: studylinux-net 
spec:
  selector:
    app: redis
  ports:
  - nodePort: 31081
    name: redis
    port: 6379
    protocol: TCP
    targetPort: 6379
  type: NodePort

# kubectl apply -f redis-redis-svc.yaml 
service/redis-redis-service created

```

### 7.2.2: 验证metrics:



![](/images/posts/11_prometheus/78.png)



### 7.2.3: prometheus采集数据：

```bash
  - job_name: "redis-metrics"
    static_configs:
      - targets: ["192.168.48.169:31082"]

```

### 7.2.4: gragana导入模板并验证：

14615



![](/images/posts/11_prometheus/79.png)

11835



![](/images/posts/11_prometheus/80.png)



## 7.3： 监控MySQL:

通过mysqld_exporter监控MySQL服务的运行状态

https://github.com/prometheus/mysqld_exporter

### 7.3.1: 安装mysql:

```bash
# apt install mariadb-server
# mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 41
Server version: 10.1.48-MariaDB-0ubuntu0.18.04.1 Ubuntu 18.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 

```



### 7.3.2: 授权监控账户权限：

```bash
MariaDB [(none)]> CREATE USER 'mysql_exporter'@'localhost' IDENTIFIED BY 'imnot007';
Query OK, 0 rows affected (0.02 sec)

MariaDB [(none)]> GRANT PROCESS,REPLICATION CLIENT,SELECT ON *.* TO 'mysql_exporter'@'localhost';
Query OK, 0 rows affected (0.00 sec)

#验证权限：
root@s5:~# mysql -umysql_exporter -pimnot007 -hlocalhost
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 43
Server version: 10.1.48-MariaDB-0ubuntu0.18.04.1 Ubuntu 18.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 

```

### 7.3.3：准备mysqld_exporter环境：

https://github.com/prometheus/mysqld_exporter/releases #下载地址

```bash
# tar -xf mysqld_exporter-0.14.0.linux-amd64.tar.gz 
# cd mysqld_exporter-0.14.0.linux-amd64/
# cp mysqld_exporter /usr/local/bin/

#免密码登录配置：
#cat /root/.my.cnf
[client]
user=mysql_exporter
password=imnot007

验证权限：
#mysql
```

### 7.3.4: 启动mysql_exporter:

```bash
# cat /etc/systemd/system/mysqld_exporter.service
[Unit]
Description=Prometheus Node Exporter
After=network.target

[Service]
ExecStart=/usr/local/bin/mysqld_exporter --config.my-cnf=/root/.my.cnf

[Install]
WantedBy=multi-user.target


# systemctl daemon-reload
# systemctl restart mysqld_exporter.service 
# systemctl enable mysqld_exporter.service 
```



### 7.3.5: 验证metrics:



![](/images/posts/11_prometheus/81.png)



### 7.3.7: prometheus采集数据：



```bash
  - job_name: "mysql-monitor"
    static_configs:
      - targets: ["192.168.48.164:9104"]

```



![](/images/posts/11_prometheus/82.png)



### 7.3.8: 导入模板：

13106

![](/images/posts/11_prometheus/83.png)



11323



![](/images/posts/11_prometheus/84.png)





## 7.4：监控haproxy:

通过haproxy_exporter监控haproxy

https://github.com/prometheus/haproxy_exporter

### 7.4.1: 部署haproxy:

```bash
# apt-cache madison haproxy
   haproxy | 1.8.8-1ubuntu0.11 | http://mirrors.aliyun.com/ubuntu bionic-updates/main amd64 Packages
   haproxy | 1.8.8-1ubuntu0.10 | http://mirrors.aliyun.com/ubuntu bionic-security/main amd64 Packages
   haproxy |    1.8.8-1 | http://mirrors.aliyun.com/ubuntu bionic/main amd64 Packages
   haproxy |    1.8.8-1 | http://mirrors.aliyun.com/ubuntu bionic/main Sources
   haproxy | 1.8.8-1ubuntu0.10 | http://mirrors.aliyun.com/ubuntu bionic-security/main Sources
   haproxy | 1.8.8-1ubuntu0.11 | http://mirrors.aliyun.com/ubuntu bionic-updates/main Sources
   
#apt install haproxy
# vim /etc/haproxy/haproxy.cfg
编译配置文件：
listen stats
  bind :9999
  stats enable
  stats uri /haproxy-status
  stats realm HAPorxy\ Stats\ Page
  stats auth admin:admin

listen prometheus-server-9090
  bind :9090
  mode http
  server 192.168.48.164 192.168.48.164:9090 check inter 3s fall 3 rise 5
  
  # systemctl restart haproxy
```



### 7.4.2：部署haproxy_exporter:

```bash
# wget https://github.com/prometheus/haproxy_exporter/releases/download/v0.13.0/haproxy_exporter-0.13.0.linux-amd64.tar.gz
# tar -xf haproxy_exporter-0.13.0.linux-amd64.tar.g
# cp haproxy_exporter-0.13.0.linux-amd64/haproxy_exporter /usr/local/bin/

启动方式一：
# haproxy_exporter --haproxy.scrape-uri=unix:/run/haproxy/admin.sock

启动方式二：
#haproxy_exporter  --haproxy.scrape-uri="http://haadmin:123456@127.0.0.1:9009/haproxy-status;csv" &
```



![](/images/posts/11_prometheus/85.png)



### 7.4.3： 验证metrics数据：



![](/images/posts/11_prometheus/86.png)





### 7.4.4：prometheus添加Job:

```bash
  - job_name: "haproxy-metrics"
    static_configs:
      - targets: ["192.168.48.165:9101"]

```



### 7.4.5: 验证指标数据：



![](/images/posts/11_prometheus/87.png)



### 7.4.6：grafana导入模板并验证：

2428   367



![](/images/posts/11_prometheus/88.png)





![](/images/posts/11_prometheus/89.png)



## 7.5：监控nginx与ingress-nginx-controller:

通过Prometheus监控Nginx

```bash
需要在编译安装nginx的时候添加nginx-module-vts模块

github地址： https://github.com/vozlt/nginx-module-vts
```



### 7.5.1: 监控Nignx

#### 7.5.1.1: 编译安装Nginx:

```bash
# git clone https://github.com/vozlt/nginx-module-vts.git
# wget http://nginx.org/download/nginx-1.20.2.tar.gz
# tar -xf nginx-1.20.2.tar.gz
# cd nginx-1.20.2/
# ./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_stub_status_module --with-http_gzip_static_module --with-pcre --with-file-aio --with-stream --with-stream_ssl_module --with-stream_realip_module --add-module=/usr/local/src/nginx-module-vts/


# make && make install
```



#### 7.5.1.2: 编译Nginx配置文件：

```bash
# vim /usr/local/nginx/conf/nginx.conf
http 范围配置：
vhost_traffic_status_zone;  #启用状态页

server 字段配置：

location /status {
            vhost_traffic_status_display;
            vhost_traffic_status_display_format html;
        }
        
```



#### 7.5.1.3： 启动nginx并验证web状态页：



```bash
# ./nginx -t
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
# ./nginx
```



访问统计页面：



![](/images/posts/11_prometheus/90.png)



#### 7.5.1.4：安装 nginx exporter:

```bash
# wget https://github.com/hnlq715/nginx-vts-exporter/releases/download/v0.10.3/nginx-vts-exporter-0.10.3.linux-amd64.tar.gz

# tar xf nginx-vts-exporter-0.10.3.linux-amd64.tar.gz
#cd nginx-vts-exporter-0.10.3.linux-amd64/

# cp nginx-vts-exporter /usr/local/bin/
启动程序
# nohup nginx-vts-exporter -nginx.scrape_uri=http://192.168.48.165/status/format/json
```



![](/images/posts/11_prometheus/91.png)



#### 7.5.1.7：prometheus配置：

```bash
  - job_name: "nginx-nodes"
    static_configs:
      - targets: ["192.168.48.165:9913"]

```



![](/images/posts/11_prometheus/92.png)



#### 7.5.1.7: grafana关联模板：

https://grafana.com/grafana/dashboards/2949-nginx-vts-stats/



![](/images/posts/11_prometheus/93.png)



## 7.6: blackbox_exporter监控组件：

https://prometheus.io/download/#blackbox_exporter



blackbox_exporter是proemtheus官方提供的一个exporter可以监视HTTP、HTTPS、DNS、TCP、ICMP等目标实例，从而实现对监控节点进行监控和数据采集：

```bash
HTTP/HTTPS: URL/API可用性检测
TCP: 端口监听检测
ICMP：主机存活检测
DNS： 域名解析
```



### 7.6.1：部署blackbox exporter:

```bash
# wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.22.0/blackbox_exporter-0.22.0.linux-amd64.tar.gz
# tar -xf blackbox_exporter-0.22.0.linux-amd64.tar.gz
# ln -sv /usr/local/src/blackbox_exporter-0.22.0.linux-amd64 /usr/local/blackbox_exporter
'/usr/local/blackbox_exporter' -> '/usr/local/src/blackbox_exporter-0.22.0.linux-amd64'
```



### 7.6.2: 创建blackbox exporter启动文件：

```bash
# # vim /etc/systemd/system/blackbox-exporter.service 
[Unit]
Description=Prometheus Blackbox Exporter
After=network.target

[Service]
Type=simple
User=root
Group=root
ExecStart=/usr/local/blackbox_exporter/blackbox_exporter  --config.file=/usr/local/blackbox_exporter/blackbox.yml --web.listen-address=:9115
Restart=on-failure

[Install]
WantedBy=multi-user.target

# systemctl restart blackbox-exporter.service && systemctl enable blackbox-exporter.service 
Created symlink /etc/systemd/system/multi-user.target.wants/blackbox-exporter.service → /etc/systemd/system/blackbox-exporter.service.

```

### 7.6.3: 验证web界面：



![](/images/posts/11_prometheus/94.png)





### 7.6.4：blackbox exporter实现URL监控：

prometheus调用blackbox exporter实现对URL/ICMP的监控

#### 7.6.4.1：prometheus URL监控配置：

```bash
 # vim prometheus.yml

# 网站监控
  - job_name: 'http_status'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets: ['http://www.aeotrade.com', 'http://www.xiaomi.com']
        labels:
          instance: http_status
          group: web
    relabel_configs:
      - source_labels: [__address__] #relabel通过将__address__(当前目标地址)写入__param_target标签来创建一个label。
        target_label: __param_target #监控目标www.xiaomi.com,作为__address__的value
      - source_labels: [__param_target] #监控目标
        target_label: url #将监控目标与url创建一个label
      - target_label: __address__
        replacement: 192.168.48.165:9115
#systemctl restart prometheus
```



![](/images/posts/11_prometheus/95.png)



#### 7.6.4.2: blackbox exporter界面验证数据：



![](/images/posts/11_prometheus/96.png)



### 7.6.5：blackbox exporter实现ICMP监控：

#### 7.6.5.1：Prometheus ICMP监控配置：

```bash
# vim prometheus.yml
# icmp 检测
  - job_name: 'ping_status'
    metrics_path: /probe
    params:
      module: [icmp]
    static_configs:
      - targets: ['192.168.48.2',"255.255.255.0"]
        labels:
          instance: 'ping_status'
          group: 'icmp'
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: ip #将ip与__param_target创建一个label
      - target_label: __address__
        replacement: 192.168.48.165:9115

```



#### 7.6.5.2: blackbox exporter界面验证数据：



![](/images/posts/11_prometheus/97.png)



### 7.6.6：blackbox exporter实现端口监控：

#### 7.6.6.1：端口监控配置：

```bash
# vim prometheus.yml
#端口监控
  - job_name: 'port_status'
    metrics_path: /probe
    params:
      module: [tcp_connect]
    static_configs:
      - targets: ['192.168.48.165:9100', '192.168.48.165:3000','192.168.48.166:22']
        labels:
          instance: 'port_status'
          group: 'port'
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: ip
      - target_label: __address__
        replacement: 192.168.48.165:9115

#systemctl restart prometheus
```



#### 7.6.6.2: blackbox exporter界面验证数据：



![](/images/posts/11_prometheus/98.png)



### 7.6.7：导入模板：

#### 7.6.7.1：13587：

https://grafana.com/grafana/dashboards/13587



![](/images/posts/11_prometheus/99.png)



9965



![](/images/posts/11_prometheus/100.png)













# 八：Alertmanager:



prometheus触发一条告警的过程：

prometheus-->触发阈值-->超出阈值-->alertmanager--->分组|抑制|静默--->媒体类型--->邮件|钉钉|微信等



![](/images/posts/11_prometheus/101.png)



在Prometheus中一条告警规则主要由以下几部分组成：

- 

  告警名称：用户需要为告警规则命名，当然对于命名而言，需要能够直接表达出该告警的主要内容

- 

  告警规则：告警规则实际上主要由PromQL进行定义，其实际意义是当表达式（PromQL）查询结果持续多长时间（During）后出发告警

在Prometheus中，还可以通过Group（告警组）对一组相关的告警进行统一定义。当然这些定义都是通过YAML文件来统一管理的。

Alertmanager作为一个独立的组件，负责接收并处理来自Prometheus Server(也可以是其它的客户端程序)的告警信息。Alertmanager可以对这些告警信息进行进一步的处理，比如当接收到大量重复告警时能够消除重复的告警信息，同时对告警信息进行分组并且路由到正确的通知方，Prometheus内置了对邮件，Slack等多种通知方式的支持，同时还支持与Webhook的集成，以支持更多定制化的场景。例如，目前Alertmanager还不支持钉钉，那用户完全可以通过Webhook与钉钉机器人进行集成，从而通过钉钉接收告警信息。同时AlertManager还提供了静默和告警抑制机制来对告警通知行为进行优化。





```bash
分组

分组机制可以将详细的告警信息合并成一个通知。在某些情况下，比如由于系统宕机导致大量的告警被同时触发，在这种情况下分组机制可以将这些被触发的告警合并为一个告警通知，避免一次性接受大量的告警通知，而无法对问题进行快速定位。

例如，当集群中有数百个正在运行的服务实例，并且为每一个实例设置了告警规则。假如此时发生了网络故障，可能导致大量的服务实例无法连接到数据库，结果就会有数百个告警被发送到Alertmanager。

而作为用户，可能只希望能够在一个通知中中就能查看哪些服务实例收到影响。这时可以按照服务所在集群或者告警名称对告警进行分组，而将这些告警内聚在一起成为一个通知。

告警分组，告警时间，以及告警的接受方式可以通过Alertmanager的配置文件进行配置。

抑制

抑制是指当某一告警发出后，可以停止重复发送由此告警引发的其它告警的机制。

例如，当集群不可访问时触发了一次告警，通过配置Alertmanager可以忽略与该集群有关的其它所有告警。这样可以避免接收到大量与实际问题无关的告警通知。

抑制机制同样通过Alertmanager的配置文件进行设置。

静默

静默提供了一个简单的机制可以快速根据标签对告警进行静默处理。如果接收到的告警符合静默的配置，Alertmanager则不会发送告警通知。

静默设置需要在Alertmanager的Werb页面上进行设置。


```



安装altermanager:

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

## 8.1：邮件通知：

https://prometheus.io/docs/alerting/configuration/ #官方配置文档

### 8.1.1：alertermanager配置文件解析：

```bash
# cat alertmanager.yml 
global:
  resolve_timeout: 1m #单次探测超时时间
  smtp_from:	#收件人邮箱地址
  smtp_smarthost:	#邮箱smtp地址
  smtp_auth_username:	#收件人的登录用户名，默认和发件人地址一致
  smtp_auth_password:	#收件人的登录密码，有时候是授权码
  smtp_require_tls:		#是否需要tls协议，默认是true


  wechat_api_url: #企业微信API地址
  wechat_api_secret: #企业微信APIsecret
   wechat_api_corp_id: #企业微信corpid 信息
   
  resolve_timeout: 60s #当一个告警在alertmanager 持续多长时间未接收到新的告警后就标记告警状态we为resolevd(已解决/以恢复)



```

配置详解：

```bash
global:
  resolve_timeout: 1m
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_from: '510587596@qq.com'
  smtp_auth_username: '510587596@qq.com'
  smtp_auth_password: 'pjtnaosng'
  smtp_hello: '@qq.com'
  smtp_require_tls: false

route: #告警的路由匹配规则
  group_by: ['alertname'] #通过alertname的值对告警进行分类，-alert:物理节点CPU使用率
  group_wait: 10s #一组告警第一次发送之前等待的延迟时间，既产生告警后延迟10秒钟将组内新产生的消息一起合并发送（一般 设置为0秒~几分钟）
  group_interval: 2m #-组已发送过初始通知的告警接收到新告警后，下次发送通知前等待的延迟时间（一般设置为5分组或更多）
  repeat_interval: 5m #一条成功发送的告警，在最终发送通知之前等待的时间（通常设置为3小时或更多时间）
  
  #group_wait: 10s #第一次产生告警，等待10s，组内有告警就一起发出，没有其它告警就单独发出。
  #group_interal: 2m #第二次产生告警，先等待2分钟，2分钟还没有恢复就进入repeat_interval.
  #repeat_interval: 5m #在最终发送消息在等待5分钟，5分钟后还没有恢复就发送第二次告警
 
 receiver: default-receiver #其它的告警发送给default=receiver
  
  routes: #将critical的报警发送给myalertname
  - receiver: myalertname
  group_wait: 10s
  match_re:
    severity: critical

receivers: #定义多接收者
- name: 'default-receiver'
  email_configs:
  - to: 'rootroot@aliyun.com'
    send_resolved: true #通知已恢复的告警
- name: myalertname
  webhook_configs:
  - url: 'http://192.168.48.165:8060/dingtalk/alertname/send'
    send_resolved: true #通知已经恢复的告警
```



### 8.1.2：配置并启动alertermanager:

```bash
# cat alertmanager.yml
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_from: '510587596@qq.com'
  smtp_auth_username: '510587596@qq.com'
  smtp_auth_password: 'pqytbuntphszbghi'
  smtp_require_tls: false
  
  
route: #route用来设置报警的分发策略
  group_by: ['alertname'] #采用哪个标签来作为分组依据
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 2m
  receiver: 'web.hook'
receivers:
- name: 'web.hook'
  email_configs:
    - to: '510587596@qq.com'
    
inhibit_rules: #抰制的规则
- source_match: #源匹配级别，当匹配成功发出通知，但是其它‘alertname’,'dev','instance'产生的warning级别的告警通知将被抑制
    severity: 'critical' #报警的事件级别
  target_match:
    severity: 'warning' #调用source_match的severity既如果已经有'critical'级别的告警，你们将匹配目标为新产生的告警级别为'warning'的将被抑制
  equal: ['alertname','dev','instance'] #匹配那些对象的告警


# systemctl restart alertmanager.service

#验证alertmanager的9093端口已经监听
#lsof -i:9093
# lsof -i:9093
COMMAND     PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
alertmana 15902 root    8u  IPv6 245335      0t0  TCP *:9093 (LISTEN)

```

alertmanager dashboard截图

![](/images/posts/11_prometheus/102.png)



### 8.1.3：配置prometheus报警规则：

```bash
# pwd
/apps/prometheus/rules
# cat pods_rule.yaml 
groups:
  - name: alertmanager_pod.rules
    rules:
    - alert: node内存可用大小 
      expr: node_memory_MemFree_bytes > 107*1024*104
      for: 15s
      labels:
        severity: critical
      annotations:
        description: "{{$labels.instance}}可用内存小于{{$value}}"

  - name: alertmanager_node.rules
    rules:
    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 80  #磁盘容量利用率大于80%
      for: 2s
      labels:
        severity: critical
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 60 #磁盘容量利用率大于60%
      for: 2s
      labels:
        severity: warning
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

```



```bash
# cat pods_rule.yaml 
groups:
  - name: alertmanager_pod.rules
    rules:
    - alert: Pod_all_cpu_usage
      expr: (sum by(name)(rate(container_cpu_usage_seconds_total{image!=""}[5m]))*100) > 10
      for: 2m
      labels:
        severity: critical
        service: pods
      annotations:
        description: 容器 {{ $labels.name }} CPU 资源利用率大于 10% , (current value is {{ $value }})
        summary: Dev CPU 负载告警

    - alert: Pod_all_memory_usage  
      #expr: sort_desc(avg by(name)(irate(container_memory_usage_bytes{name!=""}[5m]))*100) > 10  #内存大于10%
      expr: sort_desc(avg by(name)(irate(node_memory_MemFree_bytes {name!=""}[5m]))) > 2147483648   #内存大于2G
      for: 2m
      labels:
        severity: critical
      annotations:
        description: 容器 {{ $labels.name }} Memory 资源利用率大于 2G , (current value is {{ $value }})
        summary: Dev Memory 负载告警

    - alert: Pod_all_network_receive_usage
      expr: sum by (name)(irate(container_network_receive_bytes_total{container_name="POD"}[1m])) > 50*1024*1024
      for: 2m
      labels:
        severity: critical
      annotations:
        description: 容器 {{ $labels.name }} network_receive 资源利用率大于 50M , (current value is {{ $value }})

    - alert: node内存可用大小 
      expr: node_memory_MemFree_bytes > 512*1024*1024 #故意写错的
      #expr: node_memory_MemFree_bytes > 1 #故意写错的
      for: 15s
      labels:
        severity: critical
      annotations:
        description: node可用内存小于4G

  - name: alertmanager_node.rules
    rules:
    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 80  #磁盘容量利用率大于80%
      for: 2s
      labels:
        severity: critical
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 60 #磁盘容量利用率大于60%
      for: 2s
      labels:
        severity: warning
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

```







### 8.1.4: prometheus加载报警规则：

```bash
# vim prometheus.yml
# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - 192.168.48.165:9093 #altermanager地址

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - "/apps/prometheus/rules/pods_rule.yaml" #指定规则文件
  # - "second_rules.yml"


```

### 8.1.5：规则验证：

```bash
# ./promtool check rules /apps/prometheus/rules/pods_rule.yaml 
Checking /apps/prometheus/rules/pods_rule.yaml
  SUCCESS: 3 rules found
#systemctl restart prometheus
```

### 8.1.6: 查看当前告警：

在alertmanager服务器，使用amtool查看当前报警事件：

```bash
# ./amtool alert --alertmanager.url=http://192.168.48.165:9093
Alertname   Starts At                Summary  State   
node内存可用大小  2022-09-28 13:58:33 UTC           active  
node内存可用大小  2022-09-28 13:58:33 UTC           active  
```



### 8.1.7: prometheus的事件状态：

```bash
prometheus报警状态
	inactive: 没有异常
	pending: 已触发阈值，但为满足告警持续事件（既rule中的for字段）
	firing: 已触发阈值且满足条件并发送至alertmanager
```



### 8.1.8：邮箱验证邮件：



![](/images/posts/11_prometheus/103.png)



## 8.2：钉钉通知：



### 8.2.1：钉钉群组创建机器人-关键字认证：



![](/images/posts/11_prometheus/104.png)





#### 8.2.1.1：钉钉认证-关键字：

```bash
# mkdir /data/scripts -p
# cat dingding.sh 
#!/bin/bash
source   /etc/profile
#PHONE=$1
#SUBJECT=$2
MESSAGE=$1

/usr/bin/curl -X "POST"  'https://oapi.dingtalk.com/robot/send?access_token=928e29bb4519fe478e03c13b18c30d63a25b8c9b218507b84e456009559b0743' \
-H 'Content-Type: application/json' \
-d '{"msgtype": "text", 
    "text": {
         "content": "'${MESSAGE}'"
    }
  }'

```



#### 8.2.1.2: 测试发送消息：

```bash
# bash dingding.sh "namespace=default\npod-pod1\ncpu=%87\n持续时间=4.5m\nalertname=pod"
{"errcode":0,"errmsg":"ok"}
```



![](/images/posts/11_prometheus/105.png)



#### 8.2.1.3: 部署webhook-dingtalk:

https://github.com/timonwong/prometheus-webhook-dingtalk/releases

```bash
# wget https://github.com/timonwong/prometheus-webhook-dingtalk/releases/download/v1.4.0/prometheus-webhook-dingtalk-1.4.0.linux-amd64.tar.gz
# cd prometheus-webhook-dingtalk-1.4.0.linux-amd64/

后台启动
#nohup ./prometheus-webhook-dingtalk --web.listen-address="0.0.0.0:8060" --ding.profile="alertname=https://oapi.dingtalk.com/robot/send?access_token=928e29bb4519fe478e03c13b18c30d63a25b8c9b218507b84e456009559b0743" &
```

#### 8.2.1.4：部署alertermanager:

https://prometheus.io/docs/alerting/configuration/ #官方配置文档



```bash
#tar -xf alertmanager-0.24.0.linux-amd64.tar.gz
#cd  alertmanager-0.24.0.linux-amd64/
# # cat alertmanager.yml
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_from: '510587596@qq.com'
  smtp_auth_username: '510587596@qq.com'
  smtp_auth_password: 'pqytbuntphszbghi'
  smtp_require_tls: false
route:
  group_by: ['alertname'] 
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 2m
  receiver: 'dingding'
receivers:
  - name: 'web.hook'
    email_configs:
    - to: '510587596@qq.com'
      send_resolved: true
  - name: dingding
    webhook_configs:
    - url: 'http://192.168.48.164:8060/dingtalk/alertname/send'
      send_resolved: true

```



#### 8.2.1.5: 启动alertmanager:

```bash
# ./alertmanager 
```

![](/images/posts/11_prometheus/106.png)



![](/images/posts/11_prometheus/107.png)



#### 8.2.1.6: 验证消息发送：

在prometheus产生消息，验证通知消息能否发送



![](/images/posts/11_prometheus/108.png)



#### 8.2.1.7：dingtalk验证日志：



![](/images/posts/11_prometheus/109.png)



#### 8.2.1.8：钉钉验证消息：



![](/images/posts/11_prometheus/110.png)



## 8.3: 企业微信通知：

https://work.weixin.qq.com

打开企业微信官网注册账户，使用自己的手机号进行注册

### 8.3.1：注册企业微信账户：



![](/images/posts/11_prometheus/111.png)





### 8.3.2：登录PC版：

注册完成账户之后就可以扫码登录PC版web界面了，如下：

![](/images/posts/11_prometheus/112.png)



### 8.3.3：创建应用：



![](/images/posts/11_prometheus/113.png)





### 8.3.4：填写应用信息：

![](/images/posts/11_prometheus/114.png)



### 8.3.5：注册完成：

AgentID和Secret会在发送微信报警信息的时候调用：



![](/images/posts/11_prometheus/115.png)



### 8.3.6：创建微信账户：

用户账户名称必须唯一，在发送微信报警信息的时候会调用



![](/images/posts/11_prometheus/116.png)





### 8.3.7：验证通讯录：



![](/images/posts/11_prometheus/117.png)



### 8.3.8：查看企业信息：

企业ID在发送微信报警信息的时候会调用

![](/images/posts/11_prometheus/118.png)



### 8.3.9：测试发送信息：



![](/images/posts/11_prometheus/119.png)



#### 8.3.9.1：测试发送：

![](/images/posts/11_prometheus/120.png)





#### 8.3.9.2：企业微信验证消息：



![](/images/posts/11_prometheus/121.png)



### 8.3.10：认证信息收集：

```bash
企业ID： wwe91b2c2f8e93cd7e
Agentld: 1000002
Secret: tj_2dfBPVGFzAAV7WOg-ZQLYfGudjMvQ5n-g7ZctO14
```



### 8.3.11: alertermanager配置：

```bash
# cat alertmanager.yml
global:
  resolve_timeout: 5m
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_from: '510587596@qq.com'
  smtp_auth_username: '510587596@qq.com'
  smtp_auth_password: 'pqytbuntphszbghi'
  smtp_require_tls: false
route:
  group_by: ['alertname'] 
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 2m
  receiver: 'wechat'
receivers:
  - name: 'web.hook'
    email_configs:
    - to: '510587596@qq.com'
      send_resolved: true
  - name: dingding
    webhook_configs:
    - url: 'http://192.168.48.164:8060/dingtalk/alertname/send'
      send_resolved: true
  - name: 'wechat'
    wechat_configs:
    - corp_id: wwe91b2c2f8e93cd7e
      to_user: '@all'
      agent_id: 1000002
      api_secret: tj_2dfBPVGFzAAV7WOg-ZQLYfGudjMvQ5n-g7ZctO14

```



### 8.3.12: 企业微信验证信息：





## 8.4: 消息分类发送：

根据消息中的属性信息设置规则，将消息分类发送，如下为将severity级别为critical的通知消息发送到dingding，其它的则发送到微信：

### 8.4.1：prometheus rules配置：

```bash
# cat pods_rule.yaml 
groups:
  - name: alertmanager_pod.rules
    rules:
    - alert: Pod_all_cpu_usage
      expr: (sum by(name)(rate(container_cpu_usage_seconds_total{image!=""}[5m]))*100) > 10
      for: 2m
      labels:
        severity: critical
        service: pods
      annotations:
        description: 容器 {{ $labels.name }} CPU 资源利用率大于 10% , (current value is {{ $value }})
        summary: Dev CPU 负载告警

    - alert: Pod_all_memory_usage  
      #expr: sort_desc(avg by(name)(irate(container_memory_usage_bytes{name!=""}[5m]))*100) > 10  #内存大于10%
      expr: sort_desc(avg by(name)(irate(node_memory_MemFree_bytes {name!=""}[5m]))) > 2147483648   #内存大于2G
      for: 2m
      labels:
        severity: critical
        service: pods
      annotations:
        description: 容器 {{ $labels.name }} Memory 资源利用率大于 2G , (current value is {{ $value }})
        summary: Dev Memory 负载告警

    - alert: Pod_all_network_receive_usage
      expr: sum by (name)(irate(container_network_receive_bytes_total{container_name="POD"}[1m])) > 50*1024*1024
      for: 2m
      labels:
        severity: critical
      annotations:
        description: 容器 {{ $labels.name }} network_receive 资源利用率大于 50M , (current value is {{ $value }})

    - alert: node内存可用大小 
      expr: node_memory_MemFree_bytes > 512*1024*1024 #故意写错的
      #expr: node_memory_MemFree_bytes > 1 #故意写错的
      for: 15s
      labels:
        severity: critical
        project: myserver
      annotations:
        description: node可用内存小于4G

  - name: alertmanager_node.rules
    rules:
    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 80  #磁盘容量利用率大于80%
      for: 2s
      labels:
        severity: critical
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 60 #磁盘容量利用率大于60%
      for: 2s
      labels:
        severity: warning
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

```



### 8.4.2: alertermanager配置：

```bash
# cat alertmanager.yml
global:
  resolve_timeout: 1m
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_from: '510587596@qq.com'
  smtp_auth_username: '510587596@qq.com'
  smtp_auth_password: 'pqytbuntphszbghi'
  smtp_require_tls: false

route: #用来设置报警的分发策略
  group_by: ['alertname'] 
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 2m
  receiver: 'wechat' #默认告警方式为钉钉

#添加消息路由
  routes:
  - receiver: 'default-receiver' #critical级别的邮件发送给leader
    group_wait: 10s
    match_re:
      severity: critical #匹配严重等级告警
  - receiver: 'dingding' #宿主机告警通过钉钉发送给运维组
    group_wait: 10s
    match_re:
      project: node #匹配node告警

receivers:
  - name: 'default-receiver'
    email_configs:
    - to: '510587596@qq.com'
      send_resolved: true
  - name: dingding
    webhook_configs:
    - url: 'http://192.168.48.164:8060/dingtalk/alertname/send'
      send_resolved: true
  - name: 'wechat'
    wechat_configs:
    - corp_id: wwe91b2c2f8e93cd7e
     # to_user: '@all'
      to_party: 2
      agent_id: 1000002
      api_secret: tj_2dfBPVGFzAAV7WOg-ZQLYfGudjMvQ5n-g7ZctO14
      send_resolved: true

inhibit_rules: #抑制的规则
  - source_match: #源匹配级别，当匹配成功发出通知，但是其它'alertname','dev','instance'产生的warning级别的告警通知将被抑制
      severity: 'critical' #报警的事件级别
    target_match:
      severity: 'warning' #调用source_match的severity既如果已经有'citical'级别的告警，那么将匹配目标为产生的告警级别为'warning'的将被抑制
    equal: ['alertname','dev','instance'] #匹配那些对象的告警

```







## 8.5: 自定义消息模板：

默认的消息内容需要调整、而且消息是连接在一起的

### 8.5.1：定义模板：

```bash
# cat message_template.templ 
{{ define "wechat.default.message" }}  #定义模板名字
{{ range $i, $alert :=.Alerts }}
===alertmanager监控报警===
告警状态：{{   .Status }}
告警级别：{{ $alert.Labels.severity }}
告警类型：{{ $alert.Labels.alertname }}
告警应用：{{ $alert.Annotations.summary }}
故障主机: {{ $alert.Labels.instance }}
告警主题: {{ $alert.Annotations.summary }}
触发阀值：{{ $alert.Annotations.value }}
告警详情: {{ $alert.Annotations.description }}
触发时间: {{ $alert.StartsAt.Format "2006-01-02 15:04:05" }}
===========end============
{{ end }}
{{ end }}

```



### 8.5.2: alertermanager引用模板：

```bash
# cat alertmanager.yml
global:
  resolve_timeout: 1m
  smtp_smarthost: 'smtp.qq.com:465'
  smtp_from: '510587596@qq.com'
  smtp_auth_username: '510587596@qq.com'
  smtp_auth_password: 'pqytbuntphszbghi'
  smtp_require_tls: false

templates:
  - '/apps/alertmanager/message_template.templ' #alertermanagera 引用模板

route: #用来设置报警的分发策略
  group_by: ['alertname'] 
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 2m
  receiver: 'dingding' #默认告警方式为钉钉


receivers:
  - name: 'default-receiver'
    email_configs:
    - to: '510587596@qq.com'
      send_resolved: true
  - name: dingding
    webhook_configs:
    - url: 'http://192.168.48.164:8060/dingtalk/alertname/send'
      send_resolved: true

```









## 8.6: 告警抑制于静默：

### 8.6.1：告警抑制：

基于告警规则，超过80%就不在发60%的告警，既由60%的表达式触发的告警

被抑制了

```bash
# cat pods_rule.yaml 
groups:
  - name: alertmanager_pod.rules
    rules:
    - alert: node内存可用大小 
      expr: node_memory_MemFree_bytes > 107*1024*104
      for: 15s
      labels:
        severity: critical
      annotations:
        description: "{{$labels.instance}}可用内存小于{{$value}}"

  - name: alertmanager_node.rules
    rules:
    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 80  #磁盘容量利用率大于80%
      for: 2s
      labels:
        severity: critical
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

    - alert: 磁盘容量
      expr: 100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100) > 60 #磁盘容量利用率大于60%
      for: 2s
      labels:
        severity: warning
      annotations:
        summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
        description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

```



### 8.6.2：手动静默

先找到要静默的告警事件，然后手动静默指定的事件：



![](/images/posts/11_prometheus/122.png)



点击silece(静默)：

![](/images/posts/11_prometheus/123.png)



填写信息并创建：

![](/images/posts/11_prometheus/124.png)



查看被当前静默的事件：



![](/images/posts/11_prometheus/125.png)





## 8.7：alertermanager高可用：

### 8.7.1：单机：

大部分使用alertmanager组件的时候，都是用的单点架构，架构图如下所示：



![](/images/posts/11_prometheus/126.png)





### 8.7.2：基于负载均衡：

![](/images/posts/11_prometheus/127.png)



### 8.7.3：基于Gossip机制：

https://yunlzheng.gitbook.io/prometheus-book/part-ii-prometheus-jin-jie/readmd/alertmanager-high-availability

```bash
Alertmanager引入了Gossip机制，Gossip机制为多个Alertmanager之间提供了信息传递的机制，确保及时多个Alertmanager分别接收到相同告警信息的情况下，并且只有一个告警通知被发送给Receiver.
集群环境搭建：
为了能够让Alertmanager节点之间进行通讯，需要在Alertmanager启动时设置相应的参数，其中主要的参数包括：
--cluster.listen-address string: 当前实例集群服务监听地址
--cluster.peer value： 初始化时关联的其它实例的集群服务地址
```



![](/images/posts/11_prometheus/128.png)

## 8.8：PrometheusAlert:

```bash
PrometheusAlert是开源的运维中心消息转发系统，支持主流的监控系统Prometheus、Zabbix,日志系统Graylog2,graylog2、数据可视化系统Grafana、SonarQube，阿里云-云监控，以及所有支持Web-Hook接口的系统发出的预警消息，支持将受到的这些消息发送到钉钉，微信，email,飞书，腾讯短信，腾讯电话等
```

https://github.com/feiyu563/PrometheusAlert

```bash
 #wget https://github.com/feiyu563/PrometheusAlert/releases/download/v4.7/linux.zip && unzip linux.zip &&cd linux/
 

```

altermanager配置：

```bash
cat alertmanager.yml 
global:
  resolve_timeout: 5m
route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 2m
  repeat_interval: 5m
  receiver: 'web.hook'
receivers:
  - name: 'web.hook'
    webhook_configs:
    - url: 'http://192.168.1.144:8080/prometheusalert?type=dd&tpl=dingding&ddurl=https://oapi.dingtalk.com/robot/send?access_token=928e29bb4519fe478e03c13b18c30d63a25b8c9b218507b84e456009559b0743&at=15553986239'
        #inhibit_rules:
        #  - source_match:
        #      severity: 'critical'
        #    target_match:
        #      severity: 'warning'
        #    equal: ['alertname', 'dev', 'instance']

```



### 8.8.1: 告警恢复模板：

```bash
{{ $var := .externalURL}}{{ range $k,$v:=.alerts }}
{{if eq $v.status "resolved"}}
#### [Prometheus恢复信息]({{$v.generatorURL}})

##### <font color="#02b340">【告警名称】</font>：[{{$v.labels.alertname}}]({{$var}})🔥{{if $v.labels.severity}}
#### <font color="#02b340">【告警级别】</font>：{{if eq $v.labels.severity "0"}}提示{{else if eq $v.labels.severity "1"}}警告{{else if eq $v.labels.severity "2"}}一般严重{{else if eq $v.labels.severity "3"}}严重{{else if eq $v.labels.severity "4"}}灾难{{else}}{{$v.labels.severity}}{{end}}{{end}}
#### <font color="#02b340">【当前状态】</font>：**<font color="#67C23A" size=4>已恢复</font>**
##### <font color="#02b340">【触发时间】</font>：{{GetCSTtime $v.startsAt}}
##### <font color="#02b340">【恢复时间】</font>：{{GetCSTtime $v.endsAt}}
##### <font color="#02b340">【告警IP】</font>：{{$v.labels.instance}}
##### <font color="#02b340">【告警主机】</font>：{{$v.labels.nodename}}
#### =======<font color="info">告警详情</font>=======
**<font color="#FF0000">告警主题</font>：{{$v.annotations.summary}}**
**<font color="#FF0000">告警内容</font>：{{$v.annotations.description}}**
[prometheus](http://192.168.1.144:9090/alerts) | 📈[grafana](http://192.168.1.144:3000) | 🚨[alertmanager](http://192.168.1.145:9093)
![aeotrade](https://www.aeotrade.com/_nuxt/img/mainLogo.284fc34.png)
{{else}}
#### [Prometheus告警信息]({{$v.generatorURL}})

##### <font color="#FF0000">【告警名称】</font>：[{{$v.labels.alertname}}]({{$var}})✅{{if $v.labels.severity}}
#### <font color="#02b340">【告警级别】</font>：{{if eq $v.labels.severity "0"}}提示{{else if eq $v.labels.severity "1"}}警告{{else if eq $v.labels.severity "2"}}一般严重{{else if eq $v.labels.severity "3"}}严重{{else if eq $v.labels.severity "4"}}灾难{{else}}{{$v.labels.severity}}{{end}}{{end}}
####  <font color="#FF0000">【当前状态】</font>：**<font color="#E6A23C">需要处理</font>**
##### <font color="#02b340">【告警IP】</font>：{{$v.labels.instance}}
##### <font color="#02b340">【告警主机】</font>：{{$v.labels.nodename}}
#### =======<font color="#FF0000">告警详情</font>=======
**<font color="#FF0000">告警主题</font>：{{$v.annotations.summary}}**
**<font color="#FF0000">告警内容</font>：{{$v.annotations.description}}**
[prometheus](http://192.168.1.144:9090/alerts) | 📈[grafana](http://192.168.1.144:3000) | 🚨[alertmanager](http://192.168.1.145:9093)
![aeotrade](https://www.aeotrade.com/_nuxt/img/mainLogo.284fc34.png)
{{end}}
{{ end }}

```

#### 8.8.1.1: 验证：

![](/images/posts/11_prometheus/129.png)



### 8.8.2: 告警规则模板：

```bash
# cat node_rule.yaml
groups:
- name: node_instance
  rules:
  - alert: Server Status
    expr: up{instance=~'.*'} == 0
    for: 1m
    labels:
      severity: critical
      level: 3
    annotations:
      description: "Host {{ $labels.instance }}: Instance Down"
      summary: "Instance Down" 
 
  - alert: Cpu_user_hight
    expr: (1 - avg(rate(node_cpu_seconds_total{mode="idle"}[2m])) by (instance,job,env)) * 100 > 90
    for: 1m
    labels:
      severity: critical
      level: 2
    annotations:
      description: Host {{ $labels.instance }} Excessive CPU utilization
      summary: Host {{ $labels.instance }} of {{ $labels.job }} Excessive cpu utilization
 
  - alert: Mem_user_Hight
    expr: (1 - (node_memory_MemAvailable_bytes{} / (node_memory_MemTotal_bytes{})))* 100 > 80
    for: 1m
    labels:
      severity: critical
      level: 2
    annotations:
      description: "Host {{ $labels.instance }} : Memory used {{$value}}%"
      summary: "Memory Used High"

  - alert: 磁盘容量
    expr: round(100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100))  > 80
    for: 2s
    labels:
      severity: critical
      level: 3
    annotations:
      summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
      description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

  - alert: "IO使用率过高"
    expr: 100-(avg(irate(node_disk_io_time_seconds_total[1m])) by(instance)* 100) < 60
    for: 1m
    labels:
      severity: warning
    annotations:
      description: "当前使用率{{ $value }}%"
      summary: "IO使用率过高"
  - alert: "网络下载速率过高"
    expr: round(irate(node_network_receive_bytes_total{instance!~"data.*",device!~'tap.*|veth.*|br.*|docker.*|vir.*|lo.*|vnet.*'}[1m])/1024) > 2048
    for: 1m
    labels:
      severity: warning
    annotations:
      summary: "网络下载率过高"
      description: "当前速率{{ $value }}KB/s"

```



# 9: pushgateway:

https://github.com/prometheus/pushgateway

## 9.1: pushgateway简介：

```bash
pushgateway是采用被动推送的方式，而不是类似于prometheus server主动连接exporter获取监控数据

pushgateway开源单独运行在一个节点，然后需要自定义监控脚本把需要监控的主动推送给pushgateway的API接口，然后pushgateway在等待prometheus server抓取数据，既pushgateway本身没有任何抓取监控数据的功能，目前pushgateway只是被动的等待数据从客户端推送过来

--persistence.file='' #数据保存的文件，默认只保存在内存中
--persistence.interval=5m #数据持久化的间隔时间
```



![](/images/posts/11_prometheus/130.png)



## 9.2：部署pushgateway:

```bash
# docker pull prom/pushgateway
# docker run -d --name pushgateway -p 9091:9091 prom/pushgateway:latest
38ae70315b7d5b851f9ea30c81dedafaab08a6ab7ca605ee8e3649dca3f29d67

```

![](/images/posts/11_prometheus/131.png)

## 9.3: prometheus到pushgateway采集数据：

### 9.3.1：验证pushgateway：



![](/images/posts/11_prometheus/132.png)



### 9.3.2：prometheus配置数据采集：

```bash
# cat prometheus.yml
  - job_name: 'pushgateway-monitor'
    scrape_interval: 5s
    static_configs:
    - targets: ['192.168.48.164:9091']
    honor_labels: true

```

### 9.3.3: 验证数据：

![](/images/posts/11_prometheus/133.png)

## 9.4：测试从客户端推送单挑数据:

### 9.4.1: 推送单条数据：

```bash
要Push数据到pushgateway中，开源通过其提供的API标准接口来添加，默认URL地址为：
http://<ip>:9091/metrics/job/<JOBNAME>{/<LABEL_NAME>/<LABEL_VALUE>}

其中<JOBNAME>是必填项，为Job标签值，后边开源跟任意数量的标签时，一般我们会添加一个Instance/<INSTANCE_NAME>实例名称标签，来方便区分各个指标。

推送一个Job名称为mytest_job，key为mytest_metric值为2022
# echo "metest_metric 2022" | curl --data-binary @- http://192.168.48.164:9091/metrics/job/mytest_job
# echo "metest_metric 2026" | curl --data-binary @- http://192.168.48.164:9091/metrics/job/mytest_job

```



### 9.4.2: pushgateway验证数据:



![](/images/posts/11_prometheus/134.png)

```bash
除了mytest_metric外，同事还新增了push_time_seconds和push_failure_time_seconds两个指标，这两个是pushgateway自动生成的指标，分别用于记录指标的数据的成功上传时间和失败上传时间
```

![](/images/posts/11_prometheus/135.png)

### 9.4.3：prometheus server验证数据：



![](/images/posts/11_prometheus/136.png)



## 9.5：测试从客户端推送多条数据：

### 9.5.1：推送多条数据：

```bash
# cat <<EOF | curl --data-binary @- http://192.168.48.164:9091/metrics/job/test_job/instance/192.168.48.165
# TYPE node_memory_Active_bytes gauge
node_memory_Active_bytes 1087266816
# TYPE node_memory_Active_file_bytes gauge
node_memory_Active_file_bytes 486064128
EOF
```

### 9.5.2: pushgateway验证数据：



![](/images/posts/11_prometheus/137.png)

### 9.5.3：prometheus server验证数据：

![](/images/posts/11_prometheus/138.png)



## 9.6：自定义收集数据:

基于自定义脚本实现数据的收集和推送

### 9.6.1：自定义脚本

```bash
# cat monitor.sh 
#!/bin/bash 

total_memory=$(free -b |awk '/Mem/{print $2}')
used_memory=$(free -b |awk '/Mem/{print $3}')

job_name="custom_memory_monitor"
instance_name=`ifconfig  ens33 | grep -w inet | awk '{print $2}'`
pushgateway_server="http://192.168.48.164:9091/metrics/job"

cat <<EOF | curl --data-binary @- ${pushgateway_server}/${job_name}/instance/${instance_name}
#TYPE custom_memory_total  gauge
custom_memory_total $total_memory
#TYPE custom_memory_used  gauge
custom_memory_used $used_memory
EOF
分别在不同主机执行脚本，验证指标数据收集和推送
# bash monitor.sh

```



### 9.6.2：pushgateway验证数据：



![](/images/posts/11_prometheus/139.png)



### 9.6.3：prometheus验证数据：



![](/images/posts/11_prometheus/140.png)



## 9.7：删除数据：

先对一个组写入多个instance的数据：

```bash
#cat <<EOF | curl --data-binary @- http://192.168.48.164:9091/metrics/job/test_job/instance/192.168.48.165
# TYPE node_memory_usage gauge
node_memory_usage 1087266816
# TYPE node_memory_total gauge
node_memory_total 486064128
EOF

#cat <<EOF | curl --data-binary @- http://192.168.48.164:9091/metrics/job/test_job/instance/192.168.48.165
# TYPE node_memory_usage gauge
node_memory_usage 1087266816
# TYPE node_memory_total gauge
node_memory_total 486064128
EOF
```

### 9.7.1: 通过API删除指定组内指定实例的数据：

```bash
#curl -X DELETE http://192.168.48.164:9091/metrics/job/test_job/instance/192.168.48.165

```



![](/images/posts/11_prometheus/141.png)



### 9.7.2: 通过web界面删除：



![](/images/posts/11_prometheus/142.png)



# 10. prometheus联邦：

prometheus Server环境：

```bash
192.168.48.164 #主节点

192.168.48.165 #联邦节点1
192.168.48.170 #联邦节点2

192.168.48.166 #node1 联邦节点1的目标采集服务器
192.168.48.169 #node2 联邦节点2的目标采集服务器
```



## 10.1: 部署prometheus server: 

Prometheus主server 和prometheus 联邦server分别部署prometheus

```bash
# pwd
/usr/local/src
#wget https://github.com/prometheus/prometheus/releases/download/v2.33.4/prometheus-2.33.4.linux-amd64.tar.gz

# tar -xf prometheus-2.33.4.linux-amd64.tar.gz 
# ln -sv /usr/local/src/prometheus-2.33.4.linux-amd64 /usr/local/prometheus
'/usr/local/prometheus' -> 'prometheus-2.33.4.linux-amd64
# cd /usr/local/prometheus

# cat /etc/systemd/system/prometheus.service 
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


# systemctl start prometheus
# systemctl enable prometheus
```

## 10.2: 部署node_exporter:

Node节点（被监控节点）分别部署node_exporter(exporter)

```bash
# wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
#  tar zxvf node_exporter-1.3.1.linux-amd64.tar.gz
# ln -sv /usr/local/src/node_exporter-1.3.1.linux-amd64 /usr/local/exporter
'/usr/local/exporter' -> '/usr/local/src/node_exporter-1.3.1.linux-amd64'

#vim /etc/systemd/system/node-exporter.service

[Unit]
Description=Prometheus Node Exporter
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/exporter/node_exporter
Restart=on-failure

[Install]
WantedBy=multi-user.target

# systemctl daemon-reload && systemctl restart node-exporter && systemctl enable node-exporter.service
```



验证数据：



![](/images/posts/11_prometheus/143.png)

## 10.3: 配置联邦server监控node_exporter

分别在联邦节点1监控node1，在联邦节点2监控node2

```bash
prometheus联邦节点1：
# vim prometheus.yml
- job_name: "prometheus-node1"
    static_configs:
      - targets: ["192.168.48.166:9100"]
# systemctl restart prometheus.service
```

验证数据：



![](/images/posts/11_prometheus/144.png)



## 10.4：prometheus server采集联邦server:

```bash
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]

  - job_name: 'prometheus-federate-165'
    scrape_interval: 10s
    honor_labels: true
    metrics_path: '/federate'
    params:
      'match[]':
       - '{job="prometheus"}'
       - '{__name__=~"job:.*"}'
       - '{__name_=~"node.*"}'
    static_configs:
    - targets:
      - '192.168.48.165:9090' 

  - job_name: 'prometheus-federate-170'
    scrape_interval: 10s
    honor_labels: true
    metrics_path: '/federate'
    params:
      'match[]':
       - '{job="prometheus"}'
       - '{__name__=~"job:.*"}'
       - '{__name_=~"node.*"}'
    static_configs:
    - targets:
      - '192.168.48.170:9090'
#systemctl restart prometheus.service
```



## 10.5: 验证prometheus server:

### 10.5.1: 验证数据收集状态：



![](/images/posts/11_prometheus/155.png)



### 10.5.2：验证指标数据：



# 11: prometheus存储系统：

prometheus有着非常高效的时间序列数据存储方法，每个采样数据仅仅占用2.5byte左右空间，上百万条时间序列，30秒间隔，保留60天，大概200多G空间



## 11.1：Prometheus本地存储简介：



![](/images/posts/11_prometheus/156.png)



```bash
默认情况下，Prometheus将采集到的数据存储在本地TSDB数据库中，路径默认为Prometheus安装目录的data目录，数据写入过程为先把数据写入wal日志并放在内存中，然后2小时候后将内存数据保存至一个新的block块，同时在八新采集的数据写入内存并在2小时候在保存一个新的block块，以此类推
```

### 11.1.1: block简介：

每个block为一个data目录中以01开头的存储目录，如下：

```bash
# ls -l data/
总用量 4
drwxr-xr-x 3 root root  4096 9月  18 19:00 01GD8437KP8V1MZDZYTK2ZFPAG #block
drwxr-xr-x 3 root root  4096 9月  20 20:52 01GDDFAR1KFZ85PH20KWMYYY44 #block
drwxr-xr-x 3 root root  4096 9月  23 13:51 01GDMECMES2TSXHE8VKK0JF2B7 #block
drwxr-xr-x 3 root root  4096 9月  23 13:51 01GDMED2CVHVXCC4KKWY1377GH #block
```

### 11.1.2: block的特性：

```bash
block会压缩、合并历史数据块，以及删除过期的块，随着压缩、合并，block的数据量会减少，在压缩过程中会发生三件事：定期执行压缩、合并小的block到大的block、清理过期的块
```



每个块有4个部分组成：

```bash
# tree data/01GD8437KP8V1MZDZYTK2ZFPAG
data/01GD8437KP8V1MZDZYTK2ZFPAG
├── chunks
│   └── 000001  #数据目录，每个大小为512MB超过会被切分为多个
├── index	#索引文件，记录存储的数据索引信息，通过文件内的几个表来查找时许数据
├── meta.json	#block元数据信息，包含了样本数、采集数据数据的起始时间、压缩历史
└── tombstones	#逻辑数据，主要记载删除记录和标记要数据的内容，删除标记，可在查询块是排查样本

```

### 11.1.3：本地存储配置参数：

```bash
--config.file="prometheus.yml" #指定配置文件
--web.listen-address="0.0.0.0:9090" #指定监听地址
--storage.tsdb.path="data/" #指定数据存储目录
--storage.tsdb.retention.size=STORAGE.TSDB.RETENTION.SIZE #指定chunk小大，默认512MB
--storage.tsdb.retention.time= #数据保存时长，默认15天
--query.timeout=2m  #最大查询超时时间
--query.max-concurrency=20 #最大查询并发数

--web.read-timeout=5m #最大空闲超时时间
--web.max-connections=512 #最大并发连接数
--web.enable-lifecycle #启用API动态加载配置功能
```



## 12: k8s部署proemtheus告警平台

## 12.1: 环境准备：

#### 12.1.1：nfs部署

```bash
1.创建共享目录
mkdir /nfs/data/
2.安装依赖包
yum -y install nfs-utils
3.修改/etc/exports文件，将需要共享的目录和客户添加进来
cat >> /etc/exports << EOF
/nfs/data/ *(rw,sync,no_subtree_check,no_root_squash        # *代表所用IP都能访问
EOF
4.启动nfs服务，
systemctl start nfs

5.启动完成后，让配置生效
exportfs –r
 
#查看验证
exportfs
```



#### 12.1.2：部署prometheus

```bash
1.创建目录：
1.创建目录
mkdir /apps/{prometheus,grafana,alertmanager,node-exporter} -p
 
2.创建命名空间
kubectl create ns monitoring
 
3.创建PV持久化存储卷            
cat >>/apps/prometheus/prometheus-pv.yaml << EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: prometheus-data-pv    
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  nfs:
    path: /nfs/data/prometheus_data
    server: 192.168.1.69
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: prometheus-data-pvc
  namespace: monitoring
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
 
4.创建RBAC
cat >> /apps/prometheus/prometheus-rbac.yaml << EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
  namespace: monitoring
---
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: monitor-token
  namespace: monitoring
  annotations:
    kubernetes.io/service-account.name: "prometheus"
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: prometheus
rules:
- apiGroups:
  - ""
  resources:
  - nodes
  - services
  - endpoints
  - pods
  - nodes/proxy
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - "extensions"
  resources:
    - ingresses
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - configmaps
  - nodes/metrics
  verbs:
  - get
- nonResourceURLs:
  - /metrics
  verbs:
  - get
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
- kind: ServiceAccount
  name: prometheus
  namespace: monitoring
EOF
 
5.创建Prometheus监控配置文件
cat >> /apps/prometheus/prometheusconfig.yaml << EOF
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
    app: prometheus
  name: prometheus-config
  namespace: monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      scrape_timeout: 10s
      evaluation_interval: 1m
    alerting:
      alertmanagers:
      - static_configs:
        - targets: ["192.168.1.188:30093"]
    rule_files:
      - /etc/prometheus/rules/*.yml
    scrape_configs:
    - job_name: 'prometheus'
      static_configs:
      - targets: ['localhost:9090']
    ##kuberntes-api服务发现
    - job_name: kubernetes-apiserver
      kubernetes_sd_configs:
      - role: endpoints
      scheme: https
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: default;kubernetes;https
     #kubernetes-coredns服务发现
    - job_name: "kubernetes-coredns"
      kubernetes_sd_configs:
      - role: endpoints
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: kube-system;kube-dns;metrics
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_scrape"]	#
        regex: true
        action: keep
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_scheme"]
        regex: (https?)
        action: replace
        target_label: __scheme__
      - source_labels: ["__meta_kubernetes_service_annotation_prometheus_io_path"]
        regex: (.+)
        action: replace
        target_label: __metrics_path__
      - source_labels: ["__address__", "__meta_kubernetes_service_annotation_prometheus_io_port"]
        regex: ([^:]+)(?::\d+)?;(\d+)
        action: replace
        target_label: __address__
        replacement: $1:$2
      - regex: __meta_kubernetes_service_label_(.+)
        action: labelmap
      - source_labels: ["__meta_kubernetes_service_name"]
        action: replace
        target_label: kubernetes_service_name
      - source_labels: ["__meta_kubernetes_namespace"]
        action: replace
        target_label: kubernetes_namespace
    #kubernetes-node服务发现
    - job_name: "kubernetes-nodes"
      kubernetes_sd_configs:
      - role: node
      relabel_configs:
      - source_labels: ["__address__"]
        regex: '(.*):10250'
        action: replace
        target_label: __address__
        replacement: '${1}:9100'
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
    #kubernetes-cadvisor服务发现
    - job_name: kubernetes-node-cadvisor
      kubernetes_sd_configs:
      - role: node
      scheme: https
      tls_config:
        ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
      bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
      relabel_configs:
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
      - target_label: __address__
        replacement: kubernetes.default.svc:443
      - source_labels: [__meta_kubernetes_node_name]
        regex: (.+)
        target_label: __metrics_path__
        replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
    #kubernetes-consul服务发现
    - job_name: "consul-node"
      consul_sd_configs:
      - server: '192.168.1.145:8500'
        services: ["harbor-144","hengshi-69","kvm-185","consul-145"]
    - job_name: "consul-etcd"
      consul_sd_configs:
      - server: "192.168.1.145:8500"
        services: ["etcd"]
    - job_name: "haproxy"
      consul_sd_configs:
      - server: "192.168.1.145:8500"
        services: ["haproxy"]        
    - job_name: "cluster-elasticsearch"
      consul_sd_configs:
      - server: "192.168.1.145:8500"
        services: ["cluster-es"]        
    #kubernetes-service-endponts
    - job_name: "kubernetes-kube-metrics"
      kubernetes_sd_configs:
      - role: endpoints
      relabel_configs:
      - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
        action: keep
        regex: kube-system;kube-state-metrics;kube-state-metrics
      - action: labelmap
        regex: __meta_kubernetes_node_label_(.+)
    #kuberentes-bloackbox
    - job_name: 'http_status'
      metrics_path: /probe
      params:
        module: [http_2xx]
      static_configs:
      - targets: ['https://www.aeotrade.com','https://hmmtest.aeotrade.com']
        labels:
          instance: http_status
      relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: url
      - target_label: __address__
        replacement: 192.168.1.145:9115
    - job_name: 'port_status'
      metrics_path: /probe
      params:
        module: [tcp_connect]
      static_configs:
      - targets: ['39.103.146.153:80','192.168.1.188:6443']
        labels:
          instance: 'port_status'
      relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: ip
      - target_label: __address__
        replacement: 192.168.1.145:9115

 
6.创建Prometheus告警配置文件
cat >> /apps/prometheus/prometheus-rules.yaml  << EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-rules
  namespace: monitoring
data:
  node.yml: |
    groups:
    - name: node_instance
      rules:
      - alert: Server Status
        expr: up{instance=~'.*'} == 0
        for: 1m
        labels:
          severity: critical
          level: 3
        annotations:
          description: "Host {{ $labels.instance }}: Instance Down"
          summary: "Instance Down" 
 
      - alert: Cpu_user_hight
        expr: (1 - avg(rate(node_cpu_seconds_total{mode="idle"}[2m])) by (instance,job,env)) * 100 > 90
        for: 1m
        labels:
          severity: critical
          level: 2
        annotations:
          description: Host {{ $labels.instance }} Excessive CPU utilization
          summary: Host {{ $labels.instance }} of {{ $labels.job }} Excessive cpu utilization
 
      - alert: Mem_user_Hight
        expr: (1 - (node_memory_MemAvailable_bytes{} / (node_memory_MemTotal_bytes{})))* 100 > 80
        for: 1m
        labels:
          severity: critical
          level: 2
        annotations:
          description: "Host {{ $labels.instance }} : Memory used {{$value}}%"
          summary: "Memory Used High"

      - alert: 磁盘容量
        expr: round(100-(node_filesystem_free_bytes{fstype=~"ext4|xfs"}/node_filesystem_size_bytes {fstype=~"ext4|xfs"}*100))  > 80
        for: 2s
        labels:
          severity: critical
          level: 3
        annotations:
          summary: "{{$labels.mountpoint}} 磁盘分区使用率过高！"
          description: "{{$labels.mountpoint }} 磁盘分区使用大于80%(目前使用:{{$value}}%)"

      - alert: "IO使用率过高"
        expr: 100-(avg(irate(node_disk_io_time_seconds_total[1m])) by(instance)* 100) < 60
        for: 1m
        labels:
          severity: warning
        annotations:
          description: "当前使用率{{ $value }}%"
          summary: "IO使用率过高"
      - alert: "网络下载速率过高"
        expr: round(irate(node_network_receive_bytes_total{instance!~"data.*",device!~'tap.*|veth.*|br.*|docker.*|vir.*|lo.*|vnet.*'}[1m])/1024) > 2048
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "网络下载率过高"
          description: "当前速率{{ $value }}KB/s"

 
6.创建Prometheus
cat >> /apps/prometheus/prometheus.yaml  << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-server
  namespace: monitoring
  labels:
    app: prometheus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
      component: server
  template:
    metadata:
      labels:
        app: prometheus
        component: server
      annotations:
        prometheus.io/scrape: 'true'
    spec:
            #      nodeName: k8s-node-3
      serviceAccountName: prometheus
      containers:
      - name: prometheus
        image: harbor.aeotrade.net/prom/prometheus:v2.31.2 
        imagePullPolicy: IfNotPresent
        command:
          - prometheus
          - --config.file=/etc/prometheus/prometheus.yml
          - --storage.tsdb.path=/app/prometheus/data
          - --storage.tsdb.retention=720h
          - --web.external-url=http://192.168.1.188:30090
        ports:
        - containerPort: 9090
          protocol: TCP
        resources:
          requests:
            cpu: 100m
            memory: 30Mi
          limits:
            cpu: 500m
            memory: 500Mi
        volumeMounts:
        - mountPath: /etc/prometheus/prometheus.yml
          name: prometheus-config
          subPath: prometheus.yml
        - mountPath: /app/prometheus/data
          name: data
        - name: prometheus-rules
          mountPath: /etc/prometheus/rules
      volumes:
        - name: prometheus-config
          configMap:
            name: prometheus-config
            items:
              - key: prometheus.yml
                path: prometheus.yml
                mode: 0644
                #        - name: prometheus-storage-volume
                #          hostPath:
                #           path: /data/prometheusdata
                #           type: Directory
        - name: prometheus-rules
          configMap:
            name: prometheus-rules
        - name: data
          persistentVolumeClaim:
            claimName: prometheus-data-pvc

EOF
 
7.创建Prometheus svc
cat >> /usr/local/src/monitoring/prometheus/prometheus-svc.yaml << EOF
apiVersion: v1
kind: Service
metadata:
  name: prometheus
  namespace: monitoring
  labels:
    app: prometheus
spec:
  type: NodePort
  ports:
  - name: http
    port: 9090
    targetPort: 9090
    nodePort: 30090
    protocol: TCP
  selector:
    app: prometheus
    component: server

EOF
 
8.创建Prometheus-ingress
cat >> /apps/prometheus/prometheus-ingress.yaml << EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: prometheus
  namespace: monitoring
spec:
  rules:
    - host: prometheus.com
      http:
        paths:
        - path: /
          backend:
            serviceName: prometheus
            servicePort: http
EOF
 
9.部署pro
metueus
kubectl apply -f prometheus-ingress.yaml
```

#### 12.1.3: 部署grafana：

```bash
1.创建pv持久化存储卷
cat >>/apps/grafana/grafana-pv.yaml << EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: grafana-data-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  nfs:
    path: /nfs/data/grafana_data
    server: 192.168.1.69
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: grafana-data-pvc
  namespace: monitoring
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

EOF
 
2.创建grafana服务
cat >>/apps/grafana/grafana.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      securityContext:
        runAsUser: 472
        fsGroup: 472
      containers:
      - name: grafana
        image: harbor.aeotrade.net/prom/grafana:8.3.7
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 3000
          protocol: TCP
        env:
        - name: GF_SECURITY_ADMIN_USER
          value: "admin"
        - name: GF_SECURITY_ADMIN_PASSWORD
          value: "admin"
        resources:
          limits:
            cpu: 100m
            memory: 256Mi
          requests:
            cpu: 100m
            memory: 256Mi
        volumeMounts:
        - name: grafana-data
          mountPath: /var/lib/grafana
          subPath: grafana
      volumes:
      - name: grafana-data
        persistentVolumeClaim:
          claimName: grafana-data-pvc
EOF
 
3.创建grafana-svc
cat >>/apps/grafana/grafana-svc.yaml << EOF
apiVersion: v1
kind: Service
metadata:
  name: grafana
  namespace: monitoring
spec:
  ports:
  - name: http
    port: 3000
    targetPort: 3000
    nodePort: 33000
  type: NodePort
  selector:
    app: grafana

EOF
 
3.创建grafana-ingress
cat >>/apps/grafana/grafana-ingress.yaml << EOF
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: grafana
  namespace: monitoring
spec:
  rules:
    - host: grafana.com
      http:
        paths:
        - path: /
          backend:
            serviceName: grafana
            servicePort: http
EOF
 
4.部署grafana
kubecatl apply -f /apps/grafana/
```

#### 12.1.4: 部署alertmanager:

```bash
1.创建pv持久化存储卷
cat >>/apps/alertmanager/alertmanager-pv.yaml << EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: alertmanagert-data-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
  - ReadWriteOnce
  nfs:
    path: /nfs/data/alertmanager_data
    server: 192.168.1.69
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: alertmanager-data-pvc
  namespace: monitoring
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

EOF
 
2.创建alertmangaer邮箱告警文件
cat >>/apps/alertmanager/alertmanager-configmap.yaml << EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: alertmanager
  namespace: monitoring
data:
  alertmanager.yml: |
     global:
       resolve_timeout: 5m
     route:
       group_by: ['alertname']
       group_wait: 30s
       group_interval: 2m
       repeat_interval: 5m
       receiver: 'web.hook'
     receivers:
     - name: 'web.hook'
       webhook_configs:
       - url: 'http://192.168.1.144:8080/prometheusalert?type=dd&tpl=dingding&ddurl=https://oapi.dingtalk.com/robot/send?access_token=928e29bb4519fe478e03c13b18c30d63a25b8c9b218507b84e456009559b0743&at=15553986239' 

EOF
 
3.创建alertmangaer
cat >>/apps/alertmanager/alertmanager.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: alertmanager
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alertmanager
  template:
    metadata:
      labels:
        app: alertmanager
    spec:
      containers:
      - name: alertmanager
        image: harbor.aeotrade.net/prom/altermanager:latest
        args:
          - "--config.file=/etc/alertmanager/alertmanager.yml"
          - "--storage.path=/data"
        resources:
          limits:
            cpu: 10m
            memory: 50Mi
          requests:
            cpu: 10m
            memory: 50Mi
        ports:
        - name: alertmanager
          containerPort: 9093
        volumeMounts:
        - name: alertmanager-cm
          mountPath: /etc/alertmanager
        - name: storage-volume
          mountPath: "/data"
          subPath: ""
      volumes:
      - name: alertmanager-cm
        configMap:
          name: alertmanager 
      - name: storage-volume
        persistentVolumeClaim:
          claimName: alertmanager-data-pvc


EOF
 
4.创建alertmangaer-svc
cat >>/apps/alertmanager/alertmanager-svc.yaml << EOF
---
apiVersion: v1
kind: Service
metadata:
  name: alertmanager
  namespace: monitoring
spec:
  type: NodePort
  ports:
  - name: http
    port: 9093
    targetPort: 9093
    nodePort: 30093
  selector:
    app: alertmanager
 
4.创建alertmangaer-ingress
cat >> /apps/alertmanager/alertmanager-ingress.yaml << EOF
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: alertmanager
  namespace: monitoring
spec:
  rules:
    - host: alertmanager.com
      http:
        paths:
        - path: /
          backend:
            serviceName:alertmanager
            servicePort: http
EOF
 
5.部署alertmanager
kubectl apply -f /apps/alertmanager/
```

#### 12.1.5: 部署node-exporter:

```bash
cat >>/apps/node-exporter/node-exporter-ds.yaml << EOF
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
  namespace: monitoring 
  labels:
    k8s-app: node-exporter
spec:
  selector:
    matchLabels:
        k8s-app: node-exporter
  template:
    metadata:
      labels:
        k8s-app: node-exporter
    spec:
      tolerations:
        - effect: NoSchedule
          key: node-role.kubernetes.io/master
      containers:
      - image: harbor.aeotrade.net/prom/node-exporter:v1.3.1 
        imagePullPolicy: IfNotPresent
        name: prometheus-node-exporter
        ports:
        - containerPort: 9100
          hostPort: 9100
          protocol: TCP
          name: metrics
        volumeMounts:
        - mountPath: /host/proc
          name: proc
        - mountPath: /host/sys
          name: sys
        - mountPath: /host
          name: rootfs
        args:
        - --path.procfs=/host/proc
        - --path.sysfs=/host/sys
        - --path.rootfs=/host
      volumes:
        - name: proc
          hostPath:
            path: /proc
        - name: sys
          hostPath:
            path: /sys
        - name: rootfs
          hostPath:
            path: /
      hostNetwork: true
      hostPID: true
---
apiVersion: v1
kind: Service
metadata:
  annotations:
    prometheus.io/scrape: "true"
  labels:
    k8s-app: node-exporter
  name: node-exporter
  namespace: monitoring
spec:
  type: NodePort
  ports:
  - name: http
    port: 9100
    nodePort: 30009
    protocol: TCP
  selector:
    k8s-app: node-exporter

#部署
kubectl apply -f /apps/node-exporter/node-exporter-ds.yaml
```

#### 12.1.6: 部署PrometheusAlert:

```bash
wget https://github.com/feiyu563/PrometheusAlert/releases/download/v4.7/linux.zip && unzip linux.zip &&cd linux/
chmod +x PrometheusAlert
nohup ./PrometheusAlert &
```

自定义模板

![](/images/posts/11_prometheus/157.png)



```bash
{{ $var := .externalURL}}{{ range $k,$v:=.alerts }}
{{if eq $v.status "resolved"}}
#### [Prometheus恢复信息]({{$v.generatorURL}})

##### <font color="#02b340">【告警名称】</font>：[{{$v.labels.alertname}}]({{$var}})🔥{{if $v.labels.severity}}
##### <font color="#02b340">【告警级别】</font>：{{if eq $v.labels.severity "0"}}提示{{else if eq $v.labels.severity "1"}}警告{{else if eq $v.labels.severity "2"}}一般严重{{else if eq $v.labels.severity "3"}}严重{{else if eq $v.labels.severity "4"}}灾难{{else}}{{$v.labels.severity}}{{end}}{{end}}
##### <font color="#02b340">【当前状态】</font>：**<font color="#67C23A" size=4>已恢复</font>**
##### <font color="#02b340">【触发时间】</font>：{{GetCSTtime $v.startsAt}}
##### <font color="#02b340">【恢复时间】</font>：{{GetCSTtime $v.endsAt}}
##### <font color="#02b340">【告警IP】 </font>：{{$v.labels.instance}}
##### <font color="#02b340">【告警主机】</font>：{{$v.labels.nodename}}
##### =======<font color="info">告警详情</font>=======
**<font color="#FF0000">告警描述</font>：{{$v.annotations.summary}}**
**<font color="#FF0000">告警内容</font>：{{$v.annotations.description}}**
[prometheus](http://192.168.1.1188:30090/alerts) | 📈[grafana](http://192.168.1.188:33000) | 🚨[alertmanager](http://192.168.1.145:30093)
![aeotrade](https://www.aeotrade.com/_nuxt/img/mainLogo.284fc34.png)
{{else}}
#### [Prometheus告警信息]({{$v.generatorURL}})

##### <font color="#FF0000">【告警名称】</font>：[{{$v.labels.alertname}}]({{$var}})✅{{if $v.labels.severity}}
##### <font color="#02b340">【告警级别】</font>：{{if eq $v.labels.severity "0"}}提示{{else if eq $v.labels.severity "1"}}警告{{else if eq $v.labels.severity "2"}}一般严重{{else if eq $v.labels.severity "3"}}严重{{else if eq $v.labels.severity "4"}}灾难{{else}}{{$v.labels.severity}}{{end}}{{end}}
##### <font color="#FF0000">【当前状态】</font>：**<font color="#E6A23C">需要处理</font>**
##### <font color="#02b340">【告警IP】 </font>：{{$v.labels.instance}}
##### <font color="#02b340">【告警主机】</font>：{{$v.labels.nodename}}
##### =======<font color="#FF0000">告警详情</font>=======
**<font color="#FF0000">告警描述</font>：{{$v.annotations.summary}}**
**<font color="#FF0000">告警内容</font>：{{$v.annotations.description}}**
[prometheus](http://192.168.1.1188:30090/alerts) | 📈[grafana](http://192.168.1.188:33000) | 🚨[alertmanager](http://192.168.1.145:30093)
![aeotrade](https://www.aeotrade.com/_nuxt/img/mainLogo.284fc34.png)
{{end}}
{{ end }}
```



### 12.2: 数据验证：

### 12.2.1：prometheus验证：



![](/images/posts/11_prometheus/158.png)

### 12.2.2：grafana:导入模块验证：

apiserver-12006

cadvisor-14282

coredns-14981

node-11074

kube-metrics-14518



![](/images/posts/11_prometheus/159.png)



### 12.2.3: altermanager数据验证:



![](/images/posts/11_prometheus/160.png)