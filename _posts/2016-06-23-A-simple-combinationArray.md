---
layout: wp
title: 数组排列组合PHP版本
---

在我们电商平台上，有一种商品叫做套装，即多个商品打包进行售卖，套装分为几个组：  
组1：a,b,c商品  
组2: i,j,k商品  

用户购买的时候，可以从每个组选择一个商品，这样，这个商品就有以下组合:
ai, aj, ak, bi, bj, bk, ci, cj, ck  

即需要用PHP来实现一个数组的组合算法。我用递归的方式实现如下:   

```
function combinationArray($arr){
                //get value
        $len = count($arr);
        $ret = array();
        if($len<=1){
        	foreach($arr as $key=>$value){
            	//if value is array,get 
                if(is_array($value)){
                        foreach($value as $v){
                                $ret[] = array($v);
                        }
                }else{
                        $ret[] = array($value);
                }
         }
         return $ret;
        }
        $first = array_shift($arr);
        $tmp = combinationArray($arr);
        foreach ($first as  $value){
            //针对剩下的数组做组合排列，返回一个数组
            foreach($tmp as $item){
                    array_push($item, $value);
                    $ret[] = $item;
            }
        }
        return $ret;
}

$arr = array(
        array(1=>array('haha'),2=>array('dd')),
        array('a','b'),
);
$result = combinationArray($arr);
var_dump($result);
```  


