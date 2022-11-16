---
title: webpack-loaders使用理解
date: 2022-11-16 17:02:22
tags: webpack
---
# webpack-loaders使用理解
## loaders

webpack 可以使用 [loader](https://www.webpackjs.com/concepts/loaders) 来预处理文件。这允许你打包除 JavaScript 之外的任何静态资源。你可以使用 Node.js 来很简单地编写自己的 loader。

loader 通过在 `require()` 语句中使用 `loadername!` 前缀来激活，或者通过 webpack 配置中的正则表达式来自动应用 - 查看[配置](https://www.webpackjs.com/concepts/loaders#configuration)。



### babel-loader

1. 安装

   ```bash
   npm install --save-dev babel-loader @babel/core
   ```

2. 用法

   通过config方式

   ```js
   module: {
     rules: [
       { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
     ]
   }
   ```

   通过loader方式

   ```js
   var Person = require("babel!./Person.js").default;
   new Person();
   ```

3. 创建`.babelrc`配置文件

   非常棒！虽然已经配置好了 Babel ，但并没有让它真正*生效*。在项目的根目录中创建一个 [.babelrc](https://babel.docschina.org/docs/usage/babelrc) 文件并启用一些[插件](https://babel.docschina.org/docs/plugins)。

   首先，你可以使用转换 ES2015+ 的 [env preset](https://babel.docschina.org/docs/en/babel-preset-env) 。

   ```bash
   npm install @babel/preset-env --save-dev
   ```

   为了让 preset 生效，你需要像下面这样定义你的 `.babelrc` 文件：

   ```json
   {
     "presets": ["@babel/preset-env"]
   }
   ```

4. webpack使用方式

   Webpack.config.js

   ```js
   module: {
     rules: [
       {
         test: /\.js$/,
         exclude: /(node_modules|bower_components)/,
         use: {
           loader: 'babel-loader',
           options: {
             presets: ['@babel/preset-env'],
             plugins: [require('@babel/plugin-transform-object-rest-spread')]
           }
         }
       }
     ]
   }
   ```

   

##### Options

###### Plugin and Preset options

###### `plugins`

Type: `Array<PluginEntry | Plugin>`(PluginEntry)

Default:[]

处理此文件时要激活的插件数组。有关各个条目如何交互（尤其是在多个嵌套的"env"和"overrides"配置之间使用时）的信息，请参阅 [merging](https://babel.docschina.org/docs/en/options#merging)。

注意：该选项还允许来自 Babel 本身的插件实例，但不建议直接使用这些实例。如果您需要创建插件或预设的持久表示形式，应使用 babel.createConfigItem（）。



###### `presets`

Type:`Array<PresetEntry>`(PresetEntry)

Default:[]

处理此文件时要激活的预设数组。有关各个条目如何交互（尤其是在多个嵌套的"env"和"overrides"配置之间使用时）的信息，请参阅 [merging](https://babel.docschina.org/docs/en/options#merging)。

注意：预设的格式与插件相同，但名称规范化需要"预设"而不是"插件-"，预设不能是插件的实例。


