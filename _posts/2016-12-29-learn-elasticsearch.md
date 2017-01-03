---
layout: wp
title: Elasticsearch Reference 
---


这是一篇对ES的[参考](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html)的翻译笔记。  



### 简介  ### 

ES(Elasticsearch)的应用场景：  

* 站内搜索。比如文章内容搜索，商品搜索。   

* 日志(数据)收集、聚合、分析。   

* 数据可视化。   


名词解释：   

Cluster: 集群，集群名称标识一群服务器。由一个或多个Node组成。     
Node: 节点。ES启动时创建，可配置节点名称，否则自动生成UUID。一个服务器。     
Index: 索引。文档的特征值,用来标识一份文档。    
Type:索引的类别，比如：blog,comment,users.     
Document:全文搜索内容。  
  

>MySQL => Databases => Tables => Columns/Rows   
>Elasticsearch => Indices => Types => Documents with Properties



### Install ###  

install java:  

```
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz"
tar xvf jdk-8u111-linux-x64.tar.gz 
```
add JDK PATH TO system path, then  


install es:   

```
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.1.1.tar.gz
tar xvf elasticsearch-5.1.1.tar.gz

```

我本机配置不高，因此更改了ES的启动配置(config/jvm.options).   

启动后是这样的：   


```
 /home/work/app/jdk1.8.0_111/bin/java -Xms128m -Xmx128m -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -server -Xss512k -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djna.nosys=true -Djdk.io.permissionsUseCanonicalPath=true -Dio.netty.noUnsafe=true -Dio.netty.noKeySetOptimization=true -Dlog4j.shutdownHookEnabled=false -Dlog4j2.disable.jmx=true -Dlog4j.skipJansi=true -XX:+HeapDumpOnOutOfMemoryError -Des.path.home=/home/work/app/elasticsearch-5.1.1 -cp /home/work/app/elasticsearch-5.1.1/lib/elasticsearch-5.1.1.jar:/home/work/app/elasticsearch-5.1.1/lib/* org.elasticsearch.bootstrap.Elasticsearch
```




start es:  

```
nohup /bin/bash bin/elasticsearch &
```

或者：   

```
bin/elasticsearch -d -p pid
```


### 使用(Operate) ###   

ES使用rest api形式进行操作，URI格式为：

```
<REST Verb> /<Index>/<Type>/<ID>
```

#### 创建索引(Index) ####  

创建Index,named:customer：  

```
curl -X PUT "http://localhost:9200/customer?pretty"
```

查看Index:

```
curl -X GET "http://localhost:9200/_cat/indices?v"
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer X-f7oBY8S1aiJ3TnrDJS_A   5   1          0            0       650b           650b
```

删除Index:   

```
curl -X DELETE "http://localhost:9200/customer?pretty"
查看一下：
curl -X GET "http://localhost:9200/_cat/indices?v"
```

#### 添加数据(Document) ####

向ES中插入(灌入)数据：   


```
curl -X PUT "http://localhost:9200/customer/external/1?pretty" -d  '{"name": "John Doe"}'
```
其中，customer:是索引(相当于DB），external是索引的类型(type)   
返回：  

```
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "created" : true
}
```
_index:索引名称,_type:类型。_version:版本号。
_shards:索引分片。total:分片个数。successful:成功设置的索引分片个数。  





查看一下：

```
curl -X GET "http://localhost:9200/customer/external/1?pretty"
{
_index: "customer",
_type: "external",
_id: "1",
_version: 1,
found: true,
_source: {
name: "John Doe"
}
}
```

_source:document内容。

其中`_source`是document的内容。   

可以直接返回_source:   

```
 curl -X GET "http://localhost:9200/customer/external/1/_source?pretty"
```


如果不指定ID，使用随机生成的ID:  

```
curl -X POST "http://localhost:9200/customer/external?pretty" -d  '{"name": "John Doe"}'  
{
  "_index" : "customer",
  "_type" : "external",
  "_id" : "AVlOjPGICfqnGv8WJkuC",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "created" : true
}
```

更新数据：  

```
curl -X POST "http://localhost:9200/customer/external/1/_update?pretty" -d '{"doc": {"name": "John Doe"}}'
```

更新数据支持简单的`script`,  

```
 curl -X POST "http://localhost:9200/customer/external/1/_update?pretty" -d '{"script": "ctx._source.age +=5"}'
```
这里"ctx"为doc,ctx._source为doc中的_source字段。  
结果：age增加了5    

注意：`PUT`方法将更新整个DOC，而`POST`+`_udpate`只对DOC中的相应字段进行更新。   

删除数据：  

```
curl -XDELETE  "http://localhost:9200/customer/external/1" 
```





#### 批量操作 ####  

```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty&pretty' -d'
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }'
```

查询：  

```
curl 'localhost:9200/bank/account/_mget?pretty' -d '{
    "docs" : [
        {
            "_id" : "1"
        },
        {
            "_id" : "2"
        }
    ]
}'

```

或者：  

```
curl 'localhost:9200/_mget?pretty' -d '{
    "docs" : [
        {
            "_index" : "bank",
            "_type": "account",
            "_id" : "1"
        },
        {
            "_index" : "bank",
            "_type": "account",
            "_id" : "2"
        }
    ]
}'
```





#### 搜索 ####  


先灌入一些数据：  

```
wget https://raw.githubusercontent.com/elastic/elasticsearch/master/docs/src/test/resources/accounts.json
 curl -XPOST 'localhost:9200/bank/account/_bulk?pretty&refresh' --data-binary "@accounts.json"
```

index: bank, type: account   

搜索API支持两种方式，一种rest request uri，即将参数写如URI中；另一种，将参数写入BODY中(rest request body)   


```
 curl -XGET 'localhost:9200/bank/_search?q=*&sort=account_number:asc&pretty&pretty'
```

Or:

```
curl -XGET 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" }
  ]
}'
```

结果：

```
{
  "took" : 11,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : null,
    "hits" : [
      {
        "_index" : "bank",
        "_type" : "account",
        "_id" : "0",
        "_score" : null,
        "_source" : {
          "account_number" : 0,
          "balance" : 16623,
          "firstname" : "Bradshaw",
          "lastname" : "Mckenzie",
          "age" : 29,
          "gender" : "F",
          "address" : "244 Columbus Place",
          "employer" : "Euron",
          "email" : "bradshawmckenzie@euron.com",
          "city" : "Hobucken",
          "state" : "CO"
        },
        "sort" : [
          0
        ]
      },
    ...
    }
}
```

其中：hits为搜索结果，total:结果总数，hits.hits:搜索结果内容，默认显示10条，sort,排序方式，_score.   


####  搜索/查询 ####  


```
curl -XGET 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" },
    {"balance": { "order": "desc" }}
  ],
  "from": 0,
  "size" : 10
}'
```


```
curl -XGET 'localhost:9200/bank/_search?pretty' -d'
{
  "query": { 
    "bool" : {
        "must" : [
            { "match" : { "firstname" : "Opal"}}
        ],
        "filter" : [
            { "range" : { "age" : { "gte" :  30 } }}
        ]
    }
  },
  "sort": [
    { "account_number": "asc" },
    {"balance": { "order": "desc" }}
  ],
  "from": 0,
  "size" : 10,
  "_source" : [
    "firstname", "age"
  ]
}'

```

上面的将匹配firstname 为Opal，age>=30的人，并只显示firstname,age两个字段。  


注意，这里的匹配是单词匹配。即，加入firstname是"Opalxxx"是不能匹配的，如果是"Opal xxx"是可以匹配的。   

bool must是and match，bool should是or match.  


#### [搜索聚合](https://www.elastic.co/guide/en/elasticsearch/reference/current/_executing_aggregations.html) ####  



```
curl -XGET 'localhost:9200/bank/_search?pretty' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}'

```



site: 0 标识只显示聚合内容，不显示搜索的结果。  


用SQL标识即：  

```
SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC
```

结果：

```
{
  "took" : 83,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "doc_count_error_upper_bound" : 20,
      "sum_other_doc_count" : 770,
      "buckets" : [
        {
          "key" : "ID",
          "doc_count" : 27
        },
        {
          "key" : "TX",
          "doc_count" : 27
        },
        ...
      ]
    }
  }
}
```


其中： hits.total: 是总的检索的数量。
aggregations:是聚合的数量。  


需求：求20-30，30-40，40-50年龄段的人数，并显示每个性别的人数。  

```
curl -XGET 'localhost:9200/bank/_search?pretty' -d'
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender.keyword"
          },
          "aggs": {
            "average_balance": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
      }
    }
  }
}'

```

结果：

```
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_age" : {
      "buckets" : [
        {
          "key" : "20.0-30.0",
          "from" : 20.0,
          "to" : 30.0,
          "doc_count" : 451,
          "group_by_gender" : {
            "doc_count_error_upper_bound" : 0,
            "sum_other_doc_count" : 0,
            "buckets" : [
              {
                "key" : "M",
                "doc_count" : 232,
                "average_balance" : {
                  "value" : 27374.05172413793
                }
              },
              {
                "key" : "F",
                "doc_count" : 219,
                "average_balance" : {
                  "value" : 25341.260273972603
                }
              }
            ]
          }
        },
        {
          "key" : "30.0-40.0",
          "from" : 30.0,
          "to" : 40.0,
          "doc_count" : 504,
          "group_by_gender" : {
            "doc_count_error_upper_bound" : 0,
            "sum_other_doc_count" : 0,
            "buckets" : [
              {
                "key" : "F",
                "doc_count" : 253,
                "average_balance" : {
                  "value" : 25670.869565217392
                }
              },
              {
                "key" : "M",
                "doc_count" : 251,
                "average_balance" : {
                  "value" : 24288.239043824702
                }
              }
            ]
          }
        },
        {
          "key" : "40.0-50.0",
          "from" : 40.0,
          "to" : 50.0,
          "doc_count" : 45,
          "group_by_gender" : {
            "doc_count_error_upper_bound" : 0,
            "sum_other_doc_count" : 0,
            "buckets" : [
              {
                "key" : "M",
                "doc_count" : 24,
                "average_balance" : {
                  "value" : 26474.958333333332
                }
              },
              {
                "key" : "F",
                "doc_count" : 21,
                "average_balance" : {
                  "value" : 27992.571428571428
                }
              }
            ]
          }
        }
      ]
    }
  }
}
```

是不是很强大..  


### 聚合2 ###   

#### Buckets #### 

符合条件的文档集合。

#### Metrics #### 

基于Buckets进行统计分析的指标。eg: count, avg, sum,etc.   

eg:

求平均数年龄：  

```
curl -XGET "localhost:9200/bank/_search?pretty" -d '{
    "size" : 0,
    "aggs" : {
        "avg_age" : { "avg" : { "field" : "age"}}
    }   
}'

```

##### Cardinality(基数) Aggregation #####    


如果fiel 如果field是text先设置fielddata为ture:   

```
curl -XPUT 'localhost:9200/bank/_mapping/account?pretty' -d'
{
    "properties": {
        "firstname": {
            "type":     "text",
            "fielddata": true
        }
    }
}'

```

否则，会报错：  

```
"Fielddata is disabled on text fields by default. Set fielddata=true
```

>
>Most fields can use index-time, on-disk doc_values for this data access pattern, but text fields do not support doc_values.
>   
>Instead, text fields use a query-time in-memory data structure called fielddata. This data structure is built on demand the first time that a field is used for aggregations, sorting, or in a script. It is built by reading the entire inverted index for each segment from disk, inverting the term ↔︎ document relationship, and storing the result in memory, in the JVM heap.
>


好，我们统计，用户个数：  

```
curl -XGET "localhost:9200/bank/_search?pretty" -d '{
    "size" : 0,
    "aggs" : {
        "customer_count": {
            "cardinality": {"field": "firstname"}
        }
    }
}'
```


将返回聚合结果：  

```
"aggregations" : {
    "customer_count" : {
      "value" : 993
    }
  }
```

#####  stat aggregation #####   

统计年龄分布：  

```
curl -XGET "localhost:9200/bank/account/_search?pretty" -d '{
    "size" : 0,
    "aggs" : {
        "age_stat" : {
            "stats" : { "field" : "age"}
        }
    }
}'
```

将返回数量，min,max,avg,sum的聚合结果。  



##### extended stat aggregation #####   


统计年龄分布：  

```
curl -XGET "localhost:9200/bank/account/_search?pretty" -d '{
    "size" : 0,
    "aggs" : {
        "age_stats" : {
                "extended_stats" : { "field" : "age"}
        }
    }
}'
```

最大/小aggregation,eg:最大/小年龄：  

```
curl -XGET "localhost:9200/bank/account/_search?pretty" -d '{
    "size" : 0,
    "aggs" : {
        "max_ages" : {
            "max" : { "field" : "age"}
        }
    }
}'
```

max->min   


##### percentiles aggregation #####   

百分位统计，比如统计年龄分布：  

```
curl -XGET "localhost:9200/bank/account/_search?pretty" -d '{
    "size" : 0,
    "aggs" : {
        "percentiles_ages" : {
            "percentiles" : { "field" : "age"}
        }
    }
}'
```

结果返回： 

```
  "aggregations" : {
    "percentiles_ages" : {
      "values" : {
        "1.0" : 20.0,
        "5.0" : 21.0,
        "25.0" : 25.000000000000004,
        "50.0" : 30.999999999999996,
        "75.0" : 35.0,
        "95.0" : 39.0,
        "99.0" : 40.0
      }
    }
  }
```

即，20岁以下站1%，21岁以下占比5%,25岁以下占比25%,,,,    

可以指定value,来获取percentiles:   

```
curl -XGET "localhost:9200/bank/account/_search?pretty" -d '{
    "size" : 0,
    "aggs" : {
        "percentiles_ages" : {
            "percentile_ranks" : { "field" : "age", "values":[20,30,39]}
        }
    }
}'

```

将返回20，30，39岁以内的占比。  



聚合的查询结构：  


```
"aggregations" : {
    "<aggregation_name>" : {
        "<aggregation_type>" : {
            <aggregation_body>
        }
        [,"meta" : {  [<meta_data_body>] } ]?
        [,"aggregations" : { [<sub_aggregation>]+ } ]?
    }
    [,"<aggregation_name_2>" : { ... } ]*
}
```

#### Bucket Aggregations #### 

桶聚合不像metrics聚合那样计算指标的个数，它将document放到桶当中。每一个桶满足一定条件。


### 参考资料 ###  


[https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html)  
[https://www.elastic.co/blog/what-is-an-elasticsearch-index](https://www.elastic.co/blog/what-is-an-elasticsearch-index)  

