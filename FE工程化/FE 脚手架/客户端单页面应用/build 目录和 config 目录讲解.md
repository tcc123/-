对于“静态单页面应用”这个脚手架的项目结构，主要的也是最重要的在于 bulid 和 config 这两个目录。

bulid 是项目构建的相关代码，config 是项目开发环境配置。

接下来就先从**webpack.base.conf.js**开始依次介绍 build 和 config 两个目录下的相关功能。

## webpack.base.conf.js

webpack.base.conf.js 是 webpack 的基础配置，是 dev 和 prod 的公共配置文件。

```js
const path = require('path')
const utils = require('./utils')
const config = require('../config')
const vueLoaderConfig = require('./vue-loader.conf')
```

- path——该模块提供了一些用于处理文件路径的小工具
- utils——给整个CLI提供方法
- config——开发环境的配置
- vueLoaderConfig——分析是否是生产环境，然后将根据不同的环境来加载配置功能

在这个文件里一共实现了两个方法：一是合并path路径的，另一个是创建Eslint的Rules。

而剩余部分就是 webpack 的基础配置，这里简化了 webpack 结构，简化的结果其实就是 webpack 的一个骨架，如果在配置上遇到问题，可去webpack查证。

```js
...
module.exports = {
  entry: {}, // 编译入口文件
  output: {}, // 编译输出路径
  resolve: {}, // 一些解决方案配置
  module: {
    // 不同类型文件加载器配置
    rules: []
  },
  ...
  // 这些选项用于配置polyfill或mock某些node.js全局变量和模块。
  // 这可以使最初为nodejs编写的代码可以在浏览器端运行
  node: {...}
}
```

关于path有兴趣的可前往 node 学习，接下来重点介绍下 utils.js，config 和 vue-loader.conf。

## utils.js

utils.js文件中总共实现了4个方法：**assetsPath、cssLoaders、styleLoaders、createNotifierCallback**。

- assetsPath——返回不同环境下的static目录位置
- cssLoaders——为不同的css预处理器提供一个统一的生成方式
- styleLoaders——为那些独立的style文件创建加载器配置
- createNotifierCallback——以类似浏览器的通知的形式展示信息

## config

config关键文件是index.js。

这个文件是开发环境和生产环境的基本配置。

在这个文件里开发者可在dev设置开发环境的静态路径、本地服务器配置项、Eslint、SourceMaps和代理，也可在build设置生产环境是否开启gzip压缩，以及压缩后缀名的设置等。

```js
...
module.exports = {
  dev: {...},
  build: {...}
}
```

## vue-loader.conf

这个文件的内容相对比较少。首先，vue文件中的css loader将在生产环境下把css文件抽取到一个独立的文件中；其次是根据不同的环境，引入不同的source map配置文件；最后设置是否开启缓存破坏。

```js
'use strict'
const utils = require('./utils')
const config = require('../config')
const isProduction = process.env.NODE_ENV === 'production'
const sourceMapEnabled = isProduction
  ? config.build.productionSourceMap
  : config.dev.cssSourceMap
 
 
module.exports = {
  loaders: utils.cssLoaders({
    sourceMap: sourceMapEnabled,
    extract: isProduction
  }),
  cssSourceMap: sourceMapEnabled,
  cacheBusting: config.dev.cacheBusting,
  transformToRequire: {
    video: ['src', 'poster'],
    source: 'src',
    img: 'src',
    image: 'xlink:href'
  }
}

```

关于 webpack 公共配置讲完了，接下来我们就一起学习下在dev和prod环境各自的配置吧。

## webpack.dev.conf.js

这个文件引入了webpack-merge，意在将公共配置文件和dev配置合并。从代码里我们可以发现，dev环境又新增了一些配置项。

- 给独立的style文件添加了sourceMap功能，有了它，出错的时候，除错工具将直接显示原始代码，而不是转换后的代码。
- 引入devtool。
- 配置devServer，包括热部署、代理、启动程序的时候自动在浏览器打开主页面等。
- 新增一些插件，包括热替换、webpack.NamedModulesPlugin在热加载时直接返回更新文件名、html-webpack-plugin生成html文件等。

最后一个函数是为了确保启动程序时，如果端口被占用时，会通过portfinder来发布新的端口。

```js
...
const devWebpackConfig = merge(baseWebpackConfig, {
  module: { ... },
  devtool: config.dev.devtool,
  devServer: { ... },
  plugins: [ ... ]
})
 
 
module.exports = new Promise((resolve, reject) => { ... })
```

## webpack.prod.conf.js

相比 webpack.dev.conf.js，这个文件多引入了几个依赖，主要是为了压缩CSS和JS。在文件配置上多了一个output，将js文件打包成多个chuck，用hash值命名，来解决缓存策略。

到这里CLI 2的整个配置也就接近尾声了。剩下的还有check-version.js和bulid.js两个文件。

### check-version.js

这个文件主要是用来检测当前环境中的node和npm版本和我们需要的是否一致的。

### bulid.js

这个文件刚开始通过check-versions判断当前的node和npm版本号，如果现有的npm或者node的版本比定义的版本低，则生成一段警告。接下来，先删除打包目标目录下的文件，再进行打包，直至打包完成。