---
layout: post
title: 'elasticsearch在PostMan相关命令'
subtitle: 'elasticsearch在PostMan相关命令'
date: 2020-11-23
categories: Elasticsearch
tags: Elasticsearch elasticsearch
---

1. 创建index

   ```
   PUT   192.168.1.33:9200/wechat_message?pretty
   成功返回：{"acknowledged":true}
   ```

2. 查看index

   ```
   GET   192.168.1.33:9200/wechat_message/_settings
   ```

3. 修改index中max_result_window默认阈值

   ```
   PUT   192.168.1.33:9200/wechat_message/_settings
   body-raw-json    {"index":{"max_result_window":100000000}}
   ```



