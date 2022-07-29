# 一：kubernetes简介：

kubernetges最初源于谷歌内部的Borg,Borg是副歌内部的大规模集群管理系统，负载对谷歌内部很多核心服务的调度和管理，Borg的目的是让用户能够不必操心资源管理的问题，让他们专注于自己的核心业务，并且做到跨多个数据中心的资源利用率最大化

Borg主要由BorgMaster、Borglet、Bprgcfg和Scheduler组成

**功能特性：**

• 自动化容器部署与复制

• 随时扩展或收缩容器规模

• 组织容器成组，提供容器间的负载均衡

• 快速更新及回滚容器版本

• 提供弹性伸缩，如果某个容器失效就进行替换

https://kubernetes.io/zh/#官网

https://github.com/kubernetes/kubernetes #github

https://landscape.cncf.io/   CNCF网址

**架构图**

![](/images/posts/02_k8s/02/1.png)

## 1.1：kubernetes组件简介

kube-apiserver

https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-apiserver

Kubernetes API server提供了K8s各类的资源对象的增删改查及HTTP Rest接口，这些对象包括pods、services、replicationcontrollers等、API Server为REST操作提供服务，并为集群的共享状态提供前端，所有其他组件都通过前端进行交互

RESTful API

是REST风格的网络接口，REST描述的是在网络中client和server的一种交互形式

### 1.1.1：kubernetes API Server简介

* 改端口默认值为6443，可通过启动参数--secure-port的值来修改默认值
* 默认IP地址为非本地（Non-Localhost）网络端口，通过启动参数--bind-address设置该值
* 改端口用于接收客户端、dashboard等外部HTTPS请求
* 用于基于Tocken文件活客户端证书及HTTP Base的认证
* 用于基于策略的授权

### 1.1.2: kube-scheduler

https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-scheduler

* kubernetes调度器是一个控制面进程，负责将Pods指派到节点上
* 通过调度算法为待调度Pod列表的每个Pod从可用Node列表中选择一个最适合的Node，并将信息写入etcd中
* node节点上的kubelet通过API Server监听到kubernegtes Scheduler产生的Pod绑定信息，然后获取对应的Pod清单，下载image，并启动容器
* 策略
* LeastRequestedPriority
* 优先从备选节点列表中选择资源消耗最小的节点（CPU+内存）
* CalculateNodelabelPriority
* 优先选择含有指定Label的节点
* BalancedResourceALLocation
* 优先从备选节点列表中选择各项资源使用率最均衡的节点

第一步创建pod

![](/images/posts/02_k8s/02/4.png)

第二部：过滤掉资源不足的节点

![](/images/posts/02_k8s/02/5.png)

第三步：在剩余可用的节点中进行删选

![](/images/posts/02_k8s/02/6.png)

第四步：选中节点

![](/images/posts/02_k8s/02/7.png)



![](/images/posts/02_k8s/02/8.png)

### 1.1.3：pod

Pod是k8s进行资源调度的最小单位，每个Pod中运行着一个或多个密切相关的业务容器，这些业务容器共享这个Pause容器的IP和Volume，我们以这个不易死亡的Pause容器作为Pod的根容器，以它的状态表示整个容器组的状态。一个Pod一旦被创建就会放到Etcd中存储，然后由Master调度到一个Node绑定，由这个Node上的Kubelet进行实例化。

每个Pod会被分配一个单独的Pod IP，Pod IP + ContainerPort 组成了一个Endpoint



### 1.1.4：Service

service其功能使应用暴露，Pods 是有生命周期的，也有独立的 IP 地址，随着 Pods 的创建与销毁，一个必不可少的工作就是保证各个应用能够感知这种变化。这就要提到 Service 了，Service 是 YAML 或 JSON 定义的由 Pods 通过某种策略的逻辑组合。更重要的是，Pods 的独立 IP 需要通过 Service 暴露到网络中。

![](/images/posts/02_k8s/02/2.png)

### 1.1.5: master

Master节点上面主要由四个模块组成：APIServer、scheduler、controller manager、etcd

• APIServer:APIServer 负责对外提供RESTful的Kubernetes API服务，它是系统管理指令的统一入口，任何对资源进行增删改查的操作都要交给APIServer处理后再提交给etcd。如架构图中所示，kubectl（Kubernetes提供的客户端工具，该工具内部就是对Kubernetes API的调用）是直接和APIServer交互的。

• schedule:scheduler的职责很明确，就是负责调度pod到合适的Node上。如果把scheduler看成一个黑匣子，那么它的输入是pod和由多个Node组成的列表，输出是Pod和一个Node的绑定，即将这个pod部署到这个Node上。Kubernetes目前提供了调度算法，但是同样也保留了接口，用户可以根据自己的需求定义自己的调度算法。

• controller manager:如果说APIServer做的是“前台”的工作的话，那controller manager就是负责“后台”的。每个资源一般都对应有一个控制器，而controller manager就是负责管理这些控制器的。比如我们通过APIServer创建一个pod，当这个pod创建成功后，APIServer的任务就算完成了。而后面保证Pod的状态始终和我们预期的一样的重任就由controller manager去保证了。

![](/images/posts/02_k8s/02/3.png)



### 1.1.6: etcd

etcd是一个高可用的键值存储系统，Kubernetes使用它来存储各个资源的状态，从而实现了Restful的API。

### 1.1.7: Node

每个Node节点主要由三个模块组成：kubelet、kube-proxy、runtime。

runtime指的是容器运行环境，目前Kubernetes支持docker和rkt两种容器。

• kube-proxy:该模块实现了Kubernetes中的服务发现和反向代理功能。反向代理方面：kube-proxy支持TCP和UDP连接转发，默认基于Round Robin算法将客户端流量转发到与service对应的一组后端pod。服务发现方面，kube-proxy使用etcd的watch机制，监控集群中service和endpoint对象数据的动态变化，并且维护一个service到endpoint的映射关系，从而保证了后端pod的IP变化不会对访问者造成影响。另外kube-proxy还支持session affinity。

• kubelet:Kubelet是Master在每个Node节点上面的agent，是Node节点上面最重要的模块，它负责维护和管理该Node上面的所有容器，但是如果容器不是通过Kubernetes创建的，它并不会管理。本质上，它负责使Pod得运行状态与期望的状态一致。

etcd:etcd是一个高可用的键值存储系统，Kubernetes使用它来存储各个资源的状态，从而实现了Restful的API