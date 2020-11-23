---
layout: post
title: 'Linux(Centos 7)环境下安装Mysql'
subtitle: 'Linux(Centos 7)环境下安装Mysql'
date: 2020-11-23
categories: Mysql
tags: Mysql
---

Linux(Centos 7)环境下安装Mysql

下面记录了我在Linux(Centos 7)环境下安装Mysql的完整过程，**实操记录，绝非水文**，如有错误或遗漏，欢迎指正。

![\color{#FFA500}{ 注意1：}](https://math.jianshu.com/math?formula=%5Ccolor%7B%23FFA500%7D%7B%20%E6%B3%A8%E6%84%8F1%EF%BC%9A%7D) ***本文档讲解安装版本为mysql-5.7.24，对于5.7.24之后的版本，不适用此说明文档，主要原因在于之后版本的mysql配置文件的目录位置和结构有所改变，使用此说明可能会出现找不到配置文件或者配置后不生效的情况。\***

![\color{#FFA500}{ 注意2：}](https://math.jianshu.com/math?formula=%5Ccolor%7B%23FFA500%7D%7B%20%E6%B3%A8%E6%84%8F2%EF%BC%9A%7D) ***安装过程中务必保证文件路径的前后统一，否则可能会导致不可预期的结果，推荐直接使用文中的命令进行操作。\***

#### 一 安装前准备

1、检查是否已经安装过mysql，执行命令

```csharp
[root@localhost /]# rpm -qa | grep mysql
```

![image-20201119103749210](/assets/img/mysql/image-20201119103749210.png)

从执行结果，可以看出我们已经安装了**mysql-libs-5.1.73-5.el6_6.x86_64**，执行删除命令

```css
[root@localhost /]# rpm -e --nodeps mysql-libs-5.1.73-5.el6_6.x86_64
```

再次执行查询命令，查看是否删除

```csharp
[root@localhost /]# rpm -qa | grep mysql
```

![image-20201119103945937](/assets/img/mysql/image-20201119103945937.png)

2、查询所有Mysql对应的文件夹

```ruby
[root@localhost /]# whereis mysql
mysql: /usr/bin/mysql /usr/include/mysql
[root@localhost lib]# find / -name mysql
/data/mysql
/data/mysql/mysql
```

删除相关目录或文件

```kotlin
[root@localhost /]#  rm -rf /usr/bin/mysql /usr/include/mysql /data/mysql /data/mysql/mysql 
```

验证是否删除完毕

```csharp
[root@localhost /]# whereis mysql
mysql:
[root@localhost /]# find / -name mysql
[root@localhost /]# 
```

3、检查mysql用户组和用户是否存在，如果没有，则创建

```csharp
[root@localhost /]# cat /etc/group | grep mysql
[root@localhost /]# cat /etc/passwd |grep mysql
[root@localhost /]# groupadd mysql
[root@localhost /]# useradd -r -g mysql mysql
[root@localhost /]# 
```

4、从官网下载是用于Linux的Mysql安装包

下载命令：

```cpp
[root@localhost /]#  wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
```

也可以直接到 [mysql官网](https://links.jianshu.com/go?to=https%3A%2F%2Fdownloads.mysql.com%2Farchives%2Fcommunity%2F) 选择对应版本进行下载。

![image-20201119104019777](/assets/img/mysql/image-20201119104019777.png)

#### 二 安装Mysql

1、在执行**wget**命令的目录下或你的上传目录下找到Mysql安装包：**mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz**
 执行解压命令：

```css
[root@localhost /]#  tar xzvf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
[root@localhost /]# ls
mysql-5.7.24-linux-glibc2.12-x86_64
mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
```

解压完成后，可以看到当前目录下多了一个解压文件，移动该文件到**/usr/local/**下，并将文件夹名称修改为**mysql**。

> 如果**/usr/local/**下已经存在**mysql**，请将已存在mysql文件修改为其他名称，否则后续步骤可能无法正确进行。

执行命令如下：

```csharp
[root@localhost /]# mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/
[root@localhost /]# cd /usr/local/
[root@localhost /]# mv mysql-5.7.24-linux-glibc2.12-x86_64 mysql
```

如果**/usr/local/**下不存在**mysql**文件夹，直接执行如下命令，也可达到上述效果。

```csharp
[root@localhost /]# mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/mysql
```

2、在**/usr/local/mysql**目录下创建data目录

```csharp
[root@localhost /]# mkdir /usr/local/mysql/data
```

3、更改mysql目录下所有的目录及文件夹所属的用户组和用户，以及权限

```csharp
[root@localhost /]# chown -R mysql:mysql /usr/local/mysql
[root@localhost /]# chmod -R 755 /usr/local/mysql
```

4、编译安装并初始化mysql,**务必记住初始化输出日志末尾的密码（数据库管理员临时密码）**

```csharp
[root@localhost /]# cd /usr/local/mysql/bin
[root@localhost bin]# ./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql
```

> ##### ***补充说明：\***
>
> 第4步时，可能会出现错误：
>
> ![image-20201119104051505](/assets/img/mysql/image-20201119104051505.png)
>
> 出现该问题首先检查该链接库文件有没有安装使用 命令进行核查
>
> ```csharp
> [root@localhost bin]# rpm -qa|grep libaio   
> [root@localhost bin]# 
> ```
>
> 运行命令后发现系统中无该链接库文件
>
> ```csharp
> [root@localhost bin]#  yum install  libaio-devel.x86_64
> ```
>
> 安装成功后，继续运行数据库的初始化命令，此时可能会出现如下错误：
>
> ![image-20201119104123987](/assets/img/mysql/image-20201119104123987.png)
>
> 执行如下命令后：
>
> ```csharp
> [root@localhost bin]#  yum -y install numactl
> ```
>
> 执行无误之后，再重新执行第4步初始化命令，无误之后再进行第5步操作！

5、运行初始化命令成功后，输出日志如下：

![image-20201119104151571](/assets/img/mysql/image-20201119104151571.png)

记录日志最末尾位置**root@localhost:**后的字符串，此字符串为mysql管理员临时登录密码。

6、编辑配置文件my.cnf，添加配置如下

```ruby
[root@localhost bin]#  vi /etc/my.cnf

[mysqld]
datadir=/usr/local/mysql/data
port=3306
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
symbolic-links=0
max_connections=600
innodb_file_per_table=1
lower_case_table_names=1
character_set_server=utf8
```

`lower_case_table_names`：是否区分大小写，1表示存储时表名为小写，操作时不区分大小写；0表示区分大小写；不能动态设置，修改后，必须重启才能生效：
 `character_set_server`：设置数据库默认字符集，如果不设置默认为latin1
 `innodb_file_per_table`：是否将每个表的数据单独存储，1表示单独存储；0表示关闭独立表空间，可以通过查看数据目录，查看文件结构的区别；

7、测试启动mysql服务器

```csharp
[root@localhost /]# /usr/local/mysql/support-files/mysql.server start
```

显示如下结果，说明数据库安装并可以正常启动

![image-20201119104226807](/assets/img/mysql/image-20201119104226807.png)

> #### 异常情况
>
> 如果出现如下提示信息
>
> ```swift
> Starting MySQL... ERROR! The server quit without updating PID file
> ```
>
> 查看是否存在mysql和mysqld的服务，如果存在，则结束进程，再重新执行启动命令
>
> ```bash
> #查询服务
> ps -ef|grep mysql | grep -v grep
> ps -ef|grep mysqld | grep -v grep
> 
> #结束进程
> kill -9 PID
> 
> #启动服务
> /usr/local/mysql/support-files/mysql.server start
> ```
>
> ![image-20201119104302344](/assets/img/mysql/image-20201119104302344.png)

8、添加软连接，并重启mysql服务

```csharp
[root@localhost /]#  ln -s /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql 
[root@localhost /]#  ln -s /usr/local/mysql/bin/mysql /usr/bin/mysql
[root@localhost /]#  service mysql restart
```

9、登录mysql，修改密码(密码为步骤5生成的临时密码)

```csharp
[root@localhost /]#  mysql -u root -p
Enter password:
mysql>set password for root@localhost = password('yourpass');
注意：输入密码时，Enter password 后面不会有任何显示，此时实际是输入成功的，输入完密码后直接回车即可。或使用：mysql -u root -p 密码 ，回车后，即可直接进入数据库
```

![image-20201119104347680](/assets/img/mysql/image-20201119104347680.png)

10、开放远程连接

```bash
mysql>use mysql;
msyql>update user set user.Host='%' where user.User='root';
mysql>flush privileges;
```

![image-20201119104408609](/assets/img/mysql/image-20201119104408609.png)

11、设置开机自动启动

```csharp
1、将服务文件拷贝到init.d下，并重命名为mysql
[root@localhost /]# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
2、赋予可执行权限
[root@localhost /]# chmod +x /etc/init.d/mysqld
3、添加服务
[root@localhost /]# chkconfig --add mysqld
4、显示服务列表
[root@localhost /]# chkconfig --list
```

至此，mysql5.7.24版本的数据库安装，已经完成。



作者：开心跳蚤
链接：https://www.jianshu.com/p/276d59cbc529
来源：简书