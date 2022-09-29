# CSRF 跨站请求伪造(Cross-site request forgery)
**核心知识：**跨站点请求伪造请求 (跟跨域有关吗?)
**简单理解：**攻击者盗用你的身份，以你的名义发送恶意请求。

## 疑问
### cookie不是禁止跨域的吗？那为啥还会有csrf跨站请求攻击？
* 同源策略并不能阻止所有cookie跨域的可能性。
[对应文章](https://codeantenna.com/a/68Ac4sdgKg)

### 为啥用户退出网站后就不会受到csrf攻击了？

* 会话期 Cookie 是最简单的 Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。会话期 Cookie 不需要指定过期时间（Expires）或者有效期（Max-Age）。需要注意的是，有些浏览器提供了会话恢复功能，这种情况下即使关闭了浏览器，会话期 Cookie 也会被保留下来，就好像浏览器从来没有关闭一样，这会导致 Cookie 的生命周期无限期延长。
持久性 Cookie 的生命周期取决于过期时间（Expires）或有效期（Max-Age）指定的一段时间。



如果没有`cookie`，而使用`token`，则就没有`csrf`攻击了。


### 如何减轻(防范)CSRF攻击

* **只使用JSON api**: 使用`JavaScript`发起`AJAX`请求是限制跨域的。 不能通过一个简单的`<form>`来发送`JSON`， 所以，通过只接收`JSON`，你可以降低发生上面那种情况的可能性。(意思是表单跨域是可以的)

* 避免使用 Post 请求。因为 `<form>`只能使用`GET`或者`POST`

* CSRF Tokens。在表单中带着token，如果token不合法，那么服务器就会拒绝这个请求。

* 就减少使用cookie而使用token

* 充分利用好cookie的SameSite属性

### 一般CSRF攻击方式
* 1. 自动发起`Get`请求。 将链接隐藏在`img`标签内

* 2. 自动发起`POST`请求，一般是form表格

* 3. a标签链接