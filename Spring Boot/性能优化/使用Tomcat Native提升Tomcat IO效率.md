## 使用Tomcat Native提升Tomcat IO效率
[使用Tomcat Native提升Tomcat IO效率](https://cloud.tencent.com/developer/article/1767681)

### 简介
IO有很多种，从最开始的Block IO，到nonblocking IO，再到IO多路复用和异步IO，一步一步的将IO的性能提升做到极致。

今天我们要介绍一下怎么使用Tomcat Native来提升Tomcat IO的效率。

### Tomcat的连接方式
Tomcat中使用连接器来处理与外部客户端的通信。Connecter主要用来接受外部客户端的请求，并转交给处理引擎处理。

在Tomcat中有两种Connector。一种是 HTTP connector， 一种是AJP connector。

HTTP connector大家应该很好理解，它也是tomcat默认使用的连接器。

还有一个连接器叫做AJP，AJP主要是用来和web服务器进行通信用的，因为AJP协议的速度要比HTTP的快，所以AJP除了用来和其他webserver进行通信之外，还可以通过AJP来构建tomcat集群。

这两种方式都支持4中协议，分别是BIO，NIO，NIO2和APR。

[AJP协议总结与分析](https://developer.aliyun.com/article/269625)