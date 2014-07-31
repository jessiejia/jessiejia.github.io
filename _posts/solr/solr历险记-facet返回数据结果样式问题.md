
自己curl某个solr语句

    facet.field=kwfreq&facet=true&wt=json

原本我的美好预期：

    ["facet_fields"]=>
    array(1) {
      ["kwfreq"]=>
      array(2) {
        [1]=>
        int(1)
        [2]=>
        int(1)
      }
    }

残酷的现实：

    ["facet_fields"]=>
    array(1) {
      ["kwfreq"]=>
      array(4) {
        [0]=>
        string(1) "1"
        [1]=>
        int(1)
        [2]=>
        string(1) "2"
        [3]=>
        int(1)
      }
    }

于是发现wt=json时，返回结果是：

    "facet_fields":{"kwfreq":["1",2,"2",1]}  #就是这里！！！这种样式，jsondecode转化之后就无法直视，出现上面的状况了。

而wt=xml时:    

    <lst name="facet_fields">
        <lst name="kwfreq">
            <int name="1">2</int>
            <int name="2">1</int>
        </lst>
    </lst>

再试试solr-php扩展，居然也是OK的。

#### 完美解决
    
    json.nl=map

官方解释：<https://wiki.apache.org/solr/SolJSON#JSON_specific_parameters>

    json.nl - This parameter controls the output format of NamedLists, where order is more important than access by name. NamedList is currently used for field faceting data.
    json.nl=flat - the default. NamedList is represented as a flat array, alternating names and values: [name1,val1, name2,val2]
    json.nl=map - NamedList is represented as a JSON object. Although this is the simplest mapping, a NamedList can have optional keys, repeated keys, and preserves order. Using a JSON object (essentially a map or hash) for a NamedList results in the loss of some information.
    json.nl=arrarr - represent a NamedList as an array of two element arrays [[name1,val1], [name2, val2], [name3,val3]]
Note: json.nl also works for the Python and Ruby output formats.



