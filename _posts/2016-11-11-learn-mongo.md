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


<table border="1" class="docutils">
<colgroup>
<col width="50%">
<col width="50%">
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">SQL Terms/Concepts</th>
<th class="head">MongoDB Terms/Concepts</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td>database</td>
<td><a class="reference internal" ><em class="xref std std-term">database</em></a></td>
</tr>
<tr class="row-odd"><td>table</td>
<td><a class="reference internal" ><em class="xref std std-term">collection</em></a></td>
</tr>
<tr class="row-even"><td>row</td>
<td><a class="reference internal" ><em class="xref std std-term">document</em></a> or <a class="reference internal" href="../glossary/#term-bson"><em class="xref std std-term">BSON</em></a> document</td>
</tr>
<tr class="row-odd"><td>column</td>
<td><a class="reference internal" ><em class="xref std std-term">field</em></a></td>
</tr>
<tr class="row-even"><td>index</td>
<td><a class="reference internal"><em class="xref std std-term">index</em></a></td>
</tr>
<tr class="row-odd"><td>table joins</td>
<td>embedded documents and linking</td>
</tr>
<tr class="row-even"><td><p class="first">primary key</p>
<p class="last">Specify any unique column or column combination as primary
key.</p>
</td>
<td><p class="first"><a class="reference internal"><em class="xref std std-term">primary key</em></a></p>
<p class="last">In MongoDB, the primary key is automatically set to the
<a class="reference internal" ><em class="xref std std-term">_id</em></a> field.</p>
</td>
</tr>
<tr class="row-odd"><td>aggregation (e.g. group by)</td>
<td><p class="first">aggregation pipeline</p>
</td>
</tr>
</tbody>
</table>

<table border="1" class="docutils">
<colgroup>
<col width="50%">
<col width="50%">
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">SQL Terms, Functions, and Concepts</th>
<th class="head">MongoDB Aggregation Operators</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td>WHERE</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match" title="$match"><tt class="xref mongodb mongodb-pipeline docutils literal"><span class="pre">$match</span></tt></a></td>
</tr>
<tr class="row-odd"><td>GROUP BY</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/group/#pipe._S_group" title="$group"><tt class="xref mongodb mongodb-pipeline docutils literal"><span class="pre">$group</span></tt></a></td>
</tr>
<tr class="row-even"><td>HAVING</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/match/#pipe._S_match" title="$match"><tt class="xref mongodb mongodb-pipeline docutils literal"><span class="pre">$match</span></tt></a></td>
</tr>
<tr class="row-odd"><td>SELECT</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/project/#pipe._S_project" title="$project"><tt class="xref mongodb mongodb-pipeline docutils literal"><span class="pre">$project</span></tt></a></td>
</tr>
<tr class="row-even"><td>ORDER BY</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/sort/#pipe._S_sort" title="$sort"><tt class="xref mongodb mongodb-pipeline docutils literal"><span class="pre">$sort</span></tt></a></td>
</tr>
<tr class="row-odd"><td>LIMIT</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/limit/#pipe._S_limit" title="$limit"><tt class="xref mongodb mongodb-pipeline docutils literal"><span class="pre">$limit</span></tt></a></td>
</tr>
<tr class="row-even"><td>SUM()</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/sum/#grp._S_sum" title="$sum"><tt class="xref mongodb mongodb-group docutils literal"><span class="pre">$sum</span></tt></a></td>
</tr>
<tr class="row-odd"><td>COUNT()</td>
<td><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/sum/#grp._S_sum" title="$sum"><tt class="xref mongodb mongodb-group docutils literal"><span class="pre">$sum</span></tt></a></td>
</tr>
<tr class="row-even"><td>join</td>
<td><p class="first"><a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup" title="$lookup"><tt class="xref mongodb mongodb-pipeline docutils literal"><span class="pre">$lookup</span></tt></a></p>
<div class="last versionadded">
<p><span class="versionmodified">New in version 3.2.</span></p>
</div>
</td>
</tr>
</tbody>
</table>



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


### 权限控制 ###  




