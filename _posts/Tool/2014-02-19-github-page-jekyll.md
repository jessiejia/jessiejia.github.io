---
layout: post
title: Jekyll搭建github个人主页
category: 工具 
tags: Jekyll github-page
---

### 创建一个gitPage 
在github创建一个仓库，命名为jessiejia.github.io 

```https://pages.github.com/```

### 本地搭环境
写markdown很简单，但是如何在本地可以预览呢？需要搭建一个类似于github的环境

```https://help.github.com/articles/using-jekyll-with-pages```

需要注意各软件的版本哦<https://pages.github.com/versions/>,不过现在有了[the GitHub Pages Gem](https://github.com/github/pages-gem)一切变得超简单，谢天谢地

创建`vim Gemfile`
    source 'https://rubygems.org'
    gem 'github-pages'

执行`bundle install`

### 运行

在项目根目录执行`bundle exec jekyll serve`就可以跑起来了，`http://localhost:4000`，不过修改内容以后，需要重新启动才能看到新的效果。

### 搭建blog

Jekyll的作用就是将markdown之类的文件转化成html文件。

为了快速了解，可以先去copy一个比较喜欢的bolg的仓库，先跑起来。
    
    git clone https://github.com/suyan/suyan.github.io
    git clone https://github.com/jolestar/jolestar.github.com

#### Jekyll目录结构：
- `_includes` 可以在模板中随时包含的文件
- `_layouts` 布局文件，在博客头配置中可以选择
- `pages` 站内固定的页面
- `public` 公共资源，包括`js`,`css`,`img`等，还有我博客中调用的图片，我都放这里
- `index.html` 首页，入口文件
- `sitemap.txt` 给搜索引擎看的，如何爬取这个站
- `_post` 你的一篇篇文章
- `_site` jekyll生成的html文件

#### 评论模块(使用第三方的disqus)
先得有个disqus帐号，然后[Add Disqus to your site](http://disqus.com/admin/create/),由于我使用的是github-page的博客，所以'Choose your platform'选择'Universal Code'将给你的代码帖到你的代码里,如：

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

#### 遇到的问题
* Configuration file: none 并且生成的_site文件被放到_posts下
** 结果：因为在_posts文件夹下启动jekyll，导致找不到_config.yml


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

转义：Markdown支持以下特殊字符的转义操作：
```
\   反斜线
`   反引号
*   星号
_   底线
{}  花括号
[]  方括号
()  括弧
<>  尖括号
#   井字号
+   加号
-   减号
.   英文句点
!   惊叹号
```

