### 介绍：
* 是web server中最流行的关系型数据库
* 官网可免费下载，用于学习
* 轻量级，易学易用

### mysql workbench
一个mysql的可视化操作工具

### node.js如何连接mysql

1. 下载mysql模块：npm i mysql   
2. 创建链接对象
3. 开始连接
4. 执行sql语句
5. 关闭连接
```js
const mysql = require('mysql');

// 创建链接对象
const con = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: '12345678',
  port: '3306',
  database: 'myblog'
})

// 开始连接
con.connect();

// 执行sql语句
const sql = 'select * from users;';
con.query(sql, (err, result) => {
  if (err) {
    console.error(err);
    return;
  }
  console.log(result);
})

// 关闭连接
con.end();
```