---
layout: post
title: Jekyll搭建github个人主页
category: 工具 
tags: Jekyll github-page
---

### jekyll - Install
```http://jekyllrb.com/```
### 用github的page搭建个人主页(作为入门很详细的哦～)
```http://pages.github.com/```

<!--more-->

#### 目录结构：
- `_includes` 可以在模板中随时包含的文件
- `_layouts` 布局文件，在博客头配置中可以选择
- `pages` 站内固定的页面
- `public` 公共资源，包括`js`,`css`,`img`等，还有我博客中调用的图片，我都放这里
- `index.html` 首页，入口文件
- `sitemap.txt` 给搜索引擎看的，如何爬取这个站


#### 上面只是熟悉一下基础，想要更快速搭建，到网上找几个喜欢的模板，抄下来改改就好
```
git clone https://github.com/suyan/suyan.github.io
git clone https://github.com/jolestar/jolestar.github.com
```
#### jekyll - 启动(也可以在_config.yml设置)：
```
jekyll serve  --watch --port 4000   # --watch修改后无需重启立即生效 --port 指定端口
```
[更多>>](http://jekyllrb.com/docs/configuration/)


#### 评论模块(使用第三方的disqus)
先得有个disqus帐号，然后[Add Disqus to your site](http://disqus.com/admin/create/),由于我使用的是github-page的博客，所以'Choose your platform'选择'Universal Code'将给你的代码帖到你的代码里,如：
```
    <div id="disqus_thread"></div>
    <script type="text/javascript">
        /* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
        var disqus_shortname = 'yourname'; // required: replace example with your forum shortname

        /* * * DONT EDIT BELOW THIS LINE * * */
        (function() {
            var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
            dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
            (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
        })();
    </script>
    <noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
    <a href="http://disqus.com" class="dsq-brlink">comments powered by <span class="logo-disqus">Disqus</span></a>
```

-----------------------
#### markdown语法

# 标题
## 标题
### 标题
#### 标题
##### 标题
###### 标题
正文


*强调*
**加粗**
    
    代码段落
    代码


    代码


`代码`
``代码``
```代码```

`
代码
`
```
#
.
:
+
|
```

* 列表
    * 列表
    * 列表

1. 列表
    - 列表
1. 列表    



> 引用
>> 嵌套


[链接](http://zh.wikipedia.com/wiki/Markdown)


回车
回车

回车


回车

插入图片:图片放在`/public/upload`下，插入图片时`![xxx](/public/upload/xxx)`就可以了。
