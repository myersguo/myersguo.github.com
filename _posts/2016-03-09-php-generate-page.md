---
layout: wp
title: PHP分页生成器
comments: true
---  

一个简单的PHP分页函数

````
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
            if($cur- 1 <= 0) {
                 $first =  '<li class="number  prevpage"><a class="nopointercursor"><</a></li>';
            }else {
                 $first =  '<li class="number  prevpage"><a class="pointercursor" href="'.$url.'page='.($cur-1).'"><</a></li>';
            }
            
            if($cur == $total) {
                $last =  '<li class="number  nextpage"><a class="nopointercursor">></a></li>';
            }else {
                 $last =  '<li class="number  nextpage"><a class="pointercursor" href="'.$url.'page='.($cur+1).'">></a></li>';
            }
            
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
                $href = $url . 'page='. $i;
                $tmp = $tmp.'<li class="'. $class .'"><a href="'. $href .'">'.$i.'</a></li>';
            }
            if($left != 1) {
                 $first = $first . '<li class="number first"><a>1</a></li>';
            }
            if($right != $total) {
                $last = '<li class="number last"><a>'.$total.'</a></li>' . $last;
            }
            $pagestr =  '<ul class="pagenav">'. $first .$tmp . $last. '</ul>';
            return $pagestr;

        }

    }

````






