- ### TypeScript 的注释指令

  > TS 的编译器默认不会处理和编译 JS 文件，并且 JS 文件中也不能出现 TS 的类型语法。但是如果就是想让编译器处理 JS 文件怎么办？可以用 TS 的注释指令。
  >
  > 所谓“注释指令”其实就是，通过在 JS 文件中写特殊的 JS 注释，向 TS 编译器发出的命令。而对于 JS 来说这只是注释。
  
  - ##### 单行注释指令：
  
    > 以下这些是单行注释指令，只对当前文件有效，且都需要写在文件的顶部。需要注意的是：
    >
    > 1. 单行注释指令必须写在文件中的第一个非空、非注释行。它不能有任何代码或注释在其前面。
    > 2. 如果文件的第一行是**shebang**（例如`#!/usr/bin/env node`），那么`// @ts-check`可以放在第二行。
    >
    > **TIP：**
    >
    > **shebang**（也称为 hashbang 或 sha-bang）是一种特殊的注释行，用于在 Unix/Linux 系统中的脚本文件的第一行。它的格式为 `#!`，后面跟着解释器的路径。Shebang 的作用是指定用于执行该脚本的解释器程序。（注意：`shebang`必须是文件的第一行，且不能有任何空行或注释在它之前）
  
    - `@ts-nocheck`：`// @ts-nocheck`告诉 TS 编译器不对当前脚本进行类型检查。它可以用于 TS 脚本，也可以用于 JS 脚本。
  
    - `@ts-check`：如果一个 JS 脚本顶部添加了`// @ts-check`，那么 TS 编译器将对该脚本进行类型检查，不论是否启用了`checkJs`编译选项。
  
    - `@ts-ignore`：`// @ts-ignore`用于告诉编译器，不对下一行代码进行类型检查。它可用于 TS 和 JS 脚本。
  
    - `@ts-expect-error`：`// @ts-expect-error`主要用在测试用例，当下一行有类型错误时，它会压制 TS 的报错信息（即不显示报错信息），把错误留给代码自己处理。如果下一行没有类型错误，`// @ts-expect-error`则会显示一行提示：`Unused '@ts-expect-error' directive.`，表示`@ts-expect-error`没有用到。
  
  - ##### JsDoc注释：
  
    > TS 直接处理 JS 文件时，如果无法推断出类型，会使用 JS 脚本里面的 JSDoc 注释。
    >
    > 使用 JSDoc 时要注意：**JSDoc 注释必须与它描述的代码处于相邻的位置，并且注释在上，代码在下**。下面是 JSDoc 的一个简单例子。
    >
    > ```js
    > /**
    >  * @param {string} somebody
    >  */
    > function sayHello(somebody) {
    >     console.log('Hello ' + somebody);
    > }
    > ```
    >
    > TS 编译器支持大部分的 JSDoc 声明，下面介绍其中的一些：
  
    - `@typedef`：`@typedef`命令创建自定义类型，等同于 TS 里面的类型别名。

      ```js
      /**
       * @typedef {(number | string)} NumberLike
       */
      ```
  
      它等同于：`type NumberLike = string | number;`
  
    - `@type`：`@type`用于定义变量的类型。
  
      ```js
      /**
       * @type {NumberLike}
       */
      let a;
      ```
  
      在`@type`命令中可以使用由`@typedef`命令创建的类型。
  
    - `@param`：`@param`命令用于定义函数参数的类型。如果是可选参数，需要将参数名放在方括号`[]`里面。
  
      ```js
      /**
       * @param {string} [x]
       */
      function foo(x) {}
      ```
  
      方括号里面，还可以指定参数默认值。
  
      ```js
      /**
       * @param {string} [x="bar"]
       */
      function foo(x) {}
      ```
  
    - `@return/@returns`：`@return`和`@returns`的作用一样，都是指定函数返回值的类型。（推荐使用后者，更规范）
  
      ```js
      /**
       * @returns {number}
       */
      function bar() { return 0; }
      ```
  
    - `@extends`和**类型修饰符**：`@extends`命令用于定义继承的基类。
  
      ```js
      /**
       * @extends {Base}
       */
      class Derived extends Base {}
      ```
  
      `@public`、`@protected`、`@private`分别指定类的公开成员、保护成员和私有成员。`@readonly`指定只读成员。
  
      ```js
      class Base {
          /**
           * @public
           * @readonly
           */
      	x = 0;
      
          /**
           *  @protected
           */
          y = 0;
      }
      ```
  
- ### 关于 tsconfig.json 文件

  > - `tsconfig.json`是 TS 项目的配置文件，放在项目的根目录。反过来说，如果一个目录里面有`tsconfig.json`，TS 就认为这是项目的根目录。
  > - 如果项目源码是 JS，但是想用 TS 处理，那么配置文件的名字是`jsconfig.json`，它跟`tsconfig`的写法是一样的。
  > - `tsconfig.json`文件主要供`tsc`编译器使用，它的命令行参数`--project`或`-p`可以指定`tsconfig.json`的位置（目录或文件皆可，文件名随意）：`tsc -p ./dir`
  > - 如果不指定配置文件的位置，`tsc`就会在当前目录下搜索`tsconfig.json`文件，如果不存在，就到上一级目录搜索，直到找到为止。
  > - `tsconfig.json`文件的格式，是一个 JSON 对象，最简单的情况可以只放置一个空对象`{}`。（该文件能写注释）
  > - `tsconfig.json`文件可以不必手写，使用 tsc 命令的`--init`参数自动生成。生成的`tsconfig.json`文件，里面会有一些默认配置。（你也可以使用别人预先写好的 tsconfig.json 文件，npm 的`@tsconfig`名称空间下面有很多模块，都是写好的`tsconfig.json`样本，比如 `@tsconfig/deno`和`@tsconfig/node16`这俩包）
  > - `tsconfig.json`的一级属性并不多，只有很少几个，但是`compilerOptions`属性有很多二级属性。下面先逐一介绍一级属性，然后再介绍`compilerOptions`的二级属性，按照首字母排序。

  - ##### extends：

    > `tsconfig.json`可以继承另一个`tsconfig.json`文件的配置。如果一个项目有多个配置，可以把共同的配置写成`tsconfig.base.json`，其他的配置文件继承该文件，这样便于维护和修改。
    >
    > `extends`属性用来指定所要继承的配置文件。它可以是本地文件。
    >
    > ```ts
    > {
    >     "extends": "../tsconfig.base.json"
    > }
    > ```
    >
    > 如果`extends`属性指定的路径不是以`./`或`../`开头，那么编译器将在`node_modules`目录下查找指定的配置文件。
    >
    > `extends`指定的`tsconfig.json`会先加载，然后加载当前的`tsconfig.json`。如果两者有重名的属性，后者会覆盖前者。

  - ##### include：

    > `include`属性指定所要编译的文件列表，既支持逐一列出文件，也支持通配符。文件位置相对于当前配置文件而定。
    >
    > ```ts
    > {
    >     "include": ["src/**/*", "tests/**/*"]
    > }
    > ```
    >
    > `include`属性支持三种通配符。
    >
    > - `?`：指代单个字符
    > - `*`：指代任意字符，不含路径分隔符
    > - `**`：指定任意目录层级。
    >
    > 如果不指定文件后缀名，默认包括`.ts`、`.tsx`和`.d.ts`文件。如果打开了`allowJs`，那么还包括`.js`和`.jsx`。

  - ##### exclude：

    > `exclude`属性是一个数组，必须与`include`属性一起使用，用来从编译列表中去除指定的文件。它也支持使用与`include`属性相同的通配符。
    >
    > ```ts
    > {
    >     "include": ["**/*"],
    >     "exclude": ["**/*.spec.ts"]
    > }
    > ```

  - ##### files：

    > `files`属性指定编译的文件列表（`.ts`或`.d.ts`文件都行）。如果其中有一个文件不存在，就会报错。
    >
    > 它是一个数组，排在前面的文件先编译。
    >
    > ```ts
    > {
    >        "files": ["a.ts", "b.ts"]
    > }
    > ```
    >
    > 该属性必须逐一列出文件，不支持文件匹配。如果文件较多，建议使用`include`和`exclude`属性。

  - ##### references：

    > `references`值是一个对象数组，适合一个大项目由许多小项目构成的情况，用来设置需要引用的底层项目。
    >
    > ```ts
    > {
    >     "references": [
    >         { "path": "../pkg1" },
    >         { "path": "../pkg2/tsconfig.json" }
    >     ]
    > }
    > ```
    >
    > `references`数组成员对象的`path`属性，既可以是含有文件`tsconfig.json`的目录，也可以直接是该文件。
    >
    > 与此同时，引用的底层项目的`tsconfig.json`必须启用`composite`属性。
    >
    > ```ts
    > {
    >     "compilerOptions": {
    >     	"composite": true
    >     }
    > }
    > ```

  - **compilerOptions：**`compilerOptions`属性用来定制编译行为。这个属性可以省略，这时编译器将使用默认设置。它下面有很多的二级属性。

    - allowJs：`allowJs`允许 TS 项目加载 JS 脚本。编译时，也会将 JS 文件，一起拷贝到输出目录。

      ```ts
      {
          "compilerOptions": {
          	"allowJs": true
          }
      }
      ```

    - **alwaysStrict：**`alwaysStrict`确保每个TS脚本以 ECMAScript 严格模式进行解析，因此脚本头部不用再写`"use strict"`了。它的值是一个布尔值，默认为`true`。

    - **allowSyntheticDefaultImports：**它是`tsconfig.json`中的一个编译选项，用于控制 TS 如何处理默认导入的兼容性问题。值为`true`时允许`import`命令默认加载没有`default`输出的模块，TS 编译器会自动为你创建一个合成的默认导出对象，使得这种代码能够正常工作：`import React from "react";`，而不是`import * as React from "react";`。

      > 在 `strict` 模式下，默认情况下 `allowSyntheticDefaultImports` 是禁用的。如果你需要在严格模式下使用此功能，必须显式启用它。

    - **allowUnreachableCode：**

      > `allowUnreachableCode`设置是否允许存在不可能执行到的代码。它的值有三种可能。
      >
      > - `undefined`： 默认值，编辑器显示警告。
      > - `true`：忽略不可能执行到的代码。
      > - `false`：编译器报错。

    - **allowUnusedLabels：**

      > `allowUnusedLabels`设置是否允许存在没有用到的代码标签（label）。它的值有三种可能。
      >
      > - `undefined`： 默认值，编辑器显示警告。
      > - `true`：忽略没有用到的代码标签。
      > - `false`：编译器报错。

    - **baseUrl：**

      > `baseUrl`的值为字符串，指定 TS 项目中，（没有任何前缀的）模块路径的基准目录。
      >
      > 由于默认是以 tsconfig.json 的位置作为基准目录，所以一般情况不需要使用该属性。
      >
      > ```ts
      > {
      >        "compilerOptions": {
      >        	"baseUrl": "./"
      >        }
      > }
      > ```
      >
      > 上面示例中，`baseUrl`为当前目录`./`。那么当遇到下面的语句，TS 将以`./`为起点，寻找`hello/world.ts`。
      >
      > ```ts
      > import { helloWorld } from "hello/world";
      > ```

    - **checkJs：**

      > `checkJS`设置对 JS 文件同样进行类型检查。打开这个属性，也会自动打开`allowJs`。它等同于在 JS 脚本的头部添加`// @ts-check`命令。
      >
      > ```ts
      > {
      >     "compilerOptions":{
      >     	"checkJs": true
      >     }
      > }
      > ```

    - **composite：**

      > `composite`打开某些设置，使得 TS 项目可以进行增量构建，往往跟`incremental`属性配合使用。

    - **declaration：**

      > `declaration`设置编译时是否为每个脚本生成类型声明文件`.d.ts`。
      >
      > ```ts
      > {
      >     "compilerOptions": {
      >     	"declaration": true
      >     }
      > }
      > ```

    - **declarationDir：**

      > `declarationDir`设置生成的`.d.ts`文件所在的目录。
      >
      > ```ts
      > {
      >     "compilerOptions": {
      >         "declaration": true,
      >         "declarationDir": "./types"
      >     }
      > }
      > ```

    - **declarationMap：**

      > `declarationMap`设置生成`.d.ts`类型声明文件的同时，还会生成对应的 Source Map 文件。
      >
      > ```ts
      > {
      >     "compilerOptions": {
      >         "declaration": true,
      >         "declarationMap": true
      >     }
      > }
      > ```

    - **emitBOM：**

      > `emitBOM`设置是否在编译结果的文件头添加字节顺序标志 BOM，默认值是`false`。

    - **emitDeclarationOnly：**

      > `emitDeclarationOnly`设置编译后只生成`.d.ts`文件，不生成`.js`文件。

    - **esModuleInterop：**

      > `esModuleInterop`修复了一些 CommonJS 和 ES6 模块之间的兼容性问题。
      >
      > 如果`module`属性为`node16`或`nodenext`，则`esModuleInterop`默认为`true`，其他情况默认为`false`。
      >
      > 打开这个属性，使用`import`命令加载 CommonJS 模块时，TS 会严格检查兼容性问题是否存在。
      >
      > ```ts
      > import * as moment from 'moment'
      > moment();  // 报错
      > ```
      >
      > 上面示例中，根据 ES6 规范，`import * as moment`里面的`moment`是一个对象，不能当作函数调用，所以第二行报错了。
      >
      > 解决方法就是改写上面的语句，改成加载默认接口。
      >
      > ```ts
      > import moment from 'moment'
      > moment(); // 不报错
      > ```
      >
      > 打开`esModuleInterop`以后，如果将上面的代码编译成 CommonJS 模块格式，就会加入一些辅助函数，保证编译后的代码行为正确。
      >
      > 注意，打开`esModuleInterop`，将自动打开`allowSyntheticDefaultImports`。

    - **exactOptionalPropertyTypes：**

      > `exactOptionalPropertyTypes`设置可选属性不能赋值为`undefined`。
      >
      > ```ts
      > // 打开 exactOptionalPropertyTypes
      > interface MyObj {
      > 	foo?: 'A' | 'B';
      > }
      > 
      > let obj:MyObj = { foo: 'A' };
      > 
      > obj.foo = undefined; // 报错
      > ```
      >
      > 上面示例中，`foo`是可选属性，打开`exactOptionalPropertyTypes`以后，该属性就不能显式赋值为`undefined`。

    - **forceConsistentCasingInFileNames：**

      > `forceConsistentCasingInFileNames`设置文件名是否为大小写敏感，默认为`true`。

    - **incremental：**

      > `incremental`让 TS 项目构建时产生文件`tsbuildinfo`，从而完成增量构建。

    - **inlineSourceMap：**

      > `inlineSourceMap`设置将 SourceMap 文件写入编译后的 JS 文件中，否则会单独生成一个`.js.map`文件。

    - **inlineSources：**

      > `inlineSources`设置将原始的`.ts`代码嵌入编译后的 JS 中。
      >
      > 它要求`sourceMap`或`inlineSourceMap`至少打开一个。

    - **isolatedModules：**

      > `isolatedModules`设置如果当前 TS 脚本作为单个模块编译，是否会因为缺少其他脚本的类型信息而报错，主要便于非官方的编译工具（比如 Babel）正确编译单个脚本。

    - **jsx：**

      > `jsx`设置如何处理`.tsx`文件。它可以取以下五个值。
      >
      > - `preserve`：保持 jsx 语法不变，输出的文件名为`.jsx`。
      > - `react`：将`<div />`编译成`React.createElement("div")`，输出的文件名为`.js`。
      > - `react-native`：保持 jsx 语法不变，输出的文件后缀名为`.js`。
      > - `react-jsx`：将`<div />`编译成`_jsx("div")`，输出的文件名为`.js`。
      > - `react-jsxdev`：跟`react-jsx`类似，但是为`_jsx()`加上更多的开发调试项，输出的文件名为`.js`。
      >
      > ```ts
      > {
      >     "compilerOptions": {
      >     	"jsx": "preserve"
      >     }
      > }
      > ```

    - **lib：**

      > `lib`值是一个数组，描述项目需要加载的 TS 内置类型描述文件，跟三斜线指令`/// <reference lib="" />`作用相同。
      >
      > ```ts 
      > {
      >     "compilerOptions": {
      >     	"lib": ["dom", "es2021"]
      >     }
      > }
      > ```
      >
      > TS 内置的类型描述文件，以下是重要的几个：
      >
      > - ES5
      > - ES2015
      > - ES6
      > - ES2016
      > - ES7
      > - ES2017
      > - ES2018
      > - ES2019
      > - ES2020
      > - ES2021
      > - ES2022
      > - ESNext
      > - DOM
      > - WebWorker
      > - ScriptHost

    - **listEmittedFiles：**

      > `listEmittedFiles`设置编译时在终端显示，生成了哪些文件。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "listEmittedFiles": true
      >   }
      > }
      > ```

    - **listFiles：**

      > `listFiles`设置编译时在终端显示，参与本次编译的文件列表。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "listFiles": true
      >   }
      > }
      > ```

    - **mapRoot：**

      > `mapRoot`指定 SourceMap 文件的位置，而不是默认的生成位置。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "sourceMap": true,
      >     "mapRoot": "https://my-website.com/debug/sourcemaps/"
      >   }
      > }
      > ```

    - **module：**

      > `module`指定编译产物的模块格式。它的默认值与`target`属性有关，如果`target`是`ES3`或`ES5`，它的默认值是`commonjs`，否则就是`ES6/ES2015`。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "module": "commonjs"
      >   }
      > }
      > ```
      >
      > 它可以取以下值：none、commonjs、amd、umd、system、es6/es2015、es2020、es2022、esnext、node16、nodenext。

    - **moduleResolution：**

      > `moduleResolution`确定模块路径的算法，即如何查找模块。它可以取以下四种值。
      >
      > - `node`：采用 Node.js 的 CommonJS 模块算法。
      > - `node16`或`nodenext`：采用 Node.js 的 ECMAScript 模块算法，从 TS 4.7 开始支持。
      > - `classic`：TS 1.6 之前的算法，新项目不建议使用。
      > - `bundler`：TS 5.0 新增的选项，表示当前代码会被其他打包器（比如 Webpack、Vite、esbuild、Parcel、rollup、swc）处理，从而放宽加载规则，它要求`module`设为`es2015`或更高版本，详见加入该功能的 [PR 说明](https://github.com/microsoft/TypeScript/pull/51669)。
      >
      > 它的默认值与`module`属性有关，如果`module`为`AMD`、`UMD`、`System`或`ES6/ES2015`，默认值为`classic`；如果`module`为`node16`或`nodenext`，默认值为这两个值；其他情况下,默认值为`Node`。

    - **moduleSuffixes：**

      > `moduleSuffixes`指定模块的后缀名。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "moduleSuffixes": [".ios", ".native", ""]
      >   }
      > }
      > ```
      >
      > 上面的设置使得 TS 对于语句`import * as foo from "./foo";`，会搜索以下脚本`./foo.ios.ts`、`./foo.native.ts`和`./foo.ts`。

    - **newLine：**

      > `newLine`设置换行符为`CRLF`（Windows）还是`LF`（Linux）。

    - **noEmit：**

      > `noEmit`设置是否产生编译结果。如果不生成，TS 编译就纯粹作为类型检查了。

    - **noEmitHelpers：**

      > `noEmitHelpers`设置在编译结果文件不插入 TS 辅助函数，而是通过外部引入辅助函数来解决，比如 NPM 模块`tslib`。

    - **noEmitOnError：**

      > `noEmitOnError`指定一旦编译报错，就不生成编译产物，默认为`false`。

    - **noFallthroughCasesInSwitch：**

      > `noFallthroughCasesInSwitch`设置是否对没有`break`语句（或者`return`和`throw`语句）的 switch 分支报错，即`case`代码里面必须有终结语句（比如`break`）。

    - **noImplicitAny：**

      > `noImplicitAny`设置当一个表达式没有明确的类型描述、且编译器无法推断出具体类型时，是否允许将它推断为`any`类型。它是一个布尔值，默认为`true`，即只要推断出`any`类型就报错。

    - **noImplicitReturns：**

      > `noImplicitReturns`设置是否要求函数任何情况下都必须返回一个值，即函数必须有`return`语句。

    - **noImplicitThis：**

      > `noImplicitThis`设置如果`this`被推断为`any`类型是否报错。

    - **noUnusedLocals：**

      > `noUnusedLocals`设置是否允许未使用的局部变量。

    - **noUnusedParameters：**

      > `noUnusedParameters`设置是否允许未使用的函数参数。

    - **outDir：**

      > `outDir`指定编译产物的存放目录。如果不指定，编译出来的`.js`文件存放在对应的`.ts`文件的相同位置。

    - **outFile：**

      > `outFile`设置将所有非模块的全局文件，编译在同一个文件里面。它只有在`module`属性为`None`、`System`、`AMD`时才生效，并且不能用来打包 CommonJS 或 ES6 模块。

    - **paths：**

      > `paths`设置模块名和模块路径的映射，也就是 TS 如何导入`require`或`imports`语句加载的模块。
      >
      > `paths`基于`baseUrl`进行加载，所以必须同时设置它俩。
      >
      > ```ts
      > {
      >       "compilerOptions": {
      >            "baseUrl": "./",
      >            "paths": {
      >            	"b": ["bar/b"]
      >            }
      >       }
      > }
      > ```
      >
      > 上面示例中，paths 设置的是执行`require('b')`时，即加载的是`./bar/b`。
      >
      > 它还可以使用`*`等通配符，表明模块名与模块位置的对应关系。
      >
      > ```ts
      > {
      >       "compilerOptions": {
      >            "baseUrl": "./",
      >            "paths": {
      >            	"@bar/*": ["bar/*"]
      >            }
      >       }
      > }
      > ```

    - **preserveConstEnums：**

      > `preserveConstEnums`将`const enum`结构保留下来，不替换成常量值。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "preserveConstEnums": true
      >   }
      > }
      > ```

    - **pretty：**

      > `pretty`设置美化输出终端的编译信息，默认为`true`。

    - **removeComments：**

      > `removeComments`移除 TS 脚本里面的注释，默认为`false`。

    - **resolveJsonModule：**

      > `resolveJsonModule`允许 import 命令导入 JSON 文件。

    - **rootDir：**

      > `rootDir`设置源码脚本所在的目录，主要跟编译后的脚本结构有关。`rootDir`对应目录下的所有脚本，会成为输出目录里面的顶层脚本。

    - **rootDirs：**

      > `rootDirs`把多个不同目录，合并成一个虚拟目录，便于模块定位。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "rootDirs": ["bar", "foo"]
      >   }
      > }
      > ```
      >
      > 上面示例中，`rootDirs`将`bar`和`foo`组成一个虚拟目录。

    - **sourceMap：**

      > `sourceMap`设置编译时是否生成 SourceMap 文件。

    - **sourceRoot：**

      > `sourceRoot`在 SourceMap 里面设置 TS 源文件的位置。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "sourceMap": true,
      >     "sourceRoot": "https://my-website.com/debug/source/"
      >   }
      > }
      > ```

    - **strict：**

      > `strict`用来打开 TS 的严格检查。它的值是一个布尔值，默认是关闭的。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "strict": true
      >   }
      > }
      > ```
      >
      > 这个设置相当于同时打开以下的一系列设置。
      >
      > - alwaysStrict
      > - strictNullChecks
      > - strictBindCallApply
      > - strictFunctionTypes
      > - strictPropertyInitialization
      > - noImplicitAny
      > - noImplicitThis
      > - useUnknownInCatchVariables
      >
      > 打开`strict`的时候，允许单独关闭其中一项。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "strict": true,
      >     "alwaysStrict": false
      >   }
      > }
      > ```

    - **strictBindCallApply：**

      > `strictBindCallApply`设置是否对函数的`call()`、`bind()`、`apply()`这三个方法进行类型检查。
      >
      > 如果不打开`strictBindCallApply`编译选项，编译器不会对以上三个方法进行类型检查，参数类型都是`any`，传入任何参数都不会产生编译错误。
      >
      > ```ts
      > function fn(x: string) {
      >   return parseInt(x);
      > }
      > 
      > // strictBindCallApply:false
      > const n = fn.call(undefined, false);
      > // 以上不报错
      > ```

    - **strictFunctionTypes：**

      > `strictFunctionTypes`允许对函数更严格的参数检查。具体来说，如果函数 B 的参数是函数 A 参数的子类型，那么函数 B 不能替代函数 A。
      >
      > ```ts
      > function fn(x:string) {
      >   console.log('Hello, ' + x.toLowerCase());
      > }
      > 
      > type StringOrNumberFunc = (ns:string|number) => void;
      > 
      > // 打开 strictFunctionTypes，下面代码会报错
      > let func:StringOrNumberFunc = fn;
      > ```
      >
      > 上面示例中，函数`fn()`的参数是`StringOrNumberFunc`参数的子集，因此`fn`不能替代`StringOrNumberFunc`。

    - **strictNullChecks：**

      > 不打开`strictNullChecks`的情况下，一个变量不管类型是什么，都可以赋值为`undefined`或`null`。
      >
      > ```ts
      > // 不打开 strictNullChecks 的情况
      > let x:number;
      > 
      > x = undefined; // 不报错
      > x = null; // 不报错
      > ```
      >
      > 上面示例中，不打开`strictNullChecks`时，变量`x`的类型是`number`，但是赋值为`undefined`或`null`都不会报错。这是为了继承 JS 的设定：当变量没有赋值时，它的值就为`undefined`。
      >
      > 一旦打开`strictNullChecks`，就使用严格类型，禁止变量赋值为`undefined`和`null`，除非变量原本就是这两种类型。它相当于从变量的值里面，排除了`undefined`和`null`。
      >
      > ```ts
      > // 打开 strictNullChecks 的情况
      > let x:number;
      > 
      > x = undefined; // 报错
      > x = null; // 报错
      > ```
      >
      > 上面示例中，打开`strictNullChecks`时，变量`x`作为`number`类型，就不能赋值为`undefined`和`null`。
      >
      > 下面是一个例子。
      >
      > ```ts
      > // 打开 strickNullChecks 时，类型 A 为 number
      > // 不打开时，类型 A 为 string
      > type A = unknown extends {} ? string : number;
      > ```
      >
      > 上面示例中，`{}`代表了 Object 类型，不打开`strictNullChecks`时，它包括了`undefined`和`null`，就相当于包括了所有类型的值，所以这时`unknown`类型可以赋值给`{}`类型，类型`A`就为`string`。打开`strictNullChecks`时，`{}`就排除掉了`undefined`和`null`，这时`unknown`类型就不能赋值给`{}`类型后，类型`A`就为`number`。
      >
      > 最后，`strict`属性包含了`strictNullChecks`，如果打开`strict`属性，就相当于打开了`strictNullChecks`。

    - **strictPropertyInitialization：**

      > `strictPropertyInitialization`设置类的实例属性都必须初始化，包括以下几种情况。
      >
      > - 设为`undefined`类型
      > - 显式初始化
      > - 构造函数中赋值
      >
      > 注意，使用该属性的同时，必须打开`strictNullChecks`。
      >
      > ```ts
      > // strictPropertyInitialization：true
      > class User {
      >   // 报错，属性 username 没有初始化
      >   username: string;
      > }
      > 
      > // 解决方法一
      > class User {
      >   username = '张三';
      > }
      > 
      > // 解决方法二
      > class User {
      >   username:string|undefined;
      > }
      > 
      > // 解决方法三
      > class User {
      >   username:string;
      > 
      >   constructor(username:string) {
      >     this.username = username;
      >   }
      > }
      > // 或者
      > class User {
      >   constructor(public username:string) {}
      > }
      > 
      > // 解决方法四：赋值断言
      > class User {
      >   username!:string;
      > 
      >   constructor(username:string) {
      >     this.initialize(username);
      >   }
      > 
      >   private initialize(username:string) {
      >     this.username = username;
      >   }
      > }
      > ```

    - **suppressExcessPropertyErrors：**

      > `suppressExcessPropertyErrors`关闭对象字面量的多余参数的报错。

    - **target：**

      > `target`指定编译出来的 JS 代码的 ECMAScript 版本，比如`es2021`，默认是`es3`。
      >
      > 它可以取以下值。
      >
      > - es3
      > - es5
      > - es6/es2015
      > - es2016
      > - es2017
      > - es2018
      > - es2019
      > - es2020
      > - es2021
      > - es2022
      > - esnext
      >
      > 注意，如果编译的目标版本过老，比如`"target": "es3"`，有些语法可能无法编译，`tsc`命令会报错。

    - **traceResolution：**

      > `traceResolution`设置编译时，在终端输出模块解析的具体步骤。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "traceResolution": true
      >   }
      > }
      > ```

    - **typeRoots：**

      > `typeRoots`设置类型模块所在的目录，默认是`node_modules/@types`，该目录里面的模块会自动加入编译。一旦指定了该属性，就不会再用默认值`node_modules/@types`里面的类型模块。
      >
      > 该属性的值是一个数组，数组的每个成员就是一个目录，它们的路径是相对于`tsconfig.json`位置。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "typeRoots": ["./typings", "./vendor/types"]
      >   }
      > }
      > ```

    - **types：**

      > 默认情况下，`typeRoots`目录下所有模块都会自动加入编译，如果指定了`types`属性，那么只有其中列出的模块才会自动加入编译。
      >
      > ```ts
      > {
      >   "compilerOptions": {
      >     "types": ["node", "jest", "express"]
      >   }
      > }
      > ```
      >
      > 上面的设置表示，默认情况下，只有`./node_modules/@types/node`、`./node_modules/@types/jest`和`./node_modules/@types/express`会自动加入编译，其他`node_modules/@types/`目录下的模块不会加入编译。
      >
      > 如果`"types": []`，就表示不会自动将所有`@types`模块加入编译。

    - **useDefineForClassFields：**

      > `useDefineForClassFields`这个设置针对的是，在类（class）的顶部声明的属性。TS 早先对这一类属性的处理方法，与写入 ES2022 标准的处理方法不一致。这个设置设为`true`，就用来开启 ES2022 的处理方法，设为`false`就是 TS 原有的处理方法。
      >
      > 它的默认值跟`target`属性有关，如果编译目标是`ES2022`或更高，那么`useDefineForClassFields`默认值为`true`，否则为`false`。

    - **useUnknownInCatchVariables：**

      > `useUnknownInCatchVariables`设置`catch`语句捕获的`try`抛出的返回值类型，从`any`变成`unknown`。
      >
      > ```ts
      > try {
      >   someExternalFunction();
      > } catch (err) {
      >   err; // 类型 any
      > }
      > ```
      >
      > 上面示例中，默认情况下，`catch`语句的参数`err`类型是`any`，即可以是任何值。
      >
      > 打开`useUnknownInCatchVariables`以后，`err`的类型抛出的错误将是`unknown`类型。这带来的变化就是使用`err`之前，必须缩小它的类型，否则会报错。
      >
      > ```ts
      > try {
      >   someExternalFunction();
      > } catch (err) {
      >   if (err instanceof Error) {
      >     console.log(err.message);
      >   }
      > }
      > ```

- ### 关于 tsc 命令

  > tsc 是 TS 官方的命令行编译器，用来检查代码，并将其编译成 JS 代码。
  >
  > tsc 默认使用当前目录下的配置文件`tsconfig.json`，但也可以接受独立的命令行参数。命令行参数会覆盖`tsconfig.json`中的配置。

  ##### tsc 的基本用法如下：

  ```bash
  # 使用 tsconfig.json 的配置
  $ tsc
  
  # 只编译 index.ts
  $ tsc index.ts
  
  # 编译 src 目录的所有 .ts 文件
  $ tsc src/*.ts
  
  # 指定编译配置文件
  $ tsc --project tsconfig.production.json
  
  # 只生成类型声明文件，不编译出 JS 文件
  $ tsc index.js --declaration --emitDeclarationOnly
  
  # 多个 TS 文件编译成单个 JS 文件
  $ tsc app.ts util.ts --target esnext --outfile index.js
  ```

  ##### tsc 的命令行参数：

  > tsc 的命令行参数，大部分与 tsconfig.json 的属性一一对应。
  >
  > 下面只是按照首字母排序，简单罗列出主要的一些参数，详细解释可以参考`tsconfig.json`配置文件用法。
  >
  > `--all`：输出所有可用的参数。
  >
  > `--allowJs`：允许 TS 脚本加载 JS 模块，编译时将 JS 一起拷贝到输出目录。
  >
  > `--allowUnreachableCode`：如果 TS 脚本有不可能运行到的代码，不报错。
  >
  > `--allowUnusedLabels`：如果 TS 脚本有没有用到的标签，不报错。
  >
  > `--alwaysStrict`：总是在编译产物的头部添加`use strict`。
  >
  > `--baseUrl`：指定非相对位置的模块定位的基准 URL。
  >
  > `--build`：启用增量编译。
  >
  > `--checkJs`：对 JS 脚本进行类型检查。
  >
  > `--declaration`：为 TS 脚本生成一个类型生成文件。
  >
  > `--declarationDir`：指定生成的类型声明文件的所在目录。
  >
  > `--declarationMap`：为`.d.ts`文件生成 SourceMap 文件。
  >
  > `--diagnostics`：构建后输出编译性能信息。
  >
  > `--emitBOM`：在编译输出的 UTF-8 文件头部加上 BOM 标志。
  >
  > `--emitDeclarationOnly`：只编译输出类型声明文件，不输出 JS 文件。
  >
  > `--esModuleInterop`：更容易使用 import 命令加载 CommonJS 模块。
  >
  > `--exactOptionalPropertyTypes`：不允许将可选属性设置为`undefined`。
  >
  > `--experimentalDecorators`：支持早期的装饰器语法。
  >
  > `--explainFiles`：输出进行编译的文件信息。
  >
  > `--forceConsistentCasingInFileNames`：文件名大小写敏感，默认打开。
  >
  > `--help`：输出帮助信息。
  >
  > `--importHelpers`：从外部库（比如 tslib）输入辅助函数。
  >
  > `--incremental`：启用增量构建。
  >
  > `--init`：在当前目录创建一个全新的`tsconfig.json`文件，里面是预设的设置。
  >
  > `--inlineSourceMap`：SourceMap 信息嵌入 JS 文件，而不是生成独立的`.js.map`文件。
  >
  > `--inlineSources`：将 TS 源码作为 SourceMap 嵌入编译出来的 JS 文件。
  >
  > `--isolatedModules`：确保每个模块能够独立编译，不依赖其他输入的模块。
  >
  > `--jsx`：设置如何处理 JSX 文件。
  >
  > `--lib`：设置目标环境需要哪些内置库的类型描述。
  >
  > `--listEmittedFiles`：编译后输出编译产物的文件名。
  >
  > `--listFiles`：编译过程中，列出读取的文件名。
  >
  > `--listFilesOnly`：列出编译所要处理的文件，然后停止编译。
  >
  > `--locale`：指定编译时输出的语言，不影响编译结果。
  >
  > `--mapRoot`：指定 SourceMap 文件的位置。
  >
  > `--module`：指定编译生成的模块格式。
  >
  > `--moduleResolution`：指定如何根据模块名找到模块的位置。
  >
  > `--moduleSuffixes`：指定模块文件的后缀名。
  >
  > `--newLine`：指定编译产物的换行符，可以设为`crlf`或者`lf`。
  >
  > `--noEmit`：不生成编译产物，只进行类型检查。
  >
  > `--noEmitHelpers`：不在编译产物中加入辅助函数。
  >
  > `--noEmitOnError`：一旦报错，就停止编译，没有编译产物。
  >
  > `--noFallthroughCasesInSwitch`：Switch 结构的`case`分支必须有终止语句（比如`break`）。
  >
  > `--noImplicitAny`：类型推断只要为`any`类型就报错。
  >
  > `--noImplicitReturns`：函数内部没有显式返回语句（比如`return`）就报错。
  >
  > `--noImplicitThis`：如果`this`关键字是`any`类型，就报错。
  >
  > `--noImplicitUseStrict`：编译产生的 JS 文件头部不添加`use strict`语句。
  >
  > `--noResolve`：不进行模块定位，除非该模块是由命令行传入。
  >
  > `--noUnusedLocals`：如果有未使用的局部变量就报错。
  >
  > `--noUnusedParameters`：如果有未使用的函数参数就报错。
  >
  > `--outDir`：指定编译产物的存放目录。
  >
  > `--outFile`：所有编译产物打包成一个指定文件。
  >
  > `--preserveConstEnums`：不将`const enum`结构在生成的代码中，替换成常量。
  >
  > `--preserveWatchOutput`： watch 模式下不清屏。
  >
  > `--pretty`：美化显示编译时的终端输出。这是默认值，但是可以关闭`--pretty false`。
  >
  > `--project`（或者`-p`）：指定编译配置文件，或者该文件所在的目录。
  >
  > `--removeComments`：编译结果中移除代码注释。
  >
  > `--resolveJsonModule`：允许加载 JSON 文件。
  >
  > `--rootDir`：指定加载文件所在的根目录，该目录里面的目录结构会被复制到输出目录。
  >
  > `--rootDirs`：允许模块定位时，多个目录被当成一个虚拟目录。
  >
  > `--skipDefaultLibCheck`：跳过 TS 内置类型声明文件的类型检查。
  >
  > `--skipLibCheck`：跳过`.d.ts`类型声明文件的类型检查。这样可以加快编译速度。
  >
  > `--showConfig`：终端输出编译配置信息，而不进行配置。
  >
  > `--sourcemap`：为编译产生的 JS 文件生成 SourceMap 文件（.map 文件）。
  >
  > `--sourceRoot`：指定 SourceMap 文件里面的 TS 源码根目录位置。
  >
  > `--strict`：打开 TS 严格检查模式。
  >
  > `--strictBindCallApply`：bind, call、apply 这三个函数的类型，匹配原始函数。
  >
  > `--strictFunctionTypes`：如果函数 B 的参数是函数 A 参数的子类型，那么函数 B 不能替代函数 A。
  >
  > `--strictNullChecks`：对`null`和`undefined`进行严格类型检查。
  >
  > `--strictPropertyInitialization`：类的属性必须进行初始值，但是允许在构造函数里面赋值。
  >
  > `--suppressExcessPropertyErrors`：关闭对象字面量的多余参数的报错。
  >
  > `--target`：指定编译出来的 JS 代码的版本，TS 还会在编译时自动加入对应的库类型声明文件。
  >
  > `--traceResolution`：编译时在终端输出模块解析（moduleResolution）的具体步骤。
  >
  > `--typeRoots`：设置类型模块所在的目录，替代默认的`node_modules/@types`。
  >
  > `--types`：设置`typeRoots`目录下需要包括在编译之中的类型模块。
  >
  > `--version`：终端输出 tsc 的版本号。
  >
  > `--watch`（或者`-w`）：进入观察模式，只要文件有修改，就会自动重新编译。

------

