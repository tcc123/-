> 原文：https://www.jianshu.com/p/31ad2cef69d5（由于怕连接丢失，故复制整理）

## 缘起

关于get请求，不同浏览器间产生不同的问题，想必很多前端开发人员都遇到过。

常见的是ajax请求过一次以后，以后的相同 url 的 get 请求：

1. ​    case 1 有时返回304，有时返回200;
2. ​    case 2 有时无论后台数据是否变化始终返回304，有时却始终返回200；
3. ​    同一套代码在不同浏览器间结果不同；

## 禁止浏览器缓存数据网上常见的解决方案

1. ### 在html页面设置Meta标签



```js
`<``meta` `http-equiv``=``"Cache-Control"` `content``=``"no-store"``/> ``<``meta` `http-equiv``=``"Pragma"` `content``=``"no-cache"``/> ``<``meta` `http-equiv``=``"Expires"` `content``=``"0"``/>`
```

​     **结果：**因浏览器不同或者同一浏览器间版本不同，这个方法有很大的兼容性，很多时候根本没有作用。

### 2. 在ajax的 URL 参数后面加时间戳或者随机数

```js
`$.ajax({``  ``url: ``'http://localhost/api/list'``,``  ``type: ``'get'``,``  ``data: {``    ``_t: ``new` `Date().valueOf() ``//加时间戳``   ``//_t: Math.random() 加随机数``  ``},``  ``success: ``function` `( res ) {``    ``console.log(res);``  ``}``})`
```

​     **结果：**这种方式虽然能解决IE始终返回304的问题，但实际上每个ajax都会去请求服务器，对web优化并非最佳的解决方案。

### 3. 用post请求替代get请求

​    **结果：** 不推荐，一是因为这种做法不符合RESTful API设计，二是因为这种方式同样会每次请求服务器，没有真正利用到浏览器自带的缓存功能。

## 想要实现的目的

**首次请求返回200，数据不变的情况下，请求需和服务器确认返回304，如果有数据变化，则返回200，并且需要IE和google浏览器一致。**

## 浏览器缓存机制

![http](/Users/iyx/前端开发/FE知识库/HTTP 协议基础/http.png)
浏览器缓存主要由HTML Meta标签控制缓存和HTTP头信息控制缓存另外与用户行为也有关系，http头信息参数多，缺乏相关实践经验，理解起来复杂，难以记忆。大家可以参考[HTTP 缓存策略](http://wiki.iyunxiao.com/pages/viewpage.action?pageId=153452670)



## 针对客户端的测试

服务端：express提供接口，客户端：jquery ajax发起请求 ，抓包工具fiddler，仅测试 cache-control 和 Pragma

```js
`//服务端不设置响应头，普通输出，每次输出数据不变化``app.get(``'/list'``, (req, res, next)=>{``    ``Order.findAll({ where: { uuid: req.session.loginUser } }).then(result=>{``        ``res.send({code:0, msg: ``'获取成功'``, data: result});``    ``}).``catch``(err=>{``        ``res.send({code:-1, data: ``'获取失败'``});``    ``})``});``//客户端 设置请求头 cache-control 和 Pragma``$.ajax({``   ``url: ``'http://localhost/api/list'``,``   ``type: ``'get'``,``   ``headers: {``      ``//当只设置cacahe-control: 'no-cache'时，IE浏览器始终返回304，抓包工具抓不到包，请求不和服务器确认``      ``//当只设置cacahe-control: 'no-cache'时，google浏览器始终返回200，抓包工具可以抓取包，请求重新从服务器获取数据，没有利用到浏览器的缓存功能``      ``'cache-control'``: ``'no-cache'``,``      ``//当只设置Pragma: 'no-cache'时，IE浏览器始终返回200，抓包工具可以抓到所有包，请求重新从服务器获取数据，没有利用到浏览器的缓存功能``      ``//当只设置Pragma: 'no-cache'时，google浏览器始终返回200，抓包工具可以抓到所有包，请求重新从服务器获取数据，没有利用到浏览器的缓存功能``       ``'Pragma'``: ``'no-cache'``      ``//两个参数同时不设置时，IE浏览器始终返回304，抓包工具抓不到包，请求不和服务器确认``      ``//两个参数同时不设置时，google浏览器首次返回200，之后始终返回304，并且有和服务器确认``      ``//两个参数同时设置时，IE浏览器始终返回200，抓包工具可以抓到所有包，请求重新从服务器获取数据，没有利用到浏览器的缓存功能``      ``//两个参数同时设置时，google浏览器始终返回200，抓包工具可以抓到所有包，请求重新从服务器获取数据，没有利用到浏览器的缓存功能``   ``},``   ``success: ``function` `(res) {``      ``console.log(res);``   ``}``})`
```

​    **结果：**

1. 参数Pragma 设为"no-cache"时，IE和google每次从服务器获取数据，效果一致，始终不从缓存获取；
2. 参数cache-control设为"no-cache"时，对IE浏览器无效，对google浏览器效果不理想，并未到达目的。

**所以以上的方式以失败告终。**

## 针对服务端的测试

抛开请求头，设置响应头。客户端：jquery ajax发起请求，服务端 express提供接口，抓包工具fiddler，仅设置服务端 'Cache-Control'

```js
`//服务端，设置响应头 Cache-Control``app.get(``'/list'``, (req, res, next)=>{``    ``res.set( {``        ``'Cache-Control'` `: ``'max-age=5'`  `//便于测试，设置缓存时间5s；``    ``} );``    ``Order.findAll({ where: { uuid: req.session.loginUser } }).then(result=>{``        ``res.send({code:0, msg: ``'获取成功'``, data: result});``    ``}).``catch``(err=>{``        ``res.send({code:-1, data: ``'获取失败'``});``    ``})``});``//客户端，普通ajax，不做任何请求头设置`` ``$.ajax({``     ``url: ``'http://localhost/api/list'``,``     ``type: ``'get'``,``     ``headers: { },``     ``success: ``function` `(res) {``         ``console.log(res)；``     ``}``})`
```

###  IE浏览器测试结果

   IE浏览器响应头：

![1](/Users/iyx/前端开发/FE知识库/HTTP 协议基础/1.png)

   IE浏览器ajax请求：

![2](/Users/iyx/前端开发/FE知识库/HTTP 协议基础/2.png)

### google浏览器测试结果

   google浏览器响应头：

![3](/Users/iyx/前端开发/FE知识库/HTTP 协议基础/3.png)

   google浏览器ajax请求：

![4](/Users/iyx/前端开发/FE知识库/HTTP 协议基础/4.png)

## 结论：后端设置响应头缓存时间5s，前端不做设置。

1. IE浏览器在首次请求后，5s内每次请求从缓存获取，不和服务器确认，直接返回304，超过5s就和服务器确认，如果服务器数据改变则返回200，数据没有改变返回304；之后的请求以此类推。
2. googole 浏览器在首次请求后，5s内每次请求从缓存获取，不和服务器确认，网络面板显示 code 200 ,size from cache，超过5s就和服务器确认，如果服务器数据改变则返回200，数据没有改变返回304；之后的请求以此类推。

这个方法与我想要的效果一致。假如**希望每次都要和服务器确认，不直接从缓存获取，那么只需在后端设置cache-Control: 'max-age: 0'即可**。

注意：因ajax类库众多，像$jquery ajax, angular-resource, vue-resouce, axios, 原生ajax等，可能默认的请求头设置会与我上面的不一样，可能出现不一样的结果。后端也是同样的问题。