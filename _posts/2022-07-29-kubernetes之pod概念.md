# 一：kubernetes-Pod概念

## 1.2：Pod类型

- **自主式****Pod**

-  **控制器管理的****Pod**

   

  在同一个pod中，两个容器共享puse存储，端口不能冲突

  在同一个Pod中，既共享网络又共享存储



## 1.3：Pod控制器类型

- RelicationController     && RelicaSet & Deployment 
- StatefullSet
- DaemonSet
- Job , Cronjob



ReplicationController 用来确保容器应用的副本数始终保持在用户定义的副本数，既如果有容器异常退出，会自动创建新的Pod来替代，而如果异常多出来的容器也会自动回收，在新版本的kubernetes中建议使用RelicaSert来取代RelicationControlle

RelicaSet 跟ReplicationController 没有本之的不同，只是名字不一样，并且ReplicaSet支持集合式的Selector

虽然RelicaSet可以独立使用，但一般还是建议使用Deployment来自动管理ReplicaSet,这样就无需担心跟其他机制的不兼容问题（比如ReplicaSet 不支持rolling-update但Deployment 支持）

## 1.4：HPA（HorizontaIPodAtuoScale）

Horizontal Pod Autoscaling 仅适用于Deployment 和ReplicaSet ，在V1版本中仅支持根据Pod的CPU利用率扩缩容，在vlalpha版本中，支持根据内存和用户自定义的metric扩缩容



## 1.5：StatefulSet

statefulSet是为了解决有状态服务的问题（对应Deployment和ReplicaSet是无状态服务而设计）其应用场景包括：

- 稳定的持久化存储，既Pod重新调度后还是能访问岛相同的持久化数据，基于PVC来实现
- 稳定的网络标志，既Pod重新调度后其Podname和Hostname不变，基于Headless Service (既没有Cluster Ip 的Service)来实现
-  有序部署，有序扩展，既Pod是有顺序的，在部署或者扩展的时候要依据定义的顺序依次依次进行（既从0到N-1，在下一个Pod运行之前所有之前的Pod必须是Running和Ready状态），基于init     containers来实现
- 有序收缩，有序删除（既从N-1到0）

## 1.6：DaemonSet

DaemonSet 确保全部（或者一些）Node上运行一个Pod的副本，当有Node加入集群，也会为他们新增一个Pod,当有Node从集群移除时，这些Pod也会被回收，删除DaemonSet将会删除它创建的所有Pod

 

使用DaemonSet的一些典型用法：

- 运行集群存储daemon，例如在每个Node上运行glusterd、ceph
- 在每个Node上运行日志收集daemon，例如fluentd\logstash
- 在每个node上运行监控daemon，例如Prometheus Node     exporter



## 1.7：Job,Cronjob

Job负责批处理任务，既仅执行一次的任务，它保存批处理任务的一个或多个Pod成功结束

 

Cron Job 管理基于时间的Job，既：

- 在给定时间点运行一次
- 周期性的在给顶时间点运行



# 二：服务发现:



![](/images/posts/02_k8s/03/1.png)



# 三：网络通讯方式

## 3.1：网络通讯模式

Kubernetes 的网络模型假定了所有Pod都在一个可以直连通的扁平网络空间中，这在GCE（Google Compute Engine） 里面时现成的网络模型，Kubernetes假定这个网络已经存在。而在私有云里搭建kubernetes集群，就不能假定这个网络已经存在了，我们需要自己实现这个网络假设，将不同节点上的Docker容器之间的互相访问先打通，然后运行kubernetes 

同一个Pod内的多个容器之间;Io

个Pod之间的通讯： Overlay Network

Pod 与Service之间的通讯；个节点的IPtables规则



## 3.2：网络解决方案kubernetes+Flannel

Flannel是CoreOs团队针对kubernetes设计的一个网络规划服务，简单来说，它的功能是让集群中的不通节点主机创建的Docker容器都具有全集群唯一的虚拟IP地址，而且它还能在这些IP地址之间建立一个覆盖网络（Overlay Network）通过这个覆盖网络，将数据包原封不动的传递到目标容器内



![](/images/posts/02_k8s/03/2.png)



ETCD之Flannel提供说明：

- 存储管理Flannel 可分配的IP地址段资源
- 监控ETCD中每个POD的实际地址，并在内存中建立维护POD节点路由表



同一个pod内部通讯：同一个pod共享同一个网络命名空间，共享同一个linux协议栈

 

Pod1至pod2 

- Pod1于Pod2不在同一台主机，pod的地址是与docker0在同一个网段的，但docker0网段于宿主机网卡是两个不同的IP网段，并且不同node之间的通信只能通过宿主机的物理网卡进行，将pod的IP和所在Node的IP关联起来，通过这个关联让pod可以互相访问
- Pod1与pod2在同一台机器，由Docker0网桥直接转发至pod2，不需要经过flannel 

pod至Service的网络： 目前基于性能考虑，全部为iptable维护和转发

 

pod到外网：Pod向外网发送请求，查找路由表，转发数据包到宿主机的网卡，宿主网卡完成路由选择后，iptables执行Masauerade 把源IP更改为网卡的IP，然后向外网服务器发送请求

 

外网访问 Pod： Service

![](/images/posts/02_k8s/03/3.png)



