---
layout: wp
title: 功能测试面试题
---
1. 让你加购物车的功能你如何测试？  
商品的状态：上下架、缺货；  
商品的类型：普通商品、套装、多级分类、赠品、加价购；  
商品的来源：抢购、正常；  
商品的存储方式：  
商品的特殊点；  

购物车的存储方式  ：
用户的种类：匿名、登录；   
。。。。
2. HTTP状态码： 500,403,400,301,302是什么意思？  
500以上：服务器端错误；  
400---500：客户请求错误：  
403：禁止访问  
400：请求参数错误  
301、302：跳转，301：永久跳转。  


3. jsonp请求是什么意思？为什么需要JSONP请求？  
JSONP请求是解决JS不能跨域执行的问题。浏览器对AJAX请求有限制，只能同一个域名的请求才可以。JSONP可以请求不同域名，相当于执行一个远程脚本，返回一个对象，在浏览器端执行该对象。   

4. 有两张表:  
user :  
id,user_name,sex,add_time  
project:  
id,project_name,add_time  
user_project:  
id,user_id,project_id  

写出用户的所有项目，显示用户的id,user_name,项目的id,project_name  

SELECT user.id,user.user_name,project.id,project.project_name  
FROM user_project  
JOIN user ON user_project.user_id=user.id   
JOIN project ON user_project.project_id=project.id  
