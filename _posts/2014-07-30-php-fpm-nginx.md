---
layout: post
title: php-fpm-nginx搭建 
category: PHP
tags: php fpm nginx
---

#### nginx+php+fpm的安装配置

<http://my.oschina.net/chen0dgax/blog/190161>


####  开机启动

##### MAC的开机自启动

切换到root,在/Library/LaunchDaemons目录创建两个文件:

org.jessie.nginx.plist 

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
    <key>KeepAlive</key>
    <true/>
    <key>Label</key>
    <string>org.jessie.nginx</string>
    <key>Program</key>
    <string>/usr/local/bin/nginx</string>
    <key>RunAtLoad</key>
    <true/>
    <key>WorkingDirectory</key>
    <string>/usr/local/var</string>
    </dict>
    </plist>

org.jessie.php-fpm.plist 

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
      <key>KeepAlive</key>
      <true/>
      <key>Label</key>
      <string>org.jessie.php-fpm</string>
      <key>Program</key>
      <string>/usr/sbin/php-fpm</string>
      <key>RunAtLoad</key>
      <true/>
      <key>WorkingDirectory</key>
      <string>/usr/local/var</string>
    </dict>
    </plist>

注意：Label是作为唯一标示的名字，Program是脚本的路径可以通过`which nginx`查看，

增加开机启动任务，执行以下命令：

    launchctl load -w /Library/LaunchDaemons/org.jessie.php-fpm.plist
    launchctl load -w /Library/LaunchDaemons/org.jessie.nginx.plist

