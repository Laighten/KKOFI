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
- connection

##### 	响应体：

​		1、html

​		2、下载类型