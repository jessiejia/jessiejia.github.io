---
layout: post
title: HTTP Header
category: 网络
tags: http
---



响应头信息

    Connection  keep-alive
    Content-Encoding    gzip
    Content-Type    text/html; charset=utf-8
    Date    Wed, 30 Jul 2014 08:00:41 GMT
    Server  nginx
    Set-Cookie  
    Transfer-Encoding   chunked
    Vary    Accept-Encoding

请求头信息

    Accept  text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
    Accept-Encoding gzip, deflate
    Accept-Language zh-cn,zh;q=0.8,en-us;q=0.5,en;q=0.3
    Connection  keep-alive
    Cookie 
    Host    xxx.com
    User-Agent  Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:31.0) Gecko/20100101 Firefox/31.0

#### 字段解释：

##### keep-alive: 

原本每次访问都要3次握手，而一个网页都会有各种js/css/img，如果没一个都要3次握手的话比较浪费。于是，改用长连接，避免建立和释放连接的开销。

（http 1.0中默认是关闭的，需要在http头加入"Connection: Keep-Alive"，才能启用Keep-Alive；http 1.1中默认启用Keep-Alive，如果加入"Connection: close "才关闭。）

* 如果打开keep-alive,

服务器端会保持这个连接直到keepalive_timeout，必然会有一个Apache 进程对应于该用户而不能处理其他用户，假设 KeepAlive 的超时时间为 10 秒种，服务器每秒处理 50个独立用户访问，那么系统中 Apache 的总进程数就是 10 * 50 ＝ 500 个，如果一个进程占用 4M 内存，那么总共会消耗 2G内存，所以可以看出，在这种配置中，相当消耗内存，但好处是系统只处理了 50次 TCP 的握手和关闭操作。

* 如果关闭keep-alive,

如果还是每秒50个用户访问，如果用户每次连续的请求数为3个，那么 Apache 的总进程数就是 50 * 3= 150 个，如果还是每个进程占用 4M 内存，那么总的内存消耗为 600M，这种配置能节省大量内存，但是，系统处理了 150 次 TCP的握手和关闭的操作，因此又会多消耗一些 CPU 资源。

所以，如果服务器内存较少，或者服务器有非常大量的文件系统访问时，或者主要处理动态网页服务很少有JS/CSS/IMG，关闭 KeepAlive可以节约内存。

##### Transfer-Encoding:chunked

一般用Content-length来确认文件的传输结束，但是如果页面是动态的，服务器也不知道文件大小时，使用chunked。

将数据分成一块一块的，Chunked编码将使用若干个Chunk串连而成，由一个标明长度为0 的chunk标示结束。每个Chunk分为头部和正文两部分，头部内容指定正文的字符总数（十六进制的数字 ）和数量单位（一般不写），正文部分就是指定长度的实际内容，两部分之间用回车换行(CRLF) 隔开。在最后一个长度为0的Chunk中的内容是称为footer的内容，是一些附加的Header信息（通常可以直接忽略）。


响应头信息

    Age 1
    Cache-Control   max-age=315360000
    Connection  keep-alive
    Content-Length  3186
    Content-Type    image/jpeg
    Date    Tue, 22 Jul 2014 11:05:35 GMT
    Etag    "80aca507"
    Expires Fri, 19 Jul 2024 11:05:35 GMT
    Last-Modified   Tue, 22 Jul 2014 10:33:35 GMT
    Server  nginx
    Via 1.1 cdn20-005:3128 (squid/2.7.STABLE9)
    X-Via   1.1 suqian46:8107 (Cdn Cache Server V2.0), 1.1 yy58:6 (Cdn Cache Server V2.0)

请求头信息

    Accept  image/png,image/*;q=0.8,*/*;q=0.5
    Accept-Encoding gzip, deflate
    Accept-Language zh-cn,zh;q=0.8,en-us;q=0.5,en;q=0.3
    Connection  keep-alive
    Host    xxx.com
    Referer http://xxx.com/?_r=589740518
    User-Agent  Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:31.0) Gecko/20100101 Firefox/31.0

##### Cache-Control

