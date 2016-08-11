---
layout: wp
title: PHP 数组搜索  
---

今天写了个数组搜索的函数，用递归方法，感觉应该有更高大上的方法，自己写的有点LOW，分享到这里：  

1. 数组搜索  

```
/**
     * 由key搜索数组,加入找到数组，则返回整个数组
     * @param  [type] $arr     [description]
     * @param  [type] $keyword [description]
     * @return [type]          [description]
     */
    public static function array_search_by_key($arr, $keyword, $deep=3){
        $result = array();
        if(!is_array($arr) || empty($arr))return $result;

        foreach($arr as $key => $value){
            if(strpos($key, $keyword) !== false) {
                $result[$key] = $value;
            }else {
                if(is_array($value) && array_search($keyword, $value, true) !== false){
                    $result[$key] = $value;
                }else if(is_string($value) && strpos($value, $keyword)!== false) {
                    $result[$key] = $value;
                } else if($deep>1){
                    $checked = self::array_search_by_key($value, $keyword, $deep-1);
                    if(!empty($checked)){
                        $result[$key] = $value;
                    }
                }
            }
        }
        return $result;
    }

```


