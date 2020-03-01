对于“静态多页面应用”这个脚手架的项目结构，bulid 和 config 这两个目录的解释同单页面应用的 [build 目录和 config 目录讲解](http://wiki.iyunxiao.com/pages/viewpage.action?pageId=175177788)；

本文主要讲解不同之处：

## webpack.base.conf.js

webpack.base.conf.js 相比较单页面应用的基础配置，需要注意的是，entry 的变化（相对于大家了解的单页面应用）：

```js
entry: utils.getEntries('./src/modules/**/app.js')
```

## utils.js

utils.js 文件中除了：**assetsPath、cssLoaders、styleLoaders、createNotifierCallback 四个方法外，新增加 getEntries**。

- - assetsPath——返回不同环境下的static目录位置；
  - cssLoaders——为不同的css预处理器提供一个统一的生成方式；
  - styleLoaders——为那些独立的style文件创建加载器配置；
  - createNotifierCallback——以类似浏览器的通知的形式展示信息；
  - **getEntries——根据路径获取入口对象；**

## webpack.dev.conf.js、webpack.prod.conf.js

两个 webpack 配置文件增加了对多入口不同模板的解析处理；

```js
...
const devWebpackConfig = merge(baseWebpackConfig, {
  module: { ... },
  devtool: config.dev.devtool,
  devServer: { ... },
  plugins: [ ... ]
})
 
 
// 多页面中模板文件处理
var pages = utils.getEntries('./src/modules/**/*.html')
for (var page in pages) {
    // 配置生成的html文件，定义路径等
    var conf = {
        filename: page + '.html',
        template: pages[page], //模板路径
        inject: true,
        chunks: ['vendor', page]
    }
    // 需要生成几个html文件，就配置几个HtmlWebpackPlugin对象
    devWebpackConfig.plugins.push(new HtmlWebpackPlugin(conf))
}
 
module.exports = new Promise((resolve, reject) => { ... })
```

## config、vue-loader.conf、check-version.js、bulid.js

这几个文件没有变化；