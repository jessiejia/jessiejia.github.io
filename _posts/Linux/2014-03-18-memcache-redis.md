---
layout: post
title: memcache & redis
category: 运维 
tags: memcache redis
---

#### memcache
##### memcache vs memcached
memcache -- 程序
memcached -- memcache的主进程/守护进程
php-memcache模块 -- 在PHP框架内原生的，较早期
php-memcached模块 -- 基于libmemcached,比memcache更全

##### 安装

* 运行
    memcache   995  0.0  0.0 325268  2272 ?        Sl   Feb13   1:08 /usr/bin/memcached -m 64 -p 11211 -u memcache -l 127.0.0.1
##### 操作
    telnet localhost 11211 #连接
    Trying 127.0.0.1...
    Connected to localhost.
    Escape character is '^]'.
    get key1              #查询
    VALUE key1 0 21       #(VALUE keyname datatype strlen)
    This is replace value #DATA
    END                   #END  
    get key2
    VALUE key2 1 62
    a:4:{i:0;s:3:"aaa";i:1;s:3:"bbb";i:2;s:3:"ccc";i:3;s:3:"ddd";} #DATA 数组类型
    END

#### redis vs memcache
都是内存型数据库，通过tcp存取
redis具有持久化机制，
#### redis
Data可以是：字符串(String), 哈希(Map), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。
