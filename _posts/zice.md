



#### Dao2影响范围

Aifang_Web_Ajax_AutoCompleteController

http://jessiejia.kfs.dev.anjuke.test/fangyuan/?from=navigation&debug=0
    
    * http://jessiejia.kfs.dev.anjuke.test/a/autocomplete?t=2&c=11&n=8&kw=z



#### Dao影响范围

evans@kfs-web:kfs$ [pmt-21593-2] grep -rn 'Aifang_Core_Bll_Solr_'  .
./app-aifang-web/controller/aifang/web/props/Search.php:14:apf_require_class('Aifang_Core_Bll_Solr_Prop');
./app-aifang-web/controller/aifang/web/props/Search.php:33:        $this->prop_solr = new Aifang_Core_Bll_Solr_Prop();

evans@kfs-web:kfs$ [pmt-21593-2] grep -A 5 -rn Aifang_Web_Props_Search `find ./ -name route.php`
./app-aifang-web/config/route.php:827:$config['mappings']['Aifang_Web_Props_Search'] = array(
./app-aifang-web/config/route.php-828-    '^/loupfangyuan/([a-z]+)\_([0-9]+)/?$',
./app-aifang-web/config/route.php-829-    '^/fangyuan/([a-z]+)\_([0-9]+)/s$',
./app-aifang-web/config/route.php-830-    '^/fangyuan/([a-z]+)\_([0-9]+)/$',
./app-aifang-web/config/route.php-831-    '^/fangyuan/([a-z]+)/$',
./app-aifang-web/config/route.php-832-    '^/fangyuan/([a-z]+)/s+',

    http://sh.jessiejia.kfs.dev.anjuke.test/fangyuan/4647529.html

---

./app-aifang-web/controller/aifang/web/ajax/AutoComplete.php:138:            apf_require_class('Aifang_Core_Bll_Solr_Loupan');
./app-aifang-web/controller/aifang/web/ajax/AutoComplete.php:139:            $loupan_solr = new Aifang_Core_Bll_Solr_Loupan();

evans@kfs-web:kfs$ [pmt-21593-2] grep -A 5 -rn Aifang_Web_Ajax_AutoC `find ./ -name route.php`
./app-aifang-my/config/route.php:99:$config['mappings']['Aifang_Web_Ajax_AutoComplete'] = array(
./app-aifang-my/config/route.php-100-   '^/fang/a/autocomplete?',
./app-aifang-my/config/route.php-101-   '^/a/autocomplete?'
./app-aifang-my/config/route.php-102-);
./app-aifang-my/config/route.php-103-$config['mappings']['Aifang_My_MyTuangou']=array(
./app-aifang-my/config/route.php-104-   '^/fang/tuangou/',

./app-aifang-web/config/route.php:25:$config['mappings']['Aifang_Web_Ajax_AutoComplete'] = array(
./app-aifang-web/config/route.php-26-    '^/aifang/ajax/autocomplete'
./app-aifang-web/config/route.php-27-);
./app-aifang-web/config/route.php-28-//房易通
./app-aifang-web/config/route.php-29-$config['mappings']['Aifang_Web_Activity_Fyt'] = array(
./app-aifang-web/config/route.php-30-    '^/fyt/$'

./app-aifang-web/config/route.php:720:$config['mappings']['Aifang_Web_Ajax_AutoComplete'] = array(
./app-aifang-web/config/route.php-721-    '^/a/autocomplete?'

    http://jessiejia.kfs.dev.anjuke.test/a/autocomplete?from=xxxx&t=2&c=11&n=8&kw=z    

---

./app-aifang-web/controller/aifang/web/loupan/list/Base.php:479:        apf_require_class('Aifang_Core_Bll_Solr_Loupan');
./app-aifang-web/controller/aifang/web/loupan/list/Base.php:480:        $sort_filter['list'] = Aifang_Core_Bll_Solr_Loupan::get_loupan_sort_list($city_id);

evans@kfs-web:kfs$ [pmt-21593-2] grep -rn Aifang_Web_Loupan_List_Base .
./app-aifang-web/controller/aifang/web/loupan/list/Base.php:8:abstract class Aifang_Web_Loupan_List_BaseController extends Aifang_Web_AbstractController
./app-aifang-web/controller/aifang/web/loupan/list/ListIndex.php:9:class Aifang_Web_Loupan_List_ListIndexController extends Aifang_Web_Loupan_List_BaseController
./app-aifang-web/controller/aifang/web/loupan/list/ListKeywords.php:8:class Aifang_Web_Loupan_List_ListKeywordsController extends Aifang_Web_Loupan_List_BaseController 


evans@kfs-web:kfs$ [pmt-21593-2] grep -A 5 -rn Aifang_Web_Loupan_List_List `find ./ -name route.php`
./app-aifang-web/config/route.php:401:$config['mappings']['Aifang_Web_Loupan_List_ListIndex'] = array(
./app-aifang-web/config/route.php-402-//$config['mappings']['Aifang_Web_Loupan_List_NewLeadSearch'] = array(
./app-aifang-web/config/route.php-403-    '^/$',
./app-aifang-web/config/route.php-404-    '^/loupan/s?$',
./app-aifang-web/config/route.php-405-    '^/loupan/([a-z]+)$',
./app-aifang-web/config/route.php-406-    '^/loupan/([a-z]+)/$',

    app-aifang-web/controller/aifang/web/loupan/list/ListIndex.php
---

./app-aifang-web/controller/aifang/web/loupan/daquan/NewIndexB.php:7:apf_require_class('Aifang_Core_Bll_Solr_Daquan');
./app-aifang-web/controller/aifang/web/loupan/daquan/NewIndexB.php:60:        $this->SolrBll = new Aifang_Core_Bll_Solr_Daquan();

evans@kfs-web:kfs$ [pmt-21593-2] grep -A 5 -rn Aifang_Web_Loupan_Daquan_NewIndexB `find ./ -name route.php`
./app-aifang-web/config/route.php:116:$config['mappings']['Aifang_Web_Loupan_Daquan_NewIndexB'] = array(
./app-aifang-web/config/route.php-117-        //'^/leading/$',
./app-aifang-web/config/route.php-118-      //'^/leading/s?$',      //全部导购
./app-aifang-web/config/route.php-119-      '^/news/s?$',
./app-aifang-web/config/route.php-120-      '^/news/market/s?$',
./app-aifang-web/config/route.php-121-      '^/news/hot/s?$',

    http://sh.jessiejia.kfs.dev.anjuke.test/news/

---

./app-aifang-web/controller/aifang/web/loupan/view/Index.php:442:        $propsolr_bll = new Aifang_Core_Bll_Solr_Prop();
    
    http://sh.jessiejia.kfs.dev.anjuke.test/loupan/250126.html?from=AF_RANK_1    
---
./app-aifang-web/controller/aifang/web/loupan/view/louping/ArticleDaogou.php:81:        $this->SolrBll = new Aifang_Core_Bll_Solr_Daquan();

---

./app-aifang-web/controller/aifang/web/loupan/view/Price.php:114:       $this->SolrBll = new Aifang_Core_Bll_Solr_Daquan();

./app-aifang-web/config/route.php:506:$config['mappings']['Aifang_Web_Loupan_View_Price'] = array(
./app-aifang-web/config/route.php-507-    '^/loupan/jiage-([0-9]+)\.html'

function未调用

---

./app-aifang-web/controller/aifang/web/loupan/view/Single.php:298:        $propsolr_bll = new Aifang_Core_Bll_Solr_Prop();
没route
---
./app-aifang-web/controller/aifang/web/loupan/view/TanPan.php:112:      $this->SolrBll = new Aifang_Core_Bll_Solr_Daquan();

./app-aifang-web/config/route.php:1199:$config['mappings']['Aifang_Web_Loupan_View_TanPan'] = array(
./app-aifang-web/config/route.php-1200-    '^/loupan/tanpan-([0-9]+)\.html'

    http://sh.jessiejia.kfs.dev.anjuke.test/loupan/tanpan-249074.html?from=AF_RANK_2

---

./app-aifang-web/controller/aifang/web/loupan/view/Props.php:34:        $this->prop_solr = new Aifang_Core_Bll_Solr_Prop();

./app-aifang-web/config/route.php:563:$config['mappings']['Aifang_Web_Loupan_View_Props'] = array(
./app-aifang-web/config/route.php-564-    '^/loupan/fangyuan-([0-9]+)\.html',
./app-aifang-web/config/route.php-565-    '^/loupan/fangyuan-([0-9]+)/.*'
./app-aifang-web/config/route.php-566-);

    http://sh.jessiejia.kfs.dev.anjuke.test/loupan/fangyuan-210624.html
---

./app-aifang-web/controller/inform/seo/NewsList.php:94:        apf_require_class("Aifang_Core_Bll_Solr_Daquan");
./app-aifang-web/controller/inform/seo/NewsList.php:95:        $solr_bll = new Aifang_Core_Bll_Solr_Daquan();

./app-aifang-web/config/route.php:241:$config['mappings']['Inform_Seo_NewsList'] = array(
./app-aifang-web/config/route.php-242-    '^/news/b.html',
./app-aifang-web/config/route.php-243-    '^/news/b(-[p|h]).html'

    http://sh.jessiejia.kfs.dev.anjuke.test/news/b.html



##### SolrClient
evans@kfs-web:kfs$ [pmt-21593-2] grep -rn 'new SolrClient' .
./app-aifang-jobs/classes/aifang/job/loupan/LoupanIndexSimple.php:63:        $this->solrClient = new SolrClient($options);
./app-aifang-jobs/classes/aifang/job/crm/CustomerIndex.php:19:        $this->solrclient = new SolrClient($options);
./app-aifang-jobs/classes/aifang/job/crm/ContactsList.php:17:        $this->solrclient = new SolrClient($options);
./app-aifang-jobs/classes/aifang/job/crm/LoupanIndex.php:18:        $this->solrclient = new SolrClient($options);
./app-aifang-jobs/classes/aifang/job/inform/CleanInformColumn.php:97:        $this->solrClient = new SolrClient($options);
./app-aifang-admin/controller/crm/sale/ContactsRedo.php:94:        $this->solrclient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/crm3/CustomerInfo.php:142:        $this->solrClient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/crm3/CustomerResource.php:57:        $this->solrclient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/crm3/ContactUser.php:233:        $this->solrClient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/member/SeoFriendLink.php:540:        $this->solrClient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/crm/CustomerInfo.php:142:        $this->solrClient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/crm/CustomerResource.php:57:        $this->solrclient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/crm/OuterInterface.php:1688:        $solrclient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/bll/crm/ContactUser.php:277:        $this->solrClient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/solr/admin/Customer.php:10:        $this->solrClient=new SolrClient($options);
./app-aifang-core/classes/aifang/core/solr/admin/Crm.php:10:        $this->solrClient=new SolrClient($options);
./app-aifang-core/classes/aifang/core/solr/admin/Loupan.php:10:        $this->solrClient=new SolrClient($options);
./app-aifang-core/classes/aifang/core/solr/trip/TransSpot.php:17:       $this->_solrClient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/solr/trip/Loupan.php:17:        $this->_solrClient = new SolrClient($options);
./app-aifang-core/classes/aifang/core/solr/Basic.php:19:            $this->solrClient = new SolrClient($client_options);
./app-aifang-core/classes/crm2/solr/Abstract.php:21:            $this->_client = new SolrClient($options);
