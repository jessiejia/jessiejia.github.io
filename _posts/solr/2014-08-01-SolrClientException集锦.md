---
layout: post
title: SolrClientException集锦
category: 搜索引擎
tags: solr
---



## 报错SolrClientException

#### 1、Unknown commit parameter 'waitFlush'

* php扩展solrClient1.x中的commit()命令与实际调用的solr4.x不兼容导致的，（具体可以看看php.net怎么说）
* 建议代码中不要调用solrClient->commit(),改成自己curl实现(/update?commit=true)，或者依靠solr自己的autocommit机制实现。 


#### 2、URLDecoder: Invalid character encoding detected after position 13 of query string / form data (while parsing as UTF-8)

* solr4.x要求查询参数是utf8，大家要注意啊
* 所以从接收用户参数到solr查询要进行哪些处理呢？见另一篇《solr用户参数处理》

#### 3、org.apache.solr.search.SyntaxError: Cannot parse..

* 如果查询信息包含solr的特殊字符，请转义solr的特殊字符
* 可以用SolrUtils::escapeQueryChars($yourword);//转义solr特殊字符


#### 4、Unsuccessful query request : Response Code 0. (null)

* http code为0
* “the error code indicates that the response was empty, (as not even headers were returned). This means the connection was accepted and then closed gracefully (TCP FIN)” 
