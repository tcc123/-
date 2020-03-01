HTTP协议，即超文本传输协议(Hypertext transfer protocol)。是一种详细规定了浏览器和万维网(WWW = World Wide Web)服务器之间互相通信的规则，通过因特网传送万维网文档的数据传送协议。

HTTP协议是用于从WWW服务器传输超文本到本地浏览器的传送协议。它可以使浏览器更加高效，使网络传输减少，从而保证计算机正确快速地传输超文本文档。



> ## 特点：

- HTTP协议永远都是客户端发起请求，服务器回送响应
- 支持b/s(浏览器/服务器模式),c/s(客户机/服务器模式)
- 简单快速：服务器当客户端请求服务时，只需传送请求方法和路径。请求方法常用的GET，HEAD，POST。每种方法规定了客户端与服务器联系的是不同的类型
- 灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。
- 无连接：限制每次连接只处理一个请求，服务器处理完客户的请求，并收到客户的应答后，即断开连接
- 无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传

注：http1.1之后可通过设置请求头字段connection:keep-alive(http1.1默认开启)，使用持久链接，即在一个请求结束后的指定时间内，只要任意一端没有明确提出断开连接，则保持连接状态。之后的请求将无需在重新建立新的连接，提高传输效率



> ## 请求方法

HTTP1.1之前仅支持：GET,POST,HEAD三种http请求方法。

HTTP/1.1新增了：OPTIONS、PUT、DELETE、TRACE、CONNECT五种HTTP请求方法。

| 方法    | 说明                                                         |
| :------ | :----------------------------------------------------------- |
| GET     | 请求指定的页面信息，并返回实体主体                           |
| POST    | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中 |
| PUT     | 从客户端向服务器传送的数据取代指定文档的内容                 |
| HEAD    | 用于获取报头                                                 |
| DELETE  | 请求服务器删除指定的文件                                     |
| OPTIONS | 允许客户端查看服务器的性能                                   |
| TRACE   | 回显服务器收到的请求，主要用于测试或诊断                     |
| CONNECT | HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器       |

*图表来源：《图解http》*



> ## 统一资源标识符(URI)与统一资源定位符(URL)

URI(Uniform Resource Identifier)，用于标示互联网上的一个资源(就像我们每个人的身份证)，URI通常由三部分构成：

- 访问资源的命名机制
- 存放资源的主机名
- 资源自身的名称，由路径表示

URL(Uniform Resource Locator) 地址用于描述一个网络上的资源的地址(就像我们的家庭住址)， 

- 第一部分是协议(或称为服务方式)。
- 第二部分是存有该资源的主机IP地址(有时也包括端口号)。
- 第三部分是主机资源的具体地址，如目录和文件名等。

注：URL是一种URI，它标识一个互联网[资源](https://zh.wikipedia.org/wiki/资源)，并指定对其进行操作或获取该资源的方法



> ## 工作流程

一次HTTP操作称为一个事务，其工作过程可分为四步：

1. 客户端与服务器建立连接

2. 建立连接后，客户端向Web服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据4部分组成。

3. 服务器接到请求后，给予相应的响应信息，其格式为一个状态行，包括信息的协议版本号、一个成功或错误的代码，后边是MIME信息包括服务器信息、实体信息和可能的内容。

4. 若connection 模式为close，则服务器主动关闭TCP 连接，客户端被动关闭连接，释放TCP 连接;若connection 模式为keepalive，则该连接会保持一段时间，在该时间范围内可以继续接收请求。

   

> ## HTTP请求消息Request

客户端发送一个HTTP请求到服务器的请求消息包括以下格式：

- 请求行(request line)
- 请求头部（header）
- 空行
- 请求数据

```

GET /app/admin/user/get_by_username?username=admin HTTP/1.1``Host    testz.haofenshu.com``Pragma  no-cache``Cache-Control   no-cache``Accept  application/json, text/plain,``User-Agent  Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/67.0.3396.99 Safari/537.36``Referer http:``//testz.haofenshu.com/trainer/trainerWork``Accept-Encoding gzip, deflate``Accept-Language zh-CN,zh;q=0.9,fr;q=0.8,en;q=0.7``Cookie  __AIFUDAOID__=eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoiNWIxMTdiYzMwMDAwNDFhMDIzNGQ2ZGQ4IiwibmFtZSI6ImFkbWluIiwidXNlcm5hbWUiOiJhZG1pbiIsInJvbGVzIjpbIkFkbWluIiwiSGVhZHRlYWNoZXJNYW5hZ2VyIl0sInZlcnNpb24iOiIxLjAuMCJ9.l_zotV0W5KpU1MZJDkmPMnNGc0TNKlTmYO_ryNPCJSM``Connection  keep-alive
```



1. 第一部分：请求行，第一行说明了是post请求，以及http1.1版本。

2. 第二部分：请求头部，第2行至第11行。

3. 第三部分：空行，第12行的空行。

4. 第四部分：请求数据（该数据为空）

   

> ## HTTP响应消息response

HTTP响应也由四个部分组成，分别是：

- 状态行
- 消息报头
- 空行
- 响应正文

```

HTTP/1.1 200 OK``Server: nginx``Date: Tue, 17 Jul 2018 11:43:31 GMT``Content-Type: application/json; charset=utf-8``Content-Length: 164``X-Powered-By: 4.14.0``Access-Control-Allow-Origin: http:``//testcc.haofenshu.com``Access-Control-Allow-Credentials: ``true``Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, Accept``Access-Control-Allow-Methods: PUT,POST,GET,DELETE,OPTIONS``ETag: W/``"a4-vm/VzDz8VnjIRL9Voy+dfakv0HY"``Proxy-Connection: keep-alive` `{``"username"``:``"admin"``,``"user_id"``:``"5b117bc3000041a0234d6dd8"``,``"name"``:``"admin"``}
```



- 第一部分：状态行，由HTTP协议版本号， 状态码， 状态消息 三部分组成。
- 第二部分：消息报头，用来说明客户端要使用的一些附加信息
- 第三部分：空行，消息报头后面的空行是必须的
- 第四部分：响应正文，服务器返回给客户端的文本信息。

注：无论是否有响应正文(数据)或请求正文(数据)，空行必须存在，空行用于说明头部到此为止



> ## HTTP状态码

- 1xx：指示信息--表示请求已接收，继续处理
- 2xx：成功--表示请求已被成功接收、理解、接受
- 3xx：重定向--要完成请求必须进行更进一步的操作
- 4xx：客户端错误--请求有语法错误或请求无法实现
- 5xx：服务器端错误--服务器未能实现合法的请求

注：304虽为3xx，但其真正的含义跟重定向并没有什么关系



> ## HTTP常见请求头字段及含义

- Accept：用于告诉服务器，客户端支持的数据类型

- Accept-Charset：用于告诉服务器，客户端采用的编码格式和相对优先级

- Accept-Encoding：用于告诉服务器，客户端支持的数据压缩格式

- Accept-Language：客户端的语言环境

- Host：告诉服务器要访问的主机名

- If-Modified-Since：告诉服务器资源的缓存时间，若时间过期且请求资源未更新，则不接受请求，返回304，从缓存中取文件。否则接受请求，返回新文件

- Referer：告诉服务器它是从哪个资源(URI)来访问服务器的

- User-Agent：告诉服务器浏览器和用户代理名称等信息。

- Cookie：客户端向服务器请求时携带的cookie。

- Connection：处理完这次请求后是否立即断开连接

- Date：当前时间值

  

> ## HTTP常见响应头字段及含义

- Location：配合302状态码使用，用于告诉客户找谁。
- Server：告诉客户端服务器安装的HTTP应用服务器类型。
- Allow: 告诉客户端支持的所有http方法
- Content-Encoding：告诉客户端数据的压缩格式。
- Content-Length：告诉客户端回送数据的大小
- Content-length: 范围请求，允许服务器只发送数据的一部分到客户端。
- Content-Type：告诉客户端回送数据的类型
- Last-Modified：告诉客户端当前资源的最终修改时间
- Refresh：告诉客户端隔多久刷新一次
- Content-Disposition：告诉客户端以下载方式打开数据
- set-Cookie: 告诉客户端要设置的cookie信息
- Transfer-Encoding：告诉客户端数据的传送格式
- ETag：缓存唯一标示
- Expires：告诉客户端把回送的资源缓存多长时间 -1或0则是不缓存
- Cache-Control：控制客户端是否缓存数据
- Pragma： 控制客户端是否缓存数据

注：

1. Cache-Control:no-cache为不缓存过期的资源，即每次使用缓存文件前都要向服务器进行确认，防止返回过期资源。no-store为真正的不缓存资源。
2. ETag有强ETag和弱ETag之分，强ETag无论被缓存实体发生多么细微的变化都会改变其值，弱ETag只有被缓存实体发生根本变化，产生差异时才会改变，会在字段最开始处附加W/，上边响应头示例中即为弱ETag值。

------

> ## 总结

那么，我们前端了解http相关的知识有哪些实用价值呢？

- 关于cookie：由于http无状态的特性，验证状态需要使用cookie或session(这里只说cookie)，cookie在被设置后，会在客户端和服务端来回传递，对于大多数请求来说验证cookie是必要的，但是多数静态资源，如图片，css这些，这些静态资源是不需要验证cookie的，携带多余的cookie显然造成了一些浪费，这时候我们就可以使用代理服务器(cdn)托管静态资源(cookie在同一域才会下生效)，避免请求静态资源时传递多余的cookie(虽然cdn真正的优势并不在此，此方式优化资源请求时间也有些微不足道)
- 减少http请求：静态资源的整合，如合并css，js文件，使用css sprite等，由于http请求无连接的特性，即一个请求结束后立马断开连接，每次建立连接的过程都会耗费额外的时间，合并请求可以减少这些消耗。

> ## 一些疑惑

- 设置connection:keep-alive的情况下为持久连接，那么意味着许多静态资源的请求不会在建立新的连接，这种情况下还有必要使用合并静态资源，css sprite等方案进行优化吗？
- Cache-Control:no-cache 即然还会设置浏览器缓存，那么浏览器强制刷新又是如何确保无缓存的呢？

参考连接：

MDN： https://developer.mozilla.org/zh-CN/docs/Web/HTTP

一篇比较全的HTTP协议详解：http://caibaojian.com/http-protocol.html

由浅入深看http：https://www.cnblogs.com/guguli/p/4758937.html

学习http协议，一篇就够了https://juejin.im/entry/57ff5c5b0bd1d00058e5b2aa