---
layout: post
title: Mysql常见问题
category: 语言
tags: mysql
---

#### 权限
```
mysql> show grants for solrcloud_log_db;
+----------------------------------------------------------------------------------------------------------------+
| Grants for solrcloud_log_db@%                                                                                  |
+----------------------------------------------------------------------------------------------------------------+
| GRANT FILE ON *.* TO 'solrcloud_log_db'@'%' IDENTIFIED BY PASSWORD '*1A08DCA365BB79E06C75554C263B027EFFBC0B6E' | 
| GRANT ALL PRIVILEGES ON `solrcloud_log_db`.* TO 'solrcloud_log_db'@'%'                                         | 
+----------------------------------------------------------------------------------------------------------------+
``` 

#### 字符集
##### 默认安装后为latin1,要手工设置为utf8 查看当前客户端的字符集mysql --help;
```
修改方式修改my.cnf或my.ini,然后重启mysql
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
default-character-set = utf8    
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8

或者～/.bash_profile < alias mysql='mysql --default-character-set=utf8 '
```
##### 查看某DB的字符集
```
mysql> show variables like '%char%';
+--------------------------+------------------------------------+
| Variable_name            | Value                              |
+--------------------------+------------------------------------+
| character_set_client     | utf8                               | 
| character_set_connection | utf8                               | 
| character_set_database   | utf8                               | 
| character_set_filesystem | binary                             | 
| character_set_results    | utf8                               | 
| character_set_server     | utf8                               | 
| character_set_system     | utf8                               | 
| character_sets_dir       | /data1/mysql/share/mysql/charsets/ | 
+--------------------------+------------------------------------+
mysql> show variables like 'collation%';
+----------------------+-----------------+
| Variable_name        | Value           |
+----------------------+-----------------+
| collation_connection | utf8_general_ci | 
| collation_database   | utf8_general_ci | 
| collation_server     | utf8_general_ci | 
+----------------------+-----------------+
```
##### 指定字符集：
* mysql --default-character-set=utf8 -h** -u** -p** **
* mysqldump --default-character-set=utf8 --lock-tables=0 --no-create-info -h** -u** -p** -B ** > sql.sql






#### sql语法

##### mysqldump
```
mysqldump -h xxxx -uxxxx -pxxxx database tablename -t --single-transaction --where 'log_time>"2012-12-01"' > cloud_log_db_dump.sql   # -d 不要data ,-t 不要struct
```
##### 删除所有的表
```
mysqldump -d -uuser -ppass database |grep DROP mysql -uuser -ppass -Ns information_schema -e "select concat('drop table ', table_name, ';') from tables where table_schema='database';" 
```
##### 按照in的顺序排序
SELECT * FROM `approve` where id in (22,99,44,11) order by substring_index('22,99,44,11',id,1)

##### ALTER TABLE

* 修改表名：alter table t_book rename to bbb;
* 添加列：alter table 表名 add column 列名 varchar(30);
* 删除列：alter table 表名 drop column 列名;
* 修改列名MySQL： alter table bbb change nnnnn hh int;
* 修改列名SQLServer：exec sp_rename't_student.name','nn','column';
* 修改列名Oracle：lter table bbb rename column nnnnn to hh int;
* 修改列属性：alter table t_book modify name varchar(22);

##### Key

* 查看表的字段信息：desc 表名;
* 查看表的所有信息：show create table 表名;
* 添加主键约束：alter table 表名 add constraint 主键 （形如：PK_表名） primary key 表名(主键字段);
* 添加外键约束：alter table 从表 add constraint 外键（形如：FK_从表_主表） foreign key 从表(外键字段) references 主表(主键字段);
* 删除主键约束：alter table 表名 drop primary key;
* 删除外键约束：alter table 表名 drop foreign key 外键（区分大小写）;
* 修改表名：alter table t_book rename to bbb;
* 添加列：alter table 表名 add column 列名 varchar(30);
* 删除列：alter table 表名 drop column 列名;
* 修改列名MySQL： alter table bbb change nnnnn hh int;
* 修改列名SQLServer：exec sp_rename't_student.name','nn','column';
* 修改列名Oracle：alter table bbb rename column nnnnn to hh int;
* 修改列属性：alter table t_book modify name varchar(22);

##### 内置函数
```
select TIME_TO_SEC('22:23:00');  -> 80580 #时分秒转秒数 
```
