---
title: Kubernetes
date: 2021-03-21 11:29:17
categories: 运维
top: false
summary: Kubernetes
tags: 
 - 运维
---

### Kebuernetes

* Kubernetes 看作是用来是一个部署镜像的平台
* 可以用来操作多台机器调度部署镜像
* 在`Kubernetes`中，可以使用集群来组织服务器的，集群中会存在一个`Master`节点，该节点是`Kubernetes`集群的控制节点，负责调度集群中其他服务器的资源，其他节点被称为`Node`

### 基础安装

> Master & Node 节点都需要安装

#### 一、安装必备组件

* `vim`是Linux下的一个文件编辑器
* `wget`可以用作文件下载使用
* `ntpdate`则是可以用来同步时区

```bash
yum install vim wget ntpdate -y
```

#### 二、关闭防火墙

> Kubernetes 会创建防火墙规则，先关闭`firewalld`

```bash
systemctl stop firewalld & systemctl disable firewalld 
```

#### 三、关闭Swap分区

* Swap 是Linux 的交换分区，在系统资源不足时，Swap分区会启用，这个我们不需要
* 应该让新创建的服务自动调度到集群的其他 Node 节点中去，而不是使用 Swap 分区

```bash
# 临时关闭
swapoff -a
```

#### 四、关闭Selinux

* 关闭 Selinux 是为了支持容器可以访问宿主机文件系统

```bash
# 暂时关闭 selinux
setenforce 0

# 永久关闭
vi /etc/sysconfig/selinux
# 修改下参数 设置为disable
SELINUX=disabled
```

#### 五、统一我们的系统时间和时区

* 使用`ntpdate`来统一我们的系统事件和时区，服务器时间与阿里云服务器对齐

```bash
# 统一时区，为上海时区
ln -snf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
bash -c "echo 'Asia/Shanghai' > /etc/timezone"

# 统一使用阿里服务器进行时间更新
ntpdate ntp1.aliyun.com
```

#### 六、安装Docker

* 在`kubernetes`中的组件，服务都可以 Docker 镜像方式部署的，使用需要安装`Docker`
* `device-mapper-persistent-data`：存储启动，Linux上的许多高级卷管理技术
* `lvm`：逻辑卷管理器，用于创建逻辑磁盘分区使用

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2


sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum install docker-ce -y
systemctl start docker
systemctl enable docker

sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://fwvjnv59.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

#### 七、安装 Kubernetes 组件

**切换阿里云源**

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
        http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

```

**安装 Kebernetes组件**

* kubelet 是 Kubernetes 中的核心组件。它会运行在集群的所有节点上，并负责创建启动服务容器
* kubectl 则是 Kubernetes 的命令行工具。可以用来管理，删除，创建资源
* kubeadm 则是用来初始化集群，子节点加入的工具

![k8s-mysql](/medias/imges/yw/k8s-mysql.jpg)

```bash
yum install -y kubelet kubeadm kubectl
# 启动kubelet
systemctl enable kubelet && systemctl start kubelet
```

#### 八、设置 bridge-nf-call-iptables

* 配置内核参数，将桥接的`IPV4`浏览传递到`iptables`链
* 开启`bridge-nf-call-iptables`

```bash
echo 1 > /proc/sys/net/bridge/bridge-nf-call-iptables
```

### Master

> Master 节点是集群内的调度和主要节点

#### 一、修改主机名称为 `master`

```bash
hostnamectl set-hostname master
```

#### 二、配置hosts

```bash
ip addr
vim /etc/hosts

172.31.178.169  master  master
```

#### 三、配置 Kubernetes 初始化文件

* `init-defaults`输出一份默认初始化配置文件

```bash
kubeadm config print init-defaults > init-kubeadm.conf
vim init-kubeadm.conf
```

* 更换 Kubernetes 镜像仓库为阿里云镜像仓库，加速组件拉取
* 替换 ip 为自己的主机 ip
* 配置 pod 网络为 `fiannel`网段
* 为了让集群之间可以互相通信，需要配置子网络，这些在后面的`flannel`网络中需要用到
  * `10.96.0.0/12` 是 `Kubernetes`以内的网络 `pods` 需要的网络
  * `10.244.0.0/16` 是 `Kubernetes`内部的`services`需要的网络

```bash
- imageRepository: k8s.gcr.io 更换k8s镜像仓库
+ imageRepository: registry.cn-hangzhou.aliyuncs.com/google_containers
- localAPIEndpointc，advertiseAddress为master ip ,port默认不修改
localAPIEndpoint:
+ advertiseAddress: 172.31.178.169  # 此处为master的IP
  bindPort: 6443
# 配置子网络
networking:
  dnsDomain: cluster.local
  serviceSubnet: 10.96.0.0/12
+ podSubnet: 10.244.0.0/16    # 添加这个
```

**拉取其它组件**

* `kubeadm`：可以用来拉取我们的默认组件镜像
* `kube-apiserver`：提供接口服务，可以让外网访问集群
* `kube-contoller-manager`：内部的控制指令工具
* `kube-scheduler`：内部的任务调度器
* `kube-proxy`：反向代理和负载均衡，流量转发
* `pause`：进程管理工具
* `etcd`：保持集群内部的数据一致性
* `coredns`：集群内网通信

![kubernetes架构图](/medias/imges/yw/kubernetes.jpg)

```bash
# 查看缺少的组件
kubeadm config images list --config init-kubeadm.conf
# 拉取缺少的组件
kubeadm config images pull --config init-kubeadm.conf
```

#### 四、初始化 Kubernetes

```bash
kubeadm init --config init-kubeadm.conf
```

* `kubeadm join`：可以快速将`Node`节点加入到`Master`集群内
* Master 节点需要执行的初始化命令
* **将默认的 `Kubernetes`认证文件拷贝进`.kube`文件夹内，才能默认使用该配置文件**

```bash
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.178.169:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:8aac19f4dbe68f1e15ba3d80e141acdc912e353f9757ad69187e8fb9780bc975 
```

#### 五、安装`Flannel`

* `flannel`：主要的作用是创建一个虚拟网络，让不同节点下的服务有着全局唯一的`IP`地址，且服务之前可以互相访问和连接
* 集群内网网络通讯协议模式采用了`Flannel`协议

```bash
#wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
wget https://img.zhufengpeixun.com/kube-flannel.yml
docker pull quay.io/coreos/flannel:v0.13.0-rc2
kubectl apply -f kube-flannel.yml
```

```shell
net-conf.json: |
    {
      "Network": "10.244.0.0/16",
      "Backend": {
        "Type": "vxlan"
      }
    }
```

#### 六、查看启动情况

```bash
kubectl get nodes

NAME     STATUS   ROLES                  AGE     VERSION
master   Ready    control-plane,master   9m34s   v1.20.4
```

### Node节点配置

* Node节点的地位则是负责运行服务容器，负责接收调度的
* 先执行基础安装

```bash
hostnamectl set-hostname node
```

#### 一、拷贝Master节点配置文件

* 将 `master`节点的配置文件拷贝 K8s 到 node1节点

```bash
scp $HOME/.kube/config root@172.31.178.170:~/
```

* 在`node1`节点归档配置文件

```bash
mkdir -p $HOME/.kube
sudo mv $HOME/config $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### 二、加入`Master`节点

* 让 Node 节点加入到 master 集群内

```bash
kubeadm join 172.16.81.164:6443 --token abcdef.0123456789abcdef \ --discovery-token-ca-cert-hash sha256:b4a059eeffa2e52f2eea7a5d592be10c994c7715c17bda57bbc3757d4f13903d
```

```bash
- 如果刚才的命令丢了，可以在 master 机器上使用 kubeadm token create 重新生成一条命令
kubeadm token create --print-join-command
```

#### 三、安装Flannel

```bash
scp ~/kube-flannel.yml root@172.31.178.170:~/
kubectl apply -f kube-flannel.yml
```

### 查看状态

```bash 
kubectl get nodes
NAME     STATUS   ROLES                  AGE    VERSION
master   Ready    control-plane,master   24m    v1.20.4
node1    Ready    <none>                 101s   v1.20.4
```

### 直接部署nginx

```bash
kubectl create deployment nginx --image=nginx
[root@master ~]# kubectl expose deployment nginx --port=80 --type=NodePort
service/nginx exposed

kubectl get pod,svc
NAME                            READY   STATUS    RESTARTS   AGE
pod/nginx-6799fc88d8-bt5n6      1/1     Running   0          5m32s

curl 127.0.0.1:32636
//快速扩容为3个副本
[root@master ~]# kubectl scale deployment nginx --replicas=3
deployment.apps/nginx scaled
```

### 通过yaml部署mysql

#### 一、配置文件

```yaml
apiVersion: v1
kind: ReplicationController                           
metadata:
  name: mysql                                          
spec:
  replicas: 1           #Pod副本的期待数量
  selector:
    app: mysql          #符合目标的Pod拥有此标签
  template:             #根据此模板创建Pod的副本（实例）
    metadata:
      labels:
        app: mysql     #Pod副本拥有的标签，对应RC的Selector
    spec:
      containers:      #Pod内容器的定义部分
      - name: mysql            #容器的名称
        image: mysql    #容器对应的Docker image
        ports: 
        - containerPort: 3306       #容器应用监听的端口号
        env:                        #注入容器内的环境变量
        - name: MYSQL_ROOT_PASSWORD 
          value: "123456"
```

#### 二、创建POD

```bash
kubectl create -f mysql-rc.yaml
replicationcontroller/mysql created

kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
mysql         1/1     Running      0      5m56s
```

#### 三、查看状态

```bash
kubectl describe pod mysql
```

### 深入部署Kubernetes

* Master 是控制节点、负责编排、管理、调度用户提交的作业
  * 负责API调度的`kube-apiserver`
  * 负责调度的`kube-acheduler`
  * 负责容器编排的`kube-controller-manager`
  * `kube-apiserver`会处理集群的持久化数据并保存在`etcd`中
* Node是计算节点
  * CRL（Container Runtime Interface）的远程调用接口，这个接口定义了容器运行时的各项核心操作
  * OCL（Open Container Initiactive）容器运行时通过OCI同底层的Linux操作系统进行交互
  * 设备插件是用来管理宿主机物理设备的组件
  * gRPC是可以在任何环境中运行的现代开源高性能PRC框架
  * PRC是指远程过程调用，也就是说两台服务器 A、B，一个应用部署在A服务器上，想要调用B服务器上应用提供的函数/方法，由于不在一个内存空间，不能直接调用，需要通过网络来表达调用的语义和传达调用的数据

![深入kubernetes](/medias/imges/yw/kubenetes1.jpg)

![ingress](/medias/imges/yw/ingress.jpg)

#### Pod

* Pod 是K8S 中最小的可调度单元（可操作/可部署单元）
*  它里面可以包含1个或者多个 Docker 容器
* 在 Pod 内的所有 Docker 容器，都会共享同一个网络、存储卷、端口映射规则
* 一个Pod拥有一个IP，但这个IP会随着Pod的重启、创建、删除等跟着改变，所以不固定且不完全可靠，这也是 Pod的 IP 漂移问题，这个问题我们可以使用下面的`service`去自动映射
* Pod 是一个容器组，里面有很多容器，容器组内共享资源

![POD](/medias/imges/yw/pod.jpg)

#### deployment

* 希望批量启动和管理多个Pod实例，就可以使用deployment

#### service

* 有了Pod实例后就需要以固定的IP地址以及负载均衡的方式访问多个Pod实例，就有了service

###深入部署

#### 编写配置文件

* Kubernetes 最核心的设计理念就是声明式 API
* 声明式 API 可以用来描述容器化业务和容器间关系
* [apiversion](https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html)

```bash
mkdir deployment && cd deployment
vim deployment-user-v1.yaml
```

```yaml
apiVersion: apps/v1  #API 配置版本
kind: Deployment     #资源类型
metadata:
  name: user-v1     #资源名称
spec:
  selector:
    matchLabels:
      app: user-v1 #告诉deployment根据规则匹配相应的Pod进行控制和管理，matchLabels字段匹配Pod的label值
  replicas: 3 #声明一个 Pod,副本的数量
  template:
    metadata:
      labels:
        app: user-v1 #Pod的名称
    spec:   #组内创建的 Pod 信息
      containers:
      - name: nginx #容器的名称
        image: registry.cn-beijing.aliyuncs.com/zhangrenyang/nginx:user-v1 #使用哪个镜像
        ports:
        - containerPort: 80 #容器内映射的端口
```

#### 部署Pod

* `kubectl apply`代表准备对资源进行配置
* `-f`等于 --filename 后面可以跟随多个配置文件

```bash
kubectl apply -f deployment-user-v1.yaml
deployment.apps/user-v1 created
```

* 想查看部署完毕后的 Pod 运行状态，当状态都是`Running`时，代表Pod运行正常

| 字段     | 含义                                    |
| -------- | --------------------------------------- |
| name     | Pod的名称                               |
| READY    | 容器状态，格式为可用容器/所有容器的数量 |
| STATUS   | Pod的运行状态                           |
| RESTARTS | 重启数量                                |
| AGE      | Pod运行时间                             |

```bash
kubectl get pod
NAME                        READY   STATUS    RESTARTS   AGE
user-8445fbf8d7-6f6d7       0/1     ContainerCreating   0          13s
user-8445fbf8d7-nggzv       0/1     ContainerCreating   0          13s
user-8445fbf8d7-xfn52       0/1     ContainerCreating   0    
```

#### service

* deployment 是无状态的
* deployment 并不会对pod进行网络通信和分发
* Pod 的IP在运行时还会经常进行漂移且不固定
* 想访问服务需要使用`service`组织统一的`pod`访问入口
* 可以定义`Service`进行统一组织Pod服务访问
* 负责自动调度和组织deployment中 Pod 的服务访问，由于自动映射 Pod 的IP，同时也解决了 Pod 的IP漂移问题

![k8s-service](/medias/imges/yw/k8s-service.jpg)

**配置文件**

* [Kubernetes的三种外部访问方式](http://www.dockerone.com/article/4884)
* NodePort服务是引导外部流量到你的服务的最原始方式
* NodePort在所有节点上开放一个特定端口,任何发送到该端口的流量都被转发到对应服务

| 字段       | 说明                             |
| ---------- | -------------------------------- |
| protocol   | 通信类型（TCP/UDP）              |
| targetPort | 原本 Pod 开放的端口              |
| port       | Kubernetes容器之间互相访问的端口 |
| type       | NodePort，Service的一种访问方式  |

`user-service-v1.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: service-user-v1
spec:
  selector:
    app: user-v1
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: NodePort
```

**启动**

```bash
kubectl apply -f user-service-v1.yaml
service/service-user-v1 created
```

* 查看当前的服务

```bash
kubectl get svc
```

可以在任何节点上访问

```bash
curl http://172.31.178.169:30859
curl http://172.31.178.170:30859
```

#### ingress

* 我们可能会根据请求路径前缀的匹配，权重，甚至根据`cookie/header`的值去访问不同的服务
* 为了达到这种负载均衡的效果，我们可以使用`Kubernetes`的另一个组件`ingress`
* `ingress-nginx`是基于nginx的一个`ingress`实现
* 可以实现正则匹配路径，流量转发，基于`cookie、header`切分流量（灰度发布）

![POD](/medias/imges/yw/ingress-nginx.jpg)

```bash
#wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/baremetal/deploy.yaml
wget https://img.zhufengpeixun.com/deploy.yaml
```

`vi deploy.yaml `

```yaml
  namespace: ingress-nginx
spec:
  type: NodePort
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
+     nodePort: 31234
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
+     nodePort: 31235

+ image: registry.cn-hangzhou.aliyuncs.com/bin_x/nginx-ingress:v0.34.1@sha256:80359bdf124d49264fabf136d2aecadac729b54f16618162194356d3c78ce2fe
```

* 配置生效，拉取ingress镜像并自动部署ingress

```bash
kubectl apply -f deploy.yaml
```

* 查看pods的部署状态
  * `-n`：指定命名空间查询
  * `-l`：指定label名称查询

```bash
kubectl -n ingress-nginx get svc
NAME                                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             NodePort    10.108.109.94   <none>        80:31234/TCP,443:31235/TCP   16m
ingress-nginx-controller-admission   ClusterIP   10.106.43.59    <none>        443/TCP                      16m
```

```bash
kubectl -n ingress-nginx get svc
NAME                                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             NodePort    10.108.109.94   <none>        80:31234/TCP,443:31235/TCP   17m
ingress-nginx-controller-admission   ClusterIP   10.106.43.59    <none>        443/TCP                      17m
```

* ingress 服务器的配置也是使用`yaml`文件进行管理
* [annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/) 是 ingress 的主要配置项目，可以用来修改这些配置来修改 ingress 的行为。我们可以通过修改这些配置来实现灰度发布，跨域资源，甚至将 [www.abc.com](http://www.abc.com/) 重定向到 abc.com
* rules 是 ingress 配置路径转发规则的地方,当我们去访问 /front 时， ingress 就会帮我们调度到 front-service-v1 这个 service 上面
  * path 可以是一个路径字符串，也可以是一个正则表达式
  * backend 则是 k8s 的 service 服务， serviceName 是服务名称， servicePort 是服务端口
* backend 可以用来给 ingress 设置默认访问的 Service 服务。当请求不匹配 rules 中任何一条规则时，则会去走 backend 中的配置

`vi ingress.yaml`

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    kubernetes.io/ingress.class: nginx
spec:
  rules:
  - http:
      paths: 
       - path: /user
         backend:
          serviceName: user-service-v1
          servicePort: 80
       - path: /pay
         backend:
          serviceName: pay-service-v1
          servicePort: 80    
  backend:
     serviceName: user-service-v1
     servicePort: 80
```

```bash
kubectl apply -f ./ingress.yaml
```

```bash
curl http://172.16.244.93:31234/user
curl http://172.16.244.94:31234/user
curl http://172.16.244.93:31234/pay
curl http://172.16.244.94:31234/pay
```

```bash
kubectl  describe   ingress
```

### 状态查看

```bash
//查看当前的deployment
kubectl get deploy 
//删除deploy 删除后ReplicateSet和pod也没有了
kubectl delete deploy nginx

//查看Replication Controller
kubectl get rc
//删除Replication Controller,删除后Pod也没有了
kubectl delete rc mysql

//查看pod
kubectl get pod
//删除pod
kubectl delete pod mysql-77w7z

//查看服务
kubectl get svc
//删除服务
kubectl delete service nginx

//查看pod详情
kubectl describe pod fail-1034443984-jerry

```

### 发布镜像

```bash
//登录阿里云Docker Registry
sudo docker login --username=hongqishiq@126.com registry.cn-beijing.aliyuncs.com
//从Registry中拉取镜像
docker pull registry.cn-beijing.aliyuncs.com/zhangrenyang/zhangrenyang:[镜像版本号]
//将镜像推送到Registry
docker login --username=hongqishiq@126.com registry.cn-beijing.aliyuncs.com
docker tag [ImageId] registry.cn-beijing.aliyuncs.com/zhangrenyang/zhangrenyang:[镜像版本号]
docker push registry.cn-beijing.aliyuncs.com/zhangrenyang/zhangrenyang:[镜像版本号]
```

```bash
docker login --username=hongqishiq@126.com registry.cn-beijing.aliyuncs.com
docker run -d -p 8080:80 nginx
docker exec -it 6764db063e37  bash
/usr/share/nginx/html
docker container commit -m"nginx-user-v1" -a"zhangrenyang" 6764db063e37  registry.cn-beijing.aliyuncs.com/zhangrenyang/nginx:user-v1
docker container commit -m"nginx-user-v2" -a"zhangrenyang" 6764db063e37  registry.cn-beijing.aliyuncs.com/zhangrenyang/nginx:user-v2
docker container commit -m"nginx-pay-v1" -a"zhangrenyang" 6764db063e37  registry.cn-beijing.aliyuncs.com/zhangrenyang/nginx:pay-v1
docker push registry.cn-beijing.aliyuncs.com/zhangrenyang/http-probe:1.0.0
```

### 链接

- [Kubernetes中文社区](http://docs.kubernetes.org.cn/)
- [kubectl Cheat Sheet](http://docs.kubernetes.org.cn/783.html#i)
- [kubernetes.cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [namespaces](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/namespaces/)Kubernetes 支持多个虚拟集群，它们底层依赖于同一个物理集群。 这些虚拟集群被称为命名空间

```bash
kubectl get ns 
```

