---
layout: post
title: 'Linux(Centos 7)环境下安装Redis'
subtitle: 'Linux(Centos 7)环境下安装Redis'
date: 2020-11-24
categories: Redis
tags: Redis
---

准备工作：

本人测试环境：Win10

xshell远程登录Linux

Linux： Centos

软件包：redis-3..2.6.tar.gz （Linux下redis安装包） 

==================================================================================================================================================================

开始安装：

第一步：进入安装目录

```
[root@localhost home]# cd /home/data
```

第二步：wget 下载redis版本

```
 wget http://download.redis.io/releases/redis-3.2.6.tar.gz
```

第三步：.解压编译

```
tar -zxvf redis-3.2.6.tar.gz
```


 进入 redis-3.2.6 然后make

```
[root@localhost redis]# cd redis-3.2.6
[root@localhost redis-3.2.6]# make
```

\# make CFLAGS=”-march=i686”;

\#####################################################################

说明：make 后面一串代码: CFLAGS=”-march=i686” 是防止软件版本与Linux硬件不适配的。

Linux有i386和i686这种区别；在redis软件与硬件不适配的情况下直接使用make命令编译，会报这样的错误：

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

注意：但是如果在make后面加上CFLAGS=”-march=i686” 这段代码就会可以解决问题，编译成功

如果你make直接成功，不报错，就不用加CFLAGS这串代码了

期间如果gcc没有安装会报错，根据提示安装gcc

```
 [root@localhost redis]# yum install -y gcc g++ gcc-c++
```

第四步：编译成功后进入redis-2.6.14/src 目录

4.拷贝redis-cli 、 redis-server 到 /usr/local/redis/目录 （先创建usr/local/下的redis目录）

```
[root@localhost redis]# cd /usr/local/soft/redis-2.6.14/src
[root@localhost redis]# ll
```

会看到有几个可执行文件：

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image004.jpg)

这里我们只需要用到两个文件就可以了：redis-server和redis-cli

第五步：拷贝redis-conf到/usr/local/redis目录

我是在/usr/local/目录下创建了一个redis 目录

```
[root@localhost redis]# cd /usr/local/
[root@localhost redis]# mkdir redis
```

然后将src目录下的redis-server和server-cli 复制到redis目录下

```
[root@localhost redis]# cp redis-cli redis-server /usr/local/redis/
```

然后再回到redis-2.6.14 源码目录 将redis.conf 文件复制到 redis 目录下

```
[root@localhost redis]# cp redis.conf /usr/local/redis/
```

最终结果是，redis目录下有了三个文件 如下图：

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image006.jpg)

到这里，就算完成了 。

============================================================================================================================================================

接下来运行redis服务：

```
[root@localhost redis]# ./redis-server
```

出现下面的界面，就说明你的redis可以正常使用了

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image008.jpg)

 

现在还有个问题：redis在前台运行，我不能做其他事情怎么办？如何将redis放在后台运行？

方法：修改redis.conf 文件，将daemonize no 改为daemonize yes

```
[root@localhost redis]# vi redis.conf
```

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image010.jpg)

将no修改为yes ； 保存退出

redis 拒绝远程访问解决

```
1、注释掉 bind 127.0.0.1，即可让所有ip访问
2、protected-mode no
```

杀掉rdis进程，然后再次打开redis服务

```
[root@localhost redis]# killall redis-server
[root@localhost redis]# ./redis-server redis.conf
```

出现如下界面说明成功让redis在后台运行

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image011.png)

如果想查看进程里面有没有redis服务，可以用pstree命令查看进程：

```
[root@localhost redis]# pstree
```

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image012.png)

=======================================================================================================

后台运行成功以后，用redis-cli客户端连接redis：

```
[root@localhost redis]# ./redis-cli  （这里是本机连接，如果是连接网络机器 ：./redis-cli IP  端口号）
```

上面代码中IP地址和端口号可以不写，不写的话，默认连接本机redis

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image013.png)

 

查看redis里面有没有数据

```
keys *
```

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image014.png)

 

暂时还没有数据

来添加一条数据吧！

```
set mykey “tom”
```

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image015.png)

 

读取一条数据：

```
get mykey
```

![img](file:///C:/Users/YUANLI~1/AppData/Local/Temp/msohtmlclip1/01/clip_image016.png)

 

 至此，redis的安装和测试就讲完了，内容经过验证无误。

=======================================================================================================