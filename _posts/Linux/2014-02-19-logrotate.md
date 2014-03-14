---
layout: post
title: Apache&Nginx日志切割
category: 运维 
tags: logrotate log
---

#### Apache有自带的/usr/sbin/rotatelogs，
直接配置CustomLog "|/usr/sbin/rotatelogs /data1/pmt_log/access-%Y-%m-%d.log 86400 480"，以管道的方式，无需kill进程

#### Nginx使用系统自带的/usr/sbin/logrotate，
需要配置/etc/logrotate.d/nginx文件，需要kill进程，将log文件重命名
（其实是/etc/cron.daily/logrotate会调用 /usr/sbin/logrotate配置文件为/etc/logrotate.d/* ）


