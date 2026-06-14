# TypeScript

- ### TS简介

  > - TypeScript（简称 TS）是微软公司开发的基于 JavaScript （简称 JS）语言的编程语言。它的目的并不是创造一种全新语言，而是增强 JavaScript 的功能，使其更适合多人合作的企业级项目。
  > - TS 可以看成是 JS 的超集（superset），即它继承了后者的全部语法，因此 JS 代码无需任何修改就是合法 TS 代码（虽然可能提示报错）。
  > - TS 对 JS 添加的最主要部分，就是一个独立的类型系统。
  > - **注意：TS 代码必须先编译为JS才能运行。**

- ### 为什么要用TS

  > JS 当年“出身简陋”，没考虑到如今的应用场景和代码量，在编写大型项目时会有很多困扰：
  >
  > - 不清不楚的数据类型。
  > - 有漏洞的逻辑。
  > - 访问不存在的属性。
  > - 低级的拼写错误。
  >
  > ###### 使用 TS 就可以避免以上问题：
  >
  > - TS 代码在运行前会先进行编译，编译时就可以发现代码的错误或不合理之处从而减小运行时出现异常的概率，这是 TS 这种**静态类型语言**的好处。
  > - 虽然同样的功能 TS 的代码量远远大于 JS，但由于 TS 代码的结构更清晰，在后期的维护中 TS 远胜于 JS。
  >
  > ###### 静态类型语言的优点：（静态类型语言的缺点是：没有动态类型语言的代码灵活、需要先进行编译等...）
  >
  > 1. 有利于代码的静态分析。
  >
  >    有了静态类型，不必运行代码，就可以确定变量的类型，从而推断代码有没有错误。这就叫做代码的静态分析。这对于大型项目非常重要，单单在开发阶段运行静态检查，就可以发现很多问题，避免交付有问题的代码，大大降低了线上风险。
  >
  > 2. 有利于发现错误。
  >
  >    由于每个值、每个变量、每个运算符都有严格的类型约束，TS 就能轻松发现拼写错误、语义错误和方法调用错误，节省程序员的时间。
  >
  > 3. 更好的 IDE 支持，做到语法提示和自动补全。
  >
  >    IDE（集成开发环境，比如 VSCode）一般都会利用类型信息，提供语法提示功能（编辑器自动提示函数用法、参数等）和自动补全功能（只键入一部分的变量名或函数名，编辑器补全后面的部分）。
  >
  > 4. 提供了代码文档。
  >
  >    类型信息可以部分替代代码文档，解释应该如何使用这些代码，熟练的开发者往往只看类型，就能大致推断代码的作用。借助类型信息，很多工具能够直接生成文档。
  >
  > 5. 有助于代码重构。
  >
  >    修改他人的 JS 代码往往非常痛苦，项目越大越痛苦，因为不确定修改后是否会影响到其他部分的代码。类型信息大大减轻了重构的成本。一般来说，只要函数或对象的参数和返回值保持类型不变，就能基本确定，重构后的代码也能正常运行。如果还有配套的单元测试，就完全可以放心重构。越是大型的、多人合作的项目，类型信息能够提供的帮助越大。
  >
  > 综上所述，TypeScript 有助于提高代码质量，保证代码安全，更适合用在大型的企业级项目。这就是为什么大量 JavaScript 项目转成 TypeScript 的原因。

- ### 运行 TS

  - ##### 手动编译：

    > 1. npm安装 TS 的编译器：`npm i -g typescript`
    > 2. 编译TS源代码（`.ts`结尾）：`tsc demo.ts`（此时默认会在当前目录下生成一个`demo.js`文件）
    >

    ###### VSCode中也可以用Code Runner插件来右键直接运行一个TS文件，但需要全局安装node和ts-node两个包。

    > 关于`ts-node`工具：它是一个非官方的、用于直接运行TS代码的工具，使用：（它基于node.js）
    > 
    >1. 全局安装：`npm install -g ts-node`
    > 2. 直接执行TS代码：`ts-node a.ts`
    >
    > 如果不全局安装`ts-node`，也可以通过`npx`调用它来运行TS代码：`npx ts-node a.ts`，`npx`会在线调用 ts-node工具，从而在不安装的情况下运行TS代码。如果执行 ts-node 命令不带有任何参数，它会提供一个 TS 的命令行 REPL 运行环境，你可以在这个环境中输入 TS 代码，逐步逐行执行。

  - ##### 自动化编译：

     1. 在项目根目录下创建TS编译的配置文件：`tsc --init`

        > 此时会在当前目录下生成一个`tsconfig.json`配置文件（后面会详细说），其中包含着很多编译时的配置。tsc命令编译时会自动在当前目录下找该编译配置文件，根据文件中的配置项来决定编译时如何处理。

        ###### 关于`jsconfig.json`文件：
        
        > - jsconfig.json文件是VSCode中的，旨在为纯 JS 项目提供类似TS的配置功能。该文件的出现时间稍晚于 tsconfig.json，大约在 2015 年左右。
        >- 也可以将jsconfig.json看作是tsconfig.json的简化版。因为jsconfig.json的许多概念和功能是从tsconfig.json中演变而来的。jsconfig.json可以看作是tsconfig.json中的`"allowJs"`属性设置为了`true`。
        > - jsconfig.json是为了满足纯 JS 项目的需求而创建的，它继承了tsconfig.json的许多配置项，并简化了一些不需要的选项。

     2. 监视根目录下所有层级的`.ts/.tsx`文件的变化，只要发生变化就自动编译：`tsc --watch`。生成的js文件默认放在了和ts源文件同一目录下。

     3. （可选）TS 文件报错时，编译后不生成 JS 文件，在`tsconfig.json`中：（注意：一般用于当作配置文件的json里面可以写注释）

        ```ts
        {
            "compilerOptions": {
                "noEmitOnError": true,
                // "noEmit": true,  // 仅当类型报错时，编译后不生成JS文件
            }
        }
        ```

- ### 关于TS

  > - TS是用来做类型检测的，只在编译阶段有效，和运行阶段无关。TS只是用来给程序员做代码提示的。
  > - JS 的运行环境（浏览器或`Node.js`）不认识 TS 代码。所以，TS 项目要想运行，必须先转为 JS 代码，这个代码转换的过程就叫做“编译”（compile）。
  > - TS 官方没有做运行环境，只提供编译器。编译时，会将类型声明和类型相关的代码全部删除，只留下能运行的 JS 代码，并且不会改变 JS 的运行结果。因此，TS 的类型检查只是编译时的类型检查，而不是运行时的类型检查。一旦代码编译为 JS，运行时就不再检查类型了。
  > - TS代码编译之后我们写的类型就消失了。但并不是说白写了，编译后可以选择给我们写的TS代码生成单独的`*.d.ts`**类型声明文件**（关于类型声明文件的作用，后面再说）。
  > - 我们学习 TS 主要学的就是如何写类型（如何给变量加约束）。TS中的类型大致分为：**基本类型、高级类型、内置类型、自定义类型、类型体操**。
  > - 除此之外，TS 还提前支持了一些还未在所有环境中普及的 ES 新特性，如装饰器（ES11）、异步迭代器等，且能够将其编译成兼容的 JS 版本。

- ### 类型声明

  > - TS 规定，定义变量时必须为其声明类型：`变量名:类型`。如`let a: string`，此时a的类型是string，只能存字符串，赋其他类型值会编译报错（虽然能够正常运行，但 TS 觉得你这里写的有问题，得做修改）。
  >
  >   > 另外 TS 还规定，变量只有赋值后才能使用，否则就会报错。
  >
  > - TS 支持**块级类型声明**，即类型可以写在代码块（或函数）里面，并且只在当前代码块（或函数）中有效。
  >
  > - 定义函数时的形参、返回值也要指定类型：
  >
  >   ```ts
  >   // 此时调用该函数时，必须传2个number型作为参数，不能多也不能少。且该函数返回值也是number型
  >   function demo (x: number, y: number): number{
  >   	return x + y
  >   }
  >   ```
  >
  >   > 调用该函数时，必须传2个number型作为参数，不能多也不能少。

  > ###### 自动类型推断：
  >
  > - 类型声明并不是必需的，当类型没有给出时，TS 编译器利用**自动类型推断**来推断变量的类型：
  >
  >   ```ts
  >   let num = 2  // num变量推断为 number 类型
  >   function toString(num: number) {  // 自动推断函数的返回值是 string 类型
  >   	return String(num)
  >   }
  >   ```
  >
  > - 从这里可以看到，TS 的设计思想是，类型声明是可选的，你可以加，也可以不加。即使不加类型声明，依然是有效的 TS 代码，只是不能保证 TS 会正确推断出类型。
  >
  > - 对于开发者没有指定类型、TS 必须自己推断类型的那些变量，如果无法推断出类型，TS 就会认为该变量的类型是`any`。应尽量避免any（后面会说any类型）。
  >
  > - 这样做的好处是：将以前的 JS 项目改为 TS 项目时，你可以逐步地为老代码添加类型，即使有些代码没有添加，也不会无法运行。正是因为这个，因此所有 JS 代码都是合法的 TS 代码。

  > ###### 类型的兼容：
  >
  > TS 的类型存在兼容关系（类似于集合），某些类型可以兼容其他类型：
  >
  > ```ts
  > type T = number | string
  > let a: number = 1
  > let b: T = a
  > ```
  >
  > - 上面示例中，变量`a`和`b`的类型是不一样的，但是变量`a`赋值给变量`b`并不会报错。这时，我们就认为，`b`的类型兼容`a`的类型（a类型的是b类型的子集）。TS 为这种情况定义了一个专门术语。如果类型`A`的值可以赋值给类型`B`，那么类型`A`就称为类型`B`的**子类型（subtype）**。在上例中，类型`number`就是类型`number|string`的子类型。
  > - 类似于其他静态类型语言，TS 中凡是可以使用父类型的地方，都可以使用子类型（反过来不行）。
  > - 之所以有这样的规则，是因为子类型继承了父类型的所有特征，所以可以用在父类型的场合。但是，子类型还可能有一些父类型没有的特征，对父类型做了扩展，所以父类型不能用在子类型的场合。
  >
  > ###### TS 的结构类型原则（structural typing）：
  >
  > 只要对象 B 满足 对象 A 的结构特征，TS 就认为对象 B 兼容对象 A 的类型，这称为“结构类型”原则（structural typing）。
  >
  > ```ts
  > type A = {
  >   x: number;
  > };
  > 
  > type B = {
  >   x: number;
  >   y: number;
  > };
  > ```
  >
  > 上面示例中，对象`A`只有一个属性`x`，类型为`number`。对象`B`满足这个特征，因此兼容对象`A`，只要可以使用`A`的地方，就可以使用`B`。
  >
  > ```ts
  > const B = {
  >   x: 1,
  >   y: 1
  > };
  > 
  > const A:{ x: number } = B; // 正确
  > ```
  >
  > 上面示例中，`A`和`B`并不是同一个类型，但是`B`可以赋值给`A`，因为`B`满足`A`的结构特征。
  >
  > 根据“结构类型”原则，TS 检查某个值是否符合指定类型时，并不是检查这个值的类型名（即“名义类型”），而是检查这个值的结构是否符合要求（即“结构类型”）。
  >
  > TS 之所以这样设计，是为了符合 JS 的行为。JS 并不关心对象是否严格相似，只要某个对象具有所要求的属性，就能正确运行。
  >
  > 如果类型 B 可以赋值给类型 A，TS 就认为 B 是 A 的子类型（subtyping），A 是 B 的父类型。子类型满足父类型的所有结构特征，同时还具有自己的特征。凡是可以使用父类型的地方，都可以使用子类型，即子类型兼容父类型。
  >
  > 这种设计有时会导致令人惊讶的结果。
  >
  > ```ts
  > type myObj = {
  >   x: number,
  >   y: number,
  > };
  > 
  > function getSum(obj:myObj) {
  >   let sum = 0;
  > 
  >   for (const n of Object.keys(obj)) {
  >     const v = obj[n]; // 报错
  >     sum += Math.abs(v);
  >   }
  > 
  >   return sum;
  > }
  > ```
  >
  > 上面示例中，函数`getSum()`要求传入参数的类型是`myObj`，但是实际上所有与`myObj`兼容的对象都可以传入。这会导致`const v = obj[n]`这一行报错，原因是`obj[n]`取出的属性值不一定是数值（`number`），使得变量`v`的类型被推断为`any`。如果项目设置为不允许变量类型推断为`any`，代码就会报错。写成下面这样，就不会报错。
  >
  > ```ts
  > type MyObj = {
  >   x: number,
  >   y: number,
  > };
  > 
  > function getSum(obj:MyObj) {
  >   return Math.abs(obj.x) + Math.abs(obj.y);
  > }
  > ```
  >
  > 上面示例就不会报错，因为函数体内部只使用了属性`x`和`y`，这两个属性有明确的类型声明，保证`obj.x`和`obj.y`肯定是数值。虽然与`MyObj`兼容的任何对象都可以传入函数`getSum()`，但是只要不使用其他属性，就不会有类型报错。

  > ###### 类型断言：
  >
  > 对于没有类型声明的值，TS 会进行类型推断，很多时候得到的结果，未必是开发者想要的。因此TS 提供了**类型断言**这样一种手段，允许开发者在代码中*断言*某个值的类型，告诉编译器此处的值是什么类型。TS 一旦发现存在类型断言，就不再对该值进行类型推断，而是直接采用断言给出的类型。（*编译器：你在教我做事？*）
  >
  > 类型断言可以让开发者明确告诉编译器变量的类型，常用于无法推断的情况。使用`as`或`<类型>`：（尖括号断言会在JSX中失效）
  >
  > ```ts
  > let someValue: any = "this is a string"
  > let strLength: number = (someValue as String).length
  > // 或者：(<String>someValue).length
  > ```
  >
  > **需要注意的是：**
  >
  > - 当 S 类型是 T 类型的子集，或 T 是 S 的子集时，S 才能被断言成 T。这是为了在进行类型断言时提供额外的安全性，完全毫无根据的断言是危险的，如果你想这么做，你可以直接用 any。
  > - 虽然也可以进行多重类型断言：`(obj as any) as number`，但要少用，因为它会破坏原有的关系。
  > - 和强制类型转换不同的是，类型断言是一个纯编译时语法，也是一种为编译器提供关于如何分析代码的方法。

- ### 非空断言

  > `!`是TS中的**非空断言运算符**，用于告诉编译器某个变量一定不是`null`或`undefined`，从而避免类型检查错误。用法：`变量!`。
  >
  > **注意：**非空断言只有在打开编译选项`strictNullChecks`时才有意义。如果不打开这个选项，编译器就不会检查某个变量是否可能为`undefined`或`null`，因此非空断言也就没有必要了。

  ###### 非空断言还可以用于赋值断言：

  > TS 有一个编译设置，要求类的属性必须初始化（即有初始值），如果不对属性赋初值就会报错。这时就可以使用非空断言，表示这两个属性肯定会有值，这样就不会报错了。

  ```ts
  class Point {
  	x!: number; // 这里如果没有!非空断言，就会报错
  	y: number;  // 报错
  
  	constructor(x: number, y: number) {
  		// ...
  	}
  }
  ```

  > **断言函数：**
  >
  > 断言函数是一种特殊函数，用于保证函数参数符合某种类型。如果函数参数达不到要求，就会抛出错误，中断程序执行；如果达到要求，就不进行任何操作，让代码按照正常流程运行。
  >
  > ```ts
  > function isString (value: unknown): void {
  > 	if (typeof value !== "string") throw new Error("Not a string")
  > }
  > ```
  >
  > 上面示例中，函数`isString()`就是一个断言函数，用来保证参数`value`是一个字符串。用法：
  >
  > ```ts
  > const aValue: string | number = "Hello"
  > isString(aValue)
  > ```
  >
  > 上面示例中，变量`aValue`可能是字符串，也可能是数组。但是，通过调用`isString()`，后面的代码就可以确定，变量`aValue`一定是字符串。
  >
  > 但是此时有个问题：单单从isString函数的类型声明上，完全看不出来它是一个断言函数还是普通函数，因此，为了更清晰地表达断言函数，TS 3.7 引入了断言函数的新的类型写法。
  >
  > ```ts
  > function isString(value: unknown): asserts value is string {
  > 	if (typeof value !== "string") throw new Error("Not a string")
  > 	// return true; // 报错
  > }
  > ```
  >
  > 上面示例中，函数`isString()`的返回值类型写成`asserts value is string`，其中`asserts`和`is`都是关键词，`value`是函数的参数名，`string`是函数参数的预期类型。它的意思是，该函数用来断言参数`value`的类型是`string`，如果达不到要求，程序就会在这里中断。
  >
  > 使用了断言函数的新写法以后，TS 就会自动识别，只要执行了该函数，对应的变量都为断言的类型。
  >
  > 另外，断言函数的`asserts`语句等同于`void`类型，所以如果返回除了`undefined`和`null`以外的值，都会报错。
  >
  > 如果要断言参数非空，可以使用工具类型`NonNullable<T>`。
  >
  > ```ts
  > function assertIsDefined<T>(value: T): asserts value is NonNullable<T> {
  >     if (value === undefined || value === null) {
  >     	throw new Error(`${value} is not defined`)
  >     }
  > }
  > ```
  >
  > 上面示例中，工具类型`NonNullable<T>`对应类型`T`去除空类型后的剩余类型。
  >
  > 注意，断言函数与**类型保护函数（type guard）**是两种不同的函数。它们的区别是，断言函数不返回值，而类型保护函数总是返回一个布尔值。
  >
  > ```ts
  > function isString(value: unknown): value is string {
  > 	return typeof value === "string"
  > }
  > ```
  >
  > 上面示例就是一个类型保护函数`isString()`，作用是检查参数`value`是否为字符串。如果是的，返回`true`，否则返回`false`。该函数的返回值类型是`value is string`，其中的`is`是一个类型运算符，如果左侧的值符合右侧的类型，则返回`true`，否则返回`false`。
  >
  > ###### 断言函数类型的简写：
  >
  > 如果要断言某个参数保证为真（即不等于`false`、`undefined`和`null`等假值），TS 提供了断言函数的一种简写形式。
  >
  > ```ts
  > function assert(x: unknown): asserts x {
  >     if (!x) { throw new Error(`${x} should be a truthy value.`); }
  > }
  > ```
  >
  > 上面示例中，函数`assert()`的断言部分，`asserts x`省略了谓语和宾语，表示参数`x`保证为真（`true`）。

- ### `as const`断言

  > TS 提供了一种特殊的类型断言`as const`，用于告诉编译器，推断类型时，可以将这个类型视为值类型。
  >
  > ```ts
  > let s = "JavaScript" as const  // s的类型为 "JavaScript"
  > ```
  >
  > 上面示例中，let 命令声明的变量`s`，使用`as const`断言以后，就不能改变值了，否则报错。
  >
  > **注意，`as const`断言只能用于字面量（值），不能用于变量或表达式。**
  >
  > ```ts
  > let s1 = "JavaScript"
  > let s2 = s1 as const  // 报错
  > let s = ("Java" + "Script") as const  // 报错
  > ```
  >
  > `as const`也可以写成前置的形式。
  >
  > ```ts
  > // 后置形式
  > "JavaScript" as const
  > // 前置形式
  > <const>"JavaScript"
  > ```
  >
  > `as const`断言也可以用于枚举值。
  >
  > ```ts
  > enum Foo {
  > 	X,
  > 	Y,
  > }
  > let e1 = Foo.X  // Foo
  > let e2 = Foo.X as const  // Foo.X
  > ```

- ### Type 类型别名

  - 类型别名 (`type`) 可以为复杂的类型定义简短的别名，使代码更简洁、可读性更强，同时能更方便地进行类型复用和扩展。语法：`type 别名 = 类型`。注意：同一个作用域中别名不能重复。

    ```ts
    type Status = number | string
    let a: Status
    a = 1
    a = '404'
    ```

    > - 类型别名默认有变量提升，声明位置在**当前块级作用域的顶部**。
    >
    > - 类型别名支持使用表达式，也可以在定义一个别名时，使用另一个别名，即别名允许嵌套：
    >
    >    ```ts
    >    type World = "world"
    >    type Greeting = `hello ${World}`
    >    ```

  - TS中的`typeof`运算符还可以用于提取变量的类型：（**注意：`typeof`后面只能是变量，不能是表达式、字面量或类型**）

    ```ts
    const a = { x: 0 }
    type T0 = typeof a  // { x: number }
    type T1 = typeof a.x  // number
    ```

- ### TS 中的this

  > 有些场合需要给出`this`类型，但是 JS 函数通常不带有`this`参数，这时 TS 允许函数增加一个名为`this`的参数，**放在参数列表的第一位**，用来描述函数中`this`变量的类型。
  >
  > ```ts
  > // 编译前
  > function fn(this: SomeType, x: number) {}
  > // 编译后
  > function fn(x) {}
  > ```
  >
  > 相当于fn函数其实只有一个参数，调用时也只能传一个实参x，因为**`this`形参实际上会被编译掉**。
  >
  > TS 提供了一个`noImplicitThis`编译选项。如果打开了这个设置项，如果`this`的值推断为`any`类型，就会报错。

  > 在类的内部，`this`本身也可以当作类型使用，表示当前类的实例类型。（注意：`this`类型不允许应用于静态成员）
  >
  > ```ts
  > class Box {
  > 	contents: string = ""
  > 	set(value: string): this {
  > 		this.contents = value
  > 		return this
  >	}
  > }
  >```
  > 
  >有些方法返回一个布尔值，表示当前的`this`是否属于某种类型。这时，这些方法的返回值类型可以写成`this is Type`的形式。注意，`this is T`这种写法，只能用来描述类中方法的返回值类型，而不能用来描述属性的类型。（`is`运算符后面会说）
  > 
  >```ts
  > class FileSystemObject {
  > 	isFile(): this is FileRep {
  > 		return this instanceof FileRep;
  > 	}
  > 	isDirectory(): this is Directory {
  > 		return this instanceof Directory;
  > 	}
  > }
  > ```

- ### TS的基本类型

  > TS 继承了 JS 的类型，在这个基础上，还定义了一套自己的类型系统。
  >
  > JS 的`undefined、string、symbol、object、null、number、boolean、bigint`等基础类型，都是TS的基本类型。（并且还有它们的包装对象的类型：`String/Symbol/Object/Number/Boolean/BigInt`。其中，大写类型同时包含包装对象和字面量两种情况，小写类型只包含字面量，不包含包装对象。**建议只使用小写类型**）
  >
  > **注意：**
  >
  > 1. symbol 和 bigint 无法获取它们的包装对象。因为`Symbol()`和`BigInt()`不能作为构造函数使用，所以没有办法直接获得 symbol 和 bigint 的包装对象，因此`Symbol`和`BigInt`这两个包装类型虽然在TS中存在，但是目前还没什么用。
  >
  >    > 注意：目前在 TS 中，`symbol`和`Symbol`两种写法没有差异，`bigint`和`BigInt`也是如此，不知道是否属于官方的疏忽。建议始终使用小写的`symbol`和`bigint`。
  >
  > 2. bigint 类型是 ES2020 标准引入的。如果使用这个类型，TS 编译的目标 JS 版本不能低于 ES2020（即编译参数`target`不低于`es2020`）。

  - **Object：**大写的`Object`类型代表 JS 语言里面的广义对象。所有能访问到Object原型的都算`Object`类型，这囊括了几乎所有的值（除了`null`和`undefined`，这两个值不能转为包装对象），几乎没啥限制了，因此很少用。（另外，`{}`是`Object`类型的简写形式，所以使用`Object`时常常用空对象代替）

    ```ts
    let obj: Object;
    // 都能访问到 Object#toString() 方法，因此都属于Object类型
    obj = true;
    obj = "hi";
    obj = 1;
    obj = { foo: 123 };
    obj = [1, 2];
    obj = (a: number) => a + 1;
    ```

  - **object：**小写的`object`类型代表 JS 里面的狭义对象，即可以用字面量表示的对象，只包含**对象、数组、函数**，不包括原始类型的值。如果用object来限制一个变量，那么这个变量除了基本类型之外，可以赋任意的值，因为数组、函数等都属于object类型。这种在实际开发中用的很少，因为限制范围太大了。

    > 我们通常用**接口**来限制对象：

    ```ts
    // 表示person是一个有name是属性的对象，age属性可选，还可以有任意数量的其他属性（any不限类型）
    let person: { name:string; age?:number; [key:string]:any }  // 接口中的分隔符可以是, ; Enter
    ```

    > **注意：**无论是大写的`Object`类型，还是小写的`object`类型，都只包含 JS 内置对象原生的属性和方法，用户自定义的属性和方法都不存在于这两个类型之中。（后面可以用接口描述对象的自定义属性）
    >
    > ```ts
    > const o1: Object = { foo: 0 };
    > const o2: object = { foo: 0 };
    > 
    > o1.toString(); // 正确
    > o1.foo; // 报错
    > 
    > o2.toString(); // 正确
    > o2.foo; // 报错
    > ```

    ###### 可选属性：

    > TS 中的`?`表示参数是可选的，表示该参数的类型实际上是`原始类型|undefined`，它有可能为`undefined`。但是反过来就不成立，类型显式设为`undefined`的参数，传参时就不能省略。
    >
    > TS 提供编译设置`ExactOptionalPropertyTypes`，只要同时打开这个设置和`strictNullChecks`，可选属性就不能设为`undefined`。

  - **null、undefined类型：**分别表示**空值**和**未定义**。默认情况下，它们是所有类型的子类型，可以赋给任何变量，但如果开启了`strictNullChecks`空检查，则它们各自都是单独的类型，分别只能被赋值`null/undefined`这一个值。

  - **数组类型：**TS 中数组类型有一个根本特征，数组中所有元素的类型都相同，但是数量不确定：`let arr: string[]`或`let arr: Array<string>`（泛型写法，使用 TS 内置的 Array 接口）。

    > - 二维数组：`T[][]`，其中`T`是最底层数组成员的类型，其中每个一维数组中必须保存T类型元素。
    >
    > - 如果数组成员的类型比较复杂，可以写在圆括号里面。
    >
    >   ```ts
    >   let arr:(number|string)[];
    >   ```
    >
    > - TS 允许声明只读数组，方法是在数组类型前面加上`readonly`关键字：
    >
    >   ```ts
    >   const arr: readonly number[] = [0, 1]
    >   ```
    >
    >   > - TS将`readonly number[]`与`number[]`视为两种不一样的类型，后者是前者的子类型。这是因为只读数组没有pop()、push()之类会改变原数组的方法，所以number[]的方法数量要多于readonly number[]，这意味着number[]其实是readonly number[]的子类型。（加了readonly之后就是新类型了）
    >   >
    >   > - 注意，`readonly`关键字不能与数组的泛型写法一起使用：
    >   >
    >   >   ```ts
    >   >   // 报错，没有这样的写法，语法都不对
    >   >   const arr: readonly Array<number> = [0, 1]
    >   >   ```
    >   >
    >   >   实际上，TS 提供了两个专门的泛型，用来生成只读数组的类型。
    >   >
    >   >   ```ts
    >   >   const a1: ReadonlyArray<number> = [0, 1]
    >   >   const a2: Readonly<number[]> = [0, 1]
    >   >   // 上面示例中，泛型ReadonlyArray<T>和Readonly<T[]>都可以用来生成只读数组类型。两者尖括号里面的泛型写法不一样，Readonly<T[]>的尖括号里面是整个数组（number[]），而ReadonlyArray<T>的尖括号里面是数组成员（number）
    >   >   ```
    >   >
    >   > - 只读数组还有一种声明方法，就是使用**as const 断言**。（其实是只读的值类型，理解为只读数组/元组也可以）
    >   >
    >   >   ```ts
    >   >   const arr = [0, 1] as const  // 类型为：readonly [0, 1]
    >   >   ```
    >   >   
    >   >   > 上面示例中，`as const`告诉 TS，推断类型时要把变量`arr`推断为值类型，也就是只读数组/元组。
    >
    > - TS 允许使用`[]`运算符读取数组类型：（`[]`运算符后面会说，目前了解即可）
    >
    >   ```ts
    >   type Names = string[]
    >   type Name = Names[0]  // string
    >   // 由于数组成员的索引类型都是number，所以读取成员类型也可以写成下面这样：
    >   type Name = Names[number]  // string
    >   ```

  - **symbol：**TS 中用`symbol`表示 Symbol 类型，该类型表示所有的 Symbol 值。

    > 为了表示唯一的 Symbol 值，TS 还设计了`symbol`的一个子类型`unique symbol`，它表示某个唯一的 Symbol 值。并且由于这个类型的变量是不能被赋其他值的，因此变量必须用`const`不能用`let`声明：`const x: unique symbol = Symbol()`。（两个`unique symbol`类型的变量其实类型并不相同，但可以用`typeof`运算符拿到某个变量它唯一的那个`unique symbol`类型）

    > - `unique symbol`的一个作用，就是用作属性名，这可以保证不会跟其他属性名冲突。**如果要把某一个 Symbol 值当作属性名，那么它的类型只能是 unique symbol，不能是 symbol。**
    >
    > - **`unique symbol`也可以用作类的属性值，但只能赋值给类的`readonly static`属性。**
    >
    >   > 注意，这时`static`和`readonly`两个限定符缺一不可，这是为了保证这个属性是固定不变的。
    >
    > - 如果变量声明时没有给出类型，TS 会推断某个 Symbol 值变量的类型。`let`命令声明的变量，推断类型为`symbol`；`const`命令声明的变量，推断类型为`unique symbol`。
    >
    > - 不过我们知道，相同参数的`Symbol.for()`方法会返回相同的 Symbol 值。TypeScript 目前无法识别这种情况，所以可能出现多个 unique symbol 类型的变量，等于同一个 Symbol 值的情况。
    >
    >   ```ts
    >   const a:unique symbol = Symbol.for('foo');
    >   const b:unique symbol = Symbol.for('foo');
    >   ```
    >
    >   > 上面示例中，变量`a`和`b`是两个不同的值类型，但是它们的值其实是相等的。

    *除此之外，TS 还增加了以下几个基本类型：*

  - **any：**表示没有任何限制，该类型的变量可以赋予任意类型的值。any类型的范围最大，类似于*全集*的概念，适合不确定数据类型的情况。但是使用时需谨慎，变量类型一旦设为`any`，TS 实际上会关闭对这个变量的类型检查。即使有明显的类型错误，只要句法正确，都不会报错。由于这个原因，应该尽量避免使用`any`类型，否则就失去了使用 TypeScript 的意义（而是AnyScript）。

    > - 对于开发者没有指定类型、TS 必须自己推断类型的那些变量，如果无法推断出类型，TS 就会认为该变量的类型是`any`。（TS 提供了一个编译选项`noImplicitAny`，打开该选项，只要推断出`any`类型就会报错）
    >
    > - **any污染问题：**`any`类型还有一个很大的问题，就是它会*污染*其他变量。any类型的变量可以赋值给其他任何类型的变量（因为没有类型检查），导致使用其他变量时出错。
    >
    >   > ```ts
    >   > let a: any
    >   > a = 99
    >   > let s: string
    >   > s = a // 明明s要求是string型，但是any型变量的值也可以赋给s
    >   > ```
    >   >
    >   > 污染其他具有正确类型的变量，把错误留到运行时，这就是不宜使用`any`类型的另一个主要原因。
    >
    > - any是 TS 针对编程时类型不明确的变量使用的一种数据类型，它常用于以下三种情况：
    >
    >   1. 变量可以存储任意类型的值时。
    >   2. 定义存储各种类型数据的数组时（`let a: any[]`）。
    >   3. 改写现有代码时，任意值允许在编译时可选择地包含或移除类型检查。

  - **unknown：**为了解决`any`“污染”其他变量的问题，TS3.0引入了`unknown`。它与`any`含义相同，表示类型不确定，可能是任意类型，但是它的使用有一些限制，不像`any`那样自由，可以视为严格版的`any`。`unknown`跟`any`的相似之处在于，所有类型的值都可以分配给`unknown`类型（范围最大）；不同之处在于，它不能直接使用，用之前有以下限制：

    > 1. 首先，`unknown`类型的变量，不能直接赋值给其他类型的变量（除了`any`和`unknown`类型）。这就避免了污染问题，从而克服了`any`的一大缺点。
    > 2. 其次，调用`unknown`类型变量身上的任何方法、属性，都会报错。必须先**as断言**为具体的类型后，再使用。
    > 3. 最后，`unknown`类型变量能够进行的运算是有限的，只能进行比较运算（`==`、`===`、`!=`、`!==`、`||`、`&&`、`?`）、取反运算（运算符`!`）、`typeof`、`instanceof`，其他运算都会报错。
    >

    > 那么怎么才能使用`unknown`类型变量呢？答案是，只有经过“类型缩小”，`unknown`类型变量才可以使用。所谓“类型缩小”，就是缩小`unknown`变量的类型范围，给其断言为确定的类型，确保不会出错。
    >
    > 这样设计的目的是，只有明确`unknown`变量的实际类型，才允许使用它，防止像`any`那样可以随意乱用，“污染”其他变量。
    >
    > 总之，`unknown`可以看作是更安全的`any`。一般来说，凡是需要设为`any`类型的地方，通常都应该优先考虑设为`unknown`类型。在集合论上，`unknown`也可以视为所有其他类型（除了`any`）的全集，所以它和`any`一样，也属于 TS 的顶层类型。

  - **never**：为了保持与集合论的对应关系，以及类型运算的完整性，TS引入了“空类型”的概念，即该类型为空集，不包含任何值。即**never是其它所有类型的子类型（包括 null 和 undefined）**。表示任何值都不是，不存在这样的类型。这意味着声明为`never`类型的变量只能被`never`类型所赋值，在函数中它通常表现为抛出异常或无法执行到终止点（例如无限循环或抛出错误）。

    > - 就像空集是任何集合的子集一样，`never`类型可以赋值给任意其他类型。TS 把`never`称为“底层类型”（唯一的）。
    > - 几乎不用never去直接限制变量，因为没有意义。never一般是TS主动推断出来的。
    > - never一般用于限制函数的返回值类型，若一个函数的返回值类型为never，那么该函数不能顺利执行结束。

  - **字面量类型（值类型）**：TS 规定，单个值也是一种类型，称为“值类型”。字面量类型可以让变量只能拥有特定的值，通常结合联合类型使用：

    ```ts
    let direction: "up" | "down" | "left" | 2 | boolean
    direction = "up"
    ```

    > **注意**：const定义的变量意味着不会被修改，因此`const a = 1`此时a是字面量类型1而不是`number`。

  - **联合类型（union）**：表示一个变量可以是多种类型之一，通过`|`符号实现：`let id: string | number`。

  - **交叉类型**：多个类型组成的一个新类型，必须满足这多个类型的约束，用符号`&`表示：`let x: number & string`。此时变量`x`必须同时是数值和字符串，这当然是不可能的，所以 TS 会推断出`x`的类型是`never`。交叉类型一般用于类型的合成和类型的扩展：

    ```ts
    // 类型合成
    let obj: { foo: string } & { bar: string }  // 表示对象中必须包含foo和bar两个属性
    obj = {
        foo: "hello",
        bar: "world",
    }
    // 类型扩展
    type A = { foo: number }
    type B = A & { bar: number }
    ```

  - **void类型：**它通常用于声明函数的返回值，含义：函数返回值为空。只是调用者不应该依赖其返回值进行任何操作。（可以返回null或undefined，但是如果打开了`strictNullChecks`编译选项，那么 void 类型只允许返回`undefined`。如果返回`null`，就会报错）

    ```ts
    function demo(): void { console.log('hello') }
    let result = demo()
    if(result){} // 这行代码会编译报错：demo函数没有返回值，不应该拿着空的返回值去做任何操作
    ```

    > - **一个特殊的情况：**当声明函数的返回值为`void`时，TS 并不会严格要求函数返回空，只要这个返回值不再用就行：
    >
    >   ```ts
    >   let f1: () => void = () => { return 666 }  // 这里不会报错
    >   ```
    >
    >   这是因为：箭头函数省略大括号时，默认会有返回值，此时如果严格限制了，那么箭头函数就不能用简写形式了。因此TS并不严格限制函数返回空。
    >
    >   **注意：这种情况仅限于变量、对象方法、函数类型形参，函数字面量如果声明了返回值是 void 类型，还是不能有返回值。**
    >
    >   ```ts
    >   function f(): void {
    >   	return true  // 报错
    >   }
    >   ```
    >
    > - 除了函数，其他变量声明为`void`类型没有多大意义，因为这时只能赋值为`undefined`或者`null`（假定没有打开`strictNullChecks`) 。

  - **tuple（元组）：**它是一种描述**定长数组**的类型。它按序规定了数组中每个元素的类型，用于精确描述一组值。如：

    ```ts
    let a: [string, number, boolean?] = ['1', 2, true]  // 定长的数组，不按照要求push会报错
    ```

    > - `?`表示元素是可选的，所有的可选成员必须放在必选成员之后。如：`let a: [string, number?]`。
    >
    > - 使用扩展运算符（`...`）可以表示不限成员数量的元组：`let a: [string, ...string[]]`。（扩展运算符用在元组的任意位置都行，但它后面只能跟数组或元组的类型）
    >
    > - 给元组的成员命名：`let a: [a:string, b:number]`。这个成员名是说明性的，可以任意取名，没有实际作用。
    >
    > - 只读元组：
    >
    >   ```ts
    >   // 写法一
    >   type t = readonly [number, string]
    >   // 写法二，用到了工具类型Readonly<T>
    >   type t = Readonly<[number, string]>
    >   ```
    >
    > - 如果没有扩展运算符，TS 会推断出元组的成员数量（即元组长度）。
    >
    >   ```ts
    >   function f(point: [number, number]) {
    >       if (point.length === 3) {
    >       	// 报错
    >       	// ...
    >       }
    >   }
    >   ```
    >
    > - 扩展运算符（`...`）将数组（注意，不是元组）转换成一个逗号分隔的序列，这时 TS 会认为这个序列的成员数量是不确定的，因为数组的成员数量是不确定的。这会导致函数调用时，使用扩展运算符传入函数参数，可能发生参数数量与数组长度不匹配的报错。解决这个问题的一个方法，就是把成员数量不确定的数组，写成成员数量确定的元组，对元组使用扩展运算符。
    >
    > - 跟数组一样，通过`as const`断言，TS 会将该元组视为一个不可变的常量元组（其实是只读的值类型）：
    >
    >   ```ts
    >   let tuple = [42, "Hello"] as const  // 元组类型：readonly [42, "Hello"]
    >   ```
    >
    > - 和上放的数组类型一样，元组同样可以通过方括号读取其类型：（`[]`运算符后面会说，目前了解即可）
    >
    >   ```ts
    >   type Tuple = [string, number]
    >   type Age = Tuple[1]  // number
    >   ```

  - **enum（枚举值/枚举类型）：**TS中新增了枚举，它是一种类型（枚举名和枚举值都是类型），并且可以定义**一组命名常量（枚举值）**。因此不同于其他类型，枚举类型编译后不会消失，会生成 JS 对象（也叫对象枚举）。

    > - Enum 结构比较适合的场景是，成员的值不重要，名字更重要，从而增加代码的可读性和可维护性。
    >
    > - Enum 成员默认不必指定值，系统会从零开始逐一递增，按照顺序为每个成员赋值，比如 0、1、2……；
    >
    > - 也可以手动指定枚举成员的值，成员的值也可以相同。如果只设定第一个成员的值，后面成员的值就从该值开始递增。
    >
    > - 多个同名的 Enum 结构会自动合并。合并时，只允许其中一个的首成员省略初始值，并且不能有同名成员。并且这多个同名的枚举要么都是const声明的，要么都是let声明的。
    >
    > - keyof 运算符还可以取出 Enum 结构的所有成员名（非const声明的枚举），作为联合类型返回：（`keyof`后面说）
    >
    >   ```ts
    >   type Foo = keyof typeof MyEnum
    >   ```
    >
    >   注意前面要加`typeof`。因为 MyEnum 是作为值而不是类型，本质上属于`number`或`string`的一种变体，而`typeof MyEnum`相当于提取枚举所对应的对象的类型，之后就可以用`keyof`运算符返回该对象的所有属性名。
    >   
    >   如果要返回 Enum 所有的成员值，可以使用`in`运算符。
    >   
    >   ```ts
    >   enum MyEnum {
    >     A = 'a',
    >     B = 'b'
    >   }
    >       
    >   // { a: any, b: any }
    >   type Foo = { [key in MyEnum]: any };
    >   ```
    >   
    >   上面示例中，采用属性索引可以取出`MyEnum`的所有成员值。
    >
    
    - **数字枚举**：它是一种最常见的枚举类型，其枚举成员的值本质上就是`number`类型常量，且会自动递增，具备反向映射的特点。数字枚举作为类型其实等价于`number`。数字枚举的成员值可以使用 JS 表达式。

      ```ts
      enum Direction { UP,DOWN,LEFT,RIGHT }
      Direction.LEFT  // 值为2
      Direction[0]  // 值为0
      // 一般会指定枚举的值
      enum Direction { UP=1, DOWN, LEFT, RIGHT=12 }
      ```
    
    - **字符串枚举**：枚举成员的值也可以是字符串，并且可以和数组枚举成员混用。当然字符串的枚举成员没有反向映射。（除了数值和字符串，Enum 成员不允许使用其他值）
    
      ```ts
      enum Direction { UP='up', DOWN='down', LEFT='left', RIGHT='right' }
      ```
    
      > - 字符串枚举的类型就是枚举类型，和`string`类型不一样。
      > - 字符串枚举的成员值不能使用表达式赋值。
    
    - **常量枚举**：一种特殊的、用const定义的枚举，在编译时会被换掉，不会被编译为 JS 对象，而是普通的`const`常量，用于提高性能。
    
      ```ts
      const enum Direction { UP='up', DOWN='down', LEFT='left', RIGHT='right' }
      ```
    
      > 如果希望加上`const`关键词后，运行时还能访问 Enum 结构（即编译后依然将 Enum 转为 JS 对象），需要在编译时打开`preserveConstEnums`编译选项。

- ### 函数的类型

  > 函数的类型声明，需要在声明函数时，给出参数的类型和返回值的类型。（返回值类型通常会自动推断出来）

  ```ts
  function hello(txt: string): string {
  	return "hello " + txt
  }
  ```

  > 如果变量被赋值为一个函数，即该变量的类型是函数类型，需要这样写：

  ```ts
  let hello: (txt: string) => void = function (v) {  // 声明变量hello为函数类型，其中类型里的参数名txt是必须的
      console.log("hello " + txt)
  }
  ```

  ###### 注意：实际赋值的函数，其形参个数可以少于类型指定的参数个数，但是不能多于。

  ```ts
  let myFunc: (a: number, b: number) => number
  myFunc = (a: number) => a  // 正确
  ```

  > 这是因为在 JS 中，函数在声明时往往有多余的参数，实际使用时可以只传入一部分参数。

  - ##### 关于函数的类型：

    > - 可以用`typeof`运算符获取一个函数（变量）的类型：`const myParseInt: typeof parseInt;`
    >
    > - 函数类型还可以采用接口的写法：
    >
    >   ```ts
    >   let add: {
    >   	(x: number, y?: number): number  // 格式为 (参数列表):返回值，其中y?:表示该参数是可选的
    >   }
    >   add = function (x, y) { return x + y }
    >   ```
    >
    > - TS 提供了一个内置的函数类型 Function ，任何函数都属于这个类型：（太宽泛，用的很少，了解即可）
    >
    >   ```ts
    >   declare type Function = (...args: any[]) => any
    >   ```
    >
    > - 函数形参如果存在变量解构，类型写法如下：（注意：如果解构时给变量重命名了，那么就无法再为其指定类型了）
    >
    >   ```ts
    >   function f([x, y]: [number, number]) {
    >   	// ...
    >   }
    >   function sum({ a, b, c }: { a: number; b: number; c: number }) {
    >   	// ...
    >   }
    >   ```
    >
    > - rest 参数表示函数剩余的所有参数，它的类型可以是数组类型（剩余参数类型相同），也可以是元组类型（剩余参数类型不同）。
    >
    > - 如果函数内部不能修改某个参数，可以在函数定义时，在参数类型前面加上`readonly`关键字，表示这是只读参数。
    >
    >   ```ts
    >   function arraySum(arr: readonly string) {
    >   	// ...
    >   	arr = '11'  // 报错
    >   }
    >   ```
    >
    > - 构造函数的类型写法，就是在参数列表前面加上`new`命令：
    >
    >   ```ts
    >   type AnimalConstructor = new () => Animal
    >   ```
    >
    >   构造函数还有另一种接口的写法：（后面会讲接口）
    >
    >   ```ts
    >   type F = {
    >   	new (s: string): object
    >   }
    >   ```
    >
    > - 某些函数既是构造函数，又可以当作普通函数使用，比如`Date()`。这时，类型声明可以用下面的接口写法：
    >
    >   ```ts
    >   type F = {
    >   	new (s: string): object
    >   	(n?: number): number
    >   }
    >   ```

  - ##### TS 中，函数的类型重载：（箭头函数不支持重载）

    > 有些函数可以接受不同类型或不同个数的参数，并且根据参数的不同，会有不同的函数行为。这种根据参数类型不同，执行不同逻辑的行为，称为函数重载（function overload）。
    >
    > ```ts
    > reverse("abc"); // 'cba'
    > reverse([1, 2, 3]); // [3, 2, 1]
    > ```

    > TS 对于“函数重载”的类型声明方法是，逐一定义每一种情况的类型：
    >
    > ```ts
    > function reverse(str: string): string;
    > function reverse(arr: any[]): any[];
    > ```
    >
    > 最后还**必须对函数`reverse()`给予完整的类型声明，并在其中编写重载的实现**。内部需要判断参数的类型及个数，并根据判断结果执行不同的操作，实现函数重载的效果：
    >
    > ```ts
    > function reverse(str: string): string;
    > function reverse(arr: any[]): any[];  // 函数实际调用的类型，以前2行的类型声明为准
    > function reverse(stringOrArray: string | any[]): string | any[] {
    >     if (typeof stringOrArray === "string")
    >         return stringOrArray.split("").reverse().join("");
    >     else return stringOrArray.slice().reverse();
    > }
    > ```
    >
    > - 上面示例中，前两行类型声明列举了重载的各种情况。第三行是函数本身的类型声明，它必须与前面已有的声明兼容。
    >
    > - 注意：上面的重载类型描述与下面的重载的具体实现之间，不能有其他代码，否则报错。
    >
    > - 另外，重载声明的顺序也很重要。因为 TS 是按照顺序进行检查的，一旦发现符合某个类型声明，就不再往下检查了，所以**类型最宽的声明应该放在最后面**，防止覆盖其他类型声明。
    >
    > - 对象中的方法也可以使用重载：
    >
    >   ```ts
    >   class StringBuilder {
    >       #data = "";
    >   
    >       add(num: number): this;
    >       add(bool: boolean): this;
    >       add(str: string): this;
    >       add(value: any): this {
    >       	this.#data += String(value);
    >       	return this;
    >       }
    >   
    >       toString() {
    >       	return this.#data;
    >       }
    >   }
    >   ```
    >
    > - 由于重载是一种比较复杂的类型声明方法，为了降低复杂性，应该优先使用联合类型替代函数重载。


- ------

### 类型守卫(TODO)

> TS 提供了类型守卫，可以在代码中检查变量类型，帮助编译器推断更加具体的类型。这对于联合类型尤为重要。
>
> ```ts
> function printId(id: string | number) {
>        if (typeof id === "string") {
>        	console.log(id.toUpperCase())
>        } else {
>        	console.log(id.toFixed(2))
>        }
> }
> ```

### 类型兼容性和工具类型

> TS 提供了一些工具类型，如 `Partial`、`Pick`、`Readonly`、`Record` 等，这些类型可以帮助生成新的类型，简化类型定义。
>
> ```ts
> interface Todo {
>       title: string;
>       description: string;
> }
> 
> let partialTodo: Partial<Todo> = { title: "Learn TypeScript" }; // 可选属性
> ```

### 模块和命名空间

> TS 提供了基于 ES6 的模块系统，使用`import`和`export`导入和导出模块。此外，TS 还支持命名空间（Namespace），用于组织代码和避免命名冲突。

- ------

  ### 类型声明文件

  > 以`.d.ts`后缀结尾的文件是TS中的**类型定义文件**，作用是为现有的JS代码提供类型声明。这使得TS能够在使用第三方JS库时进行类型检查和提示。

  > demo.js：
  >
  > ```js
  > export function add(a,b){ return a+b }
  > export function mul(a,b){ return a*b }
  > ```
  >
  > demo.d.ts：（文件名必须和import导入的模块名保持一致）
  >
  > ```ts
  > declare function add(a:number, b:number): number
  > declare function mul(a:number, b:number): number
  > export {add,mul}
  > ```
  >
  > index.js：
  >
  > ```js
  > import { add,mul } from './demo.js' // 如果没有“demo.d.ts”则会报错：无法找到模块“./demo.js”的类型声明文件
  > console.log(add(1,2))
  > console.log(mul(1,2))
  > ```

  > 实际开发中，类型声明文件通常放在`@types`目录下。（import导入TS的类型时，为了将类型声明和值区分开，TS最新规范要求，类型前必须加type关键字：`import {type Person} from '@/types/index.ts'`）

------

