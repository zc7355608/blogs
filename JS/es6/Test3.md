- ## 函数的扩展

  - #### 函数参数的默认值

    - ##### 基本用法
  
       ES6 之前，不能直接为函数的参数指定默认值，只能采用变通的方法。
  
       ```js
       function log(x, y) {
         y = y || 'World';
         console.log(x, y);
       }
       
       log('Hello') // Hello World
       log('Hello', 'China') // Hello China
       log('Hello', '') // Hello World
       ```
  
       ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面：
  
       ```js
       function log(x, y = 'World') {
         console.log(x, y);
       }
       
       log('Hello') // Hello World
       log('Hello', 'China') // Hello China
       log('Hello', '') // Hello
       ```
  
       可以看到，ES6 的写法比 ES5 简洁许多，而且非常自然。除了简洁，ES6 的写法还有两个好处：首先，阅读代码的人，可以立刻意识到哪些参数是可以省略的，不用查看函数体或文档；其次，有利于将来的代码优化，即使未来的版本在对外接口中，彻底拿掉这个参数，也不会导致以前的代码无法运行。
  
       注意：函数的形参是默认声明的，因此函数体中不能用`let`或`const`再次声明了，且不能有同名参数。
  
       并且函数形参的默认值是惰性求值的：
  
       ```js
       let x = 99;
       function foo(p = x + 1) {
         console.log(p);
       }
       
       foo() // 100
       
       x = 100;
       foo() // 101
       ```
  
       上面代码中，参数`p`的默认值是`x + 1`。这时，每次调用函数`foo()`，都会重新计算`x + 1`，而不是默认`p`等于 100。
  
       另外，如果将形参默认值设为`undefined`，那么相当于没有给形参设置默认值。
  
       ```js
       function foo(optional = undefined) { ··· }
       ```
  
    - ##### 与解构赋值默认值结合使用
  
       参数默认值可以与解构赋值的默认值，结合起来使用。
  
       ```js
       function foo({x, y = 5}) {
         console.log(x, y);
       }
       
       foo({}) // undefined 5
       foo({x: 1}) // 1 5
       foo({x: 1, y: 2}) // 1 2
       foo() // TypeError: Cannot read property 'x' of undefined
       ```
  
       上面代码只使用了对象的解构赋值默认值，没有使用函数参数的默认值。只有当函数`foo()`的参数是一个对象时，变量`x`和`y`才会通过解构赋值生成。如果函数`foo()`调用时没提供参数，变量`x`和`y`就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。
  
       ```js
       function foo({x, y = 5} = {}) {
         console.log(x, y);
       }
       
       foo() // undefined 5
       ```
  
       上面代码指定，如果没有提供参数，函数`foo`的参数默认为一个空对象。然后再进行函数形参的解构，x没解构到因此为`undefined`，y没解构到使用默认值5。
  
    - ##### 函数的 length 属性失真
  
       **指定了形参的默认值以后，函数的`length`属性将返回没有指定默认值的形参的个数**。也就是说，指定了默认值后，`length`属性将失真。
  
       ```js
       (function (a) {}).length // 1
       (function (a = 5) {}).length // 0
       (function (a, b, c = 5) {}).length // 2
       ```
  
       这是因为`length`属性的含义是，该函数预期传入的参数个数。某个参数指定默认值以后，预期传入的参数个数就不包括这个参数了。同理，后文的 **rest 参数也不会计入`length`属性**。
  
       注意：**如果设置了默认值的参数不是尾参数，那么`length`属性也不再计入后面的参数了**。
  
       ```js
       (function (a = 0, b, c) {}).length // 0
       (function (a, b = 1, c) {}).length // 1
       ```
  
    - ##### 作用域
  
       一旦给函数形参设置默认值，函数进行声明初始化时，小括号里面会形成一个单独的作用域。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的。
  
       ```js
       var x = 1;
       
       function f(x, y = x) {
         console.log(y);
       }
       
       f(2) // 2
       ```

       上面代码中，参数`y`的默认值等于变量`x`。调用函数`f`时，参数形成一个单独的作用域。在这个作用域里面，默认值变量`x`指向第一个参数`x`，而不是全局变量`x`，所以输出是`2`。

    - ##### 应用
  
       利用参数默认值，可以指定某一个参数不得省略，如果省略就抛出一个错误。
  
       ```js
       function throwIfMissing() {
         throw new Error('Missing parameter');
       }
       
       function foo(mustBeProvided = throwIfMissing()) {
         return mustBeProvided;
       }
       
       foo()
       // Error: Missing parameter
       ```
  
       上面代码的`foo`函数，如果调用的时候没有参数，就会调用默认值`throwIfMissing`函数，从而抛出一个错误。
  
  - #### rest 参数（剩余参数）
  
    ES6 引入 rest 参数，语法为：`...变量名`，放在函数的形参末尾，用于获取函数的多余参数，这样就不需要使用`arguments`对象了。注意：**rest 参数只能出现在参数表最后，它是一个真正的数组，里面保存了多余的实参。**
  
    ```js
    function add(...values) {
      let sum = 0;
    
      for (var val of values) {
        sum += val;
      }
    
      return sum;
    }
    
    add(2, 5, 3) // 10
    ```
  
    上面代码的`add`函数是一个求和函数，利用 rest 参数，可以向该函数传入任意数目的参数。
  
    注意：函数的`length`属性不包括 rest 参数。
  
    本质上，rest参数就是通过扩展运算符`...`，将后面多余的参数收集起来，放到了一个数组中。
  
    剩余参数可以进行[解构](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring)，这允许你忽略某些参数位置。
  
    ```js
    function ignoreFirst(...[, b, c]) {
      return b + c;
    }
    ```
  
  - #### 严格模式
  
    从 ES5 开始，函数内部可以设定为严格模式。
  
    ```js
    function doSomething(a, b) {
      'use strict';
      // code
    }
    ```
  
    ES2016 做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。
  
    这样规定的原因是，**函数内部的严格模式，同时适用于函数体和函数参数**。但是，函数执行的时候，先执行函数参数，然后再执行函数体。这样就有一个不合理的地方，只有从函数体之中，才能知道参数是否应该以严格模式执行，但是参数却应该先于函数体执行。
  
    ```js
    // 报错
    function doSomething(value = 070) {
      'use strict';
      return value;
    }
    ```
  
    上面代码中，参数`value`的默认值是八进制数`070`，但是严格模式下不能用前缀`0`表示八进制，所以应该报错。但是实际上，JS 引擎会先成功执行`value = 070`，然后进入函数体内部，发现需要用严格模式执行，这时才会报错。
  
    虽然可以先解析函数体代码，再执行参数代码，但是这样无疑就增加了复杂性。因此，标准索性禁止了这种用法，**只要函数参数使用了默认值、解构赋值、或者扩展运算符，就不能显式指定严格模式。**
  
    两种方法可以规避这种限制。第1种是**设定全局性的严格模式**；第2种是**把函数包在一个无参数的立即执行函数里面**。
  
    ```js
    const doSomething = (function () {
      'use strict';
      return function(value = 42) {
        return value;
      };
    }());
    ```
  
  - #### 箭头函数
  
    ES6 允许使用“箭头”（`=>`）来定义函数，主要用来代替匿名函数的写法。语法：`(形参) => { /*函数体*/ }`，如：
  
    ```js
    var f = v => v;
    
    // 等同于
    var f = function (v) {
      return v;
    };
    ```
  
    如果箭头函数的形参只有一个，`()`可以省略。如果函数体只有一行代码，`{}`也可以省，此时默认有个`return`。
  
    > 注意：如果返回一个对象且代码只有一行，那么对象外面要加小括号：`const fn = () => ({name: 'zs'})`，目的是让 JS 解释器将其当作语句来解释而不是默认当作代码块。
  
    ##### 注意点：
  
    1. 箭头函数没有自己的`this`。它里面的`this`实际上是上层作用域的`this`。也就是说，箭头函数内部的`this`指向是固定的，相比之下，普通函数的`this`指向是可变的。
  
       > 由于箭头函数没有自己的`this`，所以用`call()`、`apply()`、`bind()`这些方法去改变`this`的指向也是无效的。
  
    2. 箭头函数不可以当作构造函数，它和原型、实例、以及一切面向对象相关的内容都没有关系。因此它也没有`prototype`属性。
  
    3. 箭头函数中没有`arguments`、`super`、`new.target`。
  
    4. 箭头函数中不可以使用`yield`命令，因此箭头函数不能用作 Generator 生成器函数。
  
    箭头函数的出现是为了将普通函数和构造函数进行区分，只要看到箭头函数就知道它和实例没有任何关系，就是个普通的函数而已。
  
    箭头函数适合作为回调函数。如：事件回调、数组的回调函数、定时器回调、等不需要`this`的场景下。
  
    ##### 不适用场合：
  
    1. 对象的方法。由于对象方法要用`this`来调用，因此如果对象的方法中用了`this`关键字的话，就会有问题，因此不建议在对象方法中使用箭头函数。
    2. 函数中需要用`this`来进行某些操作时。
  
  - #### 尾调用优化（了解）
  
    1. ##### 什么是尾调用？
  
       尾调用（Tail Call）是函数式编程的一个重要概念，本身非常简单，一句话就能说清楚，就是指**某个函数的最后一步是调用另一个函数**：
  
       ```js
       function f(x){
         return g(x);
       }
       ```
  
       上面代码中，函数`f`的最后一步是调用函数`g`，这就叫尾调用。
  
       尾调用不一定出现在函数尾部，只要是最后一步操作即可。以下三种情况都不属于尾调用：
  
       ```js
       // 情况一
       function f(x){
         let y = g(x);
         return y;
       }
       
       // 情况二
       function f(x){
         return g(x) + 1;
       }
       
       // 情况三
       function f(x){
         g(x);
       }
       ```
  
       上面代码中，情况一是调用函数`g`之后，还有赋值操作，所以不属于尾调用，即使语义完全一样。情况二也属于调用后还有操作，即使写在一行内。情况三等同于最后执行的是`return undefined;`。
  
    2. ##### 尾调用优化
  
       尾调用之所以与其他调用不同，就在于它的特殊的调用位置。
  
       我们知道，函数调用会在内存形成一个“调用记录”，又称“调用帧”（call frame），保存调用位置和内部变量等信息。如果在函数`A`的内部调用函数`B`，那么在`A`的调用帧上方，还会形成一个`B`的调用帧。等到`B`运行结束，将结果返回到`A`，`B`的调用帧才会消失。如果函数`B`内部还调用函数`C`，那就还有一个`C`的调用帧，以此类推。所有的调用帧，就形成一个“调用栈”（call stack）。
  
       尾调用由于是函数的最后一步操作，所以不需要保留外层函数的调用帧，因为调用位置、内部变量等信息都不会再用到了，只要直接用内层函数的调用帧，取代外层函数的调用帧就可以了。
  
       ```js
       function f() {
         let m = 1;
         let n = 2;
         return g(m + n);
       }
       f();
       
       // 等同于
       function f() {
         return g(3);
       }
       f();
       
       // 等同于
       g(3);
       ```
  
       上面代码中，如果函数`g`不是尾调用，函数`f`就需要保存内部变量`m`和`n`的值、`g`的调用位置等信息。但由于调用`g`之后，函数`f`就结束了，所以执行到最后一步，完全可以删除`f(x)`的调用帧，只保留`g(3)`的调用帧。
  
       这就叫做“尾调用优化”（Tail call optimization），即只保留内层函数的调用帧。如果所有函数都是尾调用，那么完全可以做到每次执行时，调用帧只有一项，这将大大节省内存。这就是“尾调用优化”的意义。
  
       注意，只有不再用到外层函数的内部变量，内层函数的调用帧才会取代外层函数的调用帧，否则就无法进行“尾调用优化”。
  
       ```js
       function addOne(a){
         var one = 1;
         function inner(b){
           return b + one;
         }
         return inner(a);
       }
       ```
  
       上面的函数不会进行尾调用优化，因为内层函数`inner`用到了外层函数`addOne`的内部变量`one`。
  
       > 注意：目前只有 Safari 浏览器支持尾调用优化，Chrome 和 Firefox 都不支持。
       >
       > 这背后其实是语言的“理论规范”与“工程实践”之间的冲突。尾调用优化虽然能节省内存，但它有一个“副作用”：它会“抹去”调用栈的中间信息，让开发者难以追踪错误，因此权衡后放弃。
  
    3. ##### 尾递归
  
       函数调用自身，称为递归。如果尾调用自身，就称为尾递归。
  
       递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生“栈溢出”错误（stack overflow）。但对于尾递归来说，由于只存在一个调用帧，所以永远不会发生“栈溢出”错误。
  
       ```js
       function factorial(n) {
         if (n === 1) return 1;
         return n * factorial(n - 1);
       }
       
       factorial(5) // 120
       ```
  
       上面代码是一个阶乘函数，计算`n`的阶乘，最多需要保存`n`个调用记录，复杂度 O(n) 。
  
       如果改写成尾递归，只保留一个调用记录，复杂度 O(1) 。
  
       ```js
       function factorial(n, total) {
         if (n === 1) return total;
         return factorial(n - 1, n * total);
       }
       
       factorial(5, 1) // 120
       ```
  
       还有一个比较著名的例子，就是计算 Fibonacci 数列，也能充分说明尾递归优化的重要性。
  
       非尾递归的 Fibonacci 数列实现如下。
  
       ```js
       function Fibonacci (n) {
         if ( n <= 1 ) {
           return 1;
         }
       
         return Fibonacci(n - 1) + Fibonacci(n - 2);
       }
       
       Fibonacci(10) // 89
       Fibonacci(100) // 超时
       Fibonacci(500) // 超时
       ```
  
       尾递归优化过的 Fibonacci 数列实现如下。
  
       ```js
       function Fibonacci2 (n , ac1 = 1 , ac2 = 1) {
         if( n <= 1 ) {
           return ac2;
         }
       
         return Fibonacci2 (n - 1, ac2, ac1 + ac2);
       }
       
       Fibonacci2(100) // 573147844013817200000
       Fibonacci2(1000) // 7.0330367711422765e+208
       Fibonacci2(10000) // Infinity
       ```
  
       由此可见，“尾调用优化”对递归操作意义重大，所以一些函数式编程语言将其写入了语言规格。ES6 亦是如此，第一次明确规定，所有 ECMAScript 的实现，都必须部署“尾调用优化”。这就是说，ES6 中只要使用尾递归，就不会发生栈溢出（或者层层递归造成的超时），相对节省内存。
  
    4. ##### 递归函数的改写
  
       尾递归的实现，往往需要改写递归函数，确保最后一步只调用自身。做到这一点的方法，就是把所有用到的内部变量改写成函数的参数。比如上面的例子，阶乘函数 factorial 需要用到一个中间变量`total`，那就把这个中间变量改写成函数的参数。这样做的缺点就是不太直观，第一眼很难看出来，为什么计算`5`的阶乘，需要传入两个参数`5`和`1`？
  
       两个方法可以解决这个问题。方法一是在尾递归函数之外，再提供一个正常形式的函数。
  
       ```js
       function tailFactorial(n, total) {
         if (n === 1) return total;
         return tailFactorial(n - 1, n * total);
       }
       
       function factorial(n) {
         return tailFactorial(n, 1);
       }
       
       factorial(5) // 120
       ```
  
       上面代码通过一个正常形式的阶乘函数`factorial`，调用尾递归函数`tailFactorial`，看起来就正常多了。
  
       函数式编程有一个概念，叫做柯里化（currying），意思是将多参数的函数转换成单参数的形式。这里也可以使用柯里化。
  
       ```js
       function currying(fn, n) {
         return function (m) {
           return fn.call(this, m, n);
         };
       }
       
       function tailFactorial(n, total) {
         if (n === 1) return total;
         return tailFactorial(n - 1, n * total);
       }
       
       const factorial = currying(tailFactorial, 1);
       
       factorial(5) // 120
       ```
  
       上面代码通过柯里化，将尾递归函数`tailFactorial`变为只接受一个参数的`factorial`。
  
       第二种方法就简单多了，就是采用 ES6 的函数默认值。
  
       ```js
       function factorial(n, total = 1) {
         if (n === 1) return total;
         return factorial(n - 1, n * total);
       }
       
       factorial(5) // 120
       ```
  
       上面代码中，参数`total`有默认值`1`，所以调用时不用提供这个值。
  
       总结一下，递归本质上是一种循环操作。纯粹的函数式编程语言没有循环操作命令，所有的循环都用递归实现，这就是为什么尾递归对这些语言极其重要。对于其他支持“尾调用优化”的语言（比如 Lua，ES6），只需要知道**循环可以用递归代替**，而**一旦使用递归，就最好使用尾递归**。
  
    5. ##### 严格模式
  
       ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。（目前只有 Safari 浏览器支持尾调用优化）
  
       这是因为在正常模式下，函数内部有两个变量，可以跟踪函数的调用栈。
  
         - `func.arguments`：返回调用时函数的参数。
          - `func.caller`：返回调用当前函数的那个函数。
  
       尾调用优化发生时，函数的调用栈会改写，因此上面两个变量就会失真。严格模式禁用这两个变量，所以尾调用模式仅在严格模式下生效。
  
    6. ##### 尾递归优化的实现
  
       尾递归优化只在严格模式下生效，那么正常模式下，或者那些不支持该功能的环境中，有没有办法也使用尾递归优化呢？
  
       回答是可以的，就是自己实现尾递归优化。
  
       它的原理非常简单。尾递归之所以需要优化，原因是调用栈太多，造成溢出，那么只要减少调用栈，就不会溢出。怎么做可以减少调用栈呢？就是**采用“循环”换掉“递归”**。
  
       下面是一个正常的递归函数。
  
       ```js
       function sum(x, y) {
         if (y > 0) {
           return sum(x + 1, y - 1);
         } else {
           return x;
         }
       }
       
       sum(1, 100000)
       // Uncaught RangeError: Maximum call stack size exceeded(…)
       ```
       
       上面代码中，`sum`是一个递归函数，参数`x`是需要累加的值，参数`y`控制递归次数。一旦指定`sum`递归 100000 次，就会报错，提示超出调用栈的最大次数。
       
       蹦床函数（trampoline）可以将递归执行转为循环执行。
       
       ```js
       function trampoline(f) {
         while (f && f instanceof Function) {
           f = f();
         }
         return f;
       }
       ```
       
       上面就是蹦床函数的一个实现，它接受一个函数`f`作为参数。只要`f`执行后返回一个函数，就继续执行。注意，这里是返回一个函数，然后执行该函数，而不是函数里面调用函数，这样就避免了递归执行，从而就消除了调用栈过大的问题。
       
       然后，要做的就是将原来的递归函数，改写为每一步返回另一个函数。
       
       ```js
       function sum(x, y) {
         if (y > 0) {
           return sum.bind(null, x + 1, y - 1);
         } else {
           return x;
         }
       }
       ```
       
       上面代码中，`sum`函数的每次执行，都会返回自身的另一个版本。
       
       现在，使用蹦床函数执行`sum`，就不会发生调用栈溢出。
       
       ```js
       trampoline(sum(1, 100000))
       // 100001
       ```
       
       蹦床函数并不是真正的尾递归优化，下面的实现才是。
       
       ```js
       function tco(f) {
         var value;
         var active = false;
         var accumulated = [];
       
         return function accumulator() {
           accumulated.push(arguments);
           if (!active) {
             active = true;
             while (accumulated.length) {
               value = f.apply(this, accumulated.shift());
             }
             active = false;
             return value;
           }
         };
       }
       
       var sum = tco(function(x, y) {
         if (y > 0) {
           return sum(x + 1, y - 1)
         }
         else {
           return x
         }
       });
       
       sum(1, 100000)
       // 100001
       ```
       
       上面代码中，`tco`函数是尾递归优化的实现，它的奥妙就在于状态变量`active`。默认情况下，这个变量是不激活的。一旦进入尾递归优化的过程，这个变量就激活了。然后，每一轮递归`sum`返回的都是`undefined`，所以就避免了递归执行；而`accumulated`数组存放每一轮`sum`执行的参数，总是有值的，这就保证了`accumulator`函数内部的`while`循环总是会执行。这样就很巧妙地将“递归”改成了“循环”，而后一轮的参数会取代前一轮的参数，保证了调用栈只有一层。
  
  - #### 函数参数的尾逗号
  
    ES2017 允许函数的最后一个形参/实参后有尾逗号（trailing comma）。
  
    此前，函数定义和调用时，都不允许最后一个参数后面出现逗号。
  
    ```js
    function clownsEverywhere(
      param1,
      param2
    ) { /* ... */ }
    
    clownsEverywhere(
      'foo',
      'bar'
    );
    ```
  
    上面代码中，如果在`param2`或`bar`后面加一个逗号，就会报错。
  
    如果像上面这样，将参数写成多行（即每个参数占据一行），以后修改代码的时候，想为函数`clownsEverywhere`添加第三个参数，或者调整参数的次序，就势必要在原来最后一个参数后面添加一个逗号。这对于版本管理系统来说，就会显示添加逗号的那一行也发生了变动。这看上去有点冗余，因此 ES6 的新语法允许定义和调用时，尾部直接有一个逗号。这使得函数参数与数组和对象的尾逗号规则，保持一致了。
  
  - #### 允许 catch 语句省略参数
  
    JS 语言的`try...catch`结构，以前明确要求`catch`的小括号中必须跟参数，去接收`try`代码块抛出的错误对象。
  
    ```js
    try {
    	// ...
    } catch (err) {
    	// 处理错误
    }
    ```
  
    上面代码中，`catch`命令后面带有参数`err`。
  
    很多时候，`catch`代码块可能用不到这个参数。但是，为了保证语法正确，还是必须写。ES2019 做出了改变，**允许`catch`语句省略参数。**
    
    ```js
    try {
      // ...
    } catch {
      // ...
    }
    ```
  
- ## 数组的扩展

  - #### 扩展运算符
  
    扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，**可以将一个可迭代结构转为用逗号分隔的参数序列**。该运算符可以对所有实现了`Iterator`接口的结构进行扩展，它是基于迭代器的。
  
    ```js
    console.log(...[1, 2, 3])  // 1 2 3
    
    console.log(1, ...[2, 3, 4], 5)  // 1 2 3 4 5
    
    [...document.querySelectorAll('div')]  // [<div>, <div>, <div>]
    ```
  
    注意：此时**扩展运算符外层只能是小括号`()`或者中括号`[]`**。此时是对数组的扩展，是基于可迭代结构的迭代器来完成的。
  
    > ES2018 之前，对不可迭代的结构使用扩展运算符会报错。ES2018 之后，扩展运算符也可用于对象。也就是说，**扩展运算符外层还能出现大括号`{}`**。此时是基于`Object.assign()`来完成的对象的扩展（后面会讲）。
  
    该运算符主要用于函数调用。
  
    ```js
    function push(array, ...items) {
      array.push(...items);
    }
    
    function add(x, y) {
      return x + y;
    }
    
    const numbers = [4, 38];
    add(...numbers) // 42
    ```
  
    上面代码中，`array.push(...items)`和`add(...numbers)`这两行，都是函数的调用，它们都使用了扩展运算符。该运算符将一个数组，变为参数序列。
  
    扩展运算符与正常的函数参数可以结合使用，非常灵活。
  
    ```js
    function f(v, w, x, y, z) { }
    const args = [0, 1];
    f(-1, ...args, 2, ...[3]);
    ```
  
    扩展运算符后面可以放置表达式。
  
    ```js
    const arr = [
      ...(x > 0 ? ['a'] : []),
      'b',
    ];
    ```
  
    **如果扩展运算符后面是一个空数组，则不产生任何效果**。
  
    ```js
    [...[], 1]  // [1]
    ```
  
    注意，**只有函数调用时，扩展运算符才可以放在圆括号中**，否则会报错。
  
    ```js
    (...[1, 2])
    // Uncaught SyntaxError: Unexpected number
    
    console.log((...[1, 2]))
    // Uncaught SyntaxError: Unexpected number
    
    console.log(...[1, 2])
    // 1 2
    ```
  
    上面三种情况，扩展运算符都放在圆括号里面，但是前两种情况会报错，因为扩展运算符所在的括号不是函数调用。
  
    - ##### 替代函数的 `apply()` 方法：
  
      由于扩展运算符可以展开数组，所以不再需要`apply()`方法将数组转为函数的参数了。
  
      ```js
      // ES5 的写法
      function f(x, y, z) {
        // ...
      }
      var args = [0, 1, 2];
      f.apply(null, args);
      
      // ES6 的写法
      function f(x, y, z) {
        // ...
      }
      let args = [0, 1, 2];
      f(...args);
      ```
  
      下面是扩展运算符取代`apply()`方法的一个实际的例子，应用`Math.max()`方法，简化求出一个数组最大元素的写法。
  
      ```js
      // ES5 的写法
      Math.max.apply(null, [14, 3, 77])
      
      // ES6 的写法
      Math.max(...[14, 3, 77])
      
      // 等同于
      Math.max(14, 3, 77);
      ```
  
      上面代码中，由于 JS 不提供求数组最大元素的函数，所以只能套用`Math.max()`函数，将数组转为一个参数序列，然后求最大值。有了扩展运算符以后，就可以直接用`Math.max()`了。
  
      另一个例子是通过`push()`函数，将一个数组添加到另一个数组的尾部。
  
      ```js
      // ES5 的写法
      var arr1 = [0, 1, 2];
      var arr2 = [3, 4, 5];
      Array.prototype.push.apply(arr1, arr2);
      
      // ES6 的写法
      let arr1 = [0, 1, 2];
      let arr2 = [3, 4, 5];
      arr1.push(...arr2);
      ```
  
      上面代码的 ES5 写法中，`push()`方法的参数不能是数组，所以只好通过`apply()`方法变通使用`push()`方法。有了扩展运算符，就可以直接将数组传入`push()`方法。
  
    - ##### 扩展运算符的应用：
  
      1. ###### 复制数组：
  
         扩展运算符提供了复制数组的简便写法。（浅拷贝）
  
           ```js
         const a1 = [1, 2];
         const a2 = [...a1];
           ```
  
      2. ###### 合并数组：
  
         扩展运算符提供了数组合并的新写法。（浅拷贝）
  
         ```js
         const arr1 = ['a', 'b'];
         const arr2 = ['c'];
         const arr3 = ['d', 'e'];
         
         // ES5 的合并数组
         arr1.concat(arr2, arr3);
         // [ 'a', 'b', 'c', 'd', 'e' ]
         
         // ES6 的合并数组
         [...arr1, ...arr2, ...arr3]
         // [ 'a', 'b', 'c', 'd', 'e' ]
         ```
  
      3. ###### 字符串：
  
         扩展运算符还可以将字符串转为数组。
  
         ```js
         [...'hello']
         // [ "h", "e", "l", "l", "o" ]
         ```
  
         上面的写法有一个好处，它能识别四个字节的 Unicode 字符。
  
         ```js
         'x\uD83D\uDE80y'.length // 4
         [...'x\uD83D\uDE80y'].length // 3
         ```
  
         上面代码的第一种写法，JS 会将四个字节的 Unicode 字符，识别为 2 个字符，采用扩展运算符就没有这个问题。因此，**正确返回字符串长度的函数**，可以像下面这样写。
  
         ```js
         function length(str) {
           return [...str].length;
         }
         
         length('x\uD83D\uDE80y') // 3
         ```
  
         凡是涉及到操作四个字节的 Unicode 字符的函数，都有这个问题。因此，最好都用扩展运算符改写。
  
         ```js
         let str = 'x\uD83D\uDE80y';
         
         str.split('').reverse().join('')
         // 'y\uDE80\uD83Dx'
         
         [...str].reverse().join('')
         // 'y\uD83D\uDE80x'
         ```
  
         上面代码中，如果不用扩展运算符，字符串的`reverse()`操作就不正确。
  
      4. ###### 实现了 `Iterator` 接口的对象：
  
         任何部署了迭代器（Iterator）接口的对象（参阅 Iterator 一章），都可以用扩展运算符将其转为真正的数组。
  
         ```js
         let nodeList = document.querySelectorAll('div');
         let array = [...nodeList];
         ```
  
         上面代码中，`querySelectorAll()`方法返回的是一个`NodeList`对象。它不是数组，而是一个类似数组的对象。这时，扩展运算符可以将其转为真正的数组，原因就在于`NodeList`对象实现了 Iterator。
  
         ```js
         Number.prototype[Symbol.iterator] = function*() {
           let i = 0;
           let num = this.valueOf();
           while (i < num) {
             yield i++;
           }
         }
         
         console.log([...5]) // [0, 1, 2, 3, 4]
         ```
  
         上面代码中，先定义了`Number`对象的迭代器接口，扩展运算符将`5`自动转成`Number`实例以后，就会调用这个接口，就会返回自定义的结果。
  
         对于那些没有部署 Iterator 接口的类似数组的对象，对其使用扩展运算符会报错。这时，可以改为使用`Array.from`方法将其转为真正的数组。
  
      5. ###### `Generator` 函数：
  
         Generator 函数运行后，返回一个迭代器对象，因此也可以使用扩展运算符。
  
         ```js
         const go = function*(){
           yield 1;
           yield 2;
           yield 3;
         };
         
         [...go()] // [1, 2, 3]
         ```
  
         上面代码中，变量`go`是一个 Generator 函数，执行后返回的是一个迭代器对象，对这个迭代器对象执行扩展运算符，就会将内部遍历得到的值，转为一个数组。
  
  - #### 静态方法
  
    - `Array.from()`：该方法可以将两类对象转为真正的数组，类似数组的对象（伪数组）和可遍历（iterable）的对象。
  
      下面是一个类似数组的对象，`Array.from()`将它转为真正的数组。
  
      ```js
      let arrayLike = {
        '0': 'a',
        '1': 'b',
        '2': 'c',
        length: 3
      };
      
      // ES5 的写法
      var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']
      
      // ES6 的写法
      let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
      ```
  
      还有部署了 Iterator 接口的数据结构，`Array.from()`也能将其转为数组。
  
      ```js
      Array.from('hello')  // ['h', 'e', 'l', 'l', 'o']
      
      let namesSet = new Set(['a', 'b'])
      Array.from(namesSet) // ['a', 'b']
      ```
  
      上面代码中，字符串和 Set 结构都具有 Iterator 接口，因此可以被`Array.from()`转为真正的数组。
  
      如果参数是一个真正的数组，`Array.from()`会返回一个一模一样的新数组。
  
      ```js
      Array.from([1, 2, 3])  // [1, 2, 3]
      ```
  
      对于还没有部署该方法的浏览器，可以用`Array.prototype.slice()`方法替代。
  
      ```js
      const toArray = (() =>
        Array.from ? Array.from : obj => [].slice.call(obj)
      )();
      ```
  
      `Array.from()`还可以接受一个函数作为第二个参数，作用类似于数组的`map()`方法，用来对每个元素进行处理，将处理后的值放入返回的数组。
  
      ```js
      Array.from(arrayLike, x => x * x);
      // 等同于
      Array.from(arrayLike).map(x => x * x);
      
      Array.from([1, 2, 3], (x) => x * x)
      // [1, 4, 9]
      ```
  
      下面的例子是取出一组 DOM 节点的文本内容。
  
      ```js
      let spans = document.querySelectorAll('span.name');
      
      // map()
      let names1 = Array.prototype.map.call(spans, s => s.textContent);
      
      // Array.from()
      let names2 = Array.from(spans, s => s.textContent)
      ```
  
      下面的例子将数组中布尔值为`false`的成员转为`0`。
  
      ```js
      Array.from([1, , 2, , 3], (n) => n || 0)  // [1, 0, 2, 0, 3]
      ```
  
      另一个例子是返回各种数据的类型。
  
      ```js
      function typesOf () {
        return Array.from(arguments, value => typeof value)
      }
      typesOf(null, [], NaN)
      // ['object', 'object', 'number']
      ```
  
      如果`map()`函数里面用到了`this`关键字，还可以传入`Array.from()`的第三个参数，用来绑定`this`。
  
      `Array.from()`可以将各种值转为真正的数组，并且还提供`map`功能。这实际上意味着，只要有一个原始的数据结构，你就可以先对它的值进行处理，然后转成规范的数组结构，进而就可以使用数量众多的数组方法。
  
      ```js
      Array.from({ length: 2 }, () => 'jack')  // ['jack', 'jack']
      ```
  
      上面代码中，`Array.from()`的第一个参数指定了第二个参数运行的次数。这种特性可以让该方法的用法变得非常灵活。
  
      `Array.from()`的另一个应用是，将字符串转为数组，然后返回字符串的长度。因为它能正确处理各种 Unicode 字符，可以避免 JS 将大于`\uFFFF`的单个 Unicode 字符，`length`为2的 bug。
  
      ```js
      function countSymbols(string) {
        return Array.from(string).length;
      }
      ```
  
    - `Array.of()`：它可以将一组值，转换为数组。
  
      ```js
      Array.of(3, 11, 8) // [3,11,8]
      Array.of(3) // [3]
      Array.of(3).length // 1
      ```
  
      这个方法的主要目的，是弥补数组构造函数`Array()`的不足。因为参数个数的不同，会导致`Array()`的行为有差异。
  
      ```js
      Array() // []
      Array(3) // [, , ,]
      Array(3, 11, 8) // [3, 11, 8]
      ```
  
      上面代码中，`Array()`方法没有参数、一个参数、三个参数时，返回的结果都不一样。只有当参数个数不少于 2 个时，`Array()`才会返回由参数组成的新数组。参数只有一个正整数时，实际上是指定数组的长度。
  
      `Array.of()`基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。
  
      ```js
      Array.of() // []
      Array.of(undefined) // [undefined]
      Array.of(1) // [1]
      Array.of(1, 2) // [1, 2]
      ```
  
      **`Array.of()`总是返回参数值组成的数组。如果没有参数，就返回一个空数组**。
  
      `Array.of()`方法可以用下面的代码模拟实现。
  
      ```js
      function ArrayOf(){
        return [].slice.call(arguments);
      }
      ```
  
  - #### 实例方法
  
    - `copyWithin()`：该方法在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。
  
      ```js
      Array.prototype.copyWithin(target, start = 0, end = this.length)
      ```
  
      它接受三个参数。
  
      - target（必需）：从该位置开始替换数据。如果为负值，表示倒数。
      - start（可选）：从该位置开始读取数据，默认为 0。如果为负值，表示从末尾开始计算。
      - end（可选）：到该位置前停止读取数据，默认等于数组长度。如果为负值，表示从末尾开始计算。
  
      这三个参数都应该是数值，如果不是，会自动转为数值。
  
      ```js
      [1, 2, 3, 4, 5].copyWithin(0, 3)
      // [4, 5, 3, 4, 5]
      ```
  
      上面代码表示将从 3 号位直到数组结束的成员（4 和 5），复制到从 0 号位开始的位置，结果覆盖了原来的 1 和 2。
  
      下面是更多例子。
  
      ```js
      // 将3号位复制到0号位
      [1, 2, 3, 4, 5].copyWithin(0, 3, 4)
      // [4, 2, 3, 4, 5]
      
      // -2相当于3号位，-1相当于4号位
      [1, 2, 3, 4, 5].copyWithin(0, -2, -1)
      // [4, 2, 3, 4, 5]
      
      // 将3号位复制到0号位
      [].copyWithin.call({length: 5, 3: 1}, 0, 3)
      // {0: 1, 3: 1, length: 5}
      
      // 将2号位到数组结束，复制到0号位
      let i32a = new Int32Array([1, 2, 3, 4, 5]);
      i32a.copyWithin(0, 2);
      // Int32Array [3, 4, 5, 4, 5]
      
      // 对于没有部署 TypedArray 的 copyWithin 方法的平台
      // 需要采用下面的写法
      [].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
      // Int32Array [4, 2, 3, 4, 5]
      ```
  
    - `find()`，`findIndex()`，`findLast()`，`findLastIndex()`：
  
      数组实例的`find()`方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为`true`的成员，然后返回该成员。如果没有符合条件的成员，则返回`undefined`。
  
      ```js
      [1, 4, -5, 10].find((n) => n < 0)  // -5
      ```
  
      上面代码找出数组中第一个小于 0 的成员。
  
      ```js
      [1, 5, 10, 15].find(function(ele, index, arr) {
        return value > 9;
      }) // 10
      ```
  
      上面代码中，`find()`方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。
  
      数组实例的`findIndex()`方法的用法与`find()`方法非常类似，返回第一个符合条件的数组成员的下标，如果所有成员都不符合条件，则返回`-1`。
  
      ```js
      [1, 5, 10, 15].findIndex(function(value, index, arr) {
        return value > 9;
      }) // 2
      ```
  
      这两个方法都可以接受第二个参数，用来绑定回调函数的`this`对象。
  
      ```js
      function f(v){
        return v > this.age;
      }
      let person = {name: 'John', age: 20};
      [10, 12, 26, 15].find(f, person);  // 26
      ```
  
      上面的代码中，`find()`函数接收了第二个参数`person`对象，回调函数中的`this`对象指向`person`对象。
  
      另外，**这两个方法都可以发现`NaN`，弥补了数组的`indexOf()`方法的不足**。
  
      ```js
      [NaN].indexOf(NaN)  // -1
      
      [NaN].findIndex(y => Object.is(NaN, y))  // 0
      ```
  
      上面代码中，`indexOf()`方法无法识别数组的`NaN`成员，但是`findIndex()`方法可以借助`Object.is()`方法做到。
  
      `find()`和`findIndex()`都是从数组的0号位，依次向后检查。[ES2022](https://github.com/tc39/proposal-array-find-from-last) 新增了两个方法`findLast()`和`findLastIndex()`，从数组的最后一个成员开始，依次向前检查，其他都保持不变。
  
      ```js
      const array = [
        { value: 1 },
        { value: 2 },
        { value: 3 },
        { value: 4 }
      ];
      
      array.findLast(n => n.value % 2 === 1); // { value: 3 }
      array.findLastIndex(n => n.value % 2 === 1); // 2
      ```
  
      上面示例中，`findLast()`和`findLastIndex()`从数组结尾开始，寻找第一个`value`属性为奇数的成员。结果，该成员是`{ value: 3 }`，位置是2号位。
  
    - `fill()`：使用给定值，填充一个数组。
  
      ```js
      ['a', 'b', 'c'].fill(7)
      // [7, 7, 7]
      
      new Array(3).fill(7)
      // [7, 7, 7]
      ```
  
      上面代码表明，`fill`方法用于空数组的初始化非常方便。数组中已有的元素，会被全部抹去。
  
      `fill`方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。
  
      ```js
      ['a', 'b', 'c'].fill(7, 1, 2)
      // ['a', 7, 'c']
      ```
  
      上面代码表示，`fill`方法从 1 号位开始，向原数组填充 7，到 2 号位之前结束。
  
      注意，如果填充的类型为对象，那么被赋值的是同一个内存地址的对象，而不是深拷贝对象。
  
      ```js
      let arr = new Array(3).fill({name: "Mike"});
      arr[0].name = "Ben";
      arr
      // [{name: "Ben"}, {name: "Ben"}, {name: "Ben"}]
      
      let arr = new Array(3).fill([]);
      arr[0].push(5);
      arr
      // [[5], [5], [5]]
      ```
  
    - `entries()`，`keys()` 和 `values()`：
  
      ES6 提供三个新的方法——`entries()`，`keys()`和`values()`——用于遍历数组。它们都返回一个遍历器对象（详见《Iterator》一章），可以用`for...of`循环进行遍历，唯一的区别是`keys()`是对键名的遍历、`values()`是对键值的遍历，`entries()`是对键值对的遍历。
  
      ```js
      for (let index of ['a', 'b'].keys()) {
        console.log(index);
      }
      // 0
      // 1
      
      for (let elem of ['a', 'b'].values()) {
        console.log(elem);
      }
      // 'a'
      // 'b'
      
      for (let [index, elem] of ['a', 'b'].entries()) {
        console.log(index, elem);
      }
      // 0 "a"
      // 1 "b"
      ```
  
      如果不使用`for...of`循环，可以手动调用遍历器对象的`next`方法，进行遍历。
  
      ```js
      let letter = ['a', 'b', 'c'];
      let entries = letter.entries();
      console.log(entries.next().value); // [0, 'a']
      console.log(entries.next().value); // [1, 'b']
      console.log(entries.next().value); // [2, 'c']
      ```
  
    - `includes()`：判断数组是否包含某个值，与字符串的`includes`方法类似。ES2016 引入了该方法。
  
      ```js
      [1, 2, 3].includes(2)     // true
      [1, 2, 3].includes(4)     // false
      [1, 2, NaN].includes(NaN) // true
      ```
  
      该方法的第二个参数表示搜索的起始位置，默认为`0`。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为`-4`，但数组长度为`3`），则会重置为从`0`开始。
  
      ```js
      [1, 2, 3].includes(3, 3);  // false
      [1, 2, 3].includes(3, -1); // true
      ```
  
      没有该方法之前，我们通常使用数组的`indexOf`方法，检查是否包含某个值。
  
      ```js
      if (arr.indexOf(el) !== -1) {
        // ...
      }
      ```
  
      `indexOf`方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于`-1`，表达起来不够直观。二是，它内部使用严格相等运算符（`===`）进行判断，这会导致对`NaN`的误判。
  
      ```js
      [NaN].indexOf(NaN)  // -1
      ```
  
      `includes`使用的是不一样的判断算法，就没有这个问题。
  
      ```js
      [NaN].includes(NaN)  // true
      ```
  
      下面代码用来检查当前环境是否支持该方法，如果不支持，部署一个简易的替代版本。
  
      ```js
      const contains = (() =>
        Array.prototype.includes
          ? (arr, value) => arr.includes(value)
          : (arr, value) => arr.some(el => el === value)
      )();
      contains(['foo', 'bar'], 'baz'); // => false
      ```
  
      另外，Map 和 Set 数据结构有一个`has`方法，需要注意与`includes`区分：
  
      - Map 结构的`has`方法，是用来查找键名的，比如`Map.prototype.has(key)`、`WeakMap.prototype.has(key)`、`Reflect.has(target, propertyKey)`。
      - Set 结构的`has`方法，是用来查找值的，比如`Set.prototype.has(value)`、`WeakSet.prototype.has(value)`。
  
    - `flat()`，`flatMap()`：数组的成员有时还是数组，`Array.prototype.flat()`用于将嵌套的数组“拉平”，变成一维的数组。该方法返回一个新数组，对原数据没有影响。
  
      ```js
      [1, 2, [3, 4]].flat()  // [1, 2, 3, 4]
      ```
  
      上面代码中，原数组的成员里面有一个数组，`flat()`方法将子数组的成员取出来，添加在原来的位置。
  
      `flat()`默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将`flat()`方法的参数写成一个整数，表示想要拉平的层数，默认为1。
  
      ```js
      [1, 2, [3, [4, 5]]].flat()  // [1, 2, 3, [4, 5]]
      
      [1, 2, [3, [4, 5]]].flat(2)  // [1, 2, 3, 4, 5]
      ```
  
      上面代码中，`flat()`的参数为2，表示要“拉平”两层的嵌套数组。
  
      如果不管有多少层嵌套，都要转成一维数组，可以用`Infinity`关键字作为参数。
  
      ```js
      [1, [2, [3]]].flat(Infinity)  // [1, 2, 3]
      ```
  
      如果原数组有空位，**`flat()`方法会忽略空位**。
  
      ```js
      [1, 2, , 4, 5].flat()  // [1, 2, 4, 5]
      ```
  
      `flatMap()`方法对原数组的每个成员执行一个函数（相当于执行`Array.prototype.map()`），然后对返回值组成的数组执行`flat()`方法。该方法返回一个新数组，不改变原数组。
  
      ```js
      // 相当于 [[2, 4], [3, 6], [4, 8]].flat()
      [2, 3, 4].flatMap((x) => [x, x * 2])
      // [2, 4, 3, 6, 4, 8]
      ```
  
      **`flatMap()`只能展开一层数组**。
  
      ```js
      // 相当于 [[[2]], [[4]], [[6]], [[8]]].flat()
      [1, 2, 3, 4].flatMap(x => [[x * 2]])
      // [[2], [4], [6], [8]]
      ```
  
      上面代码中，遍历函数返回的是一个双层的数组，但是`flatMap()`只能展开一层，因此返回的还是一个嵌套数组。
  
      `flatMap()`方法的参数是一个遍历函数（和数组的`map()`类似），该函数可以接受三个参数，分别是当前数组成员、当前数组成员的位置（从零开始）、原数组。
  
      ```js
      arr.flatMap(function callback(currentValue[, index[, array]]) {
        // ...
      }[, thisArg])
      ```
  
      `flatMap()`方法还可以有第二个参数，用来绑定遍历函数里面的`this`。
  
    - `at()`：返回数组中指定下标的元素。（类似`String.prototype.at()`）
  
      长久以来，JS 不支持数组的负索引，如果要引用数组的最后一个成员，不能写成`arr[-1]`，只能使用`arr[arr.length - 1]`。
  
      这是因为方括号运算符`[]`在 JS 语言里面，不仅用于数组，还用于对象。对于对象来说，方括号里面就是键名，比如`obj[1]`引用的是键名为字符串`1`的键，同理`obj[-1]`引用的是键名为字符串`-1`的键。由于 JS 的数组是特殊的对象，所以方括号里面的负数无法再有其他语义了，也就是说，不可能添加新语法来支持负索引。
  
      为了解决这个问题，[ES2022](https://github.com/tc39/proposal-relative-indexing-method/) 为数组实例增加了`at()`方法，接受一个整数作为参数，返回对应位置的成员，并支持负索引。
  
      `at()` 方法是[通用的](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array#通用数组方法)。其仅期望 `this` 具有 `length` 属性和以整数为键的属性（伪数组）。
  
      ```js
      const arr = [5, 12, 8, 130, 44];
      arr.at(2) // 8
      arr.at(-2) // 130
      ```
  
      如果参数位置超出了数组范围，`at()`返回`undefined`。
  
      ```js
      const sentence = 'This is a sample sentence';
      
      sentence.at(0); // 'T'
      sentence.at(-1); // 'e'
      
      sentence.at(-100) // undefined
      sentence.at(100) // undefined
      ```
  
    - `toReversed()`，`toSorted()`，`toSpliced()`，`with()`：
  
      很多数组的传统方法会改变原数组，比如`push()`、`pop()`、`shift()`、`unshift()`等等。数组只要调用了这些方法，它的值就变了。[ES2023](https://github.com/tc39/proposal-change-array-by-copy)引入了四个新方法，对数组进行操作时，不改变原数组，而返回一个原数组的拷贝。
  
      - `Array.prototype.toReversed() -> Array`
      - `Array.prototype.toSorted(compareFn) -> Array`
      - `Array.prototype.toSpliced(start, deleteCount, ...items) -> Array`
      - `Array.prototype.with(index, value) -> Array`
  
      它们分别对应数组的原有方法。
  
      - `toReversed()`对应`reverse()`，用来颠倒数组成员的位置。
      - `toSorted()`对应`sort()`，用来对数组成员排序。
      - `toSpliced()`对应`splice()`，用来在指定位置，删除指定数量的成员，并插入新成员。
      - `with(index, value)`对应`splice(index, 1, value)`，用来将指定位置的成员替换为新的值。
  
      上面是这四个新方法对应的原有方法，含义和用法完全一样，唯一不同的是不会改变原数组，而是返回原数组操作后的拷贝。
  
      下面是示例。
  
      ```js
      const sequence = [1, 2, 3];
      sequence.toReversed() // [3, 2, 1]
      sequence // [1, 2, 3]
      
      const outOfOrder = [3, 1, 2];
      outOfOrder.toSorted() // [1, 2, 3]
      outOfOrder // [3, 1, 2]
      
      const array = [1, 2, 3, 4];
      array.toSpliced(1, 2, 5, 6, 7) // [1, 5, 6, 7, 4]
      array // [1, 2, 3, 4]
      
      const correctionNeeded = [1, 1, 3];
      correctionNeeded.with(1, 2) // [1, 2, 3]
      correctionNeeded // [1, 1, 3]
      ```
  
    - ~~（提案）`group()`，`groupToMap()`~~：
  
      数组成员分组是一个常见需求，比如 SQL 有`GROUP BY`子句和函数式编程有 MapReduce 方法。现在有一个[提案](https://github.com/tc39/proposal-array-grouping)，为 JS 新增了数组实例方法`group()`和`groupToMap()`，它们可以根据分组函数的运行结果，将数组成员分组。
  
      `group()`的参数是一个分组函数，原数组的每个成员都会依次执行这个函数，确定自己是哪一个组。
  
      ```js
      const array = [1, 2, 3, 4, 5];
      
      array.group((num, index, array) => {
        return num % 2 === 0 ? 'even': 'odd';
      });
      // { odd: [1, 3, 5], even: [2, 4] }
      ```
  
      `group()`的分组函数可以接受三个参数，依次是数组的当前成员、该成员的位置序号、原数组（上例是`num`、`index`和`array`）。分组函数的返回值应该是字符串（或者可以自动转为字符串），以作为分组后的组名。
  
      `group()`的返回值是一个对象，该对象的键名就是每一组的组名，即分组函数返回的每一个字符串（上例是`even`和`odd`）；该对象的键值是一个数组，包括所有产生当前键名的原数组成员。
  
      下面是另一个例子。
  
      ```js
      [6.1, 4.2, 6.3].group(Math.floor)
      // { '4': [4.2], '6': [6.1, 6.3] }
      ```
  
      上面示例中，`Math.floor`作为分组函数，对原数组进行分组。它的返回值原本是数值，这时会自动转为字符串，作为分组的组名。原数组的成员根据分组函数的运行结果，进入对应的组。
  
      `group()`还可以接受一个对象，作为第二个参数。该对象会绑定分组函数（第一个参数）里面的`this`，不过如果分组函数是一个箭头函数，该对象无效，因为箭头函数内部的`this`是固化的。
  
      `groupToMap()`的作用和用法与`group()`完全一致，唯一的区别是返回值是一个 Map 结构，而不是对象。Map 结构的键名可以是各种值，所以不管分组函数返回什么值，都会直接作为组名（Map 结构的键名），不会强制转为字符串。这对于分组函数返回值是对象的情况，尤其有用。
  
      ```js
      const array = [1, 2, 3, 4, 5];
      
      const odd  = { odd: true };
      const even = { even: true };
      array.groupToMap((num, index, array) => {
        return num % 2 === 0 ? even: odd;
      });
      //  Map { {odd: true}: [1, 3, 5], {even: true}: [2, 4] }
      ```
  
      上面示例返回的是一个 Map 结构，它的键名就是分组函数返回的两个对象`odd`和`even`。
  
      总之，按照字符串分组就使用`group()`，按照对象分组就使用`groupToMap()`。
  
  - #### 数组的空位
  
    数组的空位指的是，数组的某一个位置没有任何值，比如`Array()`构造函数返回的数组都是空位。
  
    ```js
    Array(3) // [, , ,]
    ```
  
    上面代码中，`Array(3)`返回一个具有 3 个空位的数组。
  
    注意，空位不是`undefined`，某一个位置的值等于`undefined`，依然是有值的。空位是没有任何值，`in`运算符可以说明这一点。
  
    ```js
    0 in [undefined, undefined, undefined] // true
    0 in [, , ,] // false
    ```
  
    上面代码说明，第一个数组的 0 号位置是有值的，第二个数组的 0 号位置没有值。
  
    ES5 对空位的处理很不一致，大多数情况下会忽略空位。
  
    - `forEach()`, `filter()`, `reduce()`, `every()` 和`some()`都会跳过空位。
    - `map()`会跳过空位，但会保留这个值。
    - `join()`和`toString()`会将空位视为`undefined`，而`undefined`和`null`会被处理成空字符串。
  
    ```js
    // forEach方法
    [,'a'].forEach((x,i) => console.log(i)); // 1
    
    // filter方法
    ['a',,'b'].filter(x => true) // ['a','b']
    
    // every方法
    [,'a'].every(x => x==='a') // true
    
    // reduce方法
    [1,,2].reduce((x,y) => x+y) // 3
    
    // some方法
    [,'a'].some(x => x !== 'a') // false
    
    // map方法
    [,'a'].map(x => 1) // [,1]
    
    // join方法
    [,'a',undefined,null].join('#') // "#a##"
    
    // toString方法
    [,'a',undefined,null].toString() // ",a,,"
    ```
  
    **ES6 则是明确将空位转为`undefined`**。
  
    **`Array.from()`方法会将数组的空位，转为`undefined`**，也就是说，这个方法不会忽略空位。
  
    ```js
    Array.from(['a',,'b'])
    // [ "a", undefined, "b" ]
    ```
  
    **扩展运算符（`...`）也会将空位转为`undefined`**。
  
    ```js
    [...['a',,'b']]
    // [ "a", undefined, "b" ]
    ```
  
    **`copyWithin()`会连空位一起拷贝**。
  
    ```js
    [,'a','b',,].copyWithin(2,0) // [,"a",,"a"]
    ```
  
    **`fill()`会将空位视为正常的数组位置**。
  
    ```js
    new Array(3).fill('a') // ["a","a","a"]
    ```
  
    **`for...of`循环也会遍历空位**。
  
    ```js
    let arr = [, ,];
    for (let i of arr) {
      console.log(1);
    }
    // 1
    // 1
    ```
  
    上面代码中，数组`arr`有两个空位，`for...of`并没有忽略它们。如果改成`map()`方法遍历，空位是会跳过的。
  
    **`entries()`、`keys()`、`values()`、`find()`和`findIndex()`会将空位处理成`undefined`**。
  
    ```js
    // entries()
    [...[,'a'].entries()] // [[0,undefined], [1,"a"]]
    
    // keys()
    [...[,'a'].keys()] // [0,1]
    
    // values()
    [...[,'a'].values()] // [undefined,"a"]
    
    // find()
    [,'a'].find(x => true) // undefined
    
    // findIndex()
    [,'a'].findIndex(x => true) // 0
    ```
  
    由于空位的处理规则非常不统一，所以**建议避免出现空位**。
  
  - #### `Array.prototype.sort()` 的排序稳定性
  
    排序稳定性（stable sorting）是排序算法的重要属性，指的是排序关键字相同的项目，排序前后的顺序不变。
  
    ```js
    const arr = [
      'peach',
      'straw',
      'apple',
      'spork'
    ];
    
    const stableSorting = (s1, s2) => {
      if (s1[0] < s2[0]) return -1;
      return 1;
    };
    
    arr.sort(stableSorting)
    // ["apple", "peach", "straw", "spork"]
    ```
  
    上面代码对数组`arr`按照首字母进行排序。排序结果中，`straw`在`spork`的前面，跟原始顺序一致，所以排序算法`stableSorting`是稳定排序。
  
    ```js
    const unstableSorting = (s1, s2) => {
      if (s1[0] <= s2[0]) return -1;
      return 1;
    };
    
    arr.sort(unstableSorting)
    // ["apple", "peach", "spork", "straw"]
    ```
  
    上面代码中，排序结果是`spork`在`straw`前面，跟原始顺序相反，所以排序算法`unstableSorting`是不稳定的。
  
    常见的排序算法之中，插入排序、合并排序、冒泡排序等都是稳定的，堆排序、快速排序等是不稳定的。不稳定排序的主要缺点是，多重排序时可能会产生问题。假设有一个姓和名的列表，要求按照“姓氏为主要关键字，名字为次要关键字”进行排序。开发者可能会先按名字排序，再按姓氏进行排序。如果排序算法是稳定的，这样就可以达到“先姓氏，后名字”的排序效果。如果是不稳定的，就不行。
  
    早先的 ECMAScript 没有规定，`Array.prototype.sort()`的默认排序算法是否稳定，留给浏览器自己决定，这导致某些实现是不稳定的。[ES2019](https://github.com/tc39/ecma262/pull/1340) 明确规定，`Array.prototype.sort()`的默认排序算法必须稳定。这个规定已经做到了，现在 JS 各个主要实现的默认排序算法都是稳定的。

------

