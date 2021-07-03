---
Docker + Kubernetestitle: CICD
date: 2021-03-20 14:26:51
categories: 运维
top: false
summary: CICD
tags: 
 - 运维
---

### CICD

* CI的意思是 持续构建。负责拉取代码库中的代码后，执行用户预置定义好的操作脚本，通过一系列编译操作构建出来一个制品，并将制品推送至到制品库里面。常用工具有`Gitlab CI`，`Github CI`，`Jenkins`等。这个环节不参与部署，只负责构建代码，然后保存构建物。构建物被称为制品，保存制品的地方称为`制品库`
* CD则有2层行业，持续部署（Continuous Deployment）和持续交付（Continuous Delivery）。持续交付的概念是：将制品库的制品拿出来后，部署在测试环境 / 交付给客户提前测试，持续部署则是将制品部署在生产环境。

### 服务器

| 配置  | 技术栈               | 类型  | 标签              |
| ----- | -------------------- | ----- | ----------------- |
| 2核4G | Jenkins+Nexus+Docker | Cloud | 构建机            |
| 2核4G | Docker+Kubernetes    | Cloud | kubernetes Master |
| 1核1G | Docker+Kubernetes    | Cloud | kubernetes Node   |

### 构建机CI

![CICD](/medias/imges/yw/CICD.png)

#### 安装docker

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum install docker-ce -y
systemctl start docker
systemctl enable docker
```

**配置阿里云镜像源**

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://fwvjnv59.mirror.aliyuncs.com"]
}
EOF
# 重载所有修改过的配置文件
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 内置 Jenkins

> Jenkins是一个基于 Java语言开发的持续构建工具平台，主要用于持续、自动构建/测试你的软件和项目。它可以执行你预先设定好的设置和构建脚本，也可以和Git代码库做集成，实现自动触发和定时触发构建

**安装java**

```bash
yum install -y java
```

**安装jenkins**

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://img.zhufengpeixun.com/jenkins.repo
sudo rpm --import https://img.zhufengpeixun.com/jenkins.io.key
yum install jenkins -y
```

**启动Jenkins**

```bash
 systemctl start jenkins.service
```

**开放端口**

```bash
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=50000/tcp --permanent
systemctl reload firewalld
```

**初始化Jenkins配置**

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

**修改插件镜像**

```bash
sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' /var/lib/jenkins/updates/default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' /var/lib/jenkins/updates/default.json
```

**测试任务**

```bash
#sudo groupadd docker          #新增docker用户组
sudo gpasswd -a jenkins docker  #将当前用户添加至docker用户组
#newgrp docker                 #更新docker用户组
```

```bash
docker -v
docker pull node:latest
```

#### 安装Nodejs

* 系统管理 =>  插件管理 => 可选插件 => 安装 NodeJS插件
* 全局工具配置 => NodeJS => 新增NodeJS
* 任务的配置 => 构建环境 => 选中Provide Node & npm bin / folder to PATH

#### 集成GIT仓库

**生成公钥私钥**

```bash
ssh-keygen -t rsa -C "xxxx@126.com"
```

**Gitee配置公钥**

> 设置 => 安全设置 => ssh公钥

```bash
cat ~/.ssh/id_rsa.pub 
```

**在Jenkins配置私钥**

* 在 Jenkins 中，私钥 / 密钥 等认证信息都是以 凭证的方式管理的
* 配置 => 源码管理 => Git => Repositories
* Credentials => 添加 => SSH Username with private key
  * Username 1281026802@qq.com
  * 此处可能会反复失败，请耐心多试几次

#### 构建镜像

**编写Dockerfile**

Dockerfile

```dockerfile
FROM nginx:1.15
COPY build /etc/nginx/html
COPY conf /etc/nginx/
WORKDIR /etc/nginx/html
```

conf/site

```shell
server {
    listen       80;
    server_name  _;
    root         /etc/nginx/html;
}
```

**Jenkins配置脚本**

构建 => 执行Shell

```shell
#!/bin/sh
npm install --registry=https://registry.npm.taobao.org
npm run build
docker build -t react-project .
```

点击立即构建