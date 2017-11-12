---
layout: wp
title: django celery transaction error
comments: true
---

背景：
    提交任务，异步处理。  

异步处理方案： `celery`  

问题：   
    `celery` 在 run 的时候，根据 task id 来 `do something`, 但是偶尔会报错：   

` matching query does not exist `  

查看数据库，数据确实是存在的。为什么会报这个错呢？除非 celery 处理早于 db commit.查看资料（见参考资料):  


>The data will only be externally accessible when the view finishes its execution, and the transaction is committed. This usually will happen **after** Celery executes the task.

我们默认`autocommit`, 但 commit 是在 celery delay 之后，因此偶尔就会报错。**怎么解决？**   

如果是 django 1.9, 使用 `transaction.on_commit`: `transaction.on_commit(lambda: do_stuff.delay(my_data.pk))`

如果是 <1.9, 使用[django-transaction-hooks](https://github.com/carljm/django-transaction-hooks)   

另一种解决方案(verifying):  
手动 commit: `transaction.commit()`    



### 参考资料 ###

[Dealing with database transactions in Django + Celery](https://www.hypertrack.com/blog/2016/10/08/dealing-with-database-transactions-in-django-celery/)   
[Database concurrency in Django the right way](https://www.vinta.com.br/blog/2016/database-concurrency-in-django-the-right-way/)   
[django-transaction-hooks](https://django-transaction-hooks.readthedocs.io/en/latest/)   

