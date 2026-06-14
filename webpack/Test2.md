- ### Webpack 的高级配置

  > 本章节主要介绍 Webpack 高级配置。
  >
  > 所谓高级配置其实就是进行 Webpack 优化，让我们代码在编译/运行时性能更好~
  >
  > 我们会从以下角度来进行优化：
  >
  > 1. 提升开发体验
  > 2. 提升打包构建速度
  > 3. 减少代码体积
  > 4. 优化代码运行性能

  - #### 提升开发体验

    - ##### 关于SourceMap
  
      > 开发时我们运行的代码是经过 webpack 编译后的，例如：
  
      ```js
      /*
       * ATTENTION: The "eval" devtool has been used (maybe by default in mode: "development").
       * This devtool is neither made for production nor for readable output files.
       * It uses "eval()" calls to create a separate source file in the browser devtools.
       * If you are trying to read the output file, select a different devtool (https://webpack.js.org/configuration/devtool/)
       * or disable the default devtool with "devtool: false".
       * If you are looking for production-ready output files, see mode: "production" (https://webpack.js.org/configuration/mode/).
       */
      /******/ (() => { // webpackBootstrap
      /******/ 	"use strict";
      /******/ 	var __webpack_modules__ = ({
      
      /***/ "./node_modules/css-loader/dist/cjs.js!./node_modules/less-loader/dist/cjs.js!./src/less/index.less":
      /*!**********************************************************************************************************!*\
        !*** ./node_modules/css-loader/dist/cjs.js!./node_modules/less-loader/dist/cjs.js!./src/less/index.less ***!
        \**********************************************************************************************************/
      /***/ ((module, __webpack_exports__, __webpack_require__) => {
      
      eval("__webpack_require__.r(__webpack_exports__);\n/* harmony export */ __webpack_require__.d(__webpack_exports__, {\n/* harmony export */   \"default\": () => (__WEBPACK_DEFAULT_EXPORT__)\n/* harmony export */ });\n/* harmony import */ var _node_modules_css_loader_dist_runtime_noSourceMaps_js__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ../../node_modules/css-loader/dist/runtime/noSourceMaps.js */ \"./node_modules/css-loader/dist/runtime/noSourceMaps.js\");\n/* harmony import */ var _node_modules_css_loader_dist_runtime_noSourceMaps_js__WEBPACK_IMPORTED_MODULE_0___default = /*#__PURE__*/__webpack_require__.n(_node_modules_css_loader_dist_runtime_noSourceMaps_js__WEBPACK_IMPORTED_MODULE_0__);\n/* harmony import */ var _node_modules_css_loader_dist_runtime_api_js__WEBPACK_IMPORTED_MODULE_1__ = __webpack_require__(/*! ../../node_modules/css-loader/dist/runtime/api.js */ \"./node_modules/css-loader/dist/runtime/api.js\");\n/* harmony import */ var _node_modules_css_loader_dist_runtime_api_js__WEBPACK_IMPORTED_MODULE_1___default = /*#__PURE__*/__webpack_require__.n(_node_modules_css_loader_dist_runtime_api_js__WEBPACK_IMPORTED_MODULE_1__);\n// Imports\n\n\nvar ___CSS_LOADER_EXPORT___ = _node_modules_css_loader_dist_runtime_api_js__WEBPACK_IMPORTED_MODULE_1___default()((_node_modules_css_loader_dist_runtime_noSourceMaps_js__WEBPACK_IMPORTED_MODULE_0___default()));\n// Module\n___CSS_LOADER_EXPORT___.push([module.id, \".box2 {\\n  width: 100px;\\n  height: 100px;\\n  background-color: deeppink;\\n}\\n\", \"\"]);\n// Exports\n/* harmony default export */ const __WEBPACK_DEFAULT_EXPORT__ = (___CSS_LOADER_EXPORT___);\n\n\n//# sourceURL=webpack://webpack5/./src/less/index.less?./node_modules/css-loader/dist/cjs.js!./node_modules/less-loader/dist/cjs.js");
      
      /***/ }),
      // 其他省略
      ```
  
      > 可以看到，所有 css 和 js 合并成了一个文件，并且多了其他代码。此时如果代码运行出错，根据F12中的提示，我们是找不到代码真正出错的地方的。一旦将来开发代码文件很多，那么就很难去定位到真正出现错误的位置了。
      >
      > 所以我们需要更加准确的错误提示，来帮助我们更好的开发代码。
      >
      > **SourceMap（源代码映射）是一个源代码与构建后代码的映射文件**。它是一个 `.map` 文件，里面包含源代码和构建后代码每一行、每一列的映射关系。
      >
      > 当构建后的代码运行报错了，浏览器F12就可以通过 `.map` 文件，从构建后代码出错位置找到源代码的出错位置，从而给我们更准确的提示，帮助我们更快的找到错误源。
  
    - ##### Webpack中启用SourceMap
  
      ```js
      module.exports = {
        // 其他省略
        mode: "production",
        devtool: "source-map",
      };
      ```
      
      > `devtool` 配置项用于控制是否生成、以及如何生成 SourceMap，值是一个字符串或`false`。`false`用于显式设置不生成 SourceMap，强制覆盖开发或生产模式的默认值。其中字符串值有[26种](https://webpack.docschina.org/configuration/devtool/#devtool)，下面是常用的一些：
      >
      > **注意：**SourceMap可能是一个单独的文件，也可能添加在bundle.js文件中，这两种情况都叫生成了SourceMap。
      
      - `'eval'`（开发模式下的默认值）：每个模块都使用 `eval()` 执行，并且都有 `//# sourceURL`。由于eval()是在内存中执行，所以没有生成SourceMap文件。此选项会非常快地构建。主要缺点是，由于会映射到转换后的代码，而不是映射到原始代码（没有从 loader 中获取 source map），所以不能正确的显示行数，只能显示列数。
      
      - `(none)`，省略`devtool`选项（生产模式下的默认值）：此时不会生成SourceMap。
      
      - `'source-map'`：整个 source map 作为一个单独的文件生成，包含行/列映射；缺点是打包编译速度更慢。它是为 bundle 添加了一个引用注释，以便开发工具F12知道在哪里可以找到它。它**常用于生产环境中**。
      
        > **警告**：你应该将你的服务器配置为，不允许普通用户访问 source map 文件！
      
      - `'cheap-module-source-map'`：没有列映射(column mapping)的 source map，忽略 loader source map。它对于开发环境和生产环境都不理想，是一些特定场景下需要的，例如一些第三方工具。
      
      > 选择不同的值，最终打包的速度、构建产物的大小都会有所差异。
      >
      > 一般开发环境下：
      >
      > 1. **速度优先**：`eval-cheap-source-map`
      > 2. **调试精度优先**：`eval-source-map`
      > 3. **需要调试loader原始代码**：`cheap-module-source-map`
      >
      > Webpack官方文档也特别指出：在开发环境下不需要关注`.map`文件体积，因此应该优先选择带有`eval`的配置以获得更好的重建性能。
      
  
  - #### 提升打包构建速度
  
    - ##### HotModuleReplacement（它只针对开发模式）
  
      > 开发时我们修改了其中一个模块代码，Webpack 默认会将所有模块全部重新打包编译，速度很慢。所以我们需要做到修改某个模块代码，就只有这个模块代码需要重新打包编译，其他模块不变，这样打包速度就能很快。
      >
      > **HotModuleReplacement（HMR/热模块替换**）：在程序运行中动态的替换、添加或删除模块，而无需重新加载整个页面。
      >
      > HMR只能用于开发环境，生产环境不需要。
  
      ###### 基本配置：
      
      > ```js
      > module.exports = {
      >  // 其他省略
      >  devServer: {
      >     host: "localhost", // 启动服务器域名
      >     port: "3000", // 启动服务器端口号
      >     open: true, // 是否自动打开浏览器
      >     hot: true, // 开启HMR功能（默认值true）
      >     static: {
      >       directory: path.join(__dirname, 'public'),  // 这样index.html中就能访问到public下的静态资源了
      >     },
      >     compress: true,
      >  },
      > };
      > ```
      >
      > 此时 css 样式经过 style-loader 处理，已经具备 HMR 功能了。 但是 js 还不行。
      
      > 为什么CSS有HMR而JS没有？
      >
      > 在你的Webpack配置中，虽然已经启用了`hot: true`来开启HMR，但CSS和JS的行为不同，是因为它们的loader对HMR的支持机制是不同的：
      >
      > 1. **style-loader的内置HMR支持**。
      > 2. `style-loader`是专门为开发环境设计的CSS加载器，它内部已经实现了HMR接口，会自动处理CSS模块的热更新。当CSS文件发生变化时，`style-loader`会直接替换DOM中的样式标签而不刷新整个页面。
      > 3. **JS的默认行为不同**。默认情况下，JS没有内置的HMR支持。
      > 4. Webpack虽然可以检测到JS文件变化并重新编译，但不知道如何安全地替换运行中的模块。直接替换JS模块可能导致状态丢失或应用崩溃。**因此我们需要显式地添加HMR代码**。
      
      ###### JS配置HMR：
      
      ```js
      // main.js
      import count from "./js/count";
      import sum from "./js/sum";
      // 引入资源，Webpack才会对其打包
      import "./css/iconfont.css";
      import "./css/index.css";
      import "./less/index.less";
      import "./sass/index.sass";
      import "./sass/index.scss";
      import "./styl/index.styl";
      
      const result1 = count(2, 1);
      console.log(result1);
      const result2 = sum(1, 2, 3, 4);
      console.log(result2);
      
      // 判断浏览器是否支持HMR功能
      if (module.hot) {
        // 如果支持，当该文件发生变化后，重新接受这个count.js文件。accept的第2个参数是可选的，当内容变化后会被调用
        module.hot.accept("./js/count.js", function (count) {
          const result1 = count(2, 1);
          console.log(result1);
        });
      
        module.hot.accept("./js/sum.js", function (sum) {
          const result2 = sum(1, 2, 3, 4);
          console.log(result2);
        });
      }
      ```
      
      > 这样写太麻烦了，所以实际开发我们会使用一些实现了HMR功能的 loader 来解决JS的HMR。比如：`vue-loader`、`react-hot-loader`。
      
    - ##### OneOf
    
      > 我们现在执行打包的时候，每个文件都会经过所有 loader 处理，虽然 `test` 正则实际没有匹配上，但是还是都要过一遍，比较慢。
      >
      > `OneOf` 顾名思义就是，只要匹配上一个 loader，剩下的就不再进行匹配了。
    
      ###### 用法：（只需要在原来的loader外面再包一层`oneOf`即可）
    
      ```js
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: undefined, // 开发模式没有输出，不需要指定输出目录
          filename: "static/js/main.js", // 将 js 文件输出到 static/js 目录中
          // clean: true, // 开发模式没有输出，不需要清空输出结果
        },
        module: {
          rules: [
            {
              // 每个文件只能被其中一个loader处理
              oneOf: [
                {
                  // 用来匹配 .css 结尾的文件
                  test: /\.css$/,
                  // use 数组里面 Loader 执行顺序是从右到左
                  use: ["style-loader", "css-loader"],
                },
                {
                  test: /\.less$/,
                  use: ["style-loader", "css-loader", "less-loader"],
                },
                {
                  test: /\.s[ac]ss$/,
                  use: ["style-loader", "css-loader", "sass-loader"],
                },
                {
                  test: /\.styl$/,
                  use: ["style-loader", "css-loader", "stylus-loader"],
                },
                {
                  test: /\.(png|jpe?g|gif|webp)$/,
                  type: "asset",
                  parser: {
                    dataUrlCondition: {
                      maxSize: 10 * 1024, // 小于10kb的图片会被base64处理
                    },
                  },
                  generator: {
                    // 将图片文件输出到 static/imgs 目录中
                    // 将图片文件命名 [hash:8][ext][query]
                    // [hash:8]: hash值取8位
                    // [ext]: 使用之前的文件扩展名
                    // [query]: 添加之前的query参数
                    filename: "static/imgs/[hash:8][ext][query]",
                  },
                },
                {
                  test: /\.(ttf|woff2?)$/,
                  type: "asset/resource",
                  generator: {
                    filename: "static/media/[hash:8][ext][query]",
                  },
                },
                {
                  test: /\.js$/,
                  exclude: /node_modules/, // 排除node_modules代码不编译
                  loader: "babel-loader",
                },
              ],
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            // 指定检查文件的根目录
            context: path.resolve(__dirname, "../src"),
          }),
          new HtmlWebpackPlugin({
            // 以 public/index.html 为模板创建文件
            // 新的html文件有两个特点：1. 内容和源文件一致 2. 自动引入打包生成的js等资源
            template: path.resolve(__dirname, "../public/index.html"),
          }),
        ],
        // 开发服务器
        devServer: {
          host: "localhost", // 启动服务器域名
          port: "3000", // 启动服务器端口号
          open: true, // 是否自动打开浏览器
          hot: true, // 开启HMR功能
        },
        mode: "development",
        devtool: "cheap-module-source-map",
      };
      ```
    
      > 生产模式也是如此配置。
    
    - ##### Include/Exclude
    
      > 开发时我们需要使用第三方的库或插件，所有文件都下载到 `node_modules` 目录中了。而这些文件是不需要编译可以直接使用的。所以我们在对 js 文件处理时，要排除 `node_modules` 目录中的js文件。
      >
      > 在`loader`或`plugin`的配置对象中，可以通过`include`和`exclude`配置项来指定，`loader`在处理时要包含和要排除的文件：
      >
      > - `include`：包含，只处理 xxx 文件。
      > - `exclude`：排除，除了 xxx 文件以外其他文件都处理。
    
      ###### 用法：
    
      ```js
      module: {
        rules: [
          {
            oneOf: [
              // ...
              {
                test: /\.js$/,
                // exclude: /node_modules/, // 排除node_modules代码不经过babel编译
                include: path.resolve(__dirname, "../src"), // 也可以用包含
                loader: "babel-loader",
              },
              // ...
            ],
          },
        ],
      },
      plugins: [
        // ...
        new ESLintWebpackPlugin({
          context: path.resolve(__dirname, "../src"),
          exclude: "node_modules", // exclude在这里的默认值就是它
        }),
        // ...
      ],
      // ...
      ```
      
      > 生产模式也是如此配置。
      
    - ##### Cache（只针对开发模式）
    
      > 每次打包时 js 文件都要经过 Eslint 检查 和 Babel 编译，速度比较慢。
      >
      > 我们可以缓存之前的 Eslint 检查 和 Babel 编译结果，这样第二次打包时速度就会更快了（rebuild时的速度更快）。
    
      ###### 对 Eslint 检查 和 Babel 编译结果进行缓存：
    
      ```js
      module: {
        rules: [
          {
            oneOf: [
              // ...
              {
                test: /\.js$/,
                include: path.resolve(__dirname, "../src"),
                loader: "babel-loader",
                options: {
                  cacheDirectory: true, // 开启babel编译缓存
                  cacheCompression: false, // 缓存文件不要做压缩
                },
              },
              // ...
            ],
          },
        ],
      },
      plugins: [
        // ...
        new ESLintWebpackPlugin({
          context: path.resolve(__dirname, "../src"),
          exclude: "node_modules",
          cache: true, // 开启eslint结果缓存
          // 缓存目录
          cacheLocation: path.resolve(
            __dirname,
            "../node_modules/.cache/eslintcache"
          ),
        }),
        // ...
      ],
      // ...
      ```
      
      > 这个配置虽然只针对开发模式，但是我们为了方便验证，可以先将其配置到生产模式中。执行`npm run build`后，可以在`node_modules/.cache`中看到我们缓存的文件了。
      
    - ##### Thread
    
      > 当项目越来越庞大时，打包速度越来越慢，甚至于需要一个下午才能打包出来代码。这个速度是比较慢的。
      >
      > 我们想要继续提升打包速度，其实就是要提升 js 的打包速度，因为其他文件都比较少。而对 js 文件处理主要就是 eslint 、babel、Terser（给JS做优化、混淆、压缩的）这三个工具，所以我们要提升它们的运行速度。
      >
      > 我们可以开启多个进程同时处理 js 文件，这样速度就比之前的单进程打包更快了。
      >
      > **注意**：多进程请仅在特别耗时的操作中使用，因为每个 worker 都是一个独立的 node.js 进程，每个进程启动就有大约为 600ms 左右开销。
    
      ###### 使用多进程来提高工具对 JS 的处理速度：
    
      1. 下载包：`npm i thread-loader -D`
      
      2. 配置：（使用时，需将 thread-loader 放置在其他 loader 之前。放置在此 loader 后面的 loader 会在一个独立的 worker 池中运行）
      
         ```js
         // ...
         const os = require("os");
         const TerserPlugin = require("terser-webpack-plugin");  // 已经内置了，不需要下载了
         const threads = os.cpus().length;  // cpu核数
         
         // ...
         
         module: {
           rules: [
             {
               oneOf: [
                 // ...
                 {
                   test: /\.js$/,
                   include: path.resolve(__dirname, "../src"),
                   use: [
                     {
                       loader: "thread-loader", // 开启多进程
                       options: {
                         workers: threads, // 进程数量
                       },
                     },
                     {
                       loader: "babel-loader",
                       options: {
                         cacheDirectory: true,
                       },
                     },
                   ],
                 },
                 // ...
               ],
             },
           ],
         },
         plugins: [
           // ...
           new ESLintWebpackPlugin({
             context: path.resolve(__dirname, "../src"),
             exclude: "node_modules",
             cache: true,
             cacheLocation: path.resolve(
               __dirname,
               "../node_modules/.cache/.eslintcache"
             ),
             threads, // 开启多进程
           }),
         	new CssMinimizerPlugin(),
           // new TerserPlugin({ parallel: threads })  // 也可以放在这里
           // ...
         ],
         optimization: {
           // 告知webpack使用TerserPlugin或其它在optimization.minimizer定义的插件，去压缩bundle，默认为true
           minimize: true,
           minimizer: [
             // 生产模式会默认开启TerserPlugin，但是如果需要进行其他配置，就要显式写出来
             new TerserPlugin({
               parallel: threads // 开启多进程
             })
           ],
         },
         
         // ...
         ```
         
         > 从 webpack 4 开始，会根据你选择的 `mode` 来执行不同的优化，不过所有的优化还是可以手动配置和重写。通过`optimization`优化配置项。
         >
         > 开发和生产模式都是这样配置。
         >
         > **注意**：由于我们目前打包的内容都很少，所以因为启动进程开销原因，使用多进程打包实际上会显著的让我们打包时间变得很长。因此后面搭建VueCli、ReactCli我们不加多进程打包。因为通常我们的项目代码量并没有那么庞大。
         
    
  - #### 减少代码体积
  
    - ##### Tree Shaking
  
      > 开发时我们定义了一些工具函数库，或者引用第三方工具函数库或组件库。如果没有特殊处理的话我们打包时会引入整个库，但是实际上可能我们可能只用上极小部分的功能。这样将整个库都打包进来，体积就太大了。
      >
      > **Tree shaking** 是一个术语，通常用于描述移除 JS 上下文中的死代码（永远不会执行）。
      >
      > **注意：Tree Shaking依赖 `ES Module`**。
      >
      > Webpack 默认已经开启了Tree Shaking，无需其他配置。
  
    - ##### Babel
  
      > **Polyfill（填充物）** 是一块代码，用来为旧浏览器提供它没有原生支持的较新的功能。它们填补了浏览器对新功能的支持不足或不完整的差距，使开发人员能够在各种浏览器上使用最新的Web功能（比如`Promise`）。
      >
      > Babel 默认会在编译前，给每个JS源文件中都插入一些辅助代码（Polyfill）。最终打包工具（如 webpack、Rollup）会将这些分散在各文件中的重复辅助代码一并打包到最终的 `bundle.js` 中，导致打包体积增大。
  
      ###### 解决：
  
      > 我们可以将这些辅助代码作为一个独立模块，来避免重复引入。
      >
      > 通过Babel提供的插件`@babel/plugin-transform-runtime`：它禁用了 Babel 对每个文件自动注入的辅助代码，而是引入该插件里面定义的辅助代码（模块）。这样体积就会小很多。
  
      ###### 使用：
  
      1. 下载包：`npm i @babel/plugin-transform-runtime -D`
  
      2. 配置：
  
         ```js
         {
           test: /\.js$/,
           include: path.resolve(__dirname, "../src"),
           use: [
             {
               loader: "thread-loader",
               options: {
                 workers: threads,
               },
             },
             {
               loader: "babel-loader",
               options: {
                 cacheDirectory: true,
                 cacheCompression: false,
                 plugins: ["@babel/plugin-transform-runtime"], // 这里添加上这个插件，减少代码体积
               },
             },
           ],
         },
         ```
  
    - ##### Image Minimizer（只针对生产模式）
  
      > 开发如果项目中引用了较多图片，那么图片体积会比较大，将来请求速度比较慢。我们可以用`image-minimizer-webpack-plugin`插件对图片进行压缩，减少图片体积。
      >
      > **注意**：我们要处理的是本地加载引入的图片。如果项目中图片都是在线链接（比如放在阿里云Bucket中的资源），那么就不需要了。本地项目静态图片才需要进行压缩。
  
      ###### 使用：
  
      1. 下载包：`npm i image-minimizer-webpack-plugin imagemin -D`
  
         > 还要下载**用于压缩具体某个格式图片**的包，根据压缩行为分为2种：
         >
         > - 无损压缩：`npm install imagemin-gifsicle imagemin-jpegtran imagemin-optipng imagemin-svgo -D`
         > - 有损压缩：`npm install imagemin-gifsicle imagemin-mozjpeg imagemin-pngquant imagemin-svgo -D`
         >
         > 简单来说：无损压缩没有损坏图片内容、质量更好，有损压缩体积可以更小，但是图片质量会受影响。[有损/无损压缩的区别](https://baike.baidu.com/item/无损、有损压缩)
  
      2. 配置：（以无损压缩为例）
  
         ```js
         const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
         // ...
         
         optimization: {
           minimizer: [
             new CssMinimizerPlugin(),
             new TerserPlugin({
               parallel: threads,
             }),
             // 压缩图片（也可以放到plugins中）
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
         },
         ```
  
      3. 打包时可能会出现报错：（包没下载完整）
  
         ```tex
         Error: Error with 'src\images\1.jpeg': '"C:\Users\86176\Desktop\webpack\webpack_code\node_modules\jpegtran-bin\vendor\jpegtran.exe"'
         Error with 'src\images\3.gif': spawn C:\Users\86176\Desktop\webpack\webpack_code\node_modules\optipng-bin\vendor\optipng.exe ENOENT
         ```
  
         > 提示找不到2个exe文件。我们可以手动安装这两个文件到`node_modules`：
         >
         > - `jpegtran.exe`：需要复制到 `node_modules\jpegtran-bin\vendor` 下面。[jpegtran 官网](http://jpegclub.org/jpegtran/)
         > - `optipng.exe`：需要复制到 `node_modules\optipng-bin\vendor` 下面。[OptiPNG 官网](http://optipng.sourceforge.net/)
  
  - #### 优化代码运行性能
  
    - ##### Code Split
  
      > 打包代码时会将所有 js 文件打包到一个文件中，体积太大了。我们如果只要渲染首页，就应该只加载首页的 js 文件，其他文件不应该加载。
      >
      > 所以我们需要将打包生成的文件进行代码分割，生成多个 js 文件，渲染哪个页面就只加载某个 js 文件，这样加载的资源就少，速度就更快。
      >
      > 代码分割（Code Split）主要做了两件事：
      >
      > 1. 分割文件：将打包生成的文件进行分割，生成多个 js 文件。
      > 2. 按需加载：需要哪个文件就加载哪个文件。
      >
      > 代码分割可以有不同的方式实现，为了更加方便体现它们之间的差异，我们会分别创建新的文件来演示。
    
      - ###### 多入口：（`webpack.config.js`）
    
        ```js
        // 单入口
        // entry: './src/main.js',
        // 多入口
        entry: {
          // 打包后会生成2个js文件
          main: "./src/main.js",
          app: "./src/app.js",
        },
        output: {
          path: path.resolve(__dirname, "./dist"),
          filename: "js/[name].js",
          clear: true,
        },
        ```
    
        > `[name]`是webpack的命名规则，使用chunk的name作为输出的文件名。
        > 什么是chunk？打包的资源就是chunk，输出出去叫bundle。
        > chunk的name是啥呢？ 比如： `entry`配置项中`xxx: "./src/xxx.js"`, name就是xxx。注意是前面的key，和文件名无关。
        >
        > 为什么需要这样命名呢？如果还是之前写法main.js，那么打包生成两个js文件都会叫做main.js会直接报错。
        >
        > **Webpack中，配置了几个入口文件，至少输出几个 js 文件（bundle）**。
    
      - ###### 提取重复代码：
    
        > 默认情况下，如果多入口文件中都引用了同一份代码，则这份代码会被打包到两个文件中，导致代码重复，体积更大。我们期望在打包时，将这份重复代码生成一个单独的 js 文件，其他的文件里引用它即可。
        >
        
        ```js
        entry: {
          main: "./src/main.js",
          app: "./src/app.js",
        },
        output: {
          path: path.resolve(__dirname, "./dist"),
          filename: "js/[name].js",
          clear: true,
        },
        optimization: {
          // 代码分割配置
          splitChunks: {
            chunks: "all", // 对所有模块都进行分割
            // 以下是默认值
            // minSize: 20000, // 分割代码最小的大小
            // minRemainingSize: 0, // 类似于minSize，最后确保提取的文件大小不能为0
            // minChunks: 1, // 至少被引用的次数，满足条件才会代码分割
            // maxAsyncRequests: 30, // 按需加载时并行加载的文件的最大数量
            // maxInitialRequests: 30, // 入口js文件最大并行请求数量
            // enforceSizeThreshold: 50000, // 超过50kb一定会单独打包（此时会忽略minRemainingSize、maxAsyncRequests、maxInitialRequests）
            // cacheGroups: { // 组，哪些模块要打包到一个组
            //   defaultVendors: { // 组名
            //     test: /[\\/]node_modules[\\/]/, // 需要打包到一起的模块
            //     priority: -10, // 权重（越大越高）
            //     reuseExistingChunk: true, // 如果当前 chunk 包含已从主 bundle 中拆分出的模块，则它将被重用，而不是生成新的模块
            //   },
            //   default: { // 其他没有写的配置会使用上面的默认值
            //     minChunks: 2, // 这里的minChunks权重更大，会覆盖外层的公共配置
            //     priority: -20,
            //     reuseExistingChunk: true,
            //   },
            // },
            // 修改配置
            cacheGroups: {
              // 组，哪些模块要打包到一个组
              // defaultVendors: { // 组名
              //   test: /[\\/]node_modules[\\/]/, // 需要打包到一起的模块
              //   priority: -10, // 权重（越大越高）
              //   reuseExistingChunk: true, // 如果当前 chunk 包含已从主 bundle 中拆分出的模块，则它将被重用，而不是生成新的模块
              // },
              default: {
                // 其他没有写的配置会使用上面的默认值
                minSize: 0, // 这里我们为了看到效果，故意将文件体积设置为0，这样所有公共代码都会生成单独的bundle。实际开发中不会这么配置
                minChunks: 2,
                priority: -20,
                reuseExistingChunk: true,
              },
            },
          },
        }
        ```
        
        > 这里我们为了看到效果，故意将`minSize`文件体积设置为0，这样所有公共代码都会生成单独的bundle。实际开发中不会这么配置。
        >
        > 此时配置后，我们会发现生成了 3 个 js 文件，其中有一个就是提取的公共模块。
        
      - ###### 按需加载，动态导入：
      
        > 在Webpack 5的默认配置下，**使用`import()`语法动态导入的JS模块通常会被分离为单独的chunk，并在运行时按需加载**。这种行为可以通过`splitChunks`等配置进行定制。
        
        ###### 给动态导入文件取名称：
        
        1. 被打包的js文件：
        
           ```js
           // 也叫【webpack魔法命名】
           import(/* webpackChunkName: "math" */ "./js/math.js").then(({ count }) => {
             console.log(count(2, 1));
           });
           ```
        
           > `webpackChunkName: "math"`：这是Webpack动态导入模块的命名方式。`"math"`会作为`[name]`的值。
        
        2. `webpack.config.js`中用上这个名字：
        
           ```js
           output: {
             path: path.resolve(__dirname, './dist'),
             filename: 'js/main.js',
             chunkFilename: 'js/[name].js',  // 设置chunk文件名（即：除了入口文件生成的bundle之外，其他chunk文件生成bundle的文件名）
             clear: true,
           },
           ```
        
      - ###### 单入口：（重点）
      
        > 开发时我们可能是单页面应用（SPA），只有一个入口（单入口）。单入口也可以通过代码分割来生成多个js文件，这样配置：
      
        ```js
        const path = require("path");
        const HtmlWebpackPlugin = require("html-webpack-plugin");
        
        module.exports = {
          // 单入口
          entry: "./src/main.js",
          output: {
            path: path.resolve(__dirname, "./dist"),
            filename: "js/main.js",
            clean: true,
          },
          plugins: [
            new HtmlWebpackPlugin({
              template: "./public/index.html",
            }),
          ],
          mode: "production",
          optimization: {
            splitChunks: {
              chunks: "all",  // 只配置这一个即可
              // 其他的都不用配置，用默认值即可
              // minSize: 20000, // 分割代码最小的大小
              // minRemainingSize: 0, // 类似于minSize，最后确保提取的文件大小不能为0
              // minChunks: 1, // 至少被引用的次数，满足条件才会代码分割
              // maxAsyncRequests: 30, // 按需加载时并行加载的文件的最大数量
              // maxInitialRequests: 30, // 入口js文件最大并行请求数量
              // enforceSizeThreshold: 50000, // 超过50kb一定会单独打包（此时会忽略minRemainingSize、maxAsyncRequests、maxInitialRequests）
              // cacheGroups: { // 组，哪些模块要打包到一个组
              //   defaultVendors: { // 这里用默认的配置即可，node_modules中的js会打包到一个叫vendors.xxx.chunk.js的chunk中
              //     test: /[\\/]node_modules[\\/]/, // 需要打包到一起的模块
              //     priority: -10, // 权重（越大越高）
              //     reuseExistingChunk: true, // 如果当前 chunk 包含已从主 bundle 中拆分出的模块，则它将被重用，而不是生成新的模块
              //   },
              //   default: {
              //     minChunks: 2, // 由于我们是单入口，因此这里的默认配置没有任何作用
              //     priority: -20,
              //     reuseExistingChunk: true,
              //   },
              // },
          },
        };
        ```
      
      > 最终我们选择使用单入口+代码分割+动态导入方式来进行配置。更新之前的配置文件：（开发模式类似）
      
      ```js
      // webpack.prod.js
      const os = require("os");
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const TerserPlugin = require("terser-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      
      // cpu核数
      const threads = os.cpus().length;
      
      // 获取处理样式的Loaders
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
          path: path.resolve(__dirname, "../dist"), // 生产模式需要输出
          filename: "static/js/main.js", // 将 js 文件输出到 static/js 目录中
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
                  generator: {
                    // 将图片文件输出到 static/imgs 目录中
                    // 将图片文件命名 [hash:8][ext][query]
                    // [hash:8]: hash值取8位
                    // [ext]: 使用之前的文件扩展名
                    // [query]: 添加之前的query参数
                    filename: "static/imgs/[hash:8][ext][query]",
                  },
                },
                {
                  test: /\.(ttf|woff2?)$/,
                  type: "asset/resource",
                  generator: {
                    filename: "static/media/[hash:8][ext][query]",
                  },
                },
                {
                  test: /\.js$/,
                  // exclude: /node_modules/, // 排除node_modules代码不编译
                  include: path.resolve(__dirname, "../src"), // 也可以用包含
                  use: [
                    {
                      loader: "thread-loader", // 开启多进程
                      options: {
                        workers: threads, // 数量
                      },
                    },
                    {
                      loader: "babel-loader",
                      options: {
                        cacheDirectory: true, // 开启babel编译缓存
                        cacheCompression: false, // 缓存文件不要压缩
                        plugins: ["@babel/plugin-transform-runtime"], // 减少代码体积
                      },
                    },
                  ],
                },
              ],
            },
          ],
        },
        plugins: [
          new ESLintWebpackPlugin({
            // 指定检查文件的根目录
            context: path.resolve(__dirname, "../src"),
            exclude: "node_modules", // 默认值
            cache: true, // 开启缓存
            // 缓存目录
            cacheLocation: path.resolve(
              __dirname,
              "../node_modules/.cache/.eslintcache"
            ),
            threads, // 开启多进程
          }),
          new HtmlWebpackPlugin({
            // 以 public/index.html 为模板创建文件
            // 新的html文件有两个特点：1. 内容和源文件一致 2. 自动引入打包生成的js等资源
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          // 提取css成单独文件
          new MiniCssExtractPlugin({
            // 定义输出文件名和目录
            filename: "static/css/main.css",
          }),
          // css压缩
          // new CssMinimizerPlugin(),
        ],
        optimization: {
          minimizer: [
            // css压缩也可以写到optimization.minimizer里面，效果一样的
            new CssMinimizerPlugin(),
            // 当生产模式会默认开启TerserPlugin，但是我们需要进行其他配置，就要重新写了
            new TerserPlugin({
              parallel: threads, // 开启多进程
            }),
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
          // 代码分割配置
          splitChunks: {
            chunks: "all", // 对所有模块都进行分割
            // 其他内容用默认配置即可
          },
        },
        // devServer: {
        //   host: "localhost", // 启动服务器域名
        //   port: "3000", // 启动服务器端口号
        //   open: true, // 是否自动打开浏览器
        //   static: {
        //     directory: path.join(__dirname, 'public'),  // 这样index.html中就能访问到public下的静态资源了
        //   },
        //   compress: true,
        // },
        mode: "production",
        devtool: "source-map",
      };
      ```
      
      > 目前我们在`output.filename、output.chunkFileName`，处理图片和其他二进制文件的loader中有`generator.filename`，还有压缩css的插件中的`filename`...
      >
      > 我们来进行统一命名，这样更规范清晰一点。统一命名设置：（开发模式类似）
      
      ```js
      // webpack.prod.js
      const os = require("os");
      const path = require("path");
      const ESLintWebpackPlugin = require("eslint-webpack-plugin");
      const HtmlWebpackPlugin = require("html-webpack-plugin");
      const MiniCssExtractPlugin = require("mini-css-extract-plugin");
      const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
      const TerserPlugin = require("terser-webpack-plugin");
      const ImageMinimizerPlugin = require("image-minimizer-webpack-plugin");
      
      const threads = os.cpus().length;
      
      const getStyleLoaders = (preProcessor) => {
        return [
          MiniCssExtractPlugin.loader,
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
          path: path.resolve(__dirname, "../dist"),
          // 这样写无论是单入口还是多入口都兼容
          filename: "static/js/[name].js",
          // 为了区分主文件和其他chunk文件，通常我们会这样命名chunk
          chunkFilename: "static/js/[name].chunk.js",
          // 图片、字体等二进制的、用type:asset处理的资源，统一在这里进行命名
          assetModuleFilename: "static/media/[name].[hash][ext]",
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
                  // generator: {
                  //   filename: "static/imgs/[hash:8][ext][query]",
                  // },
                },
                {
                  test: /\.(ttf|woff2?)$/,
                  type: "asset/resource",
                  // generator: {
                  //   filename: "static/media/[hash:8][ext][query]",
                  // },
                },
                {
                  test: /\.js$/,
                  include: path.resolve(__dirname, "../src"),
                  use: [
                    {
                      loader: "thread-loader",
                      options: {
                        workers: threads,
                      },
                    },
                    {
                      loader: "babel-loader",
                      options: {
                        cacheDirectory: true,
                        cacheCompression: false,
                        plugins: ["@babel/plugin-transform-runtime"],
                      },
                    },
                  ],
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
            threads,
          }),
          new HtmlWebpackPlugin({
            template: path.resolve(__dirname, "../public/index.html"),
          }),
          new MiniCssExtractPlugin({
      		  // 多入口打包会生成多个css文件，因此这里也要动态设置name
            filename: "static/css/[name].css",
            // 如果import()导入的js中又import导入了css，那么css也会生成chunk，因此这里也要设置chunk.css
            chunkFilename: "static/css/[name].chunk.css",
          }),
          new CssMinimizerPlugin(),
        ],
        optimization: {
          minimizer: [
            new CssMinimizerPlugin(),
            new TerserPlugin({
              parallel: threads,
            }),
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
        },
        mode: "production",
        devtool: "source-map",
      };
      ```
      
    - ##### Preload / Prefetch
    
      > 我们前面已经做了代码分割，同时会使用 `import()` 动态导入语法来进行代码按需加载（我们也叫懒加载，比如路由懒加载就是这样实现的）。
      >
      > 但是这样的话，加载速度有点慢了，比如：是用户点击按钮时才加载这个资源的，如果资源体积很大，那么用户会感觉到明显卡顿效果。
      >
      > 我们想在浏览器空闲时间，加载后续需要使用的资源。我们就需要用上 `Preload` 或 `Prefetch` 技术：
      >
      > ```html
      > <link rel="prefetch">
      > <link rel="preload">
      > ```
      >
      > `Preload` 或 `Prefetch` 都是让浏览器去加载那些**按需加载的资源**，它们的不同在于：
      >
      > - `Preload`：告诉浏览器立即加载资源。
      > - `Prefetch`：告诉浏览器在空闲时再加载资源。
      >
      > 它们共同点是：
      >
      > - 都只会加载资源，但并不执行。
      > - 都有缓存。
      >
      > 它们区别是：
      >
      > - `Preload`加载优先级高，`Prefetch`加载优先级低。
      > - `Preload`只能加载当前页面需要使用的资源，`Prefetch`可以加载当前页面资源，也可以加载下一个页面需要使用的资源。
      >
      > 总结：
      >
      > - 当前页面优先级高的资源用 `Preload` 加载。
      > - 下一个页面需要使用的资源用 `Prefetch` 加载。
      >
      > 它们的问题是：兼容性较差。
      >
      > - 我们可以去 [Can I Use](https://caniuse.com/) 网站查询 API 的兼容性问题。
      > - `Preload` 相对于 `Prefetch` 兼容性好一点。
    
      ###### Webpack中，通过`preload-webpack-plugin`插件来生成`preload`或`prefetch`的`<link>`标签：
    
      1. 下载包：`npm i @vue/preload-webpack-plugin -D`
    
      2. 配置：
    
         ```js
         const PreloadWebpackPlugin = require("@vue/preload-webpack-plugin");
         // ...
         
         module.exports = {
           // ...
           plugins: [
             // 里面指定的属性都会作为link标签的属性
             new PreloadWebpackPlugin({
               rel: "preload", // preload的兼容性更好
               as: "script",
             }),
         
         // prefetch的用法，它的兼容性较差
             // new PreloadWebpackPlugin({
             //   rel: 'prefetch'
             // }),
           ]
         }
         ```
    
    - ##### Network Cache（文件指纹）
    
      > 将来部署到生产环境时，我们会对静态资源使用缓存来进行优化，这样浏览器第二次请求资源就能读取缓存了，速度很快。
      >
      > 但是这样的话就会有一个问题，因为前后输出的文件名是一样的，都叫 main.js，一旦将来在服务器上更新了静态资源，由于文件名没有变化，浏览器还会走缓存不会加载新资源，客户但访问到的还是旧的资源。
      >
      > 为了解决这个问题，我们从文件名入手，确保更新前后的文件名不一样。
      >
      > 我们通过以下webpack内置的占位符（类似于`[name]`）作为文件名：
      >
      > - `[fullhash]`（webpack4 是 `[hash]`）
      >
      >   每次修改项目中任何一个文件，输出的所有文件名的 hash 值都将改变。所以一旦修改了任何一个文件，整个项目的文件缓存都将失效。
      >
      > - `[chunkhash]`
      >
      >   根据不同的入口文件(Entry)进行依赖文件解析、构建对应的 chunk，计算生成对应的哈希值。不同chunk之间的hash值不同。
      >
      > - `[contenthash]`
      >
      >   根据文件内容生成 hash 值，只有文件内容变化了，hash 值才会重新计算。所有文件的 hash 值是独享且不同的。
    
      ###### 使用：
    
      ```js
      module.exports = {
        entry: "./src/main.js",
        output: {
          path: path.resolve(__dirname, "../dist"),
          // [contenthash:8]使用contenthash，取8位长度
          filename: "static/js/[name].[contenthash:8].js", // 入口文件打包输出资源命名方式
          chunkFilename: "static/js/[name].[contenthash:8].chunk.js", // 动态导入输出资源命名方式
          assetModuleFilename: "static/media/[name].[fullhash][ext]", // 图片、字体等资源命名方式（注意用fullhash）
          clean: true,
        },
      
        plugins: [
          // 提取css成单独文件
          new MiniCssExtractPlugin({
            // 定义输出文件名和目录
            filename: "static/css/[name].[contenthash:8].css",
            chunkFilename: "static/css/[name].[contenthash:8].chunk.css",
          }),
        ],
      
        // ...
      }
      ```
    
      > **当前存在的问题：**
      >
      > 当我们修改 math.js 文件再重新打包的时候，因为 contenthash 原因，math.js 文件 hash 值发生了变化（这是正常的）。
      >
      > 但是 main.js 文件的 hash 值也发生了变化，这会导致 main.js 的缓存失效。明明我们只修改 math.js, 为什么 main.js 也会变身变化呢？
      >
      > **原因：**
      >
      > - 更新前：math.xxx.js, main.js 引用的 math.xxx.js
      > - 更新后：math.yyy.js, main.js 引用的 math.yyy.js, 文件名发生了变化，间接导致 main.js 也发生了变化
      >
      > **解决：**
      >
      > 将 hash 值单独保管在一个 runtime 文件中。
      >
      > 我们最终输出三个文件：main、math、runtime。当 math 文件发送变化，变化的是 math 和 runtime 文件，main 不变。
      >
      > runtime 文件只保存文件的 hash 值和它们与文件关系，整个文件体积就比较小，所以变化重新请求的代价也小。
      >
      > **核心原理：**
      >
      > - **没有 runtime 文件时的情况**：
      >   - main.js 直接包含对 math.js 的引用（包括带 hash 的文件名）
      >   - math.js 文件名变化 → main.js 内容必须变化 → hash 改变
      > - **有 runtime 文件时的机制**：
      >   - main.js **不再直接包含依赖的文件路径**
      >   - Webpack 会生成一个**模块映射表**(manifest)存放在 runtime.js
      >   - main.js 通过模块 ID(通常是数字或哈希)引用其他模块
      >   - runtime.js 负责将模块ID解析为实际带hash的文件名
      >
      > 相当于main.js只记录`__webpack_require__(1)`这样的抽象引用，runtime文件维护`{1: "math.abc123.js"}`这样的映射关系。
    
      > **因此我们再加一个优化配置：**
      >
      > ```js
      > optimization: {
      >   // 提取runtime文件
      >   runtimeChunk: {
      >     name: (entrypoint) => `runtime~${entrypoint.name}`, // runtime文件命名规则
      >   },
      > }
      > ```
    
    - ##### Core-js
    
      > 过去我们使用 babel 对 js 代码进行了兼容性处理，其中使用 `@babel/preset-env` 智能预设来处理兼容性问题。它能将 ES6 的一些语法进行编译转换，比如箭头函数、点点点运算符等。但是如果是 `async` 函数、`Promise` 对象、数组的一些方法（`includes`等），babel就没办法处理了。
      >
      > 所以此时我们 js 代码仍然存在兼容性问题，一旦遇到低版本浏览器会直接报错。我们要将 js 兼容性问题彻底解决。
    
      > **`core-js` 是专门用来做 ES6 以及以上 API 的 `polyfill`。`polyfill`翻译过来叫做垫片/补丁。就是用社区上提供的一段代码，让我们在不兼容某些新特性的浏览器上，使用该新特性。**
    
      ###### 使用：
    
      1. 下载包：`npm i core-js@3`
    
      2. 引入：（任选其一）
    
         - 全部引入：入口文件`main.js`中，加上：`import 'core-js';`
    
           > 这样引入会将所有兼容性代码全部引入，体积太大了。我们只想引入部分新语法的 `polyfill`。
    
         - 按需引入（比如只引入`Promise`的`polyfill`）：`import 'core-js/es/promise';`
    
           > 只引入打包 promise 的 `polyfill`，打包体积更小。但是将来如果还想使用其他语法，我需要手动引入库很麻烦。
    
         - （**重点**）自动按需引入。`babel.config.js`：
    
           ```js
           module.exports = {
             // 智能预设：能够编译ES6语法
             presets: [
               [
                 "@babel/preset-env",
                 // 按需加载core-js的polyfill
                 { useBuiltIns: "usage", corejs: { version: "3", proposals: true } },
               ],
             ],
           };
           ```
    
           > 数组的第二个元素是对第一个参数`@babel/preset-env`的配置项，此时Babel就会自动根据我们代码中使用的语法，来按需加载`core-js`中相应的 `polyfill` 了。
    
    - ##### PWA
    
      > 我们开发的 Web 项目一旦处于网络离线状态，此时再刷新网页，就啥也看不到了。无论你缓存做的有多好。我们希望给项目提供离线体验，像手机安装的App一样。
      >
      > **渐进式网络应用程序(progressive web application - PWA)**：是一种可以提供类似于 native app(原生应用程序) 体验的 Web 技术。其中最重要的是，在 **离线(offline)** 时应用程序能够继续运行功能。它内部是通过 `Service Workers` 实现的。
      >
      > 同样的，Service Workers也有严重的兼容性问题，需要在实现了该特性的浏览器中才能使用。
      
      ###### 使用：
      
      1. 下载包：`npm i workbox-webpack-plugin -D`
      
      2. Webpack中配置：
      
         ```js
         const WorkboxPlugin = require("workbox-webpack-plugin");
         // ...
         
         module.exports = {
           // ...
           plugins: [
             new WorkboxPlugin.GenerateSW({
               // 这些选项帮助快速启用 ServiceWorkers
               // 不允许遗留任何“旧的” ServiceWorkers
               clientsClaim: true,
               skipWaiting: true,
             }),
           ],
         };
         ```
      
      3. `main.js` 中添加PWA代码：（注册生成`Service Workers`）
      
         ```js
         // ...
         if ("serviceWorker" in navigator) {
           window.addEventListener("load", () => {
             navigator.serviceWorker
               .register("/service-worker.js")
               .then((registration) => {
                 console.log("ServiceWorkers registered: ", registration);
               })
               .catch((registrationError) => {
                 console.log("ServiceWorkers registration failed: ", registrationError);
               });
           });
         }
         ```
      
      4. 运行：`npm run build`
      
         > 打包后，`dist`目录下会生成`service-worker.js`文件。浏览器通过该文件来为我们的网页实现PWA。
         
         > **注意：**
         >
         > 此时如果直接通过 VSCode 的Live Server插件访问打包后的index.html，在浏览器控制台会发现 `SW registration failed`，SW注册失败了。
         >
         > 因为我们打开的访问路径是：`http://127.0.0.1:5500/dist/index.html`，Live Server默认是以VS Code的工程目录为基准启动一个开发服务器的，此时页面会去请求 `service-worker.js` 文件，请求路径是：`http://127.0.0.1:5500/service-worker.js`，实际 `service-worker.js` 文件路径是：`http://127.0.0.1:5500/dist/service-worker.js`。这样找不到会 404。
         >
         > ###### 解决路径问题：
         >
         > 1. 下载包：`npm i serve -g`。serve 也是用来启动开发服务器来部署代码查看效果的。
         > 2. 运行指令：`serve dist`。此时就会启动一个开发服务器，部署的是dist目录下的资源。打开index.html后我们的 service-worker 就能注册成功了。将网页调成`offline`之后，刷新后还是可以看到网页资源。因为它将这些资源缓存到了Service Workers中了。
         >
         > 如果感兴趣的话，打开浏览器F12的Application，在Service Workers中可以看到我们注册的情况。具体缓存的资源可以在Cache Storage中看到。

------

