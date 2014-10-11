---
layout: post
title: python-virtualenv 
category: Python 
tags: python
---


virtualenv是一个创建独立python环境的工具。

#### 安装
    $ sudo easy_install  virtualenv
    Searching for virtualenv
    Reading http://pypi.python.org/simple/virtualenv/
    Best match: virtualenv 1.11.6
    Downloading https://pypi.python.org/packages/source/v/virtualenv/virtualenv-1.11.6.tar.gz#md5=f61cdd983d2c4e6aeabb70b1060d6f49
    Processing virtualenv-1.11.6.tar.gz
    Running virtualenv-1.11.6/setup.py -q bdist_egg --dist-dir /tmp/easy_install-lVTdHj/virtualenv-1.11.6/egg-dist-tmp-LdIT1y
    warning: no previously-included files matching '*' found under directory 'docs/_templates'
    warning: no previously-included files matching '*' found under directory 'docs/_build'
    Adding virtualenv 1.11.6 to easy-install.pth file
    Installing virtualenv script to /usr/local/bin
    Installing virtualenv-2.7 script to /usr/local/bin

    Installed /Library/Python/2.7/site-packages/virtualenv-1.11.6-py2.7.egg
    Processing dependencies for virtualenv
    Finished processing dependencies for virtualenv


    $ virtualenv ENV
    New python executable in ENV/bin/python
    Installing setuptools, pip...done.
    $ ls -al ENV
    drwxr-xr-x   6 jessie  staff  204  8  1 13:13 .
    drwxr-xr-x   3 jessie  staff  102  8  1 13:13 ..
    lrwxr-xr-x   1 jessie  staff   63  8  1 13:13 .Python -> /System/Library/Frameworks/Python.framework/Versions/2.7/Python
    drwxr-xr-x  14 jessie  staff  476  8  1 13:13 bin
    drwxr-xr-x   3 jessie  staff  102  8  1 13:13 include
    drwxr-xr-x   3 jessie  staff  102  8  1 13:13 lib

会在当前目录下创建 ENV/lib/pythonX.X/site-packages (存放安装的模块)和 ENV/bin/python(隔离环境的Python解释器)
`virtualenv ENV --distribute`默认安装setuptools，添加参数distribute改成安装distribute替代setuptools




#### 使用

    $ source ENV/bin/activate # 进入虚拟环境
    (ENV)$ pip install Django
    Downloading/unpacking Django
      Downloading Django-1.6.5-py2.py3-none-any.whl (6.7MB): 6.7MB downloaded
    Installing collected packages: Django
    Successfully installed Django
    Cleaning up...
    (ENV)$ pip freeze  # 查看已经安装的
    Django==1.6.5
    wsgiref==0.1.2
    (ENV)$ pip -h

    Usage:   
      pip <command> [options]

    Commands:
      install                     Install packages.
      uninstall                   Uninstall packages.
      freeze                      Output installed packages in requirements format.
      list                        List installed packages.
      show                        Show information about installed packages.
      search                      Search PyPI for packages.
      wheel                       Build wheels from your requirements.
      zip                         DEPRECATED. Zip individual packages.
      unzip                       DEPRECATED. Unzip individual packages.
      bundle                      DEPRECATED. Create pybundles.
      help                        Show help for commands.

    General Options:
      -h, --help                  Show help.
      -v, --verbose               Give more output. Option is additive, and can be used up to 3 times.
      -V, --version               Show version and exit.
      -q, --quiet                 Give less output.
      --log-file <path>           Path to a verbose non-appending log, that only logs failures. This log is active by default at /Users/jessie/.pip/pip.log.
      --log <path>                Path to a verbose appending log. This log is inactive by default.
      --proxy <proxy>             Specify a proxy in the form [user:passwd@]proxy.server:port.
      --timeout <sec>             Set the socket timeout (default 15 seconds).
      --exists-action <action>    Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup.
      --cert <path>               Path to alternate CA bundle.
    (ENV)$ pip show Django # 查看某一个包详细信息
    ---
    Name: Django
    Version: 1.6.5
    Location: /Users/jessie/Dev/Python/ENV/lib/python2.7/site-packages
    Requires: 
    (ENV)$ echo $PATH  # 虚拟环境下$PATH会被修改
    /Users/jessie/Dev/Python/ENV/bin:/usr/local/bin:/opt/cocos2d-x-3.0/tools/cocos2d-console/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/Users/jessie/bin
    (ENV)$ deactivate # 退出虚拟环境
    $ echo $PATH  # 实际环境的$PATH
    /usr/local/bin:/opt/cocos2d-x-3.0/tools/cocos2d-console/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/Users/jessie/bin


你也可以直接通过路径调用

    $ ENV/bin/python
    Python 2.7.5 (default, Mar  9 2014, 22:15:05) 
    [GCC 4.2.1 Compatible Apple LLVM 5.0 (clang-500.0.68)] on darwin
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import django
    >>> django.VERSION
    (1, 6, 5, 'final', 0)


