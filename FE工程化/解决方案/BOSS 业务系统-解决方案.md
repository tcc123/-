# 一、使用场景

xxx

# 二、技术栈

##     1、主要框架

​        **JS:** [Vue](https://cn.vuejs.org/)

​        **CSS:** [Element-ui](http://element-cn.eleme.io/#/zh-CN)

##    2、前端工程化方案

​        使用 [Webpack](http://wiki.iyunxiao.com/display/QTYFZX/Webpack) 作为前端工程化工具，实现启动开发服务、域名转发代理、打包发布等前端工程化流程;

配合使用 [Babel](http://wiki.iyunxiao.com/display/QTYFZX/Babel)、Sass、[Eslint](http://wiki.iyunxiao.com/display/QTYFZX/Eslint)、[Stylelint](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=platform&title=Stylelint) 等实现前端工程开发；

注：

- - 脚手架提供了 babel-polyfill 功能，如果不需要可以去除；
  - Eslint 采用的是 [Standard 标准](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=platform&title=Standard+标准)，不熟悉可以先去学习一下；

##    3、主要插件

​        [moment](http://momentjs.cn/)：时间日期处理类库；

​        [axios](https://github.com/axios/axios)：HTTP 请求库；

[lodash](http://www.css88.com/doc/lodash/): JavaScript 的实用工具库；

[js-cookie](https://github.com/js-cookie/js-cookie): cookie 操作类库；

根据需求可以灵活配置其他插件，[前端插件和类库列表](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=platform&title=前端插件和类库列表)；

##    4、单测方案

​        启动器： [Karma](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=platform&title=Karma)

测试框架：[Mocha](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=platform&title=Mocha)

断言库：[Chai](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=platform&title=Chai)

# 三、脚手架

脚手架基于 vue-cli2.0

\1. git 地址：https://git.iyunxiao.com/FE/vue-singlePage-scaffold-SSR

\2. 使用文档：[服务端单页面应用](http://wiki.iyunxiao.com/pages/createpage.action?spaceKey=platform&title=服务端单页面应用)

# 四、常见问题及解决方案

​    问题1 ：使用全局 filter

​              详细描述：希望在脚手架中使用 vue 的全局 filter；

​              原因：无

​              解决方案：

1. 在 /src 下面新建 filter 文件夹，然后按照官方规范使用书写全局 filter；
2. 在 /src/index.js 中将 filter 注册到 vue 的全局实例中； 