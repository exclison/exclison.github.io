---
title: webpack常用配置记录
date: 2022-11-16 17:07:52
tags: webpack
---

## Webpack 配置

webpack 是需要传入一个配置对象(configuration object)。取决于你如何使用 webpack，可以通过两种方式之一：终端或 Node.js。下面指定了所有可用的配置选项。

> _注意整个配置中我们使用 Node 内置的_ [path 模块](https://nodejs.org/api/path.html)_，并在它前面加上_ [\_\_dirname](https://nodejs.org/docs/latest/api/globals.html#globals_dirname)_这个全局变量。可以防止不同操作系统之间的文件路径问题，并且可以使相对路径按照预期工作。_

### 选项概述

Web pack.config.js

```js
const path = require('path');

module.exports = {

  // 选择模式告诉 Webpack 相应地使用其内置的优化。
  mode: "production", // "production" | "development" | "none"

  // 这里应用程序开始执行 webpack 开始打包
  entry: "./app/entry", // string | object | array

  output: {
    // webpack 如何输出结果的相关选项
    path: path.resolve(__dirname, "dist"), // string
    // 所有输出文件的目标路径
    // 必须是绝对路径（使用 Node.js 的 path 模块）

    filename: "bundle.js", // string    // 「入口分块(entry chunk)」的文件名模板（出口分块？）

    publicPath: "/assets/", // string    // 输出解析文件的目录，url 相对于 HTML 页面

    library: "MyLibrary", // string,
    // 导出库(exported library)的名称

    libraryTarget: "umd", // 通用模块定义    // 导出库(exported library)的类型

  module: {
    // 关于模块配置

    rules: [
      // 模块规则（配置 loader、解析器等选项）

      {
        test: /\.jsx?$/,
        include: [
          path.resolve(__dirname, "app")
        ],
        exclude: [
          path.resolve(__dirname, "app/demo-files")
        ],
        // 这里是匹配条件，每个选项都接收一个正则表达式或字符串
        // test 和 include 具有相同的作用，都是必须匹配选项
        // exclude 是必不匹配选项（优先于 test 和 include）
        // 最佳实践：
        // - 只在 test 和 文件名匹配 中使用正则表达式
        // - 在 include 和 exclude 中使用绝对路径数组
        // - 尽量避免 exclude，更倾向于使用 include

        issuer: { test, include, exclude },
        // issuer 条件（导入源）

        enforce: "pre",
        enforce: "post",
        // 标识应用这些规则，即使规则覆盖（高级选项）

        loader: "babel-loader",
        // 应该应用的 loader，它相对上下文解析
        // 为了更清晰，`-loader` 后缀在 webpack 2 中不再是可选的
        // 查看 webpack 1 升级指南。

        options: {
          presets: ["es2015"]
        },
        // loader 的可选项
      },

      {
        test: /\.html$/,
        test: "\.html$"

        use: [
          // 应用多个 loader 和选项
          "htmllint-loader",
          {
            loader: "html-loader",
            options: {
              /* ... */
            }
          }
        ]
      },

      { oneOf: [ /* rules */ ] },
      // 只使用这些嵌套规则之一

      { rules: [ /* rules */ ] },
      // 使用所有这些嵌套规则（合并可用条件）

      { resource: { and: [ /* 条件 */ ] } },
      // 仅当所有条件都匹配时才匹配

      { resource: { or: [ /* 条件 */ ] } },
      { resource: [ /* 条件 */ ] },
      // 任意条件匹配时匹配（默认为数组）

      { resource: { not: /* 条件 */ } }
      // 条件不匹配时匹配
    ],

    /* 高级模块配置（点击展示） */  },

  resolve: {
    // 解析模块请求的选项
    // （不适用于对 loader 解析）

    modules: [
      "node_modules",
      path.resolve(__dirname, "app")
    ],
    // 用于查找模块的目录

    extensions: [".js", ".json", ".jsx", ".css"],
    // 使用的扩展名

    alias: {
      // 模块别名列表
      "module": path.resolve(__dirname, "app/third/module.js"),
      // 起别名 "module" -> "./app/third/module.js" 和 "module/file" 会导致错误
      // 模块别名相对于当前上下文导入
    },

  performance: {
    hints: "warning", // 枚举    maxAssetSize: 200000, // 整数类型（以字节为单位）
    maxEntrypointSize: 400000, // 整数类型（以字节为单位）
    assetFilter: function(assetFilename) {
      // 提供资源文件名的断言函数
      return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
    }
  },

  devtool: "source-map", // enum  // 通过在浏览器调试工具(browser devtools)中添加元信息(meta info)增强调试
  // 牺牲了构建速度的 `source-map' 是最详细的。

  context: __dirname, // string（绝对路径！）
  // webpack 的主目录
  // entry 和 module.rules.loader 选项
  // 相对于此目录解析

  target: "web", // 枚举  // 包(bundle)应该运行的环境
  // 更改 块加载行为(chunk loading behavior) 和 可用模块(available module)

  externals: ["react", /^@angular\//],  // 不要遵循/打包这些模块，而是在运行时从环境中请求他们

  stats: "errors-only",  // 精确控制要显示的 bundle 信息

  devServer: {
    proxy: { // 代理 URL 到后端开发服务器
      '/api': 'http://localhost:3000'
    },
    contentBase: path.join(__dirname, 'public'), // boolean | string | array, static file location
    compress: true, // 启用 gzip 压缩
    historyApiFallback: true, // 404 时索引. html 为 true，多个路径的对象为 true
    hot: true, // 热模块替换。取决于热模块替换插件
    https: false, // 为true 证书颁发的对象
    noInfo: true, // 仅错误  热重新加载时警告
    // ...
  },

  plugins: [
    // 插件
  ],
  // 附加插件列表

}
```

### 多种配置类型

除了导出单个配置对象，还有一些方式满足其他需求。

- 导出为一个函数

  最终，你会发现需要在[开发](https://www.webpackjs.com/guides/development)和[生产构建](https://www.webpackjs.com/guides/production)之间，消除 `webpack.config.js` 的差异。（至少）有两种选项：

  作为导出一个配置对象的替代，还有一种可选的导出方式是，从 webpack 配置文件中导出一个函数。该函数在调用时，可传入两个参数：

  ​ 环境对象(environment)作为第一个参数。一个选项 map 对象（`argv`）作为第二个参数。这个对象描述了传递给 webpack 的选项，并且具有 [`output-filename`](https://www.webpackjs.com/api/cli/#output-options) 和 [`optimize-minimize`](https://www.webpackjs.com/api/cli/#optimize-options) 等 key。

  ```js
  module.exports = function (env, argv) {
    return {
      mode: env.production ? "production" : "development",
      devtool: env.production ? "source-maps" : "eval",
      plugins: [
        new webpack.optimize.UglifyJsPlugin({
          compress: argv["optimize-minimize"], // 只有传入 -p 或 --optimize-minimize
        }),
      ],
    };
  };
  ```

- 导出一个 Promise

  webpack 将运行由配置文件导出的函数，并且等待 Promise 返回。便于需要异步地加载所需的配置变量。

  ```js
  module.exports = () => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve({
          entry: "./app.js",
          /* ... */
        });
      }, 5000);
    });
  };
  ```

- 导出多个配置对象

  作为导出一个配置对象/配置函数的替代，你可能需要导出多个配置对象（从 webpack 3.1.0 开始支持导出多个函数）。当运行 webpack 时，所有的配置对象都会构建。例如，导出多个配置对象，对于针对多个[构建目标](https://www.webpackjs.com/configuration/output#output-librarytarget)（例如 AMD 和 CommonJS）[打包一个 library](https://www.webpackjs.com/guides/author-libraries) 非常有用。

  ```js
  module.exports = [
    {
      output: {
        filename: "./dist-amd.js",
        libraryTarget: "amd",
      },
      entry: "./app.js",
      mode: "production",
    },
    {
      output: {
        filename: "./dist-commonjs.js",
        libraryTarget: "commonjs",
      },
      entry: "./app.js",
      mode: "production",
    },
  ];
  ```

### 入口和上下文

`string`

entry 对象是用于 webpack 查找启动并构建 bundle。其上下文是入口文件所处的目录的绝对路径的字符串。

- context

  基础目录，**绝对路径**，用于从配置中解析入口起点(entry point)和 loader

  ```js
  context: path.resolve(__dirname, "app");
  ```

  > 默认使用当前目录，但是推荐在配置中传递一个值。这使得你的配置独立于 CWD(current working directory - 当前执行路径)。 **平时用不到**

- entry

  `string | [string] | object { <key>: string | [string] } | (function: () => string | [string] | object { <key>: string | [string] })`

  起点或是应用程序的起点入口。从这个起点开始，应用程序启动执行。如果传递一个数组，那么数组的每一项都会执行。

  动态加载的模块**不是**入口起点。

  简单规则：每个 HTML 页面都有一个入口起点。单页应用(SPA)：一个入口起点，多页应用(MPA)：多个入口起点。

  ```js
  entry: {
    home: "./home.js",
    about: "./about.js",
    contact: "./contact.js"
  }
  ```

- 命名

  如果传入一个字符串或字符串数组，chunk 会被命名为 `main`。如果传入一个对象，则每个键(key)会是 chunk 的名称，该值描述了 chunk 的入口起点。

- 动态入口

  ```js
  entry: () => "./demo";
  ```

  或

  ```js
  entry: () => new Promise((resolve) => resolve(["./demo", "./demo2"]));
  ```

  当结合 [`output.library`](https://www.webpackjs.com/configuration/output#output-library) 选项时：如果传入数组，则只导出最后一项。

### 输出

`output` 位于对象最顶级键(key)，包括了一组选项，指示 webpack 如何去输出、以及在哪里输出你的 「bundle、asset 和其他你所打包或使用 webpack 载入的任何内容」。

#### `output.filename`

`string` `function`

此选项决定了每个输出 bundle 的名称。这些 bundle 将写入到 [`output.path`](https://www.webpackjs.com/configuration/output/#output-path) 选项指定的目录下。

对于单个[`入口`](https://www.webpackjs.com/configuration/entry-context#entry)起点，filename 会是一个静态名称。

```js
filename: "bundle.js";
```

然而，当通过多个入口起点(entry point)、代码拆分(code splitting)或各种插件(plugin)创建多个 bundle，应该使用以下一种替换方式，来赋予每个 bundle 一个唯一的名称……

使用入口名称：

```js
filename: "[name].bundle.js";
```

使用内部 chunk id

```js
filename: "[id].bundle.js";
```

使用每次构建过程中，唯一的 hash 生成

```js
filename: "[name].[hash].bundle.js";
```

使用基于每个 chunk 内容的 hash：

```js
filename: "[chunkhash].bundle.js";
```

注意此选项被称为文件名，但是你还是可以使用像 `"js/[name]/bundle.js"` 这样的文件夹结构。

注意，此选项不会影响那些「按需加载 chunk」的输出文件。对于这些文件，请使用 [`output.chunkFilename`](https://www.webpackjs.com/configuration/output/#output-chunkfilename) 选项来控制输出。通过 loader 创建的文件也不受影响。在这种情况下，你必须尝试 loader 特定的可用选项。

可以使用以下替换模板字符串（通过 webpack 内部的[`TemplatedPathPlugin`][`templatedpathplugin`](https://github.com/webpack/webpack/blob/master/lib/TemplatedPathPlugin.js)）：

| 模板        | 描述                                    |
| ----------- | --------------------------------------- |
| [hash]      | 模块标识符的 hash                       |
| [chunkhash] | chunk 内容的 hash                       |
| [name]      | 模块名称                                |
| [id]        | 模块标识符                              |
| [query]     | 模块的 query,例如,文件名`?`后面的字符串 |

`[hash]` 和 `[chunkhash]` 的长度可以使用 `[hash:16]`（默认为 20）来指定。或者，通过指定[`output.hashDigestLength`](https://www.webpackjs.com/configuration/output/#output-hashdigestlength) 在全局配置长度。

#### `output.path`

`string`

output 目录对应一个绝对路径

```js
path: path.reslove(__dirname, "dist/assets");
```

注意，`[hash]` 在参数中被替换为编译过程(compilation)的 hash。

#### `output.publicPath`

`string` `function`

对于按需加载(on-demand-load)或加载外部资源(external resources)（如图片、文件等）来说，output.publicPath 是很重要的选项。如果指定了一个错误的值，则在加载这些资源时会收到 404 错误。

此选项指定在浏览器中所引用的「此输出目录对应的**公开 URL**」。相对 URL(relative URL) 会被相对于 HTML 页面（或 `` 标签）解析。相对于服务的 URL(Server-relative URL)，相对于协议的 URL(protocol-relative URL) 或绝对 URL(absolute URL) 也可是可能用到的，或者有时必须用到，例如：当将资源托管到 CDN 时。

该选项的值是以 runtime(运行时) 或 loader(载入时) 所创建的每个 URL 为前缀。因此，在多数情况下，**此选项的值都会以`/`结束**。

默认值是一个空字符串 `""`。

简单规则如下：[`output.path`](https://www.webpackjs.com/configuration/output/#output-path) 中的 URL 以 HTML 页面为基准。

```js
path: path.resolve(__dirname, "public/assets"),
publicPath: "https://cdn.example.com/assets/"
```

### Optimization

由于版本 4 Webpack 会根据所选模式运行优化，因此所有优化仍可用于手动配置和覆盖。

#### `optimization.minimize`

`boolean = true`

告诉 Webpack 使用 TerserPlugin 或优化中指定的插件最小化捆绑包。

Webpack.config.js

```js
module.exports = {
  //...
  optimization: {
    minimize: false,
  },
};
```

#### `optimization.minimizer`

`[TerserPlugin]`或者`[function (compiler)]`

允许您通过提供不同的一个或多个自定义 TerserPlugin 实例来覆盖默认最小化器。

Webpack.config.js

```js
const TerserPlugin = require("terser-webpack-plugin");

module.exports = {
  optimization: {
    minimizer: [
      new TerserPlugin({
        cache: true,
        parallel: true,
        sourceMap: true, // 如果在生产中使用源映射，则必须设置为 true
        terserOptions: {
          // https://github.com/webpack-contrib/terser-webpack-plugin#terseroptions
        },
      }),
    ],
  },
};
```

或者传入函数

```js
module.exports = {
  optimization: {
    minimizer: [
      (compiler) => {
        const TerserPlugin = require("terser-webpack-plugin");
        new TerserPlugin({
          /* your config */
        }).apply(compiler);
      },
    ],
  },
};
```

'...'可用于优化. 最小化器访问默认值。

```js
module.exports = {
  optimization: {
    minimizer: [new CssMinimizer(), "..."],
  },
};
```

#### `optimization.splitChunks`

`object`

默认情况下，Webpack v4+ 为动态导入的模块提供了开箱即用的新的常见区块策略。请参阅在 SplitChunksPlugin 页中配置此行为的可用选项。

Webpack.config.js

```js
module.exports = {
  //...
  optimization: {
    splitChunks: {
      //这指示将选择哪些区块进行优化。提供字符串时，有效值为all、async和initial。
      //提供所有块可能特别强大，因为这意味着块即使在异步块和非异步块之间也可以共享。
      chunks: "async",

      minSize: 20000, //要生成区块的最小大小（以字节为单位）

      //拆分Chunks.minRemainsize 选项在webpack 5中引入，
      //通过确保拆分后保留的区块的最小大小超过限制来避免零大小的模块。
      //在"开发"模式下默认为 0。
      //对于其他情况，拆分为"拆分".minRemainsize 默认为 splitChunks.minSize 的值，
      //因此不需要手动指定，但需要深度控制的极少数情况除外
      minRemainingSize: 0,

      //使用 maxSize告诉 Webpack 尝试将大于 maxSize 字节的区块拆分为较小的部分。
      //零件的大小至少为最小尺寸（最大尺寸旁边）。
      //maxSize 选项用于 HTTP/2 和长期缓存。它增加了请求计数，以便更好地缓存。
      //它还可用于减小文件大小，以便更快地重建。
      maxSize: 0,

      minChunks: 1, //拆分前必须共享模块的最小区块数。

      maxAsyncRequests: 30, //按需加载时并行请求的最大数量。

      maxInitialRequests: 30, //入口点上并行请求的最大数。

      //默认情况下，Webpack 将使用区块的源和名称（例如供应商main.js）生成名称。
      //此选项允许您指定用于生成名称的分隔符。
      automaticNameDelimiter: "~",

      //强制执行拆分的大小阈值和其他限制（最小保持大小、最大同步请求、最大初始请求）将被忽略
      enforceSizeThreshold: 50000,

      //缓存组可以继承和/或覆盖拆分Chunks中的任何选项。
      //但测试、优先级和重用只可以在缓存组级别进行配置。若要禁用任何默认缓存组，请将它们设置为 false。
      cacheGroups: {
        defaultVendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10,
        },
        default: {
          minChunks: 2,

          //一个模块可以属于多个缓存组。优化将更喜欢具有更高优先级的缓存组。
          //默认组具有负优先级，允许自定义组具有更高的优先级（自定义组默认值为 0）。
          priority: -20,

          //如果当前区块包含已经从主捆绑包中分离出的模块，则将重用该区块，
          //而不是生成新的区块。这可能会影响区块的结果文件名。
          reuseExistingChunk: true,
        },
      },
    },
  },
};
```

### 开发中(devServer)

`object`

通过来自 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 的这些选项，能够用多种方式改变其行为。这里有一个简单的例子，所有来自 `dist/` 目录的文件都做 gzip 压缩和提供为服务：

```js
devServer: {
  contentBase: path.join(__dirname, "dist"),
  compress: true,
  port: 9000
}
```

通过 Node.js API 来使用 dev-server， `devServer` 中的选项将被忽略。将选项作为第二个参数传入： `new WebpackDevServer(compiler, {...})`。

```js
const Webpack = require("webpack");
const WebpackDevServer = require("../../../lib/Server");
const webpackConfig = require("./webpack.config");

const compiler = Webpack(webpackConfig);
const devServerOptions = Object.assign({}, webpackConfig.devServer, {
  open: true,
  stats: {
    colors: true,
  },
});
const server = new WebpackDevServer(compiler, devServerOptions);

server.listen(8080, "127.0.0.1", () => {
  console.log("Starting server on http://localhost:8080");
});
```

#### `devServer.after`

`function`

提供在服务器内部所有其他中间件之后执行自定义中间件的能力。

```js
after(app){
  // do fancy stuff
}
```

#### `devServer.allowedHosts`

`array`

此选项允许您将允许访问开发服务器的服务列入白名单。

```js
allowedHosts: ["host.com", "subdomain.host.com", "subdomain2.host.com", "host2.com"];
```

模仿 django 的`ALLOWED_HOSTS`，一个以`.`开头的值。可用作子域通配符。.host.com 将匹配`host.com ` `www.host.com`和任何其他子域`host.com`。

```js
// 这达到相同的效果，因为第一个示例
// 不必更新您的配置
// 如果新的子域需要访问开发服务器
allowedHosts: [".host.com", "host2.com"];
```

若要在 CLI 中使用此选项，请传递 --允许的主机选项逗号分隔字符串。

```bash
webpack-dev-server --entry /entry/file --output-path /output/path --allowed-hosts .host.com,host2.com
```

#### `devServer.before`

`function`

提供在服务器内部执行所有其他中间件之前执行自定义中间件的能力。这可用于定义自定义处理程序，例如：

```js
before(app){
  app.get('/some/path', function(req, res) {
    res.json({ custom: 'response' });
  });
}
```

#### `devServer.bonjour`

此选项在启动时通过 ZeroConf 网络广播服务器

```js
bonjour: true;
```

#### `devServer.clientLogLevel`

`string`

> 控制消息显示

当使用*内联模式(inline mode)*时，在开发工具(DevTools)的控制台(console)将显示消息，如：在重新加载之前，在一个错误之前，或者模块热替换(Hot Module Replacement)启用时。这可能显得很繁琐。

你可以阻止所有这些消息显示，使用这个选项：

```js
clientLogLevel: "none";
```

可能的值有 `none`, `error`, `warning` 或者 `info`（默认值）。

#### `devServer.color` - Cli only

`boolean`

启用/禁用控制台上的颜色。

```bash
webpack-dev-server --color
```

#### `devServer.compress`

`boolean`

启用 gzip 压缩

```js
compress: true;
```

#### `devServer.contentBase`

```
boolean` `string` `array
```

告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要。[`devServer.publicPath`](https://www.webpackjs.com/configuration/dev-server/#devserver-publicpath-) 将用于确定应该从哪里提供 bundle，并且此选项优先。

默认情况下，将使用当前工作目录作为提供内容的目录，但是你可以修改为其他目录：

```js
contentBase: path.join(__dirname, "public");
```

注意，推荐使用绝对路径。

但是也可以从多个目录提供内容：

```js
contentBase: [path.join(__dirname, "public"), path.join(__dirname, "assets")];
```

禁用 `contentBase`：

```js
contentBase: false;
```

#### `devServer.filename`

`string`

在**惰性模式**中，此选项可减少编译。 默认在**惰性模式**，每个请求结果都会产生全新的编译。使用 `filename`，可以只在某个文件被请求时编译。

如果 `output.filename` 设置为 `bundle.js` ，`filename` 使用如下：

```js
lazy: true,
filename: "bundle.js"
```

现在只有在请求 `/bundle.js` 时候，才会编译 bundle。

> `filename` \*在不使用**_惰性加载_**时没有效果。

#### `devServer.headers`

`object`

在所有响应中添加首部内容：

```js
headers: {
  "X-Custom-Foo": "bar"
}
```

#### `devServer.historyApiFallback`

`boolean` `object`

当使用 [HTML5 History API](https://developer.mozilla.org/en-US/docs/Web/API/History) 时，任意的 `404` 响应都可能需要被替代为 `index.html`。通过传入以下启用：

```js
historyApiFallback: true;
```

通过传入一个对象，比如使用 `rewrites` 这个选项，此行为可进一步地控制：

```js
historyApiFallback: {
  rewrites: [
    { from: /^\/$/, to: "/views/landing.html" },
    { from: /^\/subpage/, to: "/views/subpage.html" },
    { from: /./, to: "/views/404.html" },
  ];
}
```

当路径中使用点(dot)（常见于 Angular），你可能需要使用 `disableDotRule`：

```js
historyApiFallback: {
  disableDotRule: true;
}
```

#### `devServer.host`

`string`

指定使用一个 host。默认是 `localhost`。如果你希望服务器外部可访问，指定如下：

```js
host: "0.0.0.0";
```

#### `devServer.hot`

`boolean`

启用 webpack 的模块热替换特性：

```js
hot: true;
```

> 请注意，Webpack。需要热模块更换模块才能完全启用 HMR。如果使用 --hot 选项启动 Webpack 或 Webpack-dev-server，则会自动添加此插件，因此您可能不需要将其添加到 Webpack.config.js 中。有关详细信息，请参阅 HMR 概念页面。

#### `devServer.hotOnly`

`boolean`

启用热模块替换（请参阅 devServer.hot），在生成失败时不刷新页面作为回退。

```js
hotOnly: true;
```

#### `devServer.https`

`boolean` `object`

默认情况下，dev-server 通过 HTTP 提供服务。也可以选择带有 HTTPS 的 HTTP/2 提供服务：

```js
https: true;
```

以上设置使用了自签名证书，但是你可以提供自己的：

```js
https: {
  key: fs.readFileSync("/path/to/server.key"),
  cert: fs.readFileSync("/path/to/server.crt"),
  ca: fs.readFileSync("/path/to/ca.pem"),
}
```

此对象直接传递到 Node.js HTTPS 模块，所以更多信息请查看 [HTTPS 文档](https://nodejs.org/api/https.html)。

#### `devServer.index`

`string`

被视为索引文件的文件名。

```js
index: "index.htm";
```

#### `devServer.info` - CLI only

`boolean`

输出 cli 信息。默认情况下，它处于启用状态。

```bash
webpack-dev-server --info=false
```

#### `devServer.inline`

`boolean`

在 dev-server 的两种不同模式之间切换。默认情况下，应用程序启用*内联模式(inline mode)*。这意味着一段处理实时重载的脚本被插入到你的包(bundle)中，并且构建消息将会出现在浏览器控制台。

也可以使用 **iframe 模式**，它在通知栏下面使用 `` 标签，包含了关于构建的消息。切换到 **iframe 模式**：

```js
inline: false;
```

> _推荐使用模块热替换的内联模式，因为它包含来自 websocket 的 HMR 触发器。轮询模式可以作为替代方案，但需要一个额外的入口点：_`'webpack/hot/poll?1000'`。

#### `devServer.lazy`

`boolean`

当启用 `lazy` 时，dev-server 只有在请求时才编译包(bundle)。这意味着 webpack 不会监视任何文件改动。我们称之为“**惰性模式**”。

```js
lazy: true;
```

> `watchOptions` \*在使用**\*惰性模式**时无效。
>
> \*如果使用命令行工具(CLI)，请确保**\*内联模式(inline mode)**被禁用。

#### `devServer.noInfo`

`boolean`

启用 `noInfo` 后，诸如「启动时和每次保存之后，那些显示的 webpack 包(bundle)信息」的消息将被隐藏。错误和警告仍然会显示。

```js
noInfo: true;
```

#### `devServer.open`

`boolean`

启用"open"后，开发服务器将打开浏览器。

```js
open: true;
```

#### `devServer.openPage`

`string`

指定打开浏览器时要导航到的页面。

```js
openPage: "/different/page";
```

#### `devServer.overlay`

`boolean` `object`

当存在编译器错误或警告时，在浏览器中显示全屏叠加。默认情况下禁用。如果只想显示编译器错误：

```js
overlay: true;
```

如果你想既显示错误也显示警告

```js
overlay: {
  warnings: true,
  errors: true
}
```

#### `devServer.port`

`number`

指定要监听请求的端口号:

```js
port: 8080;
```

#### `devServer.proxy`

`object`

代理服务器

如果你有单独的后端开发服务器 API，并且希望在同域名下发送 API 请求 ，那么代理某些 URL 会很有用。

dev-server 使用了非常强大的 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware) 包。更多高级用法，请查阅其[文档](https://github.com/chimurai/http-proxy-middleware#options)。

在 `localhost:3000` 上有后端服务的话，你可以这样启用代理：

```js
proxy: {
  "/api": "http://localhost:3000"
}
```

请求到 `/api/users` 现在会被代理到请求 `http://localhost:3000/api/users`。

如果你不想始终传递 `/api` ，则需要重写路径：

```js
proxy: {
  "/api": {
    target: "http://localhost:3000",
    pathRewrite: {"^/api" : ""}
  }
}
```

默认情况下，不接受运行在 HTTPS 上，且使用了无效证书的后端服务器。如果你想要接受，修改配置如下：

```js
proxy: {
  "/api": {
    target: "https://other-server.example.com",
    secure: false
  }
}
```

有时你不想代理所有的请求。可以基于一个函数的返回值绕过代理。

在函数中你可以访问请求体、响应体和代理选项。必须返回 `false` 或路径，来跳过代理请求。

例如：对于浏览器请求，你想要提供一个 HTML 页面，但是对于 API 请求则保持代理。你可以这样做：

```js
proxy: {
  "/api": {
    target: "http://localhost:3000",
    bypass: function(req, res, proxyOptions) {
      if (req.headers.accept.indexOf("html") !== -1) {
        console.log("Skipping proxy for browser request.");
        return "/index.html";
      }
    }
  }
}
```

如果要将多个特定路径代理到同一目标，可以使用具有上下文属性的一个或多个对象的数组：

```js
proxy: [
  {
    context: ["/auth", "/api"],
    target: "http://localhost:3000",
  },
];
```

#### `devServer.progress` - Cli only

`boolean`

将运行进度输出到控制台。

```js
webpack-dev-server --progress
```

#### `devServer.public`

`string`

当使用*内联模式(inline mode)*并代理 dev-server 时，内联的客户端脚本并不总是知道要连接到什么地方。它会尝试根据 `window.location` 来猜测服务器的 URL，但是如果失败，你需要这样。

例如，dev-server 被代理到 nginx，并且在 `myapp.test` 上可用：

```js
public: "myapp.test:80";
```

#### `devServer.publicPath`

`string`

此路径下的打包文件可在浏览器中访问。

假设服务器运行在 `http://localhost:8080` 并且 `output.filename` 被设置为 `bundle.js`。默认 `publicPath` 是 `"/"`，所以你的包(bundle)可以通过 `http://localhost:8080/bundle.js` 访问。

可以修改 `publicPath`，将 bundle 放在一个目录：

```js
publicPath: "/assets/";
```

你的包现在可以通过 `http://localhost:8080/assets/bundle.js` 访问。

> _确保_ `publicPath` _总是以斜杠(/)开头和结尾。_

也可以使用一个完整的 URL。这是模块热替换所必需的。

```js
publicPath: "http://localhost:8080/assets/";
```

bundle 可以通过 `http://localhost:8080/assets/bundle.js` 访问。

> `devServer.publicPath` _和_ `output.publicPath` _一样被推荐。_

#### `devServer.quiet`

`boolean`

启用 `quiet` 后，除了初始启动信息之外的任何内容都不会被打印到控制台。这也意味着来自 webpack 的错误或警告在控制台不可见。

```js
quiet: true;
```

#### `devServer.socket`

`string`

要侦听的 Unix 套接字（而不是主机）。

```js
socket: "socket";
```

#### `devServer.staticOptions`

可以配置高级选项，用于从内容库提供静态文件。有关可能的选项，请参阅快速文档。例如：

```js
staticOptions: {
  redirect: false;
}
```

#### `devServer.stats`

此选项允许您精确控制显示的捆绑信息。如果您想要一些捆绑信息，但并不是所有的，这可能是一个不错的中间地带。

```js
stats: "errors-only";
```
