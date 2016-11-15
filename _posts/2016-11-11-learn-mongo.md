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

<table border="1" class="docutils">
<colgroup>
<col width="50%">
<col width="50%">
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">SQL Schema Statements</th>
<th class="head">MongoDB Schema Statements</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">CREATE</span> <span class="k">TABLE</span> <span class="n">users</span> <span class="p">(</span>
    <span class="n">id</span> <span class="n">MEDIUMINT</span> <span class="k">NOT</span> <span class="k">NULL</span>
        <span class="n">AUTO_INCREMENT</span><span class="p">,</span>
    <span class="n">user_id</span> <span class="nb">Varchar</span><span class="p">(</span><span class="mi">30</span><span class="p">),</span>
    <span class="n">age</span> <span class="nb">Number</span><span class="p">,</span>
    <span class="n">status</span> <span class="nb">char</span><span class="p">(</span><span class="mi">1</span><span class="p">),</span>
    <span class="k">PRIMARY</span> <span class="k">KEY</span> <span class="p">(</span><span class="n">id</span><span class="p">)</span>
<span class="p">)</span>
</pre></div>
</div>
</td>
<td><p class="first">Implicitly created on first <a class="reference internal" href="https://docs.mongodb.com/manual/reference/method/db.collection.insert/#db.collection.insert" title="db.collection.insert()"><tt class="xref mongodb mongodb-method docutils literal"><span class="pre">insert()</span></tt></a> operation. The primary key <tt class="docutils literal"><span class="pre">_id</span></tt>
is automatically added if <tt class="docutils literal"><span class="pre">_id</span></tt> field is not specified.</p>
<div class="highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">insert</span><span class="p">(</span> <span class="p">{</span>
</span><span class="hll">    <span class="nx">user_id</span><span class="o">:</span> <span class="s2">"abc123"</span><span class="p">,</span>
</span><span class="hll">    <span class="nx">age</span><span class="o">:</span> <span class="mi">55</span><span class="p">,</span>
</span><span class="hll">    <span class="nx">status</span><span class="o">:</span> <span class="s2">"A"</span>
</span><span class="hll"> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
<p>However, you can also explicitly create a collection:</p>
<div class="last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">createCollection</span><span class="p">(</span><span class="s2">"users"</span><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">ALTER</span> <span class="k">TABLE</span> <span class="n">users</span>
<span class="k">ADD</span> <span class="n">join_date</span> <span class="n">DATETIME</span>
</pre></div>
</div>
</td>
<td><p class="first">Collections do not describe or enforce the structure of its
documents; i.e. there is no structural alteration at the
collection level.</p>
<p>However, at the document level, <a class="reference internal" href="https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update" title="db.collection.update()"><tt class="xref mongodb mongodb-method docutils literal"><span class="pre">update()</span></tt></a> operations can add fields to existing
documents using the <a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/update/set/#up._S_set" title="$set"><tt class="xref mongodb mongodb-update docutils literal"><span class="pre">$set</span></tt></a> operator.</p>
<div class="last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">update</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="p">},</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">$set</span><span class="o">:</span> <span class="p">{</span> <span class="nx">join_date</span><span class="o">:</span> <span class="k">new</span> <span class="nb">Date</span><span class="p">()</span> <span class="p">}</span> <span class="p">},</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">multi</span><span class="o">:</span> <span class="kc">true</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">ALTER</span> <span class="k">TABLE</span> <span class="n">users</span>
<span class="k">DROP</span> <span class="k">COLUMN</span> <span class="n">join_date</span>
</pre></div>
</div>
</td>
<td><p class="first">Collections do not describe or enforce the structure of its
documents; i.e. there is no structural alteration at the collection
level.</p>
<p>However, at the document level, <a class="reference internal" href="https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update" title="db.collection.update()"><tt class="xref mongodb mongodb-method docutils literal"><span class="pre">update()</span></tt></a> operations can remove fields from
documents using the <a class="reference internal" href="https://docs.mongodb.com/manual/reference/operator/update/unset/#up._S_unset" title="$unset"><tt class="xref mongodb mongodb-update docutils literal"><span class="pre">$unset</span></tt></a> operator.</p>
<div class="last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">update</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="p">},</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">$unset</span><span class="o">:</span> <span class="p">{</span> <span class="nx">join_date</span><span class="o">:</span> <span class="s2">""</span> <span class="p">}</span> <span class="p">},</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">multi</span><span class="o">:</span> <span class="kc">true</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">CREATE</span> <span class="k">INDEX</span> <span class="n">idx_user_id_asc</span>
<span class="k">ON</span> <span class="n">users</span><span class="p">(</span><span class="n">user_id</span><span class="p">)</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">createIndex</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="mi">1</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">CREATE</span> <span class="k">INDEX</span>
       <span class="n">idx_user_id_asc_age_desc</span>
<span class="k">ON</span> <span class="n">users</span><span class="p">(</span><span class="n">user_id</span><span class="p">,</span> <span class="n">age</span> <span class="k">DESC</span><span class="p">)</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">createIndex</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="mi">1</span><span class="p">,</span> <span class="nx">age</span><span class="o">:</span> <span class="o">-</span><span class="mi">1</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">DROP</span> <span class="k">TABLE</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">drop</span><span class="p">()</span>
</span></pre></div>
</div>
</td>
</tr>
</tbody>
</table>

<table border="1" class="docutils">
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">SQL SELECT Statements</th>
<th class="head">MongoDB find() Statements</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">()</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="n">id</span><span class="p">,</span>
       <span class="n">user_id</span><span class="p">,</span>
       <span class="n">status</span>
<span class="k">FROM</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="p">},</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="mi">1</span><span class="p">,</span> <span class="nx">status</span><span class="o">:</span> <span class="mi">1</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="n">user_id</span><span class="p">,</span> <span class="n">status</span>
<span class="k">FROM</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="p">},</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="mi">1</span><span class="p">,</span> <span class="nx">status</span><span class="o">:</span> <span class="mi">1</span><span class="p">,</span> <span class="nx">_id</span><span class="o">:</span> <span class="mi">0</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="n">user_id</span><span class="p">,</span> <span class="n">status</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">},</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="mi">1</span><span class="p">,</span> <span class="nx">status</span><span class="o">:</span> <span class="mi">1</span><span class="p">,</span> <span class="nx">_id</span><span class="o">:</span> <span class="mi">0</span> <span class="p">}</span>
</span><span class="p">)</span>
</pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">!=</span> <span class="ss">&quot;A&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$ne</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">}</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
<span class="k">AND</span> <span class="n">age</span> <span class="o">=</span> <span class="mi">50</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span><span class="p">,</span>
</span><span class="hll">      <span class="nx">age</span><span class="o">:</span> <span class="mi">50</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
<span class="k">OR</span> <span class="n">age</span> <span class="o">=</span> <span class="mi">50</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">$or</span><span class="o">:</span> <span class="p">[</span> <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">}</span> <span class="p">,</span>
</span><span class="hll">             <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="mi">50</span> <span class="p">}</span> <span class="p">]</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">age</span> <span class="o">&gt;</span> <span class="mi">25</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">    <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$gt</span><span class="o">:</span> <span class="mi">25</span> <span class="p">}</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">age</span> <span class="o">&lt;</span> <span class="mi">25</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$lt</span><span class="o">:</span> <span class="mi">25</span> <span class="p">}</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">age</span> <span class="o">&gt;</span> <span class="mi">25</span>
<span class="k">AND</span>   <span class="n">age</span> <span class="o">&lt;=</span> <span class="mi">50</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$gt</span><span class="o">:</span> <span class="mi">25</span><span class="p">,</span> <span class="nx">$lte</span><span class="o">:</span> <span class="mi">50</span> <span class="p">}</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">user_id</span> <span class="k">like</span> <span class="ss">&quot;%bc%&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="sr">/bc/</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">user_id</span> <span class="k">like</span> <span class="ss">&quot;bc%&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="sr">/^bc/</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
<span class="k">ORDER</span> <span class="k">BY</span> <span class="n">user_id</span> <span class="k">ASC</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span> <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">}</span> <span class="p">).</span><span class="nx">sort</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="mi">1</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
<span class="k">ORDER</span> <span class="k">BY</span> <span class="n">user_id</span> <span class="k">DESC</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span> <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">}</span> <span class="p">).</span><span class="nx">sort</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="o">-</span><span class="mi">1</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="k">COUNT</span><span class="p">(</span><span class="o">*</span><span class="p">)</span>
<span class="k">FROM</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">count</span><span class="p">()</span>
</span></pre></div>
</div>
<p><em>or</em></p>
<div class="last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">().</span><span class="nx">count</span><span class="p">()</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="k">COUNT</span><span class="p">(</span><span class="n">user_id</span><span class="p">)</span>
<span class="k">FROM</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">count</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$exists</span><span class="o">:</span> <span class="kc">true</span> <span class="p">}</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
<p><em>or</em></p>
<div class="last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span> <span class="p">{</span> <span class="nx">user_id</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$exists</span><span class="o">:</span> <span class="kc">true</span> <span class="p">}</span> <span class="p">}</span> <span class="p">).</span><span class="nx">count</span><span class="p">()</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="k">COUNT</span><span class="p">(</span><span class="o">*</span><span class="p">)</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">age</span> <span class="o">&gt;</span> <span class="mi">30</span>
</pre></div>
</div>
</td>
<td><div class="first highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">count</span><span class="p">(</span> <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$gt</span><span class="o">:</span> <span class="mi">30</span> <span class="p">}</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
<p><em>or</em></p>
<div class="last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span> <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$gt</span><span class="o">:</span> <span class="mi">30</span> <span class="p">}</span> <span class="p">}</span> <span class="p">).</span><span class="nx">count</span><span class="p">()</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="k">DISTINCT</span><span class="p">(</span><span class="n">status</span><span class="p">)</span>
<span class="k">FROM</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">distinct</span><span class="p">(</span> <span class="s2">&quot;status&quot;</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">LIMIT</span> <span class="mi">1</span>
</pre></div>
</div>
</td>
<td><div class="first highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">findOne</span><span class="p">()</span>
</span></pre></div>
</div>
<p><em>or</em></p>
<div class="last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">().</span><span class="nx">limit</span><span class="p">(</span><span class="mi">1</span><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">LIMIT</span> <span class="mi">5</span>
<span class="n">SKIP</span> <span class="mi">10</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">().</span><span class="nx">limit</span><span class="p">(</span><span class="mi">5</span><span class="p">).</span><span class="nx">skip</span><span class="p">(</span><span class="mi">10</span><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">EXPLAIN</span> <span class="k">SELECT</span> <span class="o">*</span>
<span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span> <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">}</span> <span class="p">).</span><span class="nx">explain</span><span class="p">()</span>
</span></pre></div>
</div>
</td>
</tr>
</tbody>
</table>


<table border="1" class="docutils">
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">SQL Update Statements</th>
<th class="head">MongoDB update() Statements</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">UPDATE</span> <span class="n">users</span>
<span class="k">SET</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;C&quot;</span>
<span class="k">WHERE</span> <span class="n">age</span> <span class="o">&gt;</span> <span class="mi">25</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">update</span><span class="p">(</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="p">{</span> <span class="nx">$gt</span><span class="o">:</span> <span class="mi">25</span> <span class="p">}</span> <span class="p">},</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">$set</span><span class="o">:</span> <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;C&quot;</span> <span class="p">}</span> <span class="p">},</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">multi</span><span class="o">:</span> <span class="kc">true</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">UPDATE</span> <span class="n">users</span>
<span class="k">SET</span> <span class="n">age</span> <span class="o">=</span> <span class="n">age</span> <span class="o">+</span> <span class="mi">3</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;A&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">update</span><span class="p">(</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;A&quot;</span> <span class="p">}</span> <span class="p">,</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">$inc</span><span class="o">:</span> <span class="p">{</span> <span class="nx">age</span><span class="o">:</span> <span class="mi">3</span> <span class="p">}</span> <span class="p">},</span>
</span><span class="hll">   <span class="p">{</span> <span class="nx">multi</span><span class="o">:</span> <span class="kc">true</span> <span class="p">}</span>
</span><span class="hll"><span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
</tbody>
</table>

<table border="1" class="docutils">
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">SQL Delete Statements</th>
<th class="head">MongoDB remove() Statements</th>
</tr>
</thead>
<tbody valign="top">
<tr class="row-even"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">DELETE</span> <span class="k">FROM</span> <span class="n">users</span>
<span class="k">WHERE</span> <span class="n">status</span> <span class="o">=</span> <span class="ss">&quot;D&quot;</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">remove</span><span class="p">(</span> <span class="p">{</span> <span class="nx">status</span><span class="o">:</span> <span class="s2">&quot;D&quot;</span> <span class="p">}</span> <span class="p">)</span>
</span></pre></div>
</div>
</td>
</tr>
<tr class="row-odd"><td><div class="first last highlight-sql"><div class="highlight"><pre><span></span><span class="k">DELETE</span> <span class="k">FROM</span> <span class="n">users</span>
</pre></div>
</div>
</td>
<td><div class="first last highlight-javascript"><div class="highlight"><pre><span></span><span class="hll"><span class="nx">db</span><span class="p">.</span><span class="nx">users</span><span class="p">.</span><span class="nx">remove</span><span class="p">({})</span>
</span></pre></div>
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




