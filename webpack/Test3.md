- ### Webpack 项目

  > 我们将使用前面所学的知识来从零开始搭建 `React-Cli` 和 `Vue-cli`。

  - #### `React-Cli`

    - ##### 开发模式配置

      ```js
      // config/webpack.dev.js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      // 专门做react代码HMR的插件
      const ReactRefreshWebpackPlugin = require("@pmmmwh/react-refresh-webpack-plugin");
      const CopyPlugin = require("copy-webpack-plugin");
      
      const getStyleLoaders = (preProcessor) => {
        return [
          "style-loader",
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  "postcss-preset-env", // 能解决大多数样式兼容性问题
                ],
              },
            },
          },
          preProcessor,
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: undefined,  // 开发模式没有输出
          filename: "static/js/[name].js",
          chunkFilename: "static/js/[name].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
        },
        module: {
          rules: [
            {
              oneOf: [
                {
                  // 用来匹配 .css 结尾的文件
                  test: /\.css$/,
                  // use 数组里面 Loader 执行顺序是从右到左
                  use: getStyleLoaders(),
                },
                {
                  test: /\.less$/,
                  use: getStyleLoaders("less-loader"),
                },
                {
                  test: /\.s[ac]ss$/,
                  use: getStyleLoaders("sass-loader"),
                },
                {
                  test: /\.styl$/,
                  use: getStyleLoaders("stylus-loader"),
                },
                {
                  test: /\.(png|jpe?g|gif|svg)$/,
                  type: "asset",
                  parser: {
                    dataUrlCondition: {
                      maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
                    },
                  },
                },
                {
                  test: /\.(ttf|woff2?)$/,
                  type: "asset/resource",
                },
                {
                  test: /\.(jsx|js)$/,
                  include: path.resolve(__dirname, "../src"),
                  loader: "babel-loader",
                  options: {
                    cacheDirectory: true,  // 开启缓存
                    cacheCompression: false,  // 缓存不做压缩，这样打包能再快一点
                    plugins: [
                      // "@babel/plugin-transform-runtime", // babel.config.js中包含了
                      "react-refresh/babel", // 开启react的js代码的HMR功能
                    ],
                  },
                },
              ],
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          new ReactRefreshWebpackPlugin(), // 解决js的HMR功能运行时全局变量的问题（react的HMR相关）
          }),
        ],
        optimization: {
          splitChunks: {
            chunks: "all",
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        // webpack解析模块加载时的配置
        resolve: {
          extensions: [".jsx", ".js", ".json"],  // 自动补全文件扩展名，令import..from 'App'时依次去找App.js/jsx/json
        },
        devServer: {
          open: true,
          host: "localhost",
          port: 3000,
          hot: true,
          static: {
            directory: path.join(__dirname, 'public'),
          },
          compress: true,
          historyApiFallback: true, // 解决react-router刷新404问题。404后开发服务器会返回index.html给你
        },
        mode: "development",
        devtool: "cheap-module-source-map",
      };
      ```
      
    - ##### 生产模式配置
  
      ```js
      // config/webpack.prod.js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const TerserWebpackPlugin = require("terser-webpack-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      const CopyPlugin = require("copy-webpack-plugin");
      
      const getStyleLoaders = (preProcessor) => {
        return [
          MiniCssExtractPlugin.loader,
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  "postcss-preset-env", // 能解决大多数样式兼容性问题
                ],
              },
            },
          },
          preProcessor,
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: path.resolve(__dirname, "../dist"),
          filename: "static/js/[name].[contenthash:10].js",
          chunkFilename: "static/js/[name].[contenthash:10].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
          clean: true,
        },
        module: {
          rules: [
            {
              oneOf: [
                {
                  // 用来匹配 .css 结尾的文件
                  test: /\.css$/,
                  // use 数组里面 Loader 执行顺序是从右到左
                  use: getStyleLoaders(),
                },
                {
                  test: /\.less$/,
                  use: getStyleLoaders("less-loader"),
                },
                {
                  test: /\.s[ac]ss$/,
                  use: getStyleLoaders("sass-loader"),
                },
                {
                  test: /\.styl$/,
                  use: getStyleLoaders("stylus-loader"),
                },
                {
                  test: /\.(png|jpe?g|gif|svg)$/,
                  type: "asset",
                  parser: {
                    dataUrlCondition: {
                      maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
                    },
                  },
                },
                {
                  test: /\.(ttf|woff2?)$/,
                  type: "asset/resource",
                },
                {
                  test: /\.(jsx|js)$/,
                  include: path.resolve(__dirname, "../src"),
                  loader: "babel-loader",
                  options: {
                    cacheDirectory: true,
                    cacheCompression: false,
                    plugins: [
                      // "@babel/plugin-transform-runtime" // babel的配置文件中已经包含了presets了
                    ],
                  },
                },
              ],
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          new MiniCssExtractPlugin({
            filename: "static/css/[name].[contenthash:10].css",
            chunkFilename: "static/css/[name].[contenthash:10].chunk.css",
          }),
          // 将public下面的资源复制到dist目录去（除了index.html）
          new CopyPlugin({
            patterns: [
              {
                from: path.resolve(__dirname, "../public"),
                to: path.resolve(__dirname, "../dist"),
                toType: "dir",
                noErrorOnMissing: true, // 不生成错误
                globOptions: {
                  // 忽略index.html文件，因为html-webpack-plugin已经处理好了
                  ignore: ["**/index.html"],
                },
                info: {
                  // 跳过terser压缩js
                  minimized: true,
                },
              },
            ],
          }),
        ],
        optimization: {
          // 压缩的操作
          minimizer: [
            new CssMinimizerPlugin(),  // css压缩
            new TerserWebpackPlugin(),  // 显式给出js的压缩插件，否则js压缩会有问题
            new ImageMinimizerPlugin({
              minimizer: {
                implementation: ImageMinimizerPlugin.imageminGenerate,
                options: {
                  plugins: [
                    ["gifsicle", { interlaced: true }],
                    ["jpegtran", { progressive: true }],
                    ["optipng", { optimizationLevel: 5 }],
                    [
                      "svgo",
                      {
                        plugins: [
                          "preset-default",
                          "prefixIds",
                          {
                            name: "sortAttrs",
                            params: {
                              xmlnsOrder: "alphabetical",
                            },
                          },
                        ],
                      },
                    ],
                  ],
                },
              },
            }),
          ],
          splitChunks: {
            chunks: "all",
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        resolve: {
          extensions: [".jsx", ".js", ".json"],
        },
        mode: "production",
        devtool: "source-map",
      };
      ```
  
    - ##### 其他配置
  
      ###### `package.json`：
  
      ```json
      {
        "name": "react-cli",
        "version": "1.0.0",
        "description": "",
        "main": "index.js",
        "scripts": {
          "start": "npm run dev",
          // 依赖cross-env这个库
          "dev": "cross-env NODE_ENV=development webpack serve --config ./config/webpack.dev.js",
          "build": "cross-env NODE_ENV=production webpack --config ./config/webpack.prod.js"
        },
        "keywords": [],
        "author": "",
        "license": "ISC",
        "devDependencies": {
          "@babel/core": "^7.17.10",
          "react-refresh": "^0.13.0",  // 是专门做react的HMR的插件
          "@pmmmwh/react-refresh-webpack-plugin": "^0.5.5",  // 是专门做react的HMR的插件
          "babel-loader": "^8.2.5",
          "babel-preset-react-app": "^10.0.1",
          "copy-webpack-plugin": "^10.2.4",  // 将public目录下的资源原封不动复制到dist中，需要用到该插件
          "cross-env": "^7.0.3",  // babel-preset-react-app需要依赖该库去提供环境变量：NODE_ENV或BABEL_ENV
          "css-loader": "^6.7.1",
          "css-minimizer-webpack-plugin": "^3.4.1",
          "eslint-config-react-app": "^7.0.1",
          "eslint-webpack-plugin": "^3.1.1",
          "html-webpack-plugin": "^5.5.0",
          "image-minimizer-webpack-plugin": "^3.2.3",
          "imagemin": "^8.0.1",
          "imagemin-gifsicle": "^7.0.0",
          "imagemin-jpegtran": "^7.0.0",
          "imagemin-optipng": "^8.0.0",
          "imagemin-svgo": "^10.0.1",
          "less-loader": "^10.2.0",
          "mini-css-extract-plugin": "^2.6.0",
          "postcss-loader": "^6.2.1",
          "postcss-preset-env": "^7.5.0",
          "sass-loader": "^12.6.0",
          "style-loader": "^3.3.1",
          "stylus-loader": "^6.2.0",
          "webpack": "^5.72.0",
          "webpack-cli": "^4.9.2",
          "webpack-dev-server": "^4.9.0"
        },
        "dependencies": {
          "antd": "^4.20.2",
          "react": "^18.1.0",
          "react-dom": "^18.1.0",
          "react-router-dom": "^6.3.0"
        },
        "browserslist": ["last 2 version", "> 1%", "not dead"]
      }
      ```
  
      ###### `.eslintrc.js`：
  
      ```js
      module.exports = {
        extends: ["react-app"], // 继承 react 的官方规则，指定eslint如何检查语法。需要安装这个插件：eslint-config-react-app
        parserOptions: {
          babelOptions: {
            presets: [
              // 解决页面eslint的报错。需要安装这个插件：babel-preset-react-app
              ["babel-preset-react-app", false],
              "babel-preset-react-app/prod",
            ],
          },
        },
      };
      ```
  
      ###### `babel.config.js`：
  
      ```js
      module.exports = {
        // 使用react官方规则，指定babel如何去编译
        presets: ["react-app"],
        // 这个预设中，给core-js、还有babel的runtime插件全都预置好了，因此什么也不用配置了
      };
      ```
  
    - ##### 合并开发和生产配置
  
      ###### `webpack.config.js`：
  
      ```js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const TerserWebpackPlugin = require("terser-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      const ReactRefreshWebpackPlugin = require("@pmmmwh/react-refresh-webpack-plugin");
      
      // 需要通过 cross-env 定义环境变量
      const isProduction = process.env.NODE_ENV === "production";
      
      const getStyleLoaders = (preProcessor) => {
        return [
          isProduction ? MiniCssExtractPlugin.loader : "style-loader",
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  "postcss-preset-env", // 能解决大多数样式兼容性问题
                ],
              },
            },
          },
          preProcessor,
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: isProduction ? path.resolve(__dirname, "../dist") : undefined,
          filename: isProduction
            ? "static/js/[name].[contenthash:10].js"
            : "static/js/[name].js",
          chunkFilename: isProduction
            ? "static/js/[name].[contenthash:10].chunk.js"
            : "static/js/[name].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
          clean: true,  // 改不改都行，因为开发模式没输出所以也没生效
        },
        module: {
          rules: [
            {
              oneOf: [
                {
                  // 用来匹配 .css 结尾的文件
                  test: /\.css$/,
                  // use 数组里面 Loader 执行顺序是从右到左
                  use: getStyleLoaders(),
                },
                {
                  test: /\.less$/,
                  use: getStyleLoaders("less-loader"),
                },
                {
                  test: /\.s[ac]ss$/,
                  use: getStyleLoaders("sass-loader"),
                },
                {
                  test: /\.styl$/,
                  use: getStyleLoaders("stylus-loader"),
                },
                {
                  test: /\.(png|jpe?g|gif|svg)$/,
                  type: "asset",
                  parser: {
                    dataUrlCondition: {
                      maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
                    },
                  },
                },
                {
                  test: /\.(ttf|woff2?)$/,
                  type: "asset/resource",
                },
                {
                  test: /\.(jsx|js)$/,
                  include: path.resolve(__dirname, "../src"),
                  loader: "babel-loader",
                  options: {
                    cacheDirectory: true, // 开启babel编译缓存
                    cacheCompression: false, // 缓存文件不要压缩
                    plugins: [
                      // "@babel/plugin-transform-runtime",  // presets中包含了
                      !isProduction && "react-refresh/babel",
                    ].filter(Boolean),
                  },
                },
              ],
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            extensions: [".js", ".jsx"],
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          isProduction && new MiniCssExtractPlugin({
              filename: "static/css/[name].[contenthash:10].css",
              chunkFilename: "static/css/[name].[contenthash:10].chunk.css",
            }),
          // 生产模式下，将public下面的资源复制到dist目录去（除了index.html）
          isProduction && new CopyPlugin({
            patterns: [
              {
                from: path.resolve(__dirname, "../public"),
                to: path.resolve(__dirname, "../dist"),
                toType: "dir",
                noErrorOnMissing: true,
                globOptions: {
                  ignore: ["**/index.html"],
                },
                info: { minimized: true, },
              },
            ],
          !isProduction && new ReactRefreshWebpackPlugin(),
        ].filter(Boolean),
        optimization: {
          minimize: isProduction,  // 是否进行压缩，即是否启用minimizer配置项
          // 压缩的操作
          minimizer: [
            new CssMinimizerPlugin(),  // 压缩css
            new TerserWebpackPlugin(),  // 压缩js
            new ImageMinimizerPlugin({  // 压缩图片
              minimizer: {
                implementation: ImageMinimizerPlugin.imageminGenerate,
                options: {
                  plugins: [
                    ["gifsicle", { interlaced: true }],
                    ["jpegtran", { progressive: true }],
                    ["optipng", { optimizationLevel: 5 }],
                    [
                      "svgo",
                      {
                        plugins: [
                          "preset-default",
                          "prefixIds",
                          {
                            name: "sortAttrs",
                            params: {
                              xmlnsOrder: "alphabetical",
                            },
                          },
                        ],
                      },
                    ],
                  ],
                },
              },
            }),
          ],
          // 代码分割配置
          splitChunks: {
            chunks: "all",
            // 其他都用默认值
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        resolve: {
          extensions: [".jsx", ".js", ".json"],
        },
        devServer: {  // 该配置需要我们运行时加serve选项，才会生效
          open: true,
          host: "localhost",
          port: 3000,
          hot: true,
          static: {
            directory: path.join(__dirname, 'public'),
          },
          compress: true,
          historyApiFallback: true,
        },
        mode: isProduction ? "production" : "development",
        devtool: isProduction ? "source-map" : "cheap-module-source-map",
      };
      ```
      
      ###### `package.json`：
      
      ```json
      {
        "name": "react-cli",
        "version": "1.0.0",
        "description": "",
        "main": "index.js",
        "scripts": {
          "start": "npm run dev",
          "dev": "cross-env NODE_ENV=development webpack serve --config ./config/webpack.config.js",
          "build": "cross-env NODE_ENV=production webpack --config ./config/webpack.config.js"
        },
        "keywords": [],
        "author": "",
        "license": "ISC",
        "devDependencies": {
          "@babel/core": "^7.17.10",
          "@pmmmwh/react-refresh-webpack-plugin": "^0.5.5",
          "babel-loader": "^8.2.5",
          "babel-preset-react-app": "^10.0.1",
          "cross-env": "^7.0.3",
          "css-loader": "^6.7.1",
          "css-minimizer-webpack-plugin": "^3.4.1",
          "eslint-config-react-app": "^7.0.1",
          "eslint-webpack-plugin": "^3.1.1",
          "html-webpack-plugin": "^5.5.0",
          "image-minimizer-webpack-plugin": "^3.2.3",
          "imagemin": "^8.0.1",
          "imagemin-gifsicle": "^7.0.0",
          "imagemin-jpegtran": "^7.0.0",
          "imagemin-optipng": "^8.0.0",
          "imagemin-svgo": "^10.0.1",
          "less-loader": "^10.2.0",
          "mini-css-extract-plugin": "^2.6.0",
          "react-refresh": "^0.13.0",
          "sass-loader": "^12.6.0",
          "style-loader": "^3.3.1",
          "stylus-loader": "^6.2.0",
          "webpack": "^5.72.0",
          "webpack-cli": "^4.9.2",
          "webpack-dev-server": "^4.9.0"
        },
        "dependencies": {
          "react": "^18.1.0",
          "react-dom": "^18.1.0",
          "react-router-dom": "^6.3.0"
        },
        "browserslist": ["last 2 version", "> 1%", "not dead"]
      }
      ```
      
    - ##### 优化配置
  
      > 1. 添加Ant-Design的自定义主题
      > 2. 设置node_modules下的js生成多个chunk
      >
      > **注意**：这里我们并没有做Ant-Design的按需加载，原因是：Ant-Design官方github中提到，按需加载会添加很多辅助代码，从而使样式体积变得很大。而且全部的样式体积也并不大，所以做按需引入的话反而不划算。
      >
      > 而且antd最新版本说了：`antd` 默认支持基于 ES modules 的 tree shaking，直接引入 `import { Button } from 'antd';` 就会有按需加载的效果。
      >
      > 因此我们不需要做按需加载相关的webpack配置。
      
      ```js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const TerserWebpackPlugin = require("terser-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      const ReactRefreshWebpackPlugin = require("@pmmmwh/react-refresh-webpack-plugin");
      const CopyPlugin = require("copy-webpack-plugin");
      
      const isProduction = process.env.NODE_ENV === "production";
      
      const getStyleLoaders = (preProcessor) => {
        return [
          isProduction ? MiniCssExtractPlugin.loader : "style-loader",
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  "postcss-preset-env",
                ],
              },
            },
          },
          preProcessor && {
            loader: preProcessor,
            options:
              preProcessor === "less-loader"
                ? {
                    // antd的自定义主题
                    lessOptions: {
                      modifyVars: {
                        // 其他主题色：https://ant.design/docs/react/customize-theme-cn
                        "@primary-color": "#1DA57A", // 全局主色
                      },
                      javascriptEnabled: true,
                    },
                  }
                : {},
          },
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: isProduction ? path.resolve(__dirname, "../dist") : undefined,
          filename: isProduction
            ? "static/js/[name].[contenthash:10].js"
            : "static/js/[name].js",
          chunkFilename: isProduction
            ? "static/js/[name].[contenthash:10].chunk.js"
            : "static/js/[name].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
          clean: true,
        },
        module: {
          rules: [
            {
              oneOf: [
                {
                  test: /\.css$/,
                  use: getStyleLoaders(),
                },
                {
                  test: /\.less$/,
                  use: getStyleLoaders("less-loader"),
                },
                {
                  test: /\.s[ac]ss$/,
                  use: getStyleLoaders("sass-loader"),
                },
                {
                  test: /\.styl$/,
                  use: getStyleLoaders("stylus-loader"),
                },
                {
                  test: /\.(png|jpe?g|gif|svg)$/,
                  type: "asset",
                  parser: {
                    dataUrlCondition: {
                      maxSize: 10 * 1024,
                    },
                  },
                },
                {
                  test: /\.(ttf|woff2?)$/,
                  type: "asset/resource",
                },
                {
                  test: /\.(jsx|js)$/,
                  include: path.resolve(__dirname, "../src"),
                  loader: "babel-loader",
                  options: {
                    cacheDirectory: true,
                    cacheCompression: false,
                    plugins: [
                      // "@babel/plugin-transform-runtime",  // presets中包含了
                      !isProduction && "react-refresh/babel",
                    ].filter(Boolean),
                  },
                },
              ],
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            extensions: [".js", ".jsx"],
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          isProduction &&
            new MiniCssExtractPlugin({
              filename: "static/css/[name].[contenthash:10].css",
              chunkFilename: "static/css/[name].[contenthash:10].chunk.css",
            }),
          !isProduction && new ReactRefreshWebpackPlugin(),
          // 将public下面的资源复制到dist目录去（除了index.html）
          !isProduction && new CopyPlugin({
            patterns: [
              {
                from: path.resolve(__dirname, "../public"),
                to: path.resolve(__dirname, "../dist"),
                toType: "dir",
                noErrorOnMissing: true, // 不生成错误
                globOptions: {
                  // 忽略文件
                  ignore: ["**/index.html"],
                },
                info: {
                  // 跳过terser压缩js
                  minimized: true,
                },
              },
            ],
          }),
        ].filter(Boolean),
        optimization: {
          minimize: isProduction,
          // 压缩的操作
          minimizer: [
            // 压缩css
            new CssMinimizerPlugin(),
            // 压缩js
            new TerserWebpackPlugin(),
            // 压缩图片
            new ImageMinimizerPlugin({
              minimizer: {
                implementation: ImageMinimizerPlugin.imageminGenerate,
                options: {
                  plugins: [
                    ["gifsicle", { interlaced: true }],
                    ["jpegtran", { progressive: true }],
                    ["optipng", { optimizationLevel: 5 }],
                    [
                      "svgo",
                      {
                        plugins: [
                          "preset-default",
                          "prefixIds",
                          {
                            name: "sortAttrs",
                            params: {
                              xmlnsOrder: "alphabetical",
                            },
                          },
                        ],
                      },
                    ],
                  ],
                },
              },
            }),
          ],
          splitChunks: {
            chunks: "all",
            // 手动调整打包的cache组
            cacheGroups: {
              // layouts通常是admin项目的主体布局组件，所有路由组件都要使用的
              // 可以单独打包，从而复用
              // 如果项目中没有，请删除
              layouts: {
                name: "chunk-layouts",
                test: path.resolve(__dirname, "../src/layouts"),
                priority: 40,
              },
              // 如果项目中使用antd，此时将所有node_modules打包在一起，那么打包输出文件会比较大。
              // 所以我们将node_modules中比较大的模块单独打包，从而并行加载速度更好
              // 如果项目中没有，请删除
              antd: {
                name: "chunk-antd",
                test: /[\\/]node_modules[\\/]antd(.*)/,
                priority: 30,
              },
              // 将react相关的库单独打包，减少node_modules的chunk体积（react、react-dom、react-router-dom）
              react: {
                name: "chunk-react",
                test: /[\\/]node_modules[\\/]react(.*)?[\\/]/,
                chunks: "initial",
                priority: 20,
              },
              // 其余node_modules中的库单独打包到一个chunk中
              libs: {
                name: "chunk-libs",
                test: /[\\/]node_modules[\\/]/,
                priority: 10, // 权重最低，优先考虑前面内容
                chunks: "initial",
              },
            },
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        resolve: {
          extensions: [".jsx", ".js", ".json"],
        },
        devServer: {
          open: true,
          host: "localhost",
          port: 3000,
          hot: true,
          static: {
            directory: path.join(__dirname, 'public'),
          },
          compress: true,
          historyApiFallback: true,
        },
        mode: isProduction ? "production" : "development",
        devtool: isProduction ? "source-map" : "cheap-module-source-map",
        performance: false, // 关闭性能分析，提示打包速度
      };
      ```
  
  - #### `Vue-cli`
  
    - ##### 开发模式配置
  
      ```js
      // webpack.dev.js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const { VueLoaderPlugin } = require("vue-loader");
      // 需要该插件去给vue定义2个环境变量，否则控制台会warn；cross-env插件负责给webpack定义环境变量的，它不能给vue用
      const { DefinePlugin } = require("webpack");
      const CopyPlugin = require("copy-webpack-plugin");
      
      const getStyleLoaders = (preProcessor) => {
        return [
          "vue-style-loader",
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  "postcss-preset-env",
                ],
              },
            },
          },
          preProcessor,
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: undefined,
          filename: "static/js/[name].js",
          chunkFilename: "static/js/[name].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
        },
        module: {
          rules: [
            {
              // 用来匹配 .css 结尾的文件
              test: /\.css$/,
              // use 数组里面 Loader 执行顺序是从右到左
              use: getStyleLoaders(),
            },
            {
              test: /\.less$/,
              use: getStyleLoaders("less-loader"),
            },
            {
              test: /\.s[ac]ss$/,
              use: getStyleLoaders("sass-loader"),
            },
            {
              test: /\.styl$/,
              use: getStyleLoaders("stylus-loader"),
            },
            {
              test: /\.(png|jpe?g|gif|svg)$/,
              type: "asset",
              parser: {
                dataUrlCondition: {
                  maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
                },
              },
            },
            {
              test: /\.(ttf|woff2?)$/,
              type: "asset/resource",
            },
            {
              test: /\.(jsx|js)$/,
              include: path.resolve(__dirname, "../src"),
              loader: "babel-loader",
              options: {
                cacheDirectory: true,
                cacheCompression: false,
                plugins: [
                  // "@babel/plugin-transform-runtime" // presets中包含了
                ],
              },
            },
            // vue-loader不支持oneOf
            {
              test: /\.vue$/,
              loader: "vue-loader", // 内部会给vue文件注入HMR功能代码
              options: {
                // 开启缓存
                cacheDirectory: path.resolve(
                  __dirname,
                  "node_modules/.cache/vue-loader"
                ),
              },
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          new VueLoaderPlugin(),
          // 给vue定义2个环境变量让它去用，解决f12页面warn
          new DefinePlugin({
            __VUE_OPTIONS_API__: "true",
            __VUE_PROD_DEVTOOLS__: "false",
          }),
        ],
        optimization: {
          splitChunks: {
            chunks: "all",
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        resolve: {
          extensions: [".vue", ".js", ".json"], // 自动补全文件扩展名，让import可以导入vue文件
        },
        devServer: {
          open: true,
          host: "localhost",
          port: 3000,
          hot: true,
      		static: {
            directory: path.join(__dirname, 'public'),
          },
          compress: true,
          historyApiFallback: true, // 解决vue-router前端history模式路由，页面刷新404问题
        },
        mode: "development",
        devtool: "cheap-module-source-map",
      };
      ```
    
    - ##### 生产模式配置
    
      ```js
      // webpack.prod.js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const TerserWebpackPlugin = require("terser-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      const { VueLoaderPlugin } = require("vue-loader");
      const { DefinePlugin } = require("webpack");
      
      const getStyleLoaders = (preProcessor) => {
        return [
          MiniCssExtractPlugin.loader,  // 生产模式下还是用该插件，生成单独的css文件
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: [
                  "postcss-preset-env", // 能解决大多数样式兼容性问题
                ],
              },
            },
          },
          preProcessor,
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: undefined,
          filename: "static/js/[name].[contenthash:10].js",
          chunkFilename: "static/js/[name].[contenthash:10].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
          clean: true,
        },
        module: {
          rules: [
            {
              // 用来匹配 .css 结尾的文件
              test: /\.css$/,
              // use 数组里面 Loader 执行顺序是从右到左
              use: getStyleLoaders(),
            },
            {
              test: /\.less$/,
              use: getStyleLoaders("less-loader"),
            },
            {
              test: /\.s[ac]ss$/,
              use: getStyleLoaders("sass-loader"),
            },
            {
              test: /\.styl$/,
              use: getStyleLoaders("stylus-loader"),
            },
            {
              test: /\.(png|jpe?g|gif|svg)$/,
              type: "asset",
              parser: {
                dataUrlCondition: {
                  maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
                },
              },
            },
            {
              test: /\.(ttf|woff2?)$/,
              type: "asset/resource",
            },
            {
              test: /\.(jsx|js)$/,
              include: path.resolve(__dirname, "../src"),
              loader: "babel-loader",
              options: {
                cacheDirectory: true,
                cacheCompression: false,
                plugins: [
                  // "@babel/plugin-transform-runtime" // presets中包含了
                ],
              },
            },
            // vue-loader不支持oneOf
            {
              test: /\.vue$/,
              loader: "vue-loader", // 内部会给vue文件注入HMR功能代码
              options: {
                // 开启缓存
                cacheDirectory: path.resolve(
                  __dirname,
                  "node_modules/.cache/vue-loader"
                ),
              },
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          new CopyPlugin({
            patterns: [
              {
                from: path.resolve(__dirname, "../public"),
                to: path.resolve(__dirname, "../dist"),
                toType: "dir",
                noErrorOnMissing: true,
                globOptions: {
                  ignore: ["**/index.html"],
                },
                info: {
                  minimized: true,
                },
              },
            ],
          }),
          new MiniCssExtractPlugin({
            filename: "static/css/[name].[contenthash:10].css",
            chunkFilename: "static/css/[name].[contenthash:10].chunk.css",
          }),
          new VueLoaderPlugin(),
          new DefinePlugin({
            __VUE_OPTIONS_API__: "true",
            __VUE_PROD_DEVTOOLS__: "false",
          }),
        ],
        optimization: {
          // 压缩的操作
          minimizer: [
            new CssMinimizerPlugin(),
            new TerserWebpackPlugin(),
            new ImageMinimizerPlugin({
              minimizer: {
                implementation: ImageMinimizerPlugin.imageminGenerate,
                options: {
                  plugins: [
                    ["gifsicle", { interlaced: true }],
                    ["jpegtran", { progressive: true }],
                    ["optipng", { optimizationLevel: 5 }],
                    [
                      "svgo",
                      {
                        plugins: [
                          "preset-default",
                          "prefixIds",
                          {
                            name: "sortAttrs",
                            params: {
                              xmlnsOrder: "alphabetical",
                            },
                          },
                        ],
                      },
                    ],
                  ],
                },
              },
            }),
          ],
          splitChunks: {
            chunks: "all",
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        resolve: {
          extensions: [".vue", ".js", ".json"],
        },
        mode: "production",
        devtool: "source-map",
      };
      ```
    
    - ##### 其他配置
    
      ###### `package.json`：
    
      ```json
      {
        "name": "vue-cli",
        "version": "1.0.0",
        "description": "",
        "main": "main.js",
        "scripts": {
          "start": "npm run dev",
          "dev": "cross-env NODE_ENV=development webpack serve --config ./config/webpack.dev.js",
          "build": "cross-env NODE_ENV=production webpack --config ./config/webpack.prod.js"
        },
        "keywords": [],
        "author": "",
        "license": "ISC",
        "devDependencies": {
          "@babel/core": "^7.17.10",
          "@babel/eslint-parser": "^7.17.0",  // vue的eslint解析选项插件
          "@vue/cli-plugin-babel": "^5.0.4",  // vue的babel插件
          "babel-loader": "^8.2.5",
          "copy-webpack-plugin": "^10.2.4",
          "cross-env": "^7.0.3",
          "css-loader": "^6.7.1",
          "css-minimizer-webpack-plugin": "^3.4.1",
          "eslint-plugin-vue": "^8.7.1",  // vue的eslint插件
          "eslint-webpack-plugin": "^3.1.1",
          "html-webpack-plugin": "^5.5.0",
          "image-minimizer-webpack-plugin": "^3.2.3",
          "imagemin": "^8.0.1",
          "imagemin-gifsicle": "^7.0.0",
          "imagemin-jpegtran": "^7.0.0",
          "imagemin-optipng": "^8.0.0",
          "imagemin-svgo": "^10.0.1",
          "less-loader": "^10.2.0",
          "mini-css-extract-plugin": "^2.6.0",
          "postcss-preset-env": "^7.5.0",
          "sass-loader": "^12.6.0",
          "stylus-loader": "^6.2.0",
          "vue-loader": "^17.0.0",  // 处理vue文件的loader
          "vue-style-loader": "^4.1.3",  // 开发模式下处理vue样式的loader，不需要style-loader了
          "vue-template-compiler": "^2.6.14",  // vue的模板编译器
          "webpack": "^5.72.0",
          "webpack-cli": "^4.9.2",
          "webpack-dev-server": "^4.9.0"
        },
        "dependencies": {
          "vue": "^3.2.33",
          "vue-router": "^4.0.15"
        },
        "browserslist": ["last 2 version", "> 1%", "not dead"]
      }
      ```
    
      ###### `.eslintrc.js`：
    
      ```js
      module.exports = {
        root: true,
        env: {
          node: true,
        },
        extends: ["plugin:vue/vue3-essential", "eslint:recommended"],  // 需要安装插件：eslint-plugin-vue
        parserOptions: {
          parser: "@babel/eslint-parser",  // 需要安装插件：@babel/eslint-parser
        },
      };
      ```
    
      ###### `babel.config.js`：
    
      ```js
      module.exports = {
        presets: ["@vue/cli-plugin-babel/preset"],  // 需要安装插件：@vue/cli-plugin-babel
      };
      ```
    
    - ##### 合并开发和生产配置
    
      ```js
      // webpack.config.js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const TerserWebpackPlugin = require("terser-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      const { VueLoaderPlugin } = require("vue-loader");
      const { DefinePlugin } = require("webpack");
      const CopyPlugin = require("copy-webpack-plugin");
      
      // 需要通过 cross-env 定义环境变量
      const isProduction = process.env.NODE_ENV === "production";
      
      const getStyleLoaders = (preProcessor) => {
        return [
          isProduction ? MiniCssExtractPlugin.loader : "vue-style-loader",
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: ["postcss-preset-env"],
              },
            },
          },
          preProcessor,
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: isProduction ? path.resolve(__dirname, "../dist") : undefined,
          filename: isProduction
            ? "static/js/[name].[contenthash:10].js"
            : "static/js/[name].js",
          chunkFilename: isProduction
            ? "static/js/[name].[contenthash:10].chunk.js"
            : "static/js/[name].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
          clean: true,
        },
        module: {
          rules: [
            {
              // 用来匹配 .css 结尾的文件
              test: /\.css$/,
              // use 数组里面 Loader 执行顺序是从右到左
              use: getStyleLoaders(),
            },
            {
              test: /\.less$/,
              use: getStyleLoaders("less-loader"),
            },
            {
              test: /\.s[ac]ss$/,
              use: getStyleLoaders("sass-loader"),
            },
            {
              test: /\.styl$/,
              use: getStyleLoaders("stylus-loader"),
            },
            {
              test: /\.(png|jpe?g|gif|svg)$/,
              type: "asset",
              parser: {
                dataUrlCondition: {
                  maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
                },
              },
            },
            {
              test: /\.(ttf|woff2?)$/,
              type: "asset/resource",
            },
            {
              test: /\.(jsx|js)$/,
              include: path.resolve(__dirname, "../src"),
              loader: "babel-loader",
              options: {
                cacheDirectory: true,
                cacheCompression: false,
                plugins: [
                  // "@babel/plugin-transform-runtime" // presets中包含了
                ],
              },
            },
            // vue-loader不支持oneOf
            {
              test: /\.vue$/,
              loader: "vue-loader", // 内部会给vue文件注入HMR功能代码
              options: {
                // 开启缓存
                cacheDirectory: path.resolve(__dirname, "node_modules/.cache/vue-loader"),
              },
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          isProduction && new CopyPlugin({
            patterns: [
              {
                from: path.resolve(__dirname, "../public"),
                to: path.resolve(__dirname, "../dist"),
                toType: "dir",
                noErrorOnMissing: true,
                globOptions: {
                  ignore: ["**/index.html"],
                },
                info: {
                  minimized: true,
                },
              },
            ],
          }),
          isProduction && new MiniCssExtractPlugin({
              filename: "static/css/[name].[contenthash:10].css",
              chunkFilename: "static/css/[name].[contenthash:10].chunk.css",
            }),
          new VueLoaderPlugin(),
          new DefinePlugin({
            __VUE_OPTIONS_API__: "true",
            __VUE_PROD_DEVTOOLS__: "false",
          }),
        ].filter(Boolean),
        optimization: {
          minimize: isProduction,
          // 压缩的操作
          minimizer: [
            new CssMinimizerPlugin(),
            new TerserWebpackPlugin(),
            new ImageMinimizerPlugin({
              minimizer: {
                implementation: ImageMinimizerPlugin.imageminGenerate,
                options: {
                  plugins: [
                    ["gifsicle", { interlaced: true }],
                    ["jpegtran", { progressive: true }],
                    ["optipng", { optimizationLevel: 5 }],
                    [
                      "svgo",
                      {
                        plugins: [
                          "preset-default",
                          "prefixIds",
                          {
                            name: "sortAttrs",
                            params: {
                              xmlnsOrder: "alphabetical",
                            },
                          },
                        ],
                      },
                    ],
                  ],
                },
              },
            }),
          ],
          splitChunks: {
            chunks: "all",
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        resolve: {
          extensions: [".vue", ".js", ".json"],
        },
        devServer: {
          open: true,
          host: "localhost",
          port: 3000,
          hot: true,
      		static: {
            directory: path.join(__dirname, 'public'),  // 这样index.html中就能访问到public下的静态资源了。其实不需要配置，默认是true
          },
          compress: true,  // 默认是true
          historyApiFallback: true, // 解决vue-router刷新404问题
        },
        mode: isProduction ? "production" : "development",
        devtool: isProduction ? "source-map" : "cheap-module-source-map",
      };
      ```
      
    - ##### 优化配置
    
      > 1. 添加Element-plus的自定义主题
      > 2. 设置node_modules下的js生成多个chunk
      
      ```js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      const TerserWebpackPlugin = require("terser-webpack-plugin");
      const CopyPlugin = require("copy-webpack-plugin");
      const { VueLoaderPlugin } = require("vue-loader");
      const { DefinePlugin } = require("webpack");
      const AutoImport = require("unplugin-auto-import/webpack");
      const Components = require("unplugin-vue-components/webpack");
      const { ElementPlusResolver } = require("unplugin-vue-components/resolvers");
      // 需要通过 cross-env 定义环境变量
      const isProduction = process.env.NODE_ENV === "production";
      
      const getStyleLoaders = (preProcessor) => {
        return [
          isProduction ? MiniCssExtractPlugin.loader : "vue-style-loader",
          "css-loader",
          {
            loader: "postcss-loader",
            options: {
              postcssOptions: {
                plugins: ["postcss-preset-env"],
              },
            },
          },
          preProcessor && {
            loader: preProcessor,
            options:
              preProcessor === "sass-loader"
                ? {
                    // 按需导入时，自定义主题：使用我们自己写的index.scss文件去覆盖掉默认的主题变量（全量导入时，import 'my.scss';导入我们自定义的样式文件即可）
                    additionalData: `@use "@/styles/element/index.scss" as *;`,
                  }
                : {},
          },
        ].filter(Boolean);
      };
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: isProduction ? path.resolve(__dirname, "../dist") : undefined,
          filename: isProduction
            ? "static/js/[name].[contenthash:10].js"
            : "static/js/[name].js",
          chunkFilename: isProduction
            ? "static/js/[name].[contenthash:10].chunk.js"
            : "static/js/[name].chunk.js",
          assetModuleFilename: "static/js/[hash:10][ext][query]",
          clean: true,
        },
        module: {
          rules: [
            {
              test: /\.css$/,
              use: getStyleLoaders(),
            },
            {
              test: /\.less$/,
              use: getStyleLoaders("less-loader"),
            },
            {
              test: /\.s[ac]ss$/,
              use: getStyleLoaders("sass-loader"),
            },
            {
              test: /\.styl$/,
              use: getStyleLoaders("stylus-loader"),
            },
            {
              test: /\.(png|jpe?g|gif|svg)$/,
              type: "asset",
              parser: {
                dataUrlCondition: {
                  maxSize: 10 * 1024,
                },
              },
            },
            {
              test: /\.(ttf|woff2?)$/,
              type: "asset/resource",
            },
            {
              test: /\.(jsx|js)$/,
              include: path.resolve(__dirname, "../src"),
              loader: "babel-loader",
              options: {
                cacheDirectory: true,
                cacheCompression: false,
                plugins: [
                  // "@babel/plugin-transform-runtime" // presets中包含了
                ],
              },
            },
            // vue-loader不支持oneOf
            {
              test: /\.vue$/,
              loader: "vue-loader", // 内部会给vue文件注入HMR功能代码
              options: {
                // 开启缓存
                cacheDirectory: path.resolve(
                  __dirname,
                  "node_modules/.cache/vue-loader"
                ),
              },
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules",
            cache: true,
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          isProduction && new CopyPlugin({
            patterns: [
              {
                from: path.resolve(__dirname, "../public"),
                to: path.resolve(__dirname, "../dist"),
                toType: "dir",
                noErrorOnMissing: true,
                globOptions: {
                  ignore: ["**/index.html"],
                },
                info: {
                  minimized: true,
                },
              },
            ],
          }),
          isProduction && new MiniCssExtractPlugin({
              filename: "static/css/[name].[contenthash:10].css",
              chunkFilename: "static/css/[name].[contenthash:10].chunk.css",
            }),
          new VueLoaderPlugin(),
          new DefinePlugin({
            __VUE_OPTIONS_API__: "true",
            __VUE_PROD_DEVTOOLS__: "false",
          }),
          // 按需加载element-plus组件样式
          AutoImport({
            resolvers: [ElementPlusResolver()],
          }),
          Components({
            resolvers: [
              ElementPlusResolver({
                importStyle: "sass", // 自定义主题
              }),
            ],
          }),
        ].filter(Boolean),
        optimization: {
          minimize: isProduction,
          // 压缩的操作
          minimizer: [
            new CssMinimizerPlugin(),
            new TerserWebpackPlugin(),
            new ImageMinimizerPlugin({
              minimizer: {
                implementation: ImageMinimizerPlugin.imageminGenerate,
                options: {
                  plugins: [
                    ["gifsicle", { interlaced: true }],
                    ["jpegtran", { progressive: true }],
                    ["optipng", { optimizationLevel: 5 }],
                    [
                      "svgo",
                      {
                        plugins: [
                          "preset-default",
                          "prefixIds",
                          {
                            name: "sortAttrs",
                            params: {
                              xmlnsOrder: "alphabetical",
                            },
                          },
                        ],
                      },
                    ],
                  ],
                },
              },
            }),
          ],
          splitChunks: {
            chunks: "all",
            cacheGroups: {
              // layouts通常是admin项目的主体布局组件，所有路由组件都要使用的
              // 可以单独打包，从而复用
              // 如果项目中没有，请删除
              layouts: {
                name: "layouts",
                test: path.resolve(__dirname, "../src/layouts"),
                priority: 40,
              },
              // 如果项目中使用element-plus，此时将所有node_modules打包在一起，那么打包输出文件会比较大。
              // 所以我们将node_modules中比较大的模块单独打包，从而并行加载速度更好
              // 如果项目中没有，请删除
              elementUI: {
                name: "chunk-elementPlus",
                test: /[\\/]node_modules[\\/]_?element-plus(.*)/,
                priority: 30,
              },
              // 将vue相关的库单独打包，减少node_modules的chunk体积。
              vue: {
                name: "chunk-vue",
                test: /[\\/]node_modules[\\/]vue(.*)[\\/]/,
                chunks: "initial",
                priority: 20,
              },
              libs: {
                name: "chunk-libs",
                test: /[\\/]node_modules[\\/]/,
                priority: 10, // 权重最低，优先考虑前面内容
                chunks: "initial",
              },
            },
          },
          runtimeChunk: {
            name: (entrypoint) => `runtime~${entrypoint.name}`,
          },
        },
        resolve: {
          extensions: [".vue", ".js", ".json"],
          alias: {
            // 路径别名
            "@": path.resolve(__dirname, "../src"),
          },
        },
        devServer: {
          open: true,
          host: "localhost",
          port: 3000,
          hot: true,
          static: {
            directory: path.join(__dirname, 'public'),
          },
          compress: true,
          historyApiFallback: true, // 解决vue-router刷新404问题
        },
        mode: isProduction ? "production" : "development",
        devtool: isProduction ? "source-map" : "cheap-module-source-map",
        performance: false,
      };
      ```

------

