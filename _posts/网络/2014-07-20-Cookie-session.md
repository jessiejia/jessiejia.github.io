---
layout: post
title: Cookie & Session
category: 网络
tags: session cookie
---



经常遇到面试的第一题就是“COOKIE和SESSION的关系“

## SESSION

#### 客户端如何记录session的？

![图片](/public/img/session.png)

默认，借用一个名为PHPSESSID的cookie，这就是为什么session依赖cookie。

    $_COOKIE：array(1) { ["PHPSESSID"]=> string(32) "dcf46d4f5e190e07751f3a80745bafe9" }  

#### 服务器上session存在哪里呢？

php.ini

    session.save_hander=files
    session.save_path=/tmp  
    #我的php.ini未设save_path，打印var_dump(session_save_path())得到string“”，网上说可能在临时文件里var_dump(sys_get_temp_dir())得到/var/tmp/找到。

在/var/tmp找到文件

    cat sess_dcf46d4f5e190e07751f3a80745bafe9 #文件名是sess_+SESSION_ID 
    views|i:4;

#### session过期

    session.gc_maxlifetime = 60  #当session文件在60s后还没有被访问的话，则该session文件将会被视为“垃圾文件”，并且等待gc（垃圾回收）进程的调用的时候被清理掉.

*那么GC什么时候被调用呢？*

它没有守护进程，而是调用session_start()的时候有一定概率去扫描session信息是否过期并调用GC，
默认情况下，session.gc_probability ＝ 1，session.gc_divisor ＝1000，也就是说有0.1%的可能性会启动GC(Garbage Collector垃圾回收)

于是，有时会出现用户长时间没操作，session过期，但是服务器GC还没来得及删除session文件，用户依然可以访问的情况。

此外，即使删除了session文件，浏览器端有PHPSESSIONID的话，执行session_start()，还是会新生成的session文件名依旧不变。


#### 禁用COOKIE

禁用以后COOKIE和SESSION都不能用了，这时需要在所有访问后面带上自定义的参数如jsessionid=fsd6789df8ssdf89,php端对此做特殊处理；


#### SESSION伪造攻击
两台电脑只要有了相同的session_id，就代表同一个用户。这样不是很危险？


解决方案：

任何身份权限变化后（登陆）调用session_regenerate_id() #新建一个session，并将现有数据复制到新session.

#### session的缺点
* 集群不方便
* 存在大量过期session浪费资源

据说用加密的cookie代替session，谁知道它的安全性呢...


关于安全和性能，这里以后再搞吧。



## COOKIE

#### 浏览器的COOKIE记录在哪里？

    firefox: /Users/jessie/Library/Application\ Support/Firefox/Profiles/mlyc06uq.default/cookies.sqlite



#### 浏览器显示“会话”不一定是session*

![](/public/img/cookie没设置过期时间.png)
当setcookie ( string $name [, string $value [, int $expire = 0 [, string $path [, string $domain [, bool $secure = false [, bool $httponly = false ]]]]]] )没设置过期时间,默认浏览器关闭才结束。

>  If set to 0, or omitted, the cookie will expire at the end of the session (when the browser closes).


 
