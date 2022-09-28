## 思考问题
* 进程内存有限，访问量过大内存暴增怎么办？
* 正式线上运行是多进程，进程之间内存无法共享

## Redis
* redis 是非关系型的数据库(nosql)，并且和其他的非关系型以及关系型数据库不一样。首先它不是表结构的，而是key:value键值对格式的(就像一个大型的json)
* web server最常用的缓存数据库，数据存放在内存中(内存速度很快)
* 相比mysql，访问速度快(内存和硬盘不是一个数量级的)，mysql是硬盘数据库，内存是内存数据库
* 但是成本更高，可存储的数据量更小(内存的硬伤)


适合把session放在redis
* 因为session访问频繁，对性能要求极高
* session 可不考虑断电数据丢失问题(内存的硬伤)，session数据丢了可以重新获取。 session可以避免断点数据丢失，需要配置
* session 数据量不会很大(相比于mysql中存储的数据)


## 安装 Redis
mac 和 linux系统的电脑安装redis非常简单
```bash
brew install redis  
```

https://lanask.com/wp-content/plugins/erphpdown/download.php?postid=1053&key=1&nologin=1

## how to use redis by nodejs

```bash
# first: 启动 Redis
redis-server  
# second: 在另外开一个终端，进行连接redis
redis-cli

# 断开连接 
quit

# 创建 key value 
SET name heyang

# 查看value
GET name 

# 删除键值对
DEL name

# 查看是否存在该键值对
EXISTS name

# 查看所有的键值对
KEYS * 

# 清理所有的键值对 
flushall

# 清理当前终端的内容
clear

# 查看过期时间(秒为单位)  -2表示已过期
ttl name   

# 设置过期时间(秒为单位)
expire name 10   

# 设置有过期时间的键值对 
setex name 10 heyang

# 设置数组 
lpush friends john
lpush friends bruce
lpush friends mike

# 查看数组 
lrange friends 0 -1

# 去掉数组尾部元素 
lpop friends  

# 去掉数组头部元素
rpop friends

# 创建一个set元素
SADD hobbies "weight lifting"

# 查看set里面的内容 
SMEMBERS hobbies

# 删除set里面的某个内容
SMEMBERS hobbies   

# 设置hash
HSET person name heyang

# 查看hash里面指定的value
HGET person name

# 查看hash里面指定的内容
HGETALL person

# 删除hash里面指定的内容
HDEL person age 

# 查看某个内容是否存在
HEXISTS person name
```


### nodejs中使用redis

```bash
npm i redis
redis-server 

```

```js
const Redis = require('redis');
const DEFAULT_EXPIRATION = 3600
const redisClient = Redis.createClient();
redisClient.setex('key', DEFAULT_EXPIRATION, JSON.stringify(data));

redisClient.get('key', (err, value) => {})
```