---
layout: post
title: php-function杂
category: 语言 
tags: php
---


###### set_exception_handler

设置默认的异常处理程序，用于没有用 try/catch 块来捕获的异常。 在 exception_handler 调用后异常会中止。代码中止
    
    function exception_handler($exception) {
      echo "Uncaught exception: " , $exception->getMessage(), "\n";
        $trace = format_trace($exception->getTrace());
        //$trace = "exception '"
        //    . get_class($exception) . "' with message '"
        //    . $exception->getMessage()."' in "
        //    . $exception->getFile() . ":" 
        //    . $exception->getLine() . " Stack trace: " . $trace;
        //echo $trac;
    }
    
    set_exception_handler('exception_handler');
    
    throw new Exception('Uncaught Exception');
