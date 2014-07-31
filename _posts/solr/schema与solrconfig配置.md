



## FieldType

#### sortMissingLast & sortMissingFirst

    <fieldType name="string" class="solr.StrField" sortMissingLast="true" />

* sortMissingLast="true"，没有该field的数据排在有该field的数据之后，而不管请求时的排序规则。
* sortMissingFirst="true"，跟上面倒过来呗。
* 默认false


#### 分词器, 差分成序列，同时去除空白字符等

- KeywordTokenizerFactory 不进行分词
- WhitespaceTokenizerFactory 空格分词（空格/tab/换行）
- StandardTokenizerFactory 它是一个对大部分西欧语言通常的分词器。它从空白符和其它Unicode标准中的词分隔符处进行切分。空白符和分隔符会被移除。连字符也被认为是词的分隔符，这使得它不适合与WordDelimiterFilter一起用。
- LowerCaseFilterFactory - 转化成小写
- StopFilterFactory  - 禁用词

    <fieldType name="suggest_text" class="solr.TextField" positionIncrementGap="100" autoGeneratePhraseQueries="true">
        <analyzer type="index">  <!-- 导入数据时的分词方法 -->
                <tokenizer class="solr.KeywordTokenizerFactory" /> <!-- 分词器, 差分成序列，同时去除空白字符等 -->
                <filter class="solr.SynonymFilterFactory" <!-- -->
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
        <analyzer type="query">  <!-- 查找时对查找词的分词方法 -->
                <tokenizer class="solr.KeywordTokenizerFactory" />
                <filter class="solr.StopFilterFactory" 
                        ignoreCase="true" 
                        words="stopwords.txt" 
                        enablePositionIncrements="true" />
                <filter class="solr.LowerCaseFilterFactory" />
                <filter class="solr.KeywordMarkerFilterFactory" protected="protwords.txt" />
        </analyzer>
    </fieldType>
    <fieldType name="text" class="solr.TextField" positionIncrementGap="100">
        <analyzer>
            <tokenizer class="solr.WhitespaceTokenizerFactory"/> <!-- 空格分词 -->
            <filter class="solr.WordDelimiterFilterFactory" generateWordParts="1" generateNumberParts="1" catenateWords="1" catenateNumbers="1" catenateAll="0" splitOnCaseChange="1"/>
            <filter class="solr.LowerCaseFilterFactory"/>
        </analyzer>
    </fieldType>






## copyField是做什么用的?

一般用来将多个字段数据copy到某一个查询字段,方便查询。

能很好解决我们同时索引多个字段(汉字、pinyin, abbre)的需求。

(注意目标字段必须是multiValued):

    <field name="kw" type="string" indexed="true" stored="true" /> 
    <field name="pinyin" type="string" indexed="true" stored="true" multiValued="true"/>
    <field name="abbre" type="string" indexed="true" stored="true" multiValued="true"/>
    <field name="kwfreq" type="int" indexed="true" stored="true" />
    <field name="suggest" type="suggest_text" indexed="true" stored="true" multiValued="true" />
    <copyField source="kw" dest="suggest" />
    <copyField source="pinyin" dest="suggest" />
    <copyField source="abbre" dest="suggest" />

最后的数据会变成：

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

此时搜索q=hs*&df=suggest就会很轻松有没有？


## 弃用`defaultSearchField`和`defaultOperator` !

你或许发现自己的defaultSearchField失效了,似乎是3.6开始的吧，只能在查询语句中指定df了

<https://issues.apache.org/jira/browse/SOLR-2724>
