## 安卓端：

#### 1、网页缓存：

在 data/ 应用p ackage下生成 database 与cache 两个文件夹，请求的Url记录是保存在 webviewCache.db 里，而url的内容是保存在 webviewCache 文件夹下。

**<1> 缓存构成**

```text
 /data/data/package_name/cache/
 /data/data/package_name/database/webview.db
 /data/data/package_name/database/webviewCache.db
```

**<2> 缓存模式**

- **LOAD_CACHE_ONLY**： 不使用网络，只读取本地缓存数据；
- **LOAD_DEFAULT**：根据 cache-control 决定是否从网络上取数据；
- **LOAD_CACHE_NORMAL**：API level 17中已经废弃, 从API level 11开始作用同- - LOAD_DEFAULT模式；
- **LOAD_NO_CACHE**: 不使用缓存，只从网络获取数据；
- **LOAD_CACHE_ELSE_NETWORK**，只要本地有，无论是否过期，或者no-cache，都使用缓存中的数据。

如果一个页面的 cache-control 为 no-cache，在模式 LOAD_DEFAULT 下，无论如何都会从网络上取数据，如果没有网络，就会出现错误页面；在 LOAD_CACHE_ELSE_NETWORK 模式下，无论是否有网络，只要本地有缓存，都使用缓存。本地没有缓存时才从网络上获取。如果一个页面的 cache-contro l为 max-age=60，在两种模式下都使用本地缓存数据。

#### 2、应用缓存

根据 setAppCachePath(String appCachePath) 提供的路径，在H5使用缓存过程中生成的缓存文件。

无模式选择，通过setAppCacheEnabled(boolean flag)设置是否打开。默认关闭，即，H5的缓存无法使用。如果要手动清理缓存，需要找到调用setAppCachePath(String appCachePath)设置缓存的路径，把它下面的文件全部删除就OK了。

## iOS端：

iOS 的 UIWebView 组件不支持 html5 应用程序缓存的方式，对于协议缓存，可以使用 sdk 中的 NSURLCache 类。NSURLRequest 需要一个缓存参数来说明它请求的url何如缓存数据的，我们先看下它的CachePolicy类型。

1. 1. NSURLRequestUseProtocolCachePolicy NSURLRequest 默认的 cache policy，使用 Protocol 协议定义，注意这种情况下默认缓存时间是 60s；
   2. NSURLRequestReloadIgnoringCacheData 忽略缓存直接从原始地址下载；
   3. NSURLRequestReturnCacheDataElseLoad 只有在cache中不存在data时才从原始地址下载；
   4. NSURLRequestReturnCacheDataDontLoad 只使用cache数据，如果不存在cache，请求失败；用于没有建立网络连接离线模式；
   5. NSURLRequestReloadIgnoringLocalAndRemoteCacheData：忽略本地和远程的缓存数据，直接从原始地址下载，与NSURLRequestReloadIgnoringCacheData类似；
   6. NSURLRequestReloadRevalidatingCacheData:验证本地数据与远程数据是否相同，如果不同则下载远程数据，否则使用本地数据；

处于数据安全性的考虑，IOS的应用拥有自己独立的目录，用来写入应用的数据或者首选项参数。应用安装后，会有对应的home目录，基于NSURLCache来实现数据的Cache，NSURLCache会存放在home内的子目录Library/ Caches下，以Bundle Identifier为文件夹名建立Cache的存放路径。在xcode下可以管理对应的文件，具体可以参见此文：[《关于 iOS 删除缓存的那些事儿》](https://link.zhihu.com/?target=http%3A//www.jianshu.com/p/e6d3f7c4baed)