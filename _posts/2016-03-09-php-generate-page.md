---
layout: wp
title: PHP分页生成器
comments: true
---  

一个简单的PHP分页函数

<pre>
<?php
    class Common {
         
        //分页
        //author: myersguo 
        //Date: 2016.03.09
        public static function generatePage ($cur, $total , $url) {
            if($total < 1)return;
            $shownumber = 5;
            $left = 1;
            $right = $total;

            $left = $cur - $shownumber + 1;
            $right = $cur + $shownumber -1;
            if($left <=0 ) {
                $right = $right - $left + 1;
                $left = 1;
            }
            if($right > $total) {
                $right = $total;
                $left = $left + $total - $cur - $shownumber;
            }
            if($left <=0) {
                $left =1;
            }

            $first = '';
            $last = '';
            $tmp = '';
            for($i = $left ; $i<=$right; $i++) {
                $class = 'number';
                if($cur == $i) {
                    $class = $class . ' current';
                }
                if($i == 1) {
                    $class = $class .' first';
                }
                if($i == $total) {
                    $class = $class . ' last';
                }
                $tmp = $tmp.'<li class="'. $class .'"><a>'.$i.'</a></li>';
            }
            if($left != 1) {
                 $first = '<li class="number first"><a>1</a></li>';
            }
            if($right != $total) {
                $last = '<li class="number last"><a>'.$total.'</a></li>';
            }
            $pagestr =  '<ul class="pagenav">'. $first .$tmp . $last. '</ul>';
            return $pagestr;

        }

    }

</pre>











