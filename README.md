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

# webpack配置