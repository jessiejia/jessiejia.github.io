---
layout: post
title: Mysql常见问题
category: 语言
tags: mysql
---

### 打印所有库和表
    use information_schema;select table_schema,table_name from tables;


### 权限
```
mysql> show grants for solrcloud_log_db;
+----------------------------------------------------------------------------------------------------------------+
| Grants for solrcloud_log_db@%                                                                                  |
+----------------------------------------------------------------------------------------------------------------+
| GRANT FILE ON *.* TO 'solrcloud_log_db'@'%' IDENTIFIED BY PASSWORD '*1A08DCA365BB79E06C75554C263B027EFFBC0B6E' | 
| GRANT ALL PRIVILEGES ON `solrcloud_log_db`.* TO 'solrcloud_log_db'@'%'                                         | 
+----------------------------------------------------------------------------------------------------------------+
```

### 字符集
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
* mysql --default-character-set=utf8 -h** -u** -p** tablename
* mysqldump --default-character-set=utf8 --lock-tables=0 --no-create-info -h** -u** -p** -B ** > sql.sql



### SQL语法

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





SELECT city，group_concat(id) as ids FROM table group by city;   #按city返回id的集合

SELECT * FROM table WHERE name REGEXP '^.{5}$'; #正则

```

##### Alter table 
[ALTER TABLE运行时会对原表进行临时复制，在副本上进行更改，然后删除原表，再对新表进行重命名。在执行ALTER TABLE时，其它用户可以阅读原表，但是对表的更新和修改的操作将被延迟，直到新表生成为止。新表生成后，这些更新和修改信息会自动转移到新表上。RENAME除外](http://dev.mysql.com/doc/refman/5.1/zh/sql-syntax.html)








##### 索引
* 索引不会包含有NULL值的列
** 只要列中包含有NULL值都将不会被包含在索引中，复合索引中只要有一列含有NULL值，那么这一列对于此复合索引就是无效的。所以我们在数据库设计时不要让字段的默认值为NULL。

* 使用短索引
** 对串列进行索引，如果可能应该指定一个前缀长度。例如，如果有一个CHAR(255)的列，如果在前10个或20个字符内，多数值是惟一的，那么就不要对整个列进行索引。短索引不仅可以提高查询速度而且可以节省磁盘空间和I/O操作。

* 索引列排序
** MySQL查询只使用一个索引，因此如果where子句中已经使用了索引的话，那么order by中的列是不会使用索引的。因此数据库默认排序可以符合要求的情况下不要使用排序操作；尽量不要包含多个列的排序，如果需要最好给这些列创建复合索引。

* like语句操作
** 一般情况下不鼓励使用like操作，如果非使用不可，如何使用也是一个问题。like “%aaa%” 不会使用索引而like “aaa%”可以使用索引。
** where a='a' and b like 'b%' and c='c'由于b为like搜索，所以c不会用到索引

* 不使用NOT IN和<>操作

* 不要在列上进行运算(不要用运算函数)
* 索引列不能是表达式或函数
** 如：where a+1=5 and TO_DAYS(b)<'2013-01-01'不会用到索引a和b



##### 数据类型
* timesamp < datetime(8B)
* tinyint(8) < smallint(16) < mediumint(24) < int(32) < bigint(64)
** 范围：-2^(n-1)~2^(n-1)-1 如：tinyint -128~127 ; tinyint unsigned 0~255 
** 误区：int(1)与int(10)实际效果是一样的。
* varchar vs char
** varchar：最大长度比平均长度长很多的 && update很少的情况
** char：非常短的,varchar会有1B标志位
** char：经常update的，因为varchar会产生碎片
** varchar(5) vs varchar(100):越大越耗内存
* enum枚举 比char好（因为实际存储的是数字）

##### 解析步骤
    (8)SELECT (9)DISTINCT   
    (11)<TOP_specification> <select_list>   
    (1)FROM <left_table>   
    (3)<join_type> JOIN <right_table>   
    (2)　　　 ON <join_condition>   
    (4)WHERE <where_condition>   
    (5)GROUP BY <group_by_list>   
    (6)WITH {CUBE ROLLUP}   
    (7)HAVING <having_condition>   
    (10)ORDER BY <order_by_list>   

    1. FROM:对FROM子句中的前两个表执行笛卡尔积，生成虚拟表VT1。 
    2. ON:对VT1应用ON筛选器。只有那些使<join_condition>为真的行才被插入VT2。 
    3. OUTER(JOIN):如果指定了OUTER JOIN，保留表中未找到匹配的行将作为外部行添加到VT2，生成VT3。 
    如果FROM子句包含两个以上的表，则对上一个联接生成的结果表和下一个表重复执行步骤1到步骤3，直到 
    处理完所有的表为止。 
    4. 对VT3应用WHERE筛选器。只有使<where_condition>为TRUE的行才被插入VT4。 
    5. GROUP BY:按GROUP BY 子句中的列列表对VT4中的行分组，生成VT5。 
    6. CUBEROLLUP:把超组插入VT5，生成VT6。 
    7. HAVING:对VT6应用HAVING筛选器。只有使<having_condition>为TRUE的组才会被插入VT7。 
    8. SELECT:处理SELECT列表，产生VT8。 
    9. DISTINCT:将重复的行从VT8中移除，产生VT9。 
    10. ORDER BY:将VT9中的行按ORDER BY子句中的列列表排序，生成一个有表(VC10)。 
    11. TOP:从VC10的开始处选择指定数量或比例的行，生成表VT11,并返回给调用者。 

* left/right/inner join
```
mysql> select * from a;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  5 | e    |
+----+------+
mysql> select * from b;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | m    |
|  3 | n    |
|  4 | o    |
+----+------+
mysql> select * from a left join b on (a.id=b.id); #a为主
+----+------+------+------+
| id | name | id   | name |
+----+------+------+------+
|  1 | a    |    1 | a    |
|  2 | b    |    2 | m    |
|  3 | c    |    3 | n    |
|  5 | e    | NULL | NULL |
+----+------+------+------+
mysql> select * from a right join b on (a.id=b.id); #b为主
+------+------+----+------+
| id   | name | id | name |
+------+------+----+------+
|    1 | a    |  1 | a    |
|    2 | b    |  2 | m    |
|    3 | c    |  3 | n    |
| NULL | NULL |  4 | o    |
+------+------+----+------+
mysql> select * from b left join a on (a.id=b.id); #b为主
+----+------+------+------+
| id | name | id   | name |
+----+------+------+------+
|  1 | a    |    1 | a    |
|  2 | m    |    2 | b    |
|  3 | n    |    3 | c    |
|  4 | o    | NULL | NULL |
+----+------+------+------+
mysql> select * from b right join a on (a.id=b.id); #a为主
+------+------+----+------+
| id   | name | id | name |
+------+------+----+------+
|    1 | a    |  1 | a    |
|    2 | m    |  2 | b    |
|    3 | n    |  3 | c    |
| NULL | NULL |  5 | e    |
+------+------+----+------+
mysql> select * from a inner join b on (a.id=b.id);
+----+------+----+------+
| id | name | id | name |
+----+------+----+------+
|  1 | a    |  1 | a    |
|  2 | b    |  2 | m    |
|  3 | c    |  3 | n    |
+----+------+----+------+
mysql> select * from b inner join a on (a.id=b.id);
+----+------+----+------+
| id | name | id | name |
+----+------+----+------+
|  1 | a    |  1 | a    |
|  2 | m    |  2 | b    |
|  3 | n    |  3 | c    |
+----+------+----+------+
mysql> select * from a union select * from b; #交集（去除完全相同的行）
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  5 | e    |
|  2 | m    |
|  3 | n    |
|  4 | o    |
+----+------+
mysql> select * from a union all select * from b;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  5 | e    |
|  1 | a    |
|  2 | m    |
|  3 | n    |
|  4 | o    |
+----+------+

```
* 聚合函数count(),Avg(),Sum(),Max()和Min()
```
mysql> select id,count(\*) as count from (select * from a union all select * from b) c group by id having count>1; #grouby再聚合count再筛选having
+----+-------+
| id | count |
+----+-------+
|  1 |     2 |
|  2 |     2 |
|  3 |     2 |
+----+-------+
```
* view视图 
```
mysql> create view v AS select a.id,a.name,b.id as bid,b.name as bname from a left join b on (a.id=b.id);
mysql> show tables;
+--------------------+
| Tables_in_try      |
+--------------------+
| a                  |
| b                  |
| v                  |
+--------------------+
mysql> select * from v;
+----+------+------+-------+
| id | name | bid  | bname |
+----+------+------+-------+
|  1 | a    |    1 | a     |
|  2 | b    |    2 | m     |
|  3 | c    |    3 | n     |
|  5 | e    | NULL | NULL  |
|  6 | f    | NULL | NULL  |
+----+------+------+-------+
mysql> select * from v where id=1;
+----+------+-----+-------+
| id | name | bid | bname |
+----+------+-----+-------+
|  1 | a    |   1 | a     |
+----+------+-----+-------+
mysql> explain select * from v where id=1;
+----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
| id | select_type | table | type  | possible_keys | key     | key_len | ref   | rows | Extra |
+----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
|  1 | SIMPLE      | a     | const | PRIMARY       | PRIMARY | 4       | const |    1 |       |
|  1 | SIMPLE      | b     | const | PRIMARY       | PRIMARY | 4       | const |    1 |       |
+----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+

mysql> update v set name='d' where  bid=3;
mysql> select * from a;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | d    |
|  5 | e    |
|  6 | f    |
+----+------+
```
* zipcode是char类型,注意type
```
mysql> explain select * from people where zipcode='230031';
+----+-------------+--------+------+---------------+---------+---------+-------+------+-------------+
| id | select_type | table  | type | possible_keys | key     | key_len | ref   | rows | Extra       |
+----+-------------+--------+------+---------------+---------+---------+-------+------+-------------+
|  1 | SIMPLE      | people | ref  | zipcode       | zipcode | 32      | const |    1 | Using where |
+----+-------------+--------+------+---------------+---------+---------+-------+------+-------------+
mysql> explain select * from people where zipcode=230031;
+----+-------------+--------+------+---------------+------+---------+------+------+-------------+
| id | select_type | table  | type | possible_keys | key  | key_len | ref  | rows | Extra       |
+----+-------------+--------+------+---------------+------+---------+------+------+-------------+
|  1 | SIMPLE      | people | ALL  | zipcode       | NULL | NULL    | NULL |    3 | Using where |
+----+-------------+--------+------+---------------+------+---------+------+------+-------------+
```





* 聚集索引&非聚集索引
** 聚集索引（主键，逻辑顺序与物理顺序相同）
** 非聚集索引（B数结构，保存index列，叶子结点保存指向实际表的指针）
*** 非聚集索引的覆盖(直接在index找到select&where涉及的字段，无需去基础表取数据)






##### 链接
博主的一套都写得很好[T-SQL查询进阶--理解SQL Server中索引原理](http://www.cnblogs.com/CareySon/archive/2011/12/22/2297568.html)












#### sql
##### sql语句整理
* db中所有表的最后更新时间
mysql> select CREATE_TIME,UPDATE_TIME, TABLE_NAME from information_schema.TABLES where TABLE_SCHEMA='my_db' and TABLE_NAME like '%_hudong%'  order by UPDAT    E_TIME desc limit 30;
        +---------------------+---------------------+----------------------------------------+
        | CREATE_TIME         | UPDATE_TIME         | TABLE_NAME                             |
        +---------------------+---------------------+----------------------------------------+
        | 2013-01-05 11:20:22 | 2014-05-06 13:47:16 | loupan_hudong_post_display_time        |
        | 2012-10-25 16:45:02 | 2014-05-06 10:24:51 | loupan_hudong_manage_log          
        | 2012-10-25 16:42:43 | 2014-05-06 00:04:09 | loupan_hudong_blacklist_user           |

* 时间戳格式化
mysql> select *,FROM_UNIXTIME(end_date,'%Y-%m-%d') from XX




##### sql优化实例

共243213534条数据

```
CREATE TABLE `page_stat_hourly` (
  `hour_index` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'Ã¿Ê±',
  `city_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '³ÇÊÐID',
  `class_id` smallint(5) unsigned NOT NULL DEFAULT '0' COMMENT 'ÒµÎñÀà±ðID',
  `item_id` int(10) unsigned NOT NULL DEFAULT '0' COMMENT 'µ¥Ò³ID',
  `pv` int(10) unsigned NOT NULL DEFAULT '0',
  `uv` int(10) unsigned NOT NULL DEFAULT '0',
  `last_update` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT 'ÏµÍ³¸üÐÂÊ±¼ä',
  PRIMARY KEY (`hour_index`,`city_id`,`class_id`,`item_id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8 COMMENT='Ã¿Ê±µ¥Ò³PV/UV±í'

```

* explian
```
mysql> explain SELECT hour_index, item_id loupan_id, SUM(pv) pv FROM aifang_dw.page_stat_hourly 
    -> WHERE class_id IN (13,14) 
    -> AND hour_index>=2014010110 AND hour_index<=2014010310
    -> AND item_id IN (240524,243937) GROUP BY item_id, hour_index;
+----+-------------+------------------+-------+---------------+---------+---------+------+--------+----------------------------------------------+
| id | select_type | table            | type  | possible_keys | key     | key_len | ref  | rows   | Extra                                        |
+----+-------------+------------------+-------+---------------+---------+---------+------+--------+----------------------------------------------+
|  1 | SIMPLE      | page_stat_hourly | range | PRIMARY       | PRIMARY | 4       | NULL | 265831 | Using where; Using temporary; Using filesort |
+----+-------------+------------------+-------+---------------+---------+---------+------+--------+----------------------------------------------+
1 row in set (0.00 sec)

```





* show profile 语句使用的资源
```
mysql> set profiling = 1;
Query OK, 0 rows affected (0.01 sec)//打开profile只在本会话内有效

mysql> show profiles;
Empty set (0.01 sec)

mysql> SELECT hour_index, item_id loupan_id, SUM(pv) pv FROM aifang_dw.page_stat_hourly 
    -> WHERE class_id IN (13,14) 
    -> AND hour_index>=2014010110 AND hour_index<=2014010310
    -> AND item_id IN (240524,243937) GROUP BY item_id, hour_index;
Empty set (2.04 sec)

mysql> show profiles;
+----------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                                                                                                                                                          |
+----------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
|        1 | 2.02570000 | SELECT hour_index, item_id loupan_id, SUM(pv) pv FROM aifang_dw.page_stat_hourly 
WHERE class_id IN (13,14) 
AND hour_index>=2014010110 AND hour_index<=2014010310
AND item_id IN (240524,243937) GROUP BY item_id, hour_index |
+----------+------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)

mysql> show profile for Query 1;
+--------------------------------+----------+
| Status                         | Duration |
+--------------------------------+----------+
| starting                       | 0.000033 |
| checking query cache for query | 0.000068 |
| Opening tables                 | 0.000012 |
| System lock                    | 0.000009 |
| Table lock                     | 0.000028 |
| init                           | 0.000029 |
| optimizing                     | 0.000015 |
| statistics                     | 0.200135 |
| preparing                      | 0.000048 |
| Creating tmp table             | 0.000043 |
| executing                      | 0.000006 |
| Copying to tmp table           | 1.824958 |
| Sorting result                 | 0.000038 |
| Sending data                   | 0.000014 |
| end                            | 0.000005 |
| removing tmp table             | 0.000167 |
| end                            | 0.000006 |
| query end                      | 0.000005 |
| freeing items                  | 0.000033 |
| storing result in query cache  | 0.000007 |
| logging slow query             | 0.000004 |
| logging slow query             | 0.000031 |
| cleaning up                    | 0.000006 |
+--------------------------------+----------+
23 rows in set (0.01 sec)

mysql> SET @query_id = 1;
Query OK, 0 rows affected (0.01 sec)

mysql> SELECT STATE,SUM(DURATION) AS Total_R,
    ->     ROUND(100*SUM(DURATION)/(SELECT SUM(DURATION) FROM INFORMATION_SCHEMA.PROFILING
    ->     WHERE QUERY_ID = @query_id),2) AS Pct_R,
    ->     COUNT(*) AS Calls,
    ->     SUM(DURATION)/COUNT(*) AS "R/Call"
    -> FROM INFORMATION_SCHEMA.PROFILING
    -> WHERE QUERY_ID = @query_id
    -> GROUP BY STATE
    -> ORDER BY Total_R DESC;
+--------------------------------+----------+-------+-------+--------------+
| STATE                          | Total_R  | Pct_R | Calls | R/Call       |
+--------------------------------+----------+-------+-------+--------------+
| Copying to tmp table           | 1.824958 | 90.09 |     1 | 1.8249580000 |
| statistics                     | 0.200135 |  9.88 |     1 | 0.2001350000 |
| removing tmp table             | 0.000167 |  0.01 |     1 | 0.0001670000 |
| checking query cache for query | 0.000068 |  0.00 |     1 | 0.0000680000 |
| preparing                      | 0.000048 |  0.00 |     1 | 0.0000480000 |
| Creating tmp table             | 0.000043 |  0.00 |     1 | 0.0000430000 |
| Sorting result                 | 0.000038 |  0.00 |     1 | 0.0000380000 |
| logging slow query             | 0.000035 |  0.00 |     2 | 0.0000175000 |
| freeing items                  | 0.000033 |  0.00 |     1 | 0.0000330000 |
| starting                       | 0.000033 |  0.00 |     1 | 0.0000330000 |
| init                           | 0.000029 |  0.00 |     1 | 0.0000290000 |
| Table lock                     | 0.000028 |  0.00 |     1 | 0.0000280000 |
| optimizing                     | 0.000015 |  0.00 |     1 | 0.0000150000 |
| Sending data                   | 0.000014 |  0.00 |     1 | 0.0000140000 |
| Opening tables                 | 0.000012 |  0.00 |     1 | 0.0000120000 |
| end                            | 0.000011 |  0.00 |     2 | 0.0000055000 |
| System lock                    | 0.000009 |  0.00 |     1 | 0.0000090000 |
| storing result in query cache  | 0.000007 |  0.00 |     1 | 0.0000070000 |
| cleaning up                    | 0.000006 |  0.00 |     1 | 0.0000060000 |
| executing                      | 0.000006 |  0.00 |     1 | 0.0000060000 |
| query end                      | 0.000005 |  0.00 |     1 | 0.0000050000 |
+--------------------------------+----------+-------+-------+--------------+
21 rows in set (0.02 sec)

```

