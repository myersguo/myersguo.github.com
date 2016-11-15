---
layout: wp
title: mysql & mongo aggregate
---

假如我们的APP CRASH数据是这样的：  

```
Create Table: CREATE TABLE `t_crash` (
  `crash_id` int(10) NOT NULL AUTO_INCREMENT COMMENT 'CRASH ID',
  `app_id` varchar(255) NOT NULL COMMENT 'APP ID',
  `app_key` varchar(255) NOT NULL COMMENT 'APP KEY',
  `device_uuid` varchar(255) DEFAULT NULL COMMENT 'DEVICE UUID',
  `device_model` varchar(255) DEFAULT NULL COMMENT '手机型号',
  `app_version` varchar(255) DEFAULT NULL COMMENT 'APP版本',
  `osVersion` varchar(255) DEFAULT NULL COMMENT '操作系统版本',
  `app_channel` varchar(255) DEFAULT NULL COMMENT 'APP渠道号',
  `app_start_time` int(10) DEFAULT NULL COMMENT 'APP启动',
  `app_crash_time` int(10) DEFAULT NULL COMMENT 'CRASH发生时间',
  `crash_exception_type` varchar(255) DEFAULT NULL COMMENT 'crash类别',
  `crash_exception_desc` text COMMENT 'CRASH堆栈',
  `crash_callstack` text COMMENT '完整的错误栈',
  PRIMARY KEY (`crash_id`),
  KEY `ix_device_model` (`device_model`)
) ENGINE=InnoDB AUTO_INCREMENT=34768 DEFAULT CHARSET=utf8 COMMENT='crash日志'
```

我们看个例子：  

```
*************************** 1. row ***************************
            crash_id: 18768
              app_id: 1
             app_key: 1
         device_uuid: MI3
        device_model: MI3
         app_version: 1.0
           osVersion: Android 2.3.3,level 10
         app_channel: 1
      app_start_time: 1478770730
      app_crash_time: 1478770730
crash_exception_type: A
crash_exception_desc: a
     crash_callstack: NULL
````

我们用MYSQL来统计：   

#### 统计设备TOP5 #### 


```
SELECT COUNT(*) as number,device_model FROM xmapp_crash 
GROUP BY device_model
ORDER BY number desc 
```

#### 统计CRASH的48小时内每小时的数据  ####

```
SELECT count(*) as number,DATE_FORMAT(FROM_UNIXTIME(app_crash_time), '%Y-%m-%d %H') as t
FROM xmapp_crash
WHERE app_crash_time>UNIX_TIMESTAMP()-3600*48
GROUP BY DATE_FORMAT(FROM_UNIXTIME(app_crash_time), '%Y-%m-%d %H')
ORDER BY t desc
```

#### 统计一个月内的数据 ####


```
SELECT count(*) as number,DATE_FORMAT(FROM_UNIXTIME(app_crash_time), '%Y-%m-%d') as t
FROM xmapp_crash
WHERE app_crash_time>UNIX_TIMESTAMP()-3600*24*30
GROUP BY DATE_FORMAT(FROM_UNIXTIME(app_crash_time), '%Y-%m-%d')
ORDER BY t desc
```

现在我们把数据导入到mongo:  

```
<?php

$connectionString = 'mysql:host=localhost;port=3306;dbname=mydb';
$username='root';
$password='root';
$db = new PDO($connectionString, $username, $password);
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
$sql = 'SELECT * FROM t_crash';
$statement=$db->prepare($sql);
$statement->execute();
$results=$statement->fetchAll(PDO::FETCH_ASSOC);

$m = new MongoClient("mongodb://localhost:27017");
$m_db = $m->test;
foreach($results as $key=>$result) {
    $m_db->app_crash->insert($result);
}

```

数据查看一下：   


```
db.app_crash.findOne()
{
        "_id" : ObjectId("582af43ca97a17601b52089a"),
        "crash_id" : "18768",
        "app_id" : "1",
        "app_key" : "1",
        "device_uuid" : "MI3",
        "device_model" : "MI3",
        "app_version" : "1.0",
        "osVersion" : "Android 2.3.3,level 10",
        "app_channel" : "1",
        "app_start_time" : "1478770730",
        "app_crash_time" : "1478770730",
        "crash_exception_type" : "A",
        "crash_exception_desc" : "a",
        "crash_callstack" : null
}
```

我们来做统计：   


####  统计设备TOP5 ####


```
db.crash.aggregate( [     { $group: { _id: "$device_model", number: { $sum: 1} } },     { $sort: {"number": -1 }} ])
```

使用mapreduce

```
db.crash.mapReduce(
    function() { emit( this.app_version, 1) },
    function(key, values) { return Array.sum(values) },
    {
        out: "crash_version"
    }
)
```

#如果使用单独的group功能

```
db.crash.group({
    key: {device_model: 1},
    reduce: function(curr, result){
        result.total += 1;
    },
    initial: {total : 0}
})

```


#### 统计CRASH的时间曲线，48小时内每小时 ####

```
db.crash.mapReduce(
    function() { 
      if(this.app_crash_time*1000 >= (new Date().getTime()-172800)){
        var date = new Date(this.app_crash_time*1000);
        var dateKey = date.getFullYear()+'-' + (date.getMonth()+1)+"-"+date.getDate()+"-"+date.getHours();
        emit( dateKey, 1);
      }
    },
    function(key, values) { return Array.sum(values) },
    {
        out: "crash_version"
    }
)
```


#### 统计一个月内，机型CRASH的分布 ####

```
db.crash.mapReduce(
    function() { 
      if(this.app_crash_time*1000 >= (new Date().getTime()-2592000)){
        var date = new Date(this.app_crash_time*1000);
        var dateKey = date.getFullYear()+'-' + (date.getMonth()+1)+"-"+date.getDate();
        emit( dateKey, 1);
      }
    },
    function(key, values) { return Array.sum(values) },
    {
        out: "crash_version"
    }
)
```

(完) 


