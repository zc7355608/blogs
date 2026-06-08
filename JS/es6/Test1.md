# ES6

- ## ECMAScript 6 简介

  ECMAScript 6.0（以下简称 ES6）是 JS 语言的下一代标准，已经在 2015 年 6 月正式发布了。它的目标，是使得 JS 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。

  - #### ECMAScript 和 JS 的关系

    一个常见的问题是，ECMAScript 和 JS 到底是什么关系？

    要讲清楚这个问题，需要回顾历史。1996 年 11 月，JS 的创造者 Netscape 公司，决定将 JS 提交给标准化组织 ECMA，希望这种语言能够成为国际标准。次年，ECMA 发布 262 号标准文件（ECMA-262）的第一版，规定了浏览器脚本语言的标准，并将这种语言称为 ECMAScript，这个版本就是 1.0 版。

    该标准从一开始就是针对 JS 语言制定的，但是之所以不叫 JS，有两个原因。一是商标，Java 是 Sun 公司的商标，根据授权协议，只有 Netscape 公司可以合法地使用 JS 这个名字，且 JS 本身也已经被 Netscape 公司注册为商标。二是想体现这门语言的制定者是 ECMA，不是 Netscape，这样有利于保证这门语言的开放性和中立性。

    因此，ECMAScript 和 JS 的关系是，前者是后者的规格，后者是前者的一种实现（另外的 ECMAScript 方言还有 JScript 和 ActionScript）。日常场合，这两个词是可以互换的。

  - #### ES6 与 ECMAScript 2015 的关系

    ECMAScript 2015（简称 ES2015）这个词，也是经常可以看到的。它与 ES6 是什么关系呢？

    2011 年，ECMAScript 5.1 版发布后，就开始制定 6.0 版了。因此，ES6 这个词的原意，就是指 JS 语言的下一个版本。

    但是，因为这个版本引入的语法功能太多，而且制定过程当中，还有很多组织和个人不断提交新功能。事情很快就变得清楚了，不可能在一个版本里面包括所有将要引入的功能。常规的做法是先发布 6.0 版，过一段时间再发 6.1 版，然后是 6.2 版、6.3 版等等。

    但是，标准的制定者不想这样做。他们想让标准的升级成为常规流程：任何人在任何时候，都可以向标准委员会提交新语法的提案，然后标准委员会每个月开一次会，评估这些提案是否可以接受，需要哪些改进。如果经过多次会议以后，一个提案足够成熟了，就可以正式进入标准了。这就是说，标准的版本升级成为了一个不断滚动的流程，每个月都会有变动。

    标准委员会最终决定，标准在每年的 6 月份正式发布一次，作为当年的正式版本。接下来的时间，就在这个版本的基础上做改动，直到下一年的 6 月份，草案就自然变成了新一年的版本。这样一来，就不需要以前的版本号了，只要用年份标记就可以了。

    ES6 的第一个版本，就这样在 2015 年 6 月发布了，正式名称就是《ECMAScript 2015 标准》（简称 ES2015）。2016 年 6 月，小幅修订的《ECMAScript 2016 标准》（简称 ES2016）如期发布，这个版本可以看作是 ES6.1 版，因为两者的差异非常小（只新增了数组实例的`includes`方法和指数运算符），基本上是同一个标准。根据计划，2017 年 6 月发布 ES2017 标准。

    因此，ES6 既是一个历史名词，也是一个泛指，含义是 5.1 版以后的 JS 的下一代标准，涵盖了 ES2015、ES2016、ES2017 等等，而 ES2015 则是正式名称，特指该年发布的正式版本的语言标准。本书中提到 ES6 的地方，一般是指 ES2015 标准，但有时也是泛指“下一代 JS 语言”。

  - #### 语法提案的批准流程

    任何人都可以向标准委员会（又称 TC39 委员会）提案，要求修改语言标准。

    一种新的语法从提案到变成正式标准，需要经历五个阶段。每个阶段的变动都需要由 TC39 委员会批准。

    - Stage 0 - Strawman（展示阶段）
    - Stage 1 - Proposal（征求意见阶段）
    - Stage 2 - Draft（草案阶段）
    - Stage 3 - Candidate（候选人阶段）
    - Stage 4 - Finished（定案阶段）

    一个提案只要能进入 Stage 2，就差不多肯定会包括在以后的正式标准里面。ECMAScript 当前的所有提案，可以在 TC39 的官方网站[GitHub.com/tc39/ecma262](https://github.com/tc39/ecma262)查看。

    本书的写作目标之一，是跟踪 ECMAScript 语言的最新进展，介绍 5.1 版本以后所有的新语法。对于那些明确或很有希望，将要列入标准的新语法，都将予以介绍。

  - #### ECMAScript 的历史

    ES6 从开始制定到最后发布，整整用了 15 年。

    前面提到，ECMAScript 1.0 是 1997 年发布的，接下来的两年，连续发布了 ECMAScript 2.0（1998 年 6 月）和 ECMAScript 3.0（1999 年 12 月）。3.0 版是一个巨大的成功，在业界得到广泛支持，成为通行标准，奠定了 JS 语言的基本语法，以后的版本完全继承。直到今天，初学者一开始学习 JS，其实就是在学 3.0 版的语法。

    2000 年，ECMAScript 4.0 开始酝酿。这个版本最后没有通过，但是它的大部分内容被 ES6 继承了。因此，ES6 制定的起点其实是 2000 年。

    为什么 ES4 没有通过呢？因为这个版本太激进了，对 ES3 做了彻底升级，导致标准委员会的一些成员不愿意接受。ECMA 的第 39 号技术专家委员会（Technical Committee 39，简称 TC39）负责制订 ECMAScript 标准，成员包括 Microsoft、Mozilla、Google 等大公司。

    2007 年 10 月，ECMAScript 4.0 版草案发布，本来预计次年 8 月发布正式版本。但是，各方对于是否通过这个标准，发生了严重分歧。以 Yahoo、Microsoft、Google 为首的大公司，反对 JS 的大幅升级，主张小幅改动；以 JS 创造者 Brendan Eich 为首的 Mozilla 公司，则坚持当前的草案。

    2008 年 7 月，由于对于下一个版本应该包括哪些功能，各方分歧太大，争论过于激烈，ECMA 开会决定，中止 ECMAScript 4.0 的开发，将其中涉及现有功能改善的一小部分，发布为 ECMAScript 3.1，而将其他激进的设想扩大范围，放入以后的版本，由于会议的气氛，该版本的项目代号起名为 Harmony（和谐）。会后不久，ECMAScript 3.1 就改名为 ECMAScript 5。

    2009 年 12 月，ECMAScript 5.0 版正式发布。Harmony 项目则一分为二，一些较为可行的设想定名为 JS.next 继续开发，后来演变成 ECMAScript 6；一些不是很成熟的设想，则被视为 JS.next.next，在更远的将来再考虑推出。TC39 委员会的总体考虑是，ES5 与 ES3 基本保持兼容，较大的语法修正和新功能加入，将由 JS.next 完成。当时，JS.next 指的是 ES6，第六版发布以后，就指 ES7。TC39 的判断是，ES5 会在 2013 年的年中成为 JS 开发的主流标准，并在此后五年中一直保持这个位置。

    2011 年 6 月，ECMAScript 5.1 版发布，并且成为 ISO 国际标准（ISO/IEC 16262:2011）。

    2013 年 3 月，ECMAScript 6 草案冻结，不再添加新功能。新的功能设想将被放到 ECMAScript 7。

    2013 年 12 月，ECMAScript 6 草案发布。然后是 12 个月的讨论期，听取各方反馈。

    2015 年 6 月，ECMAScript 6 正式通过，成为国际标准。从 2000 年算起，这时已经过去了 15 年。

    目前，各大浏览器对 ES6 的支持可以查看https://compat-table.github.io/compat-table/es6/。

    Node.js 是 JS 的服务器运行环境（runtime）。它对 ES6 的支持度更高。除了那些默认打开的功能，还有一些语法功能已经实现了，但是默认没有打开。使用下面的命令，可以查看 Node.js 默认没有打开的实验性语法。

    ```shell
    // Linux & Mac
    node --v8-options | grep harmony
    // Windows
    node --v8-options | findstr harmony
    ```

  - #### Babel

    [Babel](https://babeljs.io/) 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在老版本的浏览器执行。这意味着，你可以用 ES6 的方式编写程序，又不用担心现有环境是否支持。下面是一个例子。

    ```js
    // 转码前
    input.map(item => item + 1);
    
    // 转码后
    input.map(function (item) {
      return item + 1;
    });
    ```

    上面的原始代码用了箭头函数，Babel 将其转为普通函数，就能在不支持箭头函数的 JS 环境执行了。

    下面的命令在项目目录中，安装 Babel。

    ```shell
    npm install --save-dev @babel/core
    ```

    ##### 配置文件`.babelrc`：

    Babel 的配置文件是`.babelrc`，存放在项目的根目录下。使用 Babel 的第一步，就是配置这个文件。该文件用来设置转码规则和插件。其中`presets`字段设定转码规则，官方提供以下的规则集，你可以根据需要安装。

    ```shell
    # 最新转码规则
    npm install --save-dev @babel/preset-env
    
    # react 转码规则
    npm install --save-dev @babel/preset-react
    ```

    然后，将这些规则加入`.babelrc`。

    ```js
    {
      "presets": [
        "@babel/env",
        "@babel/preset-react"
      ],
      "plugins": []
    }
    ```

    注意：以下所有 Babel 工具和模块的使用，都必须先写好`.babelrc`。

    ##### 使用Babel进行转码：

    Babel 提供命令行工具`@babel/cli`，它提供了一些命令行脚本，可以指定文件进行转码（没有它的话，就要通过`Webpack`构建工具集成、或者自己编写js脚本来使用Babel了）。它的安装命令如下。

    ```shell
    npm install --save-dev @babel/cli
    ```

    使用：

    ```shell
    # 转码结果输出到标准输出
    npx babel example.js
    
    # 转码结果写入一个文件
    # --out-file 或 -o 参数指定输出文件
    npx babel example.js --out-file compiled.js
    # 或者
    npx babel example.js -o compiled.js
    
    # 整个目录转码
    # --out-dir 或 -d 参数指定输出目录
    npx babel src --out-dir lib
    # 或者
    npx babel src -d lib
    
    # -s 参数生成source map文件
    npx babel src -d lib -s
    ```

    ##### 使用 @babel/node 在运行时进行转换：（不推荐生产环境）

    `@babel/node` 提供了 `babel-node` 命令，可以在运行时实时转换 ES6+ 代码。如果你的脚本中使用了 Node.js 不支持的 ES6+ 特性，可以使用 `babel-node` 来直接运行这些脚本。

    `@babel/node`模块的`babel-node`命令，提供一个支持 ES6 的 REPL 环境。它支持 Node 的 REPL 环境的所有功能，而且可以直接运行 ES6 代码。使用步骤：

    1. 首先，安装这个模块：`$ npm install --save-dev @babel/node`
    2. 然后，执行`babel-node`就进入 REPL 环境：`npx babel-node`

    `babel-node`命令也可以直接运行一个 ES6 脚本：`npx babel-node es6.js`

    ##### @babel/register 模块：（不推荐生产环境）

    `@babel/register`模块改写`require`命令，为它加上一个钩子（中间件）。此后，每当使用`require`加载`.js`、`.jsx`、`.es`和`.es6`后缀名的文件，就会先用 Babel 进行转码。

    ```shell
    npm install --save-dev @babel/register
    ```

    使用时，必须首先加载`@babel/register`。

    ```js
    // index.js
    require('@babel/register');
    require('./es6.js');
    ```

    然后，就不需要手动对`index.js`转码了。而是在加载文件时实时转译代码（运行时）。

    需要注意的是，`@babel/register`只会对`require`命令加载的文件转码，而不会对当前文件转码。另外，由于它是实时转码，所以只适合在开发环境使用。

    ##### polyfill：

    Babel 默认只转换新的 JS 句法（syntax），而不转换新的 API，比如`Iterator`、`Generator`、`Set`、`Map`、`Proxy`、`Reflect`、`Symbol`、`Promise`等全局对象，以及一些定义在全局对象上的方法（比如`Object.assign`）都不会转码。

    举例来说，ES6 在`Array`对象上新增了`Array.from`方法。Babel 就不会转码这个方法。如果想让这个方法运行，可以使用`core-js`和`regenerator-runtime`（后者提供`generator`函数的转码），为当前环境提供一个垫片（polyfill）。

    安装命令如下：

    ```shell
    npm install --save-dev core-js regenerator-runtime
    ```

    然后，在脚本头部，加入如下两行代码。

    ```js
    import 'core-js';
    import 'regenerator-runtime/runtime';
    // 或者
    require('core-js');
    require('regenerator-runtime/runtime');
    ```

    Babel 默认不转码的 API 非常多，详细清单可以查看`babel-plugin-transform-runtime`模块的[definitions.js](https://github.com/babel/babel/blob/master/packages/babel-plugin-transform-runtime/src/runtime-corejs3-definitions.js)文件。

    ##### 浏览器环境：（不推荐生产环境）

    Babel 也可以用于浏览器环境，使用`@babel/standalone`模块提供的浏览器版本，将其插入网页。

    ```html
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script type="text/babel"> // 该type值浏览器不认识因此不会做任何处理，是babel解析并处理该script中的代码
    // Your ES6 code
    </script>
    ```

    注意，这种网页实时将 ES6 代码转为 ES5，对性能会有影响。生产环境需要加载已经转码完成的脚本。

- ## let 和 const 命令

  `var`声明的变量有这几个问题：**变量提升、没有块级作用域、同一个作用域可以重复声明、会挂载到全局对象**。

  ES6 新增了`let、const`命令用来声明变量，它们声明的变量没有这些问题。

  1. `let`、`const`声明的变量必须先声明后使用，否则报错（没有变量提升）。

     > 代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

  2. `let`、`const`声明的变量只在当前当前代码块内有效（块级作用域）

     > 内层作用域可以定义外层作用域的同名变量（覆盖），这点不同与Java。

  3. `let`、`const`的变量在同一个代码块中不可重复声明。

  另外，由于`let`变量具有块级作用域，因此在`for`循环中，小括号`()`和大括号`{}`各自都是单独的作用域，后者是前者的子作用域。

  ```js
  for (let i = 0; i < 3; i++) {
    let i = 'abc';
    console.log(i);
  }
  // abc
  // abc
  // abc
  ```

  上面代码正确运行，输出了 3 次`abc`。这表明函数内部的变量`i`与循环变量`i`不在同一个作用域，有各自单独的作用域（同一个作用域不可使用 `let` 重复声明同一个变量）。

  还有，ES6之后，函数的形参行为也类似于`let`声明的变量。

  ##### 暂时性死区：

  只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

  ```js
  var tmp = 123;
  
  if (true) {
    tmp = 'abc'; // ReferenceError
    let tmp;
  }
  ```

  上面代码中，存在全局变量`tmp`，但是块级作用域内`let`又声明了一个局部变量`tmp`，导致后者绑定这个块级作用域，所以在`let`声明变量前，对`tmp`赋值会报错。

  ES6 明确规定，**如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错**。

  总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

  ```js
  if (true) {
    // TDZ开始
    tmp = 'abc'; // ReferenceError
    console.log(tmp); // ReferenceError
  
    let tmp; // TDZ结束
    console.log(tmp); // undefined
  
    tmp = 123;
    console.log(tmp); // 123
  }
  ```

  上面代码中，在`let`命令声明变量`tmp`之前，都属于变量`tmp`的“死区”。

  “暂时性死区”也意味着**`typeof`不再是一个百分之百安全的操作**。

  ```js
  typeof x; // ReferenceError
  let x;
  ```

  上面代码中，变量`x`使用`let`命令声明，所以在声明之前，都属于`x`的“死区”，只要用到该变量就会报错。因此，`typeof`运行时就会抛出一个`ReferenceError`。

  作为比较，如果一个变量根本没有被声明，使用`typeof`反而不会报错。

  ```js
  typeof undeclared_variable // "undefined"
  ```

  上面代码中，`undeclared_variable`是一个不存在的变量名，结果返回`undefined`。所以，在没有`let`之前，`typeof`运算符是百分之百安全的，永远不会报错。现在这一点不成立了。这样的设计是为了让大家养成良好的编程习惯，变量一定要在声明之后使用，否则就报错。

  ES6 规定暂时性死区、`let/const`不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。这样的错误在 ES5 是很常见的，现在有了这种规定，避免此类错误就很容易了。

  总之，暂时性死区的本质就是：只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

  ##### 块级作用域与函数声明：

  函数能不能在块级作用域之中声明？这是一个相当令人混淆的问题。

  ES5 规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明。

  ```js
  // 情况一
  if (true) {
    function f() {}
  }
  
  // 情况二
  try {
    function f() {}
  } catch(e) {
    // ...
  }
  ```

  上面两种函数声明，根据 ES5 的规定都是非法的。

  但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在块级作用域之中声明函数，因此上面两种情况实际都能运行，不会报错。

  ES6 引入了块级作用域，明确允许在块级作用域之中声明函数。ES6 规定，块级作用域之中，函数声明语句的行为类似于`let`，在块级作用域之外不可引用。

  ```js
  function f() { console.log('I am outside!'); }
  
  (function () {
    if (false) {
      // 重复声明一次函数f
      function f() { console.log('I am inside!'); }
    }
  
    f();
  }());
  ```

  上面代码在 ES5 中运行，会得到“I am inside!”，因为在`if`内声明的函数`f`会被提升到函数头部，实际运行的代码如下。

  ```js
  // ES5 环境
  function f() { console.log('I am outside!'); }
  
  (function () {
    function f() { console.log('I am inside!'); }
    if (false) {
    }
    f();
  }());
  ```

  ES6 就完全不一样了，理论上会得到“I am outside!”。因为块级作用域内声明的函数类似于`let`，对作用域之外没有影响。但是，如果你真的在 ES6 浏览器中运行一下上面的代码，是会报错的，这是为什么呢？

  ```js
  // 浏览器的 ES6 环境
  function f() { console.log('I am outside!'); }
  
  (function () {
    if (false) {
      // 重复声明一次函数f
      function f() { console.log('I am inside!'); }
    }
  
    f();
  }());
  // Uncaught TypeError: f is not a function
  ```

  上面的代码在 ES6 浏览器中，都会报错。

  原来，如果改变了块级作用域内声明的函数的处理规则，显然会对老代码产生很大影响。为了减轻因此产生的不兼容问题，ES6 在[附录 B](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-block-level-function-declarations-web-legacy-compatibility-semantics)里面规定，浏览器的实现可以不遵守上面的规定，有自己的[行为方式](http://stackoverflow.com/questions/31419897/what-are-the-precise-semantics-of-block-level-functions-in-es6)。

  - 允许在块级作用域内声明函数。
  - 函数声明类似于`var`，即会提升到全局作用域或函数作用域的头部。
  - 同时，函数声明还会提升到所在的块级作用域的头部。

  注意，上面三条规则只对 ES6 的浏览器实现有效，其他环境的实现不用遵守，还是将块级作用域的函数声明当作`let`处理。

  根据这三条规则，**浏览器的 ES6 环境中，块级作用域内声明的函数，行为类似于`var`声明的变量**。上面的例子实际运行的代码如下。

  ```js
  // 浏览器的 ES6 环境
  function f() { console.log('I am outside!'); }
  (function () {
    var f = undefined;
    if (false) {
      function f() { console.log('I am inside!'); }
    }
  
    f();
  }());
  // Uncaught TypeError: f is not a function
  ```

  考虑到环境导致的行为差异太大，应该**避免在块级作用域内声明函数**。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

  ```js
  // 块级作用域内部的函数声明语句，建议不要使用
  {
    let a = 'secret';
    function f() {
      return a;
    }
  }
  
  // 块级作用域内部，优先使用函数表达式
  {
    let a = 'secret';
    let f = function () {
      return a;
    };
  }
  ```

  另外，ES6 的块级**作用域必须有大括号**，如果没有大括号，JS 引擎就认为不存在块级作用域，并不会在某些情况下隐式地创建一个`{}`。

  ```js
  // 第一种写法，报错
  if (true) let x = 1;
  
  // 第二种写法，不报错
  if (true) {
    let x = 1;
  }
  ```

  上面代码中，第一种写法没有大括号，所以不存在块级作用域。而 `let` 和 `const` 语法规范要求它们必须出现在一个“块”（即 `{}`）中，所以报错。

  函数声明也是如此，严格模式下，函数只能声明在当前作用域的顶层。

  ```js
  // 不报错
  'use strict';
  if (true) {
    function f() {}
  }
  
  // 报错
  'use strict';
  if (true)
    function f() {}
  ```

  ##### const 声明的常量：

  常量使用`const`声明，声明后值就不可改了，所以声明时必须初始化。常量名字一般用大写（规范）。

  > 通常我们声明变量先用`const`，后面发现要改报错了，再改用`let`。**var不要用！**
  >

  `const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

  如果真的想将对象冻结，应该使用`Object.freeze()`方法，将对象、以及深层次的每个层级的属性都进行冻结。

  除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

  ```js
  var constantize = (obj) => {
    Object.freeze(obj);
    Object.keys(obj).forEach((key, i) => {
      if (typeof obj[key] === 'object') {
        constantize( obj[key] );
      }
    });
  };
  ```

  ##### ES6 声明变量的六种方法：

  ES5 只有两种声明变量的方法：`var`命令和`function`命令。ES6 除了添加`let`和`const`命令，后面章节还会提到，另外两种声明变量的方法：`import`命令（等同于`const`声明）和`class`命令。所以，ES6 一共有 6 种声明变量的方法。

  ##### 顶层对象的属性：

  顶层对象，在浏览器环境指的是`window`对象，在 Node 指的是`global`对象。ES6 之前，顶层对象的属性与`var`声明全局变量是等价的。

  ```js
  window.a = 1;
  a // 1
  
  var a = 2;
  window.a // 2
  ```

  上面代码中，顶层对象的属性赋值与全局变量的赋值，是同一件事。

  顶层对象的属性与全局变量挂钩，被认为是 JS 语言最大的设计败笔之一。这样的设计带来了几个很大的问题：

  1. 首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；
  2. 其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；
  3. 最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。

  另一方面，`window`对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。

  ES6 为了改变这一点，一方面规定，为了保持兼容性，**`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性**；另一方面规定，**`let`、`const`、`class`声明的全局变量，不属于顶层对象的属性**。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。

  ##### globalThis 对象：

  JS 语言存在一个顶层对象，它提供全局环境（即全局作用域），所有代码都是在这个环境中运行。但是，顶层对象在各种实现里面是不统一的：

  1. 浏览器里面，顶层对象是`window`，但 Node 和 Web Worker 没有`window`。
  2. 浏览器和 Web Worker 里面，`self`也指向顶层对象，但是 Node 没有`self`。
  3. Node 里面，顶层对象是`global`，但其他环境都不支持。

  同一段代码，为了能够在各种环境都能取到顶层对象，我们一般是用`this`关键字来做，但是有局限性：

  1. 全局环境中，`this`会返回顶层对象。但是，Node.js 模块中`this`返回的是当前模块，ES6 模块中`this`返回的是`undefined`。
  2. 函数里面的`this`，如果函数不是作为对象的方法运行，而是单纯作为函数运行，`this`会指向顶层对象。但是，严格模式下，这时`this`会返回`undefined`。
  3. 不管是严格模式，还是普通模式，`new Function('return this')()`，总是会返回全局对象。但是，如果浏览器用了 CSP（Content Security Policy，内容安全策略），那么`eval`、`new Function`这些方法都可能无法使用。

  综上所述，很难找到一种方法，可以在所有情况下，都取到顶层对象。下面是两种勉强可以使用的方法：

  ```js
  // 方法一
  (typeof window !== 'undefined'
    ? window
    : (typeof process === 'object' &&
       typeof require === 'function' &&
       typeof global === 'object')
       ? global
       : this);
  
  // 方法二
  var getGlobal = function () {
    if (typeof self !== 'undefined') { return self; }
    if (typeof window !== 'undefined') { return window; }
    if (typeof global !== 'undefined') { return global; }
    throw new Error('unable to locate global object');
  };
  ```

  ES2020 在语言标准的层面，引入`globalThis`作为顶层对象。也就是说，任何环境下，`globalThis`都是存在的，都可以从它拿到顶层对象，指向全局环境下的`this`。

  垫片库[`global-this`](https://github.com/ungap/global-this)模拟了这个提案，可以在所有环境拿到`globalThis`。

- ## 变量的解构赋值

  ES6 允许按照一定模式，从**对象**、**数组或其他可迭代的结构**中提取值，对变量进行赋值，这被称为解构（Destructuring）。
  
  解构分为两种：一种是可迭代结构（部署了`[Symbol.iterator]`接口，比如数组）的解构赋值，使用中括号`[]`语法；另一种是属性名匹配的解构赋值（对象），使用大括号`{}`语法。
  
  > **Tips**：
  >
  > 可迭代的结构其实就是，有**`[Symbol.iterator]()`**这个方法的对象。数组是可迭代的结构、已经实现了这个方法。而普通对象`{}`虽然不是可迭代的结构，但对象的解构是基于属性名（key） 的匹配，而不是顺序遍历（迭代）的。
  
  - #### 数组（可迭代结构）的解构赋值
  
    以前，为变量赋值，只能直接指定值。
  
    ```js
    let a = 1;
    let b = 2;
    let c = 3;
    ```
  
    ES6 允许写成下面这样。
  
    ```js
    let [a, b, c] = [1, 2, 3];
    ```
  
    上面代码表示，可以从数组中提取值，按照对应位置，对变量赋值。
  
    本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。下面是一些使用嵌套数组进行解构的例子。
  
    ```js
    let [foo, [[bar], baz]] = [1, [[2], 3]];
    foo // 1
    bar // 2
    baz // 3
    
    let [ , , third] = ["foo", "bar", "baz"];
    third // "baz"
    
    let [x, , y] = [1, 2, 3];
    x // 1
    y // 3
    
    // rest参数还可以和数组解构结合使用
    let [head, ...tail] = [1, 2, 3, 4];
    head // 1
    tail // [2, 3, 4]
    
    let [x, y, ...z] = ['a'];
    x // "a"
    y // undefined
    z // []
    ```
  
    注意，rest参数可以和数组解构结合使用。它将右边数组中所有未解构的值收集起来，放到了一个数组中。
  
    用数组解构和rest参数完成数组的复制：
  
    ```js
    const a1 = ["foo", "bar", "baz"];
    const [...a2] = a1;
    ```
  
    如果**解构不成功，变量的值就等于`undefined`**。
  
    ```js
    let [foo] = [];
    let [bar, foo] = [1];
    ```
  
    以上两种情况都属于解构不成功，`foo`的值都会等于`undefined`。
  
    另一种情况是不完全解构，即等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。
  
    ```js
    let [x, y] = [1, 2, 3];
    x // 1
    y // 2
    
    let [a, [b], d] = [1, [2, 3], 4];
    a // 1
    b // 2
    d // 4
    ```
  
    上面两个例子，都属于不完全解构，但是可以成功。
  
    如果等号的右边不是数组（或者严格地说，不是可遍历的结构，参见《Iterator》一章），那么将会报错。
  
    ```js
    // 报错
    let [foo] = 1;
    let [foo] = false;
    let [foo] = NaN;
    let [foo] = undefined;
    let [foo] = null;
    let [foo] = {};
    ```
  
    上面的语句都会报错，因为等号右边的值，要么转为对象以后不具备 `Iterator` 接口（前五个表达式），要么本身就不具备 `Iterator` 接口（最后一个表达式）。
  
    对于 Set 结构，也可以使用数组的解构赋值。因为 Set 部署了 `Iterator` 接口。
  
    ```js
    let [x, y, z] = new Set(['a', 'b', 'c']);
    x // "a"
    ```
  
    事实上，**只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值**。
  
    ```js
    function* fibs() {
      let a = 0;
      let b = 1;
      while (true) {
        yield a;
        [a, b] = [b, a + b];
      }
    }
    
    let [first, second, third, fourth, fifth, sixth] = fibs();
    sixth // 5
    ```
  
    上面代码中，`fibs`是一个 Generator 函数（参见《Generator 函数》一章），原生具有 Iterator 接口。解构赋值会依次从这个接口获取值。
  
    ##### 默认值：
  
    解构赋值允许指定默认值。
  
    ```js
    let [foo = true] = [];
    foo // true
    
    let [x, y = 'b'] = ['a']; // x='a', y='b'
    let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
    ```
  
    注意，ES6 内部**使用严格相等运算符（`===`），判断一个位置是否有值**。所以，**只有当一个数组成员严格等于`undefined`，默认值才会生效**。
  
    ```js
    let [x = 1] = [undefined];
    x // 1
    
    let [x = 1] = [null];
    x // null
    ```
  
    上面代码中，如果一个数组成员是`null`，默认值就不会生效，因为`null`不严格等于`undefined`。
  
    如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会执行该表达式。
  
    ```js
    function f() {
      console.log('aaa');
    }
    
    let [x = f()] = [1];
    ```
  
    上面代码中，因为`x`能取到值，所以函数`f`根本不会执行。上面的代码其实等价于下面的代码。
  
    ```js
    let x;
    if ([1][0] === undefined) {
      x = f();
    } else {
      x = [1][0];
    }
    ```
  
    默认值可以引用解构赋值的其他变量，但该变量必须已经声明。
  
    ```js
    let [x = 1, y = x] = [];     // x=1; y=1
    let [x = 1, y = x] = [2];    // x=2; y=2
    let [x = 1, y = x] = [1, 2]; // x=1; y=2
    let [x = y, y = 1] = [];     // ReferenceError: y is not defined
    ```
  
    上面最后一个表达式之所以会报错，是因为`x`用`y`做默认值时，`y`还没有声明。
  
  - #### 对象的解构赋值（基于属性名匹配）
  
    解构不仅可以用于数组，还可以用于对象。
  
    ```js
    let { foo, bar } = { foo: 'aaa', bar: 'bbb' };
    foo // "aaa"
    bar // "bbb"
    ```
  
    对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。
  
    ```js
    let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
    foo // "aaa"
    bar // "bbb"
    
    let { baz } = { foo: 'aaa', bar: 'bbb' };
    baz // undefined
    ```
  
    上面代码的第一个例子，等号左边的两个变量的次序，与等号右边两个同名属性的次序不一致，但是对取值完全没有影响。第二个例子的变量没有对应的同名属性，导致取不到值，最后等于`undefined`。
  
    如果解构失败，变量的值等于`undefined`。
  
    ```js
    let { foo } = {bar: 'baz'};
    foo // undefined
    ```
  
    上面代码中，等号右边的对象没有`foo`属性，所以变量`foo`取不到值，所以等于`undefined`。
  
    对象的解构赋值，可以很方便地将现有对象的方法，赋值到某个变量。
  
    ```js
    // 例一
    let { log, sin, cos } = Math;
    
    // 例二
    const { log } = console;
    log('hello') // hello
    ```
  
    上面代码的例一将`Math`对象的对数、正弦、余弦三个方法，赋值到对应的变量上，使用起来就会方便很多。例二将`console.log`赋值到`log`变量。
  
    如果变量名与属性名不一致，可以用冒号`:`为解构的变量进行重命名。
  
    ```js
    let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
    baz // "aaa"
    
    let obj = { first: 'hello', last: 'world' };
    let { first: f, last: l } = obj;
    f // 'hello'
    l // 'world'
    ```
  
    这实际上说明，对象的解构赋值是下面形式的简写（参见《对象的扩展》一章）。
  
    ```js
    let { foo: foo, bar: bar } = { foo: 'aaa', bar: 'bbb' };
    ```
  
    也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。
  
    ```js
    let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
    baz // "aaa"
    foo // error: foo is not defined
    ```
  
    上面代码中，`foo`是匹配的模式，`baz`才是变量。真正被赋值的是变量`baz`，而不是模式`foo`。
  
    与数组一样，对象解构也可以用于嵌套结构的对象。
  
    ```js
    let obj = {
      p: [
        'Hello',
        { y: 'World' }
      ]
    };
    
    let { p: [x, { y }] } = obj;
    x // "Hello"
    y // "World"
    ```
  
    注意，这时`p`是模式，不是变量，因此不会被赋值。如果`p`也要作为变量赋值，可以写成下面这样。
  
    ```js
    let obj = {
      p: [
        'Hello',
        { y: 'World' }
      ]
    };
    
    let { p, p: [x, { y }] } = obj;
    x // "Hello"
    y // "World"
    p // ["Hello", {y: "World"}]
    ```
  
    下面是另一个例子。
  
    ```js
    const node = {
      loc: {
        start: {
          line: 1,
          column: 5
        }
      }
    };
    
    let { loc, loc: { start }, loc: { start: { line }} } = node;
    line // 1
    loc  // Object {start: Object}
    start // Object {line: 1, column: 5}
    ```
  
    上面代码有三次解构赋值，分别是对`loc`、`start`、`line`三个属性的解构赋值。注意，最后一次对`line`属性的解构赋值之中，只有`line`是变量，`loc`和`start`都是模式，不是变量。
  
    下面是嵌套赋值的例子。
  
    ```js
    let obj = {};
    let arr = [];
    
    ({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });
    
    obj // {prop:123}
    arr // [true]
    ```
  
    如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。
  
    ```js
    // 报错
    let { foo: { bar } } = { baz: 'baz' };
    ```
  
    上面代码中，等号左边对象的`foo`属性，对应一个子对象。该子对象的`bar`属性，解构时会报错。原因很简单，因为`foo`这时等于`undefined`，再取子属性就会报错。
  
    注意，**对象的解构赋值可以取到继承的属性**。
  
    ```js
    const obj1 = {};
    const obj2 = { foo: 'bar' };
    Object.setPrototypeOf(obj1, obj2);
    
    const { foo } = obj1;
    foo // "bar"
    ```
  
    上面代码中，对象`obj1`的原型对象是`obj2`。`foo`属性不是`obj1`自身的属性，而是继承自`obj2`的属性，解构赋值可以取到这个属性。
  
    ##### 默认值：
  
    对象的解构也可以指定默认值。
  
    ```js
    var {x = 3} = {};
    x // 3
    
    var {x, y = 5} = {x: 1};
    x // 1
    y // 5
    
    var {x: y = 3} = {};
    y // 3
    
    var {x: y = 3} = {x: 5};
    y // 5
    
    var { message: msg = 'Something went wrong' } = {};
    msg // "Something went wrong"
    ```
  
    对象解构的默认值生效的条件是，对象的属性值要严格等于`undefined`。
  
    ```js
    var {x = 3} = {x: undefined};
    x // 3
    
    var {x = 3} = {x: null};
    x // null
    ```
  
    上面代码中，属性`x`等于`null`，因为`null`与`undefined`不严格相等，所以是个有效的赋值，导致默认值`3`不会生效。
  
    ##### rest参数和对象解构：（ES2018）
  
    rest参数也可以和对象解构结合使用。它将右边对象自身所有的、可遍历的、未解构的键值对收集起来，放到了一个对象中。
  
    ```js
    let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
    x // 1
    y // 2
    z // { a: 3, b: 4 }
    ```
  
    rest参数必须是在解构的最后一个，否则会报错。
  
    ```js
    let { ...x, y, z } = someObject; // 句法错误
    let { x, ...y, ...z } = someObject; // 句法错误
    ```
  
    并且 ES6 规定，变量声明语句之中，如果使用解构赋值，扩展运算符后面必须是一个变量名，而不能是一个解构赋值表达式。
  
    ```js
    let { x, ...{ y, z } } = o;
    // SyntaxError: ... must be followed by an identifier in declaration contexts
    ```
  
    ##### 注意：
  
    1. 不同于数组解构，对象解构时，如果等号右边不是一个对象，此时会进行自动类型转换。这是一个**严格的 ToObject 转换**：除了 `null`/`undefined` 会报错外，任何值都会被转换为一个对象，然后再进行解构操作。
  
    2. 解构赋值允许等号左边的模式之中，不放置任何变量名。因此，可以写出非常古怪的赋值表达式。
  
       ```js
       ({} = [true, false]);
       ({} = 'abc');
       ({} = []);
       ```
  
       上面的表达式虽然毫无意义，但是语法是合法的，可以执行。
  
    3. 由于数组本质是特殊的对象，因此**可以对数组进行对象的解构**。
  
       ```js
       let arr = [1, 2, 3];
       let {0 : first, [arr.length - 1] : last} = arr;
       first // 1
       last // 3
       ```
  
       上面代码对数组进行对象解构。数组`arr`的`0`键对应的值是`1`，`[arr.length - 1]`就是`2`键，对应的值是`3`。方括号这种写法，属于“属性名表达式”（参见《对象的扩展》一章）。
  
  - #### 字符串的解构赋值
  
    字符串也可以解构赋值。这是因为此时字符串被自动装箱成了`String`对象，它是一个伪数组，并且实现了 `Iterator` 接口。
  
    ```js
    const [a, b, c, d, e] = 'hello';
    a // "h"
    b // "e"
    c // "l"
    d // "l"
    e // "o"
    ```
  
    伪数组有一个`length`属性，因此还可以对这个属性解构赋值。
  
    ```js
    let {length : len} = 'hello';
    len // 5
    ```
  
  - #### 数值和布尔值的解构赋值
  
    解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。
  
    ```js
    let {toString: s} = 123;
    s === Number.prototype.toString // true
    
    let {toString: s} = true;
    s === Boolean.prototype.toString // true
    ```
  
    上面代码中，数值和布尔值的包装对象都有`toString`属性，因此变量`s`都能取到值。
  
    **对象解构的规则是，只要等号右边的值不是对象，就先将其转为对**象。由于`undefined`和`null`无法转为对象，所以对它们进行对象解构，都会报错。
  
    ```js
    let { prop: x } = undefined; // TypeError
    let { prop: y } = null; // TypeError
    ```
  
  - #### 函数参数的解构赋值
  
    函数的参数也可以使用解构赋值。
  
    ```js
    function add([x, y]){
      return x + y;
    }
    
    add([1, 2]); // 3
    ```
  
    上面代码中，函数`add`的参数表面上是一个数组，但在传入参数的那一刻，数组参数就被解构成变量`x`和`y`。对于函数内部的代码来说，它们能感受到的参数就是`x`和`y`。
  
    下面是另一个例子。
  
    ```js
    [[1, 2], [3, 4]].map(([a, b]) => a + b);
    // [ 3, 7 ]
    ```
  
    函数参数的解构也可以使用默认值。
  
    ```js
    function move({x = 0, y = 0} = {}) {
      return [x, y];
    }
    
    move({x: 3, y: 8}); // [3, 8]
    move({x: 3}); // [3, 0]
    move({}); // [0, 0]
    move(); // [0, 0]
    ```
  
    上面代码中，函数`move`的参数是一个对象，通过对这个对象进行解构，得到变量`x`和`y`的值。如果解构失败，`x`和`y`等于默认值。注意：当调用`move`函数没有传参时，这里给了一个`{}`作为默认参数。
  
    注意，下面的写法会得到不一样的结果。
  
    ```js
    function move({x, y} = { x: 0, y: 0 }) {
      return [x, y];
    }
    
    move({x: 3, y: 8}); // [3, 8]
    move({x: 3}); // [3, undefined]
    move({}); // [undefined, undefined]
    move(); // [0, 0]
    ```
  
    上面代码是为函数`move`的参数指定默认值，而不是为变量`x`和`y`指定默认值，所以会得到与前一种写法不同的结果。
  
    同样，`undefined`会触发函数参数的默认值。
  
    ```js
    [1, undefined, 3].map((x = 'yes') => x);
    // [ 1, 'yes', 3 ]
    ```
  
  - #### ~~圆括号问题（了解）~~
  
    解构赋值虽然很方便，但是解析起来并不容易。对于编译器来说，一个式子到底是模式，还是表达式，没有办法从一开始就知道，必须解析到（或解析不到）等号才能知道。
  
    由此带来的问题是，如果模式中出现圆括号怎么处理。ES6 的规则是，**只要有可能导致解构的歧义，就不得使用圆括号**。
  
    但是，这条规则实际上不那么容易辨别，处理起来相当麻烦。因此，建议只要有可能，就**不要在模式中放置圆括号**。
  
    下面的例子作为了解即可。
  
    - ##### 不能使用圆括号的情况：
  
      以下三种解构赋值不得使用圆括号。
  
      （1）变量声明语句
  
      ```js
      // 全部报错
      let [(a)] = [1];
      
      let {x: (c)} = {};
      let ({x: c}) = {};
      let {(x: c)} = {};
      let {(x): c} = {};
      
      let { o: ({ p: p }) } = { o: { p: 2 } };
      ```
  
      上面 6 个语句都会报错，因为它们都是变量声明语句，模式不能使用圆括号。
  
      （2）函数参数
  
      函数参数也属于变量声明，因此不能带有圆括号。
  
      ```js
      // 报错
      function f([(z)]) { return z; }
      // 报错
      function f([z,(x)]) { return x; }
      ```
  
      （3）赋值语句的模式
  
      ```js
      // 全部报错
      ({ p: a }) = { p: 42 };
      ([a]) = [5];
      ```
  
      上面代码将整个模式放在圆括号之中，导致报错。
  
      ```js
      // 报错
      [({ p: a }), { x: c }] = [{}, {}];
      ```
  
      上面代码将一部分模式放在圆括号之中，导致报错。
  
    - ##### 可以使用圆括号的情况：
  
      可以使用圆括号的情况只有一种：赋值语句的非模式部分，可以使用圆括号。
  
      ```js
      [(b)] = [3]; // 正确
      ({ p: (d) } = {}); // 正确
      [(parseInt.prop)] = [3]; // 正确
      ```
  
      上面三行语句都可以正确执行，因为首先它们都是赋值语句，而不是声明语句；其次它们的圆括号都不属于模式的一部分。第一行语句中，模式是取数组的第一个成员，跟圆括号无关；第二行语句中，模式是`p`，而不是`d`；第三行语句与第一行语句的性质一致。
  

------

