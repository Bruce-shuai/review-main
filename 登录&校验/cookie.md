## 什么是cookie
* `HTTP` 是**无状态**的协议（对于事务处理没有记忆能力，每次客户端和服务端会话完成时，服务端不会保存任何会话信息）：每个请求都是完全独立的，服务端无法确认当前访问者的身份信息，无法分辨上一次的请求发送者和这一次的发送者是不是同一个人。所以服务器与浏览器为了进行会话跟踪（知道是谁在访问我），就必须主动的去维护一个状态，这个状态用于告知服务端前后两个请求是否来自同一浏览器。而这个状态需要通过 **cookie** 或者 **session** 去实现。   (用来维持请求状态的)

* **`cookie` 存储在客户端**： `cookie` 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时自动被携带并发送到服务器上。 

* **`cookie` 是不可跨域的**： 每个 **cookie** 都会绑定单一的域名，无法在别的域名下获取使用。 (但是`cookie`有路径`path`一说，域名路径)，以及域名可以支持一级域名和二级域名共享(靠的是domain)

* **存储容量很低** 4kb

## cookie的一些属性 

| 属性 | 说明 |
|-----|-----|
|name=value;| 设置cookie的键值对，例如：name='heyang'; ,这些都必须是字符串类型。|
|domain|指定cookie所属域名，默认是当前域名|
|path| 指定cookie在哪个路径(路由)下生效，默认是'/',如果设置为`/abc`,则只有`/abc`下的路由可以访问到该cookie,如：`/abc/read`|
|maxAge| cookie失效的时间，单位是秒，如果为整数，则该 cookie 在 maxAge 秒后失效。如果为负数，该 cookie 为临时 cookie ，关闭浏览器即失效，浏览器也不会以任何形式保存该 cookie 。如果为 0，表示删除该 cookie 。默认为 -1。- 比 expires 好用。|
|expires | 过期时间，在设置的某个时间点后该 cookie 就会失效。一般浏览器的 cookie 都是默认储存的，当关闭浏览器结束这个会话的时候，这个 cookie 也就会被删除 |
|secure | 该 cookie 是否仅被使用安全协议传输。安全协议有 HTTPS，SSL等，在网络上传输数据之前先将数据加密。默认为false。当 secure 值为 true 时，cookie 在 HTTP 中是无效，在 HTTPS 中才有效。|
| httpOnly | **如果给某个 cookie 设置了 httpOnly 属性，则无法通过 JS 脚本 读取到该 cookie 的信息，但还是能通过 Application 中手动修改 cookie，所以只是在一定程度上可以防止 XSS 攻击，不是绝对的安全**(这个牛逼) |


## 前端使用js修改cookie的方法
```js
document.cookie = 'xxx'  // 非覆盖式的
```

```js
res.setHeader('Set-Cookie', `xxxx`);
```


## Cookie 的 SameSite 属性

`SameSite` 属性可以让 `Cookie` 在**跨站**请求时不会被发送，从而可以阻止跨站请求伪造攻击(CSRF)

**属性值：**
1. **Strict** 仅允许一方请求携带 `Cookie`,即浏览器将只发送相同站点请求的 Cookie，即当前网页 URL 与请求目标 URL 完全一致。
2. **Lax** 允许部分第三方请求携带 `Cookie`
3. **None** 无论是否跨站都会发送 Cookie 

Chrome80之后默认是Lax

### 跨域和跨站 
同源策略的同源是指两个 URL 的协议/主机名/端口一致。例如，www.taobao.com/pages/...，它… https，主机名是 www.taobao.com，端口是 443。

同站：只要两个 URL 的 eTLD+1 相同即可，不需要考虑协议和端口。其中，eTLD 表示有效顶级域名，注册于 Mozilla 维护的公共后缀列表（Public Suffix List）中，例如，.com、.co.uk、.github.io 等。eTLD+1 则表示，有效顶级域名+二级域名，例如 taobao.com 等。 (只要是顶级域名 + 二级域名一致就可以)


跨站：Cookie与此息息相关，Cookie实际上遵守的是“同站”策略
(注意，二级域名不同算跨域，但属于同站)
