在开发过程中，我们不可能保证所写的页面代码很完美，所以遇到问题可能就会进行代码调试。

下面为大家找了几篇文章，针对浏览器，编译器等对代码的调试方法，有不全面的地方也欢迎大家补充修改~

## Chrome 调试 bug

  在我们公司的开发中，最常用的就是谷歌浏览器，ie 什么的暂不考虑

- chrome浏览器调试参考：[chrome浏览器-调试及插件](http://wiki.iyunxiao.com/pages/viewpage.action?pageId=161448230)
- 下面是断点相关的调试方法：http://blog.csdn.net/u014727260/article/details/53231298

​                                                         [ https://segmentfault.com/a/1190000008396389](https://segmentfault.com/a/1190000008396389)

​                                                         [http://www.cnblogs.com/wangzhenhai/p/6531885.html ](http://www.cnblogs.com/wangzhenhai/p/6531885.html)

- 调试技巧参考：http://blog.csdn.net/houfeng30920/article/details/53776448

 

## 编辑器调试

  在对应代码位置写上debugger，然后刷新浏览器页面，会出现断点调试页面，如同浏览器打断点

- webstrom：http://blog.csdn.net/sujun10/article/details/54139560
- vscode：在网上看了几篇文章，有对angular-cli的调试，有对node的调试，虽然不是vue，但是大家参考参考

​                         对angular-cli： http://blog.csdn.net/crper/article/details/60957348

​                         对node： http://cnodejs.org/topic/58f376fec749f63d48fe9548

​                         断点调试：http://blog.csdn.net/arvin0/article/details/53673351

 

## 移动端手机调试

  一般chrome浏览器对手机调试很友好，方便模拟各分辨率大小的屏幕，通过调整屏幕大小，看页面样式是否混乱及兼容。

- 参考：[H5 调试](http://wiki.iyunxiao.com/pages/viewpage.action?pageId=157123080)、[H5 调试2](http://wiki.iyunxiao.com/pages/viewpage.action?pageId=157123163)
- 参考：http://blog.csdn.net/houfeng30920/article/details/53776448

 

## 微信项目调试

  微信项目开发可以参考：[微信服务号开发总结](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=QTYFZX&title=微信服务号开发指南&linkCreation=true&fromPageId=161448228)

  首先下载安装微信开发者工具：https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1455784140

  微信扫描授权登录（需先关注项目公众号）

  修改本地hosts

  sudo npm run start命令启动项目后，复制地址到微信开发者工具，打开，就能看到页面了，详细功能操作参照上面地址中内容

  ps：如果打开的页面是乱码或者非本项目页面，你可以查看一下端口是否被占用，kill了之后还是乱码，重复操作几次，实在不行，关机重启

