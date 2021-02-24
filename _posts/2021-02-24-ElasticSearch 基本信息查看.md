---
layout: post
title: 'ElasticSearch 基本信息查看'
subtitle: 'ElasticSearch 基本信息查看'
date: 2021-02-24
categories: Elasticsearch
tags: Elasticsearch
---

ElasticSearch 基本信息查看

转载：[大师兄你家猴跑啦](https://blog.csdn.net/jeffiny) ：https://blog.csdn.net/jeffiny

1.查看集群的健康状态。

```
http://127.0.0.1:9200/_cat/health?v
```

URL中_cat表示查看信息，health表明返回的信息为集群健康信息，?v表示返回的信息加上头信息，跟返回JSON信息加上?pretty同理，就是为了获得更直观的信息，当然，你也可以不加，不要头信息，特别是通过代码获取返回信息进行解释，头信息有时候不需要，写shell脚本也一样，经常要去除一些多余的信息。
通过这个链接会返回下面的信息，下面的信息包括：

集群的状态（status）：red红表示集群不可用，有故障。yellow黄表示集群不可靠但可用，一般单节点时就是此状态。green正常状态，表示集群一切正常。

节点数（node.total）：节点数，这里是2，表示该集群有两个节点。

数据节点数（node.data）：存储数据的节点数，这里是2。数据节点在Elasticsearch概念介绍有。

分片数（shards）：这是12，表示我们把数据分成多少块存储。

主分片数（pri）：primary shards，这里是6，实际上是分片数的两倍，因为有一个副本，如果有两个副本，这里的数量应该是分片数的三倍，这个会跟后面的索引分片数对应起来，这里只是个总数。

激活的分片百分比（active_shards_percent）：这里可以理解为加载的数据分片数，只有加载所有的分片数，集群才算正常启动，在启动的过程中，如果我们不断刷新这个页面，我们会发现这个百分比会不断加大。

```
epoch timestamp cluster status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent

1506327257 16:14:17 ruan_ES green 2 2 12 6 0 0 0 0 - 100.0%
```

2.查看集群的索引数。

```
curl dmp9:9200/_cat/indices?v
```


通过该连接返回了集群中的所有索引，其中.kibana是kibana连接后在es建的索引，school是我自己添加的。
这些信息，包括

索引健康（health），green为正常，yellow表示索引不可靠（单节点），red索引不可用。与集群健康状态一致。

状态（status），表明索引是否打开。

索引名称（index），这里有.kibana和school。

uuid，索引内部随机分配的名称，表示唯一标识这个索引。

主分片（pri），这个就是集群的主分片数。

文档数（docs.count）。

已删除文档数（docs.deleted），这里统计了被删除文档的数量。

索引存储的总容量（store.size），索引的总容量，是主分片总容量的两倍，因为存在一个副本。

主分片的总容量（pri.store.size），主分片容量，是索引总容量的一半。

```
[root@dmp8 ~]# curl dmp9:9200/_cat/indices?v
health status index pri rep docs.count docs.deleted store.size pri.store.size

green open dmp-toutiao-allfields-20170410 5 1 4470231 0 21.8gb 10.9gb
green open dmp-toutiao-allfields-20170411 5 1 5483217 0 26.8gb 13.4gb
green open dmp-toutiao-allfields-20170412 5 1 1291251 0 6.5gb 3.2gb
green open dmp-toutiao-allfields-20170413 5 1 3692841 0 17.9gb 8.9gb
green open dmp-toutiao-allfields-20170414 5 1 5683632 0 26.9gb 13.4gb
green open dmp-toutiao-allfields-20170415 5 1 5558394 99 26.4gb 13.2gb
green open dmp-toutiao-allfields-20170416 5 1 5249961 0 24.9gb 12.4gb
green open dmp-toutiao-allfields-20170417 5 1 4502766 0 21.4gb 10.7gb
yellow open dmp-toutiao-allfields2 15 3 1862685 274944 17.1gb 5.7gb
green open dmp-toutiao-allfields-20170418 5 1 0 0 1.5kb 795b
green open dmp-toutiao-allfields-20170419 5 1 0 0 1.5kb 795b
yellow open dmp-toutiao-allfields 15 3 27132 0 232.3mb 77.4mb
green open .kibana 1 1 41 3 160.3kb 80.1kb
yellow open dmp-toutiao-allfields5 15 3 38828040 195705 292.1gb 97.3gb
```


3.查看集群所在磁盘的分配状况

```
http://127.0.0.1:9200/_cat/allocation?v
```


通过该连接返回了集群中的各节点所在磁盘的磁盘状况
返回的信息包括：

分片数（shards），集群中各节点的分片数相同。

索引所占空间（disk.indices），该节点中所有索引在该磁盘所点的空间。

磁盘使用容量（disk.used）

磁盘可用容量（disk.avail）

磁盘总容量（disk.total）

磁盘便用率（disk.percent）

```
[root@dmp8 ~]# curl dmp8:9200/_cat/allocation?v
shards disk.indices disk.used disk.avail disk.total disk.percent host ip node

188 225.2gb 3.5tb 35.8tb 39.3tb 9 192.168.91.4 192.168.91.4 es-dmp4
190 221.8gb 2.7tb 36.6tb 39.3tb 6 192.168.91.5 192.168.91.5 es-dmp5
188 215.5gb 2.8tb 36.5tb 39.3tb 7 192.168.91.7 192.168.91.7 es-dmp7
90 UNASSIGNED
[root@dmp8 ~]#
```


4.查看集群的节点

```
http://127.0.0.1:9200/_cat/nodes?v
```


通过该连接返回了集群中各节点的情况。这些信息中比较重要的是master列，带*星号表明该节点是主节点。带-表明该节点是从节点。
另外还是heap.percent堆内存使用情况，ram.percent运行内存使用情况，cpu使用情况。

```
[root@dmp8 ~]# curl dmp9:9200/_cat/nodes?v
host ip heap.percent ram.percent load node.role master name

192.168.91.5 192.168.91.5 13 33 0.08 d - es-dmp5
192.168.91.7 192.168.91.7 10 21 0.00 d - es-dmp7
192.168.91.8 192.168.91.8 2 18 0.00 - m es-dmp8
192.168.91.4 192.168.91.4 10 65 0.03 d - es-dmp4
192.168.91.9 192.168.91.9 1 97 0.10 - * es-dmp9
[root@dmp8 ~]#
```


5.查看集群的其它信息。

```
http://127.0.0.1:9200/_cat/
```


通过上面的链接，其实，我们就相当于获得查看集群信息的目录。

```
[root@dmp8 ~]# curl dmp9:9200/_cat/
 
=^.^=
 
/_cat/allocation
 
/_cat/shards
 
/_cat/shards/{index}
/_cat/master
 
/_cat/nodes
 
/_cat/indices
 
/_cat/indices/{index}
/_cat/segments
 
/_cat/segments/{index}
/_cat/count
 
/_cat/count/{index}
/_cat/recovery
 
/_cat/recovery/{index}
/_cat/health
 
/_cat/pending_tasks
 
/_cat/aliases
 
/_cat/aliases/{alias}
/_cat/thread_pool
 
/_cat/plugins
 
/_cat/fielddata
 
/_cat/fielddata/{fields}
/_cat/nodeattrs
 
/_cat/repositories
 
/_cat/snapshots/{repository}
[root@dmp8 ~]#
```

