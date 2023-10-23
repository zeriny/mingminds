## 网络空间安全学术论坛

智能生活的安全极客



HTTPS劫持技术

二维码劫持

- 浏览器允许加载mixed passive content
- 二维码登录、支付将不再安全

ssl stripping

- 攻击初始HTTP，劫持第一个明文的攻击
- 但是这类攻击局限性很大，要求用户原始的请求是HTTP的。并且后续都是在http的环境下进行，受害者很容易察觉。
- HSTS策略下，攻击者就没有攻击条件了

SSL Stripping2

- 浏览器不存在hsts缓存、hsts preload
- 第一个请求为HTTP

SSLStripping+

- 在任意时刻将https切换为http
- 无须其他明文的辅助条件





