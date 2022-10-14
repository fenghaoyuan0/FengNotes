# HTTP
---
## 从输入URL到页面展示发生了什么
![](https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/2019-11/url%E8%BE%93%E5%85%A5%E5%88%B0%E5%B1%95%E7%A4%BA%E5%87%BA%E6%9D%A5%E7%9A%84%E8%BF%87%E7%A8%8B.jpg)
总体来说分为一下几个过程：<br>
1. DNS解析
2. 建立TCP连接
3. 发送HTTP请求
4. 服务器处理HTTP请求并返回报文
5. 浏览器解析渲染页面
6. 关闭连接

## HHTP状态码
301 Permanently Moved 被请求的资源己永久移动到新位置，新的URL在Location头中给出，浏览器应该自动地访问新的URL。

302Found 请求的资源现在临时从不同的URL响应请求。301是永久重定向，而302是临时重定向。

2000K 表示从客户端发来的请求在服务器端被正确处理

304 Not Modified 304状态码是告诉浏览器可以从缓存中获取所请求的资源。

400 bad request 请求报文存在语法错误

403 forbidden 表示对请求资源的访问被服务器拒绝

404 not found 表示在服务器上没有找到请求的资源

500 internal sever error 表示服务器端在执行请求时发生了错误

503 service unavailable 表明服务器暂

## HTTP和HHTTPS的区别
* **端口号**：HTTP是8080，HTTPS是443
* **URL前缀**：HTTP是 http：//，HTTPS是 https：//
* **安全性和资源消耗** ： HTTP 协议运行在 TCP 之上，所有传输的内容都是明文，客户端和服务器端都无法验证对方的身份。HTTPS 是运行在 SSL/TLS 之上的 HTTP 协议，SSL/TLS 运行在 TCP 之上。所有传输的内容都经过加密，**加密采用对称加密，但对称加密的密钥用服务器方的证书进行了非对称加密**。所以说，HTTP 安全性没有 HTTPS 高，但是 HTTPS 比 HTTP 耗费更多服务器资源。

## HTTPS为什么采用混合加密机制
