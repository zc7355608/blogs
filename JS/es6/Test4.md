- ## 对象的扩展

  对象（object）是 JS 最重要的数据结构。ES6 对它进行了重大升级，本章介绍数据结构本身的改变，下一章介绍`Object`的新增方法。

  - #### 属性的简洁表示法

    ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。

    ```js
    const name = '张三';
    
    const obj = { name };
    // 等同于
    const obj = { name: name };
    ```

    上面代码中，变量`foo`直接写在大括号里面。这时，属性名就是变量名，属性值就是变量值。

    除了属性简写，方法也可以简写。

    ```js
    const o = {
      method() {
        return "Hello!";
      }
    };
    
    // 等同于
    
    const o = {
      method: function() {
        return "Hello!";
      }
    };
    ```

    属性的赋值器（setter）和取值器（getter），事实上也是采用这种写法（后面会讲 `getter` 和 `setter`）。

    ```js
    const cart = {
      _wheels: 4,
    
      get wheels () {
        return this._wheels;
      },
    
      set wheels (value) {
        if (value < this._wheels) {
          throw new Error('数值太小了！');
        }
        this._wheels = value;
      }
    }
    ```

    注意，**简写的对象方法不能用作构造函数**，会报错。

    ```js
    const obj = {
      f() {
        this.foo = 'bar';
      }
    };
    
    new obj.f() // 报错
    ```

    上面代码中，`f`是一个简写的对象方法，所以`obj.f`不能当作构造函数使用。

  - #### 属性名表达式

    JS 定义对象的属性，有两种方法。

    ```js
    // 方法一
    obj.foo = true;
    
    // 方法二
    obj['a' + 'bc'] = 123;
    ```

    上面的方法一是直接用标识符作为属性名，方法二是用表达式作为属性名，这时要将表达式放在方括号之内。

    但是，如果使用字面量方式定义对象（使用大括号），在 ES5 中只能使用方法一（标识符）定义属性。

    ```js
    var obj = {
      foo: true,
      abc: 123
    };
    ```

    ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。即属性名动态的从变量中取。

    ```js
    let propKey = 'foo';
    
    let obj = {
      [propKey]: true,
      ['a' + 'bc']: 123
    };
    ```

    表达式还可以用于定义方法名。

    ```js
    let obj = {
      ['h' + 'ello']() {
        return 'hi';
      }
    };
    
    obj.hello() // hi
    ```

    注意：

    1. 属性名表达式与简洁表示法，不能同时使用，会报错。

       ```js
       // 报错
       const foo = 'bar';
       const bar = 'abc';
       const baz = { [foo] };
       
       // 正确
       const foo = 'bar';
       const baz = { [foo]: 'abc'};
       ```

    2. 属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串`[object Object]`，这一点要特别小心。

       ```js
       const keyA = {a: 1};
       const keyB = {b: 2};
       
       const myObject = {
         [keyA]: 'valueA',
         [keyB]: 'valueB'
       };
       
       myObject // Object {[object Object]: "valueB"}
       ```

       上面代码中，`[keyA]`和`[keyB]`得到的都是`[object Object]`，所以`[keyB]`会把`[keyA]`覆盖掉，而`myObject`最后只有一个`[object Object]`属性。

  - #### 属性的可枚举性和遍历

    - ##### 可枚举性：

      对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。`Object.getOwnPropertyDescriptor()`方法可以获取该属性的描述对象。

      ```js
      let obj = { foo: 123 };
      Object.getOwnPropertyDescriptor(obj, 'foo')
      //  {
      //    value: 123,
      //    writable: true,
      //    enumerable: true,
      //    configurable: true
      //  }
      ```

      描述对象的`enumerable`属性，称为“可枚举性”，如果该属性为`false`，就表示某些操作会忽略当前属性。

      目前，有四个操作会忽略`enumerable`为`false`的属性：

      - `for...in`循环：只遍历对象自身的和继承的可枚举的属性。
      - `Object.keys()`：返回对象自身的所有可枚举的属性的键名。
      - `JSON.stringify()`：只串行化对象自身的可枚举的属性。
      - （ES6）`Object.assign()`： 忽略`enumerable`为`false`的属性，只拷贝对象自身的可枚举的属性。

      这四个操作之中，前三个是 ES5 就有的，最后一个`Object.assign()`是 ES6 新增的。其中，只有`for...in`会返回继承的属性，其他三个方法都会忽略继承的属性，只处理对象自身的属性。实际上，引入“可枚举”（`enumerable`）这个概念的最初目的，就是让某些属性可以规避掉`for...in`操作，不然所有内部属性和方法都会被遍历到。比如，对象原型的`toString`方法，以及数组的`length`属性，就通过“可枚举性”，从而避免被`for...in`遍历到。

      ```js
      Object.getOwnPropertyDescriptor(Object.prototype, 'toString').enumerable  // false
      Object.getOwnPropertyDescriptor([], 'length').enumerable  // false
      ```

      上面代码中，`toString`和`length`属性的`enumerable`都是`false`，因此`for...in`不会遍历到这两个继承自原型的属性。

      另外，ES6 规定，**所有 Class 的原型的方法都是不可枚举的**。

      ```js
      Object.getOwnPropertyDescriptor(class {foo() {}}.prototype, 'foo').enumerable  // false
      ```

      总的来说，操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，**尽量不要用`for...in`循环，而用`Object.keys()`代替**。

    - ##### 属性的遍历：

      ES6 一共有 5 种方法可以遍历对象的属性。

      1. `for...in`：遍历对象自身的、以及继承的可枚举属性（不含 Symbol 属性）。
      2. `Object.keys(obj)`：返回一个数组，包括对象自身的所有可枚举属性的键名（不含 Symbol 属性）。
      3. `Object.getOwnPropertyNames(obj)`：返回一个数组，包含对象自身的所有属性（包括不可枚举属性）的键名（不含 Symbol 属性）。
      4. `Object.getOwnPropertySymbols(obj)`：返回一个数组，包含对象自身所有的 Symbol 属性的键名（包括不可枚举属性）。
      5. `Reflect.ownKeys(obj)`：返回一个数组，包含对象自身的所有键名，无论键名是 Symbol 还是字符串，也不管是否可枚举。

      以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

      - 首先遍历所有数值键，按照数值升序排列。
      - 其次遍历所有字符串键，按照加入时间升序排列。
      - 最后遍历所有 Symbol 键，按照加入时间升序排列。

      ```js
      Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 })
      // ['2', '10', 'b', 'a', Symbol()]
      ```

      上面代码中，`Reflect.ownKeys`方法返回一个数组，包含了参数对象的所有属性。这个数组的属性次序是这样的，首先是数值属性`2`和`10`，其次是字符串属性`b`和`a`，最后是 Symbol 属性。

  - #### `super` 关键字

    我们知道，`this`关键字总是指向函数所在的当前对象，ES6 又新增了另一个类似的关键字`super`，指向当前对象的原型对象（`__proto__`）。

    ```js
    const proto = {
      foo: 'hello'
    };
    
    const obj = {
      foo: 'world',
      find() {
        return super.foo;
      }
    };
    
    Object.setPrototypeOf(obj, proto);
    obj.find() // "hello"
    ```

    上面代码中，对象`obj.find()`方法之中，通过`super.foo`引用了原型对象`proto`的`foo`属性。

    注意，**`super`关键字表示原型对象时，只能用在简写的对象方法之中，用在其他地方都会报错**。

    ```js
    // 报错
    const obj = {
      foo: super.foo
    }
    
    // 报错
    const obj = {
      foo: () => super.foo
    }
    
    // 报错
    const obj = {
      foo: function () {
        return super.foo
      }
    }
    ```

    上面三种`super`的用法都会报错，因为对于 JS  引擎来说，这里的`super`都没有用在对象的方法之中。第一种写法是`super`用在属性里面，第二种和第三种写法是`super`用在一个函数里面，然后赋值给`foo`属性。目前，只有对象方法的简写法可以让 JS 引擎确认，定义的是对象的方法。

    JS 引擎内部，`super.foo`等同于`Object.getPrototypeOf(this).foo`（属性）或`Object.getPrototypeOf(this).foo.call(this)`（方法）。

    ```js
    const proto = {
      x: 'hello',
      foo() {
        console.log(this.x);
      },
    };
    
    const obj = {
      x: 'world',
      foo() {
        super.foo();
      }
    }
    
    Object.setPrototypeOf(obj, proto);
    
    obj.foo() // "world"
    ```

    上面代码中，`super.foo`指向原型对象`proto`的`foo`方法，但是绑定的`this`却还是当前对象`obj`，因此输出的就是`world`。

  - #### 对象的扩展运算符

    《数组的扩展》一章中，已经介绍过扩展运算符（`...`）。ES2018 将这个运算符[引入](https://github.com/sebmarkbage/ecmascript-rest-spread)了对象。

    对象的扩展运算符（`...`）用于取出参数对象**自身**所有的**可遍历**属性（包括 Symbol 属性），拷贝到当前对象之中。

    ```js
    let z = { a: 3, b: 4 };
    let n = { ...z };
    n // { a: 3, b: 4 }
    ```

    不同于对数组的扩展，对对象的扩展，它的特点是`...`外层是大括号`{}`。

    由于数组是特殊的对象，所以对象的扩展运算符也可以用于数组。

    ```js
    let foo = { ...['a', 'b', 'c'] };
    foo
    // {0: "a", 1: "b", 2: "c"}
    ```

    **如果扩展运算符后面是一个空对象，（和对数组扩展一样）不产生任何效果**。

    ```js
    {...{}, a: 1}
    // { a: 1 }
    ```

    如果扩展运算符后面不是对象，则**会自动将其转为对象，等同于`Object(value)`**。

    ```js
    // 等同于 {...Object(1)}
    {...1} // {}
    ```

    上面代码中，扩展运算符后面是整数`1`，会自动转为数值的包装对象`Number{1}`。由于该对象没有自身属性，所以返回一个空对象。

    下面的例子都是类似的道理。

    ```js
    // 等同于 {...Object(true)}
    {...true} // {}
    
    // 等同于 {...Object(undefined)}
    {...undefined} // {}
    
    // 等同于 {...Object(null)}
    {...null} // {}
    
    // 等同于 {...Object('hello')}
    {...'hello'}
    ```

    **对象的扩展运算符等同于使用`Object.assign()`方法**。

    ```js
    let aClone = { ...a };
    // 等同于
    let aClone = Object.assign({}, a);
    ```

    上面的例子只是拷贝了对象实例的属性，如果想完整克隆一个对象，还拷贝对象原型的属性，可以采用下面的写法。

    ```js
    // 写法一
    const clone1 = {
      __proto__: Object.getPrototypeOf(obj),
      ...obj
    };
    
    // 写法二
    const clone2 = Object.assign(
      Object.create(Object.getPrototypeOf(obj)),
      obj
    );
    
    // 写法三
    const clone3 = Object.create(
      Object.getPrototypeOf(obj),
      Object.getOwnPropertyDescriptors(obj)
    )
    ```

    上面代码中，写法一的`__proto__`属性在非浏览器的环境不一定部署，因此推荐使用写法二和写法三。

    扩展运算符可以用于**合并两个对象**。

    ```js
    let ab = { ...a, ...b };
    // 等同于
    let ab = Object.assign({}, a, b);
    ```

    如果用户自定义的属性，放在扩展运算符后面，则扩展运算符内部的同名属性会被覆盖掉。

    ```js
    let aWithOverrides = { ...a, x: 1, y: 2 };
    // 等同于
    let aWithOverrides = { ...a, ...{ x: 1, y: 2 } };
    // 等同于
    let x = 1, y = 2, aWithOverrides = { ...a, x, y };
    // 等同于
    let aWithOverrides = Object.assign({}, a, { x: 1, y: 2 });
    ```

    上面代码中，`a`对象的`x`属性和`y`属性，拷贝到新对象后会被覆盖掉。

    这用来修改现有对象部分的属性就很方便了（推荐）。

    ```js
    let newVersion = {
      ...previousVersion,
      name: 'New Name' // Override the name property
    };
    ```

    上面代码中，`newVersion`对象自定义了`name`属性，其他属性全部复制自`previousVersion`对象。

    如果把自定义属性放在扩展运算符前面，就变成了设置新对象的默认属性值。

    ```js
    let aWithDefaults = { x: 1, y: 2, ...a };
    // 等同于
    let aWithDefaults = Object.assign({}, { x: 1, y: 2 }, a);
    // 等同于
    let aWithDefaults = Object.assign({ x: 1, y: 2 }, a);
    ```

    与数组的扩展运算符一样，**对象的扩展运算符后面可以跟表达式**。

    ```js
    const obj = {
      ...(x > 1 ? {a: 1} : {}),
      b: 2,
    };
    ```

    扩展运算符的参数对象之中，如果有取值函数`get`，这个函数是会执行的。

    ```js
    let a = {
      get x() {
        throw new Error('not throw yet');
      }
    }
    
    let aWithXGetter = { ...a }; // 报错
    ```

    上面例子中，取值函数`get`在扩展`a`对象时会自动执行，导致报错。

  - #### `AggregateError` 错误对象

    ES2021 标准之中，为了配合新增的`Promise.any()`方法（参见《Promise 对象》一章），还引入一个新的错误对象`AggregateError`，也放在这一章介绍。

    AggregateError 在一个错误对象里面，封装了多个错误。如果某个单一操作，同时引发了多个错误，需要同时抛出这些错误，那么就可以抛出一个 AggregateError 错误对象，把各种错误都放在这个对象里面。

    AggregateError 本身是一个构造函数，用来生成 AggregateError 实例对象。

    ```js
    AggregateError(errors[, message])
    ```

    `AggregateError()`构造函数可以接受两个参数。

    - errors：数组，它的每个成员都是一个错误对象。该参数是必须的。
    - message：字符串，表示 AggregateError 抛出时的提示信息。该参数是可选的。

    ```js
    const error = new AggregateError([
      new Error('ERROR_11112'),
      new TypeError('First name must be a string'),
      new RangeError('Transaction value must be at least 1'),
      new URIError('User profile link must be https'),
    ], 'Transaction cannot be processed')
    ```

    上面示例中，`AggregateError()`的第一个参数数组里面，一共有四个错误实例。第二个参数字符串则是这四个错误的一个整体的提示。

    `AggregateError`的实例对象有三个属性。

    - name：错误名称，默认为“AggregateError”。
    - message：错误的提示信息。
    - errors：数组，每个成员都是一个错误对象。

    下面是一个示例。

    ```js
    try {
      throw new AggregateError([
        new Error("some error"),
      ], 'Hello');
    } catch (e) {
      console.log(e instanceof AggregateError); // true
      console.log(e.message);                   // "Hello"
      console.log(e.name);                      // "AggregateError"
      console.log(e.errors);                    // [ Error: "some error" ]
    }
    ```

  - #### `Error` 对象的 `cause` 属性

    Error 对象用来表示代码运行时的异常情况，但是从这个对象拿到的上下文信息，有时很难解读，也不够充分。[ES2022](https://github.com/tc39/proposal-error-cause) 为 Error 对象添加了一个`cause`属性，可以在生成错误时，添加报错原因的描述。
    
    它的用法是`new Error()`生成 Error 实例时，给出一个描述对象，该对象可以设置`cause`属性。
    
    ```js
    const actual = new Error('an error!', { cause: 'Error cause' });
    actual.cause; // 'Error cause'
    ```
    
    上面示例中，生成 Error 实例时，使用描述对象给出`cause`属性，写入报错的原因。然后，就可以从实例对象上读取这个属性。
    
    `cause`属性可以放置任意内容，不必一定是字符串。
    
    ```js
    try {
      maybeWorks();
    } catch (err) {
      throw new Error('maybeWorks failed!', { cause: err });
    }
    ```
    
    上面示例中，`cause`属性放置的就是一个对象。

- ## Object 新增的静态方法

  - **`Object.is()`**：判断两个值是否相同。
  
    ES5 比较两个值是否相等，只有两个运算符：相等运算符（`==`）和严格相等运算符（`===`）。它们都有缺点，前者会自动转换数据类型，后者的`NaN`不等于自身，以及`+0`等于`-0`。JS 缺乏一种运算，在所有环境中，只要两个值是一样的，它们就应该相等。
  
    ES6 提出“Same-value equality”（同值相等）算法，用来解决这个问题。`Object.is`就是部署这个算法的新方法。它用来比较两个值是否严格相等，与严格比较运算符（===）的行为基本一致。
  
    ```js
    Object.is('foo', 'foo')
    // true
    Object.is({}, {})
    // false
    ```
  
    **不同之处只有两个：一是`+0`不等于`-0`，二是`NaN`等于自身**。
  
    ```js
    +0 === -0 //true
    NaN === NaN // false
    
    Object.is(+0, -0) // false
    Object.is(NaN, NaN) // true
    ```
  
    ES5 可以通过下面的代码，部署`Object.is`。
  
    ```js
    Object.defineProperty(Object, 'is', {
      value: function(x, y) {
        if (x === y) {
          // 针对+0 不等于 -0的情况
          return x !== 0 || 1 / x === 1 / y;
        }
        // 针对NaN的情况
        return x !== x && y !== y;
      },
      configurable: true,
      enumerable: false,
      writable: true
    });
    ```
  
  - **`Object.assign(target, ...sources)`**：将一个或者多个*源对象*中所有自身的、可枚举的属性复制到*目标对象*，并返回修改后的目标对象。
  
    ```js
    const target = { a: 1 };
    
    const source1 = { b: 2 };
    const source2 = { c: 3 };
    
    Object.assign(target, source1, source2);
    target // {a:1, b:2, c:3}
    ```
  
    `Object.assign()`方法的第一个参数是目标对象，后面的参数都是源对象。
  
    注意，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则**后面的属性会覆盖前面的属性**。
  
    ```js
    const target = { a: 1, b: 1 };
    
    const source1 = { b: 2, c: 2 };
    const source2 = { c: 3 };
    
    Object.assign(target, source1, source2);
    target // {a:1, b:2, c:3}
    ```
  
    如果只有一个参数，`Object.assign()`会直接返回该参数对象。
  
    ```js
    const obj = {a: 1};
    Object.assign(obj) === obj // true
    ```
  
    如果该参数不是对象，则会先转成对象，然后返回。
  
    ```js
    typeof Object.assign(2) // "object"
    ```
  
    由于`undefined`和`null`无法转成对象，所以如果它们作为参数，就会报错。
  
    ```js
    Object.assign(undefined) // 报错
    Object.assign(null) // 报错
    ```
  
    如果非对象参数出现在源对象的位置（即非首参数），那么处理规则有所不同。首先，这些参数都会转成对象，如果无法转成对象，就会跳过。这意味着，如果`undefined`和`null`不在首参数，就不会报错。
  
    ```js
    let obj = {a: 1};
    Object.assign(obj, undefined) === obj // true
    Object.assign(obj, null) === obj // true
    ```
  
    其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。但是，除了字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果。
  
    ```js
    const v1 = 'abc';
    const v2 = true;
    const v3 = 10;
    
    const obj = Object.assign({}, v1, v2, v3);
    console.log(obj); // { "0": "a", "1": "b", "2": "c" }
    ```
  
    上面代码中，`v1`、`v2`、`v3`分别是字符串、布尔值和数值，结果只有字符串合入目标对象（以字符数组的形式），数值和布尔值都会被忽略。这是因为只有字符串的包装对象，会产生可枚举属性。
  
    ```js
    Object(true) // {[[PrimitiveValue]]: true}
    Object(10)  //  {[[PrimitiveValue]]: 10}
    Object('abc') // {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}
    ```
  
    上面代码中，布尔值、数值、字符串分别转成对应的包装对象，可以看到它们的原始值都在包装对象的内部属性`[[PrimitiveValue]]`上面，这个属性是不会被`Object.assign()`拷贝的。只有字符串的包装对象，会产生可枚举的实义属性，那些属性则会被拷贝。
  
    `Object.assign()`拷贝的属性是有限制的，只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（`enumerable: false`）。
  
    ```js
    Object.assign({b: 'c'},
      Object.defineProperty({}, 'invisible', {
        enumerable: false,
        value: 'hello'
      })
    )
    // { b: 'c' }
    ```
  
    上面代码中，`Object.assign()`要拷贝的对象只有一个不可枚举属性`invisible`，这个属性并没有被拷贝进去。
  
    属性名为 Symbol 值的属性，也会被`Object.assign()`拷贝。
  
    ```js
    Object.assign({ a: 'b' }, { [Symbol('c')]: 'd' })
    // { a: 'b', Symbol(c): 'd' }
    ```
  
    注意点：
  
    1. `Object.assign()`方法实行的是浅拷贝，而不是深拷贝。
    2. `Object.assign()`可以用来处理数组，但是会把数组视为对象。
    3. `Object.assign()`只能进行值的复制，如果要复制的值是一个取值函数，那么将求值后再复制。
  
  - **`Object.getOwnPropertyDescriptors()`**：ES5 的`Object.getOwnPropertyDescriptor()`方法会返回某个对象属性的描述对象（descriptor）。ES2017 引入了`Object.getOwnPropertyDescriptors()`方法，返回指定对象自身的、所有的属性描述对象。
  
    ```js
    const obj = {
      foo: 123,
      get bar() { return 'abc' }
    };
    
    Object.getOwnPropertyDescriptors(obj)
    // { foo:
    //    { value: 123,
    //      writable: true,
    //      enumerable: true,
    //      configurable: true },
    //   bar:
    //    { get: [Function: get bar],
    //      set: undefined,
    //      enumerable: true,
    //      configurable: true } }
    ```
  
    上面代码中，`Object.getOwnPropertyDescriptors()`方法返回一个对象，所有原对象的属性名都是该对象的属性名，对应的属性值就是该属性的描述对象。
  
    该方法的实现非常容易。
  
    ```js
    function getOwnPropertyDescriptors(obj) {
      const result = {};
      for (let key of Reflect.ownKeys(obj)) {
        result[key] = Object.getOwnPropertyDescriptor(obj, key);
      }
      return result;
    }
    ```
  
    该方法的引入目的，主要是为了解决`Object.assign()`无法正确拷贝`get`属性和`set`属性的问题。
  
    ```js
    const source = {
      set foo(value) {
        console.log(value);
      }
    };
    
    const target1 = {};
    Object.assign(target1, source);
    
    Object.getOwnPropertyDescriptor(target1, 'foo')
    // { value: undefined,
    //   writable: true,
    //   enumerable: true,
    //   configurable: true }
    ```
  
    上面代码中，`source`对象的`foo`属性的值是一个赋值函数，`Object.assign`方法将这个属性拷贝给`target1`对象，结果该属性的值变成了`undefined`。这是因为`Object.assign`方法总是拷贝一个属性的值，而不会拷贝它背后的赋值方法或取值方法。
  
    这时，`Object.getOwnPropertyDescriptors()`方法配合`Object.defineProperties()`方法，就可以实现正确拷贝。
  
    ```js
    const source = {
      set foo(value) {
        console.log(value);
      }
    };
    
    const target2 = {};
    Object.defineProperties(target2, Object.getOwnPropertyDescriptors(source));
    Object.getOwnPropertyDescriptor(target2, 'foo')
    // { get: undefined,
    //   set: [Function: set foo],
    //   enumerable: true,
    //   configurable: true }
    ```
  
    上面代码中，两个对象合并的逻辑可以写成一个函数。
  
    ```js
    const shallowMerge = (target, source) => Object.defineProperties(
      target,
      Object.getOwnPropertyDescriptors(source)
    );
    ```
  
    `Object.getOwnPropertyDescriptors()`方法的另一个用处，是配合`Object.create()`方法，将对象属性克隆到一个新对象。这属于浅拷贝。
  
    ```js
    const clone = Object.create(Object.getPrototypeOf(obj),
      Object.getOwnPropertyDescriptors(obj));
    
    // 或者
    
    const shallowClone = (obj) => Object.create(
      Object.getPrototypeOf(obj),
      Object.getOwnPropertyDescriptors(obj)
    );
    ```
  
    上面代码会克隆对象`obj`。
  
    另外，`Object.getOwnPropertyDescriptors()`方法可以实现一个对象继承另一个对象。以前，继承另一个对象，常常写成下面这样。
  
    ```js
    const obj = {
      __proto__: prot,
      foo: 123,
    };
    ```
  
    ES6 规定`__proto__`只有浏览器要部署，其他环境不用部署。如果去除`__proto__`，上面代码就要改成下面这样。
  
    ```js
    const obj = Object.create(prot);
    obj.foo = 123;
    
    // 或者
    
    const obj = Object.assign(
      Object.create(prot),
      {
        foo: 123,
      }
    );
    ```
  
    有了`Object.getOwnPropertyDescriptors()`，我们就有了另一种写法。
  
    ```js
    const obj = Object.create(
      prot,
      Object.getOwnPropertyDescriptors({
        foo: 123,
      })
    );
    ```
  
    `Object.getOwnPropertyDescriptors()`也可以用来实现 Mixin（混入）模式。
  
    ```js
    let mix = (object) => ({
      with: (...mixins) => mixins.reduce(
        (c, mixin) => Object.create(
          c, Object.getOwnPropertyDescriptors(mixin)
        ), object)
    });
    
    // multiple mixins example
    let a = {a: 'a'};
    let b = {b: 'b'};
    let c = {c: 'c'};
    let d = mix(c).with(a, b);
    
    d.c // "c"
    d.b // "b"
    d.a // "a"
    ```
  
    上面代码返回一个新的对象`d`，代表了对象`a`和`b`被混入了对象`c`的操作。
  
    出于完整性的考虑，`Object.getOwnPropertyDescriptors()`进入标准以后，以后还会新增`Reflect.getOwnPropertyDescriptors()`方法。
  
  - **`__proto__`属性**：该属性用来读取或设置当前对象的原型对象（prototype）。目前，所有浏览器（包括 IE11）都部署了这个属性。
  
    ```js
    // es5 的写法
    const obj = {
      method: function() { ... }
    };
    obj.__proto__ = someOtherObj;
    
    // es6 的写法
    var obj = Object.create(someOtherObj);
    obj.method = function() { ... };
    ```
  
    该属性没有写入 ES6 的正文，而是写入了附录，原因是`__proto__`前后的双下划线，说明它本质上是一个内部属性，而不是一个正式的对外的 API，只是由于浏览器广泛支持，才被加入了 ES6。标准明确规定，只有浏览器必须部署这个属性，其他运行环境不一定需要部署，而且新的代码最好认为这个属性是不存在的。因此，无论从语义的角度，还是从兼容性的角度，都不要使用这个属性，而是使用下面的`Object.setPrototypeOf()`（写操作）、`Object.getPrototypeOf()`（读操作）、`Object.create()`（生成操作）代替。
  
    实现上，一个对象上的`__proto__`调用的是`Object.prototype.__proto__`，具体实现如下。
  
    ```js
    Object.defineProperty(Object.prototype, '__proto__', {
      get() {
        let _thisObj = Object(this);
        return Object.getPrototypeOf(_thisObj);
      },
      set(proto) {
        if (this === undefined || this === null) {
          throw new TypeError();
        }
        if (!isObject(this)) {
          return undefined;
        }
        if (!isObject(proto)) {
          return undefined;
        }
        let status = Reflect.setPrototypeOf(this, proto);
        if (!status) {
          throw new TypeError();
        }
      },
    });
    
    function isObject(value) {
      return Object(value) === value;
    }
    ```
  
    如果一个对象本身部署了`__proto__`属性，该属性的值就是对象的原型。
  
    ```js
    Object.getPrototypeOf({ __proto__: null })  // null
    ```
  
  - **`Object.setPrototypeOf()`**：该方法的作用与`__proto__`相同，用来设置一个对象的原型对象（prototype），返回参数对象本身。它是 ES6 正式推荐的设置原型对象的方法。
  
    ```js
    // 格式
    Object.setPrototypeOf(object, prototype)
    
    // 用法
    const o = Object.setPrototypeOf({}, null);
    ```
  
    该方法等同于下面的函数。
  
    ```js
    function setPrototypeOf(obj, proto) {
      obj.__proto__ = proto;
      return obj;
    }
    ```
  
    下面是一个例子。
  
    ```js
    let proto = {};
    let obj = { x: 10 };
    Object.setPrototypeOf(obj, proto);
    
    proto.y = 20;
    proto.z = 40;
    
    obj.x // 10
    obj.y // 20
    obj.z // 40
    ```
  
    上面代码将`proto`对象设为`obj`对象的原型，所以从`obj`对象可以读取`proto`对象的属性。
  
    如果第一个参数不是对象，会自动转为对象。但是由于返回的还是第一个参数，所以这个操作不会产生任何效果。
  
    ```js
    Object.setPrototypeOf(1, {}) === 1 // true
    Object.setPrototypeOf('foo', {}) === 'foo' // true
    Object.setPrototypeOf(true, {}) === true // true
    ```
  
    由于`undefined`和`null`无法转为对象，所以如果第一个参数是`undefined`或`null`，就会报错。
  
    ```js
    Object.setPrototypeOf(undefined, {})
    // TypeError: Object.setPrototypeOf called on null or undefined
    
    Object.setPrototypeOf(null, {})
    // TypeError: Object.setPrototypeOf called on null or undefined
    ```
  
  - **`Object.getPrototypeOf()`**：该方法与`Object.setPrototypeOf`方法配套，用于读取一个对象的原型对象。
  
    ```js
    Object.getPrototypeOf(obj);
    ```
  
    下面是一个例子。
  
    ```js
    function Rectangle() {
      // ...
    }
    
    const rec = new Rectangle();
    
    Object.getPrototypeOf(rec) === Rectangle.prototype
    // true
    
    Object.setPrototypeOf(rec, Object.prototype);
    Object.getPrototypeOf(rec) === Rectangle.prototype  // false
    ```
  
    如果参数不是对象，会被自动转为对象。
  
    ```js
    // 等同于 Object.getPrototypeOf(Number(1))
    Object.getPrototypeOf(1)
    // Number {[[PrimitiveValue]]: 0}
    
    // 等同于 Object.getPrototypeOf(String('foo'))
    Object.getPrototypeOf('foo')
    // String {length: 0, [[PrimitiveValue]]: ""}
    
    // 等同于 Object.getPrototypeOf(Boolean(true))
    Object.getPrototypeOf(true)
    // Boolean {[[PrimitiveValue]]: false}
    
    Object.getPrototypeOf(1) === Number.prototype // true
    Object.getPrototypeOf('foo') === String.prototype // true
    Object.getPrototypeOf(true) === Boolean.prototype // true
    ```
  
    如果参数是`undefined`或`null`，它们无法转为对象，所以会报错。
  
    ```js
    Object.getPrototypeOf(null)
    // TypeError: Cannot convert undefined or null to object
    
    Object.getPrototypeOf(undefined)
    // TypeError: Cannot convert undefined or null to object
    ```
  
  - **`Object.values()`**：该方法返回一个数组，成员是参数对象自身的、所有的可遍历（enumerable）属性的键值。
  
    ```js
    const obj = { foo: 'bar', baz: 42 };
    Object.values(obj)
    // ["bar", 42]
    ```
  
    返回数组的成员顺序，与本章的《属性的遍历》部分介绍的排列规则一致。
  
    ```js
    const obj = { 100: 'a', 2: 'b', 7: 'c' };
    Object.values(obj)
    // ["b", "c", "a"]
    ```
  
    上面代码中，属性名为数值的属性，是按照数值大小，从小到大遍历的，因此返回的顺序是`b`、`c`、`a`。
  
    `Object.values`会过滤属性名为 Symbol 值的属性。
  
    ```js
    Object.values({ [Symbol()]: 123, foo: 'abc' });
    // ['abc']
    ```
  
    如果参数不是对象，`Object.values`会先将其转为对象。由于数值和布尔值的包装对象，都不会为实例添加非继承的属性。所以，`Object.values`会返回空数组。
  
    ```js
    Object.values(42) // []
    Object.values(true) // []
    ```
  
    如果`Object.values`方法的参数是一个字符串，会返回各个字符组成的一个数组。
  
    ```js
    Object.values('foo')
    // ['f', 'o', 'o']
    ```
  
    上面代码中，字符串会先转成一个类似数组的对象。字符串的每个字符，就是该对象的一个属性。因此，`Object.values`返回每个属性的键值，就是各个字符组成的一个数组。
  
  - **`Object.entries()`**：该方法返回一个数组，成员是参数对象自身的、所有可遍历（enumerable）属性的键值对数组。
  
    ```js
    const obj = { foo: 'bar', baz: 42 };
    Object.entries(obj)
    // [ ["foo", "bar"], ["baz", 42] ]
    ```
  
    除了返回值不一样，该方法的行为与`Object.values`基本一致。
  
    如果原对象的属性名是一个 Symbol 值，该属性会被忽略。
  
    ```js
    Object.entries({ [Symbol()]: 123, foo: 'abc' });
    // [ [ 'foo', 'abc' ] ]
    ```
  
    上面代码中，原对象有两个属性，`Object.entries`只输出属性名非 Symbol 值的属性。将来可能会有`Reflect.ownEntries()`方法，返回对象自身的所有属性。
  
    `Object.entries`的基本用途是遍历对象的属性。
  
    ```js
    let obj = { one: 1, two: 2 };
    for (let [k, v] of Object.entries(obj)) {
      console.log(
        `${JSON.stringify(k)}: ${JSON.stringify(v)}`
      );
    }
    // "one": 1
    // "two": 2
    ```
  
    `Object.entries`方法的另一个用处是，将对象转为真正的`Map`结构。
  
    ```js
    const obj = { foo: 'bar', baz: 42 };
    const map = new Map(Object.entries(obj));
    map // Map { foo: "bar", baz: 42 }
    ```
  
    自己实现`Object.entries`方法，非常简单。
  
    ```js
    // Generator函数的版本
    function* entries(obj) {
      for (let key of Object.keys(obj)) {
        yield [key, obj[key]];
      }
    }
    
    // 非Generator函数的版本
    function entries(obj) {
      let arr = [];
      for (let key of Object.keys(obj)) {
        arr.push([key, obj[key]]);
      }
      return arr;
    }
    ```
  
  - **`Object.fromEntries()`**：该方法是`Object.entries()`的逆操作，用于将一个键值对数组转为对象。
  
    ```js
    Object.fromEntries([
      ['foo', 'bar'],
      ['baz', 42]
    ])
    // { foo: "bar", baz: 42 }
    ```
  
    该方法的主要目的，是将键值对的数据结构还原为对象，因此特别**适合将 Map 结构转为对象**。
  
    ```js
    // 例一
    const entries = new Map([
      ['foo', 'bar'],
      ['baz', 42]
    ]);
    
    Object.fromEntries(entries)
    // { foo: "bar", baz: 42 }
    
    // 例二
    const map = new Map().set('foo', true).set('bar', false);
    Object.fromEntries(map)
    // { foo: true, bar: false }
    ```
  
    该方法的一个用处是配合`URLSearchParams`对象，将查询字符串转为对象。
  
    ```js
    Object.fromEntries(new URLSearchParams('foo=bar&baz=qux'))
    // { foo: "bar", baz: "qux" }
    ```
  
  - **`Object.hasOwn()`**：判断对象自身是否包含某个属性。该方法旨在取代`Object.prototype.hasOwnProperty()`。
  
    JS 对象的属性分成两种：自身的属性和继承的属性。对象实例有一个`hasOwnProperty()`方法，可以判断某个属性是否为原生属性。ES2022 在`Object`对象上面新增了一个静态方法[`Object.hasOwn()`](https://github.com/tc39/proposal-accessible-object-hasownproperty)，也可以判断是否为自身的属性。
  
    `Object.hasOwn()`可以接受两个参数，第一个是所要判断的对象，第二个是属性名。
  
    ```js
    const foo = Object.create({ a: 123 });
    foo.b = 456;
    
    Object.hasOwn(foo, 'a') // false
    Object.hasOwn(foo, 'b') // true
    ```
  
    上面示例中，对象`foo`的属性`a`是继承属性，属性`b`是原生属性。`Object.hasOwn()`对属性`a`返回`false`，对属性`b`返回`true`。
  
    `Object.hasOwn()`的一个好处是，对于原型为`null`的对象不会报错，而`hasOwnProperty()`是会报错的。
  
    ```js
    const obj = Object.create(null);
    
    obj.hasOwnProperty('foo') // 报错
    Object.hasOwn(obj, 'foo') // false
    ```
  
    上面示例中，`Object.create(null)`返回的对象`obj`是没有原型的，不继承任何属性，这导致调用`obj.hasOwnProperty()`会报错，但是`Object.hasOwn()`就能正确处理这种情况。
  
- ## 运算符的扩展

  - #### 可选链运算符 `?.`

    可选链运算符（`?.`） 用于访问对象的属性或调用函数。如果使用此运算符访问的对象或调用的函数是空（`undefined`或`null`），则表达式会短路并返回 `undefined`，而不是抛出错误。

    编程实务中，如果读取对象内部的某个属性，往往需要判断一下，属性的上层对象是否存在。比如，读取`message.body.user.firstName`这个属性，安全的写法是写成下面这样。
  
    ```js
    // 错误的写法
    const  firstName = message.body.user.firstName || 'default';
    
    // 正确的写法
    const firstName = (message
      && message.body
      && message.body.user
      && message.body.user.firstName) || 'default';
    ```

    上面例子中，`firstName`属性在对象的第四层，所以需要判断四次，每一层是否有值。

    三元运算符`?:`也常用于判断对象是否存在。
  
    ```js
    const fooInput = myForm.querySelector('input[name=foo]')
    const fooValue = fooInput ? fooInput.value : undefined
    ```

    上面例子中，必须先判断`fooInput`是否存在，才能读取`fooInput.value`。

    这样的层层判断非常麻烦，因此 [ES2020](https://github.com/tc39/proposal-optional-chaining) 引入了“可选链运算符”（optional chaining operator）`?.`，简化上面的写法。
  
    ```js
    const firstName = message?.body?.user?.firstName || 'default';
    const fooValue = myForm.querySelector('input[name=foo]')?.value
    ```

    上面代码使用了`?.`运算符，直接在链式调用的时候判断，**左侧的对象是否为`null`或`undefined`。如果是的，就不再往下运算，而是返回`undefined`。**

    下面是判断对象方法是否存在，如果存在就立即执行的例子。
  
    ```js
    iterator.return?.()
    ```

    上面代码中，`iterator.return`如果有定义，就会调用该方法，否则`iterator.return`直接返回`undefined`，不再执行`?.`后面的部分。

    对于那些可能没有实现的方法，这个运算符尤其有用。
  
    ```js
    if (myForm.checkValidity?.() === false) {
      // 表单校验失败
      return;
    }
    ```

    上面代码中，老式浏览器的表单对象可能没有`checkValidity()`这个方法，这时`?.`运算符就会返回`undefined`，判断语句就变成了`undefined === false`，所以就会跳过下面的代码。

    链判断运算符`?.`有三种写法：
  
    - `obj?.prop` // 对象属性是否存在
    - `obj?.[expr]` // 同上
    - `func?.(...args)` // 函数或对象方法是否存在

    下面是`obj?.[expr]`用法的一个例子。
  
    ```js
    let hex = "#C0FFEE".match(/#([A-Z]+)/i)?.[1];
    ```

    上面例子中，字符串的`match()`方法，如果没有发现匹配会返回`null`，如果发现匹配会返回一个数组，`?.`运算符起到了判断作用。

    下面是`?.`运算符常见形式，以及不使用该运算符时的等价形式。
  
    ```js
    a?.b
    // 等同于
    a == null ? undefined : a.b
    
    a?.[x]
    // 等同于
    a == null ? undefined : a[x]
    
    a?.b()
    // 等同于
    a == null ? undefined : a.b()
    
    a?.()
    // 等同于
    a == null ? undefined : a()
    ```

    上面代码中，特别注意后两种形式，如果`a?.b()`和`a?.()`。如果`a?.b()`里面的`a.b`有值，但不是函数，不可调用，那么`a?.b()`是会报错的。`a?.()`也是如此，如果`a`不是`null`或`undefined`，但也不是函数，那么`a?.()`也会报错。

    使用这个运算符，有几个注意点：

    1. 短路机制：本质上，`?.`运算符相当于一种短路机制，只要不满足条件，就不再往下执行。

       ```js
       a?.[++x]
       // 等同于
       a == null ? undefined : a[++x]
       ```
  
       上面代码中，如果`a`是`undefined`或`null`，那么`x`不会进行递增运算。也就是说，链判断运算符一旦为真，右侧的表达式就不再求值。

    2. 括号的影响：如果属性链有圆括号，链判断运算符对圆括号外部没有影响，只对圆括号内部有影响。

       ```js
       (a?.b).c
       // 等价于
       (a == null ? undefined : a.b).c
       ```
  
       上面代码中，`?.`对圆括号外部没有影响，不管`a`对象是否存在，圆括号后面的`.c`总是会执行。
  
       一般来说，使用`?.`运算符的场合，不应该使用圆括号。

    3. 报错场合：以下写法是禁止的，会报错。

       ```js
       // 构造函数
       new a?.()
       new a?.b()
       
       // 链判断运算符的右侧有模板字符串
       a?.`{b}`
       a?.b`{c}`
       
       // 链判断运算符的左侧是 super
       super?.()
       super?.foo
       
       // 链运算符用于赋值运算符左侧
       a?.b = c
       ```
  
    4. 右侧不得为十进制数值：为了保证兼容以前的代码，允许`foo?.3:0`被解析成`foo ? .3 : 0`，因此规定如果`?.`后面紧跟一个十进制数字，那么`?.`不再被看成是一个完整的运算符，而会按照三元运算符进行处理，也就是说，那个小数点会归属于后面的十进制数字，形成一个小数。
  
    5. 可选链运算符不能用于未声明的根对象，但可以用于值为 `undefined` 的根对象。
  
       ```js
       undeclaredVar?.prop; // ReferenceError: undeclaredVar is not defined
       ```

  - #### 空值合并运算符 `??`

    空值合并运算符（`??`）是一个逻辑运算符，当左侧的操作数是空（`undefined`或`null`）时，返回其右侧操作数，否则返回左侧操作数。

    读取对象属性的时候，如果某个属性的值是`null`或`undefined`，有时候需要为它们指定默认值。常见做法是通过`||`运算符指定默认值。
  
    ```js
    const headerText = response.settings.headerText || 'Hello, world!';
    const animationDuration = response.settings.animationDuration || 300;
    const showSplashScreen = response.settings.showSplashScreen || true;
    ```

    上面的三行代码都通过`||`运算符指定默认值，但是这样写是错的。开发者的原意是，只要属性的值为`null`或`undefined`，默认值就会生效，但是属性的值如果为空字符串或`false`或`0`，默认值也会生效。

    为了避免这种情况，[ES2020](https://github.com/tc39/proposal-nullish-coalescing) 引入了一个新的 Null 判断运算符`??`。它的行为类似`||`，但是只有运算符左侧的值为`null`或`undefined`时，才会返回右侧的值。
  
    ```js
    const headerText = response.settings.headerText ?? 'Hello, world!';
    const animationDuration = response.settings.animationDuration ?? 300;
    const showSplashScreen = response.settings.showSplashScreen ?? true;
    ```

    上面代码中，默认值只有在左侧属性值为`null`或`undefined`时，才会生效。

    这个运算符的一个目的，就是跟链判断运算符`?.`配合使用，为`null`或`undefined`的值设置默认值。
  
    ```js
    const animationDuration = response.settings?.animationDuration ?? 300;
    ```

    上面代码中，如果`response.settings`是`null`或`undefined`，或者`response.settings.animationDuration`是`null`或`undefined`，就会返回默认值300。也就是说，这一行代码包括了两级属性的判断。

    这个运算符很适合判断函数参数是否赋值。
  
    ```js
    function Component(props) {
      const enable = props.enabled ?? true;
      // …
    }
    ```

    上面代码判断`props`参数的`enabled`属性是否赋值，基本等同于下面的写法。
  
    ```js
    function Component(props) {
      const {
        enabled: enable = true,
      } = props;
      // …
    }
    ```

    `??`本质上是逻辑运算，它与其他两个逻辑运算符`&&`和`||`有一个优先级问题，它们之间的优先级到底孰高孰低。优先级的不同，往往会导致逻辑运算的结果不同。

    现在的规则是，**如果多个逻辑运算符一起使用，必须用括号表明优先级，否则会报错。**
  
    ```js
    // 报错
    lhs && middle ?? rhs
    lhs ?? middle && rhs
    lhs || middle ?? rhs
    lhs ?? middle || rhs
    ```

    上面四个表达式都会报错，必须加入表明优先级的括号。
  
    ```js
    (lhs && middle) ?? rhs;
    lhs && (middle ?? rhs);
    
    (lhs ?? middle) && rhs;
    lhs ?? (middle && rhs);
    
    (lhs || middle) ?? rhs;
    lhs || (middle ?? rhs);
    
    (lhs ?? middle) || rhs;
    lhs ?? (middle || rhs);
    ```

  - #### 逻辑赋值运算符

    ES2021 引入了三个新的[逻辑赋值运算符](https://github.com/tc39/proposal-logical-assignment)（logical assignment operators），将逻辑运算符与赋值运算符进行结合。
  
    ```js
    // 或赋值运算符
    x ||= y
    // 等同于
    x || (x = y)
    
    // 与赋值运算符
    x &&= y
    // 等同于
    x && (x = y)
    
    // Null 赋值运算符
    x ??= y
    // 等同于
    x ?? (x = y)
    ```

    这三个运算符`||=`、`&&=`、`??=`相当于先进行逻辑运算，然后根据运算结果，再视情况进行赋值。

    它们的一个用途是，为变量或属性设置默认值。
  
    ```js
    // 老的写法
    user.id = user.id || 1;
    
    // 新的写法
    user.id ||= 1;
    ```

    上面示例中，`user.id`属性如果不存在，则设为`1`，新的写法比老的写法更紧凑一些。

    下面是另一个例子。
  
    ```js
    function example(opts) {
      opts.foo = opts.foo ?? 'bar';
      opts.baz ?? (opts.baz = 'qux');
    }
    ```

    上面示例中，参数对象`opts`如果不存在属性`foo`和属性`baz`，则为这两个属性设置默认值。有了“Null 赋值运算符”以后，就可以统一写成下面这样。
  
    ```js
    function example(opts) {
      opts.foo ??= 'bar';
      opts.baz ??= 'qux';
    }
    ```

  - #### `#!`命令
  
    Unix 的命令行脚本都支持`#!`命令，又称为 Shebang 或 Hashbang。这个命令放在脚本的第一行，用来指定脚本的执行器。
    
    比如 Bash 脚本的第一行。
    
    ```bash
    #!/bin/sh
    ```
    
    Python 脚本的第一行。
    
    ```python
    #!/usr/bin/env python
    ```

    [ES2023](https://github.com/tc39/proposal-hashbang) 为 JS 脚本引入了`#!`命令，写在脚本文件或者模块文件的第一行。
  
    ```js
    // 写在脚本文件第一行
    #!/usr/bin/env node
    'use strict';
    console.log(1);
    
    // 写在模块文件第一行
    #!/usr/bin/env node
    export {};
    console.log(1);
    ```
  
    有了这一行以后，Unix 命令行就可以直接执行脚本。
    
    ```bash
    # 以前执行脚本的方式
    $ node hello.js
    
    # hashbang 的方式
    $ ./hello.js
    ```
    
    对于 JS 解释器来说，会把`#!`理解成注释，忽略掉这一行。
  
    > （了解）**Tips**：关于`#!`
    >
    > - Shebang 通常出现在类Unix系统的脚本中第一行，作为前两个字符。在 Shebang 之后，可以有一个或数个空白字符，后接解释器的绝对路径，用于指明执行这个 Shell 脚本的解释器。在直接调用脚本时，系统的程序载入器会分析 Shebang 后的内容，将这些内容作为解释器指令，并调用该指令，将载有 Shebang 的文件路径作为该解释器的参数，执行脚本，从而使得脚本文件的调用方式与普通的可执行文件类似。例如，以指令 `#!/bin/sh` 开头的文件，在执行时会实际调用 /bin/sh 程序（通常是 Bourne shell 或兼容的 shell，例如 bash、dash 等）来执行。
    > - 由于 # 符号在许多脚本语言中都是注释标识符，Shebang 的内容会被这些脚本解释器自动忽略。 在 # 字符不是注释标识符的语言中，例如 Scheme，解释器也可能忽略以 #! 开头的首行内容，以提供与 Shebang 的兼容性。
    > - Shebang 的一些具体用法罗列如下：
    >   1. 如果脚本文件中没有#!这一行，那么执行时会默认采用当前Shell去解释这个脚本(即：<code>$SHELL</code>环境变量）。
    >   2. 如果#!之后的解释程序是一个可执行文件，那么执行这个脚本时，它就会把文件名及其参数一起作为参数传给那个解释程序去执行。
    >   3. 如果#!指定的解释程序没有可执行权限，则会报错“bad interpreter: Permission denied”。如果#!指定的解释程序不是一个可执行文件，那么指定的解释程序会被忽略，转而交给当前的SHELL去执行这个脚本。
    >   4. 如果#!指定的解释程序不存在，那么会报错“bad interpreter: No such file or directory”。注意：#!之后的解释程序，需要写其绝对路径（如：#!/bin/bash），它是不会自动到`$PATH`中寻找解释器的。
    >   5. 当然，如果你使用类似于”bash test.sh”这样的命令来执行脚本，那么#!这一行将会被忽略掉，解释器当然是用命令行中显式指定的bash。
    >   6. 脚本文件必须拥有可执行权限。
  

------

