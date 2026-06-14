- ### Webpack 原理

  > 本章节我们主要学习：
  >
  > - loader 原理
  > - 自定义常用 loader
  > - plugin 原理
  > - 自定义常用 plugin

  - #### Loader 原理

    > 帮助 webpack 将不同类型的文件转换为 webpack 可识别的模块，以便能够`import`导入不同类型的文件。

    - ##### loader 执行顺序

      > **loader分为：**
      >
      > - pre： 前置 loader
      > - normal： 普通 loader
      > - inline： 内联 loader
      > - post： 后置 loader
      >
      > **4 类 loader 的执行优级为**：`pre > normal > inline > post` 。
      >
      > 相同优先级的 loader 执行顺序为：`从右到左/从下到上`。也就是数组末尾的loader先执行（栈，后进先出）。
      >
      > 之前我们没有指定loader的`enforce: "pre/normal/post"`配置项，它们都是普通的`normal loader`。

      > **使用 loader 的方式：**
      >
      > - 配置方式：在 `webpack.config.js` 文件中指定 loader。（pre、normal、post loader）
      > - 内联方式：在每个 `import` 语句中显式指定 loader。（inline loader）
      >
      > **inline loader：**
      >
      > 用法：`import Styles from 'style-loader!css-loader?modules!./styles.css';`
      >
      > 含义：
      >
      > - 使用 `css-loader` 和 `style-loader` 处理 `styles.css` 文件
      > - 通过 `!` 将资源中的 loader 分开
      > - `?`是给`css-loader`传递的参数
      >
      > `inline loader` 可以通过添加不同前缀，跳过其他类型 loader。也就是说如果在 `webpack.config.js` 文件中也配置了对应类型的 loader 的话，那么加上这些前缀的话，配置文件中的loader就不会执行。
      >
      > - `!` 跳过 normal loader。（只执行inline loader）
      >
      >   ```js
      >   import Styles from '!style-loader!css-loader?modules!./styles.css';
      >   ```
      >
      > - `-!` 跳过 pre 和 normal loader。
      >
      >   ```js
      >   import Styles from '-!style-loader!css-loader?modules!./styles.css';
      >   ```
      >
      > - `!!` 跳过 pre、 normal 和 post loader。
      >
      >   ```js
      >   import Styles from '!!style-loader!css-loader?modules!./styles.css';
      >   ```
      >

    - ##### 开发一个 loader

      1. ###### 定义一个loader（函数）
  
         ```js
         // loaders/loader1.js
         module.exports = function loader1(content, map, meta) {
           /*
         		content：源文件的内容
         		map：SourceMap 相关的数据
         		meta：别的loader传递过来的数据，可以是任何内容
           */
           console.log(content);
           return content;
         };
         ```
  
         > 它将被处理的文件源码作为参数，输出loader处理后的结果。
  
      2. ###### 使用自定义的loader：
  
         ```js
         // webpack-config.js
         const path = require("path");
         const HtmlWebpackPlugin = require("html-webpack-plugin");
         
         module.exports = {
           entry: "./src/main.js",
           output: {
             path: path.resolve(__dirname, "dist"),
             filename: "js/[name].js",
             clean: true,
           },
           module: {
             rules: [
               {
                 test: /\.js$/,
                 // webpack去处理文件时，就会调用对应的loader（函数）去执行
                 loader: "./loaders/loader1.js",
               },
             ],
           },
           plugins: [
             new HtmlWebpackPlugin({
               template: path.resolve(__dirname, "../public/index.html"),
             }),
           ],
           mode: "development",
         };
         ```

    - ##### loader 分类

      > loader有不同的定义方式。

      1. ###### 同步 loader

         ```js
         module.exports = function (content, map, meta) {
           return content;
         };
         ```
  
         > 同步loader还有另一种更灵活的写法：`this.callback`，它允许向后传递多个参数，而不仅仅是 `content`。

         ```js
         module.exports = function (content, map, meta) {
           // 第1个参数是loader执行错误时的Error对象，没有错误就传null
           // 传递map，让source-map不中断；传递meta，让下一个loader接收到其他参数
           this.callback(null, content, map, meta);
           return; // 当调用 callback() 函数时，总是返回 undefined
         };
         ```
  
         > 这种方式的好处：
         >
         > 1. 可以将loader执行失败的原因传递出去
         > 2. 可以传递多个参数出去，return就只能返回一个结果了，有局限性。
  
      2. ###### 异步 loader
  
         > 异步loader用于异步操作。只有该loader的异步方法`this.async()`执行后，后续的loader才会开始执行执行。
  
         ```js
         module.exports = function (content, map, meta) {
           const callback = this.async();
           // 进行异步操作
           setTimeout(() => {
             // 该callback函数执行后，才会继续执行后面的loader
             callback(null, content, map, meta);
             /*
             this.callback();  // 实际上它已经在同步代码中被调用过了，再次调用会报错
             注意：同步loader的callback方法不能放在异步操作中，会报错。这是因为下一个loader会接收到，同步loader函数return的undefined，所以后面的loader的content接收到的是undefined就有问题了。
             */
           }, 1000);
         };
         ```
  
         > `this.async()` 执行后，才会继续执行后面的loader。
         >
         > 注意：同步loader的 `callback()` 方法不能放在异步操作中，会报错。这是因为下一个loader会接收到，同步loader函数return的undefined，所以后面的loader的content接收到的是undefined就有问题了。
  
         > **TIP：**
         >
         > 由于同步计算过于耗时，在 Node.js 这样的单线程环境下进行此操作并不是好的方案，我们建议尽可能地使你的 loader 异步化。但如果计算量很小，同步 loader 也是可以的。
  
      3. ###### Raw Loader
  
         > 默认情况下，资源文件会被转化为 UTF-8 格式字符串，然后传给 loader 函数的 content 参数。通过设置 `raw` 为 `true`，使 loader 函数可以接收原始的 Buffer 来处理二进制内容。
  
         ```js
         module.exports = function (content) {
           // content是一个Buffer数据
           return content;
         };
         module.exports.raw = true; // 开启 Raw Loader
         ```
  
         > 当然，Raw Loader中也可以使用同步或异步的写法。
  
      4. ###### Pitching Loader
  
         ```js
         module.exports = function (content) {
           return content;
         };
         module.exports.pitch = function (remainingRequest, precedingRequest, data) {
           // 该函数会在loader执行之前去执行
           console.log("do somethings");
         };
         ```
  
         > 注意：pitch函数和loader函数的执行顺序是相反的，是从左到右，先执行数组头部的loader定义的pitch。
         >
         > webpack 会先从左到右执行 loader 链中的每个 loader 上的 pitch 方法（如果有），然后再从右到左执行 loader 链中的每个 loader 上的普通 loader 方法。（假设都是normal loader）
         >
         > ![loader1](./assets/loader1.png)
         >
         > 在这个过程中如果任何 pitch 函数有返回值（不为undefined），则 loader 链被阻断。webpack 会跳过后面所有的的 pitch 和 loader，直接进入上一个 loader 。如果没有上一个loader函数，那么当前处理就完成了。（pitch函数return的结果会传给后面的loader函数）
         >
         > 这其实是一种**熔断机制**，我们可以在处理一些任务失败时，让后面的loader都不再执行了，避免不可预见的问题。
         >
         > ![loader2](./assets/loader2.png)
  
    - ##### loader API
  
      | 方法名                  | 含义                                                         | 用法                                           |
      | ----------------------- | ------------------------------------------------------------ | ---------------------------------------------- |
      | this.async              | 异步回调 loader。返回 this.callback                          | const callback = this.async()                  |
      | this.callback           | 可以同步或者异步调用的并返回多个结果的函数                   | this.callback(err, content, sourceMap?, meta?) |
      | this.getOptions(schema) | 获取我们给 loader 配置的 options，调用时传一个规则schema，符合规则就可以获取到对应的配置，不符合规则就会报错 | this.getOptions(schema)                        |
      | this.emitFile           | 产生一个文件                                                 | this.emitFile(name, content, sourceMap)        |
      | this.utils.contextify   | 返回一个相对路径，参数是当前的路径和要处理的目标路径。该方法返回的路径是符合loader格式要求的路径。 | this.utils.contextify(context, request)        |
      | this.utils.absolutify   | 返回一个绝对路径                                             | this.utils.absolutify(context, request)        |
      
      > 更多文档，请查阅 [webpack 官方 loader api 文档](https://webpack.docschina.org/api/loaders/#the-loader-context)
  
    - ##### 手写 clean-log-loader
  
      > 作用：用来清理 js 代码中的`console.log`
      >
      > ```js
      > // loaders/clean-log-loader.js
      > module.exports = function cleanLogLoader(content) {
      >     // 将console.log替换为空
      >     return content.replace(/console\.log\(.*\);?/g, "");
      > };
      > ```
  
    - ##### 手写 banner-loader
  
      > 作用：给 js 代码添加文本注释
      >
      > ```js
      > // loaders/banner-loader/index.js
      > const schema = require("./schema.json");
      > 
      > module.exports = function (content) {
      >   // 获取loader的options，同时对options内容进行校验
      >   // schema是options的校验规则（符合 JSON schema 规则）
      >   const options = this.getOptions(schema);
      >   const prefix = `
      >     		/*
      >      * Author: ${options.author}
      >      */
      >   `;
      >   return `${prefix} \n ${content}`;
      > };
      > ```
      > 
      > ```json
      >// loaders/banner-loader/schema.json
      > {
      >   "type": "object",
      >   "properties": {
      >     // 只允许包含string类型的author属性
      >     "author": {
      >       "type": "string"
      >     }
      >   },
      >   "additionalProperties": false  // 不允许追加其他属性了
      > }
      > ```
      
    - ##### 手写 babel-loader
  
      > 作用：编译 js 代码，将 ES6+语法编译成 ES5-语法。
      >
      > 说明：由于babel将大部分功能已经做成了一个个的npm包，因此我们只写loader部分的代码，具体的实现直接用babel提供好的包即可。
      >
      > 这里我们用到了2个包：`@babel/core`和`@babel/preset-env`，分别是babel的核心功能以及具体做哪些语法转换。
  
      1. 下载包：`npm i @babel/core @babel/preset-env -D`
  
      2. 编写loader代码：
  
         ```js
         // loaders/babel-loader/index.js
         const schema = require("./schema.json");
         const babel = require("@babel/core");
         
         module.exports = function (content) {
           const options = this.getOptions(schema);
           // 使用异步loader
           const callback = this.async();
           // 使用babel对js代码进行编译
           babel.transform(content, options, function (err, result) {
             // result对象有3个属性，code、map、ast（分别是转换后的代码、sourceMap、以及抽象语法树）
             callback(err, result.code);
           });
         };
         ```
  
         > 这部分代码是参考的babel官网。其中：
         >
         > `babel.transform`的回调函数是异步的，因此这里必须用**异步Loader**。
  
      3. 编写schema配置文件：
  
         ```js
         // loaders/banner-loader/schema.json
         {
           "type": "object",
           "properties": {
             "presets": {
               "type": "array"
             }
           },
           "additionalProperties": true
         }
         ```
  
    - ##### 手写 file-loader
  
      > `file-loader` 需要做3件事：
      >
      > 1. 根据文件内容为文件生成hash值。
      > 2. 将原文件原封不动输出出去。
      > 3. 向外暴露：`export default "文件路径";`，这样其他地方导入之后就是一个文件路径字符串。
  
      1. 下载包：`npm i loader-utils -D`
      
      2. loaders/file-loader.js：
      
         ```js
         const loaderUtils = require("loader-utils");
         
         function fileLoader(content) {
           // 根据文件内容生产一个新的文件名称（这里我们借助webpack官方提供的库loader-utils来做）
           const filename = loaderUtils.interpolateName(this, "[hash].[ext]", {
             content,
           });
           // 输出文件
           this.emitFile(filename, content);
           // 暴露出去，给js引用（记得加上''）
           return `export default '${filename}'`;
         }
         
         // loader 解决的是二进制的内容
         // 图片是 Buffer 数据
         fileLoader.raw = true;
         
         module.exports = fileLoader;
         ```
         
      3. 配置file-loader：
      
         ```js
         {
           test: /\.(png|jpe?g|gif)$/,
           loader: "./loaders/file-loader.js",
           type: "javascript/auto", // webpack默认还会输出文件，需要配置下这里，解决图片重复打包问题
         },
         ```
      
    - ##### 手写 style-loader
  
      > 作用：动态创建 style 标签，插入 js 中的样式代码，使样式生效。
  
      ```js
      // loaders/style-loader/index.js
      module.exports = function (content) {
        const script = `
          const styleEl = document.createElement('style');
          styleEl.innerHTML = ${JSON.stringify(content)};
          document.head.appendChild(styleEl);
        `;
        return script;  // 返回的js代码会被添加到bundle.js中，当该js被引入到html中后就会执行，达成style标签的添加
      };
      ```
      
      > 但是只有一个 style-loader 会有问题，css中通过`url()`导入的其他资源（字体图标、图片等），这些文件我们也需要去进行关联，让它们被打包到dist目录中去才行。
      >
      > 因此我们需要在 style-loader 之前配置上 css-loader，让它帮助我们去解析css文件中的各种依赖，并对这些文件进行相应的处理。
      >
      > 但是新的问题是：`css-loader` 的返回值是一段js代码，在这个js中将css样式默认暴露了出去。
      >
      > 我们需要做的是：执行 `css-loader` 并拿到执行后暴露的内容，将其插入到 style 标签中。
      
      ```js
      // loaders/style-loader.js
      const styleLoader = () => {};
      
      styleLoader.pitch = function (remainingRequest) {  // remainingRequest是后面需要执行的loader函数
        /*
          remainingRequest: C:\Users\86176\Desktop\source\node_modules\css-loader\dist\cjs.js!C:\Users\86176\Desktop\source\src\css\index.css
            这里是inline loader用法，代表后面还有一个css-loader的函数等待执行
      
          我们需要将remainingRequest中的路径转化成相对路径，webpack才能处理
            希望得到：../../node_modules/css-loader/dist/cjs.js!./index.css
      
          所以：需要将绝对路径转化成相对路径
          要求：
            1. 必须是相对路径
            2. 相对路径必须以 ./ 或 ../ 开头
            3. 相对路径的路径分隔符必须是 / ，不能是 \
        */
        const relativeRequest = remainingRequest.split("!").map((part) => {
          // this.context是当前目录
          return this.utils.contextify(this.context, part);
        }).join("!");
      
        /*
          !!${relativeRequest} 
            relativeRequest：../../node_modules/css-loader/dist/cjs.js!./index.css
            relativeRequest是inline loader用法，代表要处理的index.css资源, 使用css-loader处理
            !!代表禁用所有配置的loader，只使用inline loader。（也就是外面我们style-loader和css-loader）,它们被禁用了，只是用我们指定的inline loader，也就是css-loader
      
          import style from "!!${relativeRequest}"
            引入css-loader处理后的css文件
            为什么需要css-loader处理css文件，不是我们直接读取css文件使用呢？
            因为可能存在@import导入css语法，这些语法就要通过css-loader解析才能变成一个css文件，否则我们引入的css资源会缺少
          const styleEl = document.createElement('style')
            动态创建style标签
          styleEl.innerHTML = style
            将style标签内容设置为处理后的css代码
          document.head.appendChild(styleEl)
            添加到head中生效
        */
        const script = `
          import style from "!!${relativeRequest}"
          const styleEl = document.createElement('style')
          styleEl.innerHTML = style
          document.head.appendChild(styleEl)
        `;
      
        // style-loader是第一个loader, 由于return导致熔断，所以其他loader不执行了（不管是normal还是pitch）
        return script;  // 返回的js代码会被添加到bundle.js中，当该js被引入到html中后就会执行，达成style标签的添加
      };
      
      module.exports = styleLoader;
      ```
  
  - #### Plugin 原理
  
    > 通过插件我们可以扩展 webpack，加入自定义的构建行为，使 webpack 可以执行更广泛的任务，拥有更强的构建能力。
  
    - ##### Plugin 工作原理
  
      > webpack 就像一条生产线，要经过一系列处理流程后才能将源文件转换成输出结果。 这条生产线上的每个处理流程的职责都是单一的，多个流程之间有存在依赖关系，只有完成当前处理后才能交给下一个流程去处理。 插件就像是一个插入到生产线中的一个功能，在特定的时机对生产线上的资源做处理。webpack 通过 Tapable （钩子事件）来组织这条复杂的生产线。 webpack 在运行过程中会广播事件，插件只需要监听它所关心的事件，就能加入到这条生产线中，去改变生产线的运作。 webpack 的事件流机制保证了插件的有序性，使得整个系统扩展性很好。 ——「深入浅出 Webpack」
  
      站在代码逻辑的角度就是：webpack 在编译代码过程中，会触发一系列 `Tapable` 钩子事件，插件所做的，就是找到相应的钩子，往上面挂上自己的任务，也就是注册事件，这样，当 webpack 构建的时候，插件注册的事件就会随着 `Tapable` 钩子事件的触发而执行了。
  
    - ##### Webpack 内部的钩子
  
      1. ###### 什么是钩子
  
         > 钩子的本质就是：事件。为了方便我们直接介入和控制编译过程，webpack 把编译过程中触发的各类关键事件封装成事件接口暴露了出来。这些接口被很形象地称做：`hooks`（钩子）。开发插件，离不开这些钩子。（生命周期回调函数）
  
      2. ###### Tapable
  
         > `Tapable` 为 webpack 提供了统一的插件接口（钩子）类型定义，它是 webpack 的核心功能库。webpack 中目前有十种 `hooks`，在 `Tapable` 源码中可以看到：（即：Hooks 是通过 Tapable 来实现的。这些了解即可）
         >
         > ```js
         > // https://github.com/webpack/tapable/blob/master/lib/index.js
         > exports.SyncHook = require("./SyncHook");
         > exports.SyncBailHook = require("./SyncBailHook");
         > exports.SyncWaterfallHook = require("./SyncWaterfallHook");
         > exports.SyncLoopHook = require("./SyncLoopHook");
         > exports.AsyncParallelHook = require("./AsyncParallelHook");
         > exports.AsyncParallelBailHook = require("./AsyncParallelBailHook");
         > exports.AsyncSeriesHook = require("./AsyncSeriesHook");
         > exports.AsyncSeriesBailHook = require("./AsyncSeriesBailHook");
         > exports.AsyncSeriesLoopHook = require("./AsyncSeriesLoopHook");
         > exports.AsyncSeriesWaterfallHook = require("./AsyncSeriesWaterfallHook");
         > exports.HookMap = require("./HookMap");
         > exports.MultiHook = require("./MultiHook");
         > ```
         >
         > `Tapable` 还统一暴露了三个方法给插件，用于注入不同类型的自定义构建行为：（就是所有的 Hooks 钩子身上都有这3个方法）
         >
         > - `tap`：可以给同步钩子和异步钩子注册事件。因为我们有时只需要在异步钩子中执行同步操作。
         > - `tapAsync`：只能给异步钩子注册事件。（异步钩子中执行异步操作时，必须用 `tapAsync` 和 `tapPromise`）
         > - `tapPromise`：Promise 方式给异步钩子注册事件。
  
    - ##### Plugin 构建对象
  
      1. ###### Compiler
  
         > **compiler 对象中保存着完整的 Webpack 环境配置**，每次启动 webpack 构建时它都是一个**独一无二**，仅仅会创建一次的对象。
         >
         > 这个对象会在**首次启动 Webpack 时创建**，我们可以通过 compiler 对象上访问到 Webapck 的主环境配置，比如 loader 、 plugin 等等配置信息。
         >
         > 它有以下主要属性：
         >
         > - `compiler.options` 可以访问本次启动 webpack 时候所有的配置文件，包括但不限于 loaders 、 entry 、 output 、 plugin 等等完整配置信息。
         > - `compiler.inputFileSystem` 和 `compiler.outputFileSystem` 可以进行文件操作，相当于 Nodejs 中 fs。我们在插件中输入或输出文件需要用它们来做。
         > - `compiler.hooks` 身上有许多的不同种类的 Hooks，用于我们往 compiler 对象上注册钩子，从而可以在 compiler 生命周期中植入不同的逻辑。
         >
         > [compiler hooks 文档](https://webpack.docschina.org/api/compiler-hooks/)
  
      2. ###### Compilation
  
         > compilation 对象代表一次资源的构建，是单次构建的上下文，包含本次构建的所有状态（如模块、资源、哈希等）。compilation 实例能够访问所有的模块和它们的依赖。
         >
         > 一个 compilation 对象会对构建依赖图中所有模块，进行编译。 在编译阶段，模块会被加载(load)、封存(seal)、优化(optimize)、 分块(chunk)、哈希(hash)和重新创建(restore)。
         >
         > 它有以下主要属性：
         >
         > - `compilation.modules` 可以访问所有模块，打包的每一个文件都是一个模块。
         > - `compilation.chunks` chunk 即是多个 modules 组成而来的一个代码块。入口文件引入的资源组成一个 chunk，通过代码分割的模块又是另外的 chunk。
         > - `compilation.assets` 可以访问本次打包生成所有文件的结果。
         > - `compilation.hooks` 类似与 compiler 对象，compilation 对象身上也有许多的不同种类的 Hooks，用于我们往 compilation 对象上注册钩子，从而可以在 compilation 生命周期中植入不同的逻辑。
         >
         > [compilation hooks 文档](https://webpack.docschina.org/api/compilation-hooks/)
  
      3. ###### 生命周期简图
  
         > ![plugin](./assets/plugin.jpg)
         >
         > 注意：上方的 compilation 阶段可能会循环执行多次，对应多个 compilation 对象并处理不同的资源。
  
    - ##### 开发第一个插件
  
      1. ###### 最简单的插件
    
         ```js
         // plugins/test-plugin.js
         class TestPlugin {
           constructor() {
             console.log("TestPlugin constructor()");
           }
           // 1. webpack读取配置时，new TestPlugin() ，会执行插件 constructor 方法
           // 2. webpack创建 compiler 对象
           // 3. 遍历所有插件，调用插件的 apply 方法
           apply(compiler) {
             console.log("TestPlugin apply()");
           }
         }
         
         module.exports = TestPlugin;
         ```
         
         > 插件就是一个包含了 `apply` 方法的构造函数：
         >
         > 1. Webpack 一上来会读取解析 `webpack.config.js` 文件，此时就会 `new TestPlugin()` 将插件创建出来，从而执行插件的 `constructor` 方法。
         > 2. Webpack 开始创建 `compiler` 对象，然后遍历所有插件，调用插件的 `apply` 方法，将 `compiler` 对象作为参数传进去。
         > 3. 执行剩下的编译流程（触发各个 Hooks 事件）。
         
      2. ###### 注册 hook
      
         ```js
         // plugins/test-plugin.js
         class TestPlugin {
           constructor() {
             console.log("TestPlugin constructor()");
           }
         
           apply(compiler) {
             console.log("TestPlugin apply()");
         
             /* 由文档可知，compiler对象上的environment是一个SyncHook同步钩子，因此我们调用tap方法来给TestPlugin插件注册事件，第二个参数是事件回调函数，environment钩子不会给回调传参，因此这里形参为空。
             还有比如compiler对象上的emit就是一个AsyncSeriesHook异步串行钩子，就是说我们可以给它身上注册多个回调，它们会按顺序串行执行。
             */
             compiler.hooks.environment.tap("TestPlugin", () => {
               console.log("TestPlugin environment");
             });
         
             // emit是可以注册多个事件的，如下方注册的3个事件回调，由于是异步串行，因此它们是从上往下依次执行的
             compiler.hooks.emit.tap("TestPlugin", (compilation) => {
               console.log("TestPlugin emit 111");
             });
             // `tapAsync`的回调函数中，最后一个参数会再传一个callback，它是异步操作完成后的回调函数
             compiler.hooks.emit.tapAsync("TestPlugin", (compilation, callback) => {
               setTimeout(() => {
         	      console.log("TestPlugin emit 222");
                 callback();
               }, 2000);
             });
             // `tapPromise`要求必须返回一个Promise对象
             compiler.hooks.emit.tapPromise("TestPlugin", (compilation) => {
               return new Promise((resolve) => {
                 setTimeout(() => {
                   console.log("TestPlugin emit 333");
                   resolve();
                 }, 1000);
               });
             });
             
             // make是AsyncParallelHook异步并行的钩子，可以注册多个事件，多个事件是并行的
             compiler.hooks.make.tapAsync("TestPlugin", (compilation, callback) => {
               // compilation也可以给钩子注册回调。它需要在compilation hooks触发前注册才会被调用
               compilation.hooks.seal.tap("TestPlugin", () => {
                 console.log("TestPlugin seal");
               });
         
               setTimeout(() => {
         	      console.log("TestPlugin make 111");
                 callback();
               }, 3000);
             });
             compiler.hooks.make.tapAsync("TestPlugin", (compilation, callback) => {
               setTimeout(() => {
         	      console.log("TestPlugin make 222");
                 callback();
               }, 1000);
             });
           }
         }
         
         module.exports = TestPlugin;
         ```
      
    - ##### BannerWebpackPlugin
    
      > 我们之前开发的 Banner loader 只能在开发模式下使用，生产模式下默认会压缩优化代码，注释都会被删除。因此我们通过 BannerWebpackPlugin 插件来给打包输出的文件添加注释。
      >
      > 开发思路：
      >
      > - 我们需要在打包输出前添加注释，要用到 `compiler.hooks.emit` 钩子, 它是打包输出前触发。
      > - 如何获取打包输出的资源？`compilation.assets` 可以获取所有即将输出的资源文件。
      >
      > 实现：
      >
      > ```js
      > // plugins/banner-webpack-plugin.js
      > class BannerWebpackPlugin {
      >   constructor(options = {}) {
      >     this.options = options;
      >   }
      > 
      >   apply(compiler) {
      >     // 需要处理文件
      >     const extensions = ["js", "css"];
      > 
      >     // emit是异步串行钩子
      >     compiler.hooks.emit.tapAsync("BannerWebpackPlugin", (compilation, callback) => {
      >       // compilation.assets包含所有即将输出的资源
      >       // 通过过滤只保留需要处理的文件（js、css）
      >       const assetPaths = Object.keys(compilation.assets).filter((path) => {
      >         const splitted = path.split(".");
      >         return extensions.includes(splitted[splitted.length - 1]);
      >       });
      > 
      >       assetPaths.forEach((assetPath) => {
      >         const asset = compilation.assets[assetPath];
      > 
      >         const source = `/*
      > * Author: ${this.options.author}
      > */\n${asset.source()}`;
      > 
      >         // 覆盖资源
      >         compilation.assets[assetPath] = {
      >           // 资源内容。最终资源输出时会调用source()方法，该方法返回值就是资源的内容
      >           source() {
      >             return source;
      >           },
      >           // 资源大小（单位/字节）
      >           size() {
      >             return source.length;
      >           },
      >         };
      >       });
      > 
      >       callback();
      >     });
      >   }
      > }
      > 
      > module.exports = BannerWebpackPlugin;
      > ```
    
    - ##### CleanWebpackPlugin
    
      > 作用：在 webpack 打包输出前将上次打包内容清空。
      >
      > 开发思路：
      >
      > 如何在打包输出前执行？需要使用 `compiler.hooks.emit` 钩子, 它是打包输出前触发。这是为了万一我们打包失败了，这时就不要清空上次打包的资源。
      >
      > 如何清空上次打包内容？
      >
      > - 获取打包输出目录：通过 compiler 对象。
      > - 通过文件操作清空内容：通过 `compiler.outputFileSystem` 操作文件。
      >
      > 实现：
      >
      > ```js
      > // plugins/clean-webpack-plugin.js
      > class CleanWebpackPlugin {
      >   apply(compiler) {
      >     // 获取操作文件的对象
      >     const fs = compiler.outputFileSystem;
      >     // emit是异步串行钩子
      >     compiler.hooks.emit.tapAsync("CleanWebpackPlugin", (compilation, callback) => {
      >       const outputPath = compiler.options.output.path;  // 获取输出文件目录
      >       const err = this.removeFiles(fs, outputPath);  // 删除目录中所有文件
      >       // 执行成功err为undefined，执行失败err就是错误原因
      >       callback(err);
      >     });
      >   }
      > 
      >   removeFiles(fs, path) {
      >     try {
      >       // 读取当前目录下所有文件
      >       const files = fs.readdirSync(path);
      > 
      >       // 遍历文件，删除
      >       files.forEach((file) => {
      >         // 获取文件完整路径
      >         const filePath = `${path}/${file}`;
      >         // 分析文件
      >         const fileStat = fs.statSync(filePath);
      >         // 判断是否是文件夹
      >         if (fileStat.isDirectory()) {
      >           // 是文件夹需要递归遍历删除下面所有文件
      >           this.removeFiles(fs, filePath);
      >         } else {
      >           // 不是文件夹就是文件，直接删除
      >           fs.unlinkSync(filePath);
      >         }
      >       });
      > 
      >       // 最后删除当前目录
      >       fs.rmdirSync(path);
      >     } catch (e) {
      >       // 将产生的错误返回出去
      >       return e;
      >     }
      >   }
      > }
      > 
      > module.exports = CleanWebpackPlugin;
      > ```
      
    - ##### AnalyzeWebpackPlugin
    
      > 作用：分析 webpack 打包资源大小，并输出分析文件。
      >
      > 开发思路：
      >
      > - 在哪做? `compiler.hooks.emit`，它是在打包输出前触发，我们需要分析资源大小同时添加上分析后的 md 文件。
      >
      > 实现：
      >
      > ```js
      > // plugins/analyze-webpack-plugin.js
      > class AnalyzeWebpackPlugin {
      >   apply(compiler) {
      >     // emit是异步串行钩子
      >     compiler.hooks.emit.tap("AnalyzeWebpackPlugin", (compilation) => {
      >       // Object.entries将对象变成二维数组。二维数组中第一项值是key，第二项值是value
      >       const assets = Object.entries(compilation.assets);
      > 
      >       let source = "# 分析打包资源大小 \n| 名称 | 大小 |\n| --- | --- |";
      > 
      >       assets.forEach(([filename, file]) => {
      >         source += `\n| ${filename} | ${Math.ceil(file.size() / 1024)}kb |`;
      >       });
      > 
      >       // 添加资源
      >       compilation.assets["analyze.md"] = {
      >         source() {
      >           return source;
      >         },
      >         size() {
      >           return source.length;
      >         },
      >       };
      >     });
      >   }
      > }
      > 
      > module.exports = AnalyzeWebpackPlugin;
      > ```
    
    - ##### InlineChunkWebpackPlugin
    
      > 如果我们打包的 .js 文件比较小，我们希望给该文件内置到 index.html 中，从而减少请求数量。例如：webpack 打包生成的 runtime 文件太小了，额外发送请求性能不好，所以需要将其内联到 js 中，从而减少请求数量。
      >
      > 开发思路：
      >
      > - 我们需要借助 `html-webpack-plugin` 来实现
      >   - 在 `html-webpack-plugin` 输出 index.html 前将内联 runtime 注入进去
      >   - 删除额外输出的 runtime 文件
      > - 如何操作 `html-webpack-plugin`？
      >
      > ![flow](./assets/flow.png)
      >
      > 我们在 html-webpack-plugin 提供的 `alterAssetTagGroups` 钩子中去做，因为这时候Tags标签都分好组了，我们只需要去组中找到相应的标签去改一下就好了。
      >
      > 我们需要到 [html-webpack-plugin 官方文档](https://github.com/jantimon/html-webpack-plugin/#afteremit-hook)，参考它提供的样例代码来实现：（需要先：`npm i safe-require -D`）
      >
      > ```js
      > // plugins/inline-chunk-webpack-plugin.js
      > // 我们的插件对于 html-webpack-plugin 来说，是一个可选的依赖，因此用这种方式引入
      > const HtmlWebpackPlugin = require("safe-require")("html-webpack-plugin");
      > 
      > class InlineChunkWebpackPlugin {
      >   constructor(tests) {
      >     this.tests = tests;
      >   }
      > 
      >   apply(compiler) {
      >     compiler.hooks.compilation.tap("InlineChunkWebpackPlugin", (compilation) => {
      >       // 1、获取html-webpack-plugin的hooks
      >       const hooks = HtmlWebpackPlugin.getHooks(compilation);
      > 			// 2、往html-webpack-plugin的hook（alterAssetTagGroups）上注册事件回调
      >       hooks.alterAssetTagGroups.tap("InlineChunkWebpackPlugin", (assets) => {
      >         // 3、从里面将script引入的runtime.js文件，变成inline-script
      >         assets.headTags = this.getInlineTag(assets.headTags, compilation.assets);
      >         assets.bodyTags = this.getInlineTag(assets.bodyTags, compilation.assets);
      >       });
      > 			// 4、删除runtime.js文件
      >       hooks.afterEmit.tap("InlineChunkHtmlPlugin", () => {
      >         Object.keys(compilation.assets).forEach((assetName) => {
      >           if (this.tests.some((test) => assetName.match(test))) {
      >             delete compilation.assets[assetName];
      >           }
      >         });
      >       });
      >     });
      >   }
      > 
      >   getInlineTag(tags, assets) {
      >    /*
      >     修改之前：
      >       [
      >         {
      >           tagName: '',
      >           voidTag: false,
      >           meta: { plugin: 'html-webpack-plugin' },
      >           attributes: { defer: true, type: undefined, src: 'js/runtime~main.js' },
      >         }
      >       ]
      >     修改之后：
      >       [
      >         {
      >           tagName: '',
      >           innerHTML: runtime文件的内容,
      >           closeTag: true,
      >         }
      >       ]
      >     */
      >     return tags.map((tag) => {
      >       if (tag.tagName !== "script") return tag;  // 如果不是script标签，不做修改
      >       const filepath = tag.attributes.src;
      >       if (!filepath) return tag;  // 如果不是scripe标签的方式引入，不做修改
      > 
      > 		  // 如果文件名匹配上了，是我们通过参数传进来要处理的文件，则进行替换
      >       if (!this.tests.some((test) => filepath.match(test))) return tag;
      >       return { tagName: "script", innerHTML: assets[filepath].source(), closeTag: true };
      >     });
      >   }
      > }
      > 
      > module.exports = InlineChunkWebpackPlugin;
      > ```


------

