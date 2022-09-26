## 思考问题
* 进程内存有限，访问量过大内存暴增怎么办？
* 正式线上运行是多进程，进程之间内存无法共享

## Redis
* web server最常用的缓存数据库(key-value的数据库)，数据存放在内存中(内存速度很快)
* 相比mysql，访问速度快(内存和硬盘不是一个数量级的)，mysql是硬盘数据库，内存是内存数据库
* 但是成本更高，可存储的数据量更小(内存的硬伤)


适合把session放在redis
* 因为session访问频繁，对性能要求极高
* session 可不考虑断电数据丢失问题(内存的硬伤)，session数据丢了可以重新获取。 session可以避免断点数据丢失，需要配置
* session 数据量不会很大(相比于mysql中存储的数据)


## 安装 Redis
```bash
brew install redis  
```