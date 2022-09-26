## Node.js的能力
* 脱离浏览器运行JS
* 前端工程化
* 后端 Server    
* 作为中间层  

### 后端 Server
* API 和 数据存储
* 登录和redis
* 安全和日志
* pm2  

### 后端思维

#### 服务稳定性
* server端可能会遭受各种恶意攻击和误操作
* 单个客户端可以挂掉，但是服务端不行
* PM2 进程守候

#### 考虑CPU和内存(优化、扩展)
* 客户端独占一个浏览器，内存和cpu都不是问题
* server端要承载很多请求，CPU和内存都是稀缺资源   
* 使用stream写日志，使用redis存session   

#### 日志记录
* 前端也会写日志，但只是日志的发起方，不用关心后续
* server端要记录日志、存储日志、分析日志，前端是不用关心的

#### 安全
* server端要随时准备接受各种恶意攻击，前端则少很多
* 如：越权操作，数据库攻击等
* 登录验证，预防xss攻击和sql注入

#### 集群和服务拆分
* 产品发展速度快，流量可能会迅速增加
* 如何通过扩展机器和服务拆分来承载大流量

### nodejs处理 http 请求
* get请求和querystring
  * get请求，即客户端要向server端获取数据，如查询博客列表
  * 通过querystring来传递数据，如a.html?a=100&b=200 
  * 浏览器直接访问，就发送get请求  
* post请求和postdata
  * post请求，即客户端要向服务端传递数据，如新建博客
  * 通过 post data 传递数据
  * 浏览器无法直接模拟，需要手写js，或者使用postman
* 路由     
  * https://github.com/
  * https://github.com/username
  * https://github.com/username/xxx

#### 简单处理http请求示例
```js
const http = require('http');

const server = http.createServer((req, res) => {
  res.end('hello world');
})
server.listen(8000);
```

**发送get请求**

```js
const http = require('http');
const querystring = require('querystring');

const server = http.createServer((req, res) => {
  console.log(req.method);  // 客户端请求的方法 GET
  const url = req.url;      // 获取请求的完整url
  req.query = querystring.parse(url.split('?')[1])// 解析路径的参数
  res.end(JSON.stringify(req.query));  // 将querystring返回
})
server.listen(8000);
```

**发送post请求**
```js
const http = require('http');

const server = http.createServer((req, res) => {
  if (req.method === 'POST') {
    // 数据格式 content-type 就是数据类型
    console.log('content-type', req.headers['content-type'])
    // 接收数据
    let postData = "";
    req.on('data', chunk => {  // chunk就是一点点的数据...流数据。这个'data'事件可能会触发很多次，每次触发，都会启动下面这个人回调
      postData += chunk.toString() // chunk是二进制格式，toString转化为字符串格式
    })
    req.on('end', () => { // 上述data事件触发结束后，就会触发这个end事件
      console.log(postData);
      res.end('hello world');  
    })
  }
})
server.listen(8000);
```

**综合练习**
可在postman里面测试测试
```js
const http = require('http');
const querystring = require('querystring');
const server = http.createServer((req, res) => {
  const method = req.method;
  const url = req.url;
  const path = url.split('?')[0];
  const query = querystring.parse(url.split('?')[1]);
  
  // 返回的数据格式
  res.setHeader('Content-type', 'application/json');

  const resData = {
    method,
    url,
    path,
    query    
  }


  if (method === 'GET') {
    res.end(
      JSON.stringify(resData)
    )
  }
  if (req.method === 'POST') {
    // 接收数据
    let postData = "";
    req.on('data', chunk => {  // chunk就是一点点的数据...流数据。这个'data'事件可能会触发很多次，每次触发，都会启动下面这个人回调
      postData += chunk.toString()
    })
    req.on('end', () => { // 上述data事件触发结束后，就会触发这个end事件
      resData.postData = postData;
      res.end(JSON.stringify(resData));
    })
  }
})
server.listen(8000);
```

### node.js文件处理
```js
// 核心是使用
const fs = require('fs');       // 文件名
const path = require('path');   // 文件路径

// 查找文件的绝对路径
const fullFileName = path.resolve(__dirname, '当前所在的文件夹', '文件名');
// 文件读取 (这是异步的)
fs.readFile(fullFileName, (err, data) => {
  if (err) {
    console.error(err)
    return;
  }
  console.log(data.toString());
})
```

### 搭建nodejs项目

一般需要安装的依赖包： cross-env 和 nodemon

 