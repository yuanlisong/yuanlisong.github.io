---
layout: post
title: 'Linux安装elasticsearch及相关安装问题'
subtitle: 'elasticsearch-7.3.2安装'
date: 2020-11-23
categories: Elasticsearch
tags: Elasticsearch
---

Linux安装elasticsearch及相关问题 elasticsearch-7.3.2

1.elasticsearch是基于java开发的，所以安装之前需要先安装版本大于等于1.8的jdk

**elasticsearch安装:** 下载---解压---配置---启动

> ```
> wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.3.2.tar.gz
> tar -zxvf elasticsearch-7.3.2.tar.gz
> ```

<img src="/assets/img/es/image-20201119105344208.png" alt="image-20201119105344208" style="zoom:67%;" />

> ```
> vi config/elasticsearch.yml
> ```

<img src="/assets/img/es/image-20201119105407424.png" alt="image-20201119105407424" style="zoom:67%;" />

elasticsearch默认安装后设置的内存是1GB,我这里只是自己的一个乞丐版本的服务器，内存比较小，我就只给他分配256M（根据自己情况使用配置，如果内存不够的话，启动过程中会提示已杀死这类提示）

> ```
> vi config/jvm.options
> ```

<img src="/assets/img/es/image-20201119105436699.png" alt="image-20201119105436699" style="zoom: 67%;" />

ps： 更多具体配置可以在[elasticsearch官网](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.elastic.co%2F)查看

启动： elasticsearch可以接收用户输入的脚本并且执行，处于安全考虑，他不能root账户来启动，不然会报下图错误

<img src="/assets/img/es/image-20201119105529128.png" alt="image-20201119105529128" style="zoom:67%;" />

所以我需要为elasticsearch新建一个系统运行账号

> ```
> groupadd elasticsearch   //新建一个elasticsearch的用户组
> useradd -g elasticsearch elasticsearch //在elasticsearch用户组下面建立一个elasticsearch的用户
> ```

将elasticsearch目录的所有者给刚刚建立的账号

> ```
> chown -R elasticsearch:elasticsearch elasticsearch-7.3.2/
> ```

<img src="/assets/img/es/image-20201119105616446.png" alt="image-20201119105616446" style="zoom:67%;" />

然后切换到刚刚的账号启动elasticsearch

> ```
> su elasticsearch
> ./elasticsearch-7.3.2/bin/elasticsearch
> ```

启动过程中可能会出现下面这类的错误提示

<img src="/assets/img/es/image-20201119105646397.png" alt="image-20201119105646397" style="zoom:67%;" />

错误1： elasticsearch这个用户的最大打开线程数（3894）太低，至少增加到4096

解决：

\1. 查看用户最大打开线程数

> ```
> ulimit -a
> ```

<img src="/assets/img/es/image-20201119105714807.png" alt="image-20201119105714807" style="zoom:67%;" />

\2. 切换到root用户，编辑文件

> ```
> vi /etc/security/limits.conf
> ```

在文件末尾加上下面配置

<img src="/assets/img/es/image-20201119105854889.png" alt="image-20201119105854889" style="zoom:67%;" />

\* 表示匹配所有用户， nproc 表示配置最大打开线程数

\3. 退出从新登录后，再次查看，修改成功

<img src="/assets/img/es/image-20201119105922920.png" alt="image-20201119105922920" style="zoom:67%;" />

错误2： 最大虚拟内存区域vm.max_map_count（65530）太低，至少增加到262144

解决：

在root账号下修改配置文件

> ```
> vi /etc/sysctl.conf
> ```

在末尾添加配置，值大于等于实体的262144就可以

<img src="/assets/img/es/image-20201119105955396.png" alt="image-20201119105955396" style="zoom:67%;" />

添加完成后执行下面命令

> ```
> sysctl -p
> ```

然后可以查看到成功修改成你设置的数字

<img src="/assets/img/es/image-20201119110027443.png" alt="image-20201119110027443" style="zoom:67%;" />

上面问题解决后，切换成elasticsearch账号，继续启动es,看到下图的样子证明就启动成功了

<img src="/assets/img/es/image-20201119110050150.png" alt="image-20201119110050150"  />

## 验证

浏览器输入ip地址,加上自己在配置文件中配置的端口,比如我这里的9201访问

<img src="/assets/img/es/image-20201119110115757.png" alt="image-20201119110115757" style="zoom:67%;" />

我前面配置的host是0.0.0.0,所以可以用外网访问,不需要外网访问的话,就配置127.0.0.1或者对应的内网ip就行



## 安装出现的问题

问题出现环境，OS版本：CentOS-7-x86_64-Minimal-1708；ES版本：elasticsearch-6.2.2。

1、max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]

　　每个进程最大同时打开文件数太小，可通过下面2个命令查看当前数量

```
ulimit -Hn
ulimit -Sn
```

　　修改/etc/security/limits.conf文件，增加配置，用户退出后重新登录生效

```
*               soft    nofile          65536
*               hard    nofile          65536
```

![image-20201119104655238](/assets/img/es/image-20201119104655238.png)

 

2、max number of threads [3818] for user [es] is too low, increase to at least [4096]

　　问题同上，最大线程个数太低。修改配置文件/etc/security/limits.conf（和问题1是一个文件），增加配置

```
*               soft    nproc           4096
*               hard    nproc           4096
```

　　可通过命令查看

```
ulimit -Hu
ulimit -Su
```

![image-20201119104728783](/assets/img/es/image-20201119104728783.png)

修改后的文件：

<img src="/assets/img/es/image-20201119104753225.png" alt="image-20201119104753225" style="zoom:80%;" />

3、max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

　　修改/etc/sysctl.conf文件，增加配置vm.max_map_count=262144

```
vi /etc/sysctl.conf
sysctl -p
```

　　执行命令sysctl -p生效

<img src="/assets/img/es/image-20201119104828935.png" alt="image-20201119104828935" style="zoom: 80%;" />

 4、Exception in thread "main" java.nio.file.AccessDeniedException: /usr/local/elasticsearch/elasticsearch-6.2.2-1/config/jvm.options

　　elasticsearch用户没有该文件夹的权限，执行命令

```
chown -R es:es /usr/local/elasticsearch/
```



文档出处

[链接]: https://www.jianshu.com/p/1d2ddb92f6fb
[链接]: https://www.cnblogs.com/zhi-leaf/p/8484337.html

