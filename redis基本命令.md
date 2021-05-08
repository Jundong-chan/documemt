```bash
命令不区分大小写
###########启动服务器和客户端等操作##########
1.redis-server redis.conf
2.redis-cli -h+host地址 -p +port
3.redis-benchmark 
4.select #后跟数字，表示切换数据库#
5.dbsize #表示显示该数据库的大小#

############关闭#################
1.shutdown 后 exit退出就好了
2.ps -ef | grep redis  使用 管道过滤 关键字redis 查看有关redis的进程
###########k-v类型的存储################
1.set
set name "wang"
set id 9
2.get
get name  #返回值#
get id
3.keys * #返回所的key#
4.exist name #查看某个key是否存在，返回bool#
5.flushdb #清空该db#
6.flushall 	#清空所有的db#
7.mv key db
mv name 2  #移动name到2号数据库#
8.del key #删除某个key#
9.expire name 30  #设置name的过期时间为30s，过期后，此命令就无法再设置这个key的过期时间了，get也无法查到它的信息#
10.ttl name	#查看name的过期时间还剩多少，已过期的显示-2，没有设置过的显示-1#
11.type key  #查看key对应的value的类型#
12.append name "xiaoming"		#追加,返回key当前的值长度，没有则新建#
13.incr id  #id+1,以下情况都是如果key不存在则新建#
decr id  #id-1#
incrby id 10 #id+10#
decrby id 10 #id-10#
14.getrange name 0 1  	#获取区间字符串#
15.setrange name 4 "kai"  #从第4个字符开始往后，替换成kai，kai后面的字符不变#
16.setex name 10 "sss" #设置name为 “sss”，并设置10s过期时间，如果name不存在则新建，具有原子性#
#等同于#
SET mykey value
EXPIRE mykey seconds
17.setnx name "sssss" 	#如果name不存在则设置，存在则不做，具有原子性#
18.mset与mget  #批量设置与获取#
mset k1 v1 k2 v2
msetnx 	#设置多个值，如果不存在才设置，具有原子性，其中一个存在，也不能成功#
19.getset #先get再set ,如果get得到nil，set也会成功,像这样的组合命令更快#
20.mset student:1:name:"wangxiaoming" student:1:school:"guangzhou"	
#设置student多个属性，1可作为id,mset才可以同时设置多个属性#
set student:1:name:"wangxiaoming" 	#set单次只能这样设置#
#get 同理#
mget student:1:name student:1:school  #返回两个信息#


##############list类型#########################################
#####list本质为双向链表，可用做数组，队列，栈，阻塞队列#########
1.lpush 与rpush #从左放入和从右放入,可一次放入多个元素#
lpush list1 "wang"
2.lpop与rpop   #从左取出与从右取出#
3.lrange list1 0 1	#获取区间元素，rrange 类似，方向不同而已#
lrange list1 0 -1		#获取list1的所有元素#
4.lindex与rindex		#根据下标获取对应值，类似数组操作#
lindex list1 1
5.llen list1	#返回元素个数，不存在rlen#
6.lrem list1 3 "d"  #从左往右开始数，移除3个值为d的元素，返回的是成功移除了多少个,不存在rrem#
7.rpoplpush list1 list2	#从list1右边取出一个，放入list2最左边，如果list2不存在则同时新建，注意不存在lpoprpush#
8.lset list1 0 "w"	#根据下标设置值，不存在rset#
9.linsert list1 before或者after 1 "s" "sss" 	#往指定的值前或者后插入新的值，不存在rinsert#
```



```bash
set类型是一种集合，无序，元素不能重复
###################set类型###############################
1.sadd myset "hello"
2.smembers myset #查看myset所有的元素#
3.sismember myset hello  #判断某个元素是否在myset中#
4.srandmember myset #随机选择一个元素，可用于抽奖场景# 
srandmember myset 2 #随机选择两个#
5.srem myset hello #删除hello#
6.scard myset  #获取myset的个数#
7.sdiff myset1 myset2  #求myset1的差集#
8.sinter myset1 myset2  #求多个集合的交集，可用于共同好友功能#
9.suinon myset1 myset2 #求多个集合的并集#
```

```bash
相当于 key-map 类型 也就是 key-key-value
存储的是map
string类型可以存储对象（使用:），但是使用hash存储对象更方便
################hash类型###########################
1.hset myhash field1 "hello"	#设置键值，重复设置会覆盖#
2.hget myhash field1  #获取#
3.hmset 与 hmget #设置和获取多个值#
4.hgetall myhash 	#获取所有的k-v，以k -v 形式显示#
5.hdel myhash field1  #删除指定的值#
6.hlen myhash  #获取hash表的字段数量#
7.hexist myhash filed1  #判断是否存在#
8.hkeys myhash		#获取所有的key#
9.hvals myhash 	#获取所有的value#
```

```bash
有序的集合，相对于set就是加了一个类似权重的东西有助于排序，只要value不重复即可，权重可以重复
可用于工资排序等等
也可用于权重分配，排行榜，取top n
##############zset类型###########################
1.zadd myset 1 one 2 oo 3 dh	#数字表示排序的依据#
2.zrange myset 0 -1 #拿到所有的元素，从小到大#
zrevrange myset 0 -1  #从大到小排序输出#
3.zrangebyscore myset -inf +inf   #从最小到最大排序#
4.3.zrangebyscore myset -inf +inf withscores  #从最小到最大排序,带上成绩#
5.zrem myset one	#移除指定元素#
6.zcard myset 
7.zcount myset 1 3  #获取指定区间的成员数量#

```

