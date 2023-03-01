## 起因

前端访问后端时，由于跨域导致接口访问不通，后端配置 CORS 后接口可以通，

但是 cookie 不能正常使用，axios 配置 withCredentials=true 后可以使用

对于 cookie 的安全问题引出“跨站请求伪造”如何避免 => 配置 SameSite=Lax（默认值就是这个），

以及设置 HttpOnly（禁止 js 读取） Secure 属性（只有 https 才发送）

## 知识点

同源策略 Same Origin Policy

协议，域名，端口三者必须都相同才算同源

Cookie

跨域资源共享 Cross-origin resource sharing

withCredentials

SameSite

## 参考

[浏览器同源政策及其规避方法](https://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)

[HTTP Cookie](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)

[跨域资源共享 CORS 详解](https://www.ruanyifeng.com/blog/2016/04/cors.html)

[Cookie 的 SameSite 属性](https://www.ruanyifeng.com/blog/2019/09/cookie-samesite.html)

[白帽子讲Web安全](https://book.douban.com/subject/10546925/)


## 遗留

Q: 跨域和安全有什么关系？

Q: 浏览器设置中的 Block third-party cookies 是用来追踪统计信息的？

Q: SameSite 的实测效果
