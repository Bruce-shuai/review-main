### what is JS?

一种直接运行在网页 `HTML` 中的脚本代码。
与 Java/C++/C 等编译型语言不一样，它是一种解释型语言。

由于有了`Node.js`的出现，`JS` 不仅仅可以运行在浏览器，它可以运行在服务端以及各种可以使用 `Nodejs` 环境的地方。(`NodeJS` 有可以执行 `JS` 的 `v8` 引擎，并且 `NodeJS` 里面的 `libUv`(C++所写的库) 让 `JS` 可以跨环境运行。)

```bash
引擎是如何工作的？

引擎本身是复杂的，但是基本原理比较简单：
1. 引擎读取('解析')脚本
2. 引擎将脚本转化为机器语言
3. 快速执行机器语言
```

### 在浏览器中的 JS 可以做什么？

在浏览器中的 `JS` 是不能去访问 `内存` 或 `CPU` 的，这保障了操作系统级别的安全！
但是要注意的是 `Node.js` 本身是支持访问 `内存` 和 `CPU` 的，所以使用 nodejs 能够进行后端代码的编写。

浏览器中的 JS 可以做以下事情：

- 修改网页样式、增添网页动画以及交互效果。
- 发送网络请求，从服务端获取数据
- 反馈用户所触发的事件(例如：用户鼠标点击事件、用户鼠标移动事件)
- 进行用户信息的存储

### 在浏览器中的 JS 不可以做什么？

因为这里的 JS 和用户个人信息绑定比较深，所以为了保护用户的信息完全，在浏览器中的 JS 的功能是收到限制的。防止信息被篡改或窃取

- 浏览器中的 JS 不可以访问并操控操作系统的能力。例如，增删改查电脑磁盘，甚至在做一些文件相关的操作时，也需要用户自己把文件放进 input 标签里才会起效果。

- 受到同源策略的限制，以及有比较强大的 HTTP 头信息以及 HTTPS 的保护

### JS 流行的原因

- 操作简单
- 与 HTML/CSS 的集成度很高
- 被各类现代浏览器支持，并默认开启
- nodejs 的出现，让 js 并不局限于浏览器、以及网页控制。

### JS 的上层语言

例如`TypeScript`。但 TS 也是在**浏览器执行之前**，被**编译**(转化)成 JS。

### 建议的开发工具

IDE: vscode
浏览器： Chrome + Chrome 控制台 (f12 或者 Cmd + Opt + J)
