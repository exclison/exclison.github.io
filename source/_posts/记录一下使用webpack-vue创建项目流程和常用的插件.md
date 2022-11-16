---
title: 记录一下使用webpack+vue创建项目流程和常用的插件
date: 2022-11-16 17:05:22
tags: webpack
---

## 记录一下使用webpack+vue创建项目流程和常用的插件

##### 1.项目初始化

```
npm init
```

##### 2.安装webpack

```
npm i webpack webpack-dev-server
```

##### 3.创建配置文件

```text
//项目文档结构
web
├─package-lock.json
├─package.json
├─src
|  └main.js
├─scripts // 构建脚本文件夹
|    ├─dev.js //开发环境构建脚本
|    ├─prod.js //生产环境构建脚本
|    ├─config // 构建脚本配置文件夹
|    |   ├─index.js // webpack配置文件 导出开发与生产配置函数
|    |   ├─util.js // 导出工具函数
|    |   ├─plugin // 封装插件文件夹
|    |   |   └index.js
|    |   ├─loader // 封装loaders文件夹
|    |   |   └index.js
├─public
|   └index.html // html模板
├─dist // 打包输出路径
```



##### 4.loaders

<font color=#0089ff>注意:第4,第5步为常用loader和plugin介绍</font>



  - thread-loader

    ```javascript
    //安装 使用时，需将此 loader 放置在其他 loader 之前。放置在此 loader 之后的 loader 会在一个独立的 worker 池中运行
    npm install --save-dev thread-loader
    
    //webpack.config.js
    module.exports = {
      module: {
        rules: [
          {
            test: /\.js$/,
            include: path.resolve("src"),
            use: [
              "thread-loader",
              // 耗时的 loader （例如 babel-loader）
            ]
          }
        ]
      }
    }
    
    ```

    

  - babel-loader

    ```javascript
    //当前演示webpack使用方法 其他参照babel官网
    //安装
    npm install --save-dev babel-loader @babel/core
    
    //webpack.config.js
    module: {
      rules: [
        { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" }
      ]
    }
    
    //创建 .babelrc 配置文件
    //安装插件 使用转换 ES2015+ 的 env preset 
    npm install @babel/preset-env --save-dev 
    
    //.babelrc
    {
      "presets": ["@babel/preset-env"]
    }
    ```

- eslint-loader

  ```javascript
  //安装
  npm install eslint --save-dev //安装eslint
  npm install eslint-loader --save-dev //安装eslint-loader
  
  //webpack.config.js
  module.exports = {
    module: {
      rules: [
        {
          test: /\.js$/,
          exclude: /node_modules/,
          use: ['babel-loader', 'eslint-loader'],
        },
      ],
    },
  };
  
  
  
  ```

- style-loader

  ```javascript
  //安装
  npm install --save-dev style-loader
  //webpack.config.js
  module.exports = {
    module: {
      rules: [
        {
          test: /\.css$/i,
          use: ['style-loader', 'css-loader'],
        },
      ],
    },
  };
  
  ```

  

- css-loader

    ```javascript
    //安装
    npm install --save-dev css-loader
    
    //webpack.config.js
    module.exports = {
      module: {
        rules: [
          {
            test: /\.css$/i,
            use: ['style-loader', 'css-loader'],
          },
        ],
      },
    };
    ```

- postcss-loader

  ```javascript
  npm i -D postcss-loader
  
  //postcss.config.js
  module.exports = {
    parser: 'sugarss',
    plugins: {
      'postcss-import': {},
      'postcss-cssnext': {},
      'cssnano': {}
    }
  }
  //webpack.config.js
  module.exports = {
    module: {
      rules: [
        {
          test: /\.css$/,
          use: [ 'style-loader', 'postcss-loader' ]
        }
      ]
    }
  }
  ```

- less-loader

  ```javascript
  //安装
  npm install --save-dev less-loader less
  
  // webpack.config.js
  module.exports = {
      ...
      module: {
          rules: [{
              test: /\.less$/,
              use: [{
                  loader: "style-loader" // creates style nodes from JS strings
              }, {
                  loader: "css-loader" // translates CSS into CommonJS
              }, {
                  loader: "less-loader" // compiles Less to CSS
              }]
          }]
      }
  };
  ```

  

- Sass-loader

  ```javascript
  npm install sass-loader node-sass webpack --save-dev
  // webpack.config.js
  module.exports = {
    ...
    module: {
      rules: [{
        test: /\.scss$/,
        use: [{
            loader: "style-loader" // 将 JS 字符串生成为 style 节点
        }, {
            loader: "css-loader" // 将 CSS 转化成 CommonJS 模块
        }, {
            loader: "sass-loader" // 将 Sass 编译成 CSS
        }]
      }]
    }
  };
  ```

- Vue-loader

  ```javascript
  //安装
  npm install -D vue-loader vue-template-compiler
  
  // webpack.config.js
  const VueLoaderPlugin = require('vue-loader/lib/plugin')
  
  module.exports = {
    module: {
      rules: [
        // ... 其它规则
        {
          test: /\.vue$/,
          loader: 'vue-loader'
        }
      ]
    },
    plugins: [
      // 请确保引入这个插件！
      new VueLoaderPlugin()
    ]
  }
  ```

- Url-loader

  ```javascript
  //安装
  npm install --save-dev url-loader
  
  module.exports = {
    module: {
      rules: [
        {
          test: /\.(png|jpg|gif)$/,
          use: [
            {
              loader: 'url-loader',
              options: {
                limit: 8192
              }
            }
          ]
        }
      ]
    }
  }
  ```

- File-loader

  ```javascript
  //安装
  npm install --save-dev file-loader
  
  module.exports = {
    module: {
      rules: [
        {
          test: /\.(png|jpg|gif)$/,
          use: [
            {
              loader: 'file-loader',
              options: {}
            }
          ]
        }
      ]
    }
  }
  ```

  



##### 5.plugins



  - mini-css-extract-plugin

    ```javascript
    //安装
    npm install --save-dev mini-css-extract-plugin
    
    //webpack.config.js
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
    Options
    
    ```

- html-webpack-plugin

  ```javascript
  //安装
  npm install --save-dev html-webpack-plugin
  
  var HtmlWebpackPlugin = require('html-webpack-plugin');
  var path = require('path');
  
  var webpackConfig = {
    entry: 'index.js',
    output: {
      path: path.resolve(__dirname, './dist'),
      filename: 'index_bundle.js'
    },
    plugins: [new HtmlWebpackPlugin()]
  };
  ```

  

- clean-webpack-plugin

  ```javascript
  const { CleanWebpackPlugin } = require('clean-webpack-plugin');
   
  const webpackConfig = {
      plugins: [
          /**
           * All files inside webpack's output.path directory will be removed once, but the
           * directory itself will not be. If using webpack 4+'s default configuration,
           * everything under <PROJECT_DIR>/dist/ will be removed.
           * Use cleanOnceBeforeBuildPatterns to override this behavior.
           *
           * During rebuilds, all webpack assets that are not used anymore
           * will be removed automatically.
           *
           * See `Options and Defaults` for information
           */
          new CleanWebpackPlugin(),
      ],
  };
   
  module.exports = webpackConfig;
  ```

  

- optimize-css-assets-webpack-plugin

  ```javascript
  npm install --save-dev optimize-css-assets-webpack-plugin
  
  var OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');
  module.exports = {
    module: {
      rules: [
        {
          test: /\.css$/,
          loader: ExtractTextPlugin.extract('style-loader', 'css-loader')
        }
      ]
    },
    plugins: [
      new ExtractTextPlugin('styles.css'),
      new OptimizeCssAssetsPlugin({
        assetNameRegExp: /\.optimize\.css$/g,
        cssProcessor: require('cssnano'),
        cssProcessorPluginOptions: {
          preset: ['default', { discardComments: { removeAll: true } }],
        },
        canPrint: true
      })
    ]
  };
  ```

  

- terser-webpack-plugin

  ```javascript
  npm install terser-webpack-plugin --save-dev
  
  const TerserPlugin = require('terser-webpack-plugin');
   
  module.exports = {
    optimization: {
      minimize: true,
      minimizer: [new TerserPlugin()],
    },
  };
  ```

  

- HotModuleReplacementPlugin

  ```javascript
  const { HotModuleReplacementPlugin } = require('webpack');
  
  const webpackConfig = {
      plugins: [
          new HotModuleReplacementPlugin();
      ],
  };
   
  module.exports = webpackConfig;
  
  ```

  ##### 

##### 6.环境构建

  - 开发环境

    ```javascript
    //使用nodeAPI构建开发环境
    // ./scripts/dev.js
    const Webpack = require("webpack");
    const WebpackDevServer = require("webpack-dev-server");
    const { getDevConfig } = require("./config");
    const webpackConfig = getDevConfig();
    
    const compiler = Webpack(webpackConfig);
    const devServerOptions = Object.assign({}, webpackConfig.devServer, {
      historyApiFallback: true,
      open: true,
      hot: true,
      inline: true,
      stats: "errors-warnings",
      overlay: {
        // 浏览器全屏显示错误
        errors: true,
        warnings: false,
      },
      stats: {
        colors: true,
      },
    });
    const server = new WebpackDevServer(compiler, devServerOptions);
    
    server.listen(8087, "127.0.0.1", () => {
      console.log("Starting server on http://localhost:8087");
    });
    
    ```

    

  - 生产环境

    ```javascript
    //使用nodeAPI构建生产环境
    // ./scripts/prod.js
    const { getProdConfig } = require("./config");
    const config = getProdConfig();
    
    const webpack = require("webpack");
    
    const compiler = webpack(config);
    
    compiler.run((err, stats) => {
      const info = stats.toJson();
    
      if (err) {
        console.error(err.stack || err);
        if (err.details) {
          console.error(err.details);
        }
        return;
      }else if (stats.hasErrors()) {
        console.error(info.errors);
      }else if (stats.hasWarnings()) {
        console.warn(info.warnings);
      }else{
          console.log('build successfully')
          console.log([
            console.log(`Time: ${info.time}ms`),
            console.log(`output: ${info.outputPath}`),
            console.log(`webpack version: ${info.version}`),
          ].join('\n'))
      }
    });
    
    ```

##### 7.补充代码

  - 将webpack配置文件以函数形式封装

    ```javascript
    // ./scripts/config/index.js
    const {
      getJSLoader,
      getVueLoader,
      getCSSLoader,
      getLESSLoader,
      getSASSLoader,
      getAssetsLoader,
    } = require("./loader");
    
    const { 
      getVueLoaderPlugin,
      getHtmlWebpackPlugin,
      getStylePlugin,
      getMiniCssExtractPlugin,
      getTerserPlugin,
      getHotModuleReplacementPlugin,
     } = require("./plugin");
    
    const { reslove } = require("./util");
    
    function getCommonConfig () {
      return {
        entry: reslove("./src/main.js"), // 入口文件
        resolve: {
          // 解析模块请求的选项
          // （不适用于对 loader 解析）
    
          extensions: [".js", ".json", ".jsx", ".css"],
          // 使用的扩展名
    
          alias: {
            // 模块别名列表
            "@": reslove("./src"),
          },
        },
      };
    }
    
    exports.getDevConfig = function () {
      const mode = "development";
    
      const common = getCommonConfig();
    
      return Object.assign(common, {
        output: {
          path: reslove("./dist"), //打包后的文件存放的地方
          filename: "[name].[hash].js", //打包后输出文件的文件名
        },
        mode,
        module: {
          // 关于模块配置
          rules: [
            // 模块规则（配置 loader、解析器等选项）
            getJSLoader(mode),
            getVueLoader(mode),
            getCSSLoader(mode),
            getLESSLoader(mode),
            getSASSLoader(mode),
            ...getAssetsLoader(mode),
          ],
        },
        plugins: [
          getVueLoaderPlugin(),
          getHtmlWebpackPlugin({
            template:reslove('./public/index.html'),
            filename:'index.html',
            minify: {
              caseSensitive: false, // 是否大小写敏感
              collapseWhitespace: true, // 是否去除空格
              removeAttributeQuotes: true, // 去掉属性引用
              removeComments: true, // 去注释
            },
          }),
          getHotModuleReplacementPlugin(),
        ],
      });
    }
    
    exports.getProdConfig = function () {
      const mode = "production";
    
      const common = getCommonConfig();
    
      return Object.assign(common, {
        output: {
          path: reslove("./dist"), //打包后的文件存放的地方
          filename: "[name].[chunkhash].js", //打包后输出文件的文件名
        },
        mode,
        module: {
          // 关于模块配置
          rules: [
            // 模块规则（配置 loader、解析器等选项）
            getJSLoader(mode),
            getVueLoader(mode),
            getCSSLoader(mode),
            getLESSLoader(mode),
            getSASSLoader(mode),
            ...getAssetsLoader(mode),
          ],
        },
        plugins: [
          getVueLoaderPlugin(),
          getHtmlWebpackPlugin({
            template:reslove('./public/index.html'),
            filename:'index.html',
            minify: {
              caseSensitive: false, // 是否大小写敏感
              collapseWhitespace: true, // 是否去除空格
              removeAttributeQuotes: true, // 去掉属性引用
              removeComments: true, // 去注释
            },
          }),
          getMiniCssExtractPlugin(),
          getStylePlugin(),
        ],
        optimization: {
          minimize: true,
          minimizer: [new getTerserPlugin()],
        },
      });
    }
    
    ```

    

  - 封装常用loaders

    ```javascript
    // ./scripts/config/loader/index.js
    const MiniCssExtractPlugin = require("mini-css-extract-plugin");
    const eslintLoader = {
      loader: "eslint-loader",
      options: {
        fix: true,
      },
    };
    exports.getJSLoader = function (mode) {
      const use = [
        "thread-loader",
        {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"],
          },
        },
      ];
    
      mode === "development" && use.push(eslintLoader);
    
      return {
        test: /\.js$/,
        use,
        exclude: /node_modules/,
      };
    }
    
    exports.getVueLoader = function (mode) {
      const use = ["vue-loader"];
      mode === "development" && use.push(eslintLoader);
    
      return {
        test: /\.vue$/,
        use,
      };
    }
    
    exports.getCSSLoader = function (mode) {
      const use = [
        mode === "development" ? "style-loader" : MiniCssExtractPlugin.loader,
        "css-loader",
        "postcss-loader",
      ];
    
      return {
        test: /\.css$/i,
        use,
      };
    }
    
    exports.getLESSLoader = function (mode) {
      const use = [
        mode === "development" ? "style-loader" : MiniCssExtractPlugin.loader,
        "css-loader",
        "postcss-loader",
        "less-loader",
      ];
    
      return {
        test: /\.less$/i,
        use,
      };
    }
    exports.getSASSLoader = function (mode) {
      const use = [
        mode === "development" ? "style-loader" : MiniCssExtractPlugin.loader,
        "css-loader",
        "postcss-loader",
        "sass-loader",
      ];
    
      return {
        test: /\.less$/i,
        use,
      };
    }
    exports.getAssetsLoader = function (mode) {
    
        const name = mode === "development" ? "[name].[ext]" : "[name].[contenthash].[ext]"
    
        const imgReslove = {
            test: /\.(png|jpg|jpeg|svg|gif)$/,
            use: [
              {
                loader: "url-loader",
                options: {
                  name: `assets/images/${name}`,
                  limit: 3 * 1024,
                },
              },
            ],
          }
    
        const mediaReslove = {
            test: /\.(mp4|avi|mp3|rmvb|wmv|flv|webm)$/,
            use: [
              {
                loader: "file-loader",
                options: {
                  name: `assets/media/${name}`,
                },
              },
            ],
          }
    
        const fontReslove = {
            test: /\.(woff2|woff|eot|ttf|otf)$/,
            use: [
              {
                loader: "url-loader",
                options: {
                  name: `assets/font/${name}`,
                },
              },
            ],
          }
    
    
      return [imgReslove,mediaReslove,fontReslove];
    }
    
    ```

    

  - 封装常用plugins

    ```javascript
    // ./scripts/config/plugins/index.js
    
    const VueLoaderPlugin = require("vue-loader/lib/plugin");
    const HtmlWebpackPlugin = require("html-webpack-plugin");
    const { CleanWebpackPlugin } = require("clean-webpack-plugin");
    const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");
    const MiniCssExtractPlugin = require("mini-css-extract-plugin");
    const { HotModuleReplacementPlugin } = require('webpack');
    const TerserPlugin = require('terser-webpack-plugin');
    
    exports.getVueLoaderPlugin =  function () {
      return new VueLoaderPlugin();
    }
    
    exports.getHtmlWebpackPlugin = function (options) {
      return new HtmlWebpackPlugin(Object.assign({}, options));
    }
    
    exports.getCleanWebpackPlugin = function (options) {
      return new CleanWebpackPlugin(Object.assign({}, options));
    }
    exports.getStylePlugin = function (options) {
      return new OptimizeCssAssetsPlugin(
        Object.assign(
          {
            assetNameRegExp: /\.optimize\.css$/g,
            cssProcessor: require("cssnano"),
            cssProcessorPluginOptions: {
              preset: ["default", { discardComments: { removeAll: true } }],
            },
            canPrint: true,
          },
          options
        )
      );
    }
    exports.getMiniCssExtractPlugin = function (options) {
      return new MiniCssExtractPlugin(Object.assign({
        filename: 'assets/style/[name].[contenthash].css',
      }, options));
    }
    exports.getHotModuleReplacementPlugin = function () {
      return new HotModuleReplacementPlugin();
    }
    exports.getTerserPlugin = function (options) {
      return new TerserPlugin(
          Object.assign({
            cache: true, // 使用 cache，加快二次构建速度
            parallel: true, // 开启多线程
            extractComments: true,
            terserOptions: {
              compress: {
                unused: true,
                drop_debugger: true,
                drop_console: true,
                dead_code: true,
              },
            },
          },options)
      );
    }
    
    ```

- 工具函数

  ```javascript
  // ./scripts/config/util.js
  const path = require('path')
  
  exports.reslove = function  (url){
      return path.resolve(__dirname,`../../${url}`)
  }
  ```