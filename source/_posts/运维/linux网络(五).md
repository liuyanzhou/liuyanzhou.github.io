---
title: linux网络(五)
date: 2021-03-07 11:13:19
categories: 运维
top: false
summary: linux网络(五)
tags: 
 - 运维
---

### 网络

### 配置IP地址

查看与配置网络状态

```bash
ifconfig
```

### 查看网络环境

#### 查询网络状态

* netstat 选项

| 选项 | 含义                                     |
| ---- | ---------------------------------------- |
| -t   | 列出TCP协议端口                          |
| -u   | 列出UDP协议端口                          |
| -n   | 不使用域名和服务名，而使用IP地址和端口号 |
| -l   | 仅列出在监听状态网络服务                 |
| -a   | 列出所有的网络连接                       |

```bash
netstat -tlun
netstat -an | more 
netstat -unt | grep ESTABLISHED
```

### 网络测试命令

#### ping

* ping [ 选项 ] ip或域名
* 测试指定的IP或域名的网络状态
* 选项
  * -c c次数指定ping包的次数

```bash
ping www.baidu.com -c 3
```

#### wget

下载命令

```bash
wget http://www.baidu.com
```

### 远程登录

#### SSH协议原理

**对称加密算法**

* 采用单密钥系统的加密方法，同一个密钥可以同时用作信息的加密和解密，这种加密被称为对称加密
* 非对称加密算法，需要公钥和私钥

**SSH安全外壳协议**

* ssh 用户名@ip
* 远程指定的linux服务器

```bash
[root@192-171-207-101-static ~]# ssh root@192.171.207.101
The authenticity of host '192.171.207.101 (192.171.207.101)' can't be established.
RSA key fingerprint is a4:97:52:eb:0a:0b:35:a0:98:7d:4f:c8:3b:dc:f9:0a.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.171.207.101' (RSA) to the list of known hosts.
```

/root/.ssh/konwn_hosts

```bash
192.171.207.101 ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAomDpQxV3RmjJyKkf7elMTInbdm+/ZLnFpfbAryi5PSb2ewfYbwRaBcVl1lBta6yjFuz0J12p9qy90DBhadvoBsfwTB8lQhmlT8B2eCcHr0bfLa1IdKMcjImxRJiD4v0emCGFquHnHIr41vs8uxQ2Ek28mH/1JC0e/+VPEvylBB4+Kk2789ACdAlmhGTtlu7zgeUoLaWQSl1/6g7zfSLIz+/U8qGiRSPaGT+M40oqx/PZdoGOMTRhHgNIR5qgvcNaJXhlZGYT42fLFSmtzUHJ030hP7JGZ99oXS20/mnc8qvonC9itp0+K/nCj5g6uR/gPFb5B0NmTZCM2/gcLkHumw==
```

#### scp

* scp是`secure copy`的缩写，scp是linux系统下基于ssh登录进行安全的远程文件拷贝命令
* linux的`scp`命令可以在linux服务器之间复制文件和目录
* 命令格式`scp [参数] [原路径] [目标路径]`

**从本地服务器复制到远程服务器**

```bash
#scp local_file remote_username@remote_ip:remote_folder  
scp -r local_folder remote_username@remote_ip:remote_folder  
```

**从远程服务器复制到本地服务器**

```bash
# scp  remote_username@remote_ip:remote_folder  local_file
scp -r  remote_username@remote_ip:remote_folder local_folder
```

