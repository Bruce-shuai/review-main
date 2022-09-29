# XSS 跨站脚本（英语：Cross-site scripting，通常简称为：XSS）

XSS 攻击，一般是指攻击者通过在网页中注入恶意脚本，当用户浏览网页时，恶意脚本执行，控制用户浏览器行为的一种攻击方式。

## 常见的危害
* 窃取用户Cookie，获取用户隐私，盗取用户账号。
* 劫持用户（浏览器）会话，从而执行任意操作，例如进行非法转账、强制发表日志、发送电子邮件等。
* 强制弹出广告页面，刷流量，传播跨站脚本蠕虫，网页挂马等。
* 结合其他漏洞，如 CSRF 漏洞，实施进一步的攻击。


## XSS 攻击分类 

<img src="https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/11/17/16e78e96864fa151~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp" />

### 如何阻止 XSS 攻击 

1. **服务器对输入脚本进行过滤或转码**
2. **充分利用CSP**，用来限制资源的下载
3. **httpOnly** 避免通过js来控制cookie