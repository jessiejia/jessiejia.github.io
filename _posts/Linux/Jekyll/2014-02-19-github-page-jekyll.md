---
layout: post
title: Jekyll搭建github个人主页
category: Linux 
tags: Jekyll github-page
---

### jekyll - Install
```http://jekyllrb.com/```
### 用github的page搭建个人主页(很详细的哦～)
```http://pages.github.com/```


#### 目录结构：
- `_includes` 可以在模板中随时包含的文件
- `_layouts` 布局文件，在博客头配置中可以选择
- `pages` 站内固定的页面
- `public` 公共资源，包括`js`,`css`,`img`等，还有我博客中调用的图片，我都放这里
- `index.html` 首页，入口文件
- `sitemap.txt` 给搜索引擎看的，如何爬取这个站


#### 网上找了几个喜欢的模板，抄下来改改
```
git clone https://github.com/suyan/suyan.github.io
git clone https://github.com/jolestar/jolestar.github.com
```
#### jekyll - 启动：
```
jekyll serve  --watch --port 4000   # --watch修改后无需重启立即生效 --port 指定端口
```

#### 语法
##### 插入图片
图片放在`/public/upload`下，插入图片时`![xxx](/public/upload/xxx)`就可以了。
