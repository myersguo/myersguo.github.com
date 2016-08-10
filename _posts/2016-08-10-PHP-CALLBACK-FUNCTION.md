---
layout: wp
title: PHP回调函数  
---
之前写的一个HTTP请求的方法：  

```
 static function httpRequest($url, $post_data = null, $timeout = 1) {
        if (!preg_match('!^\w+://! i', $url)) {
            $url = 'http://' . $url;
        }
        if(empty($url))
            return;
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        if(!empty($post_data) && isset($post_data['options'])) {
            foreach($post_data['options'] as $key=>$value){
                eval("\$opkey = $key;");
                curl_setopt($ch, $opkey, $value);
            }       
        }           
        if(!empty($post_data) && isset($post_data['data'])) {
            curl_setopt($ch, CURLOPT_POST, true);
            $filed = is_array($post_data['data']) ? http_build_query($post_data['data']) : $post_data['data'];
            curl_setopt($ch, CURLOPT_POSTFIELDS, $filed);
        }  
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, CURLOPT_TIMEOUT, $timeout);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);    
        $data = curl_exec($ch);
        if($data === false || 200 != curl_getinfo($ch, CURLINFO_HTTP_CODE)  ){
            Yii::log('curl:error:'. $url, ':http code'.curl_getinfo($ch, CURLINFO_HTTP_CODE));
        }
        curl_close($ch);
        return $data;
    }
```

这里curl请求之后直接关闭了连接，对资源和返回做更多处理就无法满足；因此想要扩展一下这个函数，让它支持自定义回调。将$ch和$data传给回掉函数，使其在关闭前执行。更改如下：   


```
增加callback参数.
在curl_close之前：

if($callback && is_callable($callback)) {
    call_user_func($callback,$ch, $data);
}
```

调用的时候：   

```
$result = Common::httpRequest($url, $post_data, 10, function($ch, $data) {
    ....
});
```

完。  

