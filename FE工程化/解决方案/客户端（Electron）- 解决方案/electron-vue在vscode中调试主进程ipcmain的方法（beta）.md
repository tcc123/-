#### 目前不完善的地方且代解决的地方：  

1.每次修改主进程代码，需要运行```yarn run pack-dev```重新打开发包；

 2.直接在vscode的debug中调试会只运行一个空白window。所以需要先运行```yarn run start```或者```yarn run dev```启动一个electron应用，然后再运行vscode中的debug即可；

####  调试方法以及配置：  

1.按照以下配置来修改.vscode文件夹中launch.json的配置

```js
 { "version": "0.2.0", 
   "configurations": [ 
     { "type": "node", 
      "request": "launch", 
      "name": "Launch Program", 
      "cwd": "${workspaceRoot}", 
      "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron", 
      "windows": { 
        "runtimeExecutable": "${workspaceRoot}/node_modules/.bin/electron.cmd" 
      }, 
      "args" : ["."], 
      "program": "${workspaceFolder}/dist/electron/main.js" 
     } 
   ] 
 }
```



 2.按照以下配置来修改在webpack.main.config.js的配置，添加在非生产环境下生成sourceMap的选项 

```js
if (process.env.NODE_ENV !== 'production') { 
  mainConfig.devtool = 'source-map' 
  mainConfig.plugins.push( 
    new webpack.DefinePlugin({ 
    '__static': `"${path.join(__dirname, '../static').replace(/\\/g, '\\\\')}"` 
    }) 
  ) 
} 
```

3.按照以下配置来修改在webpack.renderer.config.js的配置，添加在非生产环境下生成sourceMap的选项

```js
if (process.env.NODE_ENV !== 'production') { 
  rendererConfig.devtool = 'source-map' 
  rendererConfig.plugins.push( 
    new webpack.DefinePlugin({ 
      '__static': `"${path.join(__dirname, '../static').replace(/\\/g, '\\\\')}"` 
    }) 
  ) 
} 
```

 4.由于要打开发包，需要在package.json中添加一条打开发包的命令 

```js
"pack-dev": "cross-env NODE_ENV=development webpack --progress --colors --config.electron-vue/webpack.main.config.js && cross-env NODE_ENV=development webpack --progress --colors --config .electron-vue/webpack.renderer.config.js",
```

5.运行yarn run pack-dev 打包确保dist/electron目录下有map文件生成，要不然vscode会识别不到具体文件路径！！

![WechatIMG5](/Users/iyx/前端开发/FE工程化/解决方案/客户端（Electron）- 解决方案/WechatIMG5.png)

6.然后运行yarn run start或者yarn run dev来启动electron项目

![WechatIMG6](/Users/iyx/前端开发/FE工程化/解决方案/客户端（Electron）- 解决方案/WechatIMG6.png)

7.启动完毕之后，再运行vscode中的debug，这时候会又启动了一个一样的electron项目，再将之前的electron项目关闭，就可以进行调试了