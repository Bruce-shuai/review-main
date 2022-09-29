## 什么是token
* 访问资源接口（API）时所需要的资源凭证
* 一般是由：uid(用户唯一的身份标识)，time当前的时间戳，sign签名(哈希)
* 特点
  * 服务端无状态化、可扩展性好  (这是啥意思？)
  * 支持移动设备 (重点)
  * 安全 
  * 支持跨程序调用
  
## token身份验证流程

```bash

   浏览器                            服务器
       -----username&password------->
       <---------返回token----------   将登录凭证做数字签名，加密之后得到字符串作为token
拿到token后，
将token保存在
本地(cookie或者localStorage里面)
       -----请求API时携带Token--------> 拿到token串，做解密和签名认证，判断其有效性
       <-------返回数据---------------
```

* 每一次请求都需要手动携带 token，需要把 token 放到 HTTP 的 Header 里
* 基于 token 的用户认证是一种服务端无状态的认证方式，服务端不用存放 token 数据。用解析 token 的计算时间换取 session 的存储空间，从而减轻服务器的压力，减少频繁的查询数据库
* token 完全由应用管理，所以它可以避开同源策略 (重点)

### refresh token  
`refresh token` 是专用于刷新 `access token` 的 `token`。如果没有 `refresh token`，也可以刷新 `access token`，但每次刷新都要用户输入登录用户名与密码，会很麻烦。有了 `refresh token`，可以减少这个麻烦，客户端直接用 `refresh token` 去更新 `access token`，无需用户进行额外的操作。

```bash  
    浏览器                            服务器 
        ------username&password------->
        <---access token 一周---------- 将登录凭证做数字签名，加密之后得到字符串作为token
            refresh token 一个月

        --------access token--------->
        <---------返回数据------------- access token没过期 

        --------access token--------->
        <---------返回错误------------- access token过期，refresh token没有过期

        --------refresh token ------->
        <-------1.refresh token没有过期，
          则返回新的access token和refresh token     判断refresh token是否过期
        <-------2.refresh token过期则需要重新登录
```

* Access Token 的有效期比较短，当 Acesss Token 由于过期而失效时，使用 Refresh Token 就可以获取到新的 Token，如果 Refresh Token 也失效了，用户就只能重新登录了。
* Refresh Token 及过期时间是存储在服务器的数据库中，只有在申请新的 Acesss Token 时才会验证，不会对业务接口响应时间造成影响，也不需要向 Session 一样一直保持在内存中以应对大量的请求。

## token vs Session
* 因为用户的状态不再存储在服务端的内存中，所以`token`是一种无状态的认证机制
* session 的安全性比session更高
* 如果你的用户数据可能需要和第三方共享，或者允许第三方调用api接口，用token。如果永远只是自己的网站，自己的app，用什么都无所谓

## jwt
JSON Web Token（简称 JWT）是目前最流行的**跨域认证**解决方案。 (session、cookie的认证不能跨域的)

JWT是一种认证、授权机制。就是登录成功后将相关用户信息组成 JSON 对象，然后对这个对象进行某种方式的加密，返回给客户端； 客户端在下次请求时带上这个 Token； 服务端再收到请求时校验 token 合法性，其实也就是在校验请求的合法性。

### JWT的组成

JWT 由三部分组成：`Header 头部`、`Payload 负载` 和 `Signature 签名`
是一个很长的字符串，中间用 `.` 来分成3个部分
```bash 
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

头部主要是用来记录用的哪些算法
负载主要是存放一些用户相关信息
签名是用来防止上述两部分被篡改


```bash

    浏览器                         服务器
      -----1.username&password------>     2. 服务端使用密钥创建jwt
      <----3.把JWT返回给浏览器---------
      ---4.在发给服务器的认识头中发送JWT->    5. 检查JWT的签名，从JWT获取用户信息
      <--6.把相应发送给客户端---------
```

`JWT` 放在请求头的`Authorization`字段中:
`Authorization: Bearer <token>`

因为 `JWT` 并不使用 `Cookie` 的，所以你可以使用任何域名提供你的 `API` 服务而不需要担心跨域资源共享问题`（CORS）`。而 `session` 就没办法，需要把 `seesionId` 放在 `cookie` 里面，所以也会收到跨域问题。


当客户端收到服务器返回的`JWT`，可以存储在`Cookie`里面，也可以存储在`localStorage`里面。但是如果放在`cookie`里面，后续发送则无法支持跨域，所以最好的做法是放在`HTTP`请求头信息的`Authorization`

