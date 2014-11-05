---                                                                                                                                                                              
layout: post
title: Mysql left/right/inner join
category: Mysql
tags: Mysql
---

##### left/right/inner join的区别

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
