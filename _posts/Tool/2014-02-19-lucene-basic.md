---
layout: post
title: 搜索引擎基础
category: 搜索引擎 
tags: lucene solr elastic
---

* pider
```
判断是否过期
$ curl -I http://baidu.com
HTTP/1.1 200 OK
Date: Fri, 24 May 2013 05:33:40 GMT
Server: Apache
Cache-Control: max-age=86400
Expires: Sat, 25 May 2013 05:33:40 GMT
Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT
ETag: "51-4b4c7d90"
Accept-Ranges: bytes
Content-Length: 81
Connection: Keep-Alive
Content-Type: text/html

$ curl -I --header 'If-Modified-Since: Tue, 12 Jan 2010 13:48:00 GMT'  http://baidu.com
HTTP/1.1 304 Not Modified
Date: Fri, 24 May 2013 05:35:17 GMT
Server: Apache
Connection: Keep-Alive
ETag: "51-4b4c7d90"
Expires: Sat, 25 May 2013 05:35:17 GMT
Cache-Control: max-age=86400
```
* Analyzer
** 分词 (抽取主要的单词)
** 词法语法 (处理成标准单词，中文效率低&需要词库)

*Indexer
** 生成反向索引文件
*** 按照字母顺序
*** 单词 - 文档频次（共N个doc出现） -> 1号doc,出现M次，位置分别是X  ->5号doc,出现M次，位置分别是Y
***

* Score
** 权重：计算查询的各个单词对所在文档的权重 W = tf * log(n/df)
*** 词频（越高越好） tf
*** 次要关键词 （到处都出现的关键词，越少越好） df
** 空间向量模型

*lucene | solr | elasticSearch
** lucene(全文检索引擎工具包) 2001年9月 v4.2
** solr(全文检索Server) 2006年1月   v4.2   适合update很少的大数据量的查询
** elastic(全文检索Server) 2010年2月 v4    适合Real-Time查询

* 分布式
** Master/Slave 
** Cloud   （例如：http://10.10.6.40:7733/solr/#/~cloud）

* DataImport

#### elastic
```
# start
bin/elasticsearch -f

# 创建索引
$ curl -XPUT 'http://localhost:9200/test-index/'

# 设置分片/备份及schema
$ curl -XPUT http://localhost:9200/test-index/test-type/_mapping -d '{"properies":{"name":{"type":"String"},"job":{"type":"String"}}}'
$ curl -XPOST localhost:9200/test-index -d '{
    "settings" : {
        "number_of_shards" : 1,
        "number_of_replicas" : 2
    },
    "mappings" : {
        "test-type" : {
            "_source" : { "enabled" : false },
            "properties" : {
                "uid" : { "type" : "int","store" : "no"},
                "name" : { "type" : "string","store" : "yes"},
                "job" : { "type" : "string", "store" : "yes"}
                "code" : { "type" : "string","store" : "yes"}
                "phone":{"type" : "string", "store" : "yes"}
                "email":{"type" : "string", "store" : "yes"}
                "depart":{"type" : "string","store" : "yes"}

            }
        }
    }
}'
# 导入数据
curl -XPOST http://localhost:9200/test-index/test-type/ -d '{"name":"莉莉李", "job":"开发" , "english":"jiaojiaoli"}'

# 查看mapping
http://localhost:9200/test-index/test-type/_mapping

# Search
http://localhost:9200/test-index/test-type/_search?q=name:mike

# Delete
curl -XDELETE 'http://localhost:9200/test-index/test-type/1'
```

#### solr
```
# 查询
select * from solr where loupan_id=236850 and (status:9 OR status:4 OR status:7) and (20131022<=start_day<=20140120 OR 20131022<=end_day<=20140120 OR(start_day<=20131022 AND end_day<=20140120)) order by end_day desc,ad_id desc offset 0 limit 100;

http://dev.aifang.com:8487/solr/crm-order/select/?q=*:*&fq=(loupan_id:236850)&fq=(status:9 OR status:4 OR status:7)&fq=(start_day:\[20131022 TO 20140120\] OR end_day:\[20131022 TO 20140120\] OR(start_day:\[* TO 20131022\] AND end_day:\[ * TO 20140120\]))&sort=end_day desc,ad_id desc&rows=100&start=0

```


