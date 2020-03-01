什么是cookie？

​        一语胜千言：cookie是由服务器端生成，发送给User-Agent,浏览器会将cookie的key/value保存到某个目录下的文本文件内，下次请求同一网站时就发送该Cookie给服务器。由此可见cookie作为http协议的一个扩展，在请求接口的时候将保存在在客户端的记录信息发送给服务端以确定用户身份。而且因为cookie是保存在本地浏览器中的，所以就扩展出了另一个用法：在同域名下不同页面间传递信息，作用与localStorage和sessionStorage一样，但是后两种方法在功能上比cookie强大得多，而且操作也更简单，所以这个功能基本上没人再使用。虽然第一个功能相对来说有点复杂，但是这是一个合格前端的必备技能。

## cookie在http请求中是怎么用的？

​       具体来说就是当用户使用浏览器登陆个人账号的时候，浏览器将用户名及密码提交至服务器；接着，服务器验证通过后会在向客户端回传相应超文本的同时发回一个作为通行证的特殊信息（这个信息就是cookie），当然这些信息并不是存放在HTTP响应体（Response Body）中的，而是存放于HTTP响应头（Response Headers）中，再具体一点是在set-cookie中；当客户端浏览器接收到来自服务器的响应之后，浏览器会将这些信息存放在一个统一的位置；自此，客户端再向服务器发送请求的时候，都会把相应的cookie再次发回至服务器。而这次，Cookie信息则存放在HTTP请求头（Response Headers）了。有了Cookie这样的技术实现，服务器在接收到来自客户端浏览器的请求之后，就能够通过分析存放于请求头的Cookie得到客户端特有的信息，从而动态生成与该客户端相对应的内容。这个过程中的cookie操作都是浏览器自动执行的，其中包括将Response Headers里的Set-Cookie保存在本地，在访问domain域名下path范围内的接口时将cookie放入Request Headers中。来个图片展示一下：

![WechatIMG7 1](/Users/iyx/前端开发/FE日常分享/WechatIMG7 1.png)

​        再结合一个例子补充一下cookie这张通行证上的信息以及用法。首先先去找个网站登陆一下，以获取cookie。__AIFUDAOID__=eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoiNWIxMTdiYzMwMDAwNDFhMDIzNGQ2Z

GQ4IiwibmFtZSI6ImFkbWluIiwidXNlcm5hbWUiOiJhZG1pbiIsImlzX2ZpcnN0X2xvZ2luIjpmYWxzZSwicm9sZXMiOlsiQWRtaW4iLCJTdXBlckFkbWluIl0sInZlcnNpb24iOiIxLjAuMCJ9.hbtpYUOd-n7yOKk7HoOTE-GJFxwtF3oS7ZUdopzDwRQ; Max-Age=86400; Domain=.[haofenshu.com](http://haofenshu.com/); Path=/; Expires=Fri, 11 Jan 2019 07:42:54 GMT，首先将它根据key=value的形式拆分：

| __AIFUDAOID__ | eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoiNWIxMTdiYzMwMDAwNDFhMDIzNGQ2ZGQ4IiwibmFtZSI6ImFkbWluIiwidXNlcm5hbWUiOiJhZG1pbiIsImlzX2ZpcnN0X2xvZ2luIjpmYWxzZSwicm9sZXMiOlsiQWRtaW4iLCJTdXBlckFkbWluIl0sInZlcnNpb24iOiIxLjAuMCJ9.hbtpYUOd-n7yOKk7HoOTE-GJFxwtF3oS7ZUdopzDwRQ |
| ------------- | ------------------------------------------------------------ |
| Max-Age       | 86400                                                        |
| Domain        | .[haofenshu.com](http://haofenshu.com/)                      |
| Path          | /                                                            |
| Expires       | Fri, 11 Jan 2019 07:42:54 GMT                                |

从上面的表格就可以很清楚的知道这个cookie通行证上的信息：首先是通行证的名及值（别问我这个值是怎么生成的，这已经触及到我的知识盲点了），这个是cookie的主体部分，其他的都是一些限制属性，Max-Age是有效时间，即在86400秒内这个cookie是有效的，Domain是有效范围，这个cookie只有在[haofenshu.com](http://haofenshu.com/)的域名下才能生效，记住一定是以“.”开头的，Path是生效路径，“/”表示在所有路径里都生效，最后的Expires是cookie有效期，在2019.1.11 07:42:54这个时间点之前这个cookie都是存在的（在有Max-Age的情况下，优先认可Max-Age里的值）。在服务端返回这个cookie后，除了名值对是需要放在请求头里作为验证信息传递给服务端外，其他信息都是浏览器自己使用的。

##  在接口请求中引入cookie的原因 

​       在接口请求中引入cookie的原因。虽然现在出现了很多传输协议，但是绝大多数的Web应用程序还是使用HTTP协议传输数据的。HTTP协议是无状态的协议。一旦数据交换完毕，客户端与服务器端的连接就会关闭，再次交换数据需要建立新的连接。也就是说任何两个链接都是独立不相关的，举例来说就是，我在一个网站登陆后，其他的接口在不手动传入这个账号信息的情况下是不知道当前登陆信息的。而cookie的产生弥补HTTP协议无状态的不足，它把单个用户的所有请求信息链接了起来。http的响应头部和请求头部是专门负责设置以及发送cookie的,它们分别是Set-Cookie以及Cookie。当服务器返回给客户端一个http响应信息时，其中如果包含Set-Cookie这个头部时，意思就是指示客户端建立一个cookie，并且在后续的http请求中自动发送这个cookie到服务器端，直到这个cookie过期。

## cookie的组成部分

​      说到这里cookie的作用以及用法在理论上也就讲清楚了。然后再补充一下cooki的组成部分的官方解释：

| name(String)value(String) | name是一个唯一确定的cookie名称，通常来讲cookie的名称是不区分大小写的value是存储在cookie中的字符串值。最好为cookie的name和value进行url编码 |
| ------------------------- | ------------------------------------------------------------ |
| domain(String)            | cookie对于哪个域是有效的。所有向该域发送的请求中都会包含这个cookie信息。 |
| path(String)              | 表示这个cookie影响到的路径，浏览器跟会根据这项配置，像指定域中匹配的路径发送cookie。 |
| expires                   | 失效时间，表示cookie何时应该被删除的时间戳(也就是，何时应该停止向服务器发送这个cookie)。如果不设置这个时间戳，浏览器会在页面关闭时即将删除所有cookie；不过也可以自己设置删除时间。这个值是GMT时间格式，如果客户端和服务器端时间不一致，使用expires就会存在偏差。 |
| max-age(Int)              | 与expires作用相同，用来告诉浏览器此cookie多久过期（单位是秒），而不是一个固定的时间点，如果为负数，该Cookie为临时Cookie，关闭浏览器即失效，浏览器也不会以任何形式保存该Cookie。如果为0，表示删除该Cookie。默认为–1。正常情况下，max-age的优先级高于expires。 |
| HttpOnly                  | 告知浏览器不允许通过脚本document.cookie去更改这个值，同样这个值在document.cookie中也不可见。但在http请求张仍然会携带这个cookie。注意这个值虽然在脚本中不可获取，但仍然在浏览器安装目录中以文件形式存在。这项设置通常在服务器端设置。 |
| secure(Boolean)           | 安全标志，指定后，只有在使用SSL链接时候才能发送到服务器，如果是http链接则不会传递该信息。就算设置了secure 属性也并不代表他人不能看到你机器本地保存的 cookie 信息，所以不要把重要信息放cookie就对了服务器端设置 |