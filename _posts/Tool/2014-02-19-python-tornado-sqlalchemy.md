---
layout: post
title: setuptools + Tornado + Sqlalchemy环境搭建
category: 环境 
tags: setuptools tornado sqlalchemy
---

#### Tornado
* python web framework,常见还有 Django、flask、bottle

* 文档：[http://www.tornadoweb.org/en/stable/documentation.html](http://www.tornadoweb.org/en/stable/documentation.html)

#### 环境搭建
##### virtualenv 项目建立独立的虚拟环境
```
sudo apt-get install python-virtualenv
virtualenv venv --distribute
source venv/bin/activate #切换 env
venv/bin/python
```
##### 包管理工具: easy_install（老）, pip （新）
```
pip install xxx
```
##### 项目依赖包管理: setuptools(老) ，distribute（新），(其实还是调用easy_install)
* setup.py其实是python工具包distutils的配置文件，setuptools就是基于distutils来做的。 在setup.py中通过setup函数来配置打包信息。首先要引入setuptools的函数setup。setuptools的setup其实就是distutils的setup函数
```
from setuptools import find_packages
from setuptools import setup
setup(name='AEA',
      version='0.1.dev',
      package_dir={'': 'src'},
      packages=find_packages('src'),
      include_package_data=True,
      install_requires=['tornado==3.0.1',
                        'SQLAlchemy==0.8.0',
                      'pyExcelerator==0.6.4.1',
                      'MySQL-python==1.2.4',
                      'argparse==1.2.1',
                      'gunicorn==0.17.4'],
      entry_points={
        "console_scripts": [
            "aea-run = aea.run:main"
        ]   
    }
)
```
##### 运行setup.py
* python setup.py develop/install , 使用develop则AEA.egg没有真正安装，只是在venv/lib/python2.7/site-packages(/usr/local/lib/python2.6/dist-packages/)目录下，写入path文件AEA.egg-link，指向项目源码; install会将项目打包egg，装在目录里。 扩展阅读：[制作自己的egg包](http://www.worldhello.net/2010/12/08/2178.html)
```
/var/www/html/venv/bin/python setup.py develop
```
###### 查看已安装的模块
```
$pip freeze
        AEA==0.1.dev
        MySQL-python==1.2.4
        SQLAlchemy==0.8.0
        argparse==1.2.1
        chardet==2.1.1
        distribute==0.6.46
        gunicorn==0.17.4
        pyExcelerator==0.6.4.1
        tornado==3.0.1
        wsgiref==0.1.2
```
最终被安装到 venv/lib/python2.7/site-packages
```
AEA.egg-link
chardet
chardet-2.1.1-py2.7.egg-info
distribute-0.6.24-py2.7.egg
distribute-0.6.46-py2.7.egg
easy-install.pth
gunicorn-0.17.4-py2.7.egg
meld3
meld3-0.6.10-py2.7.egg-info
MySQL_python-1.2.4-py2.7-linux-x86_64.egg
pip-1.1-py2.7.egg
pyExcelerator
pyExcelerator-0.6.4.1-py2.7.egg-info
setuptools-0.6c11-py2.7.egg-info
setuptools.pth
sqlalchemy
SQLAlchemy-0.8.0-py2.7.egg-info
supervisor
supervisor-3.0-py2.7.egg-info
supervisor-3.0-py2.7-nspkg.pth
tornado
tornado-3.0.1-py2.7.egg-info
```
##### 报错：MySQL server has gone away
* 原因：mysql的连接默认8小时无访问，会自动断开
* 环境：
* 处理：根据sqlalchemy添加自动``engine = create_engine('mysql://'+ self.settings['mysql_user'] +':'+ self.settings['mysql_password'] +'@'+ self.settings['mysql_host'] +'/'+ self.settings['mysql_database'] +'?charset=utf8', pool_recycle=7200)`` 无效。据说是使用了tornado的缘故。
* 解决：使用PeriodicCallback每隔2小时执行一次_ping_db()
```
        engine = create_engine('mysql:*******?charset=utf8', pool_recycle=7200)
        self.db = scoped_session(sessionmaker(bind=engine))

        from tornado.ioloop import PeriodicCallback
        PeriodicCallback(self._ping_db, 7200 * 1000).start()
    def _ping_db(self):
        self.session.execute('show variables')``` 

##### tornado.template传参
* 常见方法：```self.render("list.html",user_name=user_name, user_info=self.user_info);  {{user_name}}```
* 直接调用handler中的变量: ```{{handler.user_name}}```
* 检查某参数是否传递过来，try..catch... 



### sqlalchemy
* 相当于PDO 
* 文档:[http://docs.sqlalchemy.org/en/rel_0_9/orm/tutorial.html](http://docs.sqlalchemy.org/en/rel_0_9/orm/tutorial.html)

### 使用
##### 连接
```
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, scoped_session
engine = create_engine('mysql://root:123456@localhost/aea?charset=utf8', echo=True)   #create_engine() 会返回一个数据库引擎,echo=True 时,会打印 SQL 语句,生产环境下请关闭。
```
数据库连接
```
db = scoped_session(sessionmaker(bind=engine))
```
增删改查
```
增
reimsub = Reimsub(id=12, type_id=44, expense=55.5)
db.add(reimsub)
db.flush()
删
db.query(Reimsub).filter_by(type_id=33).delete()
改
for c in session.query(Reimsub).all():
c.foo = c.foo+1
db.flush()
或
db.query(Reimsub).update({Reimsub.foo:Reimsub.foo + 1})
db.flush()
查
reimsub = db.query(Reimsub).filter_by(type_id=12).first()
```
flush VS commit
* commit() 提交到数据库,并写到了磁盘上
* flush() 提交到数据库,并且数据对其他访问可见,但是没有写到磁盘上
* [ MySql 的日志先行策略 http://www.woqutech.com/?p=769 ](http://www.woqutech.com/?p=769)


#### gunicorn
```
用bottle框架 
 venv/bin/python -m bottle --bind 0.0.0.0:9075 --debug --reload sakuya:app
  \_ /var/www/sakuya/venv/bin/python /var/www/sakuya/venv/lib/python2.7/site-packages/bottle-0.11.3-py2.7.egg/bottle.py --bind 0.0.0.0:9075 --debug --reload sakuya:app
 用tornado框架
 var/www/html/AEA/venv/bin/python /var/www/html/AEA/venv/bin/gunicorn -k tornado -w 2 -b0.0.0.0:7777 aea.app:myapp
  \_ /var/www/html/AEA/venv/bin/python /var/www/html/AEA/venv/bin/gunicorn -k tornado -w 2 -b0.0.0.0:7777 aea.app:myapp
  \_ /var/www/html/AEA/venv/bin/python /var/www/html/AEA/venv/bin/gunicorn -k tornado -w 2 -b0.0.0.0:7777 aea.app:myapp
```
* -m #Searches sys.path for the named module and runs the corresponding .py file as a script.
```
【bottle】框架
--bind 0.0.0.0:9075
--debug #更详细的errorlog
--reload #检测到文件变化就autorestart，其实是子进程16009被3513重启；
package.module:app
```
```
【gunicorn】[1] 一种WSGI
-k gevent #The type of workers to use.网络模型([sync]同步阻塞/gevent/eventlet/tornado[不推荐,具体??]) http://docs.gunicorn.org/en/latest/design.html
-w 5 #The number of worker process for handling requests.
-b 0.0.0.0:9075
--max-requests 2048 #The maximum number of requests a worker will process before restarting.防止memory leak
sakuya:app
【WSGI】Python Web Server Gateway Interface (监听端口，管理进程，负载均衡) 【uWSGI】据说linux自带，且性能比gunicorn好呢？
```
#### supervisor


