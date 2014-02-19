---
layout: post
title: telnet手工发送邮件
category: 工具 
tags: telnet sendmail
---

```
telnet smtp.qq.com 25
开始输入
helo xiaoyu
auth login
eGlhb3l1（base64加密后的用户名xiaoyu）
eGlhb3l1（base64加密后的密码xiaoyu）
mail from:<76126128@qq.com>
rcpt to:<8454051@qq.com>
data
From:76126128@qq.com
To:8454051@qq.com
Subject:test
 
test
```
