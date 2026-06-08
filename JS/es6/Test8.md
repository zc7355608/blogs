- ## Generator 生成器函数

  - #### 简介

     - ##### 基本概念：

        Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同，可以把 Generator 函数理解成一个状态机，封装了多个内部状态。

        **执行 Generator 函数会返回一个迭代器对象**，也就是说，Generator 函数除了状态机，还是一个**迭代器对象生成函数**。它返回的迭代器对象，可以依次遍历 Generator 函数内部的每一个状态。因此 Generator 生成器函数还可以作为`Symbol.iterator`的值，简化了迭代器生成函数的写法。

        形式上，Generator 函数是一个普通函数，但是有两个特征：

        1. `function`关键字与函数名之间有一个星号`*`；

        2. Generator 函数体内部能够用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）。

           ```js
        function* helloWorldGenerator() {
             yield 'hello';
             yield 'world';
             return 'ending';
           }
           
           var hw = helloWorldGenerator();
           ```
        
           上面代码定义了一个 Generator 函数`helloWorldGenerator`，它内部有两个**`yield`表达式**（`hello`和`world`），即该函数有三个状态：hello，world 和 return 语句（结束状态）。

        Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的**迭代器对象**。

        下一步，必须调用迭代器对象的`next`方法，使得指针移向下一个状态。也就是说，每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式或`return`语句为止。换言之，Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。

        ```js
  hw.next()
        // { value: 'hello', done: false }
        
        hw.next()
        // { value: 'world', done: false }
        
        hw.next()
        // { value: 'ending', done: true }
        
        hw.next()
        // { value: undefined, done: true }
        ```
        
        上面代码一共调用了四次`next`方法。

        第一次调用，Generator 函数开始执行，直到遇到第一个`yield`表达式为止。`next`方法返回一个**信息对象**，它的`value`属性就是当前`yield`关键字后的表达式的值`hello`，`done`属性的值`false`，表示遍历还没有结束（后面还有`yield`或`return`）。

        第二次调用，Generator 函数从上次`yield`表达式停下的地方，一直执行到下一个`yield`表达式。`next`方法返回的信息对象的`value`属性就是当前`yield`后面那个表达式的值`world`，`done`属性的值`false`，表示遍历还没有结束。

        第三次调用，Generator 函数从上次`yield`表达式停下的地方，一直执行到`return`语句（如果没有`return`语句，就执行到函数结束）。`next`方法返回的信息对象的`value`属性，就是紧跟在`return`语句后面的表达式的值（如果没有`return`语句，则`value`属性的值为`undefined`），`done`属性的值`true`，表示遍历已经结束（后面没有`yield`或`return`了）。

        第四次调用，此时 Generator 函数已经运行完毕，`next`方法返回对象的`value`属性为`undefined`，`done`属性为`true`。以后再调用`next`方法，返回的都是这个值。（后面再调用`next()`，总是返回这个值）

        总结一下，**调用 Generator 函数，返回一个迭代器对象，代表 Generator 函数的内部指针。以后，每次调用迭代器对象的`next`方法，就会返回一个有着`value`和`done`两个属性的信息对象。`value`属性表示当前的内部的状态值，是`yield`后面那个表达式的值；`done`属性是一个布尔值，表示是否遍历结束**。

        ES6 没有规定，`function`关键字与函数名之间的星号，写在哪个位置。这导致下面的写法都能通过。

        ```js
  function * foo(x, y) { ··· }
        function *foo(x, y) { ··· }
        function* foo(x, y) { ··· }
        function*foo(x, y) { ··· }
        ```
        
        由于 Generator 函数仍然是普通函数，所以一般的写法是上面的第三种，即星号紧跟在`function`关键字后面。本书也采用这种写法。

     - ##### yield 表达式：

        由于 Generator 函数返回的迭代器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

        迭代器对象的`next`方法的运行逻辑如下：

        1. 遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的信息对象的`value`属性值。

        2. 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。

        3. 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的信息对象的`value`属性值。**此时信息对象的`done`属性值为`true`**。

           > 如果该函数没有`return`语句，则返回的信息对象的`value`属性值为`undefined`。

        需要注意的是，**`yield`关键字后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行**，因此等于为 JS 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。

        ```js
     function* gen() {
          yield  123 + 456;
        }
        ```
     
        上面代码中，`yield`后面的表达式`123 + 456`，不会立即求值，只会在`next`方法将指针移到这一句时，才会求值。

        `yield`表达式与`return`语句既有相似之处，也有区别。相似之处在于，都能返回紧跟在语句后面的那个表达式的值。区别在于每次遇到`yield`，函数暂停执行，下一次再从该位置继续向后执行，而`return`语句不具备位置记忆的功能。一个函数里面，只能执行一次（或者说一个）`return`语句，但是可以执行多次（或者说多个）`yield`表达式。正常函数只能返回一个值，因为只能执行一次`return`；Generator 函数可以返回一系列的值，因为可以有任意多个`yield`。从另一个角度看，也可以说 Generator 生成了一系列的值，这也就是它的名称的来历（英语中，generator 这个词是“生成器”的意思）。

        Generator 函数内部也可以不写`yield`表达式，这时它就变成了一个单纯的暂缓执行函数。

        ```js
     function* f() {
          console.log('执行了！')
        }
        
        var generator = f();
        
        setTimeout(function () {
          generator.next()
        }, 2000);
        ```
     
        上面代码中，函数`f`如果是普通函数，在为变量`generator`赋值时就会执行。但是，函数`f`是一个 Generator 函数，就变成只有调用`next`方法时，函数`f`才会执行。

        另外需要注意，**`yield`表达式只能用在 Generator 函数里面，用在其他地方都会报错**。

        ```js
     (function (){
          yield 1;
        })()
        // SyntaxError: Unexpected number
        ```
     
        上面代码在一个普通函数中使用`yield`表达式，结果产生一个句法错误。

        下面是另外一个例子。

        ```js
     var arr = [1, [[2, 3], 4], [5, 6]];
        
        var flat = function* (a) {
          a.forEach(function (item) {
            if (typeof item !== 'number') {
              yield* flat(item);
            } else {
              yield item;
            }
          });
        };
        
        for (var f of flat(arr)){
          console.log(f);
        }
        ```
     
        上面代码也会产生句法错误，因为`forEach`方法的参数是一个普通函数，但是在里面使用了`yield`表达式（这个函数里面还使用了`yield*`表达式，详细介绍见后文）。一种修改方法是改用`for`循环。

        ```js
     var arr = [1, [[2, 3], 4], [5, 6]];
        
        var flat = function* (a) {
          var length = a.length;
          for (var i = 0; i < length; i++) {
            var item = a[i];
            if (typeof item !== 'number') {
              yield* flat(item);
            } else {
              yield item;
            }
          }
        };
        
        for (var f of flat(arr)) {
          console.log(f);
        }
        // 1, 2, 3, 4, 5, 6
        ```
     
        另外，**`yield`表达式如果放在另一个表达式中，就必须放在圆括号内**。

        ```js
     function* demo() {
          console.log('Hello' + yield); // SyntaxError
          console.log('Hello' + yield 123); // SyntaxError
        
          console.log('Hello' + (yield)); // OK
          console.log('Hello' + (yield 123)); // OK
        }
        ```
     
        `yield`表达式用作函数参数或放在赋值语句的右边，可以不加括号。

        ```js
     function* demo() {
          foo(yield 'a', yield 'b'); // OK
          let input = yield; // OK
        }
        ```
     
     - ##### 与 Iterator 接口的关系：

        上一章说过，任意一个对象的`Symbol.iterator`方法，等于该对象的迭代器对象生成函数，调用该函数会返回该对象的一个迭代器对象。

        由于 Generator 函数就是一个迭代器对象生成函数，因此可以把 Generator 生成器函数赋值给对象的`Symbol.iterator`属性，从而使得该对象具有 Iterator 接口。

        ```js
        var myIterable = {};
        myIterable[Symbol.iterator] = function* () {
          yield 1;
          yield 2;
          yield 3;
        };
        
        [...myIterable] // [1, 2, 3]
        ```
     
        上面代码中，Generator 函数赋值给`Symbol.iterator`属性，从而使得`myIterable`对象具有了 Iterator 接口，可以被`...`运算符遍历了。

        Generator 函数执行后，返回的这个迭代器对象，它本身也具有`Symbol.iterator`属性，执行后返回自身。

        ```js
        function* gen(){
          // some code
        }
        
        var g = gen();
        
        g[Symbol.iterator]() === g
        // true
        ```
     
        上面代码中，`gen`是一个 Generator 函数，调用它会生成一个迭代器对象`g`。它的`Symbol.iterator`属性，也是一个迭代器对象生成函数，执行后返回它自己。

  - #### next 方法的参数

     `yield`表达式本身没有返回值，或者说总是返回`undefined`。`next()`方法调用时可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。

     ```js
     function* f() {
       for(var i = 0; true; i++) {
         var reset = yield i;
         if(reset) { i = -1; }
       }
     }
     
     var g = f();
     
     g.next() // { value: 0, done: false }
     g.next() // { value: 1, done: false }
     g.next(true) // { value: 0, done: false }
     ```

     上面代码先定义了一个可以无限运行的 Generator 函数`f`，如果`next`方法没有参数，每次运行到`yield`表达式，变量`reset`的值总是`undefined`。当`next`方法带一个参数`true`时，变量`reset`就被重置为这个参数（即`true`），因此`i`会等于`-1`，下一轮循环就会从`-1`开始递增。

     这个功能有很重要的语法意义。Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过`next`方法的参数，就有办法在 Generator 函数开始运行之后，继续向函数体内部注入值。也就是说，**可以在 Generator 函数运行的不同阶段，从外部（通过`next()`函数）向内部注入不同的值，从而调整函数行为**。

     再看一个例子。

     ```js
     function* foo(x) {
       var y = 2 * (yield (x + 1));
       var z = yield (y / 3);
       return (x + y + z);
     }
     
     var a = foo(5);
     a.next() // Object{value:6, done:false}
     a.next() // Object{value:NaN, done:false}
     a.next() // Object{value:NaN, done:true}
     
     var b = foo(5);
     b.next() // { value:6, done:false }
     b.next(12) // { value:8, done:false }
     b.next(13) // { value:42, done:true }
     ```

     上面代码中，第二次运行`next`方法的时候不带参数，导致 y 的值等于`2 * undefined`（即`NaN`），除以 3 以后还是`NaN`，因此返回对象的`value`属性也等于`NaN`。第三次运行`next`方法的时候不带参数，所以`z`等于`undefined`，返回对象的`value`属性等于`5 + NaN + undefined`，即`NaN`。

     如果向`next`方法提供参数，返回结果就完全不一样了。上面代码第一次调用`b`的`next`方法时，返回`x+1`的值`6`；第二次调用`next`方法，将上一次`yield`表达式的值设为`12`，因此`y`等于`24`，返回`y / 3`的值`8`；第三次调用`next`方法，将上一次`yield`表达式的值设为`13`，因此`z`等于`13`，这时`x`等于`5`，`y`等于`24`，所以`return`语句的值等于`42`。

     注意，由于`next`方法的参数表示上一个`yield`表达式的返回值，所以**在第一次使用`next`方法时，给`next`传递参数是无效的**。V8 引擎直接忽略第一次使用`next`方法时的参数，只有从第二次使用`next`方法开始，参数才是有效的。从语义上讲，第一个`next`方法用来启动迭代器对象，所以不用带有参数。

     再看一个通过`next`方法的参数，向 Generator 函数内部输入值的例子。

     ```python
     function* dataConsumer() {
       console.log('Started');
       console.log(`1. ${yield}`);
       console.log(`2. ${yield}`);
       return 'result';
     }
     
     let genObj = dataConsumer();
     genObj.next();
     // Started
     genObj.next('a')
     // 1. a
     genObj.next('b')
     // 2. b
     ```

     上面代码是一个很直观的例子，每次通过`next`方法向 Generator 函数输入值，然后打印出来。

     **如果想要第一次调用`next`方法时，就能够输入值，可以在 Generator 函数外面再包一层**。（高阶函数）

     ```python
     function wrapper(generatorFunction) {
       return function (...args) {
         let generatorObject = generatorFunction(...args);
         generatorObject.next();
         return generatorObject;
       };
     }
     
     const wrapped = wrapper(function* () {
       console.log(`First input: ${yield}`);
       return 'DONE';
     });
     
     wrapped().next('hello!')
     // First input: hello!
     ```

     上面代码中，Generator 函数如果不用`wrapper`先包一层，是无法第一次调用`next`方法，就输入参数的。

  - #### for...of 循环

     `for...of`循环可以自动遍历 Generator 函数运行时生成的迭代器对象，此时不再需要调用`next`方法。因为这个迭代器对象，它内部的`[Symbol.iterator]()`执行后返回的迭代器对象还是它自身。

     ```js
     function* foo() {
       yield 1;
       yield 2;
       yield 3;
       yield 4;
       yield 5;
       return 6;
     }
     
     for (let v of foo()) {
       console.log(v);
     }
     // 1 2 3 4 5
     ```

     上面代码使用`for...of`循环，依次显示 5 个`yield`表达式的值。这里需要注意，**一旦`next`方法的返回对象的`done`属性为`true`，`for...of`循环就会中止，且不包含该返回对象**。所以上面代码的`return`语句返回的`6`，不包括在`for...of`循环之中。

     下面是一个利用 Generator 函数和`for...of`循环，实现斐波那契数列的例子。

     ```js
     function* fibonacci() {
       let [prev, curr] = [0, 1];
       for (;;) {
         yield curr;
         [prev, curr] = [curr, prev + curr];
       }
     }
     
     for (let n of fibonacci()) {
       if (n > 1000) break;
       console.log(n);
     }
     ```

     从上面代码可见，使用`for...of`语句时不需要使用`next`方法。

     利用`for...of`循环，可以写出遍历任意对象的方法。原生的 JS 对象没有遍历接口，无法使用`for...of`循环，通过 Generator 函数为它加上这个接口，就可以用了。

     ```js
     function* objectEntries(obj) {
       let propKeys = Reflect.ownKeys(obj);
     
       for (let propKey of propKeys) {
         yield [propKey, obj[propKey]];
       }
     }
     
     let jane = { first: 'Jane', last: 'Doe' };
     
     for (let [key, value] of objectEntries(jane)) {
       console.log(`${key}: ${value}`);
     }
     // first: Jane
     // last: Doe
     ```

     上面代码中，对象`jane`原生不具备 Iterator 接口，无法用`for...of`遍历。这时，我们通过 Generator 函数`objectEntries`为它加上迭代器接口，就可以用`for...of`遍历了。加上迭代器接口的另一种写法是，将 Generator 函数加到对象的`Symbol.iterator`属性上面。

     ```js
     function* objectEntries() {
       let propKeys = Object.keys(this);
     
       for (let propKey of propKeys) {
         yield [propKey, this[propKey]];
       }
     }
     
     let jane = { first: 'Jane', last: 'Doe' };
     
     jane[Symbol.iterator] = objectEntries;
     
     for (let [key, value] of jane) {
       console.log(`${key}: ${value}`);
     }
     // first: Jane
     // last: Doe
     ```

     **除了`for...of`循环以外，扩展运算符（`...`）、解构赋值和`Array.from`方法内部调用的，都是迭代器接口。这意味着，它们都可以将 Generator 函数返回的 Iterator 迭代器对象，作为参数**。

     ```js
     function* numbers () {
       yield 1
       yield 2
       return 3
       yield 4
     }
     
     // 扩展运算符
     [...numbers()] // [1, 2]
     
     // Array.from 方法
     Array.from(numbers()) // [1, 2]
     
     // 解构赋值
     let [x, y] = numbers();
     x // 1
     y // 2
     
     // for...of 循环
     for (let n of numbers()) {
       console.log(n)
     }
     // 1
     // 2
     ```

  - #### `Generator.prototype.throw()`

     Generator 函数返回的迭代器对象有一个`throw`方法，调用后会中断 Generator 函数的执行，相当于往生成器函数内部注入 `throw` 语句。即：**`throw()`方法的执行，可以在生成器函数的里面抛出错误**。**如果该错误被捕获处理了，那么会程序还会向后执行到下一个`yield`语句，相当于执行了一次`next()`**。

     ```js
     var g = function* () {
       try {
         yield;
       } catch (e) {
         console.log('内部捕获', e);
       }
     };
     
     var i = g();
     i.next();
     
     try {
       i.throw('a');
       i.throw('b');
     } catch (e) {
       console.log('外部捕获', e);
     }
     // 内部捕获 a
     // 外部捕获 b
     ```

     上面代码中，迭代器对象`i`连续抛出两个错误。第一个错误被 Generator 函数体内的`catch`语句捕获。`i`第二次抛出错误，由于 Generator 函数内部的`catch`语句已经执行过了，不会再捕捉到这个错误了，所以这个错误就被抛出了 Generator 函数体，被函数体外的`catch`语句捕获。（`throw()`抛出的错误如果被正确捕获处理了，那么会程序还会向后执行到下一个`yield`语句，相当于执行了一次`next()`。由于此时已经到了生成器函数的末尾了，末尾并没有被`catch`块包住，因此继续往外抛，被外部的`catch`语句捕获。这和普通的`throw`语句的工作原理是一样的）

     **`throw`方法可以接受一个参数，该参数可以被`catch`语句接收**，建议抛出`Error`对象的实例。

     ```js
     var g = function* () {
       try {
         yield;
       } catch (e) {
         console.log(e);
       }
     };
     
     var i = g();
     i.next();
     i.throw(new Error('出错了！'));
     // Error: 出错了！(…)
     ```

     注意，不要混淆迭代器对象的`throw`方法和全局的`throw`命令。上面代码的错误，是用迭代器对象的`throw`方法抛出的，而不是用`throw`命令抛出的。后者只能被函数体外的`catch`语句捕获。

     ```js
     var g = function* () {
       while (true) {
         try {
           yield;
         } catch (e) {
           if (e != 'a') throw e;
           console.log('内部捕获', e);
         }
       }
     };
     
     var i = g();
     i.next();
     
     try {
       throw new Error('a');
       throw new Error('b');
     } catch (e) {
       console.log('外部捕获', e);
     }
     // 外部捕获 [Error: a]
     ```

     上面代码之所以只捕获了`a`，是因为函数体外的`catch`语句块，捕获了抛出的`a`错误以后，就不会再继续`try`代码块里面剩余的语句了。

     如果 Generator 函数内部没有部署`try...catch`代码块，那么`throw`方法抛出的错误会层层往外抛，直到将被外部`try...catch`代码块捕获。（这和普通的`throw`语句的工作原理是一样的）

     ```js
     var g = function* () {
       while (true) {
         yield;
         console.log('内部捕获', e);
       }
     };
     
     var i = g();
     i.next();
     
     try {
       i.throw('a');
       i.throw('b');
     } catch (e) {
       console.log('外部捕获', e);
     }
     // 外部捕获 a
     ```

     上面代码中，Generator 函数`g`内部没有部署`try...catch`代码块，所以抛出的错误直接被外部`catch`代码块捕获。

     如果 Generator 函数内部和外部，都没有部署`try...catch`代码块，那么程序将报错，直接中断执行。

     ```js
     var gen = function* gen(){
       yield console.log('hello');
       yield console.log('world');
     }
     
     var g = gen();
     g.next();
     g.throw();
     // hello
     // Uncaught undefined
     ```

     上面代码中，`g.throw`抛出错误以后，没有任何`try...catch`代码块可以捕获这个错误，导致程序报错，中断执行。

     **`throw`方法抛出的错误要被内部捕获，前提是必须至少执行过一次`next`方法**。

     ```js
     function* gen() {
       try {
         yield 1;
       } catch (e) {
         console.log('内部捕获');
       }
     }
     
     var g = gen();
     g.throw(1);
     // Uncaught 1
     ```

     上面代码中，`g.throw(1)`执行时，`next`方法一次都没有执行过。这时，**抛出的错误不会被内部捕获，而是直接在外部抛出，导致程序出错**。这种行为其实很好理解，**因为第一次执行`next`方法，等同于启动执行 Generator 函数的内部代码，否则 Generator 函数还没有开始执行，这时`throw`方法抛错只可能抛出在函数外部**。

     **`throw`方法被内部捕获以后，会附带执行到下一条`yield`表达式，这种情况下等同于执行一次`next`方法**。

     ```js
     var gen = function* gen(){
       try {
         yield 1;
       } catch (e) {
         yield 2;
       }
       yield 3;
     }
     
     var g = gen();
     g.next() // { value:1, done:false }
     g.throw() // { value:2, done:false }
     g.next() // { value:3, done:false }
     g.next() // { value:undefined, done:true }
     ```

     上面代码中，`g.throw`方法被内部捕获以后，等同于执行了一次`next`方法，所以返回`{ value:2, done:false }`。另外，也可以看到，只要 Generator 函数内部部署了`try...catch`代码块，那么迭代器的`throw`方法抛出的错误，不影响下一次遍历。

     另外，**`throw`命令与`throw()`方法是无关的，两者互不影响**。

     ```js
     var gen = function* gen(){
       yield console.log('hello');
       yield console.log('world');
     }
     
     var g = gen();
     g.next();
     
     try {
       throw new Error();
     } catch (e) {
       g.next();
     }
     // hello
     // world
     ```

     上面代码中，`throw`命令抛出的错误不会影响到迭代器的状态，所以两次执行`next`方法，都进行了正确的操作。

     这种函数体内捕获错误的机制，大大方便了对错误的处理。多个`yield`表达式，可以只用一个`try...catch`代码块来捕获错误。如果使用回调函数的写法，想要捕获多个错误，就不得不为每个函数内部写一个错误处理语句，现在只在 Generator 函数内部写一次`catch`语句就可以了。

     **Generator 函数体外抛出的错误，可以在函数体内捕获；反过来，Generator 函数体内抛出的错误，也可以被函数体外的`catch`块捕获**。

     ```js
     function* foo() {
       var x = yield 3;
       var y = x.toUpperCase();
       yield y;
     }
     
     var it = foo();
     
     it.next(); // { value:3, done:false }
     
     try {
       it.next(42);
     } catch (err) {
       console.log(err);
     }
     ```

     上面代码中，第二个`next`方法向函数体内传入一个参数 42，数值是没有`toUpperCase`方法的，所以会抛出一个 TypeError 错误，被函数体外的`catch`捕获。

     注意：**一旦 Generator 函数执行时内部抛出了错误，且没有被内部捕获处理，那么该函数的状态就变为结束状态了。此后再调用`next()`方法将返回一个`value`属性等于`undefined`、`done`属性等于`true`的对象，即 JS 引擎认为这个 Generator 已经运行结束了**。

     ```js
     function* g() {
       yield 1;
       console.log('throwing an exception');
       throw new Error('generator broke!');
       yield 2;
       yield 3;
     }
     
     function log(generator) {
       var v;
       console.log('starting generator');
       try {
         v = generator.next();
         console.log('第一次运行next方法', v);
       } catch (err) {
         console.log('捕捉错误', v);
       }
       try {
         v = generator.next();
         console.log('第二次运行next方法', v);
       } catch (err) {
         console.log('捕捉错误', v);
       }
       try {
         v = generator.next();
         console.log('第三次运行next方法', v);
       } catch (err) {
         console.log('捕捉错误', v);
       }
       console.log('caller done');
     }
     
     log(g());
     // starting generator
     // 第一次运行next方法 { value: 1, done: false }
     // throwing an exception
     // 捕捉错误 { value: 1, done: false }
     // 第三次运行next方法 { value: undefined, done: true }
     // caller done
     ```

     上面代码一共三次运行`next`方法，第二次运行的时候会抛出错误，然后第三次运行的时候，Generator 函数就已经结束了，不再执行下去了。

  - #### `Generator.prototype.return()`

     Generator 函数返回的迭代器对象，还有一个**`return()`方法，可以返回给定的值，并且将 Generator 函数终止掉**。相当于注入了一个`return`语句并执行到这个`return`处。它的返回值和`next()`方法一样，是一个信息对象（具有`done`和`value`属性）。

     ```js
     function* gen() {
       yield 1;
       yield 2;
       yield 3;
     }
     
     var g = gen();
     
     g.next()        // { value: 1, done: false }
     g.return('foo') // { value: "foo", done: true }
     g.next()        // { value: undefined, done: true }
     ```

     上面代码中，迭代器对象`g`调用`return()`方法后，返回值的`value`属性就是`return()`方法的参数`foo`。并且，Generator 函数的遍历就终止了，返回值的`done`属性为`true`，以后再调用`next()`方法，`done`属性总是返回`true`。

     如果`return()`方法调用时，不提供参数，则返回值的`value`属性为`undefined`。

     ```js
     function* gen() {
       yield 1;
       yield 2;
       yield 3;
     }
     
     var g = gen();
     
     g.next() // { value: 1, done: false }
     g.return() // { value: undefined, done: true }
     ```

     **如果 Generator 函数内部有`try...finally`代码块，且正在执行`try`代码块，那么`return()`方法会导致立刻进入`finally`代码块，执行完以后，整个函数才会结束**。因为`finally`在`return`之后执行。

     ```js
     function* numbers () {
       yield 1;
       try {
         yield 2;
         yield 3;
       } finally {
         yield 4;
         yield 5;
       }
       yield 6;
     }
     var g = numbers();
     g.next() // { value: 1, done: false }
     g.next() // { value: 2, done: false }
     g.return(7) // { value: 4, done: false }
     g.next() // { value: 5, done: false }
     g.next() // { value: 7, done: true }
     ```

     上面代码中，调用`return()`方法后，就开始执行`finally`代码块，不执行`try`里面剩下的代码了，然后等到`finally`代码块执行完，再返回`return()`方法指定的返回值。

  - #### `next()`、`throw()`、`return()` 的共同点

     `next()`、`throw()`、`return()`这三个方法本质上是同一件事，可以放在一起理解。它们的作用都是让 Generator 函数恢复执行，并且使用不同的语句替换`yield`表达式。

     **`next()`是将`yield`表达式替换成一个值**。

     ```js
     const g = function* (x, y) {
       let result = yield x + y;
       return result;
     };
     
     const gen = g(1, 2);
     gen.next(); // Object {value: 3, done: false}
     
     gen.next(1); // Object {value: 1, done: true}
     // 相当于将 let result = yield x + y
     // 替换成 let result = 1;
     ```

     上面代码中，第二个`next(1)`方法就相当于将`yield`表达式替换成一个值`1`。如果`next`方法没有参数，就相当于替换成`undefined`。

     **`throw()`是将`yield`表达式替换成一个`throw`语句**。

     ```js
     gen.throw(new Error('出错了')); // Uncaught Error: 出错了
     // 相当于将 let result = yield x + y
     // 替换成 let result = throw(new Error('出错了'));
     ```

     **`return()`是将`yield`表达式替换成一个`return`语句**。

     ```js
     gen.return(2); // Object {value: 2, done: true}
     // 相当于将 let result = yield x + y
     // 替换成 let result = return 2;
     ```

  - #### yield* 表达式

     如果在 Generator 函数内部，调用另一个 Generator 函数。需要在前者的函数体内部，自己手动完成遍历。

     ```js
     function* foo() {
       yield 'a';
       yield 'b';
     }
     
     function* bar() { // 执行两次next()即可结束
       yield 'x';
       // 手动遍历 foo()
       for (let i of foo()) {
         console.log(i);
       }
       yield 'y';
     }
     
     for (let v of bar()){
       console.log(v);
     }
     // x
     // a
     // b
     // y
     ```

     上面代码中，`foo`和`bar`都是 Generator 函数，在`bar`里面调用`foo`，就需要手动写`for of`遍历`foo`函数。如果有多个 Generator 函数嵌套，写起来就非常麻烦。

     作为解决办法，**ES6 提供了`yield*`表达式，用来在一个 Generator 函数里面执行另一个 Generator 函数**。相当于用`yield*`将一个 Generator 函数返回的所有状态进行了展开，展开为了多个`yield`语句。

     ```js
     function* foo() {
       yield 'a';
       yield 'b';
     }
     
     function* bar() {
       yield 'x';
       yield* foo();
       yield 'y';
     }
     
     // 等同于
     function* bar() {
       yield 'x';
       yield 'a';
       yield 'b';
       yield 'y';
     }
     
     // 等同于
     function* bar() {
       yield 'x';
       for (let v of foo()) {
         yield v;
       }
       yield 'y';
     }
     
     for (let v of bar()){
       console.log(v);
     }
     // "x"
     // "a"
     // "b"
     // "y"
     ```

     再来看一个对比的例子。

     ```js
     function* inner() {
       yield 'hello!';
     }
     
     function* outer1() {
       yield 'open';
       yield inner();
       yield 'close';
     }
     
     var gen = outer1()
     gen.next().value // "open"
     gen.next().value // 返回一个迭代器对象
     gen.next().value // "close"
     
     function* outer2() {
       yield 'open'
       yield* inner()
       yield 'close'
     }
     
     var gen = outer2()
     gen.next().value // "open"
     gen.next().value // "hello!"
     gen.next().value // "close"
     ```

     上面例子中，`outer2`使用了`yield*`，`outer1`没使用。结果就是，`outer1`返回一个迭代器对象，`outer2`返回该迭代器对象的内部值。

     从语法角度看，**如果`yield`表达式后面跟的是一个迭代器对象，需要在`yield`表达式后面加上星号，表明它返回的是一个迭代器对象。这被称为`yield*`表达式**。（否则就会将该迭代器对象当作`yield`语句的产出了）

     ```js
     let delegatedIterator = (function* () {
       yield 'Hello!';
       yield 'Bye!';
     }());
     
     let delegatingIterator = (function* () {
       yield 'Greetings!';
       yield* delegatedIterator;
       yield 'Ok, bye.';
     }());
     
     for(let value of delegatingIterator) {
       console.log(value);
     }
     // "Greetings!
     // "Hello!"
     // "Bye!"
     // "Ok, bye."
     ```

     上面代码中，`delegatingIterator`是代理者，`delegatedIterator`是被代理者。由于`yield* delegatedIterator`语句得到的值，是一个迭代器，所以要用星号表示。运行结果就是使用一个迭代器，遍历了多个 Generator 函数，有递归的效果。

     **`yield*`后面的 Generator 函数（里面没有`return`语句时），等同于在 Generator 函数内部，部署一个`for...of`循环**。

     ```js
     function* concat(iter1, iter2) {
       yield* iter1;
       yield* iter2;
     }
     
     // 等同于
     
     function* concat(iter1, iter2) {
       for (var value of iter1) {
         yield value;
       }
       for (var value of iter2) {
         yield value;
       }
     }
     ```

     上面代码说明，`yield*`后面的 Generator 函数（里面没有`return`语句时），不过是`for...of`的一种简写形式，完全可以用后者替代前者。

     在有`return`语句时，**`return` 的值作为 `yield*` 表达式的返回值**，可以用`var value = yield* genFunc()`的形式获取 Generator 函数中`return`语句的值。

     ```js
     function* foo() {
       yield 'a';
       yield 'b';
       return 'foo';
     }
     
     function* bar() {
       yield 'x';
       console.log(yield* foo());
       yield 'y';
     }
     
     const b = bar();
     console.log(b.next());
     console.log(b.next());
     console.log(b.next());
     console.log(b.next());
     
     /*
     { value: 'x', done: false }
     { value: 'a', done: false }
     { value: 'b', done: false }
     foo
     { value: 'y', done: false }
     */
     ```

     如果`yield*`后面跟着一个数组，由于数组原生支持迭代器，因此就会遍历数组成员。

     ```js
     function* gen(){
       yield* ["a", "b", "c"];
     }
     
     gen().next() // { value:"a", done:false }
     ```

     上面代码中，`yield`命令后面如果不加星号，返回的是整个数组，加了星号就表示返回的是数组的迭代器对象。

     实际上，**任何可迭代结构（或迭代器对象）都可以被`yield*`遍历，`yield*`相当于是在对后面的可迭代结构进行`for...of`**。

     ```js
     let read = (function* () {
       yield 'hello';
       yield* 'hello';
     })();
     
     read.next().value // "hello"
     read.next().value // "h"
     ```

     上面代码中，`yield`表达式返回整个字符串，`yield*`语句返回单个字符。因为字符串具有 Iterator 接口，所以被`yield*`遍历。

     如果被代理的 Generator 函数有`return`语句，那么就可以向代理它的 Generator 函数返回数据。

     ```js
     function* foo() {
       yield 2;
       yield 3;
       return "foo";
     }
     
     function* bar() {
       yield 1;
       var v = yield* foo();
       console.log("v: " + v);
       yield 4;
     }
     
     var it = bar();
     
     it.next()
     // {value: 1, done: false}
     it.next()
     // {value: 2, done: false}
     it.next()
     // {value: 3, done: false}
     it.next();
     // "v: foo"
     // {value: 4, done: false}
     it.next()
     // {value: undefined, done: true}
     ```

     上面代码在第四次调用`next`方法的时候，屏幕上会有输出，这是因为函数`foo`的`return`语句，向函数`bar`提供了返回值。

     再看一个例子。

     ```js
     function* genFuncWithReturn() {
       yield 'a';
       yield 'b';
       return 'The result';
     }
     function* logReturned(genObj) {
       let result = yield* genObj;
       console.log(result);
     }
     
     [...logReturned(genFuncWithReturn())]
     // The result
     // 值为 [ 'a', 'b' ]
     ```

     上面代码中，存在两次遍历。第一次是扩展运算符遍历函数`logReturned`返回的迭代器对象，第二次是`yield*`语句遍历函数`genFuncWithReturn`返回的迭代器对象。这两次遍历的效果是叠加的，最终表现为扩展运算符遍历函数`genFuncWithReturn`返回的迭代器对象。所以，最后的数据表达式得到的值等于`[ 'a', 'b' ]`。但是，函数`genFuncWithReturn`的`return`语句的返回值`The result`，会返回给函数`logReturned`内部的`result`变量，因此会有终端输出。

     **`yield*`命令可以很方便地取出嵌套数组的所有成员**。

     ```js
     function* iterTree(tree) {
       if (Array.isArray(tree)) {
         for(let i=0; i < tree.length; i++) {
           yield* iterTree(tree[i]);
         }
       } else {
         yield tree;
       }
     }
     
     const tree = [ 'a', ['b', 'c'], ['d', 'e'] ];
     
     for(let x of iterTree(tree)) {
       console.log(x);
     }
     // a
     // b
     // c
     // d
     // e
     ```

     由于扩展运算符`...`默认调用 Iterator 接口，所以上面这个函数也可以用于嵌套数组的平铺。

     ```js
     [...iterTree(tree)] // ["a", "b", "c", "d", "e"]
     ```

     下面是一个稍微复杂的例子，使用`yield*`语句遍历完全二叉树。

     ```js
     // 下面是二叉树的构造函数，
     // 三个参数分别是左树、当前节点和右树
     function Tree(left, label, right) {
       this.left = left;
       this.label = label;
       this.right = right;
     }
     
     // 下面是中序（inorder）遍历函数。
     // 由于返回的是一个迭代器，所以要用generator函数。
     // 函数体内采用递归算法，所以左树和右树要用yield*遍历
     function* inorder(t) {
       if (t) {
         yield* inorder(t.left);
         yield t.label;
         yield* inorder(t.right);
       }
     }
     
     // 下面生成二叉树
     function make(array) {
       // 判断是否为叶节点
       if (array.length == 1) return new Tree(null, array[0], null);
       return new Tree(make(array[0]), array[1], make(array[2]));
     }
     let tree = make([[['a'], 'b', ['c']], 'd', [['e'], 'f', ['g']]]);
     
     // 遍历二叉树
     var result = [];
     for (let node of inorder(tree)) {
       result.push(node);
     }
     
     result
     // ['a', 'b', 'c', 'd', 'e', 'f', 'g']
     ```

  - #### 作为对象属性的 Generator 函数

     如果对象中的方法是 Generator 函数，可以简写成下面的形式。即在方法前加`*`号。

     ```js
     let obj = {
       * myGeneratorMethod() { // 也可以：*myGeneratorMethod，但是不推荐
         ···
       }
     };
     ```

     上面代码中，`myGeneratorMethod`属性前面有一个星号，表示这个属性是一个 Generator 函数。

     它的完整形式如下，与上面的写法是等价的。

     ```js
     let obj = {
       myGeneratorMethod: function* () {
         // ···
       }
     };
     ```

  - #### Generator 函数的`this`

     **Generator 函数总是返回一个迭代器对象，ES6 规定这个迭代器对象是 Generator 函数的实例，也继承了 Generator 函数的`prototype`对象上的方法**。

     ```js
     function* g() {}
     
     g.prototype.hello = function () {
       return 'hi!';
     };
     
     let obj = g();
     
     obj instanceof g // true
     obj.hello() // 'hi!'
     ```

     上面代码表明，Generator 函数`g`返回的迭代器`obj`，是`g`的实例，而且继承了`g.prototype`。

     但是，**Generator 函数不能当作普通的构造函数**，因为 Generator 函数回的总是迭代器对象，而不是返回`this`指的对象。

     ```js
     function* g() {
       this.a = 11;
     }
     
     let obj = g();
     obj.next();
     obj.a // undefined
     ```

     上面代码中，Generator 函数`g`在`this`对象上面添加了一个属性`a`，但是`obj`对象拿不到这个属性。

     因此，**Generator 函数也不能跟`new`命令一起用，会报错**。

     ```js
     function* F() {
       yield this.x = 2;
       yield this.y = 3;
     }
     
     new F()
     // TypeError: F is not a constructor
     ```

     上面代码中，`new`命令跟构造函数`F`一起使用，结果报错，因为`F`不是构造函数。

     并且生成器函数内部的 `this` 和普通函数一样，取决于**调用时的上下文**（谁调用的）。

     有没有办法让 Generator 函数返回一个正常的对象实例，既可以用`next`方法，又可以获得正常的`this`？

     下面是一个变通方法。首先，生成一个空对象，使用`call`方法绑定 Generator 函数内部的`this`。这样，构造函数调用以后，这个空对象就是 Generator 函数的实例对象了。

     ```js
     function* F() {
       this.a = 1;
       yield this.b = 2;
       yield this.c = 3;
     }
     var obj = {};
     var f = F.call(obj);
     
     f.next();  // Object {value: 2, done: false}
     f.next();  // Object {value: 3, done: false}
     f.next();  // Object {value: undefined, done: true}
     
     obj.a // 1
     obj.b // 2
     obj.c // 3
     ```

     上面代码中，首先是`F`内部的`this`对象绑定`obj`对象，然后调用它，返回一个 Iterator 对象。这个对象执行三次`next`方法（因为`F`内部有两个`yield`表达式），完成 F 内部所有代码的运行。这时，所有内部属性都绑定在`obj`对象上了，因此`obj`对象也就成了`F`的实例。

     上面代码中，执行的是迭代器对象`f`，但是生成的对象实例是`obj`，有没有办法将这两个对象统一呢？

     一个办法就是将`obj`换成`F.prototype`。

     ```js
     function* F() {
       this.a = 1;
       yield this.b = 2;
       yield this.c = 3;
     }
     var f = F.call(F.prototype);
     
     f.next();  // Object {value: 2, done: false}
     f.next();  // Object {value: 3, done: false}
     f.next();  // Object {value: undefined, done: true}
     
     f.a // 1
     f.b // 2
     f.c // 3
     ```

     再将`F`改成构造函数，就可以对它执行`new`命令了。

     ```js
     function* gen() {
       this.a = 1;
       yield this.b = 2;
       yield this.c = 3;
     }
     
     function F() {
       return gen.call(gen.prototype);
     }
     
     var f = new F();
     
     f.next();  // Object {value: 2, done: false}
     f.next();  // Object {value: 3, done: false}
     f.next();  // Object {value: undefined, done: true}
     
     f.a // 1
     f.b // 2
     f.c // 3
     ```

  - #### 含义

    - ##### Generator 与状态机

      Generator 是实现状态机的最佳结构。比如，下面的`clock`函数就是一个状态机。

      ```js
      var ticking = true;
      var clock = function() {
        if (ticking)
          console.log('Tick!');
        else
          console.log('Tock!');
        ticking = !ticking;
      }
      ```

      上面代码的`clock`函数一共有两种状态（`Tick`和`Tock`），每运行一次，就改变一次状态。这个函数如果用 Generator 实现，就是下面这样。

      ```js
      var clock = function* () {
        while (true) {
          console.log('Tick!');
          yield;
          console.log('Tock!');
          yield;
        }
      };
      ```

      上面的 Generator 实现与 ES5 实现对比，可以看到少了用来保存状态的外部变量`ticking`，这样就更简洁，更安全（状态不会被非法篡改）、更符合函数式编程的思想，在写法上也更优雅。Generator 之所以可以不用外部变量保存状态，是因为它本身就包含了一个状态信息，即目前是否处于暂停态。

    - ##### Generator 与协程

      协程（coroutine）是一种程序运行的方式，可以理解成“协作的线程”或“协作的函数”。协程既可以用单线程实现，也可以用多线程实现。前者是一种特殊的子例程，后者是一种特殊的线程。

      **（1）协程与子例程的差异**

      传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式，只有当调用的子函数完全执行完毕，才会结束执行父函数。协程与其不同，多个线程（单线程情况下，即多个函数）可以并行执行，但是只有一个线程（或函数）处于正在运行的状态，其他线程（或函数）都处于暂停态（suspended），线程（或函数）之间可以交换执行权。也就是说，一个线程（或函数）执行到一半，可以暂停执行，将执行权交给另一个线程（或函数），等到稍后收回执行权的时候，再恢复执行。这种可以并行执行、交换执行权的线程（或函数），就称为协程。

      从实现上看，在内存中，子例程只使用一个栈（stack），而协程是同时存在多个栈，但只有一个栈是在运行状态，也就是说，协程是以多占用内存为代价，实现多任务的并行。

      **（2）协程与普通线程的差异**

      不难看出，协程适合用于多任务运行的环境。在这个意义上，它与普通的线程很相似，都有自己的执行上下文、可以分享全局变量。它们的不同之处在于，同一时间可以有多个线程处于运行状态，但是运行的协程只能有一个，其他协程都处于暂停状态。此外，普通的线程是抢先式的，到底哪个线程优先得到资源，必须由运行环境决定，但是协程是合作式的，执行权由协程自己分配。

      由于 JS 是单线程语言，只能保持一个调用栈。引入协程以后，每个任务可以保持自己的调用栈。这样做的最大好处，就是抛出错误的时候，可以找到原始的调用栈。不至于像异步操作的回调函数那样，一旦出错，原始的调用栈早就结束。

      Generator 函数是 ES6 对协程的实现，但属于不完全实现。Generator 函数被称为“半协程”（semi-coroutine），意思是只有 Generator 函数的调用者，才能将程序的执行权还给 Generator 函数。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。

      如果将 Generator 函数当作协程，完全可以将多个需要互相协作的任务写成 Generator 函数，它们之间使用`yield`表达式交换控制权。

    - ##### Generator 与上下文

      JS 代码运行时，会产生一个全局的上下文环境（context，又称运行环境），包含了当前所有的变量和对象。然后，执行函数（或块级代码）的时候，又会在当前上下文环境的上层，产生一个函数运行的上下文，变成当前（active）的上下文，由此形成一个上下文环境的堆栈（context stack）。

      这个堆栈是“后进先出”的数据结构，最后产生的上下文环境首先执行完成，退出堆栈，然后再执行完成它下层的上下文，直至所有代码执行完成，堆栈清空。

      Generator 函数不是这样，它执行产生的上下文环境，一旦遇到`yield`命令，就会暂时退出堆栈，但是并不消失，里面的所有变量和对象会冻结在当前状态。等到对它执行`next`命令时，这个上下文环境又会重新加入调用栈，冻结的变量和对象恢复执行。

      ```js
      function* gen() {
        yield 1;
        return 2;
      }
      
      let g = gen();
      
      console.log(
        g.next().value,
        g.next().value,
      );
      ```

      上面代码中，第一次执行`g.next()`时，Generator 函数`gen`的上下文会加入堆栈，即开始运行`gen`内部的代码。等遇到`yield 1`时，`gen`上下文退出堆栈，内部状态冻结。第二次执行`g.next()`时，`gen`上下文重新加入堆栈，变成当前的上下文，重新恢复执行。

  - #### 应用

    Generator 可以暂停函数执行，返回任意表达式的值。这种特点使得 Generator 有多种应用场景。

    - ##### 异步操作的同步化表达

      Generator 函数的暂停执行的效果，意味着可以把异步操作写在`yield`表达式里面，等到调用`next`方法时再往后执行。这实际上等同于不需要写回调函数了，因为异步操作的后续操作可以放在`yield`表达式下面，反正要等到调用`next`方法时再执行。所以，Generator 函数的一个重要实际意义就是用来处理异步操作，改写回调函数。

      ```js
      function* loadUI() {
        showLoadingScreen();
        yield loadUIDataAsynchronously();
        hideLoadingScreen();
      }
      var loader = loadUI();
      // 加载UI
      loader.next()
      
      // 卸载UI
      loader.next()
      ```

      上面代码中，第一次调用`loadUI`函数时，该函数不会执行，仅返回一个迭代器。下一次对该迭代器调用`next`方法，则会显示`Loading`界面（`showLoadingScreen`），并且异步加载数据（`loadUIDataAsynchronously`）。等到数据加载完成，再一次使用`next`方法，则会隐藏`Loading`界面。可以看到，这种写法的好处是所有`Loading`界面的逻辑，都被封装在一个函数，按部就班非常清晰。

      Ajax 是典型的异步操作，通过 Generator 函数部署 Ajax 操作，可以用同步的方式表达。

      ```js
      function* main() {
        var result = yield request("http://some.url");
        var resp = JSON.parse(result);
        console.log(resp.value);
      }
      
      function request(url) {
        makeAjaxCall(url, function(response){
          it.next(response);
        });
      }
      
      var it = main();
      it.next();
      ```

      上面代码的`main`函数，就是通过 Ajax 操作获取数据。可以看到，除了多了一个`yield`，它几乎与同步操作的写法完全一样。注意，`makeAjaxCall`函数中的`next`方法，必须加上`response`参数，因为`yield`表达式，本身是没有值的，总是等于`undefined`。

      下面是另一个例子，通过 Generator 函数逐行读取文本文件。

      ```js
      function* numbers() {
        let file = new FileReader("numbers.txt");
        try {
          while(!file.eof) {
            yield parseInt(file.readLine(), 10);
          }
        } finally {
          file.close();
        }
      }
      ```

      上面代码打开文本文件，使用`yield`表达式可以手动逐行读取文件。

    - ##### 控制流管理

      如果有一个多步操作非常耗时，采用回调函数，可能会写成下面这样。

      ```js
      step1(function (value1) {
        step2(value1, function(value2) {
          step3(value2, function(value3) {
            step4(value3, function(value4) {
              // Do something with value4
            });
          });
        });
      });
      ```

      采用 Promise 改写上面的代码。

      ```js
      Promise.resolve(step1)
        .then(step2)
        .then(step3)
        .then(step4)
        .then(function (value4) {
          // Do something with value4
        }, function (error) {
          // Handle any error from step1 through step4
        })
        .done();
      ```

      上面代码已经把回调函数，改成了直线执行的形式，但是加入了大量 Promise 的语法。Generator 函数可以进一步改善代码运行流程。

      ```js
      function* longRunningTask(value1) {
        try {
          var value2 = yield step1(value1);
          var value3 = yield step2(value2);
          var value4 = yield step3(value3);
          var value5 = yield step4(value4);
          // Do something with value4
        } catch (e) {
          // Handle any error from step1 through step4
        }
      }
      ```

      然后，使用一个函数，按次序自动执行所有步骤。

      ```js
      scheduler(longRunningTask(initialValue));
      
      function scheduler(task) {
        var taskObj = task.next(task.value);
        // 如果Generator函数未结束，就继续调用
        if (!taskObj.done) {
          task.value = taskObj.value
          scheduler(task);
        }
      }
      ```

      注意，上面这种做法，只适合同步操作，即所有的`task`都必须是同步的，不能有异步操作。因为这里的代码一得到返回值，就继续往下执行，没有判断异步操作何时完成。如果要控制异步的操作流程，详见后面的《异步操作》一章。

      下面，利用`for...of`循环会自动依次执行`yield`命令的特性，提供一种更一般的控制流管理的方法。

      ```js
      let steps = [step1Func, step2Func, step3Func];
      
      function* iterateSteps(steps){
        for (var i=0; i< steps.length; i++){
          var step = steps[i];
          yield step();
        }
      }
      ```

      上面代码中，数组`steps`封装了一个任务的多个步骤，Generator 函数`iterateSteps`则是依次为这些步骤加上`yield`命令。

      将任务分解成步骤之后，还可以将项目分解成多个依次执行的任务。

      ```js
      let jobs = [job1, job2, job3];
      
      function* iterateJobs(jobs){
        for (var i=0; i< jobs.length; i++){
          var job = jobs[i];
          yield* iterateSteps(job.steps);
        }
      }
      ```

      上面代码中，数组`jobs`封装了一个项目的多个任务，Generator 函数`iterateJobs`则是依次为这些任务加上`yield*`命令。

      最后，就可以用`for...of`循环一次性依次执行所有任务的所有步骤。

      ```js
      for (var step of iterateJobs(jobs)){
        console.log(step.id);
      }
      ```

      再次提醒，上面的做法只能用于所有步骤都是同步操作的情况，不能有异步操作的步骤。如果想要依次执行异步的步骤，必须使用后面的《异步操作》一章介绍的方法。

      `for...of`的本质是一个`while`循环，所以上面的代码实质上执行的是下面的逻辑。

      ```js
      var it = iterateJobs(jobs);
      var res = it.next();
      
      while (!res.done){
        var result = res.value;
        // ...
        res = it.next();
      }
      ```

    - ##### 部署 Iterator 接口

      利用 Generator 函数，可以在任意对象上部署 Iterator 接口。

      ```js
      function* iterEntries(obj) {
        let keys = Object.keys(obj);
        for (let i=0; i < keys.length; i++) {
          let key = keys[i];
          yield [key, obj[key]];
        }
      }
      
      let myObj = { foo: 3, bar: 7 };
      
      for (let [key, value] of iterEntries(myObj)) {
        console.log(key, value);
      }
      
      // foo 3
      // bar 7
      ```

      上述代码中，`myObj`是一个普通对象，通过`iterEntries`函数，就有了 Iterator 接口。也就是说，可以在任意对象上部署`next`方法。

      下面是一个对数组部署 Iterator 接口的例子，尽管数组原生具有这个接口。

      ```js
      function* makeSimpleGenerator(array){
        var nextIndex = 0;
      
        while(nextIndex < array.length){
          yield array[nextIndex++];
        }
      }
      
      var gen = makeSimpleGenerator(['yo', 'ya']);
      
      gen.next().value // 'yo'
      gen.next().value // 'ya'
      gen.next().done  // true
      ```

    - ##### 作为数据结构

      Generator 可以看作是数据结构，更确切地说，可以看作是一个数组结构，因为 Generator 函数可以返回一系列的值，这意味着它可以对任意表达式，提供类似数组的接口。

      ```js
      function* doStuff() {
        yield fs.readFile.bind(null, 'hello.txt');
        yield fs.readFile.bind(null, 'world.txt');
        yield fs.readFile.bind(null, 'and-such.txt');
      }
      ```

      上面代码就是依次返回三个函数，但是由于使用了 Generator 函数，导致可以像处理数组那样，处理这三个返回的函数。

      ```js
      for (task of doStuff()) {
        // task是一个函数，可以像回调函数那样使用它
      }
      ```

      实际上，如果用 ES5 表达，完全可以用数组模拟 Generator 的这种用法。

      ```js
      function doStuff() {
        return [
          fs.readFile.bind(null, 'hello.txt'),
          fs.readFile.bind(null, 'world.txt'),
          fs.readFile.bind(null, 'and-such.txt')
        ];
      }
      ```

      上面的函数，可以用一模一样的`for...of`循环处理！两相一比较，就不难看出 Generator 使得数据或者操作，具备了类似数组的接口。

- ## Generator 生成器函数的异步应用

  异步编程对 JS 语言太重要。JS 语言的执行环境是“单线程”的，如果没有异步编程，根本没法用，非卡死不可。本章主要介绍 Generator 函数如何完成异步操作。

  - #### 传统方法

     ES6 诞生以前，异步编程的方法，大概有下面四种。

     - 回调函数
     - 事件监听
     - 发布/订阅
     - Promise 对象

     Generator 生成器函数将 JS 异步编程带入了一个全新的阶段。

  - #### 基本概念

     - ##### 异步

       所谓"异步"，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

       比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。

       相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

     - ##### 回调函数

       JS 语言对异步编程的实现，就是回调函数。所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。回调函数的英语名字`callback`，直译过来就是"重新调用"。

       读取文件进行处理，是这样写的。

       ```js
       fs.readFile('/etc/passwd', 'utf-8', function (err, data) {
         if (err) throw err;
         console.log(data);
       });
       ```

       上面代码中，`readFile`函数的第三个参数，就是回调函数，也就是任务的第二段。等到操作系统返回了`/etc/passwd`这个文件以后，回调函数才会执行。

       一个有趣的问题是，为什么 Node 约定，回调函数的第一个参数，必须是错误对象`err`？（没有错误的话该参数是`null`）

       原因是：回调函数的执行分成两段，第一段执行完以后，任务所在的上下文环境就已经结束了。在这以后抛出的错误，原来的上下文环境已经无法捕捉，只能当作参数，传入第二段。

     - ##### Promise

       回调函数本身并没有问题，它的问题出现在多个回调函数嵌套。假定读取`A`文件之后，再读取`B`文件，代码如下。

       ```js
       fs.readFile(fileA, 'utf-8', function (err, data) {
         fs.readFile(fileB, 'utf-8', function (err, data) {
           // ...
         });
       });
       ```

       不难想象，如果依次读取两个以上的文件，就会出现多重嵌套。代码不是纵向发展，而是横向发展，很快就会乱成一团，无法管理。因为多个异步操作形成了强耦合，只要有一个操作需要修改，它的上层回调函数和下层回调函数，可能都要跟着修改。这种情况就称为"回调函数地狱"（callback hell）。

       Promise 对象就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套，改成链式调用。采用 Promise，连续读取多个文件，写法如下。

       ```js
       var readFile = require('fs-readfile-promise');
       
       readFile(fileA)
         .then(function (data) {
           console.log(data.toString());
         })
         .then(function () {
           return readFile(fileB);
         })
         .then(function (data) {
           console.log(data.toString());
         })
         .catch(function (err) {
           console.log(err);
         });
       ```

       上面代码中，我使用了`fs-readfile-promise`模块，它的作用就是返回一个 Promise 版本的`readFile`函数。Promise 提供`then`方法加载回调函数，`catch`方法捕捉执行过程中抛出的错误。

       可以看到，Promise 的写法只是回调函数的改进，使用`then`方法以后，异步任务的两段执行看得更清楚了，除此以外，并无新意。

       Promise 的最大问题是代码冗余，原来的任务被 Promise 包装了一下，不管什么操作，一眼看去都是一堆`then`，原来的语义变得很不清楚。

       那么，有没有更好的写法呢？

  - #### Generator 函数

     - ##### 协程

       传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做"协程"（coroutine），意思是多个线程互相协作，完成异步任务。

       协程有点像函数，又有点像线程。它的运行流程大致如下。

       - 第一步，协程`A`开始执行。
       - 第二步，协程`A`执行到一半，进入暂停，执行权转移到协程`B`。
       - 第三步，（一段时间后）协程`B`交还执行权。
       - 第四步，协程`A`恢复执行。

       上面流程的协程`A`，就是异步任务，因为它分成两段（或多段）执行。

       举例来说，读取文件的协程写法如下。

       ```js
       function* asyncJob() {
         // ...其他代码
         var f = yield readFile(fileA);
         // ...其他代码
       }
       ```

       上面代码的函数`asyncJob`是一个协程，它的奥妙就在其中的`yield`命令。它表示执行到此处，执行权将交给其他协程。也就是说，`yield`命令是异步两个阶段的分界线。

       协程遇到`yield`命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作，如果去除`yield`命令，简直一模一样。

     - ##### 协程的 Generator 函数实现

       Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

       整个 **Generator 函数就是一个封装的异步任务，或者说是异步任务的容器**。异步操作需要暂停的地方，都用`yield`语句注明。Generator 函数的执行方法如下。

       ```js
       function* gen(x) {
         var y = yield x + 2;
         return y;
       }
       
       var g = gen(1);
       g.next() // { value: 3, done: false }
       g.next() // { value: undefined, done: true }
       ```

       上面代码中，调用 Generator 函数，会返回一个内部指针（即迭代器）`g`。这是 Generator 函数不同于普通函数的另一个地方，即执行它不会返回结果，返回的是指针对象。调用指针`g`的`next`方法，会移动内部指针（即执行异步任务的第一段），指向第一个遇到的`yield`语句，上例是执行到`x + 2`为止。

       换言之，`next`方法的作用是分阶段执行`Generator`函数。每次调用`next`方法，会返回一个对象，表示当前阶段的信息（`value`属性和`done`属性）。`value`属性是`yield`语句后面表达式的值，表示当前阶段的值；`done`属性是一个布尔值，表示 Generator 函数是否执行完毕，即是否还有下一个阶段。

     - ##### Generator 函数的数据交换和错误处理

       Generator 函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。除此之外，它还有两个特性，使它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制。

       `next`返回值的 value 属性，是 Generator 函数向外输出数据；`next`方法还可以接受参数，向 Generator 函数体内输入数据。

       ```js
       function* gen(x){
         var y = yield x + 2;
         return y;
       }
       
       var g = gen(1);
       g.next() // { value: 3, done: false }
       g.next(2) // { value: 2, done: true }
       ```

       上面代码中，第一个`next`方法的`value`属性，返回表达式`x + 2`的值`3`。第二个`next`方法带有参数`2`，这个参数可以传入 Generator 函数，作为上个阶段异步任务的返回结果，被函数体内的变量`y`接收。因此，这一步的`value`属性，返回的就是`2`（变量`y`的值）。

       Generator 函数内部还可以部署错误处理代码，捕获函数体外抛出的错误。

       ```js
       function* gen(x){
         try {
           var y = yield x + 2;
         } catch (e){
           console.log(e);
         }
         return y;
       }
       
       var g = gen(1);
       g.next();
       g.throw('出错了');
       // 出错了
       ```

       上面代码的最后一行，Generator 函数体外，使用指针对象的`throw`方法抛出的错误，可以被函数体内的`try...catch`代码块捕获。这意味着，出错的代码与处理错误的代码，实现了时间和空间上的分离，这对于异步编程无疑是很重要的。

     - ##### 异步任务的封装

       下面看看如何使用 Generator 函数，执行一个真实的异步任务。

       ```js
       var fetch = require('node-fetch');
       
       function* gen(){
         var url = 'https://api.github.com/users/github';
         var result = yield fetch(url);
         console.log(result.bio);
       }
       ```

       上面代码中，Generator 函数封装了一个异步操作，该操作先读取一个远程接口，然后从 JSON 格式的数据解析信息。就像前面说过的，这段代码非常像同步操作，除了加上了`yield`命令。

       执行这段代码的方法如下。

       ```js
       var g = gen();
       var result = g.next();
       
       result.value.then(function(data){
         return data.json();
       }).then(function(data){
         g.next(data);
       });
       ```

       上面代码中，首先执行 Generator 函数，获取迭代器对象，然后使用`next`方法（第二行），执行异步任务的第一阶段。由于`Fetch`模块返回的是一个 Promise 对象，因此要用`then`方法调用下一个`next`方法。

       可以看到，**虽然 Generator 函数将异步操作表示得很简洁，但是流程管理却不方便**（即何时执行第一阶段、何时执行第二阶段）。

  - #### Thunk 函数

     Thunk 函数是自动执行 Generator 函数的一种方法。我们之前写的自动执行 Generator 函数的方法，里面不能有异步操作。而现在通过 Thunk 函数可以做到。

     - ##### 参数的求值策略

       Thunk 函数早在上个世纪 60 年代就诞生了。

       那时，编程语言刚刚起步，计算机学家还在研究，编译器怎么写比较好。一个争论的焦点是"求值策略"，即函数的参数到底应该何时求值。

       ```js
       var x = 1;
       
       function f(m) {
         return m * 2;
       }
       
       f(x + 5)
       ```

       上面代码先定义函数`f`，然后向它传入表达式`x + 5`。请问，这个表达式应该何时求值？

       一种意见是"传值调用"（call by value），即在进入函数体之前，就计算`x + 5`的值（等于 6），再将这个值传入函数`f`。C 语言就采用这种策略。

       ```js
       f(x + 5)
       // 传值调用时，等同于
       f(6)
       ```

       另一种意见是“传名调用”（call by name），即直接将表达式`x + 5`传入函数体，只在用到它的时候求值。Haskell 语言采用这种策略。

       ```js
       f(x + 5)
       // 传名调用时，等同于
       (x + 5) * 2
       ```

       传值调用和传名调用，哪一种比较好？

       回答是各有利弊。传值调用比较简单，但是对参数求值的时候，实际上还没用到这个参数，有可能造成性能损失。

       ```js
       function f(a, b){
         return b;
       }
       
       f(3 * x * x - 2 * x - 1, x);
       ```

       上面代码中，函数`f`的第一个参数是一个复杂的表达式，但是函数体内根本没用到。对这个参数求值，实际上是不必要的。因此，有一些计算机学家倾向于"传名调用"，即只在执行时求值。

     - ##### Thunk 函数的含义

       编译器的“传名调用”的实现，是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做 Thunk 函数。

       ```js
       function f(m) {
         return m * 2;
       }
       
       f(x + 5);
       
       // 等同于
       
       var thunk = function () {
         return x + 5;
       };
       
       function f(thunk) {
         return thunk() * 2;
       }
       ```

       上面代码中，函数 f 的参数`x + 5`被一个函数替换了。凡是用到原参数的地方，对`Thunk`函数求值即可。

       这就是 Thunk 函数的定义，它是“传名调用”的一种实现策略，用来替换某个表达式。

     - ##### JS 语言的 Thunk 函数

       JS 语言也是传值调用，它的 Thunk 函数含义有所不同。在 JS 语言中，Thunk 函数替换的不是表达式，而是多参数函数，将其替换成一个只接受回调函数作为参数的单参数函数。

       ```js
       // 正常版本的readFile（多参数版本）
       fs.readFile(fileName, callback);
       
       // Thunk版本的readFile（单参数版本）
       var Thunk = function (fileName) {
         return function (callback) {
           return fs.readFile(fileName, callback);
         };
       };
       
       var readFileThunk = Thunk(fileName);
       readFileThunk(callback);
       ```

       上面代码中，`fs`模块的`readFile`方法是一个多参数函数，两个参数分别为文件名和回调函数。经过转换器处理，它变成了一个单参数函数，只接受回调函数作为参数。这个单参数版本，就叫做 Thunk 函数。

       **任何函数，只要参数有回调函数，就能写成 Thunk 函数的形式**。下面是一个简单的 Thunk 函数转换器。

       ```js
       // ES5版本
       var Thunk = function(fn){
         return function (){
           var args = Array.prototype.slice.call(arguments);
           return function (callback){
             args.push(callback);
             return fn.apply(this, args);
           }
         };
       };
       
       // ES6版本
       const Thunk = function(fn) {
         return function (...args) {
           return function (callback) {
             return fn.call(this, ...args, callback);
           }
         };
       };
       ```

       使用上面的转换器，生成`fs.readFile`的 Thunk 函数。

       ```js
       var readFileThunk = Thunk(fs.readFile);
       readFileThunk(fileA)(callback);
       ```

       下面是另一个完整的例子。

       ```js
       function f(a, cb) {
         cb(a);
       }
       const ft = Thunk(f);
       
       ft(1)(console.log) // 1
       ```

     - ##### Thunkify 模块

       生产环境的转换器，建议使用 Thunkify 模块。

       首先是安装。

       ```bash
       npm install thunkify
       ```

       使用方式如下。

       ```js
       var thunkify = require('thunkify');
       var fs = require('fs');
       
       var read = thunkify(fs.readFile);
       read('package.json')(function(err, str){
         // ...
       });
       ```

       Thunkify 的源码与上一节那个简单的转换器非常像。

       ```js
       function thunkify(fn) {
         return function() {
           var args = new Array(arguments.length);
           var ctx = this;
       
           for (var i = 0; i < args.length; ++i) {
             args[i] = arguments[i];
           }
       
           return function (done) {
             var called;
       
             args.push(function () {
               if (called) return;
               called = true;
               done.apply(null, arguments);
             });
       
             try {
               fn.apply(ctx, args);
             } catch (err) {
               done(err);
             }
           }
         }
       };
       ```

       它的源码主要多了一个检查机制，变量`called`确保回调函数只运行一次。这样的设计与下文的 Generator 函数相关。请看下面的例子。

       ```js
       function f(a, b, callback){
         var sum = a + b;
         callback(sum);
         callback(sum);
       }
       
       var ft = thunkify(f);
       var print = console.log.bind(console);
       ft(1, 2)(print);
       // 3
       ```

       上面代码中，由于`thunkify`只允许回调函数执行一次，所以只输出一行结果。

     - ##### Generator 函数的流程管理

       你可能会问， Thunk 函数有什么用？回答是以前确实没什么用，但是 ES6 有了 Generator 函数，**Thunk 函数现在可以用于 Generator 函数的自动流程管理**。

       Generator 函数可以自动执行。

       ```js
       function* gen() {
         // ...
       }
       
       var g = gen();
       var res = g.next();
       
       while(!res.done){
         console.log(res.value);
         res = g.next();
       }
       ```

       上面代码中，Generator 函数`gen`会自动执行完所有步骤。

       但是，这不适合异步操作。如果必须保证前一步执行完，才能执行后一步，上面的自动执行就不可行。这时，Thunk 函数就能派上用处。以读取文件为例。下面的 Generator 函数封装了两个异步操作。

       ```js
       var fs = require('fs');
       var thunkify = require('thunkify');
       var readFileThunk = thunkify(fs.readFile);
       
       var gen = function* (){
         var r1 = yield readFileThunk('/etc/fstab');
         console.log(r1.toString());
         var r2 = yield readFileThunk('/etc/shells');
         console.log(r2.toString());
       };
       ```

       上面代码中，`yield`命令用于将程序的执行权移出 Generator 函数，那么就需要一种方法，将执行权再交还给 Generator 函数。

       这种方法就是 Thunk 函数，因为它可以在回调函数里，将执行权交还给 Generator 函数。为了便于理解，我们先看如何手动执行上面这个 Generator 函数。

       ```js
       var g = gen();
       
       var r1 = g.next();
       r1.value(function (err, data) {
         if (err) throw err;
         var r2 = g.next(data);
         r2.value(function (err, data) {
           if (err) throw err;
           g.next(data);
         });
       });
       ```

       上面代码中，变量`g`是 Generator 函数的内部指针，表示目前执行到哪一步。`next`方法负责将指针移动到下一步，并返回该步的信息（`value`属性和`done`属性）。

       仔细查看上面的代码，可以发现 Generator 函数的执行过程，其实是将同一个回调函数，反复传入`next`方法的`value`属性。这使得我们可以用递归来自动完成这个过程。

     - ##### Thunk 函数的自动流程管理

       Thunk 函数真正的威力，在于可以自动执行 Generator 函数。下面就是一个基于 Thunk 函数的 Generator 执行器。

       ```js
       function run(fn) {
         var gen = fn();
       
         function next(err, data) {
           var result = gen.next(data);
           if (result.done) return;
           result.value(next);
         }
       
         next();
       }
       
       function* g() {
         // ...
       }
       
       run(g);
       ```

       上面代码的`run`函数，就是一个 Generator 函数的自动执行器。内部的`next`函数就是 Thunk 的回调函数。`next`函数先将指针移到 Generator 函数的下一步（`gen.next`方法），然后判断 Generator 函数是否结束（`result.done`属性），如果没结束，就将`next`函数再传入 Thunk 函数（`result.value`属性），否则就直接退出。

       有了这个执行器，执行 Generator 函数方便多了。不管内部有多少个异步操作，直接把 Generator 函数传入`run`函数即可。当然，前提是**每一个异步操作，都要是 Thunk 函数**，也就是说，跟在`yield`命令后面的必须是 Thunk 函数。

       
       
       ```js
       function* g() {
         var f1 = yield readFileThunk('fileA');
         var f2 = yield readFileThunk('fileB');
         // ...
         var fn = yield readFileThunk('fileN');
       };
       
     run(g);
       ```

       上面代码中，函数`g`封装了`n`个异步的读取文件操作，只要执行`run`函数，这些操作就会自动完成。这样一来，异步操作不仅可以写得像同步操作，而且一行代码就可以执行。
       
       Thunk 函数并不是 Generator 函数自动执行的唯一方案。因为自动执行的关键是，必须有一种机制，自动控制 Generator 函数的流程，接收和交还程序的执行权。回调函数可以做到这一点，Promise 对象也可以做到这一点。

  - #### co 模块

     - ##### 基本用法

       [co 模块](https://github.com/tj/co)是著名程序员 TJ Holowaychuk 于 2013 年 6 月发布的一个小工具，用于 Generator 函数的自动执行。

       下面是一个 Generator 函数，用于依次读取两个文件。

       ```js
       var gen = function* () {
         var f1 = yield readFile('/etc/fstab');
         var f2 = yield readFile('/etc/shells');
         console.log(f1.toString());
         console.log(f2.toString());
       };
       ```

       co 模块可以让你不用编写 Generator 函数的执行器。

       ```js
       var co = require('co');
       co(gen);
       ```

       上面代码中，Generator 函数只要传入`co`函数，就会自动执行。

       `co`函数返回一个`Promise`对象，因此可以用`then`方法添加回调函数。

       ```js
       co(gen).then(function (){
         console.log('Generator 函数执行完成');
       });
       ```

       上面代码中，等到 Generator 函数执行结束，就会输出一行提示。

     - ##### co 模块的原理

       为什么 co 可以自动执行 Generator 函数？

       前面说过，Generator 就是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。

       两种方法可以做到这一点。

       （1）回调函数。将异步操作包装成 Thunk 函数，在回调函数里面交回执行权。

       （2）Promise 对象。将异步操作包装成 Promise 对象，用`then`方法交回执行权。

       co 模块其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个模块。**使用 co 的前提条件是，Generator 函数的`yield`命令后面，只能是 Thunk 函数或 Promise 对象**。如果数组或对象的成员，全部都是 Promise 对象，也可以使用 co，详见后文的例子。

       上一节已经介绍了基于 Thunk 函数的自动执行器。下面来看，基于 Promise 对象的自动执行器。这是理解 co 模块必须的。

     - ##### 基于 Promise 对象的自动执行

       还是沿用上面的例子。首先，把`fs`模块的`readFile`方法包装成一个 Promise 对象。

       ```js
       var fs = require('fs');
       
       var readFile = function (fileName){
         return new Promise(function (resolve, reject){
           fs.readFile(fileName, function(error, data){
             if (error) return reject(error);
             resolve(data);
           });
         });
       };
       
       var gen = function* (){
         var f1 = yield readFile('/etc/fstab');
         var f2 = yield readFile('/etc/shells');
         console.log(f1.toString());
         console.log(f2.toString());
       };
       ```

       然后，手动执行上面的 Generator 函数。

       ```js
       var g = gen();
       
       g.next().value.then(function(data){
         g.next(data).value.then(function(data){
           g.next(data);
         });
       });
       ```

       手动执行其实就是用`then`方法，层层添加回调函数。理解了这一点，就可以写出一个自动执行器。

       ```js
       function run(gen){
         var g = gen();
       
         function next(data){
           var result = g.next(data);
           if (result.done) return result.value;
           result.value.then(function(data){
             next(data);
           });
         }
       
         next();
       }
       
       run(gen);
       ```

       上面代码中，只要 Generator 函数还没执行到最后一步，`next`函数就调用自身，以此实现自动执行。

     - ##### co 模块的源码

       co 就是上面那个自动执行器的扩展，它的源码只有几十行，非常简单。

       首先，co 函数接受 Generator 函数作为参数，返回一个 Promise 对象。

       ```js
       function co(gen) {
         var ctx = this;
       
         return new Promise(function(resolve, reject) {
         });
       }
       ```

       在返回的 Promise 对象里面，co 先检查参数`gen`是否为 Generator 函数。如果是，就执行该函数，得到一个内部指针对象；如果不是就返回，并将 Promise 对象的状态改为`resolved`。

       ```js
       function co(gen) {
         var ctx = this;
       
         return new Promise(function(resolve, reject) {
           if (typeof gen === 'function') gen = gen.call(ctx);
           if (!gen || typeof gen.next !== 'function') return resolve(gen);
         });
       }
       ```

       接着，co 将 Generator 函数的内部指针对象的`next`方法，包装成`onFulfilled`函数。这主要是为了能够捕捉抛出的错误。

       ```js
       function co(gen) {
         var ctx = this;
       
         return new Promise(function(resolve, reject) {
           if (typeof gen === 'function') gen = gen.call(ctx);
           if (!gen || typeof gen.next !== 'function') return resolve(gen);
       
           onFulfilled();
           function onFulfilled(res) {
             var ret;
             try {
               ret = gen.next(res);
             } catch (e) {
               return reject(e);
             }
             next(ret);
           }
         });
       }
       ```

       最后，就是关键的`next`函数，它会反复调用自身。

       ```js
       function next(ret) {
         if (ret.done) return resolve(ret.value);
         var value = toPromise.call(ctx, ret.value);
         if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
         return onRejected(
           new TypeError(
             'You may only yield a function, promise, generator, array, or object, '
             + 'but the following object was passed: "'
             + String(ret.value)
             + '"'
           )
         );
       }
       ```

       上面代码中，`next`函数的内部代码，一共只有四行命令。

       第一行，检查当前是否为 Generator 函数的最后一步，如果是就返回。

       第二行，确保每一步的返回值，是 Promise 对象。

       第三行，使用`then`方法，为返回值加上回调函数，然后通过`onFulfilled`函数再次调用`next`函数。

       第四行，在参数不符合要求的情况下（参数非 Thunk 函数和 Promise 对象），将 Promise 对象的状态改为`rejected`，从而终止执行。

     - ##### 处理并发的异步操作

       co 支持并发的异步操作，即允许某些操作同时进行，等到它们全部完成，才进行下一步。

       这时，要把并发的操作都放在数组或对象里面，跟在`yield`语句后面。

       ```js
       // 数组的写法
       co(function* () {
         var res = yield [
           Promise.resolve(1),
           Promise.resolve(2)
         ];
         console.log(res);
       }).catch(onerror);
       
       // 对象的写法
       co(function* () {
         var res = yield {
           1: Promise.resolve(1),
           2: Promise.resolve(2),
         };
         console.log(res);
       }).catch(onerror);
       ```

       下面是另一个例子。

       ```js
       co(function* () {
         var values = [n1, n2, n3];
         yield values.map(somethingAsync);
       });
       
       function* somethingAsync(x) {
         // do something async
         return y
       }
       ```

       上面的代码允许并发三个`somethingAsync`异步操作，等到它们全部完成，才会进行下一步。

     - ##### 实例：处理 Stream

       Node 提供了基于 Stream 模式的读写数据API，特点是一次只处理数据的一部分，数据分成一块块依次处理，就好像“数据流”一样。这对于处理大规模数据非常有利。Stream 模式使用 EventEmitter API，会释放三个事件。

       - `data`事件：下一块数据块已经准备好了。
       - `end`事件：整个“数据流”处理完了。
       - `error`事件：发生错误。

       使用`Promise.race()`函数，可以判断这三个事件之中哪一个最先发生，只有当`data`事件最先发生时，才进入下一个数据块的处理。从而，我们可以通过一个`while`循环，完成所有数据的读取。

       ```js
       const co = require('co');
       const fs = require('fs');
       
       const stream = fs.createReadStream('./les_miserables.txt');
       let valjeanCount = 0;
       
       co(function*() {
         while(true) {
           const res = yield Promise.race([
             new Promise(resolve => stream.once('data', resolve)),
             new Promise(resolve => stream.once('end', resolve)),
             new Promise((resolve, reject) => stream.once('error', reject))
           ]);
           if (!res) {
             break;
           }
           stream.removeAllListeners('data');
           stream.removeAllListeners('end');
           stream.removeAllListeners('error');
           valjeanCount += (res.toString().match(/valjean/ig) || []).length;
         }
         console.log('count:', valjeanCount); // count: 1120
       });
       ```

       上面代码采用 Stream 模式读取《悲惨世界》的文本文件，对于每个数据块都使用`stream.once`方法，在`data`、`end`、`error`三个事件上添加一次性回调函数。变量`res`只有在`data`事件发生时才有值，然后累加每个数据块之中`valjean`这个词出现的次数。

- ## async 函数

  - #### 含义

    ES2017 标准引入了 async 函数，使得异步操作变得更加方便。

    一句话，**async 函数就是 Generator 函数的语法糖**。

    前文有一个 Generator 函数，依次读取两个文件。

    ```js
    const fs = require('fs');
    
    const readFile = function (fileName) {
      return new Promise(function (resolve, reject) {
        fs.readFile(fileName, function(error, data) {
          if (error) return reject(error);
          resolve(data);
        });
      });
    };
    
    const gen = function* () {
      const f1 = yield readFile('/etc/fstab');
      const f2 = yield readFile('/etc/shells');
      console.log(f1.toString());
      console.log(f2.toString());
    };
    ```

    上面代码的函数`gen`可以写成`async`函数，就是下面这样。

    ```js
    const asyncReadFile = async function () {
      const f1 = await readFile('/etc/fstab');
      const f2 = await readFile('/etc/shells');
      console.log(f1.toString());
      console.log(f2.toString());
    };
    ```

    一比较就会发现，**`async`函数就是将 Generator 函数的星号（`*`）替换成`async`，将`yield`替换成`await`**，仅此而已。

    `async`函数对 Generator 函数的改进，体现在以下四点：

    1. 内置执行器。

       Generator 函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。

       ```js
       asyncReadFile();
       ```

       上面的代码调用了`asyncReadFile`函数，然后它就会自动执行，输出最后结果。这完全不像 Generator 函数，需要调用`next`方法，或者用`co`模块，才能真正执行，得到最后结果。

    2. 更好的语义。

       `async`和`await`，比起星号和`yield`，语义更清楚了。`async`表示函数里有异步操作，`await`表示紧跟在后面的表达式需要等待结果。

    3. 更广的适用性。

       **`co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象**，而**`async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值。如果不是 Promise 对象（即原始类型值）的话，会自动通过 `Promise.resolved` 转成 Promise 对象**。

    4. 返回值是 Promise。

       **`async`函数的返回值是 Promise 对象（如果不是则会自动通过 `Promise.resolved` 将返回值转成 Promise 对象）**。这比 Generator 函数的返回值是 Iterator 对象方便多了。直接就可以用`then`方法指定下一步的操作。

       进一步说，`async`函数完全可以看作多个异步操作，包装成的一个 Promise 对象，而`await`命令就是内部`then`命令的语法糖。

  - #### 基本用法

    `async`函数返回一个 Promise 对象，可以使用`then`方法添加回调函数。当函数执行的时候，一旦遇到`await`就会先暂停执行，转去执行其他的同步代码。等到异步操作完成，再接着执行函数体内后面的语句。

    下面是一个例子。

    ```js
    async function getStockPriceByName(name) {
      const symbol = await getStockSymbol(name);
      const stockPrice = await getStockPrice(symbol);
      return stockPrice;
    }
    
    getStockPriceByName('goog').then(function (result) {
      console.log(result);
    });
    ```

    上面代码是一个获取股票报价的函数，函数前面的`async`关键字，表明该函数内部有异步操作。调用该函数时，会立即返回一个`Promise`对象。

    下面是另一个例子，指定多少毫秒后输出一个值。

    ```js
    function timeout(ms) {
      return new Promise((resolve) => {
        setTimeout(resolve, ms);
      });
    }
    
    async function asyncPrint(value, ms) {
      await timeout(ms);
      console.log(value);
    }
    
    asyncPrint('hello world', 50);
    ```

    上面代码指定 50 毫秒以后，输出`hello world`。

    由于`async`函数返回的是 Promise 对象，可以作为`await`命令的参数。所以，上面的例子也可以写成下面的形式。

    ```js
    async function timeout(ms) {
      await new Promise((resolve) => {
        setTimeout(resolve, ms);
      });
    }
    
    async function asyncPrint(value, ms) {
      await timeout(ms);
      console.log(value);
    }
    
    asyncPrint('hello world', 50);
    ```

    async 函数有多种使用形式。

    ```js
    // 函数声明
    async function foo() {}
    
    // 函数表达式
    const foo = async function () {};
    
    // 对象的方法
    let obj = { async foo() {} };
    obj.foo().then(...)
    
    // Class 的方法
    class Storage {
      constructor() {
        this.cachePromise = caches.open('avatars');
      }
    
      async getAvatar(name) {
        const cache = await this.cachePromise;
        return cache.match(`/avatars/${name}.jpg`);
      }
    }
    
    const storage = new Storage();
    storage.getAvatar('jake').then(…);
    
    // 箭头函数
    const foo = async () => {};
    ```

  - #### 语法

    `async`函数的语法规则总体上比较简单，难点是错误处理机制。

    - ##### 返回 Promise 对象

      `async`函数返回一个 Promise 对象。

      `async`函数内部`return`语句返回的值，会成为`then`方法回调函数的参数。

      ```js
      async function f() {
        return 'hello world';
      }
      
      f().then(v => console.log(v))
      // "hello world"
      ```

      上面代码中，函数`f`内部`return`命令返回的值，会被`then`方法回调函数接收到。这是因为`return`返回的值会被 `Promise.resolve()` 解析为 Promise 对象并返回。

      **`async`函数内部抛出错误，会导致返回的 Promise 对象变为`reject`状态**。抛出的错误对象会被`catch()`方法指定的回调函数接收到。

      ```js
      async function f() {
        throw new Error('出错了');
      }
      
      f().then(
        v => console.log('resolve', v),
        e => console.log('reject', e)
      )
      //reject Error: 出错了
      ```

    - ##### Promise 对象的状态变化

      `async`函数返回的 Promise 对象，必须等到内部所有`await`命令后面的 Promise 对象执行完，才会发生状态改变，除非遇到`return`语句或者抛出错误。也就是说，只有`async`函数内部的异步操作执行完，才会执行`then`方法指定的回调函数。

      下面是一个例子。

      ```js
      async function getTitle(url) {
        let response = await fetch(url);
        let html = await response.text();
        return html.match(/<title>([\s\S]+)<\/title>/i)[1];
      }
      getTitle('https://tc39.github.io/ecma262/').then(console.log)
      // "ECMAScript 2017 Language Specification"
      ```

      上面代码中，函数`getTitle`内部有三个操作：抓取网页、取出文本、匹配页面标题。只有这三个操作全部完成，才会执行`then`方法里面的`console.log`。

    - ##### await 命令

      通常情况下，`await`关键字后面是一个 Promise 对象，**`await`关键字会等待并尝试取该 Promise 对象成功状态的结果值，如果是失败的 Promise 则会`throw`抛出其中的结果值**。

      如果`await`命令后面不是 Promise 对象，此时会自动通过 `Promise.resolved()` 转成 Promise 对象。
  
      ```js
      async function f() {
        // 等同于：return 123;
        return await 123;
      }
      
      f().then(v => console.log(v))  // 123
      ```
  
      上面代码中，`await`命令的参数是数值`123`，这时等同于`return 123`。因为`f`是一个`async`函数，它的返回值会被`Promise.resolve()`包装后返回。
  
      另一种情况是，`await`命令后面是一个`thenable`对象（即定义了`then`方法的对象），那么`await`会将其等同于 Promise 对象。
  
      ```js
      class Sleep {
        constructor(timeout) {
          this.timeout = timeout;
        }
        then(resolve, reject) {
          const startTime = Date.now();
          setTimeout(
            () => resolve(Date.now() - startTime),
            this.timeout
          );
        }
      }
      
      (async () => {
        const sleepTime = await new Sleep(1000);
        console.log(sleepTime);
      })();
      // 1000
      ```
  
      上面代码中，`await`命令后面是一个`Sleep`对象的实例。这个实例不是 Promise 对象，但是因为定义了`then`方法，`await`会将其视为`Promise`处理。
  
      这个例子还演示了如何实现休眠效果。JS 一直没有休眠的语法，但是借助`await`命令就可以让程序停顿指定的时间。下面给出了一个简化的`sleep`实现。
  
      ```js
      function sleep(interval) {
        return new Promise(resolve => {
          setTimeout(resolve, interval);
        })
      }
      
      // 用法
      async function one2FiveInAsync() {
        for(let i = 1; i <= 5; i++) {
          console.log(i);
          await sleep(1000);
        }
      }
      
      one2FiveInAsync();
      ```
  
      上面代码中，`await`命令后面的 Promise 对象如果变为`reject`状态，则`reject`的参数会被`catch`方法的回调函数接收到。
  
      ```js
      async function f() {
        await Promise.reject('出错了');
      }
      
      f()
        .then(v => console.log(v))
        .catch(e => console.log(e))
      // 出错了
      ```
  
      注意，上面代码中，`await`后面是一个失败的 Promise，值为传入的字符串。由于`await`会尝试取成功状态 Promise 的结果值，取不到因此这里会`throw '出错了'`，导致`f()`函数返回一个失败的Promise，值为`'出错了'`。（这里`await`前面加不加`return`效果是一样的）
  
      **任何一个`await`语句后面的 Promise 对象变为`reject`状态，那么整个`async`函数都会中断执行**。
  
      ```js
      async function f() {
        await Promise.reject('出错了');
        await Promise.resolve('hello world'); // 不会执行
      }
      ```
  
      上面代码中，第二个`await`语句是不会执行的，因为第一个`await`语句状态变成了`reject`。
  
      有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时**可以将第一个`await`放在`try...catch`结构里面**，这样不管这个异步操作是否成功，第二个`await`都会执行。
  
      ```js
      async function f() {
        try {
          await Promise.reject('出错了');
        } catch(e) {
        }
        return await Promise.resolve('hello world');
      }
      
      f()
      .then(v => console.log(v))
      // hello world
      ```
  
      **另一种方法是`await`后面的 Promise 对象再跟一个`catch`方法，处理前面可能出现的错误。**
  
      ```js
      async function f() {
        await Promise.reject('出错了')
          .catch(e => console.log(e));
        return await Promise.resolve('hello world');
      }
      
      f()
      .then(v => console.log(v))
      // 出错了
      // hello world
      ```
  
    - ##### 错误处理
  
      如果`await`后面的异步操作出错，那么等同于`async`函数返回的 Promise 对象被`reject`。
  
      ```js
    async function f() {
        await new Promise(function (resolve, reject) {
          throw new Error('出错了');
        });
      }
      
      f()
        .then(v => console.log(v))
        .catch(e => console.log(e))
      // Error：出错了
      ```
  
      上面代码中，`async`函数`f`执行后，`await`后面的 Promise 对象会抛出一个错误对象，导致`catch`方法的回调函数被调用，它的参数就是抛出的错误对象。具体的执行机制，可以参考后文的“async 函数的实现原理”。
  
      防止出错的方法，也是将其放在`try...catch`代码块之中。
  
      ```js
    async function f() {
        try {
          await new Promise(function (resolve, reject) {
            throw new Error('出错了');
          });
        } catch(e) {
        }
        return await('hello world');
      }
      ```
  
      如果有多个`await`命令，可以统一放在`try...catch`结构中。
  
      ```js
    async function main() {
        try {
          const val1 = await firstStep();
          const val2 = await secondStep(val1);
          const val3 = await thirdStep(val1, val2);
      
          console.log('Final: ', val3);
        }
        catch (err) {
          console.error(err);
        }
      }
      ```
  
      下面的例子使用`try...catch`结构，实现多次重复尝试。
  
      ```js
    const superagent = require('superagent');
      const NUM_RETRIES = 3;
      
      async function test() {
        let i;
        for (i = 0; i < NUM_RETRIES; ++i) {
          try {
            await superagent.get('http://google.com/this-throws-an-error');
            break;
          } catch(err) {}
        }
        console.log(i); // 3
      }
      
      test();
      ```
  
      上面代码中，如果`await`操作成功，就会使用`break`语句退出循环；如果失败，会被`catch`语句捕捉，然后进入下一轮循环。
  
    - ##### 使用注意点：
  
      1. 前面已经说过，**`await`命令后面的`Promise`对象，运行结果可能是`rejected`，所以最好把`await`命令放在`try...catch`代码块中**。
  
        ```js
        async function myFunction() {
          try {
            await somethingThatReturnsAPromise();
          } catch (err) {
            console.log(err);
          }
        }
        
        // 另一种写法
        
        async function myFunction() {
          await somethingThatReturnsAPromise()
          .catch(function (err) {
            console.log(err);
          });
        }
        ```
  
      2. 多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。
  
        ```js
        let foo = await getFoo();
        let bar = await getBar();
        ```
  
        上面代码中，`getFoo`和`getBar`是两个独立的异步操作（即互不依赖），被写成继发关系。这样比较耗时，因为只有`getFoo`完成以后，才会执行`getBar`，完全可以让它们同时触发。
  
        ```js
        // 写法一
        let [foo, bar] = await Promise.all([getFoo(), getBar()]);
        
        // 写法二
        let fooPromise = getFoo();
        let barPromise = getBar();
        let foo = await fooPromise;
        let bar = await barPromise;
        ```
  
        上面两种写法，`getFoo`和`getBar`都是同时触发，这样就会缩短程序的执行时间。
  
      3. **`await`命令只能用在`async`函数中**。如果用在普通函数，就会报错。
  
        ```js
        async function dbFuc(db) {
          let docs = [{}, {}, {}];
        
          // 报错
          docs.forEach(function (doc) {
            await db.post(doc);
          });
        }
        ```
  
        上面代码会报错，因为`await`用在普通函数之中了。但是，如果将`forEach`方法的参数改成`async`函数，也有问题。
  
        ```js
        function dbFuc(db) { //这里不需要 async
          let docs = [{}, {}, {}];
        
          // 可能得到错误结果
          docs.forEach(async function (doc) {
            await db.post(doc);
          });
        }
        ```
  
        上面代码可能不会正常工作，原因是这时三个`db.post()`操作将是并发执行，也就是同时执行，而不是继发执行。正确的写法是采用`for of`循环。
  
        ```js
        async function dbFuc(db) {
          let docs = [{}, {}, {}];
        
          for (let doc of docs) {
            await db.post(doc);
          }
        }
        ```
  
        另一种方法是使用数组的`reduce()`方法。
  
        ```js
        async function dbFuc(db) {
          let docs = [{}, {}, {}];
        
          await docs.reduce(async (_, doc) => {
            await _;
            await db.post(doc);
          }, undefined);
        }
        ```
  
        上面例子中，`reduce()`方法的第一个参数是`async`函数，导致该函数的第一个参数是前一步操作返回的 Promise 对象，所以必须使用`await`等待它操作结束。另外，`reduce()`方法返回的是`docs`数组最后一个成员的`async`函数的执行结果，也是一个 Promise 对象，导致在它前面也必须加上`await`。
  
        上面的`reduce()`的参数函数里面没有`return`语句，原因是这个函数的主要目的是`db.post()`操作，不是返回值。而且`async`函数不管有没有`return`语句，总是返回一个 Promise 对象，所以这里的`return`是不必要的。
  
        **如果确实希望多个请求并发执行，可以使用`Promise.all`方法**。当三个请求都会`resolved`时，下面两种写法效果相同。
  
        ```js
        async function dbFuc(db) {
          let docs = [{}, {}, {}];
          let promises = docs.map((doc) => db.post(doc));
        
          let results = await Promise.all(promises);
          console.log(results);
        }
        
        // 或者使用下面的写法
        
        async function dbFuc(db) {
          let docs = [{}, {}, {}];
          let promises = docs.map((doc) => db.post(doc));
        
          let results = [];
          for (let promise of promises) {
            results.push(await promise);
          }
          console.log(results);
        }
        ```
  
      4. **async 函数可以保留运行堆栈**。这点和 Generator 生成器函数是一样的。
  
         ```js
         const a = () => {
           b().then(() => c());
         };
         ```
  
         上面代码中，函数`a`内部运行了一个异步任务`b()`。当`b()`运行的时候，函数`a()`不会中断，而是继续执行。等到`b()`运行结束，可能`a()`早就运行结束了，`b()`所在的上下文环境已经消失了。如果`b()`或`c()`报错，错误堆栈将不包括`a()`。
  
         现在将这个例子改成`async`函数。
  
         ```js
         const a = async () => {
           await b();
           c();
         };
         ```
  
         上面代码中，`b()`运行的时候，`a()`是暂停执行，上下文环境都保存着。一旦`b()`或`c()`报错，错误堆栈将包括`a()`。
  
  - #### async 函数的实现原理
  
    async 函数的实现原理，就是将 Generator 函数和自动执行器，包装在一个函数里。
  
    ```js
    async function fn(args) {
      // ...
    }
    
    // 等同于
    function fn(args) {
      return spawn(function* () {
        // ...
      });
    }
    ```
    
  所有的`async`函数都可以写成上面的第二种形式，其中的`spawn`函数就是自动执行器。
    
  下面给出`spawn`函数的实现，基本就是前文自动执行器的翻版。
    
  ```js
    function spawn(genF) {
      return new Promise(function(resolve, reject) {
        const gen = genF();
        function step(nextF) {
          let next;
          try {
            next = nextF();
          } catch(e) {
            return reject(e);
          }
          if(next.done) {
            return resolve(next.value);
          }
          Promise.resolve(next.value).then(function(v) {
            step(function() { return gen.next(v); });
          }, function(e) {
            step(function() { return gen.throw(e); });
          });
        }
        step(function() { return gen.next(undefined); });
      });
    }
    ```
    
  - #### 与其他异步处理方法的比较
  
    我们通过一个例子，来看 async 函数与 Promise、Generator 函数的比较。
  
    假定某个 DOM 元素上面，部署了一系列的动画，前一个动画结束，才能开始后一个。如果当中有一个动画出错，就不再往下执行，返回上一个成功执行的动画的返回值。
  
    首先是 Promise 的写法。
  
    ```js
    function chainAnimationsPromise(elem, animations) {
    
      // 变量ret用来保存上一个动画的返回值
      let ret = null;
    
      // 新建一个空的Promise
      let p = Promise.resolve();
    
      // 使用then方法，添加所有动画
      for(let anim of animations) {
        p = p.then(function(val) {
          ret = val;
          return anim(elem);
        });
      }
    
      // 返回一个部署了错误捕捉机制的Promise
      return p.catch(function(e) {
        /* 忽略错误，继续执行 */
      }).then(function() {
        return ret;
      });
    
    }
    ```
  
    虽然 Promise 的写法比回调函数的写法大大改进，但是一眼看上去，代码完全都是 Promise 的 API（`then`、`catch`等等），操作本身的语义反而不容易看出来。
  
    接着是 Generator 函数的写法。
  
    ```js
    function chainAnimationsGenerator(elem, animations) {
    
      return spawn(function*() {
        let ret = null;
        try {
          for(let anim of animations) {
            ret = yield anim(elem);
          }
        } catch(e) {
          /* 忽略错误，继续执行 */
        }
        return ret;
      });
    
    }
    ```
  
    上面代码使用 Generator 函数遍历了每个动画，语义比 Promise 写法更清晰，用户定义的操作全部都出现在`spawn`函数的内部。这个写法的问题在于，必须有一个任务运行器，自动执行 Generator 函数，上面代码的`spawn`函数就是自动执行器，它返回一个 Promise 对象，而且必须保证`yield`语句后面的表达式，必须返回一个 Promise。
  
    最后是 async 函数的写法。
  
    ```js
    async function chainAnimationsAsync(elem, animations) {
      let ret = null;
      try {
        for(let anim of animations) {
          ret = await anim(elem);
        }
      } catch(e) {
        /* 忽略错误，继续执行 */
      }
      return ret;
    }
    ```
  
    可以看到 Async 函数的实现最简洁，最符合语义，几乎没有语义不相关的代码。它将 Generator 写法中的自动执行器，改在语言层面提供，不暴露给用户，因此代码量最少。如果使用 Generator 写法，自动执行器需要用户自己提供。
  
  - #### 实例：按顺序完成异步操作
  
    实际开发中，经常遇到一组异步操作，需要按照顺序完成。比如，依次远程读取一组 URL，然后按照读取的顺序输出结果。
  
    Promise 的写法如下。
  
    ```js
    function logInOrder(urls) {
      // 远程读取所有URL
      const textPromises = urls.map(url => {
        return fetch(url).then(response => response.text());
      });
    
      // 按次序输出
      textPromises.reduce((chain, textPromise) => {
        return chain.then(() => textPromise)
          .then(text => console.log(text));
      }, Promise.resolve());
    }
    ```
  
    上面代码使用`fetch`方法，同时远程读取一组 URL。每个`fetch`操作都返回一个 Promise 对象，放入`textPromises`数组。然后，`reduce`方法依次处理每个 Promise 对象，然后使用`then`，将所有 Promise 对象连起来，因此就可以依次输出结果。
  
    这种写法不太直观，可读性比较差。下面是 async 函数实现。
  
    ```js
    async function logInOrder(urls) {
      for (const url of urls) {
        const response = await fetch(url);
        console.log(await response.text());
      }
    }
    ```
  
    上面代码确实大大简化，问题是所有远程操作都是继发。只有前一个 URL 返回结果，才会去读取下一个 URL，这样做效率很差，非常浪费时间。我们需要的是并发发出远程请求。
  
    ```js
    async function logInOrder(urls) {
      // 并发读取远程URL
      const textPromises = urls.map(async url => {
        const response = await fetch(url);
        return response.text();
      });
    
      // 按次序输出
      for (const textPromise of textPromises) {
        console.log(await textPromise);
      }
    }
    ```
  
    上面代码中，虽然`map`方法的参数是`async`函数，但它是并发执行的，因为只有`async`函数内部是继发执行，外部不受影响。后面的`for..of`循环内部使用了`await`，因此实现了按顺序输出。
  
  - #### 顶层 await
  
    早期的语法规定是，`await`命令只能出现在 `async` 函数内部，否则都会报错。
  
    ```js
    // 报错
    const data = await fetch('https://api.example.com');
    ```
  
    上面代码中，`await`命令独立使用，没有放在 async 函数里面，就会报错。
  
    从 [ES2022](https://github.com/tc39/proposal-top-level-await) 开始，**允许在模块的顶层独立使用`await`命令**，使得上面那行代码不会报错了。它的主要目的是使用`await`解决模块异步加载的问题。
  
    ```js
    // awaiting.js
    let output;
    async function main() {
      const dynamic = await import(someMission);
      const data = await fetch(url);
      output = someProcess(dynamic.default, data);
    }
    main();
    export { output };
    ```
  
    上面代码中，模块`awaiting.js`的输出值`output`，取决于异步操作。我们把异步操作包装在一个 `async` 函数里面，然后调用这个函数，只有等里面的异步操作都执行，变量`output`才会有值，否则就返回`undefined`。
  
    下面是加载这个模块的写法。
  
    ```js
    // usage.js
    import { output } from "./awaiting.js";
    
    function outputPlusValue(value) { return output + value }
    
    console.log(outputPlusValue(100));
    setTimeout(() => console.log(outputPlusValue(100)), 1000);
    ```
  
    上面代码中，`outputPlusValue()`的执行结果，完全取决于执行的时间。如果`awaiting.js`里面的异步操作没执行完，加载进来的`output`的值就是`undefined`。
  
    目前的解决方法，就是让原始模块输出一个 Promise 对象，从这个 Promise 对象判断异步操作有没有结束。
  
    ```js
    // awaiting.js
    let output;
    export default (async function main() {
      const dynamic = await import(someMission);
      const data = await fetch(url);
      output = someProcess(dynamic.default, data);
    })();
    export { output };
    ```
  
    上面代码中，`awaiting.js`除了输出`output`，还默认输出一个 Promise 对象（`async` 函数立即执行后，返回一个 Promise 对象），从这个对象判断异步操作是否结束。
  
    下面是加载这个模块的新的写法。
  
    ```js
    // usage.js
    import promise, { output } from "./awaiting.js";
    
    function outputPlusValue(value) { return output + value }
    
    promise.then(() => {
      console.log(outputPlusValue(100));
      setTimeout(() => console.log(outputPlusValue(100)), 1000);
    });
    ```
  
    上面代码中，将`awaiting.js`对象的输出，放在`promise.then()`里面，这样就能保证异步操作完成以后，才去读取`output`。
  
    这种写法比较麻烦，等于要求模块的使用者遵守一个额外的使用协议，按照特殊的方法使用这个模块。一旦你忘了要用 Promise 加载，只使用正常的加载方法，依赖这个模块的代码就可能出错。而且，如果上面的`usage.js`又有对外的输出，等于这个依赖链的所有模块都要使用 Promise 加载。
  
    顶层的`await`命令，就是为了解决这个问题。它保证只有异步操作完成，模块才会输出值。
  
    ```js
    // awaiting.js
    const dynamic = import(someMission);
    const data = fetch(url);
    export const output = someProcess((await dynamic).default, await data);
    ```
  
    上面代码中，两个异步操作在输出的时候，都加上了`await`命令。只有等到异步操作完成，这个模块才会输出值。
  
    加载这个模块的写法如下。
  
    ```js
    // usage.js
    import { output } from "./awaiting.js";
    function outputPlusValue(value) { return output + value }
    
    console.log(outputPlusValue(100));
    setTimeout(() => console.log(outputPlusValue(100)), 1000);
    ```
  
    上面代码的写法，与普通的模块加载完全一样。也就是说，模块的使用者完全不用关心，依赖模块的内部有没有异步操作，正常加载即可。
  
    这时，模块的加载会等待依赖模块（上例是`awaiting.js`）的异步操作完成，才执行后面的代码，有点像暂停在那里。所以，它总是会得到正确的`output`，不会因为加载时机的不同，而得到不一样的值。
  
    注意，**顶层`await`只能用在 ES6 模块，不能用在 CommonJS 模块**。这是因为 CommonJS 模块的`require()`是同步加载，如果有顶层`await`，就没法处理加载了。
  
    下面是顶层`await`的一些使用场景。
  
    ```js
    // import() 方法加载
    const strings = await import(`/i18n/${navigator.language}`);
    
    // 数据库操作
    const connection = await dbConnector();
    
    // 依赖回滚
    let jQuery;
    try {
      jQuery = await import('https://cdn-a.com/jQuery');
    } catch {
      jQuery = await import('https://cdn-b.com/jQuery');
    }
    ```
  
    注意，如果**加载多个包含顶层`await`命令的模块，加载命令是同步执行的**。
  
    ```js
    // x.js
    console.log("X1");
    await new Promise(r => setTimeout(r, 1000));
    console.log("X2");
    
    // y.js
    console.log("Y");
    
    // z.js
    import "./x.js";
    import "./y.js";
    console.log("Z");
    // X1
  // Y
    // X2
  // Z
    ```
    
    上面代码有三个模块，最后的`z.js`加载`x.js`和`y.js`，打印结果是`X1`、`Y`、`X2`、`Z`。这说明，`z.js`并没有等待`x.js`加载完成，再去加载`y.js`。
    
    **顶层的`await`命令有点像，交出代码的执行权给其他的模块加载，等异步操作完成后，再拿回执行权，继续向下执行**。

------

