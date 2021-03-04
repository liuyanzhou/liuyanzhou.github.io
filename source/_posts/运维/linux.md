---
title: linux
date: 2021-02-25 18:49:33
categories: 运维
top: false
summary: linux
tags: 
 - 运维
---

### linux

* Linux 是一套免费使用和自由传播的类`Unix`操作系统
* 在服务器端领域和嵌入式领域有非常广泛的应用

版本：分为内核版本和发行版本

> * [kernel](https://www.kernel.org/)
> * 各个厂商会制作自己的发行版本
>   * redhat
>   * CentOS
>   * ubuntu
>   * fedora

Linux 与 windows 的不同

* Linux严格区分大小写
* Linux中所有的内容以文件形式保存，包括硬件、用户和文件
* Linux不靠扩展名区分文件类型，是靠权限来区分，但是有一些约定的扩展名，是给管理员看的
  * 压缩包：`.gz`，`.bz2`，`tar.bz2`，`.tgz`
  * 二进制文件：`.rpm`
  * 网页文件：`.html`，`.php`
  * 脚本文件：`.sh`
  * 配置文件：`.conf`

* Windows 下的程序不能直接在Linux中安装和运行
* Linux 更多使用字符界面
  * 占用的系统资源更少
  * 减少了出错和被攻击的可能性，会让系统更稳定

购买服务器：

* [阿里云ECS 【推荐】](https://www.aliyun.com/)，------> 推荐 CentOS  7.6版本
* [亚马逊 AWS](https://aws.amazon.com/cn/) 
* [百度云](https://cloud.baidu.com/)

连接服务器：

* git bash
* mac shell
* xshell4
* xfp4

### linux常用命令

让一个文件执行的三种方法

* `chmod +x xxx` 添加可执行权限 xxx内部要行执行环境代码 `#!/usr/bin/bash`，这样子就可以通过`./xxx`来执行文件
* 使用sh来执行`sh xxxx`
* 将文件路径拷贝到`/usr/bin`目录下，或者文件路径添加到环境变量`$PATH`后面，执行`xxx`就可以，内部需要含文件执行环境的代码`#!/usr/bin/bash`

#### 一、常见目录

| 目录      | 用途                                                         |
| --------- | ------------------------------------------------------------ |
| /         | 根目录                                                       |
| /boot     | 启动目录，启动相关文件                                       |
| /dev      | 设备文件                                                     |
| /ect      | 配置文件                                                     |
| /home     | 普通用户的家目录，可以操作                                   |
| /lib      | 系统库保存目录                                               |
| /mnt      | 移动设备挂载目录                                             |
| /media    | 光盘挂载目录                                                 |
| /misc     | 磁带机挂载目录                                               |
| /root     | 超级用户的家目录，可以操作                                   |
| /tmp      | 临时目录，可以操作                                           |
| /proc     | 正在运行的内核信息映射，主要输出进程信息、内存资源信息和磁盘分区信息等等 |
| /sys      | 硬件设备的驱动程序信息                                       |
| /var      | 变量                                                         |
| /bin      | 普通的基本命令，如 ls, chmod等，一般的用户也都可以使用       |
| /sbin     | 基本的系统命令，如shutdown，reboot，用于启动系统，修改系统，只有管理员才可以运行 |
| /usr/bin  | 手机你在后期安装的一些软件的运行脚本                         |
| /usr/sbin | 放置一些用户安装的系统管理必备程序                           |

#### 二、命令的基本格式

##### 2.1、命令提示符

```
[root@linux ~]#
```

* root：当前登录用户
* linux：主机名
* `~`：当前工作目录，默认是当前用户的家目录，root就是`/root`，普通用户是`/home/用户名`
* 提示符：超级用户就是`#`，普通用户是`$`

##### 2.2、命令格式

* 命令 [选项] [参数]
* 当有多个选项时，可以写在一起，一般参数有简化的完整写法两种`-a`与`--all`等效

##### 2.3、ls

* 查看目录中的内容
* ls [选项] [文件或者目录]
* 选项
  * -a 显示所有文件，包括隐藏文件
  * -l 显示详细信息
  * -d 查看目录本身的属性而非子文件 
  * -h 人性化的方式显示文件大小
* 默认当前目录下的文件列表

```
ls -l ==> ll
```

详细信息如下：

```
drwxr-xr-x root root 800 Sep 16 00:19 logs
```

| drwxr-xr-x     | root   | root   | 800      | Sep 16 00:19 | logs   |
| -------------- | ------ | ------ | -------- | ------------ | ------ |
| 文件类型和权限 | 所有者 | 所属组 | 文件大小 | 最后修改事件 | 文件名 |

##### 2.4、文件处理命令

**mkdir**

* 建立目录 `make directory`
* mkdir -p [目录名]
  * -p 递归创建，当创建不存在的目录时使用 `mkdir -p b/a`

> mkdir -p hello

**cd**

* 切换所在目录 change directory
* cd [目录]
  * `~`：家目录
  * `.`：当前目录
  * `..`：上级目录
* 相对路径是参考当前所在目录
* 绝对路径是从根目录开始
* 按`TAB`键可以补全命令和目录

```bash
cd hello
```

**pwd**

显示当前目录 

```bash
pwd
```

**rmdir**

* 删除目录 `remove empty directory`
* rmdir [目录名]

```bash
rmdir hello
```

**rm**

* 删除文件或目录 remove , 可以直接删除文件，当删除目录时候要加`-r`
* rm [文件或目录]
  * -r 删除目录
  * -f 强制删除
* rm -rf 文件或者目录  【递归前强制删除所有目录】

```bash
rm -rf hello 
```

**cp**

* copy 复制命令
* copy [源文件或者目录] [目标文件]
  * -r 复制命令，默认是复制文件
  * -i 会在复制文件的时候给出提示，如果复制的目标文件存在，会给你提示是否要覆盖

```bash
mkdir afolder
mkdir bfolder 
cd afolder
touch 1.txt 
cp 1.txt ~/bfolder/  # 将1.txt复制到bfolder目录去
```

**mv**

* 移动文件或者改名 ，当直接加文件并不存在目录时，就是改名，否则就是移动
* mv [源文件或目录] [目标文件]

```bash
mv 1.txt 11.txt  # 改名
mv 1.txt ~/bfolder # 将1.txt 移动到bfolder去
```

**In**

* 链接命令，生成链接文件`link`
* ln -s [源文件] [目标文件]
  * -s 创建软链接
* 类似Windows快捷方式
* 修改任意一个文件，另一个都会改变
* 删除源文件，软链接不能使用
* 软连接有我呢间必须写**绝对路径**

```bash
ln -s /root/bfolder/11.txt 22.txt
```

##### 2.5、文件搜索命令

**locate**

* 在后台数据库中按文件名搜索，速度比较快
* 数据报错在`/var/lib/mlocate/mlocate.db`后台数据库，每天更新一次
* 可以`updatedb`命令立刻更新数据库
* 只能搜索文件名

要安装mlocate

```bash
yum -y install mlocate
```

使用

```bash
locate 1.txt[文件名]
```

**whereis**

* 搜索命令所在路径以及帮助文档所在位置
* whereis 命令名`whereis is`
  * -b 只查找可执行文件
  * -m 只查找帮助文件
* 可以查看shell自带的命令，如`whereis cd`

**which**

* 可以看到别名`which is`
* 能看到的都是外部安装的命令
* 无法查看Shell自带的命令，如`which cd`

**环境变量**

`/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin`

* 定义的是系统搜索命令的路径
* echo $PATH

**find**

* 文件搜索命令
* find [搜索范围] [搜索条件]

按名称搜索：避免大范围的搜索，会非常消耗系统资源

```bash
find / -name 11.txt
```

通配符

* find 是在系统当中搜索符合条件的文件名，如果需要匹配，使用通配符匹配，通配符是完全匹配
* 通配符
  * `*`：匹配任意内容
  * `?`：匹配任意一个字符
  * `[]`：匹配任意一个中括号内的字符

```bash
touch abc.txt 
find . -name ab[cdef].txt
```

在find中可以使用 `-i`来忽略大小写

```bash
find . -iname Ab[cdef].txt
```

找到用户文件【-user】:

* 按所有者进行搜索

```bash
find /root -user root
find /root -nouser # 不属于某用户的文件
```

按时间搜索

```bash
find . -mtime +5
```

| 参数  | 含义                |
| ----- | ------------------- |
| atime | 文件访问时间        |
| ctime | 改变文件属性时间    |
| mtime | 改变文件内容时间    |
| +5    | 5天内修改的文件     |
| 5     | 5天前当天的修改文件 |
| -5    | 5天前修改的文件     |

按大小搜索：`k要小写，M要大写`

```bash
find . -size +0k
```

| 参数 | 含义   |
| ---- | ------ |
| -8k  | 小于8k |
| 8k   | 等于8k |
| -8k  | 大于8k |
| +8M  | 大于8M |

综合使用

```bash
find /tmp -size +10k -a -size -20k
```

> * 查找`/tmp`目录下，大于10kb并且小于20kb的文件
> * -a (and)逻辑与，两个条件都满足
> * -o (or) 逻辑或，两个条件满足一个就可以

```bash
find /tmp -size +10k -a -size -20k exec ls -lh {} \;
```

* exec 对上个命令的结果进行操作

**grep**

* 在文件当中匹配符合条件的字符串
  * `-i`：忽略大小写
  * `-v`：排除指定字符串
* find命令，在系统当中搜索符合条件的文件名，如果需要匹配，使用通配符匹配，通配符是完全匹配
* grep命令，在文件当中搜索符合条件的字符串，如果需要匹配，使用正则表达式进行匹配，正则表达式是包含匹配

```bash
vi a.txt
grep b a.txt 
grep -v b a.txt 
grep -i f a.txt 
```

##### 2.6、压缩与解压缩命令

**zip格式**：压缩文件或目录，是一种压缩格式

| 功能     | 命令                                |
| -------- | ----------------------------------- |
| 压缩文件 | zip 【压缩文件名.zip】【 源文件】   |
| 压缩目录 | zip -r 【压缩目录名.zip】【源目录】 |
| 解压     | unzip 压缩目录名.zip                |

```bash
yum install -y unzip zip 
mkdir book 
touch book/1.txt book/2.txt 
zip -r book.zip book # 压缩
rm -rf book # 先删除防止冲突
unzip book.zip  #解压
```

**gzip**

* `gzip`为高压，可以把文件压缩得更小
* `gzip`命令不支持目录

| 命令                      | 示例                             | 含义                                                         |
| ------------------------- | -------------------------------- | ------------------------------------------------------------ |
| gzip 源文件               | gzip book .txt                   | 压缩为`.gz`格式的压缩文件，源文件会消失                      |
| gzip -c 源文件 > 压缩文件 | gzip -c book.txt > book.txt.gzip | 压缩`.gz`格式的压缩文件，源文件不会消失                      |
| gzip -r 目录              | gzip -r book                     | 把目录下的每个字文件都变成压缩包，并删除源文件，当前目录无变化 |
| gzip -d 压缩文件名        | gzip -d 1.txt.gz                 | 解压缩文件，不保留压缩包                                     |
| gunzip 压缩文件           | gunzip 2.txt.gz                  | 解压缩文件，也不保留压缩包                                   |

压缩是压缩目录下的文件

```bash
touch book.txt 
mkdir book
touch book/1.txt book/2.txt
gzip -c 1.txt > 1.txt.gz # 源文件会消失
gzip book.txt # 压缩为.gz格式文件，源文件会消失
gzip -r book # 把目录下的每个子文件都变成压缩包
cd book 
gzip -d 1.txt.gz # 解压缩文件，不保留压缩包
gunzip 2.txt.gz 
```

**tar**

* 打包命令，只打包并不压缩
* `tar -cvf`：打包文件名，源文件
  * `-c`：打包
  * `-v`：显示过程
  * `-f`：指定打包后的文件名

```bash
tar -cvf book.tar book # 会打包出一个book.tar文件
```

* 解压包

```bash
tar -xvf book.tar 
```

**tar.gz压缩格式**

* `zip`可以压缩目录但压缩效率不高，`gzip`压缩效率高但不支持目录
* 可以先打包为`.tar`格式，在压缩为`.gz`格式 ，`-c`压缩为`.tar.gz`格式，`-x`解压缩`.tar.gz`格式

| 命令                                 | 示例                       | 含义                                      |
| ------------------------------------ | -------------------------- | ----------------------------------------- |
| tar -zcvf `压缩包名.tar.gz`   源文件 | tar -zcvf book.tar.gz book | 可以先打包为`.tar`格式，再压缩为`.gz`格式 |
| tar -zxvf `压缩包名.tar.gz`          | tar -zxvf book.tar.gz      | 解压`tar.gz`压缩包                        |

```bash
tar -zcvf book.tar.gz book
tar -zxvf book.tar.gz
```

#####2.7、关机和重启命令

**shutdown**

* shutdown 关机命令
  * `-c`：取消前一个关机命令
  * `-h`：关机
  * `-r`：重启

```bash
shutdown -r 06:00 # 6点重启
shutdown -c # 取消前一个关机命令
```

**init**

* 关机

```bash
init 0
```

* 重启

```bash
init 6
```

**logout**

退出登录

```bash
logout
```

##### 2.8、查看登录用户信息

**w**：查看登录用户信息

| USER         | TTY                                   | FROM     | LOGIN      | IDLE         | JCPU                     | PCPU                 | WHAT           |
| ------------ | ------------------------------------- | -------- | ---------- | ------------ | ------------------------ | -------------------- | -------------- |
| 登录的用户名 | 登录的终端 ;tty1 本地终端; pts/0 远程 | 登录的IP | 登录的时间 | 用户闲置时间 | 该终端所有进程占用的时间 | 当前进程所占用的时间 | 正在执行的命令 |

**who**

* 查看登录用户信息

| USER         | TTY                                   | LOGIN                  |
| ------------ | ------------------------------------- | ---------------------- |
| 登录的用户名 | 登录的终端 ;tty1 本地终端; pts/0 远程 | 登录的时间（登录的IP） |

**last**

* 查看当前登录和过去登录的用户信息
* 默认读取`/var/log/wtmp`文件
  * 用户名
  * 登录终端
  * 登录IP
  * 登录时间
  * 退出时间（在线时间）

**lastlog**

* 查看所有用户的最后一次登录时间
  * 用户名
  * 登录终端
  * 登录IP
  * 最后一次登录时间

##### 2.9、文件查看命令

**cat**

* cat 命令用于连接文件并打印到标准输出设备上

* cat [-AbeEnstTuv] [--help] [--version] fileName

* 参数

  * -n 或 --number : 由1开始对所有输出的行数编号

  ```bash
  cat -n textfile1 
  ```

**more**

* Linux more 命令类型 cat ，不过会以一页一页的形式显示，更方便使用者逐页阅读，而最基本的指令就是按空白键（space）就往下一页显示，按 b 键就会往回 （back） 一页显示，而且还有搜寻字符串的功能（与vi相似），使用中的说明书，请按 h
* more fileName

```bash
more testfile
```

**head**

* 用来显示开头某个数量的文字区块

```bash
head -5 readme.txt 
```

**tail**

* tail 命令可用于查看文件的内容
* 有一个常用的参数`-f`常用于查阅正在改变的日志文件
* tail [参数] [文件]
* 参数
  * `-f`：循环读取（类似监听）
  * -n<行数> 显示文件的尾部n行内容

```bash
tail -5 mail.txt 
tail -f access.log
```

综合使用：先取出file文件的前10行，在从这10行中从尾部开始取5行

```bash
head -10 file | tail -5 
```

### 三、Vi编辑器

* VI ：visual interface (可视化接口)
* Linux 中的编辑器，类似于windows中的记事本
* VIM 是 VI 编辑器的升级版
* 不需要鼠标，只用键盘即可

#### 3.1、操作模式

| 模式名                 | 含义                     |
| ---------------------- | ------------------------ |
| 命令模式               | 等待输入的模式           |
| 输入模式               | 编辑模式，用于输入文本   |
| 底行（尾行、末行）模式 | 可以输入指令，搜索，保存 |

![vi](/medias/imges/yw/vimode.jpg)

#### 3.2、打开文件

* 输入`vi 文件名`可以进入这个模式
* vimtutor ---> vi编辑器教程

| 命令       | 含义                 |
| ---------- | -------------------- |
| vim 文件名 | 打开文件定位到第一行 |

#### 3.3、底行模式

| 命令 | 含义                       |
| ---- | -------------------------- |
| :w   | 把写入的内容保存到硬盘中   |
| :q   | 退出当前VI编辑器打开的文件 |
| :wq! | 强制保存                   |

#### 3.4、命令模式

##### 3.4.1光标

| 命令 | 含义     |
| ---- | -------- |
| h    | 光标左移 |
| j    | 光标下移 |
| k    | 光标上移 |
| l    | 光标右移 |

##### 3.4.2、翻页

| 命令   | 含义       |
| ------ | ---------- |
| ctrl+f | 向下翻页   |
| ctrl+b | 向上翻页   |
| ctrl+d | 向下翻半页 |
| ctrl+u | 向上翻半页 |

##### 3.4.3、插入类

| 命令 | 含义                                       |
| ---- | ------------------------------------------ |
| i    | 在当前位置插入                             |
| a    | 在当前光标右边插入                         |
| A    | 在当前光标右的行末插入内容                 |
| o    | 在光标所在行的下方插入一行并切换到输入模式 |
| O    | 在光标所在行的上方插入一行并切换到输入模式 |
| s    | 删除当前光标位置并插入                     |

```bash
123
456
789
```

##### 3.4.5、删除类

| 命令 | 含义         |
| ---- | ------------ |
| x    | 删除当前字符 |

##### 3.4.5、行删除类

| 命令 | 含义           |
| ---- | -------------- |
| dd   | 删除光标所在行 |

##### 3.4.6、撤销

| 命令 | 含义                   |
| ---- | ---------------------- |
| u    | 撤销最后执行的一次命令 |

##### 3.4.7、剪切类

| 命令 | 含义                   |
| ---- | ---------------------- |
| dd   | 删除光标所在行         |
| yy   | 复制光标所在的行       |
| p    | 在光标所在行的下方粘贴 |
| P    | 在光标所在行的上方粘贴 |

##### 3.4.8、替换类

| 命令 | 含义             |
| ---- | ---------------- |
| r    | 替换当前位置字符 |

##### 3.4.9、搜索

| 命令    | 含义                                |
| ------- | ----------------------------------- |
| /       | 输入/可以在当前的文件中查找该字符串 |
| n       | 查找下一个                          |
| shift-N | 查找上一个                          |

##### 3.4.10、修改错误

| 命令         | 含义                       |
| ------------ | -------------------------- |
| :s/old/new   | 把光标所在行的old替换成new |
| :s/old/new/g | 把所有行的old替换成new     |

#### 3.5、图

![vi](/medias/imges/yw/vi.png)

![vikey](/medias/imges/yw/vikey.jpg)

### 四、用户与用户组

#### 4.1、用户与用户组

* 使用操作系统的人都是用户
* 用户组是具有相同系统权限的一组用户

#### 4.2、配置文件

`/etc/group`

* /etc/group 存储当前系统中所有用户组信息
* `/root`组编号为0
* `1-499`系统预留的编号，预留给安装的软件和服务的
* 用户手动创建的用户从`500`开始
* 组密码占位符都是`x`
* 如果组内只有一个用户，而且用户名和组名相同的话，是可以省略用户的

`root:x:0:root`

| 内容 | 含义           |
| ---- | -------------- |
| root | 组的名称       |
| x    | 密码占位符     |
| 0    | 组编号         |
| root | 组中用户名列表 |

`/ect/gshadow`

* 存储当前系统中所有用户的信息

`root:x:0:0:root:/root:/bin/bash`

| 内容      | 含义         |
| --------- | ------------ |
| root      | 用户名       |
| x         | 密码占位符   |
| 0         | 用户编号     |
| 0         | 用户组编号   |
| root      | 用户注释信息 |
| /root     | 用户主目录   |
| /bin/bash | shell类型    |

`/etc/shadow`

* 存放当前系统中所有用户的密码信息
* user:xxx:::::::
* 主要关注**用户名和密码**

`root:password:17982:0:99999:7:::`

| 内容     | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| root     | 用户名                                                       |
| password | 单向加密后的密码                                             |
| 17982    | 修改日期，这个是表明上次修改密码的日期与1970-1-1相距的天数密码不可改的天数：假如这个数组是9，则8天内不可改密码，如果是0，则随时可以改 |
| 0        | 这个是表明上一次修改密码的日期与1970-1-1相距的天数，并且是密码不可改的天数：假如这个数字是8，则8天内不可改密码，如果是0，则随时可以改 |
| 99999    | 如果是99999则永远不用改，如果是其他数字，比如12345，那么必须在距离1970-1-1的12345天内修改密码，否则密码失效 |
| 7        | 修改期限前N天发出警告                                        |
|          | 密码过期的宽限天数                                           |
|          | 账号失效日期                                                 |
|          | 保留：被保留项，暂时还没有被用上                             |

#### 4.3、用户和用户组操作

显示登陆的用户

```bash
whoami
```

显示指定用户信息，包括用户编号，用户名，主要组的编号及名称，附属组列表

```bash
id student
```

显示zhangsan用户所在的所有组

```bash
groups student 
```

**添加用户组**

```bash
groupadd stu 
cat /etc/group
```

**修改用户组名称**

```bash
groupmod -n student(修改后的组名) stu(原组名)
cat /etc/group
```

**修改用户编号**

```bash
groupmod -g 666 student
cat /etc/group
```

**创建分组并指定编号**

```bash
groupadd -g 888 teacher 
```

**删除用户组**

```bash
groupdel student 
```

**添加用户**

* 如果创建用户的时候没有指定用户组，系统会为创建一个和用户名相同的用户组

```bash
groupadd stu # 添加用户组
useradd -g stu zhangsan # 创建用户并指定用户组
useradd -g stu lisi # 创建用户并指定用户组

id zhangsan
id lisi 

useradd -d /home/wangwu wangwu # 创建用户并指定家目录

passwd zhangsan # root 用户可以设置用户密码
```

**指定个人文件夹**

```bash
usermod -d /home/wangwu2 wangwu
```

**修改用户组**

```bash
usermod -g student wangwu
```

**删除用户**

```bash
userdel wangwu  # 默认删除不删除用户的家目录
userdel -r wangwu # 删除用户的时候连其删除对应的目录
```

### 五、文件权限

#### 5.1、文件基本权限

`-rw-r--r--`

* 文件类型：（第一个）
  * `-`文件
  * `d`目录
  * `l`软连接文件
* 所有组-所属组-其他人
* r：读，w：写，x：执行

**基本权限的修改**

chmod [选项] 模式 文件名

* 选项：`-R`递归
* 模式：[u/g/o/a]\[+-=]\[rwx]

* 权限数字：r：4，w：2，x：1

```bash
chmod 000 1.txt 
chmod u+w 1.txt 
chmod g+x 1.txt 
chmod o+r 1.txt 

chmod g+x,o+x 1.txt 
chmod u-w 1.txt 
chomd u+rwx 1.txt 
chmod 777 1.txt 
```

**权限的作用**

* 对文件来说最高权限是`x`
* 对目录来讲最高权限是`w`,只有读取权限是没有意义的，对目录有了写权限，里面可以做任何事情

**文件权限**

| 权限 | 含义                                                         | 示例                   |
| ---- | ------------------------------------------------------------ | ---------------------- |
| r    | 读取文件内容                                                 | cat more head tail     |
| w    | 编辑，新增，修改文件内容，但不能删除文件，除非对目录有些权限 | vi echo                |
| x    | 可执行                                                       | ./xx.txt  \| sh xx.txt |

**目录权限**

| 权限 | 含义                                                         | 示例           |
| ---- | ------------------------------------------------------------ | -------------- |
| r    | 可以查看目录下的文件名                                       | ls             |
| w    | 具有修改目录结构的权限。如新建、删除和重命名此目录下的文件和目录 | touch rm mv cp |
| x    | 进入目录                                                     | cd             |

```bash
useradd zf1 
paswd zf1 

cd /home/zf1 
mkdir folder 
touch folder/1.txt # 默认权限是755 
chmod 750 folder 
chmod 640 folder/1.txt 
chmod 754 folder
chmod 755 folder 
chmod 644 folder/1.txt 
chmod 646 folder/1.txt 
chmod 757 folder 
```

**其他权限命令**

`chown`

* chown 用户名 文件名
* 如果想让员工用户拥有 7 q权限，可以把这个文件的所有者改成这个用户名

```bash
chmod 755 folder  
chown zf1 folder 
```

`chgrp`

* chgrp 组名 文件名
* 创建用户名的时候会为它创建一个所属组

```bash
chgrp zf1 folder 
chown root:root folder 
```

#### 5.2、默认权限

**unmask**

* 查看默认权限
* 0022
  * 第一位0 文件特殊权限
  * 022 文件默认权限

**文件权限**

* 默认全新就是文件-创建后就拥有权限
* 文件默认不能建立可执行文件，必须手动赋予 执行权限
* 文件默认权限最大为`666`
* 没入权限需要换算成字符在相减
* 建立文件之后的权限，为666减去umask值

```bash
666 - 022 = 744
```

**目录**

* 目录默认权限最大为 777 
* 建立目录之后默认权限，为777 减去umask值

```bash
777 - 022 =755 
```

**修改umask值**

* 临时修改

```bash
umask 0002
```

* 永久修改

```bash
vi /etc/profile
```

#### 5.3、sudo权限

* root 把本来只有超级管理员可以使用的命令赋予普通用户来使用
* sudo操作的对象是系统命令

**visudo**

* 通过`visudo`可以由超级用户赋权
* 实际修改的是`/etc/sudoers`文件
* 命令必须写绝对路径

```bash
root   ALL=(ALL) ALL
# 用户名  被管理主机地址=(可使用的身份) 授权命令(绝对理路径)
stu2 ALL=(ALL) ALL # 新增加用户行
```

### 六、shell基础

* shell 是一个命令行解释器，它为用户提供了一个向`linux`内核发送请求以便运行程序的界面系统级程序
* 用户可以用 Shell 来启动、挂起、停止或者编写一些程序
* Shell 还是一个功能强大的编程语言，易编写，易调试，灵活性较强
* Shell 是解释执行的脚本语言，在Shell中可以直接调用`Linux`系统命令

**echo**

* 输出命令
* -e：激活转义字符

```bash
echo hello 
echo -e "a\tb"
```

**编写执行shell**

hello.sh

```bash
#!/bin/bash
echo hello
```

```bash
# 通过Bash调用执行脚本
sh hello.sh
# 赋予执行权限 直接运行
chmod 755 hello.sh
chmod u+x hello.sh
./hello.sh
```

**别名**

* 命令别名就是小名
* 临时生效：`alias cp ="cp -i"`
* 写入环境变量配置文件：`vi ~/.bashrc`
* source ~/.bashrc
* 删除别名：`unalias 别名`

**命令生效顺序**

* 绝对路径或者相对路径
* 别名
* bash内部命令
* 按照`$PATH`环境变量定义的目录查找顺序找到的第一条命令

**命令快捷键**

| 命令     | 含义                       |
| -------- | -------------------------- |
| ctrl + c | 前置终止当前命令           |
| ctrl + l | 清屏                       |
| ctrl + a | 光标移动到命令行首         |
| ctrl + e | 光标移动到命令行尾         |
| ctrl + u | 从光标所在的位置删除到行首 |

**历史命令**

* history [选项] [历史命令保存文件]
* 选项
  * -c 清空历史命令
  * -w 把缓存中的历史命令写入历史命令保存文件
  * `~/.bash_history`
* 没入保存1000条 `/etc/profile`  `HISSIZE=1000`

**调用**

* 使用上下箭头调用以前的历史命令
* 使用`!n`重复执行第n条历史命令
* 使用`!!`重复执行上一条命令
* 使用`!字符`重复执行最后一条以该字符串开头的命令

```bash
history -c 
1 echo 1
2 echo 2
3 echo 3
!2
!!
!echo 
```

**输出重定向**

* 标准输入输出

| 设备   | 设备文件名  | 文件描述符 | 类型         |
| ------ | ----------- | ---------- | ------------ |
| 键盘   | /dev/stdin  | 0          | 标准输入     |
| 显示器 | /dev/stdout | 1          | 标准输出     |
| 显示器 | /dev/stderr | 2          | 标准错误输出 |

| 类型                       | 符号                | 作用                                                         |
| -------------------------- | ------------------- | ------------------------------------------------------------ |
| 标准输出重定向             | 命令 > 文件         | 以覆盖的方式，把命令的正确输出输出到指定的文件或设备当中     |
| 标准输出重定向             | 命令 >> 文件        | 以追加的方式，把命令的正确输出输出到指定的文件或设备当中     |
| 错误输出重定向             | 命令>文件           | 以覆盖的方式，把命令的错误输出输出到指定的文件或设备当中     |
| 错误输出重定向             | 命令>>文件          | 以追加的方式，把命令的错误输出输出到指定的文件或设备当中     |
| 正确输出和错误输出同时保存 | 命令>文件 2>&1      | 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中     |
| 正确输出和错误输出同时保存 | 命令>文件 2>>&1     | 以追加的方式，把正确输出和错误输出都保存到同一个文件当中     |
| 正确输出和错误输出同时保存 | 命令&>文件          | 以覆盖的方式，把正确输出和错误输出都保存到同一个文件当中     |
| 正确输出和错误输出同时保存 | 命令&>>文件         | 以追加的方式，把正确输出和错误输出都保存到同一个文件当中     |
| 正确输出和错误输出同时保存 | 命令>>文件1 2>文件2 | 以覆盖的方式，正确的输出追加到文件1中，把错误输出追加到文件2中 |

**输出重定向**

* `wc` 命令的功能为统计指定文件中的行数、字数、字节数，并将统计结果显示输出
* 命令`<`文件把文件作为命令的输入

```bash
wc < a.txt
```

**管道符号**

* 多命令顺序执行

| **多命令执行符** | **格式**         | 作用                                                         | 案例                |
| ---------------- | ---------------- | ------------------------------------------------------------ | ------------------- |
| ;                | 命令1;命令2      | 多个命令执行，命令之间没有任何逻辑联系                       | echo 1;echo 2;      |
| &&               | 命令1&&命令2     | 逻辑与 当命令1正确执行，则命令2才会执行 当命令1执行不正确，则命令2不会执行 | echo 1&&echo 2;     |
| \|\|             | 命令1 \|\| 命令2 | 逻辑或 当命令1执行不正确，则命令2才会执行 当命令1正确执行，则命令2不会执行 | echo 1 \|\| echo 2; |

```bash
echo 1;echo 2;
echo 1&&echo 2;
echo 1||echo 2;
```

**管道符号**

* 命令1 的正确输出会作为命令2的操作对象
* 命令1 | 命令2

```bash
ls /etc/ | more 
netstat -an | grep ESTABLISHED | wc -l
```

**通配符**

* 匹配文件名和目录名

| 通配符 | 作用                                      |
| ------ | ----------------------------------------- |
| ?      | 匹配一个任意字符                          |
| *      | 匹配0个或任意字符，也就是可以匹配任意内容 |
| []     | 匹配中括号中任意一个字符                  |
| [-]    | 匹配中括号中任意一个字符,-代表范围        |
| [^]    | 匹配不是中括号中的一个字符                |

**其他符号**

| 符号 | 作用                                                         |
| ---- | ------------------------------------------------------------ |
| ''   | 单引号。在单引号中所有的特殊符号，如$和`都没有特殊含义       |
| ""   | 双引号，在双引号里特殊符号都没有特殊含义，但是 $ ` \ 例外，拥有调用变量值，引用命令和转义的含义 |
| ``   | 反引号，扩起来的是系统命令                                   |
| $()  | 和反引号一样                                                 |
| #    | 在shell脚本中，#开头的行代表注释                             |
| $    | 用于调用变量的值                                             |
| \    | 转义符号                                                     |

```bash
echo '$PATH' # 字符串：$PATH
echo "$PATH" # 输出$PATH变量
echo `ls`
echo $(ls)
echo -e "a\tb"
```

#### 变量

什么是变量：

* 可以变化的量
* 变量必须以字母或下划线开头，名字中间只能由字母，数字和下划线组成
* 变量名的长度不得超过255个字符
* 变量名在有效范围内必须唯一
* 变量默认类型都是字符串

变量分类：

* 字符串
* 整型
* 浮点型
* 日期型

用户自定义变量：

* 这些变量的值是自己定义的
* 变量名不能为数字开头
* 等号左右两边不能有空格

定义变量

```zhu
name="zhufeng"
age=10
```

输出变量值

```bash
echo $变量名
```

值默认都是字符串

```bash
x=1
y=2
z=3
k=$x+$y+$z
echo $k # 1+2+3
```

在赋值的时候引用变量

```bash
m="$x"2
n=${x}2
echo $m,$n
```

##### set

> 查询系统中默认所有已经生效的变量，包括系统变量，也包括自定义变量

```bash
set | grep zhufeng 
```

##### unset 

> 删除变量

```bash
unset a 
```

##### 环境变量

* 环境变量是全局变量，而自定义变量是局部变量
* 自定义变量会在当前的shell中生效，而环境变量会在当前shell以及其子shell中生效
* 这种变量主要保存的是和系统操作环境相关的数据
* 变量可以自定义，但是对系统生效的环境变量名和变量作用是固定的

**自定义环境变量**

```bash
export 变量名=变量值
export envName=prod
```

**env**

> 仅仅用来查看环境变量，而不看到本地变量

```bash
env | grep envName
```

**常用环境变量**

| 变量名     | 含义                                                | 示例                             |
| ---------- | --------------------------------------------------- | -------------------------------- |
| HOSTNAME   | 主机名                                              | HOSTNAME=localhost               |
| SHELL      | 当前的shell                                         | SHELL=/bin/bash                  |
| HISTSIZE   | 历史命令条数                                        | HISTSIZE=1000                    |
| SSH_CLIENT | 当前操作环境如果是用SSH连接的话，这里会记录客户端IP | SSH_CLIENT=192.168.1.10057596 22 |
| USER       | 当前登录的用户                                      | USER=root                        |

```bash
echo $HOSTNAME
echo $SHELL
echo $HISTSIZE
echo $SSH_CLIENT
echo $USER
```

**path**

> 系统搜索路径

```bash
echo $PATH
# /usr/lib/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
```

如果想把一个自定义的脚本直接可以执行，或者把这个文件拷贝到目标目录下，或者把脚本所在目录添加到环境变量中的`$PATH`路径中

/root/shells/hello.sh

```shell
#!/usr/bin/bash
echo hello
```

```bash
export PATH="$PATH":/root/shelles
hello.sh
```

**语系环境变量**

* 查询当前系统语系
* 在Linux中通过`locale`来设置系统运行的不同语言环境，locale由ANSLC提供支持，locale的命名规则为 <语言>_<地区>.<字符集编码>，如`zh_CN.UTF-8`，zh代表中文，CN代表大陆地区，UTF-8表示字符集
* LANG：定义系统主语系的变量

```bash
locale
LANG=zhu_CN.UTF-8
echo $LANG
```

**中文支持**

* 图形界面可以支持中文
* 第三方工具比如xshell语系设置正确可以支持中文
* 虚拟机中的纯字符界面不支持中文

##### 位置参数变量

> 这种变量主要是用来向脚本当中传递参数或数据的，变量名不能自定义，变量作用是固定的

| 位置参数变量 | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| $n           | n为数字，$0代表命令本身，`$1-$9`代表第1到第9个参数，10以上的参数需要用大括号包含，如`${10}` |
| $*           | 这个变量代表命令中所有的参数，$*把所有的变量看成一个整体     |
| $@           | 这个变量也代表命令行中所有的参数，包裹$@把每个参数进行区分   |
| $#           | 这个变量代表命令行中所有参数的个数                           |

sum.sh

```bash
#!/usr/bin/bash
num1=$1
num2=$2
sum=$((num1+num2))
echo $sum
```

```bash
sh sum.sh 3 4
```

vi for.sh

```shell
#!/usr/bin/bash
for i in "$@"
  do 
    echo "i=$i"
  done 
```

```bash
sh for.sh 1 2 3
i=1
i=2
i=3
```

vi for2.sh 

```bash
#!/bin/bash
for i in "$#"
  do
    echo "i=$i"
  done 
```

```bash
sh for2.sh 1 2 3 
# i= 1 2 3
```

vi for3.sh 

```shell
#!/bin/bash
echo "$#"
```

```bash
sh for3.sh 1 2 3 
3
```

##### 预定义变量

> 是脚本中已经定义好的变量，变量名不能自定义，变量作用也是固定的

| 位置参数变量 | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| $?           | 最后一次执行的命令的返回状态，0表示正确执行，非0表示错误执行 |
| $$           | 当前进程的进程好（PID）                                      |

```bash
echo $?
echo $$
```

##### read

```bash
read [选项] [变量名]
```

| 选项 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -p   | 提示信息，在等待read输入时，输出提示信息                     |
| -t   | 秒数：read命令会一直等待用户输入，使用此选项可以指定等待时间 |
| -n   | 字符数，read命令值接受指定的字符数，就会执行                 |
| -s   | 预测输入的数据，适用于机密信息的输入                         |

```shell
#!/bin/bash
read -p 'please input your name:' -t 5 name
echo -e "\n"
read -p 'please input you gender[m/f]:' -n 1 gender
echo -e "\n"
read -p 'please input your password:' -s password
echo -e "\n"
echo $name,$gender,$password
```

```bash
sh read.sh
```

#### 运算符

> linux定义变量是弱类型并且默认是字符串类型

##### declare命令

**declare命令**

* 用来声明变量类型
* declare [+-] [选项] 变量名

| 选项 | 含义                       |
| ---- | -------------------------- |
| -    | 给变量设定类型属性         |
| +    | 取消变量的类型属性         |
| -a   | 将变量声明为数组类型       |
| -i   | 将变量声明为整形类型       |
| -x   | 将变量声明为环境变量       |
| -r   | 将变量声明为只读变量       |
| -p   | 显示指定变量的被声明的类型 |

```bash
a=1
b=2
c=$a+$b
echo $c
1+2
declare -i c=$a+$b //声明成整型
echo $c
3
declare +i c //取消变量的类型属性
c=$a+$b
echo $c
1+2
declare -i c="3" //给变量设定类型属性
declare -p c //显示指定变量的被声明的类型
//declare -i c="3"

//声明环境变量
declare -x kk=1
set | grep kk      //查看所有变量
env | grep kk      //只查看系统变量

declare -r x //只读
x=2  //bash: x: readonly variable
```

##### 数组

```bash
//声明为数组类型
declare -a names;

names[0]=zhangsan
names[1]=lisi

//默认只打印第一个元素
echo ${names}
zhangsan
//打印第2个元素
echo ${names[1]}
lisi
//打印全部
echo ${names[*]}
zhangsan lisi
```

##### 声明环境变量

* export 最终执行的是`declare -x`命令
* `declare -p`可以查看所有的类型

```bash
export NAME=zhufeng
declare -x NAME=zhufeng
```

##### 只读属性

```bash
declare -r gender=m
gender=f
# -bash: gender: readonly variable
```

##### 查询变量属性

* `declare -p`查询所有遍历的属性
* `declare -p 变量名` 查询指定变量的属性

##### 数值运算的方法

> 只要用`declare`声明的变量的时候指定类型就可以进行数值运算

**expr或let**

* +/-... 运算符号左右两边必须有空格，否则还是整块输出

```bash
num1=2
num2=3
sum=$(expr $num1 + $num2)
echo $sum
5
```

```bash
sum2=$(($num1+$num2))
echo $sum2
5
sum3=$[$num1+$num2]
echo $sum3
5

d=$(date)
echo $d
```

优先级

![优先级](/medias/imges/yw/priority.png)

```bash
result=$(((1+2)*4/2))
echo $result
6
```

#### 环境变量配置文件

##### source命令

* 修改完配置文件后，必须注销重新登录才能生效，使用`source`命令可以不用重新登录
* source配置文件
* `. 配置文件`

##### 环境变量配置文件简介

- PATH、HISTSIZE、PS1、HOSTNAME等环境变量写入对应的环境变量配置文件

- 环境变量配置文件中主要是定义地系统操作环境生效的系统默认环境变量，如`PATH`等此文件登录时起作用的环境变量

| 路径            | 说明                                     |
| --------------- | ---------------------------------------- |
| /etc/profile    | 系统变量配置文件                         |
| /etc/bashrc     | 系统的sh变量配置文件                     |
| ~/.bash_profile | 用户的变量配置文件，只会对当前用户生效   |
| ~/.bashrc       | 用户的sh变量配置文件，只会对当前用户生效 |

![配置文件](/medias/imges/yw/profile.png)

##### 环境变量配置文件的功能

`/etc/profile`

- 在这里修改系统变量

```bash
cat  /etc/profile | grep USER
```

| **变量名**           | **含义** |
| -------------------- | -------- |
| USER                 | 用户名   |
| LOGNAME              | 登录名   |
| MAIL                 | 邮箱地址 |
| PATH                 | 查找路径 |
| HOSTNAME             | 主机名   |
| umask                | 权限掩码 |
| /etc/profile.d/星.sh |          |

`~/.bash_profile`

- 在这里修改`PATH`路径
- 调用 ~/.bashrc

`~/.bashrc`

- 在这里改别名,配置alias
- 调用 `/etc/bashrc`

`/etc/bashrc`

- `PS1` 登录提示符在这里修改
- `umask`
- `PATH`变量
- 调用 `/etc/profile.d/星.sh`文件

其他配置文件

| 配置文件路径      | 含义                                                  |
| ----------------- | ----------------------------------------------------- |
| `~/.bash_logout`  | 注销时生效的环境变量配置文件                          |
| `~/.bash_history` | 脚本历史 ，当正确退出计算机的时候会将历史记录写入文件 |

shell登录信息

- 本地终端欢迎信息 `/etc/issue`
- 远程终端欢迎信息 `/etc/issue.net`
- 不管远程还是本地都可以生效 `/etc/motd`

| 参数 | 含义                       |
| ---- | -------------------------- |
| \d   | 当前系统日期               |
| \s   | 显示操作系统名称           |
| \l   | 显示登录的终端号           |
| \m   | 显示硬件体系结构，如i386等 |
| \n   | 显示主机名                 |
| \o   | 显示域名                   |
| \r   | 显示内核版本               |
| \t   | 显示当前系统时间           |
| \u   | 显示当前登录用户的序列号   |

```bash
vi /etc/ssh/sshd_config
Banner /etc/issue.net
service sshd restart
```

#### 正则表达式

概念：

* 正则表达式是用于描述字符排列和匹配模式的一种语法规则
* 它主要用于字符串的模式分割，匹配查找及替换操作

通配符：

* 通配符用来匹配符合条件的文件名，通配符是完全匹配
* Is find 这些命令不支持正则，只能使用通配符

| 符号 | 含义                         |
| ---- | ---------------------------- |
| ？   | 匹配任意一个字符             |
| *    | 匹配任意多个字符             |
| []   | 匹配中括号中范围内的一个字符 |

正则表达式

* 正则表达式是用来在文件中匹配符合条件的字符串，是包含匹配
* grep  awk sed 等都可以支持正则表达式

元字符

`alias grep='grep --color=auto'`

| 元字符 | 作用                                                         | 示例                  |
| ------ | ------------------------------------------------------------ | --------------------- |
| *      | 前一个字符匹配 0 次或任意多次                                | grep 1* reg.txt       |
| .      | 匹配除换行符外的任意一个字符                                 | grep . reg.txt        |
| ^      | 匹配行首。例如，^hello 会匹配以 hello 开头的行               | grep ^a reg.txt       |
| $      | 匹配行尾。例如，hello& 会匹配以 hello 结尾的行               | grep a$ reg.txt       |
| []     | 匹配屮柄号屮指定的任意一个字符，而且只匹配一个字符。<br/>例如.[aoeiu]匹配任意一个元音字母， [0-9] 匹配任意一位数字，<br/>[a-z]\[0-9] 匹配由小写字母和一位数字构成的两位字符 | grep ab[bc]c reg.txt  |
| [^]    | 匹配除中括号中的字符以外的任意一个字符。例如，\[^0-9] 匹配任意一位非数字字符，<br/>\[^a-z] 匹配任意一位非小写字母 | grep a[^fg]c reg.txt  |
| \      | 转义符，用于取消特殊符号的含义                               | grep \.$ reg.txt      |
| {n}    | 表示其前面的字符恰好出现 n 次。例如，[0-9]{4} 匹配4位数字，\[1]\[3-8][0-9]{9} 匹配手机号码 | grep "a{1}" reg.txt   |
| (n,}   | 表示其前面的字符出现不少于 n 次。例如，[0-9]{2,} 匹配两位及以上的数字 | grep "a{1,}" reg.txt  |
| {n,m}  | 表示其前面的字符至少出现 n 次，最多出现 m 次。例如，[a-z]{6,8} 匹配 6〜8 位的小写字母 | grep "a{2,3}" reg.txt |

##### cut

- cut用来提取文本中的某一部分文本
- cut [选项] 文件名
  - -f 列号，用来指定要提取的列
  - -d 分隔符，按照指定分隔符分割列,默认分隔符是TAB制表符

提取用户名和它使用的shell

```bash
cat /etc/passwd | cut -f 1,7 -d :
```

##### printf

- 按规定格式输出
- printf 输出类型 输出内容

| 参数  | 含义                                                         |
| ----- | ------------------------------------------------------------ |
| %ns   | 输出字符串,n是数字指代输出几个字符                           |
| %ni   | 输出整数,n是指输出几个数字                                   |
| %m.nf | 输出浮点数,m和n是数字，指代输出的整数位数和小数位数，如%6.2f代表输出6位位，2位小数，4位整数 |

```bash
printf "%s\t%s\t%s\t%s\t%s\t\%s\n" $(df -h | grep /dev/vda1) | cut -f 1,5
```

##### awk

- awk '条件1{动作1} 条件2{动作2}...' 文件名

条件(Pattern)

- 一般使用关系表达式作为条件
- x > 10 判断变量x是否大于10
- x >=10 大于等于
- x <=10 小于等于

- 动作(Action)
  - 格式化输出
- $0 整行 $1 第一列...

```bash
df -h | grep /dev/vda1 |awk '{print $5}'| cut -d '%' -f 1
```

**begin end**

- awk可以正确截取制表符和空格
- begin 在所有的输出之前打印
- end 在所有的输出之后打印

```bash
echo "" > numbers.txt
awk 'BEGIN{print "开始"}END{print "结束"}' numbers.txt 
```

**FS**

- Field Separator，字段分隔符

```bash
awk 'BEGIN{FS=":"}{print $1"\t"$2}' /etc/passwd
```

**声明变量**

numbers.txt

```txt
1
2
3
```

```bash
awk 'BEGIN{sum=0}{sum=sum+$1}END{print sum}' numbers.txt 
#6
```

**多条件**

score.txt

```txt
zhangsan 91
li 81
wangwu 71
```

```bash
awk '$2>90{print $1"\t优秀"}$2>80{print $2"\t良好"}' score.txt
```

**NR**

* NR,表示awk开始执行程序后所读取的数据行数

```bash
awk '{print NR,$0}' score.txt
```

**OFS**

- OFS Out of Field Separator，输出字段分隔符

```bash
echo "i love you" | awk 'BEGIN{ FS=" ";OFS="-" }{print $1,$2,$3}'
```

##### sed命令

- sed是一个轻量级编辑器，主要用来对数据进行选取、替换、替换和新增操作
- sed [选项] '[动作]' 文件名
- 所有的动作都必须用单号号括起来

- 类型类似于批量`vi`操作

动作

| 参数 | 含义                                                         | 示例                               |
| ---- | ------------------------------------------------------------ | ---------------------------------- |
| a    | 追加，在每一行或者指定行下面添加一行或多行                   | `sed '1a newline' score.txt`       |
| c    | 行替换，用c后面的字符串替换掉原始整个数据行                  | `sed 'c newline' score.txt`        |
| s    | 字符串替换,用一个字符串替换另外一个字符串，格式为 "行范围s/旧字符串/新字符串/g" | `sed '3s/lisi/lisisi/g' score.txt` |
| i    | 插入，在当前行插入一行或多行                                 | `sed '1i newline' score.txt`       |
| d    | 删除指定的行                                                 | `sed '1,2d' score.txt`             |
| p    | 打印,输出指定的行                                            | `sed -n '2p' score.txt`            |

选项

| 参数 | 含义                                                         | 示例                                     |
| ---- | ------------------------------------------------------------ | ---------------------------------------- |
| -n   | 一般sed命令会把所有的数据都输出到屏幕上，如果加入此选项则只会把处理过的行输出到屏幕上 | `sed -n '2p' score.txt`                  |
| -e   | 允许对输入数据应用多条sed编辑命令                            | `sed -e 's/91/92/g;s/81/82/g' score.txt` |
| -i   | 用sed的修改直接修改编辑的文件，而不是在屏幕上输出            | `sed -i '1i newline' score.txt`          |

排序命令 sort 

- sort [选项] 文件名
- 选项

| 选项     | 含义                                                         | 示例                                 |
| -------- | ------------------------------------------------------------ | ------------------------------------ |
| -f       | 忽略大小写                                                   | sort -f -t ":" -n -k 5,5 /etc/passwd |
| -n       | 以数值型进行排序，默认使用字符串顺序                         | sort -t ":" -n -k 3,3 /etc/passwd    |
| -r       | 反向排序，默认从小到大                                       | sort -r /etc/passwd                  |
| -t       | 指定分割符，默认分割符是制表符                               | sort -t ":" -k 3,3 /etc/passwd       |
| -k n[,m] | 按照指定的字段范围排序。从第n个字段开始，到第m个字段结束，默认是到行尾 | sort -t ":" -k 3,3 /etc/passwd       |

```bash
sort /etc/passwd
sort -r /etc/passwd
sort -t ":" -k 3,3 /etc/passwd
```

##### wc

- wc [选项] 文件名

| 选项 | 含义         |
| ---- | ------------ |
| -l   | 只统计行数   |
| -w   | 只允许单词数 |
| -m   | 只统计字符数 |

```bash
wc wc.txt
```

####流程控制

##### 条件判断

**按照文件类型进行判断**

| 选项 | 含义                           |
| ---- | ------------------------------ |
| -d   | 文件是否存在并且是目录         |
| -e   | 文件是否存在                   |
| -f   | 文件是否存在并且是普通文件     |
| -b   | 文件是否存在并且是块设备文件   |
| -c   | 文件是否存在并且是字符设备文件 |
| -L   | 文件是否存在并且是链接文件     |
| -p   | 文件是否存在并且是管道文件     |
| -s   | 文件是否存在并且是否为非空     |
| -S   | 文件是否存在并且是套接子文件   |

```bash
touch 1.txt 
test -e 1.txt 
[ -e 1.txt ]
echo $?
```

exist.sh

```bash
[ -e 1.txt ] && echo "yes" || echo "no"
# yes
[ -e 11.txt ] && echo "yes" || echo "no"
# no
```

**按照文件权限进行判断**

| 选项 | 含义                             |
| ---- | -------------------------------- |
| -r   | 文件是否存在，并且是否拥有读权限 |
| -w   | 文件是否存在，并且是否拥有写权限 |
| -x   | 文件是否存在，并且是否有执行权限 |

```bash
echo read  > read.txt
echo write  > write.txt
echo execute  > execute.txt

chmod u+w write.txt 
chmod u+x execute.txt

[ -r read.txt ]&&echo "read yes"|| echo "no"
[ -w write.txt ]&&echo "write yes"|| echo "no"
[ -x execute.txt ]&&echo "execute yes"|| echo "no"
```

**两个文件间的比较**

| 选项            | 含义                                                  |
| --------------- | ----------------------------------------------------- |
| 文件1 -nt 文件2 | 判断文件1的修改时间是否比文件2 的新                   |
| 文件1 -ot 文件2 | 判断文件1的修改时间是否比文件2的 旧                   |
| 文件1 -ef 文件2 | 判断文件1 和 文件2的inode号是否一致，可用于判断硬链接 |

```bash
[ write.txt -nt read.txt ]&&echo "write is older than read"|| echo "no"
[ read.txt -ot write.txt ]&&echo "read is older than write"|| echo "no"
ln execute.txt execute2.txt
[ execute.txt -ef execute2.txt ]&&echo "execute and execute2.txt are the same"|| echo "no"
```

**两个整数间的比较**

| 选项            | 含义                       |
| --------------- | -------------------------- |
| 整数1 -eq 整数2 | 判断整数1是否和整数2相等   |
| 整数1 -ne 整数2 | 判断整数1是否和整数2不相等 |
| 整数1 -gt 整数2 | 判断整数1 是否大于整数2    |
| 整数1 -lt 整数2 | 判断整数1是否小于整数2     |
| 整数1 -ge 整数2 | 判断整数1是否大于等于整数2 |
| 整数1 -le 整数2 | 判断整数1是否小于等于整数2 |

```bash
[ 2 -eq 2 ]&&echo "2==2"|| echo "no"
[ 3 -ne 2 ]&&echo "2!=2"|| echo "no"
[ 3 -gt 2 ]&&echo "2>2"|| echo "no"
[ 1 -lt 2 ]&&echo "2<2"|| echo "no"
[ 2 -ge 2 ]&&echo "2>=2"|| echo "no"
[ 2 -le 2 ]&&echo "2<=2"|| echo "no"
```

**字符串的判断**

| 选项               | 含义                           |
| ------------------ | ------------------------------ |
| -z 字符串          | 判断字符串是否为空             |
| -n 字符串          | 判断字符串是否为非空           |
| 字符串1 == 字符串2 | 判断字符串1 是否和字符串2相等  |
| 字符串1 != 字符串2 | 判断字符串1是否和字符串2不相等 |

```bash
name=zhufeng
[ -z "$name" ]&&echo "空"|| echo "非空"
[ -n "$name" ]&&echo "非空"|| echo "空"
name2=zhufeng
[ "$name" == "$name2" ]&&echo "相等"|| echo "不相等"
[ "$name" != "$name2" ]&&echo "不相等"|| echo "相等"
```

**多重条件判断**

| 选项           | 含义   |
| -------------- | ------ |
| 判断1 -a 判断2 | 逻辑与 |
| 判断1 -o 判断2 | 逻辑或 |
| !判断          | 逻辑非 |

```bash
[ 2 -gt 1 -a 3 -gt 2 ]&&echo "yes"|| echo "no"
[ 2 -gt 1 -a 3 -gt 4 ]&&echo "yes"|| echo "no"
[ 2 -gt 1 -o 3 -gt 4 ]&&echo "yes"|| echo "no"
[ ! 3 -gt 4 ]&&echo "yes"|| echo "no
```

##### 单分支`if`语句

* if语句使用`fi`结尾
* [条件判断式]就是使用`test`命令进行判断，所以中括号和条件判断式组件必须有空格
* then后面跟符合条件之后执行的程序，可以放在[]之后，用 `;`分隔，也可以换行省略`;`

语法：

```bash
if [条件判断];then
 代码体
fi

if [条件判断]
then
 代码体
fi
```

```bash
if [ 2 -gt 1 ];then echo bigger; fi

if [ 2 -gt 1 ]
then
 echo bigger
fi
```

判断当前用户是否是root用户

```bash
#!/bin/bash
user=$(whoami)
user=`whoami`
if [ "$user" == root ]
then
echo "我是root用户"
fi
```

##### 双分支if语句

语法：

```bash
if [条件判断]
then
 代码体1
else
 代码体2 
fi
```

判断是否目录

isDir.sh

```bash
#!/bin/bash
read -t 10 -p "请输入一个路径" dir
if [ -d "$dir" ]
then
  echo "$dir是目录"
else 
  echo "$dir不是目录"
fi
```

##### 多分支if语句

语法

```bash
if [条件判断1]
then
 代码体1
elif [条件判断2] 
 代码体2
else
 代码体3
fi
```

grade.sh

```bash
#!/bin/bash
read -p "请输入一个分数" grade
if [ "$grade" -gt 90 ]
 then
  echo 优秀
elif [ "$grade" -gt 80 ]
  then
   echo 良
else
   echo 差
fi
```

##### case语句

> case和if 都是多分支判断语句，if能判断多个条件，case只能判断一个条件

语法：

```bash
case 变量名 in 
值1)
  代码块1
  ;;
值2)
  代码块2
 ;;
*)
  代码块3
 ;;
esac
```

case.sh

```shell
#!/bin/bash
read -p "yes or no?" -t 30 choose
case $choose in 
  "yes")
    echo '是'
    ;;
  "no")
    echo "否"
   ;;
  *)
    echo 其它
   ;;
esac
```

##### for循环

语法

```bash 
for 变量 in 值1 值2 值3
do
代码块
done
```

for.sh

```shell
#!/bin/bash
for i in 1 2 3
do
 echo $i
done

# 或

#!/bin/bash  
for((i=1;i<=10;i++));  
do   
 echo $(($i));
done 
```

##### while循环

> while循环是不定循环，也称为条件循环，只要条件判断成立，就会一直继续

```bash
while [条件判断式]
do
 代码块
done
```

while.sh

```shell
#!/bin/bash
i=1
result=0
while [ $i -le 100 ]
 do
   result=$(($result+i))
   i=$(($i+1))
 done
echo $result
```

##### util循环

> 直到条件不成立停止

until.sh

```shell
#!/bin/bash
i=1
result=0
until [ $i -gt 100 ]
 do
   result=$(($result+i))
   i=$(($i+1))
 done
echo $result
```

#### 函数

* `linux shell`可以用户定义函数，然后在shell脚本中可以随便调用
* 可以带`function fun()`定义，也可以最`fun()`定义，不带任何参数
* 调用不需要加`()`

简单函数

```bash
[ function ] funcName [()]
{
    action;
    [return int;]
}
```

```bash
start(){
>  echo start
> }
 start
```

```shell
#!/bin/bash
start(){
 echo start
}
start
```

返回值

> 参数返回，可以显示加 `return `返回，如果不加，将以最后一条命令运行结果作为返回值

```bash
sum(){
>  result=$(($1+$2))
>  return $result
> }
sum4 2 3
echo $?
5
```

参数说明

| 参数处理 | 说明                                                        |
| -------- | ----------------------------------------------------------- |
| $#       | 传递到脚本的参数个数                                        |
| $*       | 以一个单字符串显示所有向脚本传递的参数                      |
| $@       | 以$*相同，但是使用时加引号，并在引号之后返回每个参数        |
| $$       | 脚本运行的当前进程ID号                                      |
| $?       | 显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误 |

#### shell实战

注意事项：

* 开头加解释器：`# !/bin/bash`和注释说明
* 命名建议规则：变量名大写、局部变量小写，函数名小写，名字体现出实际作用
* 默认变量是全局的，在函数中变量`local`指定为局部变量，避免污染其它作用域
* `set -e`遇到执行非0时退出脚本，`set -x`打印执行过程
* 写脚本一定要先测试在上生成环境

**检查主机存活状态**

ip.sh

```bash
#!/bin/bash
for IP in $@; do
  if ping -c 1 $IP &>/dev/null; then
     echo "$IP is ok."
  else
     echo "$IP is wrong!"
  fi
done  
```

> sh ip.sh 127.0.0.1