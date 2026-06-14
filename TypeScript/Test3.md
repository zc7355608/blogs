- ### declare关键字

  > `declare`用来告诉编译器，某个类型是存在的（全局或其他的 TS 文件中），可以在当前文件中使用。
  >
  > 它的主要作用，就是让当前文件可以使用其他文件声明的类型。举例来说，自己的脚本使用外部库定义的函数，编译器会因为不知道外部函数的类型定义而报错，这时就可以在自己的脚本里面使用`declare`关键字，明确声明出该类型。这样的话，编译单个脚本就不会因为使用了外部类型而报错。
  >
  > declare 关键字可以描述以下类型：
  >
  > - 变量（const、let、var 声明）
  > - type 类型别名或 interface 接口类型
  > - Class 类
  > - Enum 枚举
  > - 函数（function）
  > - 模块（module）及 命名空间（namespace）
  >
  > declare 关键字的重要特点是，它只是通知编译器某个类型是存在的，不用给出具体实现。比如，只描述函数的类型，不给出函数的实现，如果不使用`declare`，这是做不到的。
  >
  > declare 只能用来描述已经存在的变量和数据结构，不能用来声明新的变量和数据结构。另外，所有 declare 语句都不会出现在编译后的 JS 文件里面（只是提供给编译器来用的）。

  - **变量：**`declare` 可以给出外部变量的类型描述。

    > 举例来说，当前脚本使用了其他脚本定义的全局变量`x`。
    >
    > ```ts
    > x = 123  // 报错
    > ```
    >
    > 上面示例中，变量`x`是其他脚本定义的，当前脚本不知道它的类型，编译器就会报错。
    >
    > 这时使用`declare`给出它的类型，就不会报错了。
    >
    > ```ts
    > declare let x:number
    > x = 1
    > ```
    >
    > 如果 declare 关键字没有给出变量的具体类型，那么变量类型就是`any`。
    >
    > ```ts
    > declare let x
    > x = 1  // 这里x的类型为any
    > ```
    >
    > 注意，declare 关键字只用来给出类型描述，是纯的类型代码，不允许设置变量的初始值，即不能涉及值。
    >
    > ```ts
    > declare let x:number = 1  // 报错
    > ```

  - **函数：**`declare` 可以给出外部函数的类型描述。

    ```ts
    declare function sayHello(name: string): void
    // declare 命令给出了sayHello()的类型描述，表示这个函数是由外部文件定义的，因此这里可以直接使用该函数
    sayHello('张三')
    ```

    > 注意，这种单独的函数类型声明，只能用于`declare`命令后面。一方面，TS 不支持单独的函数类型声明语句；另一方面，declare 关键字后面也不能带有函数的具体实现。

  - **类：**

    > declare 给出 class 类型描述的写法如下。
    >
    > ```ts
    > declare class Animal {
    >     constructor(name: string);
    >     eat(): void;
    >     sleep(): void;
    > }
    > ```
    >
    > 下面是一个复杂一点的例子。
    >
    > ```ts
    > declare class C {
    >     // 静态成员
    >     public static s0(): string;
    >     private static s1: string;
    > 
    >     // 属性
    >     public a: number;
    >     private b: number;
    > 
    >     // 构造函数
    >     constructor(arg: number);
    > 
    >     // 方法
    >     m(x: number, y: number): number;
    > 
    >     // 存取器
    >     get c(): number;
    >     set c(value: number);
    > 
    >     // 索引签名
    >     [index:string]: any;
    > }
    > ```
    >
    > 同样的，declare 后面不能给出 Class 的具体实现或初始值。

  - **模块、命名空间：**

    > 如果想把变量、函数、类组织在一起，可以将 declare 与 module 或 namespace 一起使用。相当于声明了某个模块/命名空间的存在，让 TS 编译器知道有这个外部模块。尤其是在 TS 中使用外部的 JS 库时，可以手动指定这个库的具体类型。

    ```ts
    // 模块
    declare module AnimalLib {
        class Animal {
            constructor(name:string);
            eat(): void;
            sleep(): void;
        }
    
        type Animals = 'Fish' | 'Dog';
    }
    
    // 命名空间，了解即可
    declare namespace AnimalLib {
        class Animal {
            constructor(name:string);
            eat():void;
            sleep():void;
        }
    
        type Animals = 'Fish' | 'Dog';
    }
    ```

    > 注意：**某些第三方库是纯 JS 写的，没有类型声明文件**，此时`import`导入该 JS 模块会报错，因为编译器不认为这是一个合法的 TS 模块。解决办法之一就是，在自己的脚本顶部加上一行命令：`declare module "模块名";`。
    >
    > 加上上面的命令以后，外部模块即使没有类型声明，也可以通过编译。但是，从该模块输入的所有接口都将为`any`类型。
    >
    > 下面的例子是当前脚本使用了`myLib`这个外部 JS 库，它有方法`makeGreeting()`和属性`numberOfGreetings`。`myLib`的类型描述就可以这样写。这样在 TS 项目中用这个 JS 库时，就能让其拥有 TS 的类型提示了。
    >
    > ```ts
    > declare module myLib {
    >     function makeGreeting(s:string): string;
    >     let numberOfGreetings: number;
    > }
    > ```

    > *declare module 和 declare namespace 里面，如果不加`export`关键字则是全局的，任何 TS 模块都可以直接用。不过一般都会加`export`区分以及隔离不同模块中的类型接口，这时要用的话就必须通过`import`来导入。(可能不对，TODO)*
    >
    > declare module 和 declare namespace 里面，如果不加`export`关键则该类型只能在模块内使用，外部获取不了该类型。一般都会加`export`，这时要用的话就必须通过`import`来导入。
    >
    > ```ts
    > declare module 'io' {
    > 	export function readFile(filename: string): string;
    > }
    > declare namespace Foo {
    > 	export var a: boolean;
    > }
    > ```

    ###### declare 关键字的另一个用途，是为外部模块添加属性和方法时，给出新增部分的类型描述（默认导出的值不能扩展）。

    > 下面是一个例子。一个项目有多个 TS 模块，可以通过`declare`为导入的外部模块做类型扩展。
    >
    > ```ts
    > // a.ts
    > export interface A {
    > 	x: number;
    >    }
    >    
    >    // b.ts
    >    import { A } from './a';
    >    
    > declare module './a' {
    >  interface A {
    > 	y: number;
    >  }
    >}
    > 
    > const a:A = { x: 0, y: 0 };
    > ```
    > 
    > 使用这种语法进行模块的类型扩展时，有两点需要注意：
    > 
    > 1. `declare module NAME`语法里面的模块名`NAME`，跟 import 和 export 的模块名规则是一样的，且必须跟当前文件加载该模块的语句中的模块名保持一致（上例`import { A } from './a'`）。（其实也支持通配符）
    > 2. 不能创建新的顶层类型。也就是说，只能对`a.ts`模块中已经存在的类型进行扩展，不允许增加新的顶层类型，比如新定义一个接口`B`。
    > 3. 不能对默认导出接口进行扩展，只能对 export 分别导出的接口进行扩展。（这是因为在进行类型扩展时，需要依赖输出的接口名）

    > declare module 描述的模块名可以使用通配符。
    >
    > ```ts
    > declare module 'my-plugin-*' {
    >     interface PluginOptions {
    >         enabled: boolean;
    >         priority: number;
    >     }
    >     function initialize(options: PluginOptions): void;
    >    export = initialize;
    > }
    >```
    > 
    >上面示例中，模块名`my-plugin-*`表示适配所有以`my-plugin-`开头的模块名（比如`my-plugin-logger`）。

  - **枚举：**declare 关键字给出 Enum 枚举类型描述的例子如下，下面的写法都是允许的。

    ```ts
    declare enum E1 {
        A,
        B,
    }
    
    declare enum E2 {  // 因为枚举名和枚举值同样都可以作为类型使用，因此这里看似是值，其实是类型
        A = 0,
        B = 1,
    }
    
    declare const enum E3 {
        A,
        B,
    }
    
    declare const enum E4 {
        A = 0,
        B = 1,
    }
    ```

  - **declare global：**如果要为 JS 的内置对象/模块（`window/document/global/String`等）添加属性和方法，可以使用`declare global {}`语法。

    ```ts
    export {};
    
    declare global {
        interface String {
        	toSmallString(): string;
        }
    }
    
    String.prototype.toSmallString = ():string => {
        // 具体实现
        return '';
    };
    ```

    > 上面示例中，为 JS 原生的`String`对象添加了`toSmallString()`方法。`declare global`给出这个新增方法的类型描述。
    >
    > 这个示例第一行的空导出语句`export {}`，作用是强制编译器将这个脚本当作模块处理。这是因为**`declare global`必须用在模块里面**。
    >
    > 下面的示例是为 window 对象（类型接口为`Window`）添加一个属性`myAppConfig`。
    >
    > ```ts
    > export {};
    > 
    > declare global {
    >        interface Window {
    >        	myAppConfig:object;
    >        }
    > }
    > 
    > const config = window.myAppConfig;
    > ```
    >
    > `declare global`同样只能扩充现有对象的类型描述，不能增加新的顶层类型。

  - **declare module 用于类型声明文件：**

    > 我们可以为每个模块脚本，定义一个`.d.ts`文件，把该脚本用到的类型定义都放在这个文件里面。但是，更方便的做法是为整个项目，定义一个大的`.d.ts`文件，在这个文件里面使用`declare module`定义项目中每个模块的类型。

    > 下面的示例是`node.d.ts`文件的一部分。
    >
    > ```ts
    > declare module "url" {
    >     export interface Url {
    >         protocol?: string;
    >         hostname?: string;
    >         pathname?: string;
    >     }
    >     export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
    > }
    > declare module "path" {
    >     export function normalize(p: string): string;
    >     export function join(...paths: any[]): string;
    >     export var sep: string;
    > }
    > // ...
    > ```
    >
    > 上面示例中，`url`和`path`都是单独的模块文件，但是它们的类型都定义在`node.d.ts`这个文件里面。
    >
    > 使用时，自己的 TS 文件中可以通过三斜杠命令，来导入这个类型声明文件。
    >
    > ```ts
    > /// <reference path="node.d.ts" />  // 必须写在文件第一行
    > ```
    >
    > 旧方式，目前更推荐使用新方式（`tsconfig.json`中进行配置）或`import`引入类型声明文件中声明的类型：
    >
    > ```ts
    > // my-module.d.ts
    > declare module 'my-module' {
    >     export interface MyType {
    >         name: string;
    >         age: number;
    >     }
    > }
    > 
    > // b.ts
    > import { MyType } from 'my-module';
    > 
    > const person: MyType = {
    >     name: 'Alice',
    >     age: 25
    > };
    > console.log(person);
    > ```

- ### `.d.ts`类型声明文件

  > 单独使用的模块，一般会同时提供一个单独的类型声明文件（declaration file），把本模块的外部接口的所有类型都写在这个文件里面，便于模块使用者了解接口，也便于编译器检查使用者的用法是否正确。它的文件名一般为`[模块名].d.ts`的形式。
  >
  > （TS 项目最终发布到npm仓库的肯定是 JS 文件，当别人引用该项目，就能通过该项目提供的`d.ts`文件来获取类型了）

  - ###### 用法：

    > 举例来说，有一个模块的代码如下。
    >
    > ```ts
    > const maxInterval = 12;
    > function getArrayLength(arr) {
    > 	return arr.length;
    > }
    > module.exports = {
    >  getArrayLength,
    >  maxInterval,
    > };
    > ```
    >
    > 它的类型声明文件可以写成下面这样。
    >
    > ```ts
    > export function getArrayLength(arr: any[]): number;
    > export const maxInterval: 12;
    > ```
    >
    > 类型声明文件也可以使用`export =`命令，对外输出类型接口。下面是 moment 模块的类型声明文件的例子。
    >
    > ```ts
    > declare module 'moment' {
    >  function moment(): any;
    >  export = moment;
    > }
    > ```
    >
    > 上面示例中，模块`moment`内部有一个函数`moment()`，而`export =`表示`module.exports`输出的就是这个函数。
    >
    > 除了使用`export =`，模块输出在类型声明文件中，也可以使用`export default`。
    >
    > ```ts
    > // 写法一
    > declare const pi: number;
    > export default pi;
    > 
    > // 写法二
    > declare const pi: number;
    > export= pi;
    > ```
    >
    > 下面是一个如何使用类型声明文件的简单例子。有一个类型声明文件`types.d.ts`。
    >
    > ```ts
    > // types.d.ts
    > export interface Character {
    >  catchphrase?: string;
    >  name: string;
    > }
    > ```
    >
    > 然后，就可以在 TS 脚本里面导入该文件声明的类型。
    >
    > ```ts
    > // index.ts
    > import { Character } from "./types";
    > 
    > export const character:Character = {
    >  catchphrase: "Yee-haw!",
    >  name: "Sandy Cheeks",
    > };
    > ```

  - ###### （新的类型声明文件引入方式）类型声明文件也可以在 `tsconfig.json` 中通过`files`配置项加入编译，这样的话，编译器打包项目时会自动将该类型声明文件加入编译，而不必在每个脚本里面加载类型声明文件。比如，moment 模块的类型声明文件是`moment.d.ts`，使用 moment 模块的项目可以将其加入项目的 `tsconfig.json` 文件：

    ```ts
    {
    	"compilerOptions": {},
        "files": [
            "src/index.ts",
            "typings/moment.d.ts"
        ]
    }
    ```

    > 这种方式加入的文件，文件中的声明全局有效。（除非使用了模块化语法，如 `export`）。

  - ###### 类型声明文件只包含类型描述，不包含具体实现，所以非常适合使用`declare`语句来描述类型。

    > 类型声明文件里面，变量的类型描述必须使用`declare`命令，否则会报错，因为变量声明语句是值相关代码。
    >
    > ```ts
    > declare let foo: string;
    > ```
    >
    > 接口有没有`declare`都可以，因为接口是完全的类型代码。
    >
    > ```ts
    > interface Foo {}  // 正确
    > declare interface Foo {}  // 正确
    > ```
    >
    > *类型声明文件里面，顶层类型可以加`export`也可以不加，取决于使用者是否通过`import`命令导入该模块中的类型。并且加了`export`的话类型会做模块隔离。（TODO,可能不对）*
    >
    > declare module 和 declare namespace 里面，如果不加`export`关键则该类型只能在模块内使用，外部获取不了该类型。一般都会加`export`，这时要用的话就必须通过`import`来导入。
    >
    > ```ts
    > export interface Data {
    >  version: string;
    > }
    > ```
    >
    > 下面是`moment`和`D3`库类型声明文件的例子。
    >
    > ```ts
    > declare module 'moment' {
    > export interface Moment {
    >  	format(format:string): string;
    > 
    >      add(
    >          amount: number,
    >          unit: 'days' | 'months' | 'years'
    >      ): Moment;
    > 
    >      subtract(
    >          amount:number,
    >          unit:'days' | 'months' | 'years'
    >      ): Moment;
    > }
    > 
    > function moment(
    >      input?: string | Date
    > ): Moment;
    > 
    > export default moment;
    > }
    > ```
    >
    > ```ts
    > declare namespace D3 {
    > export interface Selectors {
    >      select: {
    >          (selector: string): Selection;
    >          (element: EventTarget): Selection;
    >      };
    > 	}
    > 
    > export interface Event {
    >      x: number;
    >      y: number;
    > }
    > 
    > export interface Base extends Selectors {
    >  	event: Event;
    > }
    > }
    > 
    > declare var d3: D3.Base;
    > ```
    >

  - #### 类型声明文件的来源

    - **TS 编译器生成**：只要启用编译选项`declaration`，编译器就会在编译时自动生成单独的类型声明文件。

    - ##### TS 内置类型声明文件：

      > 安装 TS 语言时，会同时安装一些内置的类型声明文件，主要是内置的全局对象（JS 语言接口和运行环境 API）的类型声明。这些内置声明文件位于 TS 语言安装目录的`lib`目录中，数量大概有几十个，下面是其中一些主要文件：
      >
      > - lib.d.ts
      > - lib.dom.d.ts
      > - lib.es2015.d.ts
      > - lib.es2016.d.ts
      > - lib.es2017.d.ts
      > - lib.es2018.d.ts
      > - lib.es2019.d.ts
      > - lib.es2020.d.ts
      > - lib.es5.d.ts
      > - lib.es6.d.ts
      >
      > 这些内置声明文件的文件名统一为`lib.[description].d.ts`的形式，其中`description`部分描述了文件内容。比如，`lib.dom.d.ts`这个文件就描述了 DOM 结构的类型。
      >
      > 如果开发者想了解全局对象的类型接口（比如 ES6 全局对象的类型），那么就可以去查看这些内置声明文件。
      >
      > TS 编译器会自动根据编译选项`target`的值，加载对应的内置声明文件，所以不需要特别的配置。但是，可以使用编译选项`lib`，指定加载哪些内置声明文件。（编译选项`noLib`会禁止加载任何内置声明文件）

    - ##### 外部模块的类型声明文件，需要通过`npm`来安装：

      > 如果项目中使用了外部的某个第三方代码库（JS），那么就需要这个库的类型声明文件。这时又分成三种情况：

      - **这个库自带了类型声明文件**：一般来说，如果这个库的源码包含了`库名.d.ts`文件，那么就自带了类型声明文件。此时TS 一般会自动加载这些类型声明文件，无需额外配置。（类型声明文件的位置一般在`package.json`的`types`或`typings`字段中指定，或是`包名.d.ts`和`index.d.ts`）

      - **这个库没有自带，但是可以找到社区制作的类型声明文件（`@types/xxx`）**：第三方库如果没有提供类型声明文件，社区往往会提供。TS 社区主要使用 DefinitelyTyped 仓库，各种类型声明文件都会提交到那里，已经包含了几千个第三方库。这些声明文件都会作为一个单独的库，发布到 npm 的`@types/xxx`包下。比如，jQuery 的类型声明文件就发布成`@types/jquery`这个库，使用时安装这个库就可以了。

        > **注意：**
        >
        > 1. TS 会自动加载`node_modules/@types`目录下的模块，可以使用编译配置项`typeRoots`来改变这种行为。
        > 2. 默认情况下，TS 会自动加载`typeRoots`指定的这些目录中的所有模块，编译选项`types`可以指定加载这些目录中的哪些模块。这时 TS 就不再去加载`node_modules/@types`目录下的模块了。

      - **找不到类型声明文件，那就需要自己写了。**（或者用`declare module '模块名';`，此时模块的接口都为any类型）

  - #### 模块发布

    > 当前模块如果包含自己的类型声明文件，可以在 `package.json` 文件里面添加一个`types`字段或`typings`字段，指明类型声明文件的位置。（`types`是新写法，兼容性更好）
    >
    > ```ts
    > {
    >        "name": "awesome",
    >        "author": "Vandelay Industries",
    >        "version": "1.0.0",
    >        "main": "./lib/main.js",
    >        "types": "./lib/main.d.ts"
    > }
    > ```
    >
    > **注意：**如果类型声明文件名为`index.d.ts`，且在项目的根目录中，那就不需要在`package.json`里面注明了。

    > 有些包的类型声明文件会单独发布成一个 npm 模块，这时用户就必须同时安装该模块的类型声明模块。如`browserify`：
    >
    > ```ts
    > {
    >        "name": "browserify-typescript-extension",
    >        "author": "Vandelay Industries",
    >        "version": "1.0.0",
    >        "main": "./lib/main.js",
    >        "types": "./lib/main.d.ts",
    >        "dependencies": {
    >            "browserify": "latest",
    >            "@types/browserify": "latest",  // 其实这个包只安装为开发依赖即可，不过打包结果是一样的
    >            "typescript": "next"
    >        }
    > }
    > ```
    >
    > 上面示例是某个模块的 `package.json` 文件，该模块需要依赖 browserify 模块。由于后者的类型声明文件是一个单独的模块`@types/browserify`，所以还需要加载那个模块。

  - #### 三斜杠命令

    > 如果类型声明文件的内容非常多，可以拆分成多个文件，然后入口文件使用三斜杠命令，加载其他拆分后的文件。
    
    > 举例来说，入口文件是`main.d.ts`，里面的接口定义在`interfaces.d.ts`，函数定义在`functions.d.ts`。那么，`main.d.ts`里面可以用三斜杠命令，加载后面两个文件。
    >
    > ```ts
    > /// <reference path="./interfaces.d.ts" />
    > /// <reference path="./functions.d.ts" />
    > ```
    >
    > 注意：
    >
    > - 三斜杠命令（`///`）是一个 TS 编译器命令，用来指定编译器行为。它只能用在文件的头部，如果用在其他地方，会被当作普通的注释。另外，**若一个文件中使用了三斜线命令，那么在三斜线命令之前只允许使用单行注释、多行注释和其他三斜线命令，否则三斜杠命令也会被当作普通的注释**。
    > - 除了拆分类型声明文件，**普通 TS 脚本中也可以使用三斜杠命令来引入类型声明文件**。
    
    ##### 三斜杠命令主要包含三个参数，代表三种不同的命令：
    
    - **path：**`/// <reference path="" />`是最常见的三斜杠命令，告诉编译器在编译时需要包括的类型文件（`.d.ts`或`.ts`），常用来声明当前脚本依赖的类型声明文件。
    
      > ```ts
      > /// <reference path="./lib.ts" />
      > let count = add(1, 2);
      > ```
      >
      > 上面示例表示，当前脚本依赖于`./lib.ts`，里面是`add()`的定义。编译当前脚本时，还会同时编译`./lib.ts`。编译产物会有两个 JS 文件，一个当前脚本，另一个就是`./lib.js`。
      >
      > 下面的例子是当前脚本依赖于 Node.js 类型声明文件。
      >
      > ```ts
      > /// <reference path="node.d.ts"/>
      > import * as URL from "url";
      > let myUrl = URL.parse("https://www.typescriptlang.org");
      > ```
      >
      > **编译器会在预处理阶段，找出所有三斜杠引用的文件，将其添加到编译列表中，然后一起编译。**
      >
      > `path`参数指定了所引入文件的路径。如果该路径是一个相对路径，则基于当前脚本的路径进行计算。
      >
      > 使用该命令时，有以下两个注意事项。
      >
      > - `path`参数必须指向一个存在的文件，若文件不存在会报错。
      > - `path`参数不允许指向当前文件。
      >
      > 默认情况下，每个三斜杠命令引入的脚本，都会编译成单独的 JS 文件。
      >
      > 如果希望编译后只产出一个合并文件，可以使用编译选项`outFile`。但是，**`outFile`编译选项不支持合并 CommonJS 模块和 ES 模块**。只有当编译参数`module`的值设为 None、System 或 AMD 时，才能编译成一个文件。
      >
      > 如果打开了编译参数`noResolve`，则忽略三斜杠指令，将其当作一般的注释，原样保留在编译产物中。
    
    - **types：**`types` 参数用来告诉编译器当前脚本依赖某个 DefinitelyTyped 类型库，通常安装在`node_modules/@types`目录。types 参数的值是类型库的名称，也就是安装到`node_modules/@types`目录中的子目录的名字。
    
      > ```ts
      > /// <reference types="node" />
      > ```
      >
      > 上面示例中，这个三斜杠命令表示编译时添加 Node.js 的类型库，实际添加的脚本是`node_modules`目录里面的`@types/node/index.d.ts`。可以看到，这个命令的作用类似于`import`命令。
      >
      > 注意，这个命令只在你自己手写类型声明文件（`.d.ts`文件）时，才有必要用到。也就是说，只应该出现在`.d.ts`文件中，普通的`.ts`脚本文件不需要写这个命令。如果是普通的`.ts`脚本，可以使用`tsconfig.json`文件的`types`属性指定依赖的类型库。
      
    - **lib：**`/// <reference lib="..." />`命令允许脚本文件显式导入内置 lib 库，等同于在`tsconfig.json`文件里面使用`lib`属性指定 lib 库。
    
      > 前文说过，安装 TS 软件包时，会同时安装一些内置的类型声明文件，即内置的 lib 库。这些库文件位于 TS 安装目录的`lib`文件夹中，它们描述了 JS 语言和引擎的标准 API。
      >
      > 库文件并不是固定的，会随着 TS 版本的升级而更新。库文件统一使用 **lib.[description].d.ts** 的命名方式，而该命令里的`lib`属性的值就是库文件名的`description`部分，比如 `/// <reference lib="es2015" />` 就表示加载库文件`lib.es2015.d.ts`。
      >

- ### 类型运算符

  > TS 提供强大的类型运算能力，可以使用各种类型运算符，对已有的类型进行计算，得到新类型。

  - ##### `keyof` 运算符

    > TS 中的 `keyof` 是一个单目运算符，接受一个**接口**类型作为参数，返回该接口的所有键名(字符串)组成的联合类型：
    >
    > ```ts
    > type A = {
    >        name: string,
    >        age: number,
    > }
    > type MyKeys = keyof A  // 此时MyKeys的类型为：'name' | 'age' 的联合类型
    > ```
    >
    > 该运算符的主要用途是：精确表达对象的属性类型，以及用于*属性映射*（后面说）。

    > **注意：**
    >
    > - 由于 JS 对象的键名只有三种类型，所以`keyof any`的结果是`string|number|symbol`。
    >
    > - 对于没有自定义键名的类型使用 keyof，返回`never`类型，表示不可能有这样类型的键名。
    >
    >   ```ts
    >   type A = keyof {}  // never
    >   ```
    >
    > - 如果接口的属性名采用索引形式，keyof 会返回属性名的索引类型。
    >
    >   ```ts
    >   // 示例一
    >   interface T {
    >   	[prop: number]: number;
    >   }
    >   type KeyT = keyof T;  // number
    >     
    >   // 示例二
    >   interface T {
    >   	[prop: string]: number;
    >   }
    >   type KeyT = keyof T;  // string|number
    >   ```
    >
    > - 对于联合类型，keyof 返回成员共有的键名。对于交叉类型，keyof 返回所有键名。
    >
    >   ```ts 
    >   type A = { a: string; z: boolean };
    >   type B = { b: string; z: boolean };
    >   // 联合类型：返回 'Z'
    >  type T1 = keyof (A | B);
    >   // 交叉类型：返回 'a' | 'b' | 'z'
    >  type T2 = keyof (A & B);  // 相当于 keyof (A & B) ≡ keyof A | keyof B
    >   ```
    >
    > - keyof 取出的是键名组成的联合类型，如果想取出键值组成的联合类型，可以像下面这样写。
    > 
    >   ```ts
    >   type MyObj = {
    >       foo: number,
    >       bar: string,
    >   };
    >   type Keys = keyof MyObj;
    >  type Values = MyObj[Keys];  // number|string
    >   ```
    >
    >   > 上面示例中，`Keys`是键名组成的联合类型，而`MyObj[Keys]`会取出每个键名对应的键值类型，组成一个新的联合类型，即`number|string`。
    > 
    > - 如果 keyof 运算符用于数组或元组类型，得到的结果可能出人意料。
    > 
    >   ```ts
    >   type Result = keyof ['a', 'b', 'c'];
    >   // 返回 number | "0" | "1" | "2" | ... | "length" | "pop" | "push" | ···
    >   ```
    >
    >   > 上面示例中，keyof 会返回数组的所有键名，包括数字键名和继承的键名。

  - ##### `in` 运算符

    > 在 JS 中，`in`运算符用来确定对象是否包含某个属性。而在 TS 语言的类型运算中，`in`运算符还可以**取出（遍历）联合类型中的每一个成员类型。**
    >
    > ```ts
    > type U = 'a'|'b'|'c';
    > 
    > type Foo = {
    >    	[Prop in U]: number;
    > };
    > // 等同于
    > type Foo = {
    >        a: number,
    >        b: number,
    >        c: number
    > };
    > ```
    >
    > 上面示例中，`[Prop in U]`表示依次取出联合类型`U`中的每一个成员。

  - ##### `[]` 运算符

    > TS 中，`[]`运算符用于取出**接口**中键的类型，比如`T[K]`会返回接口`T`中属性`K`的类型。（`[]`中不能包含值的运算）
    >
    > ```ts
    > type Person = {
    >        age: number;
    >        name: string;
    >        alive: boolean;
    > };
    > type Age = Person['age'];  // Age 的类型是 number
    > ```
    >
    > 方括号的参数如果是联合类型，那么返回的也是联合类型。
    >
    > ```ts 
    > type Person = {
    >        age: number;
    >        name: string;
    >        alive: boolean;
    > };
    > type T = Person['age'|'name'];  // number|string
    > type A = Person[keyof Person];  // number|string|boolean
    > ```
    > 
    > 如果访问不存在的属性，会报错。
    >
    > ```ts
    >type T = Person['notExisted'];  // 报错
    > ```
    > 
    > 方括号运算符的参数也可以是属性名的索引类型。
    >
    > ```ts
    >type Obj = {
    > 	[key:string]: number,
    > };
    > type T = Obj[string];  // number
    > ```
    > 
    > 上面的语法对于数组也同样适用，可以使用`number`作为方括号的参数。（注意必须加`typeof`关键字）
    >
    > ```ts
    >// MyArray 的类型是 { [key:number]: string }
    > const MyArray = ['a','b','c'];
    > 
    > // 等同于 (typeof MyArray)[number]
    > type Person = typeof MyArray[number];  // 返回 string
    > ```
    
  - ##### `extends...?:` 条件运算符

    > 条件运算符可以根据当前类型是否符合某种条件，返回不同的类型：`T extends U ? X : Y`。含义：如果`T`能够赋值给类型`U`，表达式的结果为类型`X`，否则结果为类型`Y`。注意：条件运算符的参数都是类型，并且该运算符可以嵌套使用。
    >
    > 比如：
    >
    > ```ts
    > type T = 1 extends number ? true : false;  // true
    > ```
    >
    > 上面式子中，`1`是`number`的子类型，所以返回`true`类型。

    > 如果对泛型使用`extends...?:`，有一个地方需要注意。当泛型的类型参数是一个联合类型时，那么`extends...?:`运算符会展开这个类型参数，即`T<A|B> = T<A> | T<B>`，所以 extends 对类型参数的每个部分是分别计算的。也就是说，如果类型参数是联合类型，条件运算的返回结果依然是一个联合类型。
    >
    > ```ts
    > type Cond<T> = T extends U ? X : Y;
    > 
    > type MyType = Cond<A|B>;
    > // 等同于 Cond<A> | Cond<B>
    > // 等同于 (A extends U ? X : Y) | (B extends U ? X : Y)
    > ```
    >
    > *如果不想分别计算，有一个小窍门是：将泛型参数放在`[T]`中，这样类型就变成了一个元组。（了解即可，不重要）*

  - ##### `infer` 关键字

    > `infer`关键字用来定义泛型里面推断出来的类型参数，而不是外部传入的类型参数。
    >
    > 它通常跟上方的条件运算符一起使用，用在`extends`关键字后面的父类型之中。用法：
    >
    > ```ts
    > type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
    > ```
    >
    > - `T extends (...args: any[]) => infer R`：检查 `T` 是否为函数类型。
    > - `infer R`：如果 `T` 是函数类型，推断其返回类型为 `R`。
    > - `? R : never`：如果是函数类型，返回 `R`，否则返回 `never`。

    > 下面是`infer`提取对象指定属性的例子。
    >
    > ```ts
    > type MyType<T> =
    >     T extends {
    >         a: infer M,
    >         b: infer N
    >     } ? [M, N] : never;
    > 
    > // 用法示例
    > type T = MyType<{ a: string; b: number }>;
    > // [string, number]
    > ```
    >
    > 上面示例中，`infer`提取了参数对象的属性`a`和属性`b`的类型。
    >
    > 下面是`infer`通过正则匹配提取类型参数的例子。
    >
    > ```ts
    > type Str = 'foo-bar';
    > type Bar = Str extends `foo-${infer rest}` ? rest : never // 'bar'
    > ```
    >
    > 上面示例中，`rest`是从模板字符串提取的类型参数。

  - ##### `is` 运算符

    > `is`运算符用来**描述**返回值属于`true`还是`false`。当函数返回布尔类型值时，可以使用`is`运算符，限定返回的布尔值与参数之间的关系（通常用于类型保护函数）。
    >
    > ```ts
    > function isFish(pet: Fish|Bird): pet is Fish {
    > 	return (pet as Fish).swim !== undefined;
    > }
    > ```
    >
    > 上面示例中，函数`isFish()`的返回值类型为`pet is Fish`（也就是`boolean`），它告诉编译器：表示如果参数`pet`类型为`Fish`，则返回`true`，否则返回`false`。
    >
    > `is`运算符总是用于描述函数的返回值类型，写法采用`parameterName is Type`的形式，即左侧为当前函数的参数名，右侧为某一种类型。它返回一个布尔值，表示左侧参数是否属于右侧的类型。

  - ##### 模板字符串

    > TS 中的模板字符串还可以用来**构建类型**。TS 中的模板字符串的特点就是：内部可以引用其他类型。
    >
    > ```ts
    > type World = "world";
    > type Greeting = `hello ${World}`;  // 类型为："hello world"
    > ```
    >
    > 上面示例中，类型`Greeting`是一个模板字符串，里面引用了另一个字符串类型`world`，因此类型`Greeting`实际上是字符串`hello world`。
    >
    > 模板字符串可以引用的类型一共7种，分别是：`string、number、bigint、boolean、null、undefined、Enum`。引用这7种之外的其他类型会报错。
    >
    > ```ts
    > type Num = 123;
    > type Obj = { n : 123 };
    > 
    > type T1 = `${Num} received`; // 正确
    > type T2 = `${Obj} received`; // 报错
    > ```
    >
    > 模板字符串里面引用的类型，如果是一个联合类型，那么它返回的也是一个联合类型，即模板字符串可以展开联合类型。
    >
    > ```ts
    > type T = 'A'|'B';
    > type U = `${T}_id`;  // "A_id"|"B_id"
    > ```
    >
    > 如果模板字符串引用两个联合类型，它会交叉展开这两个类型。
    >
    > ```ts
    > type T = 'A'|'B';
    > type U = '1'|'2';
    > type V = `${T}${U}`;  // 'A1'|'A2'|'B1'|'B2'
    > ```

  - ##### `satisfies` 运算符

    > TS中的 `satisfies` 运算符用于检查一个值是否满足某个类型，但不会改变值的类型推断，不满足则编译报错。
    >
    > ```ts
    > type Colors = "red" | "green" | "blue";
    > const myColor = "red" satisfies Colors; // OK
    > const invalidColor = "yellow" satisfies Colors; // 编译报错："yellow" 不是 Colors 类型
    > ```

- ### 类型映射

  > 映射（mapping）指的是，将一种类型按照映射规则，转换成另一种类型，通常用于接口类型。用法：
  >
  > ```ts
  > type A = {
  >     foo: number;
  >     bar: number;
  > };
  > type B = {
  >     foo: string;
  >     bar: string;
  > };
  > ```
  >
  > 上面示例中，这两个类型的属性结构是一样的，但是属性的类型不一样。如果属性数量多的话，逐个写起来就很麻烦。
  >
  > 使用类型映射，就可以从类型`A`得到类型`B`。
  >
  > ```ts
  > type A = {
  >     foo: number;
  >     bar: number;
  > };
  > type B = {
  > 	[prop in keyof A]: string;
  > };
  > ```
  >
  > 上面示例中，类型`B`采用了属性名索引的写法，`[prop in keyof A]`表示依次得到类型`A`的所有属性名，然后将每个属性的类型改成`string`。
  >
  > 在语法上，`[prop in keyof A]`是一个属性名表达式，表示这里的属性名需要计算得到。具体的计算规则如下：
  >
  > - `prop`：属性名变量，名字可以随便起。
  > - `in`：运算符，遍历右侧的联合类型的每一个成员。
  > - `keyof A`：返回类型`A`的每一个属性名，组成一个联合类型。
  >
  > 下面是复制原类型的例子。
  >
  > ```ts
  > type A = {
  >     foo: number;
  >     bar: string;
  > };
  > 
  > type B = {
  > 	[prop in keyof A]: A[prop];
  > };
  > ```
  >
  > 上面示例中，类型`B`原样复制了类型`A`。
  >
  > 为了增加代码复用性，可以把常用的映射写成泛型。
  >
  > ```ts
  > type ToBoolean<Type> = {
  > 	[Property in keyof Type]: boolean;
  > };
  > ```
  >
  > 上面示例中，定义了一个泛型，可以将其他对象的所有属性值都改成 boolean 类型。
  >
  > 下面是另一个例子。
  >
  > ```ts
  > type MyObj = {
  > 	[P in 0|1|2]: string;
  > };
  > // 等同于
  > type MyObj = {
  >     0: string;
  >     1: string;
  >     2: string;
  > };
  > ```
  >
  > 上面示例中，联合类型`0|1|2`映射成了三个属性名。
  >
  > 不使用联合类型，直接使用某种具体类型进行属性名映射，也是可以的。
  >
  > ```ts
  > type MyObj = {
  > 	[p in 'foo']: number;
  > };
  > 
  > // 等同于
  > type MyObj = {
  > 	foo: number;
  > };
  > ```
  >
  > 上面示例中，`p in 'foo'`可以看成只有一个成员的联合类型，因此得到了只有这一个属性的对象类型。
  >
  > 甚至还可以写成`p in string`。
  >
  > ```ts
  > type MyObj = {
  > 	[p in string]: boolean;
  > };
  > 
  > // 等同于
  > type MyObj = {
  > 	[p: string]: boolean;
  > };
  > ```
  >
  > 上面示例中，`[p in string]`就是属性名索引形式`[p: string]`的映射写法。
  >
  > 通过映射，可以把某个对象的所有属性改成可选属性。
  >
  > ```ts
  > type A = {
  >     a: string;
  >     b: number;
  > };
  > 
  > type B = {
  > 	[Prop in keyof A]?: A[Prop];
  > };
  > ```
  >
  > 上面示例中，类型`B`在类型`A`的所有属性名后面添加问号，使得这些属性都变成了可选属性。
  >
  > 事实上，TS 的内置工具类型`Partial<T>`，就是这样实现的。
  >
  > TS内置的工具类型`Readonly<T>`可以将所有属性改为只读属性，实现也是通过映射。
  >
  > ```ts
  > // 将 T 的所有属性改为只读属性
  > type Readonly<T> = {
  > 	readonly [P in keyof T]: T[P];
  > };
  > ```

  - ##### 映射修饰符

    > 映射会原样复制原始对象的可选属性和只读属性：
    >
    > ```ts
    > type A = {
    >     a?: string;
    >     readonly b: number;
    > };
    > type B = {
    > 	[Prop in keyof A]: A[Prop];
    > };
    > // 等同于
    > type B = {
    >     a?: string;
    >     readonly b: number;
    > };
    > ```

    > 如果要删改可选和只读这两个特性，并不是很方便。为了解决这个问题，TS 引入了两个映射修饰符，用来在映射时添加或移除某个属性的`?`修饰符和`readonly`修饰符。
    >
    > - `+`修饰符：写成`+?`或`+readonly`，为映射属性添加`?`修饰符或`readonly`修饰符。
    > - `–`修饰符：写成`-?`或`-readonly`，为映射属性移除`?`修饰符或`readonly`修饰符。
    >
    > 下面是添加或移除可选和只读属性的例子。
    >
    > ```ts
    > // 添加可选属性
    > type Optional<Type> = {
    > 	[Prop in keyof Type]+?: Type[Prop];
    > };
    > // 移除可选属性
    > type Concrete<Type> = {
    > 	[Prop in keyof Type]-?: Type[Prop];
    > };
    > 
    > // 添加 readonly
    > type CreateImmutable<Type> = {
    >     +readonly [Prop in keyof Type]: Type[Prop];
    > };
    > // 移除 readonly
    > type CreateMutable<Type> = {
    >     -readonly [Prop in keyof Type]: Type[Prop];
    > };
    > ```
    >
    > 注意：`+?`或`-?`要写在属性名的后面；`+readonly`和`-readonly`要写在属性名的前面。
    >
    > 如果同时增删`?`和`readonly`这两个修饰符，写成下面这样。
    >
    > ```ts
    > // 增加
    > type MyObj<T> = {
    > 	+readonly [P in keyof T]+?: T[P];
    > };
    > // 移除
    > type MyObj<T> = {
    > 	-readonly [P in keyof T]-?: T[P];
    > }
    > ```
    >
    > 另外，`+?`修饰符可以简写成`?`，`+readonly`修饰符可以简写成`readonly`。
    >
    > ```ts
    >type A<T> = {
    > 	+readonly [P in keyof T]+?: T[P];
    > };
    > // 等同于
    > type A<T> = {
    > 	readonly [P in keyof T]?: T[P];
    > };
    > ```
    
  - ##### 键名重映射

    > TS 4.1 引入了键名重映射（key remapping），允许用`as`改变接口的键名。
    >
    > ```ts
    > type A = {
    >     foo: number;
    >     bar: number;
    > };
    > 
    > type B = {
    > 	[p in keyof A as `${p}ID`]: number;
    > };
    > 
    > // 等同于
    > type B = {
    >     fooID: number;
    >     barID: number;
    > };
    > ```
    >
    > 可以看到，键名重映射的语法是在键名映射的后面加上`as + 新类型`子句。这里的“新类型”通常是一个模板字符串，里面可以对原始键名进行各种操作。
    >
    > 下面是另一个例子。
    >
    > ```ts
    > interface Person {
    >     name: string;
    >     age: number;
    >     location: string;
    > }
    > 
    > type Getters<T> = {
    >     [P in keyof T
    >     	as `get${Capitalize<string & P>}`]: () => T[P];
    > };
    > 
    > type LazyPerson = Getters<Person>;
    > // 等同于
    > type LazyPerson = {
    >     getName: () => string;
    >     getAge: () => number;
    >     getLocation: () => string;
    > }
    > ```
    >
    > 上面示例中，类型`LazyPerson`是类型`Person`的映射，并且把键名改掉了。
    >
    > 它的修改键名的代码是一个模板字符串`get${Capitalize<string & P>}`，下面是各个部分的解释。
    >
    > - `get`：为键名添加的前缀。
    > - `Capitalize<T>`：一个原生的工具泛型，用来将`T`的首字母变成大写。
    > - `string & P`：一个交叉类型，其中的`P`是 keyof 运算符返回的键名联合类型`string|number|symbol`，但是`Capitalize<T>`只能接受字符串作为类型参数，因此`string & P`只返回`P`的字符串属性名。
  
    - **属性过滤：**

      > 键名重映射还可以过滤掉某些属性。下面的例子是只保留字符串属性。
      >
      > ```ts
      > type User = {
      >   name: string,
      >   age: number
      > }
      > 
      > type Filter<T> = {
      >   [K in keyof T
      >     as T[K] extends string ? K : never]: string
      > }
      > 
      > type FilteredUser = Filter<User> // { name: string }
      > ```
      >
      > 上面示例中，映射`K in keyof T`获取类型`T`的每一个属性以后，然后使用`as Type`修改键名。
      >
      > 它的键名重映射`as T[K] extends string ? K : never]`，使用了条件运算符。如果属性值`T[K]`的类型是字符串，那么属性名不变，否则属性名类型改为`never`，即这个属性名不存在。这样就等于过滤了不符合条件的属性，只保留属性值为字符串的属性。
  
    - **联合类型的映射：**

      > 由于键名重映射可以修改键名类型，所以原始键名的类型不必是`string|number|symbol`，任意的联合类型都可以用来进行键名重映射。
      >
      > ```ts
      > type S = {
      >     kind: 'square',
      >     x: number,
      >     y: number,
      > };
      > 
      > type C = {
      >     kind: 'circle',
      >     radius: number,
      > };
      > 
      > type MyEvents<Events extends { kind: string }> = {
      > 	[E in Events as E['kind']]: (event: E) => void;
      > }
      > 
      > type Config = MyEvents<S|C>;
      > // 等同于
      > type Config = {
      >     square: (event:S) => void;
      >     circle: (event:C) => void;
      > }
      > ```
      >
      > 上面示例中，原始键名的映射是`E in Events`，这里的`Events`是两个对象组成的联合类型`S|C`。所以，`E`是一个接口，然后再通过键名重映射，得到字符串键名`E['kind']`。
  
- ### TS中的装饰器（Decorator）

  - ##### 概述：

    > 装饰器（Decorator）是一种语法结构，用来在定义时修改类（class）的行为。
    >
    > 在语法上，装饰器有如下几个特征。
    >
    > 1. 第一个字符（或者说前缀）是`@`，后面是一个表达式。
    > 2. `@`后面的表达式，必须是一个函数（或者执行后可以得到一个函数）。
    > 3. 这个函数接受所修饰对象的一些相关值作为参数。
    > 4. 这个函数要么不返回值，要么返回一个新对象取代所修饰的目标对象。
    >
    > 举例来说，有一个函数`Injectable()`当作装饰器使用，那么需要写成`@Injectable`，然后放在某个类的前面。
    >
    > ```ts
    > @Injectable class A {/* ... */}
    > ```
    >
    > 上面示例中，由于有了装饰器`@Injectable`，类`A`的行为在运行时就会发生改变。下面就是一个最简单的装饰器：
    >
    > ```ts
    > function simpleDecorator() { console.log('hi'); }
    > 
    > @simpleDecorator
    > class A {} // "hi"
    > ```
    >
    > 上面示例中，函数`simpleDecorator()`用作装饰器，附加在类`A`之上，后者在类代码解析时就会打印一行日志。
    >
    > 编译上面的代码会报错，提示没有用到装饰器的参数。现在就为装饰器加上参数，让它更像正式运行的代码：
    >
    > ```ts
    > function simpleDecorator(value:any, context:any) {
    >     console.log(`hi, this is ${context.kind} ${context.name}`);
    >     return value;
    > }
    > 
    > @simpleDecorator
    > class A {} // "hi, this is class A"
    > ```
    >
    > 上面的代码就可以顺利通过编译了，代码含义这里先不解释。大家只要理解，类`A`在执行前会先执行装饰器`simpleDecorator()`，并且会向装饰器自动传入参数就可以了。
    >
    > 装饰器有多种形式，基本上只要在`@`符号后面添加表达式都是可以的。下面都是合法的装饰器：
    >
    > ```ts
    > @myFunc
    > @myFuncFactory(arg1, arg2)
    > 
    > @libraryModule.prop
    > @someObj.method(123)
    > 
    > @(wrap(dict['prop']))
    > ```
    >
    > 注意，`@`后面的表达式，最终执行后得到的应该是一个函数。
    >
    > 相比使用子类改变父类，装饰器更加简洁优雅，缺点是不那么直观，功能也受到一些限制。所以，装饰器一般只用来为类添加某种特定行为。
    >
    > ```ts
    > @frozen
    > class Foo {
    >     @configurable(false)
    >     @enumerable(true)
    >     method() {}
    > 
    >     @throttle(500)
    >     expensiveMethod() {}
    > }
    > ```
    >
    > 上面示例中，一共有四个装饰器，一个用在类本身（`@frozen`），另外三个用在类的方法（`@configurable`、`@enumerable`、`@throttle`）。它们不仅增加了代码的可读性，清晰地表达了意图，而且提供一种方便的手段，增加或修改类的功能。

  - ##### 装饰器的版本：

    > TS 其实从早期开始就支持装饰器。但是，后来 ECMAScript 规范草案中的装饰器语法发生了变化，ECMAScript 标准委员会最终通过的语法标准，与 TS 早期使用的语法有很大差异。
    >
    > 因此，TS 5.0 同时支持两种装饰器语法。最新的标准语法可以直接使用，传统语法需要打开`--experimentalDecorators`编译参数。
    >
    > ```shell
    > $ tsc --target ES5 --experimentalDecorators
    > ```
    >
    > 若今后，装饰器的标准语法真正成为新一代的 ES 标准之后，传统语法就会被淘汰掉了，因此这里不再介绍传统语法了。

  - ##### 装饰器的结构：

    > 装饰器（函数）的类型定义如下：
    >
    > ```ts
    > type Decorator = (
    >     value: DecoratedValue,
    >     context: {
    >         kind: string;
    >         name: string | symbol;
    >         addInitializer?(initializer: () => void): void;
    >         static?: boolean;
    >         private?: boolean;
    >         access: {
    >             get?(): unknown;
    >             set?(value: unknown): void;
    >         };
    >     }
    > ) => void | ReplacementValue;
    > ```
    >
    > 上面代码中，`Decorator`是装饰器的类型定义。它是一个函数，使用时会接收到`value`和`context`两个参数：
    >
    > - `value`：所装饰的对象。
    > - `context`：上下文对象，TS 提供一个原生接口`ClassMethodDecoratorContext`用于描述该对象。
    >
    > `context`对象的属性，根据所装饰对象的不同而不同，其中只有两个属性（`kind`和`name`）是必须的，其他都是可选的：
    >
    > 1. `kind`：字符串，表示所装饰对象的类型，可能取以下的值。（这表示一共有六种类型的装饰器）
    >    - 'class'
    >    - 'method'
    >    - 'getter'
    >    - 'setter'
    >    - 'field'
    >    - 'accessor'
    > 2. `name`：字符串或者 Symbol 值，它是所装饰对象的名字，比如类名、属性名等。
    > 3. `addInitializer()`：用来添加所装饰的对象的初始化逻辑。比如之前，这些逻辑通常放在构造函数里面，对方法进行初始化，现在改成以**初始化函数**的形式传入`addInitializer()`方法参数中。
    > 4. `private`：布尔值，表示所装饰的对象是否为类的私有成员。
    > 5. `static`：布尔值，表示所装饰的对象是否为类的静态成员。
    > 6. `access`：一个对象，包含了某个值的 get 和 set 方法，即存取器。

  - ##### 类装饰器：

    > 类装饰器的类型描述如下：
    >
    > ```ts
    > type ClassDecorator = (
    >     value: Function,
    >     context: {
    >         kind: 'class';
    >         name: string | undefined;
    >         addInitializer(initializer: () => void): void;
    >     }
    > ) => Function | void;
    > ```
    >
    > 类装饰器接受两个参数：`value`（当前类本身）和`context`（上下文对象）。
    >
    > 其中，`context`对象的`kind`属性固定为字符串`class`；`context.addInitializer()`方法用来定义一个类的初始化函数，在类完全定义结束后执行。
    >
    > 类装饰器一般用来对类进行操作，可以不返回任何值，请看下面的例子：
    >
    > ```ts
    > function Greeter(value, context) {
    >     if (context.kind === 'class') {
    >         value.prototype.greet = function () {
    >         	console.log('你好');
    >         };
    >     }
    > }
    > 
    > @Greeter
    > class User {}
    > 
    > let u = new User();
    > u.greet(); // "你好"
    > ```
    >
    > 上面示例中，类装饰器`@Greeter`在类`User`的原型对象上，添加了一个`greet()`方法，实例就可以直接使用该方法。
    >
    > **类装饰器可以返回一个函数，替代当前类的构造方法。**
    >
    > ```ts
    > function countInstances(value:any, context:any) {
    >     let instanceCount = 0;
    > 
    >     const wrapper = function (...args:any[]) {
    >         instanceCount++;
    >         const instance = new value(...args);
    >         instance.count = instanceCount;
    >         return instance;
    >     } as unknown as typeof MyClass;
    > 
    >     wrapper.prototype = value.prototype; // A
    >     return wrapper;
    > }
    > 
    > @countInstances
    > class MyClass {}
    > 
    > const inst1 = new MyClass();
    > inst1 instanceof MyClass // true
    > inst1.count // 1
    > ```
    >
    > 上面示例中，类装饰器`@countInstances`返回一个函数，替换了类`MyClass`的构造方法。新的构造方法实现了实例的计数，每新建一个实例，计数器就会加一，并且对实例添加`count`属性，表示当前实例的编号。
    >
    > 注意，上例为了确保新构造方法继承定义在`MyClass`的原型之上的成员，特别加入`A`行，确保两者的原型对象是一致的。否则，新的构造函数`wrapper`的原型对象，与`MyClass`不同，通不过`instanceof`运算符。
    >
    > **类装饰器也可以返回一个新的类，替代原来所装饰的类。**
    >
    > ```ts
    > function countInstances(value:any, context:any) {
    >     let instanceCount = 0;
    > 
    >     return class extends value {
    >         constructor(...args:any[]) {
    >             super(...args);
    >             instanceCount++;
    >             this.count = instanceCount;
    >         }
    >     };
    > }
    > 
    > @countInstances
    > class MyClass {}
    > 
    > const inst1 = new MyClass();
    > inst1 instanceof MyClass // true
    > inst1.count // 1
    > ```
    >
    > 上面示例中，`@countInstances`返回一个`MyClass`的子类。
    >
    > 类装饰器的上下文对象`context`的`addInitializer()`方法，用来定义一个类的初始化函数，在类完全定义结束后执行。
    >
    > ```ts
    > function customElement(name: string) {
    >     return <Input extends new (...args: any) => any>(value: Input, context: ClassDecoratorContext) => {
    >         context.addInitializer(function () {
    >         	customElements.define(name, value);
    >         });
    >     };
    > }
    > 
    > @customElement("hello-world")
    > class MyComponent extends HTMLElement {
    >     constructor() {
    > 	    super();
    >     }
    >     connectedCallback() {
    > 	    this.innerHTML = `<h1>Hello World</h1>`;
    >     }
    > }
    > ```
    >
    > 上面示例中，类`MyComponent`定义完成后，会自动执行类装饰器`@customElement()`给出的初始化函数，该函数会将当前类注册为指定名称（本例为`<hello-world>`）的自定义 HTML 元素。

  - ##### 方法装饰器：

    > 方法装饰器用来装饰类中的方法（method）。它的类型描述如下：
    >
    > ```ts
    > type ClassMethodDecorator = (
    >     value: Function,
    >     context: {
    >         kind: 'method';
    >         name: string | symbol;
    >         static: boolean;
    >         private: boolean;
    >         access: { get: () => unknown };
    >         addInitializer(initializer: () => void): void;
    >     }
    > ) => Function | void;
    > ```
    >
    > 方法装饰器接受两个参数：`value`（当前方法本身）和`context`（上下文对象）。
    >
    > 其中，`context`对象的`kind`属性固定为字符串`method`；`context.addInitializer()`方法用来为方法添加一个初始化函数，它会在构造方法执行期间执行，早于属性（field）的初始化（**对于实例成员，`addInitializer`注册的函数会在构造函数执行时调用；对于静态成员，`addInitializer`注册的函数会在类定义时调用**）；`context.access`是一个对象，包含了方法的存取器，但是只有`get()`方法用来取值，没有`set()`方法进行赋值。
    >
    > 方法装饰器会改写类的原始方法，实质等同于下面的操作。
    >
    > ```ts
    > function trace(decoratedMethod) {
    >   // ...
    > }
    > 
    > class C {
    >   @trace
    >   toString() {
    >     return 'C';
    >   }
    > }
    > 
    > // `@trace` 等同于
    > // C.prototype.toString = trace(C.prototype.toString);
    > ```
    >
    > 上面示例中，`@trace`是方法`toString()`的装饰器，它的效果等同于最后一行对`toString()`的改写。
    >
    > **如果方法装饰器返回一个新的函数，就会替代所装饰的原始函数。**
    >
    > ```ts
    > function replaceMethod() {
    >   return function () {
    >     return `How are you, ${this.name}?`;
    >   }
    > }
    > 
    > class Person {
    >   constructor(name) {
    >     this.name = name;
    >   }
    > 
    >   @replaceMethod
    >   hello() {
    >     return `Hi ${this.name}!`;
    >   }
    > }
    > 
    > const robin = new Person('Robin');
    > 
    > robin.hello() // 'How are you, Robin?'
    > ```
    >
    > 上面示例中，装饰器`@replaceMethod`返回的函数，就成为了新的`hello()`方法。
    >
    > 下面是另一个例子。
    >
    > ```ts
    > class Person {
    >   name: string;
    >   constructor(name: string) {
    >     this.name = name;
    >   }
    > 
    >   @log
    >   greet() {
    >     console.log(`Hello, my name is ${this.name}.`);
    >   }
    > }
    > 
    > function log(originalMethod:any, context:ClassMethodDecoratorContext) {
    >   const methodName = String(context.name);
    > 
    >   function replacementMethod(this: any, ...args: any[]) {
    >     console.log(`LOG: Entering method '${methodName}'.`)
    >     const result = originalMethod.call(this, ...args);
    >     console.log(`LOG: Exiting method '${methodName}'.`)
    >     return result;
    >   }
    > 
    >   return replacementMethod;
    > }
    > 
    > const person = new Person('张三');
    > person.greet()
    > // "LOG: Entering method 'greet'."
    > // "Hello, my name is 张三."
    > // "LOG: Exiting method 'greet'."
    > ```
    >
    > 上面示例中，装饰器`@log`的返回值是一个函数`replacementMethod`，替代了原始方法`greet()`。在`replacementMethod()`内部，通过执行`originalMethod.call()`完成了对原始方法的调用。
    >
    > 利用方法装饰器，可以将类的方法变成延迟执行：
    >
    > ```ts
    > function delay(milliseconds: number = 0) {
    >   return function (value, context) {
    >     if (context.kind === "method") {
    >       return function (...args: any[]) {
    >         setTimeout(() => {
    >           value.apply(this, args);
    >         }, milliseconds);
    >       };
    >     }
    >   };
    > }
    > 
    > class Logger {
    >   @delay(1000)
    >   log(msg: string) {
    >     console.log(`${msg}`);
    >   }
    > }
    > 
    > let logger = new Logger();
    > logger.log("Hello World");
    > ```
    >
    > 上面示例中，方法装饰器`@delay(1000)`将方法`log()`的执行推迟了1秒（1000毫秒）。这里真正的方法装饰器，是`delay()`执行后返回的函数，`delay()`的作用是接收参数，用来设置推迟执行的时间。这种通过高阶函数返回装饰器的做法，称为“工厂模式”，即可以像工厂那样生产出一个模子的装饰器。
    >
    > 方法装饰器的参数`context`对象里面，有一个`addInitializer()`方法。它是一个钩子方法，用来在类的初始化阶段，添加回调函数。这个回调函数就是作为`addInitializer()`的参数传入的，它会在构造方法执行期间执行，早于属性（field）的初始化。
    >
    > 下面是`addInitializer()`方法的一个例子。我们知道，类的方法往往需要在构造方法里面，进行`this`的绑定。
    >
    > ```ts
    > class Person {
    >   name: string;
    >   constructor(name: string) {
    >     this.name = name;
    > 
    >     // greet() 绑定 this
    >     this.greet = this.greet.bind(this);
    >   }
    > 
    >   greet() {
    >     console.log(`Hello, my name is ${this.name}.`);
    >   }
    > }
    > 
    > const g = new Person('张三').greet;
    > g() // "Hello, my name is 张三."
    > ```
    >
    > 上面例子中，类`Person`的构造方法内部，将`this`与`greet()`方法进行了绑定。如果没有这一行，将`greet()`赋值给变量`g`进行调用，就会报错了。
    >
    > `this`的绑定必须放在构造方法里面，因为这必须在类的初始化阶段完成。现在，它可以移到方法装饰器的`addInitializer()`里面。
    >
    > ```ts
    > function bound(originalMethod:any, context:ClassMethodDecoratorContext) {
    >   const methodName = context.name;
    >   if (context.private) {
    >     throw new Error(`不能绑定私有方法 ${methodName as string}`);
    >   }
    >   context.addInitializer(function () {
    >     this[methodName] = this[methodName].bind(this);
    >   });
    > }
    > ```
    >
    > 上面示例中，绑定`this`转移到了`addInitializer()`方法里面。
    >
    > 下面再看一个例子，通过`addInitializer()`将选定的方法名，放入一个集合。
    >
    > ```ts
    > function collect(
    >   value,
    >   {name, addInitializer}
    > ) {
    >   addInitializer(function () {
    >     if (!this.collectedMethodKeys) {
    >       this.collectedMethodKeys = new Set();
    >     }
    >     this.collectedMethodKeys.add(name);
    >   });
    > }
    > 
    > class C {
    >   @collect
    >   toString() {}
    > 
    >   @collect
    >   [Symbol.iterator]() {}
    > }
    > 
    > const inst = new C();
    > inst.collectedMethodKeys // new Set(['toString', Symbol.iterator])
    > ```
    >
    > 上面示例中，方法装饰器`@collect`会将所装饰的成员名字，加入一个 Set 集合`collectedMethodKeys`。

  - ##### 属性装饰器：

    > 属性装饰器用来装饰定义在类顶部的属性（field）。它的类型描述如下：
    >
    > ```ts
    > type ClassFieldDecorator = (
    >     value: undefined,
    >     context: {
    >         kind: 'field';
    >         name: string | symbol;
    >         static: boolean;
    >         private: boolean;
    >         access: { get: () => unknown, set: (value: unknown) => void };
    >         addInitializer(initializer: () => void): void;
    >     }
    > ) => (initialValue: unknown) => unknown | void;
    > ```
    >
    > 注意：属性装饰器的第一个参数`value`的类型是`undefined`，这意味着这个参数实际上没用的，装饰器不能从`value`获取所装饰属性的值。
    >
    > 另外，第二个参数`context`对象的`kind`属性的值固定为字符串`field`；`context.access`是一个对象，包含了属性的存取器。`context.addInitializer()`方法用来为属性添加一个初始化函数，它会在构造方法执行期间执行，早于属性（field）的初始化。
    >
    > **属性装饰器要么不返回值，要么返回一个函数，该函数会自动执行，用来对所装饰属性进行初始化。该函数的参数是所装饰属性的初始值，该函数的返回值是该属性的最终值。**（晚于添加的初始化函数）
    >
    > ```ts
    > function logged(value, context) {
    >   const { kind, name } = context;
    >   if (kind === 'field') {
    >     return function (initialValue) {
    >       console.log(`initializing ${name} with value ${initialValue}`);
    >       return initialValue;
    >     };
    >   }
    > }
    > 
    > class Color {
    >   @logged name = 'green';
    > }
    > 
    > const color = new Color();
    > // "initializing name with value green"
    > ```
    >
    > 上面示例中，属性装饰器`@logged`装饰属性`name`。`@logged`的返回值是一个函数，该函数用来对属性`name`进行初始化，它的参数`initialValue`就是属性`name`的初始值`green`。**新建实例对象`color`时**，该函数会自动执行。
    >
    > 属性装饰器的返回值函数，可以用来更改属性的初始值：
    >
    > ```ts
    > function twice() {
    >   	return initialValue => initialValue * 2;
    > }
    > 
    > class C {
    >       @twice
    >       field = 3;
    > }
    > 
    > const inst = new C();
    > inst.field // 6
    > ```
    >
    > 上面属性装饰器`@twice`返回一个函数，该函数的返回值是属性`field`的初始值乘以2，所以属性`field`的最终值是6。
    >
    > 属性装饰器的上下文对象`context`的`access`属性，提供所装饰属性的存取器，请看下面的例子。
    >
    > ```ts
    > let acc;
    > 
    > function exposeAccess(value, {access}) {
    >   	acc = access;
    > }
    >   
    > class Color {
    >     @exposeAccess
    >     name = 'green'
    >   }
    >   
    > const green = new Color();
    > green.name // 'green'
    > 
    > acc.get(green) // 'green'
    > 
    > acc.set(green, 'red');
    > green.name // 'red'
    > ```
    > 
    > 上面示例中，`access`包含了属性`name`的存取器，可以对该属性进行取值和赋值。
    
  - ##### `getter`装饰器和`setter` 装饰器：

    > getter 装饰器和 setter 装饰器，是分别针对类的取值器（getter）和存值器（setter）的装饰器。它们的类型描述如下：
    >
    > ```ts
    > type ClassGetterDecorator = (
    >       value: Function,
    >       context: {
    >            kind: 'getter';
    >            name: string | symbol;
    >            static: boolean;
    >            private: boolean;
    >            access: { get: () => unknown };
    >            addInitializer(initializer: () => void): void;
    >       }
    > ) => Function | void;
    > 
    > type ClassSetterDecorator = (
    >       value: Function,
    >       context: {
    >            kind: 'setter';
    >            name: string | symbol;
    >            static: boolean;
    >            private: boolean;
    >            access: { set: (value: unknown) => void };
    >            addInitializer(initializer: () => void): void;
    >       }
    > ) => Function | void;
    > ```
    >
    > 注意：getter 装饰器的上下文对象`context`的`access`属性，只包含`get()`方法；setter 装饰器的`access`属性，只包含`set()`方法。
    >
    > **这两个装饰器要么不返回值，要么返回一个函数，取代原来的取值器或存值器。**
    >
    > 下面的例子是将取值器的结果，保存为一个属性，加快后面的读取。
    >
    > ```ts
    > class C {
    >   @lazy
    >   get value() {
    >     console.log('正在计算……');
    >     return '开销大的计算结果';
    >   }
    > }
    > 
    > function lazy(
    >   value:any,
    >   {kind, name}:any
    > ) {
    >   if (kind === 'getter') {
    >     return function (this:any) {
    >       const result = value.call(this);
    >       Object.defineProperty(
    >         this, name,
    >         {
    >           value: result,
    >           writable: false,
    >         }
    >       );
    >       return result;
    >     };
    >   }
    >   return;
    > }
    > 
    > const inst = new C();
    > inst.value
    > // 正在计算……
    > // '开销大的计算结果'
    > inst.value
    > // '开销大的计算结果'
    > ```
    >
    > 上面示例中，第一次读取`inst.value`，会进行计算，然后装饰器`@lazy`将结果存入只读属性`value`，后面再读取这个属性，就不会进行计算了。
  
  - ##### accessor（存取器）装饰器：

    > 装饰器语法引入了一个新的属性修饰符`accessor`，可以为属性自动生成对应的`getter`和`setter`：
    >
    > ```ts
    > class C {
    > 	accessor x = 1;
    > }
    > ```
    >
    > 上面示例中，`accessor`修饰符等同于为公开属性`x`自动生成取值器和存值器，它们作用于私有属性`x`。（注意，公开的`x`与私有的`x`不是同一个属性）也就是说，上面的代码等同于下面的代码。
    >
    > ```ts
    > class C {
    >     #x = 1;
    > 
    >     get x() {
    >     	return this.#x;
    >     }
    > 
    >     set x(val) {
    >     	this.#x = val;
    >     }
    > }
    > ```
    >
    > `accessor`也可以与静态属性和私有属性一起使用。
    >
    > ```ts
    > class C {
    >     static accessor x = 1;
    >     accessor #y = 2;
    > }
    > ```
    >
    > accessor 装饰器（装饰 accessor 属性）的类型如下：
    >
    > ```ts
    > type ClassAutoAccessorDecorator = (
    >     value: {
    >         get: () => unknown;
    >         set: (value: unknown) => void;
    >     },
    >     context: {
    >         kind: "accessor";
    >         name: string | symbol;
    >         access: { get(): unknown, set(value: unknown): void };
    >         static: boolean;
    >         private: boolean;
    >         addInitializer(initializer: () => void): void;
    >     }
    > ) => {
    >     get?: () => unknown;
    >     set?: (value: unknown) => void;
    >     init?: (initialValue: unknown) => unknown;
    > } | void;
    > ```
    >
    > accessor 装饰器的`value`参数，是一个包含`get()`方法和`set()`方法的对象。该装饰器可以不返回值，或者返回一个新的对象，用来取代原来的`get()`方法和`set()`方法（内部默认生成的存取器）。此外，装饰器返回的对象还可以包括一个`init()`方法，用来改变私有属性的初始值。
    >
    > 下面是一个例子。
    >
    > ```ts
    > class C {
    > 	@logged accessor x = 1;
    > }
    > 
    > function logged(value, { kind, name }) {
    >     if (kind === "accessor") {
    >         let { get, set } = value;
    > 
    >         return {
    >             get() {
    >                 console.log(`getting ${name}`);
    >                 return get.call(this);
    >             },
    > 
    >             set(val) {
    >                 console.log(`setting ${name} to ${val}`);
    >                 return set.call(this, val);
    >             },
    > 
    >             init(initialValue) {
    >                 console.log(`initializing ${name} with value ${initialValue}`);
    >                 return initialValue;
    >             }
    >         };
    >     }
    > }
    > 
    > let c = new C();
    > 
    > c.x;
    > // getting x
    > 
    > c.x = 123;
    > // setting x to 123
    > ```
    >
    > 上面示例中，装饰器`@logged`为属性`x`的存值器和取值器，加上了日志输出。
  
  - ##### 装饰器的执行顺序：
  
    > 装饰器的执行分为两个阶段：
    >
    > 1. 评估（evaluation）：计算`@`符号后面的表达式的值，得到的应该是函数。
    > 2. 应用/执行（application/execute）：将评估装饰器后得到的装饰器（函数），应用于所装饰对象。也就是执行对应的装饰器函数。
    >
    > 也就是说，装饰器的执行顺序是，先评估所有装饰器表达式的值，再将其应用于当前类。
    >
    > 应用/执行装饰器时，顺序依次为：方法装饰器、属性装饰器、类装饰器。
    >
    > 请看下面的例子。
    >
    > ```ts
    > function d(str:string) {
    > console.log(`评估 @d(): ${str}`);
    > return (
    >  value:any, context:any
    > ) => console.log(`应用 @d(): ${str}`);
    > }
    > 
    > function log(str:string) {
    > console.log(str);
    > return str;
    > }
    > 
    > @d('类装饰器')
    > class T {
    > @d('静态属性装饰器')
    > static staticField = log('静态属性值');
    > 
    > @d('原型方法')
    > [log('计算方法名')]() {}
    > 
    > @d('实例属性')
    > instanceField = log('实例属性值');
    > 
    > @d('静态方法装饰器')
    > static fn(){}
    > }
    > ```
    >
    > 上面示例中，类`T`有四种装饰器：类装饰器、静态属性装饰器、方法装饰器、属性装饰器。
    >
    > 它的运行结果如下。
    >
    > ```txt
    > 评估 @d(): 类装饰器
    > 评估 @d(): 静态属性装饰器
    > 评估 @d(): 原型方法
    > 计算方法名
    > 评估 @d(): 实例属性
    > 评估 @d(): 静态方法装饰器
    > 应用 @d(): 静态方法装饰器
    > 应用 @d(): 原型方法
    > 应用 @d(): 静态属性装饰器
    > 应用 @d(): 实例属性
    > 应用 @d(): 类装饰器
    > 静态属性值
    > ```
    >
    > 可以看到，类载入的时候，代码按照以下顺序执行。
    >
    > （1）装饰器评估：这一步计算装饰器的值，首先是类装饰器，然后是类内部的装饰器，按照它们出现的顺序。
    >
    > 注意，如果属性名或方法名是计算值（本例是“计算方法名”），则它们在对应的装饰器评估之后，也会进行自身的评估。
    >
    > （2）装饰器应用：实际执行装饰器函数，将它们与对应的方法和属性进行结合。
    >
    > 静态方法装饰器首先应用，然后是原型方法的装饰器和静态属性装饰器，接下来是实例属性装饰器，最后是类装饰器。
    >
    > 注意：“实例属性值”在类初始化的阶段并不执行，直到类实例化时才会执行。
    >
    > 如果一个方法或属性有多个装饰器，则**内层的装饰器先执行（应用），外层的装饰器后执行**。
    >
    > ```ts
    > class Person {
    >     name: string;
    > 
    >     constructor(name: string) {
    > 	    this.name = name;
    >     }
    > 
    >     @bound
    >     @log
    >     greet() {
    > 	    console.log(`Hello, my name is ${this.name}.`);
    >     }
    > }
    > ```
    >
    > 上面示例中，`greet()`有两个装饰器，内层的`@log`先执行，外层的`@bound`针对得到的结果再执行。


------

