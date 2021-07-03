---
title: linux服务与源码安装(四)
date: 2021-03-07 10:32:24
categories: 运维
top: false
summary: linux服务与源码安装(四)
tags: 
 - 运维
---

### 服务简介和分类

#### 运行级别

* 运行级别分类

| 运行级别 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| 0        | 关机                                                         |
| 1        | 单用户，类似于`window`的安全模式，主要用于系统修复           |
| 2        | 不完全多用户，类似于字符界面，但不包含NFS（Linux和window进行文件共享）服务 |
| 3        | 完全的命令行模式，就是标椎的字符界面                         |
| 4        | 系统保留未使用                                               |
| 5        | 图形界面                                                     |
| 6        | 重启                                                         |

* 查看上一个级别和当前级别

```bash
runlevel
# N 3
```

* 切换运行级别

```bash
init 5
```

* 设置默认运行级别

```bash
vi /etc/inittab
# id:3:initdefault:
```

#### 服务分类

* 服务的分类
  * 系统开启的服务越少，服务器就会更加稳定和安全
  * 服务安装方式不同，启动的方式不同

* 服务管理的方式
  * `RPM包`：安装的服务，由软件包作者指定安装位置，独立的服务，绝大数服务都是独立运行在内存中，可以直接响应客户端的请求
  * `源码包`：安装的服务，由我们用户决定安装的位置
* 查看RPM包安装的服务

```bash
chkconfig --list
```

* 查看源码包安装的服务
  * 查看自定义的安装位置，默认为`/usr/local`下
  * usr = `Unix System Resource`系统资源
* 启动和自启动
  * 启动服务就是指让此服务在当前系统中运行，并向客户端提供服务
  * 服务自启动就是指通过设置，让此服务在开机或者重启随着系统启动而自启动

#### 服务与端口

查看系统中运行中的进程

```bash
ps -aux
```

查看常见服务端口

```bash
cat /etc/services
```

#### 查看系统中监听的端口

```bash
netstat -tulnp
```

| 参数 | 含义                           |
| ---- | ------------------------------ |
| -t   | 列出tcp数据                    |
| -u   | 列出udp数据                    |
| -l   | 列出正在监听的网络服务         |
| -n   | 用端口号来显示服务，而非服务名 |
| -p   | 列出该访问的进程ID             |

### PRM包服务管理

* RMP 是 LINUX 下的一种软件的可执行程序，你只要安装它就可以了。这种软件安装包通常是一个`RPM`包（Redhat Linux Packet Manager，就是Redhat的包管理器），后缀是`.rpm`
* RMP 是`Red Hat`公司随着`Redhat Linux`推出了一个软件包管理器，通过它能够更加轻松容易地软件安装

rmp命令

| 用途                               | 命令                    | 解释                                                         |
| ---------------------------------- | ----------------------- | ------------------------------------------------------------ |
| 安装软件                           | 执行`rpm -ivh rpm`包名  | 其中`i`表示安装install，`v`表示显示安装过程verbose，`h`表示显示进度 |
| 升级软件                           | 执行`rpm -Uvh rpm`包名  | U表示升级update                                              |
| 反安装                             | 执行`rpm -e rpm`包名    |                                                              |
| 查询软件包的详细信息               | 执行`rpm -qpi rpm`包名  |                                                              |
| 查询某个文件是属于那个rpm包的      | 执行`rpm -qf rpm`包名   |                                                              |
| 查该软件包会向系统里面写入哪些文件 | 执行 `rpm -qpl rpm`包名 |                                                              |

**repo**

* repo文件是yum源（软件仓库）的配置文件，通常一个`repo`文件定义了一个或者多个软件仓库的细节内容，例如我们将从哪里下载需要安装或者升级的软件包，repo文件中的设置内容将被yum读取和应用
* 服务器端：在服务器上面存放了所有的`RPM`软件包，然后以相关的功能去分析每个RPM文件的依赖关系，将这些数据记录成文件存放在服务器的某特定目录内
* 客户端：如果需要安装某个软件时，先下载服务器上面记录的依赖关系文件（可通过WWW或FTP方式），通过对服务端下载的纪录数据进行分析，然后取得所有相关的软件，一次全部下载下来进行安装

```bash
cat /etc/yum.conf
/etc/yum.repos.d
/etc/yum.repos.d/nginx.repo
```

**RPM包的默认安装位置**

| 文件           | 含义                   |
| -------------- | ---------------------- |
| /etc           | 配置文件位置           |
| /etc/init.d    | 启动脚本的位置         |
| /etc/sysconfig | 初始化环境配置文件位置 |
| /var/lib       | 服务产生的数据放在这里 |
| /var/log       | 日志                   |

**启动命令**

* systemd是Linux系统最新的初始化系统（init）,作用是提高系统的启动速度，尽可能启动较少的进程，尽可能更多的进程并发启动
* systemd对应的进程管理命令时`systemctl`

```bash
rpm -ivh http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm
yum info nginx
yum install -y nginx 
systemctl start nginx.service
netstat -ltun | grep 80
curl http://localhost
```

### 源码包服务管理

* 使用绝对路径，调用启动脚本来启动
* 不同的源码包的启动脚本不一样
* 要通过阅读源码包安装说明的方式来查看启动的方法

#### 安装nginx

* 安装依赖

```bash
yum install gcc gcc-c++ perl -y
```

* 下载源文件

**PCRE**

> - [官网](http://www.pcre.org/)
> - [FTP下载](ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/)
> - [HTTP下载](http://sourceforge.net/projects/pcre/files/pcre/)

```bash
wget https://img.zhufengpeixun.com/pcre-8.44.tar.gz
```

##### zlib

- [官网](http://www.zlib.net/)
- [HTTP下载](http://prdownloads.sourceforge.net/libpng/zlib-1.2.11.tar.gz)

```bash
wget https://img.zhufengpeixun.com/zlib-1.2.11.tar.gz
```

##### openssl

- [官网](https://www.openssl.org/)

```bash
wget https://img.zhufengpeixun.com/openssl-1.0.2n.tar.gz
```

##### nginx

- [官网](http://nginx.org/en/docs/configure.html)

```js
wget https://img.zhufengpeixun.com/nginx-1.10.1.tar.gz
```

**解压文件**

```bash
mkdir /root/nginxinstall
cd  /root/nginxinstall
tar -zxvf nginx-1.10.1.tar.gz
tar -zxvf openssl-1.0.2h.tar.gz
tar -zxvf pcre-8.44.tar.gz
tar -zxvf zlib-1.2.11.tar.gz
```

**配置和安装**

```bash
cd nginx-1.10.1
./configure --prefix=/usr/local/nginx \
--pid-path=/usr/local/nginx/nginx.pid \
--error-log-path=/usr/local/nginx/error.log \
--http-log-path=/usr/local/nginx/access.log \
--with-http_ssl_module \
--with-mail --with-mail_ssl_module \
--with-stream --with-threads \
--user=comex --group=comexgroup \
--with-pcre=/root/nginxinstall/pcre-8.44 \
--with-zlib=/root/nginxinstall/zlib-1.2.11 \
--with-openssl=/root/nginxinstall/openssl-1.0.2n
make && make install

/usr/local/nginx/sbin/nginx  -t
nginx: [emerg] getpwnam("comex") failed

useadd nginx # 添加nginx用户
vi /usr/local/nginx/conf/nginx.conf
user  nginx;
```

**源码安装的好处**

* 可以灵活的指定安装位置
* 可以进行灵活配置安装源
* 可以很方便支持不同发行版 ubutun centos 

**管理命令**

| 功能                      | 命令                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 启动                      | /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf |
| 从容停止                  | ps -ef grep nginx;kill -QUIT 2072                            |
| 快速停止                  | ps -ef grep nginx;kill -TERM 2132; kill -INT 2132            |
| 强制停止                  | pkill -9 nginx                                               |
| 验证nginx配置文件是否正确 | nginx -t                                                     |
| 重启Nginx服务             | nginx -s reload                                              |
| 查找当前nginx进程号       | kill -HUP 进程号                                             |

**以服务启动service**

* Nginx启动、关闭、重新加载脚本
* 创建文件`etc/init.d/nginx`

```bash
/etc/init.d/nginx start
```

```shell
#! /bin/bash

NAME=nginx
DAEMON=/usr/local/nginx/sbin/$NAME
CONFIGFILE=/usr/local/nginx/conf/$NAME.conf
PIDFILE=/usr/local/nginx/logs/$NAME.pid
SCRIPTNAME=/etc/init.d/$NAME

set -e
[ -x "$DAEMON" ] || exit 0

do_start() {
 $DAEMON -c $CONFIGFILE  || echo -n "nginx already running"
 pid=$(ps -ef | grep nginx | grep master | awk '{print $2}')
 echo $pid > "$PIDFILE"
}

do_stop() {
 kill -INT `cat $PIDFILE` || echo -n "nginx not running"
}

do_reload() {
 kill -HUP `cat $PIDFILE` || echo -n "nginx can't reload"
}

case "$1" in
 start)
 echo -n "Starting  $NAME"
 do_start
 echo "."
 ;;
 stop)
 echo -n "Stopping  $NAME"
 do_stop
 echo "."
 ;;
 reload|graceful)
 echo -n "Reloading  configuration"
 do_reload
 echo "."
 ;;
 restart)
 echo -n "Restarting  $NAME"
 do_stop
 do_start
 echo "."
 ;;
 *)
 echo "Usage: $SCRIPTNAME {start|stop|reload|restart}" >&2
 exit 3
 ;;
esac
exit 0
```

**chkconfig**

> 指定nginx脚本可以被chkconfig命令管理

**开机启动**

```bash
chkconfig --add nginx
chkconfig --list 
chkconfig --level 2345 nginx on 
chkconfig nginx off
```

