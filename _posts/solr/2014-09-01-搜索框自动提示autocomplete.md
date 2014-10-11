---
layout: post
title: 搜索框自动提示autocomplete 
category: 搜索引擎
tags: solr
---

需求

* 根据前缀匹配原则
* 支持中文、拼音、拼音缩写查找
* 支持多音字输入提示
* 根据用户行为，按照关键字热度排序

推荐阅读 [搜索引擎关键字智能提示的一种实现](http://tech.meituan.com/pinyin-suggest.html)

#### schema
    
    <field name="kw" type="string" indexed="true" stored="true" />  
    <field name="pinyin" type="string" indexed="true" stored="false" multiValued="true"/>
    <field name="abbre" type="string" indexed="true" stored="false" multiValued="true"/>
    <field name="kwfreq" type="int" indexed="true" stored="true" />
    <field name="suggest" type="suggest_text" indexed="true" stored="false" multiValued="true" />

    <copyField source="kw" dest="suggest" />
    <copyField source="pinyin" dest="suggest" />
    <copyField source="abbre" dest="suggest" />
    <uniqueKey>kw</uniqueKey>

    <fieldType name="suggest_text" class="solr.TextField" positionIncrementGap="100" autoGeneratePhraseQueries="true">
        <analyzer type="index">
                <tokenizer class="solr.KeywordTokenizerFactory" />
                <filter class="solr.SynonymFilterFactory" 
                        synonyms="synonyms.txt" 
                        ignoreCase="true" 
                        expand="true" />
                <filter class="solr.StopFilterFactory" 
                        ignoreCase="true" 
                        words="stopwords.txt" 
                        enablePositionIncrements="true" />
                <filter class="solr.LowerCaseFilterFactory" />
                <filter class="solr.KeywordMarkerFilterFactory" protected="protwords.txt" />
        </analyzer>
        <analyzer type="query">
                <tokenizer class="solr.KeywordTokenizerFactory" />
                <filter class="solr.StopFilterFactory" 
                        ignoreCase="true" 
                        words="stopwords.txt" 
                        enablePositionIncrements="true" />
                <filter class="solr.LowerCaseFilterFactory" />
                <filter class="solr.KeywordMarkerFilterFactory" protected="protwords.txt" />
        </analyzer>
    </fieldType>
    <fieldType name="int" class="solr.TrieIntField" precisionStep="0" positionIncrementGap="0"/>
    <fieldType name="string" class="solr.StrField" sortMissingLast="true" />

#### 形成的数据为

     <doc>
         <str name="kw">红烧小公鸡</str>
         <arr name="suggest">
             <str>红烧小公鸡</str>
             <str>hongshaoxiaogongji</str>
             <str>hsxgj</str>
         </arr>
         <arr name="pinyin">
             <str>hongshaoxiaogongji</str>
         </arr>
         <arr name="abbre">
             <str>hsxgj</str>
         </arr>
         <int name="kwfreq">1</int>
     </doc>
    
#### 针对suggest字段查询

http://localhost:8988/solr/collection1/select/?q=红*&df=suggest&sort=kwfreq desc

    <response>
      <lst name="responseHeader">
        <int name="status">0</int>
        <int name="QTime">2</int>
        <lst name="params">
          <str name="df">suggest</str>
          <str name="q">红*</str>
        </lst>
      </lst>
      <result name="response" numFound="2" start="0">
        <doc>
          <str name="kw">红烧茄子</str>
          <int name="kwfreq">1</int>
        </doc>
        <doc>
          <str name="kw">红烧小公鸡</str>
          <int name="kwfreq">2</int>
        </doc>
      </result>
    </response>

