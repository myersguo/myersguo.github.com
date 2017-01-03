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

start es:  

```
nohup /bin/bash bin/elasticsearch &
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

其中`_source`是document的内容。   


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


#### 批量操作 ####  

```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty&pretty' -d'
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }'
```

....   


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




### 参考资料 ###  


[https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html](https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html)  
[https://www.elastic.co/blog/what-is-an-elasticsearch-index](https://www.elastic.co/blog/what-is-an-elasticsearch-index)  

