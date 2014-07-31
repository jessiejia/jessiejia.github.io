## 安装/启动

下载solr-4.8.1.tgz并解压

文件夹下的example里面给出了完整的demo，可以直接运行

    java -jar start.jar

就可以直接使用了

    http://localhost:8983/solr/
    http://localhost:8983/solr/collection1/browse


## 配置

schema.xml

#### field

    <!-- solr4.x引入的的，不能删除，记录当前索引版本号  -->
    <field name="_version_" type="long" indexed="true" stored="true"/>

    <field name="id" type="string" indexed="true" stored="true" multiValued="false" required="true"/>
    <field name="name" type="text" indexed="true" stored="true" multiValued="false" />
    <field name="description" type="text" indexed="true" stored="true" multiValued="false" />
    <field name="name_auto" type="text_auto" indexed="true" stored="true" multiValued="false" />

    <dynamicField name="*_i" type="int" indexed="true" stored="true"/> 

    <copyField source="name" dest="name_auto" />



    属性       | 描述
    -----------|----------------
    name       | 字段类型名  
    indexed    | 缺省true。 说明这个数据应被搜索和排序，如果数据没有indexed， 则stored应是true。  
    stored     | 缺省true。说明这个字段被包含在搜索结果中是合适的。如果数据没有stored,则indexed应是true。
    omitNorms  | 字段的长度不影响得分和在索引时不做boost时，设置它为true。  一般文本字段不设置为true。  
    termVectors| 如果字段被用来做more like this 和highlight的特性时应设置为 true。  
    compressed | 字段是压缩的。这可能导致索引和搜索变慢，但会减少存储空间，只有StrField和TextField是可以压缩，这通常适合字段的长度超过200 个字符。  
    multiValued | 字段多于一个值的时候，可设置为true。  
    positionIncrementGap  | 和multiValued一起使用，设置多个值之间的虚拟空白的数量




#### fieldType

    <fieldType name="string" class="solr.StrField" sortMissingLast="true" />
    <fieldType name="text" class="solr.TextField" positionIncrementGap="100">
        <analyzer>
            <tokenizer class="solr.WhitespaceTokenizerFactory"/>
            <filter class="solr.WordDelimiterFilterFactory" generateWordParts="1" generateNumberParts="1" catenateWords="1" catenateNumbers="1" catenateAll="0" splitOnCaseChange="1"/>
            <filter class="solr.LowerCaseFilterFactory"/>
        </analyzer>
    </fieldType> 



    属性            | 描述 
    ----------------|--------------------------
    name            | 标识而已  
    class           | 和其他属性决定了这个fieldType的实际行为。  
    sortMissingLast | 设置成true没有该field的数据排在有该field的数据之后，而不管请求时的排序规则, 默认是设置成false。 
    sortMissingFirst| 跟上面倒过来。 默认是设置成false 
    analyzer        | 字段类型指定的分词器  
    type            | 当前分词用用于的操作.index代表生成索引时使用的分词器query代码在查询时使用的分词器 
    tokenizer       | 分词器类 
    filter          | 分词后应用的过滤器  过滤器调用顺序和配置相同.


#### uniqueKey

唯一键，主要用于当update/insert/delete时,具体见下。

#### defaultSearchField

指定默认搜索字段，用于q=abc 相当于 q=abc?df=name

#### solrQueryParser

指定各查询条件默认的关系, 默认是OR

    q=name:百度 name:abc
    等同于
    q=name:百度 OR name:abc
    q=name:百度 name:abc&q.op=OR


## 查询
    
    权重
    q=id:A*^3 id:U*  
    fl
    q=*:*&fl=sum:sum(weight, price),manu*,score,*&rows=5&start=3

## 更新/导入

    curl 'http://localhost:8983/solr/update' --data-binary @filename.xml -H 'Content-type:application/xml'
    
    <add>
        <doc>
          <field name="id">USD</field>
          <field name="name">One Dollar</field>
          <field name="description">Coins and notes</field>
        </doc>

        <doc>
          <field name="id">EUR</field>
          <field name="name">One Euro</field>
          <field name="description">Coinsnotes</field>
        </doc>
    </add>
        

## 删除
    
    curl http://localhost:8989/solr/update --data-binary "<delete><query>title:abc</query></delete>" -H 'Content-type:text/xml; charset=utf-8'

## 场景1；
#### 搜索时按分组统计

* facet方式

http://localhost:8989/solr/collection1/select/?rows=0&q=*:*&facet=true&facet.field=name_auto&facet.mincount=1&facet.prefix=百

    <response> 
      <lst name="responseHeader"> 
        <int name="status">0</int>  
        <int name="QTime">1</int>  
        <lst name="params"> 
          <str name="facet">true</str>  
          <str name="facet.mincount">1</str>  
          <str name="q">*:*</str>  
          <str name="facet.prefix">百</str>  
          <str name="facet.field">name_auto</str>  
          <str name="rows">0</str> 
        </lst> 
      </lst>  
      <result name="response" numFound="8" start="0"/>  
      <lst name="facet_counts"> 
        <lst name="facet_queries"/>  
        <lst name="facet_fields"> 
          <lst name="name_auto"> 
            <int name="百岁 考虑">1</int>  
            <int name="百度 百科">1</int>  
            <int name="百科 百度">1</int> 
          </lst> 
        </lst>  
        <lst name="facet_dates"/>  
        <lst name="facet_ranges"/> 
      </lst> 
    </response>

优点：简单，没有额外的组件依赖；有结果统计数；
缺点：添加额外的字段，消耗机器性能（数据量大时统计耗内存，CPU高）；

## 场景2；
#### 搜索框自动提示

* suggest模块[link](http://hankesi2000.iteye.com/blog/1171500)[wiki](http://wiki.apache.org/solr/Suggester)


http://localhost:8989/solr/collection1/suggest?spellcheck.build=true   #建索引
http://localhost:8989/solr/collection1/suggest?q=百

优点：灵活、性能好
缺点：当q中有空格，suggest会将其作为分隔符，比如“one dollar”结果只有one和dollar开头的;搜“百度”无结果，搜“百”有结果

    <response>
        <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">3</int>
        </lst>
    <str name="command">build</str>
    <lst name="spellcheck">
        <lst name="suggestions">
            <lst name="百">
                <int name="numFound">3</int>
                <int name="startOffset">0</int>
                <int name="endOffset">1</int>
                <arr name="suggestion">
                    <str>百岁</str>
                    <str>百度</str>
                    <str>百科</str>
                </arr>
            </lst>
        </lst>
    </lst>
    </response>


* terms模块[wiki](http://wiki.apache.org/solr/TermsComponent)

http://localhost:8989/solr/terms?terms.fl=name&terms.prefix=百

        <response>
            <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">0</int>
            </lst>
            <lst name="terms">
                <lst name="name">
                    <int name="百度">2</int>
                    <int name="百科">2</int>
                    <int name="百岁">1</int>
                </lst>
            </lst>
        </response>

http://localhost:8989/solr/terms?terms.fl=name&terms.regex=.*o.*&terms.regex.flag=case_insensitive  #支持正则，忽略大小写

        <response>
        <lst name="responseHeader">
        <int name="status">0</int>
        <int name="QTime">2</int>
        </lst>
        <lst name="terms">
        <lst name="name">
        <int name="one">4</int>
        <int name="dollar">1</int>
        <int name="euro">1</int>
        <int name="krone">1</int>
        <int name="pound">1</int>
        </lst>
        </lst>
        </response>


场景3；

* 搜索框输入多个查询条件

http://localhost:8989/solr/collection1/select/?q=one%20百科&df=name  #默认对name字段查询，根据schema定义name字段的分割符是Whitespace

    <response>
      <lst name="responseHeader"> 
        <int name="status">0</int>  
        <int name="QTime">0</int>  
        <lst name="params"> 
          <str name="index">on</str>  
          <str name="df">name</str>  
          <str name="q">one 百科</str> 
        </lst> 
      </lst>  
      <result name="response" numFound="6" start="0"> 
        <doc> 
          <str name="id">ABD</str>  
          <str name="name">百度 百科</str>  
          <str name="name_auto">百度 百科</str>  
          <str name="suggestion">百度 百科</str>  
          <str name="description">万事 如意</str>  
          <long name="_version_">1471946931639943168</long> 
        </doc>  
        <doc> 
          <str name="id">ABF</str>  
          <str name="name">百科 百度</str>  
          <str name="name_auto">百科 百度</str>  
          <str name="suggestion">百科 百度</str>  
          <str name="description">万事 如意</str>  
          <long name="_version_">1471946931643088896</long> 
        </doc>  
        <doc> 
          <str name="id">USD</str>  
          <str name="name">One Dollar</str>  
          <str name="name_auto">One Dollar</str>  
          <str name="suggestion">One Dollar</str>  
          <str name="description">Coins and notes</str>  
          <long name="_version_">1471946931629457408</long> 
        </doc>  
        <doc> 
          <str name="id">EUR</str>  
          <str name="name">One Euro</str>  
          <str name="name_auto">One Euro</str>  
          <str name="suggestion">One Euro</str>  
          <str name="description">Coinsnotes</str>  
          <long name="_version_">1471946931634700288</long> 
        </doc>  
        <doc> 
          <str name="id">NOK</str>  
          <str name="name">One Krone</str>  
          <str name="name_auto">One Krone</str>  
          <str name="suggestion">One Krone</str>  
          <str name="description">and notes</str>  
          <long name="_version_">1471946931637846016</long> 
        </doc>  
        <doc> 
          <str name="id">GBP</str>  
          <str name="name">One British Pound</str>  
          <str name="name_auto">One British Pound</str>  
          <str name="suggestion">One British Pound</str>  
          <str name="description">C and notes</str>  
          <long name="_version_">1471946931635748864</long> 
        </doc> 
      </result> 
    </response>





## 更多

* 缓存 http://my.oschina.net/u/1026644/blog/123957
* suggest http://tech.meituan.com/pinyin-suggest.html
* facet美团 http://tech.meituan.com/solr-facet.html
