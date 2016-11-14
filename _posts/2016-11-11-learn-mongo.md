---
layout: wp
title: learn about mongo
---


### 安装与组成 ###  


使用`yum install mongodb-org`安装后，会将mongos,mongo tools,mongo shell,mongo server全部安装：   

* mongos: mongo的分片服务器，可以理解为mongo的proxy   
* mongod: mongo的服务器端，对DB进行操作   
* mongo: mongo的客户端工具   
* mongo tools: 包括bsondump,mongoexport,mongoimport,mongoperf,mongostat等工具。   

### 存储与引擎 ###   

<table class="table table-bordered">
  <tbody><tr>
    <th>RDBMS</th>
    <th>MongoDB</th>
  </tr>
  <tr>
    <td>Table</td>
    <td>Collection</td>
  </tr>
  <tr>
    <td>Column</td>
    <td>Key</td>
  </tr>
  <tr>
    <td>Value</td>
    <td>Value</td>
  </tr>
  <tr>
    <td>Records / Rows</td>
    <td>Document / Object</td>
  </tr>
</tbody></table>

mongo3.2.10的默认存储引擎是,wiredtiger.之前的存储引擎是MMAPV1.  

它包含三种[存储引擎](https://docs.mongodb.com/v3.2/core/storage-engines/):  

* WiredTiger   
* MMAPv1  
* In-Memory Storage   


### 概念 ###   

![image](http://www.w3resource.com/mongodb/mongodb-document-collection.png)  


#### Dcoments ####  

document是mongo的存储单元，即一条存储记录。不同与传统的RDBMS,它不是一个一个个单列组成的，而是一个JSON对象。   

>A record in a MongoDB collection and the basic unit of data in MongoDB. Documents are analogous to JSON objects but exist in the database in a more type-rich format known as BSON. See Documents.


```
[
  {
    "_id": {
      "$oid": "58255eed6729d0068b8b1de8"
    },
    "address": {
      "building": "469",
      "coord": [
        -73.961704,
        40.662942
      ],
      "street": "Flatbush Avenue",
      "zipcode": "11225"
    },
    "borough": "Brooklyn",
    "cuisine": "Hamburgers",
    "grades": [
      {
        "date": {
          "$date": "2014-12-30T00:00:00.000Z"
        },
        "grade": "A",
        "score": 8
      },
      {
        "date": {
          "$date": "2014-07-01T00:00:00.000Z"
        },
        "grade": "B",
        "score": 23
      },
      {
        "date": {
          "$date": "2013-04-30T00:00:00.000Z"
        },
        "grade": "A",
        "score": 12
      },
      {
        "date": {
          "$date": "2012-05-08T00:00:00.000Z"
        },
        "grade": "A",
        "score": 12
      }
    ],
    "name": "Wendy'S",
    "restaurant_id": "30112340"
  }
]
```

#### Collection ####  

记录的集合。

>A grouping of MongoDB documents. A collection is the equivalent of an RDBMS table. A collection exists within a single database. Collections do not enforce a schema. Documents within a collection can have different fields. Typically, all documents in a collection have a similar or related purpose. See Namespaces.   



![image](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-insert.png)   
![image](https://docs.mongodb.com/manual/_images/crud-annotated-mongodb-find.png)  



### Aggregation ###  


