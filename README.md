# vue-cli 基于webpack打包文档详解
* [webpack中文文档地址](https://www.webpackjs.com/)

## vue-cli脚手架工具全局安装
```bash
  npm install vue-cli -g
```
## vue-cli创建项目
```bash
  npm init webpack project

  # 下面是生成过程中对应的某些模块 
  # 可根据自己项目需求选择对应的模块
  # Project name (project)
  # Project name project
  # Project description (A Vue.js project)
  # Project description A Vue.js project
  # Author (lkjx19812006 <371516100@qq.com>)
  # Author lkjx19812006 <371516100@qq.com>
  # Vue build (Use arrow keys)
  # Vue build standalone
  # Install vue-router? (Y/n)
  # Install vue-router? Yes
  # Use ESLint to lint your code? (Y/n) n
  # Use ESLint to lint your code? No
  # Set up unit tests (Y/n) n
  # Set up unit tests No
  # Setup e2e tests with Nightwatch? (Y/n) n
  # Setup e2e tests with Nightwatch? No

```
## 生成后的目录结构

```
   webpack
      |---build webpack相关的打包文件
      |---config webpack打包的配置文件
      |---dist  webpack打包后的生产文件目录
      |---src 资源文件目录 我们以后开发就在该目录下开发即可
        |---assets 资源
        |---components组件
        |---router 路由文件
        |---App.vue vue的入口组件 
        |---main.js webpack打包的入口文件
      |---static 一些不变动的静态资源目录可一放在这里
      |---.babelrc babel的配置文件，能正常识别es6语法等
      |---.editorconfig 
      |---.gitignore git仓库的排除提交文件配置
      |---.postcssrc.js
      |---index.html 入口的html模板页面
      |---package.json 常用的包依赖配置文件 以及打包命令配置等
```
## 下载包依赖
```bash
  npm install 
```

## 运行
```bash
npm run dev
```

# webpack配置详解
## build 目录
### build.js 
```js
//执行 npm run build 实现生产环境打包时，会自动查找package.json中配置好的 build选项
"scripts": {
  "dev": "webpack-dev-server --inline --progress --config,
  "start": "npm run dev",
  "build": "node build/build.js"
}
//build 选项会查找到build目录下的build.js。
//所以build.js 是作为生产环境打包的入口配置页
//以下是js部分

'use strict'
require('./check-versions')()

process.env.NODE_ENV = 'production'//声明打包环境

const ora = require('ora')
const rm = require('rimraf')
const path = require('path')
const chalk = require('chalk')
const webpack = require('webpack')
//以上是一些常规包文件

//引入配置文件 这个后面说
const config = require('../config')
//引入生产打包的配置
const webpackConfig = require('./webpack.prod.conf')
//这两个文件是重点


const spinner = ora('building for production...')
spinner.start()

//每次打包都会删除上次打包的文件
rm(path.join(config.build.assetsRoot, config.build.assetsSubDirectory), err => {
  if (err) throw err
  //执行打包
  /**
   * @param {webpackConfig} 引入打包的配置文件
   * @param {callback} 执行的回调函数
   */
  webpack(webpackConfig, (err, stats) => {
    spinner.stop()
    if (err) throw err
    process.stdout.write(stats.toString({
      colors: true,
      modules: false,
      children: false, // If you are using ts-loader, setting this to true will make TypeScript errors show up during build.
      chunks: false,
      chunkModules: false
    }) + '\n\n')

    if (stats.hasErrors()) {
      console.log(chalk.red('  Build failed with errors.\n'))
      process.exit(1)
    }

    console.log(chalk.cyan('  Build complete.\n'))
    console.log(chalk.yellow(
      '  Tip: built files are meant to be served over an HTTP server.\n' +
      '  Opening index.html over file:// won\'t work.\n'
    ))
  })
})

```

### check-versions.js
```bash
#这个文件主要检查当前node 环境的版本号，和npm 的版本号基本不需要关注

```

### utils.js
```bash
#这个文件生产打包配置的一些loader 如css-loader less-loader scss-loader 主要是用来解析组件模板中的css模块等 基本也不需要关注。如果需要使用less或者scss直接下载包就能使用，不用手动再配置loader

```
### vue-loader.conf
```bash
#这个文件主要加载 utils文件生成的loader配置
```
### webpack.base.conf webpack基础配置 【重点】 
```js
'use strict'
const path = require('path')
const utils = require('./utils')
const config = require('../config')
const vueLoaderConfig = require('./vue-loader.conf')

function resolve (dir) {
  return path.join(__dirname, '..', dir)
}



module.exports = {
  context: path.resolve(__dirname, '../'),
  //入口文件
  entry: {
    app: './src/main.js'
  },
  //输出文件
  output: {
    path: config.build.assetsRoot,
    filename: '[name].js',
    publicPath: process.env.NODE_ENV === 'production'
      ? config.build.assetsPublicPath
      : config.dev.assetsPublicPath
  },
  //配置模块如何解析例如，当在 ES2015 中调用 import "lodash"，
  //resolve 选项能够对 webpack 查找 "lodash" 的方式去做修改（查看模块）。
  resolve: {
    //自动解析确定的扩展。默认值为：
    //就是说再import js文件的时候不用加js后缀
    extensions: ['.js', '.vue', '.json'],
    //创建 import 或 require 的别名 来确保模块引入变得更简单 其实就是将链接取一个快捷的名字，如此而已
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
    }
  },
  module: {
    //定义一些打包规则
    rules: [
      {
        test: /\.vue$/,//以.vue结尾的文件
        loader: 'vue-loader',//使用vue-loader 去解析打包 以下类似
        options: vueLoaderConfig
      },
      {
        test: /\.js$/,//以.js结尾的文件
        loader: 'babel-loader',//使用babel-loader 去解析打包解决ES6的兼容性问题
        include: [resolve('src'), resolve('test'), resolve('node_modules/webpack-dev-server/client')]
      },
      {
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,//打包大小 即文件大小超过这个值将不打包到js文件中
          name: utils.assetsPath('img/[name].[hash:7].[ext]')//设置文件的hash值
        }
      },
      {
        test: /\.(mp4|webm|ogg|mp3|wav|flac|aac)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,//打包大小 即文件大小超过这个值将不打包到js文件中
          name: utils.assetsPath('media/[name].[hash:7].[ext]')
        }
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        loader: 'url-loader',
        options: {
          limit: 10000,//打包大小 即文件大小超过这个值将不打包到js文件中
          name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
        }
      }
    ]
  },
  //设置node打包过程相关配置 基本不需要关注
  node: {
    // prevent webpack from injecting useless setImmediate polyfill because Vue
    // source contains it (although only uses it if it's native).
    setImmediate: false,
    // prevent webpack from injecting mocks to Node native modules
    // that does not make sense for the client
    dgram: 'empty',
    fs: 'empty',
    net: 'empty',
    tls: 'empty',
    child_process: 'empty'
  }
}

```



### webpack.dev.conf webpack本地开发node服务配置 【重点】 
```js
'use strict'
const utils = require('./utils')
const webpack = require('webpack')

//引入config中的配置文件
const config = require('../config')

const merge = require('webpack-merge')
const path = require('path')

//引入基本的配置
const baseWebpackConfig = require('./webpack.base.conf')

const CopyWebpackPlugin = require('copy-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin')
const portfinder = require('portfinder')

const HOST = process.env.HOST //域名如果package.json 中配置了就是用package.json 中的
const PORT = process.env.PORT && Number(process.env.PORT)//端口 同上

//合并配置文件 将基础配置与当前配置合并 实现本地node服务配置
const devWebpackConfig = merge(baseWebpackConfig, {
  module: {
    rules: utils.styleLoaders({ sourceMap: config.dev.cssSourceMap, usePostCSS: true })
  },
  // cheap-module-eval-source-map is faster for development
  devtool: config.dev.devtool,

  // these devServer options should be customized in /config/index.js
  devServer: {
    clientLogLevel: 'warning',//客户端显示日志等级 可能的值有 none, error, warning 或者 info（默认值）
    //使用 HTML5 History API 时任意的404响应都会被替代为index.html
    historyApiFallback: {
      rewrites: [
        { from: /.*/, to: path.posix.join(config.dev.assetsPublicPath, 'index.html') },
      ],
    },
    hot: true,//启用 webpack 的模块热替换特性
    contentBase: false, // since we use CopyWebpackPlugin.告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要
    compress: true,//启用gzip压缩
    host: HOST || config.dev.host,//主机域名
    port: PORT || config.dev.port,//端口号
    open: config.dev.autoOpenBrowser,//自动打开浏览器
    overlay: config.dev.errorOverlay
      ? { warnings: false, errors: true }
      : false,//编译器错误警告时是否再浏览器全覆盖，警告不覆盖 错误覆盖 参数在config中
    publicPath: config.dev.assetsPublicPath,//告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要
    proxy: config.dev.proxyTable,//接口转发代理
    quiet: true, // necessary for FriendlyErrorsPlugin
    //监视文件相关的控制选项。
    watchOptions: {
      poll: config.dev.poll,
    }
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env': require('../config/dev.env')//将环境变量暴露 全局能够使用
    }),
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NamedModulesPlugin(), // HMR shows correct file names in console on update.
    new webpack.NoEmitOnErrorsPlugin(),
    // https://github.com/ampedandwired/html-webpack-plugin
    new HtmlWebpackPlugin({
      filename: 'index.html',//输出文件
      template: 'index.html',//文件模板
      inject: true
    }),
    // copy custom static assets
    new CopyWebpackPlugin([
      {
        from: path.resolve(__dirname, '../static'),
        to: config.dev.assetsSubDirectory,
        ignore: ['.*']
      }
    ])
  ]
})

module.exports = new Promise((resolve, reject) => {
  portfinder.basePort = process.env.PORT || config.dev.port
  portfinder.getPort((err, port) => {
    if (err) {
      reject(err)
    } else {
      // publish the new Port, necessary for e2e tests
      process.env.PORT = port
      // add port to devServer config
      devWebpackConfig.devServer.port = port

      // Add FriendlyErrorsPlugin
      devWebpackConfig.plugins.push(new FriendlyErrorsPlugin({
        compilationSuccessInfo: {
          messages: [`Your application is running here: http://${devWebpackConfig.devServer.host}:${port}`],
        },
        onErrors: config.dev.notifyOnErrors
        ? utils.createNotifierCallback()
        : undefined
      }))

      resolve(devWebpackConfig)
    }
  })
})



```




### webpack.prod.conf webpack生产打包配置 【重点】 