### HTTP请求及服务端响应中的一些字段

#### （1）HTTP请求数据格式

![image-20210308115629439](F:\Git仓库\前端面试常考点\面试常考知识点总结\img\image-20210308115629439.png)

##### 	请求行：

​		1、请求方法，如get、post等

​		2、请求的URI

​		3、Http协议版本

##### 	请求头：

- Host：主机地址
- Cache-Control：缓存控制
- Cookie：

##### 	请求体：

#### （2）服务端响应数据格式

![](F:\Git仓库\前端面试常考点\面试常考知识点总结\img\Snipaste_2021-02-02_20-00-16.png)

##### 	响应行：

​		1、Http协议版本

​		2、状态码

##### 	响应头：

- Content-type：text/html(响应体是html文件) application/octet-stream(响应体是字节流类型，通常会被浏览器当做下载类型处理)  。 它主要是告诉浏览器响应体的类型，可能是html、下载类型等。
- 缓存机制：Last-Motified/Etag
- Location：浏览器需要重定向的主机地址
- Content-Encoding:gzip 压缩方式
- connection

##### 	响应体：

​		1、html

​		2、下载类型

### http请求中的keep-alive你了解吗？他有什么作用？

1. **作用**：使客户端到服务器端的**连接持续有效**，当出现对服务器的后继请求时，Keep-Alive功能避免了建立或者重新建立连接。Web服务器，基本上都支持HTTP Keep-Alive。
2. **缺点：**对于提供静态内容的网站来说，这个功能通常很有用。但是，对于负担较重的网站来说，虽然为客户保留打开的连接有一定的好处，但它同样影响了性能，因为在处理暂停期间，本来可以释放的资源仍旧被占用。当Web服务器和应用服务器在同一台机器上运行时，Keep- Alive功能对资源利用的影响尤其突出。
3. **解决：**Keep-Alive: timeout=5, max=100
   timeout：过期时间5秒（对应httpd.conf里的参数是：KeepAliveTimeout），max是最多一百次请求，强制断掉连接。就是在timeout时间内又有新的连接过来，同时max会自动减1，直到为0，强制断掉。

