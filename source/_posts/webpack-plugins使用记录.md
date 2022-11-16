---
title: webpack-plugins使用记录
date: 2022-11-16 17:03:48
tags: webpack
---
# webpack-plugins使用记录
## Plugins

webpack 有着丰富的插件接口(rich plugin interface)。webpack 自身的多数功能都使用这个插件接口。这个插件接口使 webpack 变得**极其灵活**。

### HtmlWebpackPlugin

简单创建 HTML 文件，用于服务器访问

#### **安装**

```bash
 npm i --save-dev html-webpack-plugin
```

```bash
 yarn add --dev html-webpack-plugin
```

这是一个 Webpack 插件，它简化了 HTML 文件的创建，可为 Webpack 捆绑包服务。这对于在文件名中包含哈希的 Webpack 捆绑包特别有用，该哈希值更改了每次编译。您可以让插件生成 HTML 文件，使用 lodash 模板提供您自己的模板或使用您自己的加载器。



#### **使用**

该插件将生成一个 HTML5 文件，该文件包含使用脚本标记在正文中的所有 Webpack 捆绑包。只需将插件添加到 Webpack 配置，如下所示：

Webpack.config.js

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: 'index.js',
  output: {
    path: __dirname + '/dist',
    filename: 'index_bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin()
  ]
}
```

这将生成包含以下内容dist/index.html文件

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script>
  </body>
</html>
```

如果您有多个 Webpack 入口点，则它们都将包含在生成的 HTML 中的脚本标记中。

如果您在 Webpack 的输出中具有任何 CSS 资产（例如，使用迷你 css 提取插件提取的 CSS），则这些资产将包含在 HTML 头的 <link> 标记中。

如果你有插件使用它，html-Webpack-插件应先订购之前，任何集成插件。

#### Options

您可以将配置选项的哈希传递给 html-Webpack-plugin。允许的值如下所示

| Name                     | Type                        | Default                                               | Description                                                  |
| ------------------------ | --------------------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| **`title`**              | `{String}`                  | `Webpack App`                                         | 用于生成的 HTML 文档的标题                                   |
| **`filename`**           | `{String}`                  | `'index.html'`                                        | 要将 HTML 写入的文件。默认值为"index.html"。您也可以在此处指定子目录（例如："assets/admin.html"） |
| **`template`**           | `{String}`                  | ``                                                    | 模板的相对路径或绝对路径。默认情况下，如果存在，它将使用"src/index.ejs"。 |
| **`templateContent`**    | `{string|Function|false}`   | false                                                 | 可以使用代替"模板"来提供内联模板                             |
| **`templateParameters`** | `{Boolean|Object|Function}` | `false`                                               | 允许覆盖模板中使用的参数                                     |
| **`inject`**             | `{Boolean|String}`          | `true`                                                | `true || 'head' || 'body' || false` 将所有资源注入给定的"template"或"templateContent"。传递"true"或"body"时，所有 javascript 资源都将放置在正文元素的底部。"head"将脚本放在头部元素中 |
| **`scriptLoading`**      | `{'blocking'|'defer'}`      | `'blocking'`                                          | 现代浏览器支持非阻塞 javascript 加载（"defer"）以提高页面启动性能。 |
| **`favicon`**            | `{String}`                  | ``                                                    | 将给定的 favicon 路径添加到输出 HTML                         |
| **`meta`**               | `{Object}`                  | `{}`                                                  | 允许注入meta标记.例如:`meta: {viewport: 'width=device-width, initial-scale=1, shrink-to-fit=no'}` |
| **`base`**               | `{Object|String|false}`     | `false`                                               | 注入一个 [`base`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) 标记 `base: "https://example.com/path/page.html` |
| **`minify`**             | `{Boolean|Object}`          | `true` if `mode` is `'production'`, otherwise `false` | 控制输出的分化方式以及以什么方式进行。 参考 [minification](https://github.com/jantimon/html-webpack-plugin#minification) |
| **`hash`**               | `{Boolean}`                 | `false`                                               | 如果"true"，则将唯一的"Webpack"编译哈希附加到所有包含的脚本和 CSS 文件。这对于破坏缓存非常有用 |
| **`cache`**              | `{Boolean}`                 | `true`                                                | 仅在文件已更改时更新文件                                     |
| **`showErrors`**         | `{Boolean}`                 | `true`                                                | 允许您仅添加某些区块（例如，仅添加单元测试区块）             |
| **`chunks`**             | `{?}`                       | `?`                                                   | 允许您仅添加某些区块（例如，仅添加单元测试区块）             |
| **`chunksSortMode`**     | `{String|Function}`         | `auto`                                                | 允许控制在将区块包含在 HTML 之前应如何排序。可选值: `'none' | 'auto' | 'manual' | {Function}` |
| **`excludeChunks`**      | `{Array.}`                  | ``                                                    | 允许您跳过某些区块（例如不添加单元测试区块）                 |
| **`xhtml`**              | `{Boolean}`                 | `false`                                               | 如果"true"将"link"标记呈现为自关闭（符合 XHTML）             |

下面是一个 Webpack 配置示例，说明如何使用这些选项

**webpack.config.js**

```javascript
{
  entry: 'index.js',
  output: {
    path: __dirname + '/dist',
    filename: 'index_bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'My App',
      filename: 'assets/admin.html'
    })
  ]
}
```



### TerserWebpackPlugin



此插件使用 terser 来抽取您的 JavaScript。

#### 安装

```bash
$ npm install terser-webpack-plugin --save-dev
```



#### 使用

**webpack.config.js**

```javascript
const TerserPlugin = require('terser-webpack-plugin');

module.exports = {
  optimization: {
    minimize: true,
    minimizer: [new TerserPlugin({
        test: /\.js(\?.*)?$/i,
      	include: /\.m?js(\?.*)?$/i,
      })],
  },
};
```



#### Options

| Name            | Type                                                         | Default               | Description                                                  |
| --------------- | ------------------------------------------------------------ | --------------------- | ------------------------------------------------------------ |
| test            | `String|RegExp|Array<String|RegExp>`                         | `/\.m?js(\?.*)?$/i`   | 测试以匹配文件                                               |
| include         | `String|RegExp|Array<String|RegExp>`                         | `/\.m?js(\?.*)?$/i`   | 要包含的文件                                                 |
| exclude         | `String|RegExp|Array<String|RegExp>`                         | `underfined`          | 要排除的文件                                                 |
| cache           | `Boolean|String`                                             | `true`                | 启用文件缓存,webpack5中将移除.缓存目录的默认路径`node_modules/.cache/terser-webpack-plugin` |
| cacheKeys       | `Function<(keys,file)->Object>`                              | `(keys,file)->Object` | 允许您覆盖默认缓存键                                         |
| parallel        | `Boolean|Number`                                             | `true`                | 启用/禁用多进程并行运行                                      |
| sourceMap       | `Boolean`                                                    | `false`               | 使用源映射将错误消息位置映射到模块（这会减慢编译速度）。     |
| minify          | `Function`                                                   | `undefined`           | 允许您覆盖默认抽离函数。默认情况下，插件使用 terser 包。可用于使用和测试未发布的版本或分叉。 |
| terserOptions   | `Object`                                                     | `default`             | Terser 选项。                                                |
| extractComments | `Boolean|String|RegExp|Function<(node,comment)->Boolean|Object>` | `true`                | 启用/禁用提取注释。                                          |





### MiniCssExtractPlugin



此插件将 CSS 提取到单独的文件中。它为每个包含 CSS 的 JS 文件创建一个 CSS 文件。它支持 CSS 和源映射的按需加载。

它基于新的 Webpack v4 功能（模块类型）构建，并且需要 Webpack 4 才能工作。

- 异步加载
- 无重复编译（性能）
- 更易于使用
- 特定于 CSS

#### 安装

```bash
npm install --save-dev mini-css-extract-plugin
```



#### 使用

建议将`mini-css-extract-plugin` 提取插件与 [`css-loader`](https://webpack.js.org/loaders/css-loader/)相结合

然后将加载器和插件添加到 Webpack 配置中。例如：

Webpack.config.js

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```



#### Options



##### `publicpath`

Type: `String|Function` Default: the `publicPath` in `webpackOptions.output`

指定目标文件的自定义公共路径。

`String`

Webpack.config.js

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '/public/path/to/',
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};

```

`Function`

Webpack.config.js

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: (resourcePath, context) => {
                return path.relative(path.dirname(resourcePath), context) + '/';
              },
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};

```



##### `esModule`

Type: `Boolean` Default: `false`

默认情况下，mini-css-extract-plugin生成使用 CommonJS 模块语法的 JS 模块。在某些情况下，使用 ES 模块是有益的，例如在 [module concatenation](https://webpack.js.org/plugins/module-concatenation-plugin/) 和 [tree shaking](https://webpack.js.org/guides/tree-shaking/)的情况下。

您可以使用：

webpack.config.js

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true,
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};
```



##### `modules`

Type: `Object` Default: `undefined`

配置 CSS 模块

###### `namedExport`

Type: `Boolean` Default: `false`

启用/禁用名为局部变量导出的 ES 模块。

> ⚠名字使用驼峰命名。
>
> ⚠不允许在 css 类名称中使用 JavaScript 保留字。
>
> ⚠选项 esModule 和*modules.namedExport*在*css-loader*和 *MiniCssExtractPlugin.loader* 中应启用。



Webpack.config.js

```javascript
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true,
              modules: {
                namedExport: true,
              },
            },
          },
          {
            loader: 'css-loader',
            options: {
              esModule: true,
              modules: {
                namedExport: true,
                localIdentName: 'foo__[name]__[local]',
              },
            },
          },
        ],
      },
    ],
  },
};
```