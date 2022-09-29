# CORS: Cross Origin Resource-Sharing 跨站资源共享

实现 `CORS` 通信的关键是服务器。只要服务器实现了 `CORS` 接口，就可以跨源通信，即为了解决跨域问题。

## CORS 请求类型 

浏览器将 CORS 请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）

### 简单请求
| 情况 | 描述 |
|------|------|
| 请求方法 | 请求方法为： `HEAD` 或 `GET` 或 `POST` |
| HTTP头信息 | HTTP 头信息不超出以下几种字段：AcceptAccept-LanguageContent-LanguageLast-Event-IDContent-Type：只限于三个值 application/x-www-form-urlencoded、multipart/form-data、text/plain|

凡是不同时满足上面两个条件，就属于非简单请求。

当浏览器发现我们的 `AJAX` 请求是个简单请求，便会自动在头信息中，增加一个 `Origin` 字段。
`Origin` 字段用来说明本次请求的来源（包括协议 + 域名 + 端口号），服务端根据这个值来决定是否同意此次请求。

服务器发送的头信息中的关键字段：
* `Access-Control-Allow-Origin`: 这字段是必须有的，表示许可范围的域名。通常两种值：请求时Origin字段的值或者 `*`(表示任意域名)
* `Access-Control-Allow-Credentials`: 可选字段：表示在CORS请求之中发送`Cookie`。若不携带Cookie则不需要设置该字段(**cookie 跨域操作**)。这个设置为true时，请求方需要开启 `withCredentials`属性，否则浏览器也不会发送 `Cookie`。
```js
let xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```
注意： 如果前端设置 Access-Control-Allow-Credentials 为 true 来携带 Cookie 发起请求，则服务端 Access-Control-Allow-Origin 不能设置为 *。

### 非简单请求
非简单请求情况如：请求方法是 `PUT / DELETE` 或者 **`Content-Type:application/json`** 类型的请求。

在非简单请求发出 `CORS` 请求时，会在正式通信之前增加一次 “预检”请求（`OPTIONS`方法），来询问服务器，本次请求的域名是否在许可名单中，以及使用哪些头信息。

当 “预检”请求 通过以后，才会正式发起 `AJAX` 请求，否则报错。

“预检”请求 信息中包含两个特殊字段：
`Access-Control-Request-Method`
该字段是必须的，用来列出浏览器的 `CORS` 请求会用到哪些 `HTTP` 方法，上例是 `PUT`。
`Access-Control-Request-Headers`
指定浏览器 `CORS` 请求额外发送的头信息字段，上例是 `X-Custom-Header`。

当预检请求通过以后，在预检响应头中，会返回 `Access-Control-Allow-` 开头的信息，其中 `Access-Control-Allow-Origin` 表示许可范围，值也可以是 `*`。
当预检请求拒绝以后，在预检响应头中，不会返回 `Access-Control-Allow-` 开头的信息，并在控制台输出错误信息。
