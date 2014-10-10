---
layout: post
title: APF调试logger
category: APF
tags: logger
---

## 新版本debug信息,基于log4php

[什么是log4php？](/2014/08/18/log4php.html)


	self::$logger = Apf_Logger_LoggerFactory::getLogger(__CLASS__);
	self::$logger->debugs("memcache init $configName", $hosts, self::$version);

system-ext/classes/apf/logger/LoggerFactory.php

    public static function getLogger($name)
    {   
        self::init();
        $logger = null;
        if(! self::$initFalse) {
            $logger = Logger::getLogger(str_replace('_', '.', $name));//创建基于log4php的$logger
        }   
        return new Apf_Logger_Logger($logger);//将$logger对象进行封装
    } 



## 老版本debug信息

	message ==>>==  debug	==>>==    APF_Performance(发给java)(debuger.php)
								      APF_Debugger(debug信息)(debuger.php)
			==>>==  console(直接输出)(logger.php文件) 
			==>>==	file(打印到文件)(logger.php文件)


./app-aifang-core/classes/aifang/core/DAO.php

	self::$logger = Aifang_System_Log_Factory::getLogger(__CLASS__);
	self::$logger->debug("[execute sql] $sql");

用单例模式调用Logger类

aifang-system/classes/aifang/system/log/Logger.php

它会从文件`logger.php`获取config

	$config['name'][Aifang_System_Log_Logger::ROOT_NAME] = array(
        'level' => Aifang_System_Log_Logger::LEVEL_WARN,
        'file_path' => '/data1/logs/php/v2-default.log'
	);
	$config['name']['Aifang_Core_DAO']['file_path'] = '/data1/logs/php/v2-dao.log';

	$config['target_enable'][Aifang_System_Log_Logger::TARGET_FILE] = true;//$message写入文件file_path，如果file_path==null则无效
	$config['target_enable'][Aifang_System_Log_Logger::TARGET_CONSOLE] = false;//直接echo $message

如果满足isset($apf->debugger) && 报错等级

    private function innerLog($level) {
		//如果APF_DebuggerInterceptor中初始化APF_Debugger成功
		//或者如果报错等级>=config['name']['Aifang_Core_DAO']['level']规定的报错等级
        if(! self::$apf->is_debug_enabled() && ! $this->checkEnable($level)) {
            return false;
        }   

        $args = func_get_args();
        $args = $args[1];

        $leven_name = self::$LEVEL_MP[$level];

        $only_string = true;
        foreach($args as $val) {
            if(! is_string($val) && ! is_numeric($val)) {
                $only_string = false;
            }   
        }   

        if($only_string) {
            $message = join(' | ', $args);
        } else {
            $message = json_encode($args);
        }   

        $request_session_id = ''; 

        if(defined('REQUEST_SESSION_ID')) {
            $request_session_id = "[" . REQUEST_SESSION_ID . "] ";
        }   

        $message = "$leven_name " . APP_NAME . date(' Y-m-d H:i:s') . " [{$this->log_name}] $request_session_id" . $message . PHP_EOL;

		//Aifang_System_Debugger::get_instance()->debug($message)模块[#见下文]
        self::$debugger->debug($message);
        if(! $this->checkEnable($level) || ! $this->checkFilterAllow($message)) {
            return false;
        }   
		
		//如果$config['target_enable'][Aifang_System_Log_Logger::TARGET_CONSOLE]==true，直接打印
        if(self::$target_enable_console) {
            echo $message;
        }   

		//如果$config['target_enable'][Aifang_System_Log_Logger::TARGET_FILE]==true,
		//并且设置了config['name']['XXX']['file_path']，则直接输出到log文件
        if(self::$target_enable_file && $this->file_path !== null) {
            file_put_contents($this->file_path, $message, FILE_APPEND | LOCK_EX);        }   
        return true;
    }   

aifang-system/classes/aifang/system/Debugger.php

从config文件debuger.php

	$config['default'] = array(
		'enable' => true,
    	'targets' => array(
			'apf_debug' => true
		)   
	);


    public function debug($log) {
        $this->set_force_performance();

        /**
         * 在强制性能监控并且将数据发送到java端
         */
        if($this->force_performance) {
            $this->force_performance_trace($log);
        }
		//如果['default']['enable']==true才能继续
        if(! $this->_enable) {
            return;
        }
		//如果['default']['targets']['apf_debug']==true && APF_DebuggerInterceptor中初始化APF_Debugger成功
        if($this->enable_apf_debug) {
            $trace = '';
            if(empty($_REQUEST['notrace'])) {
                $trace = $this->get_backtrace();
            }
            self::$apf->debug($log . "|||" . $trace);//单例模式调用APF_Debugger
        }

		//详见config文件performance.php
        if(! $this->force_performance) {
            $this->performance_trace($log);//干什么用的？？？
        }
    }

./interceptor/apf/Debugger.php

打开APF_Debug，使能够APF::get_instance()->debug();

* common.php
		
		$config['debug_allow_patterns'] = array('/^192\.168\.181\./', '/^192\.168\.201\.133$/', );

* ?debug=1



#### 载入配置get_config(参数名,文件名)

根据index.php里面设置的$G_CONF_PATH,依次include(path.文件名.php)，注意后加载的同名变量会覆盖前面的。

最后返回$config[文件名][参数名]

#### interceptor的加载及作用

- 先在配置文件interceptor.php
- 找$config['Aifang_Sales_V2_Ajax_PlateTrain'] + $config['global'],
- 找不到则$config['APF_Controller'] + $config['global'],
- 找不到则$config['default'] + $config['global'],


        $interceptores = @$this->get_config($class, "interceptor");
        if ($interceptores) {
            $interceptor_classes = $this->get_interceptor_classes($class);
        } else {
            $basic_class = $controller->get_interceptor_index_name();
            $interceptor_classes = $this->get_interceptor_classes($basic_class);
        }

        $step = APF_Interceptor::STEP_CONTINUE;
        foreach ($interceptor_classes as $interceptor_class) {
            $interceptor = $this->load_interceptor($interceptor_class);
            if (!$interceptor) {
                continue;
            }   
            $interceptors[] = $interceptor;
            $this->debug("interceptor::before(): " . get_class($interceptor));
            $step = $interceptor->before();
            if ($step != APF_Interceptor::STEP_CONTINUE) {
                break;
            }   
        }   



线上地址

    define('APP_PATH', "/home/www/releases/v2/20140813_03/app-aifang-newsales/");
    define('SYS_PATH', APP_PATH."../../../system/20140813_03/");
    $G_CONF_PATH = array(
        SYS_EXT_PATH."config/",
        APP_PATH . "../../../app-3rd/app-faq-shared/".FAQ_SHARED_VERSION."/config/",//new
        "/home/www/config/v2/faq-shared/config/",
        APP_PATH . "../../../app-3rd/app-member-public/".MEMBER_PUBLIC_VERSION."/config/",//new
        APP_PATH . "../../../app-3rd/app-common/".COMMON_VERSION."/config/",//new

        APP_PATH."../app-aifang-core/config/",
        /home/www/releases/v2/20140813_03/app-aifang-newsales/config/",
        "/home/www/config/v2/member-share/config/",
        "/home/www/config/v2/aifang-core/config/",
        "/home/www/config/v2/aifang-newsales/config/",
        "/home/www/config/v2/local/config/",
    );
