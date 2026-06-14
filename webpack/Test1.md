# Webpack5

- ### 关于打包工具

  - ##### 为什么前端需要打包工具：

    > 前端进行开发时，我们会使用框架（Vue、React），ES6模块化语法，Less/Sass等CSS预处理器进行代码的编写。而这样的代码想在浏览器上运行必须先被预处理，经历很多繁琐的步骤处理后才能运行在前端。用打包工具可以让我们避免做这样复杂的工作，让Webpack帮我们做项目的打包和代码替换，这样就可以直接放在前端运行。除此之外，打包工具还能够压缩代码、做兼容性处理、提升代码性能等。
    >
    > 总之，打包工具就是将**开发环境**的代码，编译为**生产环境**的代码。
    >
    > 因为开发环境的代码更符合我们的阅读习惯，可以热更新、使用Less/Sass/Stylus、使用最新的ES6语法...；生产环境的代码对于浏览器更友好、兼容性更好、运行效率更高、代码体积更小...
  
  - ##### 打包工具可以帮我们做什么：
  
    > 1. **性能优化**：通过合并文件，减少了文件的大小，从而减少HTTP请求的数量，加快页面加载速度。
    >
    > 2. **代码预处理和打包**：打包工具可以将模块化语法、框架语法（Vue、React）进行预处理。
    > 3. **资源优化**：能够处理各种资源（如图片、字体、样式）并将它们包含在最终的输出中。
    > 4. **转换&兼容性**：可以将高级的 JS（例如 ES6+）或其他编程语言（例如 TS）转换为广泛支持的 ES5 代码。
    > 5. **依赖管理**：确保代码按正确的顺序执行，满足模块间的依赖关系。
  
  
    - ##### 常见的打包工具：
  
      > - Grunt
      > - Gulp
      > - Parcel
      > - **Webpack**
      > - Rollup
      > - esbuild
      > - **Vite**
      > - ...
      >
      > 目前市面上最流行的是 Webpack，它也是每个前端必备的技能。
  


  - ### `Webpack` 概述

    > Webpack是一个**静态资源打包工具**（就是别人写好的一个基于Node的依赖包）。它会以一个或多个文件作为入口，编译后生成一个或多个文件，输出到指定目录下。通常我们将这些输出的文件称为`bundle`（束、捆），`bundle`可以直接在浏览器中运行。
    >
    > 注意：我们之前都是在html中通过`<script>`标签引入第三方库（如lodash）的js文件的。现在有了Webpack这样的静态资源打包工具就不同了：我们在js代码中`import`导入这个第三方库，执行打包命令，最终输出的js文件就包含了lodash的代码。

    ###### 使用：

    1. ##### 首先初始化`package.json`文件：`npm init`，使我们的项目目录先成为一个Node工程。

    2. ##### 下载`webpack`作为工程的开发依赖：`npm i webpack webpack-cli -D`

       > `webpack-cli`是Webpack的命令行工具，里面有很多我们需要的Webpack命令。其实node命令我们之所以可以直接运行，就是因为它已经内置了node的命令工具cli，而`webpack`包中不包含cli，所以要手动安装`webpack-cli`才能够用Webpack的命令。

    3. ##### 通过命令`webpack`，将我们开发代码编译为生产代码

       > `npx`命令会将`./node_modules/.bin`目录临时添加为环境变量，然后执行后面紧跟着的cli命令。如果没有npx，还得cd切到`./node_modules/.bin`目录中才能行其中的cli命令。

       - **开发模式**打包：`npx webpack ./src/main.js --mode=development`

       - **生产模式**打包（默认）：`npx webpack ./src/main.js --mode=production`

         > - 在`webpack`命令后面指定打包的入口文件，也就是从`src`中的`main.js`开始打包。将该入口文件、以及入口文件中通过`import`关联导入的所有文件一起打包，然后输出为一个或多个`bundle`。
         > - `--mode=xxx`参数用于指定 Webpack 的工作模式。若是开发模式则输出易读代码，生产模式默认会混淆压缩JS。

    ###### 观察输出文件：

    1. 默认`Webpack`将`bundle`输出到项目根目录下的`dist`目录中。
    2. `Webpack` 只是一个**静态模块打包工具**，它本身并不提供任何功能，仅仅只是编译和输出JS文件（即将模块化的代码编译为非模块化的代码）。我们需要的任何功能，都体现在 Webpack 的插件（`plugin`）和加载器（`loader`）中。并且Webpack有很多官方或社区提供的`plugin`和`loader`，我们需要的任何功能，都可以找到对应的 Webpack 插件和加载器，将其配置到 Webpack 配置文件中即可。
    3. 当`main.js`入口文件中`import`导入了`.css`、`.img`等不符合JS语法的资源，如果不在 `Webpack` 中进行配置的话，它不去对代码做预处理，那么就会编译失败。因为JS语法不支持这样做。

- ### `Webpack` 的基本配置

  - #### 概念：

    1. **入口（entry）**：打包时要告诉Webpack，从哪个/些文件开始打包。
    2. **输出（output/bundle）**：指定打包后文件的输出位置，如何命名等。
    3. **加载器（loader）**：Webpack本身只能处理JS、JSON资源，处理其他资源需要配置对应的加载器`loader`。
    4. **插件（plugins）**：使用插件可以扩展Webpack的功能，让它做更多的事情。
    5. **模式（mode）**：主要有2种输出模式，*开发模式（development）*和*生产模式（production）*。Webpack5在生产模式下默认会将JS代码进行优化、混淆、压缩（内部是通过`terser`来完成的），Webpack4还需要进行配置才可以。

  - #### Webpack的配置文件：

    > 对Webpack进行配置需要用到`webpack.config.js`文件。在Node项目的根目录下新建该文件：（后面会说如何指定文件名和存放路径）
    
    ```js
    module.exports = {
      // 入口
      entry: "",
      // 输出
      output: {},
      // 加载器
      module: {
        rules: [],
      },
      // 插件
      plugins: [],
      // 模式
      mode: "",
    };
    ```
    
    > Webpack是基于Node.js运行的，所以默认采用`Common.js`模块化规范。
    >
    > ###### 修改配置文件：（目前的配置等价于之前的打包命令）
    
    ```js
    const path = require('path')  // 用到了path模块
    module.exports = {
    	// 入口文件（主文件），从哪个文件开始打包（必须用相对路径）
      entry: '入口文件的相对路径',
    	// 输出（bundle文件），指定打包后的输出目录（必须用绝对路径）
      output: {
        // 打包后往哪个目录输出。这里必须配置一个绝对路径
        path: path.resolve(__dirname, 'dist'),
    		// 指定main.js主文件打包后的文件名。此时主文件打包后输出为dist/js/main.js
    		filename: 'js/main.js',
    		clean: true, // 打包前将上次打包目录资源清空
      },
    	// 加载器
      module: {
        // rules中配置loader的规则
        rules: []
        // loader的其他配置
      },
    	// 插件
      plugins: [],
    	// 模式
      mode: 'development'
    }
    ```
    
    > - 配置好后，项目根目录下运行：`npx webpack`，此时不用再加其他参数了，因为该目录下有Webpack的默认配置文件了，如果再指定就会覆盖配置文件中的配置。
    > - 将来都通过`webpack.config.js`文件对 `Webpack` 进行配置，让它能够预处理`.css`资源、做语法转换...
    
    ###### 接下来我们分别通过两个模式来对 Webpack 进行配置。先进行开发模式的配置，再完成生产模式的配置。
  
- ### 开发模式的配置

  > 开发模式顾名思义就是我们开发代码时使用的模式。这个模式下我们主要做两件事：
  >
  > 1. 编译代码，使浏览器能够识别运行。主要是对代码进行预处理、打包，使多个静态资源文件都合并为一个JS文件。（开发时我们有样式资源、字体图标、图片资源、html 资源等，Webpack默认都不能处理这些资源，所以我们要配置来Loader来处理这些资源，使之能够在浏览器上运行）
  > 2. 代码质量检查，树立代码规范。（提前检查代码的一些隐患，让代码运行时能更加健壮，还有提前检查代码规范和格式，统一团队编码风格，让代码更优雅美观）

  - #### 处理样式资源：（预处理CSS资源并将其打包到JS中）

    > - Webpack通过Loader（加载器）来**对任何文件进行打包**，可以处理包括JS文件在内的任何静态资源。并且可以使用Node.js来轻松编写自定义的Loader。
    > - 样式资源有`CSS、Less、Sass/Scss、Stylus`样式资源。Webpack本身是不能识别样式资源的，所以我们需要配置加载器（Loader）来帮助Webpack解析和打包样式资源。
    > - 在Webpack官网中几乎可以找到处理任何类型静态资源的Loader。如果官方文档找不到的话，可以再去Github社区中找对应的Loader。官方Loader地址：https://webpack.docschina.org/loaders/
    
    - ##### CSS资源：
    
      > 开发时我们将CSS资源打包到JS代码中，作为JS代码的一部分使用，即`CSS-in-JS`。这种方式虽然性能会有影响，但是它对热更新（HMR）友好，方便我们进行开发。生产模式下我们还是会将CSS单独提取出来作为静态资源。
      >
      > ###### 功能介绍：
      >
      > - `css-loader`：负责将 CSS 文件编译成 Webpack 能识别的模块
      > - `style-loader`：会通过JS动态创建一个 Style 标签到，里面放置 Webpack 中 CSS 模块内容
      >
      > 此时样式就会以 Style 标签的形式在页面上生效。
    
      ###### 用法：
    
      1. 安装`css-loader`、`style-loader`包：`npm i css-loader style-loader -D`
    
      2. 然后在`webpack.config.js`中配置`loader`：
    
         ```js
         module: {
           rules: [
             {
               // 匹配.css结尾的文件
               test: /\.css$/,
               // `use`表示用哪些loader来处理该文件。数组里面Loader的执行顺序是从右到左。css-loader负责将CSS文件预编译成Webpack能识别的CommonJS模块；style-loader会将这些CSS模块（.js文件），通过动态创建<style>标签的方式引入到html中
               use: ['style-loader', 'css-loader'],  // 这里如果写loader:''配置项，则只能用一个loader
             },
           ]
         },
         ```

      3. 这样在主文件`main.js`中，就可以导入`.css`文件了：`import './index.css'`。注意：只有导入了该文件，Webpack才会将该CSS文件进行打包。

         > import导入css文件其实是Webpack提供的功能，它会对代码中导入的CSS文件用指定的Loader去预处理。

      ###### 编写代码进行验证：
    
      1. 首先准备一个`index.css`文件，里面编写样式。
    
         ```css
         .box1 {
           width: 100px;
           height: 100px;
           background-color: pink;
         }
         ```
    
      2. 然后在`main.js`文件中导入该css文件：
    
         ```js
         // 引入 Css 资源，Webpack才会对其打包
         import "./css/index.css";
         console.log('执行了main');
         ```
         
      3. 运行webpack命令对`main.js`进行打包：`npx webpack`，完成后会输出`/dist/js/main.js`。
      
      4. 准备一个html文件，在其中通过`<script>`标签引入打包后的bundle：
      
         ```html
         <!DOCTYPE html>
         <html lang="en">
           <head>
             <meta charset="UTF-8" />
             <meta http-equiv="X-UA-Compatible" content="IE=edge" />
             <meta name="viewport" content="width=device-width, initial-scale=1.0" />
             <title>webpack5</title>
           </head>
           <body>
             <h1>Hello Webpack5</h1>
             <!-- 准备一个使用样式的 DOM 容器 -->
             <div class="box1"></div>
             <!-- 引入打包后的js文件，才能看到效果 -->
             <script src="../dist/js/main.js"></script>
           </body>
         </html>
         ```
      
         如果页面上能看到样式生效了，说明我们配置的loader起作用了。
      
    - ##### Less资源：
    
      > 和处理CSS资源类似，只是要在最右边再加一个`less-loader`，因为要先通过该loader去将`.less`文件编译为CSS：
    
      1. 除了安装处理css的2个loader，还需要再安装`less-loader`：`npm i less-loader -D`
    
      2. `rules`配置项的数组末尾再添加一个`less-loader`：
    
         ```js
         {
         	test: /\.less$/,
         	use: ["style-loader", "css-loader", "less-loader"],
         },
         ```
    
      > 验证步骤和上方类似，只是新建的是`index.less`文件。
    
    - ##### Stylus资源：
    
      > Stylus也是一个CSS预处理器，文件后缀为`.styl`。处理方式和Less类似，只是它用的是`stylus-loader`。
    
    - ##### Sass/Scss资源：
    
      > Sass也是一个CSS预处理器，文件以`.sass`或`.scss`结尾。和之前不同的是，它需要安装两个依赖：`npm i sass-loader sass -D`
      >
      > ###### 功能介绍：
      >
      > - `sass-loader`：负责将 Sass 文件编译成 css 文件
      > - `sass`：`sass-loader` 依赖 `sass` 进行编译
      
      ###### 配置：
      
      ```js
      {
      	test: /\.s[ac]ss$/,
      	use: ["style-loader", "css-loader", "sass-loader"],
      },
      ```
    
  - #### 处理图片、字体、多媒体等资源：

    > 资源模块(asset module)是一种模块类型，它允许我们打包资源文件（字体，图标等）而无需配置额外 loader。

    > 在 webpack 5 之前，通常使用：
    >
    > - [`file-loader`](https://v4.webpack.js.org/loaders/file-loader/) 将文件原封不动输出到 bundle 中。
    >
    > - [`url-loader`](https://v4.webpack.js.org/loaders/url-loader/) 将文件作为 data URI 内联到 bundle 中。
    >
    >   > 将文件作为 **Data URI 内联到 bundle 中**是一种前端构建优化技术，它的核心思想是将外部资源（如图片、字体或小型脚本）直接转换为 Base64 编码的字符串，并嵌入到最终生成的代码文件（bundle）中。
    >   >
    >   > 将图片用Base64转成字符串的优点是：不需要再发送请求了。缺点是：体积会比原来大一些（图片越大大的越多）。所以一般我们综合考虑，会将小于10KB的图片进行Base64转码，减轻服务器的压力。
    >   >
    >   > **几乎所有文件都可以用Base64进行转码**，因为Base64是一种将二进制数据编码为ASCII字符的方法。
    >
    > - [`raw-loader`](https://v4.webpack.js.org/loaders/raw-loader/) 将文件导入为字符串。
    >
    > 这3个loader来处理图片、字体、多媒体等静态资源。
    >
    > 当CSS中通过`url()`、HTML中通过img的src、或JS中通过`import`引入了外部图片时，`file-loader`会将资源原封不动输出。而代码中`import`或`url()`导入的是最终生成文件的 **公共 URL 路径**（如 `"/dist/image.png"`）。
    >
    > 现在Webpack5已经内置了这3个loader，不用再安装了，直接可以用。

    > 资源模块类型(asset module type)，通过添加 4 种新的模块类型，来直接使用上面的3个loader：
    >
    > - `asset/resource` 发送一个单独的文件并导出 URL。之前通过使用 `file-loader` 实现。
    > - `asset/inline` 导出一个资源的 data URI。之前通过使用 `url-loader` 实现。
    > - `asset/source` 导出资源的源代码。之前通过使用 `raw-loader` 实现。
    > - `asset` 在 `asset/inline` 和 `asset/resource` 之间自动选择：小于 8kb 的文件，将会视为 `inline` 模块类型，否则会被视为 `resource` 模块类型。之前Webpack4是通过使用 `url-loader`并配置资源体积限制来实现的。

    - 需要处理字体、音视频等需要原封不动输出的文件，直接配置`type`类型来使用`file-loader`，它会将这些文件原封不动输出到指定目录中：

      ```js
      {
      	test: /\.(ttf|woff2?|map4|map3|avi)$/,
      	type: "asset/resource",  // 表示使用`file-loader`
      	generator: {
        	// 除此之外，还可以指定文件的输出目录
      		filename: "media/[hash:8][ext][query]",
      	},
      },
      ```

      > 默认情况下，`asset/resource` 模块以 `[hash][ext][query]` 文件名发送到输出目录。我们可以通过上方的 `filename` 来指定将这些资源，以指定的名字发送到指定的目录中。
      >
      > 另一种方式是，通过在 webpack 配置中设置 `output.assetModuleFilename` 来修改此模板字符串：
      >
      > ```js
      > output: {
      >      filename: 'main.js',
      >      path: path.resolve(__dirname, 'dist'),
      >      assetModuleFilename: 'images/[hash][ext][query]'
      > },
      > ```

    - 默认情况下，引入图片或字体文件时，会原封不动输出到`dist`目录下。如果想加上`url-loader`对图片进行Base64转码，可以将`type`类型设置为`asset`来完成，它会根据设置的阈值来决定使用`file-loader`或`url-loader`之一（默认阈值是8KB）：

      ```js
      {
        test: /\.(png|jpe?g|gif|webp|svg)$/,
        /*
      		自动选择模式：根据文件大小自动在 asset/resource（生成文件）和 asset/inline（内联为 Data URL，即使用`url-loader`再进行base64转码）之间切换。默认阈值：默认小于 8KB 的文件会内联为 Data URL（类似 url-loader），大于阈值的生成单独文件
         */
        type: "asset",
        parser: {
          dataUrlCondition: {
          	maxSize: 10 * 1024 // 也可以手动设置`url-loader`的阈值为10kb，小于10KB的图片会被转为base64格式
        	}
        },
        // 该配置项用于指定`file-loader`输出图片的路径和文件名
        generator: {
          // 将图片文件输出到 static/imgs 目录中
          // 将图片文件命名 [hash:8][ext][query]
          // [hash:8]: hash值取8位
          // [ext]: 使用之前的文件扩展名
          // [query]: 添加之前的query参数
          filename: "static/imgs/[hash:8][ext][query]",
        },
      },
      ```

      > 如果将`type`类型设置为`asset/inline`，则所有的文件都会作为 data URI 注入到 bundle 中（即转码为Base64）。

  - #### 自动清空上次打包的内容：

    > 目前我们每次重新打包前，都需要将上次打包生成的文件先进行清空，否则会导致新旧版本文件同时存在，引发问题。
    >
    > 但是这样太麻烦了。其实我们只需要在`output`中添加配置：`clean: true`，这样每次打包前会先将path目录进行清空，再输出。（Webpack4必须通过安装插件来完成：`clean-webpack-plugin`）

  - #### 处理JS资源：

    > 你可能会问，JS资源Webpack不是已经能处理了吗，为什么我们还要去配置处理JS资源呢？原因是：
    >
    > Webpack对JS的处理是有限的，只能将开发模式中的模块化语法（Node环境下才能执行模块化代码），编译为不包含模块化的 JS bundle，不能做其他事情了。
    >
    > 我们有时候还希望可以：做代码的格式检查、兼容性处理、性能优化、将TS编译为JS、编译JS的扩展语法JSX、编译Vue的模板语法...，这些都可以在Webpack中进行配置。

    > ###### 对JS、JSX代码进行格式检查一般都用`ESLint`，对JS代码做兼容性处理一般用Babel。
    >
    > 我们先用ESLint检查代码格式，无误后再用Babel做兼容性处理：

    - **ESLint**：可组装的 JavaScript 和 JSX 检查工具（Facebook公司的）。我们用Eslint关键是写ESLint的配置文件，里面写上各种rules规则，将来运行ESLint进行语法检查时就可以根据写的规则对JS/TS代码进行检查了。

      - ###### ESLint的配置文件：（以`.eslintrc.js`为例）

        > ESLint 支持几种格式的配置文件，ESLint 将按照以下优先顺序以此使用其一：（都在项目根目录下）
        >
        > - `.eslintrc.js`
        > - `.eslintrc.cjs`
        > - `.eslintrc.yaml`
        > - `.eslintrc.yml`
        > - `.eslintrc.json`
        > - `package.json` 中通过 `eslintConfig` 配置项来配置。这种方式不需要创建新的文件。
        >
        > 注意：ESLint9版本之后将使用`eslint.config.js/mjs/cjs/ts/mts/cts`作为新的配置文件，旧的配置文件将被弃用。

        ```js
        module.exports = {
          // 解析选项
          parserOptions: {
            ecmaVersion: 6,  // ES的语法版本
            sourceType: "module",  // 用的哪个模块化
            ecmaFeatures: {  // 用的ES的其他特性
            	jsx: true  // 是否检查JSX语法
            }
          },
        
        	// 具体的检查规则
            /*
              "off" 或 0 - 关闭规则
              "warn" 或 1 - 开启规则，使用警告级别的错误，不会导致程序退出
              "error" 或 2 - 开启规则，使用错误级别的错误，当被触发的时候程序会退出，打包失败
             */
          rules: {
            semi: "error",  // 禁止使用分号
            'array-callback-return': 1,  // 强制数组方法的回调函数中有 return 语句，否则警告
            'default-case': [  // 如果需要再配置详细的规则，需要用数组的第2个参数，值为配置项
            	'warn',  // 要求 switch 语句中有 default 分支，否则警告
            	{ commentPattern: '^no default$' }  // 允许在最后注释 no default, 就不会有警告了
            ],
            eqeqeq: ['warn','smart'],
          },
        
          // 继承其他规则
        	extends: ["eslint:recommended"], // 我们自己配置的rules如果放在extends下面的话，就会覆盖掉继承过来的默认规则
            /* 有以下较为有名的规则：
              Eslint 官方的规则：eslint:recommended
              Vue Cli 官方的规则：plugin:vue/essential
              React Cli 官方的规则：react-app
            */
        }
        ```

      - ###### ESLint的使用：

        > ESLint在Webpack4中是一个Loader，在Webpack5中是作为插件Plugin使用。步骤如下：

        1. 下载包：`npm i eslint-webpack-plugin@4 eslint@8 -D`（后者是要用的eslint工具，前者是将eslint集成到Webpack的插件）

        2. 在项目根目录下编写`.eslintrc.js`文件，并将eslint配置到`webpack.config.js`中：

           ```js
           const ESLintWebpackPlugin = require('eslint-webpack-plugin');
           
           module.exports = {
             // ...
             plugins: [
               new ESLintWebpackPlugin({
               	// 指定检查的目录为src，其他配置项参考webpack官网
               	context: path.resolve(__dirname, "src"),
               }),
             ],
             // ...
           }
           ```

           > 此时执行`npx webpack`打包时，打包前会先用eslint检查语法，检查通过后才会进行打包输出。检查如果报错，程序则会终止，不会输出任何文件。

      - ###### 如果想忽略某个文件，可以在项目根目录下新建eslint的忽略文件`.eslintignore`：

        ```yaml
        # 不检查根目录下的dist目录
        dist
        ```

      - ###### VSCode Eslint 插件：

        > 打开 VSCode，下载 Eslint 插件，即可不用编译就能看到错误，可以提前解决。
        >
        > 但是该插件会对项目中所有文件进行 Eslint 检查，我们只需要检查 src 下面的文件。所以可以使用 Eslint 忽略文件来解决到处报错的问题。
        >
        > 在项目根目录新建下面文件：`.eslintignore`
        >
        > ```tex
        > # 忽略dist目录下所有文件
        > dist
        > ```

    - **Babel**：它是一个JS编译器。主要用于将ES6语法编写的代码转换为向后兼容的JS代码，以便能够运行在旧版本的浏览器中。

      - ###### Babel的配置文件：（以`babel.config.js`为例）

        > Babel 支持几种格式的配置文件，Babel 将按照以下优先顺序以此使用其一：（都在项目根目录下）
        >
        > - `babel.config.json/js/cjs/mjs`
        > - `.babelrc.json/js/cjs/mjs`、`.babelrc`
        > - `package.json` 中通过 `babel` 配置项来配置。这种方式不需要创建新的文件。
      
        ```js
        module.exports = {
          // 预设。就是添加一组Babel的插件，扩展Babel的功能，想让Babel转换哪些语法就要添加哪些插件进去
          presets: ["@babel/preset-env"],
            /* 常用的预设插件有：
              @babel/preset-env: 用来编译最新的JS语法
              @babel/preset-react：用来编译React的JSX语法
              @babel/preset-typescript：用来编译TS语法
            */
        }
        ```

      - ###### Babel的使用：

        1. 下载包：`npm i babel-loader @babel/core @babel/preset-env -D`（分别是babel的loader、babel、babel需要的预设插件）
      
        2. 项目根目录下编写Babel的配置文件`babel.config.js`，并将Babel的Loader配置到 Webpack 配置文件中：
      
           ```js
           module: {
             rules: [
               ...
               {
               	test: /\.js$/,
               	exclude: /node_modules/,  // node_modules目录不编译
               	loader: 'babel-loader',
                 // 也可以不创建Babel的配置文件，在这里写配置
                 // options: {
                 //   presets: ["@babel/preset-env"],  // 智能预设
                 // },
               },
               ...
             ]
           }
           ```
           
           > 然后运行指令`npx webpack`，查看打包后的bundle文件会发现：箭头函数等ES6的语法已经被转换了。

  - #### 处理HTML资源：

    > 我们现在还需要在`index.html`中通过`<script>`标签来引入dist目录下生成的bundle。
    >
    > 其实开发中我们通常这样做：将`index.html`作为静态资源一并打包到dist目录中，并且打包生成的这个`index.html`不需要我们通过`<script>`手动引入生成的bundle文件，引入的操作通过Webpack的插件来自动完成。最终生成的整个bundle目录直接放到服务器上就可以运行。
    
    1. 下载包：`npm i html-webpack-plugin -D`
    
    2. `webpack.config.js`中：
    
       ```js
       const HtmlWebpackPlugin = require("html-webpack-plugin")
       
       module.exports = {
         // ...
         plugins: [
         	new HtmlWebpackPlugin({
             // 以 public/index.html 为模板创建文件。生成的新html内容和源文件一致，且会自动引入打包生成的所有bundle
             template: path.resolve(__dirname, "public/index.html"),
         	}),
         ],
         // ...
       }
       ```
    
       > 然后运行指令`npx webpack`，此时dist目录还会输出一个`index.html`文件。**并且使用了该插件后，如果设置的是开发模式：`mode: 'development'`，还会对HTML进行压缩。**
    
  - #### 开发服务器&自动化：
  
    > 开发环境下，目前我们每次修改完源代码，还需要重新去执行`npx webpack`重新打包才能看到最新的效果，很麻烦。
    >
    > 我们可以启动一个开发服务器，将构建的bundle放到服务器目录中，每次改完代码保存后让Webpack自动重新打包，这样整个流程完全自动化了，开发效率大大提高。
  
    1. 下载包：`npm i webpack-dev-server -D`（`webpack-dev-server`会自动开启一个服务器来监视`src/`下的代码，当代码变化后会自动重新打包）
  
    2. `webpack.config.js`中：
    
       ```js
       module.exports = {
         // ...
         // 配置开发服务器（这是一个新的配置项）
         devServer: {
         	host: "localhost", // 启动服务器ip
         	port: "3000", // 启动服务器端口号
         	open: true, // 服务器启动时是否打开默认浏览器
           
           // 以下两个配置项其实不用配置，因为默认就会找public目录，并且会进行gzip压缩
           // 使用gzip压缩public目录当中的所有内容并提供一个本地服务(serve)
           static: {
             directory: path.join(__dirname, 'public'),  // 这样index.html中就能访问到public下的静态资源了
           },
           compress: true,
         },
         // ...
       }
       ```
       
       > 此时就不是执行`npx webpack`了，而是用`npx webpack serve`启动Webpack的开发服务器。
       >
       > 并且当你使用开发服务器时，所有代码都会在内存中编译打包，并不会输出到 dist 目录下（开发服务器中的静态资源都在内存中）。
       >
       > 因为开发时我们只关心代码能运行，有效果即可，至于代码被编译成什么样子，我们并不需要知道。
  
- ### 生产模式的配置

  > 生产模式是开发完成代码后，我们需要将bundle部署到服务器上。这个模式下我们主要对代码进行优化、压缩，让其运行性能更好。优化主要从两个角度出发：
  >
  > 1. 优化代码运行性能
  > 2. 优化代码打包体积

  - #### 生产模式准备：

    1. 我们准备2个配置文件，分别存放不同环境下的Webpack配置。在根目录下新建`config`目录，里面存放：`webpack.dev.js`和`webpack.prod.js`。

    2. 开发环境下的配置文件：`webpack.dev.js`

       ```js
       const path = require("path")
       const ESLintWebpackPlugin = require("eslint-webpack-plugin")
       const HtmlWebpackPlugin = require("html-webpack-plugin")
       
       module.exports = {
         // 由于`webpack`命令是在项目根目录下执行的，所以相对路径不用变。绝对路径得改
         entry: "./src/main.js",
         output: {
           path: undefined, // 开发模式没有输出，不需要指定输出目录
           filename: "static/js/main.js",  // 虽然不写入磁盘，但保持配置的完整性
           // clean: true, // 开发模式没有输出，不需要清空输出结果
         },
         module: {
           rules: [
             {
               test: /\.css$/,
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
                   maxSize: 10 * 1024,
                 },
               },
               generator: {
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
               exclude: /node_modules/,
               loader: "babel-loader",
             },
           ],
         },
         plugins: [
           new ESLintWebpackPlugin({
             context: path.resolve(__dirname, "../src"),
           }),
           new HtmlWebpackPlugin({
             template: path.resolve(__dirname, "../public/index.html"),
           }),
         ],
         // 开发时用开发服务器启动项目
         devServer: {
           host: "localhost", // 启动服务器域名
           port: "3000", // 启动服务器端口号
           open: true, // 是否自动打开浏览器
           static: {
             directory: path.join(__dirname, 'public'),  // 这样index.html中就能访问到public下的静态资源了
           },
           compress: true,
         },
         mode: "development",
       }
       ```
       
       > 运行开发模式的命令：`npx webpack serve --config ./config/webpack.dev.js`
       
    3. 生产环境下的配置文件：`webpack.prod.js`（生产环境下还有其他配置，后面会补充）
    
       ```js
       const path = require("path")
       const ESLintWebpackPlugin = require("eslint-webpack-plugin")
       const HtmlWebpackPlugin = require("html-webpack-plugin")
       
       module.exports = {
         entry: "./src/main.js",
         output: {
           path: path.resolve(__dirname, "../dist"), // 生产模式需要输出
           filename: "static/js/main.js",
           clean: true, // 自动清理上次打包的资源
         },
         module: {
           rules: [
             {
               test: /\.css$/,
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
                   maxSize: 10 * 1024,
                 },
               },
               generator: {
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
               exclude: /node_modules/,
               loader: "babel-loader",
             },
           ],
         },
         plugins: [
           new ESLintWebpackPlugin({
             context: path.resolve(__dirname, "../src"),
           }),
           new HtmlWebpackPlugin({
             template: path.resolve(__dirname, "../public/index.html"),
           }),
         ],
         // 生产时不使用开发服务器
         // devServer: {
         //   host: "localhost", // 启动服务器域名
         //   port: "3000", // 启动服务器端口号
         //   open: true, // 是否自动打开浏览器
         //   static: {
         //     directory: path.join(__dirname, 'public'),  // 这样index.html中就能访问到public下的静态资源了
         //   },
         //   compress: true,
         // },
         mode: "production", // 生产模式下`html-webpack-plugin`会压缩html代码，js代码Webpack默认就会进行压缩
       }
       ```
    
       > 运行生产模式的指令：`npx webpack --config ./config/webpack.prod.js`
    
    4. 配置运行指令：
    
       > 为了方便运行不同模式的指令，我们将指令定义在`package.json`中`scripts`配置项里面：
    
       ```json
       "scripts": {
         "start": "npm run dev",
         "dev": "webpack serve --config ./config/webpack.dev.js",
         "build": "webpack --config ./config/webpack.prod.js"
       }
       ```
       
       > ###### 注意：`scripts`中的命令不用以`npx`开头，默认就会去`./node_modules/bin`目录中找。
       >
       > 以后启动项目：
       >
       > - 开发模式：`npm start` 或 `npm run dev`
       > - 生产模式：`npm run build`
    
  - #### 生产模式下CSS资源的处理：

    1. ##### 将CSS提取为单独文件：
  
       > 开发模式下的CSS文件被打包到JS文件中有助于在代码更改时立即查看到效果（CSS-in-JS）。但是在生产环境下这种方式就不太好了。当JS文件加载时，会动态创建`<style>`标签来生成样式，这种方式网络差时会出现闪屏现象，并且没有link标签加载CSS的性能好，代码也无法复用。因此生产模式下对CSS资源的处理是另一种方式。
  
       1. 下载包：`npm i mini-css-extract-plugin -D`
  
       2. 在`webpack.prod.js`中进行配置：
  
          ```js
          const MiniCssExtractPlugin = require("mini-css-extract-plugin")
          
          module.exports = {
            // 需要将原来所有用到"style-loader"的地方替换成：MiniCssExtractPlugin.loader
            module: {
              rules: [
                {
                	test: /\.css$/,
                	use: [MiniCssExtractPlugin.loader, "css-loader"],
                },
                // ...其他地方的"style-loader"也要替换
              ]
            }
            // ...
            plugins: [
              // 它会将所有css提取合并为一个单独的css文件
              new MiniCssExtractPlugin({
              	// 定义输出的css文件名
              	filename: "static/css/main.css",
              }),
            ],
            // ...
          }
          ```
          
          > 默认会将所有的css代码合并为一个css文件。最终会被`html-webpack-plugin`插件导入到`index.html`中。
  
    2. ##### 为CSS做兼容性处理：（开发和生产模式都需要）
  
       > CSS样式也是有兼容性的，所以我们在Webpack中加上`PostCSS`来处理下CSS的兼容性。步骤：
       >
       > **注意**：PostCSS是一个用于处理CSS的工具链的统称，它不仅可以处理CSS兼容性，还可以解决类名冲突等一系列问题。几乎任何对CSS的相关处理你都可以通过PostCSS来做。
  
       1. 下载包：`npm i postcss postcss-preset-env postcss-loader -D`
  
          > 分别是postcss、postcss预设的一个功能集插件、还有postcss用于集成在webpack中的loader。
    
       2. 在`webpack.prod.js`中进行配置：
    
          ```js
          module.exports = {
            // 在所有的"css-loader"和"less-loader"中间加上该对象
            module: {
              rules: [
                {
                  test: /\.less$/,
                  use: [
                    MiniCssExtractPlugin.loader,
                    "css-loader",
                    {
                      loader: "postcss-loader",
                      options: {
                        postcssOptions: {
                          plugins: [
                            "postcss-preset-env", // 该预设插件，能解决大多数样式兼容性问题
                          ],
                        },
                      },
                    },
                    "less-loader",
                  ],
                },
                // ...用到css-loader的前面都需要加上postcss-loader
              ]
            }
            // ...
          }
          ```
  
       3. （可选）控制兼容性的处理程度：
    
          > 我们可以在`package.json`中添加`browserslist`来控制CSS的兼容性做到什么程度。（以上配置已经可以处理大部分兼容性问题了）
    
          ```json
          {
            // ...
            "browserslist": ["ie >= 8"] // 设置兼容ie8以上的浏览器
          }
          ```
  
          > 想要知道更多的 `browserslist` 配置，查看[browserslist 文档open in new window](https://github.com/browserslist/browserslist)
          >
          > 以上为了测试兼容性所以设置兼容浏览器 ie8 以上。实际开发中我们一般不考虑像ie8这样低版本的浏览器了，所以我们可以这样设置：
          >
          > ```json
          > {
          >     // ...
          >     "browserslist": ["last 2 version", "> 1%", "not dead"]
          > }
          > ```
          >
          > 表示市面上所有浏览器只兼容其最近2个版本，且只覆盖99%的浏览器，冷门的不管，且不管没人维护的、已经凉凉浏览器了（交集）。
    
       4. （可选）合并配置：
    
          > 我们发现做兼容性配置时有大量的重复代码，可以这样做，在`webpack.prod.js`中加一个函数：（得是JS类型的配置文件才可以）
    
          ```js
          // 定义处理样式的函数，返回（Loaders）数组
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
            ].filter(Boolean) //  过滤数组的空值undefined
          }
          ```
    
          > css-loader、less-loader中这样写：
    
          ```js
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
              ]
          }
          ```
    
    3. ##### 压缩CSS：
    
       1. 下载包：`npm i css-minimizer-webpack-plugin -D`
    
       2. 在`webpack.prod.js`中进行配置该插件：
    
          ```js
          const CssMinimizerPlugin = require("css-minimizer-webpack-plugin")
          
          module.exports = {
            // ...
            plugins: [
              // 配置CSS压缩插件
              new CssMinimizerPlugin(),
            ],
            // ...
          }
          ```
          
          > 或者：
          >
          > ```js
          > module.exports = {
          >     optimization: {
          >        // 告知 webpack 使用 TerserPlugin 或其它在 optimization.minimizer定义的插件压缩 bundle，默认为true
          >        minimize: true,
          >        minimizer: [
          >          // css压缩也可以写到optimization.minimizer里面，效果一样的
          >          new CssMinimizerPlugin(),
          >        ]
          >     },
          > };
          > ```

------

