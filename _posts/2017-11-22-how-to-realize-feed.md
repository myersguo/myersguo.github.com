---
layout: wp
title: thinking in feed timeline
comments: true
---

### timeline ###

两种类型：user timeline(用户的的个人主页), home timeline(用户关注的人的信息流merge)   

实现方式:   

### 推(push) ###

用户发信息时，推送给所有的粉丝(更新粉丝的 home timeline)   

```
    def get_followers(uid):
        return redis.smembers('followers:' + uid)

    def write_post(uid, data):
        id = redis.incr('post:uuid:')  # 生成 Twitter id 
        redis.hset('posts:%s' % id, data) # 存放 内容到 hash set
        followers = get_followers(uid) # 获取粉丝列表
        for follower in followers:
            redis.zadd('home_timeline:%s' % follower , id) # 写入粉丝列表 timeline
        
```

获取 timeline:   

```
    def get_timeline(uid):
        posts = redis.zrevrange('home_timeline:%s' % uid, 0, 30) #获取30个 feed timeline
        ret = []
        for id in posts:
            ret[id] = hgetall('posts:%s' % id) # 获取每个id的内容
        return ret 
```

这里的推送是将 id 写入到 集合中，当然也可以使用队列的方式，写时 lpush 到粉丝的 timeline 中，粉丝获取 timeline时，lrange 获取。  

### 拉 ###  

```
    #写
    def write_post(uid, data):
        id = redis.incr('post:uuid:')  # 生成 Twitter id 
        redis.hset('posts:%s' % id, data) # 存放 内容到 hash set
        redis.sadd('posts:user:' % uid, id)
        
    def get_following(uid):
        return redis.smembers('following:' + uid)
    #读
    def get_timeline(uid):
        followings = get_following(uid) # 获取关注的列表
        redis.zunionstore(ret, {'post::user:user1', 'post:user:user2'})  # 获取关注的用户的并集
```

### 问题 ###   

推的问题：   
    假如一个用户的粉丝有1000万个，那么一次写入要推送到1千万用户的timeline中，非常耗时，解决办法有：   
    * 粉丝按照活跃度排序，优先推给活跃的粉丝，其他粉丝异步延迟推送  
    * 当粉丝数过多时，不进行推送，粉丝的timeline 从关注的大V中获取最新 post ，然后进行 merge    
拉的问题：   
    每次拉取都是一次大量并集运算, 相反「推」则一次 get 可获取所有消息列表。
    * 关注者按照活跃度排序，只获取特定数量的活跃用户记录

### 参考资料 ###

[The Architecture Twitter Uses To Deal With 150M Active Users, 300K QPS, A 22 MB/S Firehose, And Send Tweets In Under 5 Seconds](http://highscalability.com/blog/2013/7/8/the-architecture-twitter-uses-to-deal-with-150m-active-users.html)
[The Infrastructure Behind Twitter: Scale](https://blog.twitter.com/engineering/en_us/topics/infrastructure/2017/the-infrastructure-behind-twitter-scale.html)
[Redis timeline](https://segmentfault.com/a/1190000004650279)
