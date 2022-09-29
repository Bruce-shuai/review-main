## 什么是session
* `session` 是另一种记录服务器和客户端会话状态的机制
* `session` 是基于 `cookie` 实现的，`session` 存储在服务器端，`sessionId` 会被存储到客户端的 `cookie` 中。
* `session`的数据一般存放在`redis`里面。毕竟`session`数据不用持久存储以及`session`的使用频率很高，所以用`redis`就显得恰到好处。

```bash

客户端(浏览器) ------第一次请求---->  服务器(创建Session)
             <---返回SessionID--- (一般放在响应头的Set-Cookie里)
这个id放在cookie里，cookie放在浏览器里面的
      
     ----第二次请求，携带Cookie中的SessionID---> 利用SessionId,来判断是哪个用户，以及进行后续操作
```

## session认证流程
用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建对应的 Session
请求返回时将此 Session 的唯一标识信息 SessionID 返回给浏览器
浏览器接收到服务器返回的 SessionID 信息后，会将此信息存入到 Cookie 中，同时 Cookie 记录此 SessionID 属于哪个域名
当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。

## 注意：
* Cookie 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭（默认情况下）或者 Session 超时都会失效
* Cookie 只支持存字符串数据，想要设置其他类型的数据，需要将其转换成字符串，Session 可以存任意数据类型。
* Session的存储量远远高于Cookie的4kb存储量