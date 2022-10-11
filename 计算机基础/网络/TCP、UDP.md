# TCP与UDP
---
## TCP与UDP的区别
1. **TCP是面向连接的**，在发送消息之前需要建立连接，消息发送完要断开连接。TCP 在传递数据之前，会有三次握手来建立连接，而且在数据传递时，有确认、窗口、重传、拥塞控制机制。通过 TCP 连接传输的数据，无差错、不丢失、不重复、并且按序到达。**所以TCP可以保证消息的可靠性**<br>**UDP**在传输之前不需要建立连接，UDP不能保证数据的可靠性
2. **TCP提供有状态服务**，TCP会记录自己发送消息的状态，维持复杂的连接状态表；**UDP是无状态服务**，发送出消息后不会进行其他操作；
3. 由于TCP多了连接，确认，重传步骤，所以效率比UDP低很多
4. TCP面向字节流，UDP面向报文
5. 首部开销 ：TCP 首部开销（20 ～ 60 字节）比 UDP 首部开销（8 字节）要大。
6. 是否提供广播或多播服务 ：TCP 只支持点对点通信，UDP 支持一对一、一对多、多对一、多对多；

## TCP与UDP的应用场景
* UDP 一般用于即时通信：语音、视频、直播。这些场景对数据准确性要求不是特别高，比如你看视频即使少个一两帧，实际给人的感觉区别也不大。
* TCP 用于对传输准确性要求特别高的场景，比如文件传输、发送和接收邮件、远程登录等

## 使用 TCP 的协议有哪些?使用 UDP 的协议有哪些?

### 运行于 TCP 协议之上的协议 ：

* HTTP 协议 ：超文本传输协议（HTTP，HyperText Transfer Protocol)主要是为 Web 浏览器与 Web 服务器之间的通信而设计的。当我们使用浏览器浏览网页的时候，我们网页就是通过 HTTP 请求进行加载的。
* HTTPS 协议 ：更安全的超文本传输协议(HTTPS,Hypertext Transfer Protocol Secure)，身披 SSL 外衣的 HTTP 协议
* FTP 协议：文件传输协议 FTP（File Transfer Protocol），提供文件传输服务，基于 * TCP 实现可靠的传输。使用 FTP 传输文件的好处是可以屏蔽操作系统和文件存储方式。
* SMTP 协议：简单邮件传输协议（SMTP，Simple Mail Transfer Protocol）的缩写，基于 TCP 协议，用来发送电子邮件。注意 ⚠️：接受邮件的协议不是 SMTP 而是 POP3 协议。
* POP3/IMAP 协议： POP3 和 IMAP 两者都是负责邮件接收的协议。
Telent 协议：远程登陆协议，通过一个终端登陆到其他服务器。被一种称为 SSH 的非常安全的协议所取代。
* SSH 协议 : SSH（ Secure Shell）是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。SSH 建立在可靠的传输协议 TCP 之上。
......

### 运行于 UDP 协议之上的协议 ：

* DHCP 协议：动态主机配置协议，动态配置 IP 地址
* DNS ： 域名系统（DNS，Domain Name System）将人类可读的域名 (例如，www.baidu.com) 转换为机器可读的 IP 地址 (例如，220.181.38.148)。 我们可以将其理解为专为互联网设计的电话薄。实际上 DNS 同时支持 UDP 和 TCP 协议。

## TCP的三次握手与四次挥手

### 建立连接--三次握手
![三次握手](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/cs-basics/network/tcp-shakes-hands-three-times.png)
1. 一次握手:客户端发送带有 SYN（SEQ=x） 标志的数据包 -> 服务端，然后客户端进入 SYN_SEND 状态，等待服务器的确认；（server：确认client的发送正常）
2. 二次握手:服务端发送带有 SYN+ACK(SEQ=y,ACK=x+1) 标志的数据包 –> 客户端,然后服务端进入 SYN_RECV 状态（client确认自己的发送、接收正常，对方的发送、接受正常；server确认对方发送正常，自己接受正常）
3. 三次握手:客户端发送带有带有 ACK(ACK=y+1) 标志的数据包 –> 服务端，然后客户端和服务器端都进入ESTABLISHED 状态，完成TCP三次握手。（双方确认自己的接收、发送正常，可以建立连接）

### 断开连接--四次挥手
 ![四次挥手](https://guide-blog-images.oss-cn-shenzhen.aliyuncs.com/github/javaguide/cs-basics/network/tcp-waves-four-times.png)

 * **第一次挥手** ：客户端发送一个 FIN（SEQ=X） 标志的数据包->服务端，用来关闭客户端到服务器的数据传送。然后，客户端进入 FIN-WAIT-1 状态。
 * **第二次挥手** ：服务器收到这个 FIN（SEQ=X） 标志的数据包，它发送一个 ACK （SEQ=X+1）标志的数据包->客户端 。然后，此时服务端进入CLOSE-WAIT状态，客户端进入FIN-WAIT-2状态。
 * **第三次挥手** ：服务端关闭与客户端的连接并发送一个 FIN (SEQ=y)标志的数据包->客户端请求关闭连接，然后，服务端进入LAST-ACK状态。
 * **第四次挥手** ：客户端发送 ACK (SEQ=y+1)标志的数据包->服务端并且进入TIME-WAIT状态，服务端在收到 ACK (SEQ=y+1)标志的数据包后进入 CLOSE 状态。此时，如果客户端等待 2MSL 后依然没有收到回复，就证明服务端已正常关闭，随后，客户端也可以关闭连接了。

 #### 为什么第四次挥手客户端需要等待 2*MSL（报文段最长寿命）时间后才进入 CLOSED 状态?
 
 第四次挥手时，客户端发送给服务器的 ACK 有可能丢失，如果服务端没有因为某些原因而没有收到 ACK 的话，服务端就会重发 FIN，如果客户端在 2*MSL 的时间内收到了 FIN，就会重新发送 ACK 并再次等待 2MSL，防止 Server 没有收到 ACK 而不断重发 FIN。
>MSL(Maximum Segment Lifetime) : 一个片段在网络中最大的存活时间，2MSL 就是一个发送和一个回复所需的最大时间。如果直到 2MSL，Client 都没有再次收到 FIN，那么 Client 推断 ACK 已经被成功接收，则结束 TCP 连接。
