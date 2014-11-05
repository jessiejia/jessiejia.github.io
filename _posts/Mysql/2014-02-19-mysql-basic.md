---
layout: post
title: Mysql杂记
category: Mysql
tags: Mysql
---

### 查看权限
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

##### 查看字符集
```
mysql> show variables like 'character_set%';
+--------------------------+--------------------------------------------------------+
| Variable_name            | Value                                                  |
+--------------------------+--------------------------------------------------------+
| character_set_client     | utf8   客户端发送来的数据的字符集                          |
| character_set_connection | utf8   客户端数据(client字符集)转换为该会话所用的字符集       |
| character_set_database   | latin1                                                 |
| character_set_filesystem | binary                                                 |
| character_set_results    | utf8   客户端接收到的数据                                 |
| character_set_server     | latin1                                                 |
| character_set_system     | utf8                                                   |
| character_sets_dir       | /usr/local/mysql-5.6.21-osx10.8-x86_64/share/charsets/ |
+--------------------------+--------------------------------------------------------+
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


## ALTER TABLE

* 修改表名：alter table t_book rename to bbb;
* 添加列：alter table 表名 add column 列名 varchar(30);
* 删除列：alter table 表名 drop column 列名;
* 修改列名： alter table bbb change nnnnn hh int;
* 修改列属性：alter table t_book modify name varchar(22);

##### 修改索引

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

[ALTER TABLE运行时会对原表进行临时复制，在副本上进行更改，然后删除原表，再对新表进行重命名。在执行ALTER TABLE时，其它用户可以阅读原表，但是对表的更新和修改的操作将被延迟，直到新表生成为止。新表生成后，这些更新和修改信息会自动转移到新表上。RENAME除外](http://dev.mysql.com/doc/refman/5.1/zh/sql-syntax.html)




### SQL语法

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





