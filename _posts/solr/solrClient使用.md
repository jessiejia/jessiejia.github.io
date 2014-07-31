
在使用solrClient的时候，可以输出solrQuery拼装完成的查询语句

    <?php
    $options = array
    (
        'hostname' => '127.0.0.1',
        'port'     => 8983,
    );
    $client = new SolrClient($options);

    $query = new SolrQuery();

    $query->setQuery($query);

    $query->addFilterQuery('weight:1');

    echo $query;  //未urldecode的查询语句
    var_dump($query->getPreparedParams());  //urldecoded过的查询条件


    $query_response = $client->query($query);
    $response = $query_response->getResponse();
    print_r($response);


