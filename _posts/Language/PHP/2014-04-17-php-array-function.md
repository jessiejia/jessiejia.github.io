---
layout: post
title: 多维数组操作
category: 语言 
tags: php
---


#### multi-array操作

    <?php
    class Util_Array {
        /**
         * 二维数组按照某列排序
         * @param $multi_array
         * @param $sort_key
         * @param int $sort
         * @return array|bool
         */
        public static function multi_array_sort($multi_array,$sort_key,$sort=SORT_ASC){
            if(is_array($multi_array)){
                foreach ($multi_array as $row_array){
                    if(is_array($row_array)){
                        $key_array[] = $row_array[$sort_key];
                    }else{
                        return false;
                    }
                }
            }else{
                return false;
            }
            array_multisort($key_array,$sort,$multi_array);
            return $multi_array;
        }

        /**
         * 多维数组或对象数组，取其pk作为数组key
         */
        public function multi_array_set_pk_as_key($multi_array, $key){
            if(empty($multi_array) || empty($key) || !is_array($multi_array)) return $multi_array;
            $ret_array = array();
            $current = current($multi_array);
            if(is_array($current) && isset($current[$key])){
                foreach($multi_array as $v){
                    $ret_array[$v[$key]] = $v;
                }
            }elseif(is_object($current) && isset($current->$key)){
                foreach($multi_array as $v){
                    $ret_array[$v->$key] = $v;
                }
            }else{
                return $multi_array;
            }
            return $ret_array;
        }

        /**
         * 二维数组提取某字段
         */
        public static function multi_array_get_column($multi_array, $val='name',$key=''){
            $tmp = array();
            if(empty($key)){
                foreach($multi_array as $k=>$v){
                    $tmp[$k] = $v[$val];
                }
            }else{
                foreach($multi_array as $v){
                    $tmp[$v[$key]] = $v[$val];
                }
            }
            return $tmp;
        }
    }

