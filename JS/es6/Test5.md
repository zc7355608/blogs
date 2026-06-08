- ## Symbol

  ES5 的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin  模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。

  ES6 引入了一种新的原始数据类型`Symbol`，表示独一无二的值。它是 JS 的8种数据类型之一。

  **Symbol 值通过`Symbol()`函数生成**。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。只要属性名是 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

  ```js
  let s = Symbol();
  typeof s  // "symbol"
  ```

  上面代码中，变量`s`就是一个独一无二的值。`typeof`运算符的结果，表明变量`s`是 Symbol 数据类型，而不是字符串之类的其他类型。

  注意，`Symbol()`函数前不能使用`new`命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象，所以不能使用`new`命令来调用。另外，由于 Symbol 值不是对象，所以也不能添加属性。基本上，它**是一种类似于字符串的数据类型**。

  `Symbol()`函数可以接受一个字符串作为参数，表示对该 Symbol 值的描述。这主要是为了在控制台显示，或者转为字符串时，比较容易区分。

  ```js
  let s1 = Symbol('foo');
  let s2 = Symbol('bar');
  
  s1 // Symbol(foo)
  s2 // Symbol(bar)
  
  s1.toString() // "Symbol(foo)"
  s2.toString() // "Symbol(bar)"
  ```

  上面代码中，`s1`和`s2`是两个 Symbol 值。如果不加参数，它们在控制台的输出都是`Symbol()`，不利于区分。有了参数以后，就等于为它们加上了描述，输出的时候就能够分清，到底是哪一个值。

  如果 Symbol 的参数是一个对象，就会调用该对象的`toString()`方法，将其转为字符串，然后才生成一个 Symbol 值。

  ```js
  const obj = {
    toString() {
      return 'abc';
    }
  };
  const sym = Symbol(obj);
  sym // Symbol(abc)
  ```

  注意，**`Symbol()`函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。**

  ```js
  // 没有参数的情况
  let s1 = Symbol();
  let s2 = Symbol();
  
  s1 === s2 // false
  
  // 有参数的情况
  let s1 = Symbol('foo');
  let s2 = Symbol('foo');
  
  s1 === s2 // false
  ```

  上面代码中，`s1`和`s2`都是`Symbol()`函数的返回值，而且参数相同，但是它们是不相等的。事实上，如果调用100次`Symbol()`，会得到100个互不相等的值。

  **Symbol 值不能与其他类型的值进行运算**，会报错。

  ```js
  let sym = Symbol('My symbol');
  
  "your symbol is " + sym
  // TypeError: can't convert symbol to string
  `your symbol is ${sym}`
  // TypeError: can't convert symbol to string
  ```

  但是，**Symbol 值可以显式转为字符串**。

  ```js
  let sym = Symbol('My symbol');
  
  String(sym) // 'Symbol(My symbol)'
  sym.toString() // 'Symbol(My symbol)'
  ```

  另外，**Symbol 值也可以转为布尔值，但是不能转为数值**。

  ```js
  let sym = Symbol();
  Boolean(sym) // true
  !sym  // false
  
  if (sym) {
    // ...
  }
  
  Number(sym) // TypeError
  sym + 2 // TypeError
  ```

  - #### `Symbol.prototype.description`

    前面说过，`Symbol()`函数创建 Symbol 值时，可以用参数添加一个描述。

    ```js
    const sym = Symbol('foo');
    ```

    上面代码中，`sym`这个值的描述就是字符串`foo`。

    但是，读取这个描述需要将 Symbol 显式转为字符串，即下面的写法。

    ```js
    const sym = Symbol('foo');
    
    String(sym) // "Symbol(foo)"
    sym.toString() // "Symbol(foo)"
    ```

    上面的用法不是很方便。[ES2019](https://github.com/tc39/proposal-Symbol-description) 提供了一个 Symbol 值的实例属性`description`，直接返回 Symbol 值的描述。

    ```js
    const sym = Symbol('foo');
    
    sym.description // "foo"
    ```

  - #### 作为属性名的 Symbol

    由于每一个 Symbol 值都是不相等的，这意味着只要 Symbol 值作为标识符，用于对象的属性名，就能保证不会出现同名的属性。这对于一个对象由多个模块构成的情况非常有用，能防止某一个键被不小心改写或覆盖。

    ```js
    let mySymbol = Symbol();
    
    // 第一种写法
    let a = {};
    a[mySymbol] = 'Hello!';
    
    // 第二种写法
    let a = {
      [mySymbol]: 'Hello!'
    };
    
    // 第三种写法
    let a = {};
    Object.defineProperty(a, mySymbol, { value: 'Hello!' });
    
    // 以上写法都得到同样结果
    a[mySymbol] // "Hello!"
    ```

    上面代码通过方括号结构和`Object.defineProperty()`方法，将对象的属性名指定为一个 Symbol 值。

    注意，Symbol 值作为对象属性名时，不能用点运算符。

    ```js
    const mySymbol = Symbol();
    const a = {};
    
    a.mySymbol = 'Hello!';
    a[mySymbol] // undefined
    a['mySymbol'] // "Hello!"
    ```

    上面代码中，因为点运算符后面总是字符串，所以不会读取`mySymbol`作为标识名所指代的那个值，导致`a`的属性名实际上是一个字符串，而不是一个 Symbol 值。

    同理，在对象的内部，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中。

    ```js
    let s = Symbol();
    
    let obj = {
      [s]: function (arg) { ... }
      // 或者
      [s](arg) { ... }
    };
    
    obj[s](123);
    ```

    上面代码中，如果`s`不放在方括号中，该属性的键名就是字符串`s`，而不是`s`所代表的那个 Symbol 值。

    Symbol 类型还可以用于定义一组常量，保证这组常量的值都是不相等的。

    ```js
    const log = {};
    
    log.levels = {
      DEBUG: Symbol('debug'),
      INFO: Symbol('info'),
      WARN: Symbol('warn')
    };
    console.log(log.levels.DEBUG, 'debug message');
    console.log(log.levels.INFO, 'info message');
    ```

    下面是另外一个例子。

    ```js
    const COLOR_RED    = Symbol();
    const COLOR_GREEN  = Symbol();
    
    function getComplement(color) {
      switch (color) {
        case COLOR_RED:
          return COLOR_GREEN;
        case COLOR_GREEN:
          return COLOR_RED;
        default:
          throw new Error('Undefined color');
        }
    }
    ```

    常量使用 Symbol 值最大的好处，就是其他任何值都不可能有相同的值了，因此可以保证上面的`switch`语句会按设计的方式工作。

    注意，Symbol 值作为属性名时，该属性还是公开属性，不是私有属性。

  - #### 属性名的遍历

    Symbol 值作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。因为毕竟 Symbol 不是字符串。

    但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有 Symbol 属性名。该方法返回一个数组，成员是当前对象的所有的 Symbol 属性名。

    ```js
    const obj = {};
    let a = Symbol('a');
    let b = Symbol('b');
    
    obj[a] = 'Hello';
    obj[b] = 'World';
    
    const objectSymbols = Object.getOwnPropertySymbols(obj);
    
    objectSymbols
    // [Symbol(a), Symbol(b)]
    ```

    上面代码是`Object.getOwnPropertySymbols()`方法的示例，可以获取所有 Symbol 属性名。

    下面是另一个例子，`Object.getOwnPropertySymbols()`方法与`for...in`循环、`Object.getOwnPropertyNames`方法进行对比的例子。

    ```js
    const obj = {};
    const foo = Symbol('foo');
    
    obj[foo] = 'bar';
    
    for (let i in obj) {
      console.log(i); // 无输出
    }
    
    Object.getOwnPropertyNames(obj) // []
    Object.getOwnPropertySymbols(obj) // [Symbol(foo)]
    ```

    上面代码中，使用`for...in`循环和`Object.getOwnPropertyNames()`方法都得不到 Symbol 键名，需要使用`Object.getOwnPropertySymbols()`方法。

    另一个新的 API，`Reflect.ownKeys()`方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。

    ```js
    let obj = {
      [Symbol('my_key')]: 1,
      enum: 2,
      nonEnum: 3
    };
    
    Reflect.ownKeys(obj)
    //  ["enum", "nonEnum", Symbol(my_key)]
    ```

    由于以 Symbol 值作为键名，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。

    ```js
    let size = Symbol('size');
    
    class Collection {
      constructor() {
        this[size] = 0;
      }
    
      add(item) {
        this[this[size]] = item;
        this[size]++;
      }
    
      static sizeOf(instance) {
        return instance[size];
      }
    }
    
    let x = new Collection();
    Collection.sizeOf(x) // 0
    
    x.add('foo');
    Collection.sizeOf(x) // 1
    
    Object.keys(x) // ['0']
    Object.getOwnPropertyNames(x) // ['0']
    Object.getOwnPropertySymbols(x) // [Symbol(size)]
    ```

    上面代码中，对象`x`的`size`属性是一个 Symbol 值，所以`Object.keys(x)`、`Object.getOwnPropertyNames(x)`都无法获取它。这就造成了一种非私有的内部方法的效果。

  - #### `Symbol.for()，Symbol.keyFor()`

    `Symbol.for(key)` 方法会根据给定的键 `key`（`string`类型），来从运行时的 symbol 注册表中找到对应的 symbol，如果找到了，则返回它，否则，新建一个与该键关联的 symbol，并放入全局 symbol 注册表中。

    有时，我们希望重新使用同一个 Symbol 值，`Symbol.for()`方法可以做到这一点。它接受一个字符串作为`key`，然后在全局搜索有没有以该`key`注册的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建一个以该`key`为描述的 Symbol 值，**并将其注册到全局**。

    ```js
    let s1 = Symbol.for('foo');
    let s2 = Symbol.for('foo');
    
    s1 === s2 // true
    ```

    上面代码中，`s1`和`s2`都是 Symbol 值，但是它们都是由同样参数的`Symbol.for`方法生成的，所以实际上是同一个值。

    `Symbol.for()`与`Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，**前者会被登记在全局环境中供搜索**，后者不会。`Symbol.for()`不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的`key`是否已经存在，如果不存在才会新建一个值。比如，如果你调用`Symbol.for("cat")`30 次，每次都会返回同一个 Symbol 值，但是调用`Symbol("cat")`30 次，会返回 30 个不同的 Symbol 值。

    ```js
    Symbol.for("bar") === Symbol.for("bar")  // true
    
    Symbol("bar") === Symbol("bar")  // false
    ```

    上面代码中，由于**`Symbol()`写法没有登记机制**，所以每次调用都会返回一个不同的值。

    **`Symbol.keyFor()`方法返回一个在全局已注册的 Symbol 值的`key`。找不到返回`undefined`**。

    ```js
    let s1 = Symbol.for("foo");
    Symbol.keyFor(s1) // "foo"
    
    let s2 = Symbol("foo");
    Symbol.keyFor(s2) // undefined
    ```

    上面代码中，变量`s2`属于未登记的 Symbol 值，所以返回`undefined`。

    注意，`Symbol.for()`为 Symbol 值登记的名字，是全局环境的，不管有没有在全局环境运行。

    ```js
    function foo() {
      return Symbol.for('bar');
    }
    
    const x = foo();
    const y = Symbol.for('bar');
    console.log(x === y); // true
    ```

    上面代码中，`Symbol.for('bar')`是函数内部运行的，但是生成的 Symbol 值是登记在全局环境的。所以，第二次运行`Symbol.for('bar')`可以取到这个 Symbol 值。

    **`Symbol.for()`的这个全局登记特性，可以用在不同的 `<iframe>` 或 service worker 中取到同一个值**。（同一个页面的全局共享）

    ```js
    iframe = document.createElement('iframe');
    iframe.src = String(window.location);
    document.body.appendChild(iframe);
    
    iframe.contentWindow.Symbol.for('foo') === Symbol.for('foo')
    // true
    ```

    上面代码中，iframe 窗口生成的 Symbol 值，可以在主页面得到。

  - #### （了解）实例：模块的 Singleton 模式

    Singleton 模式指的是调用一个类，任何时候返回的都是同一个实例。

    对于 Node 来说，模块文件可以看成是一个类。怎么保证每次执行这个模块文件，返回的都是同一个实例呢？

    很容易想到，可以把实例放到顶层对象`global`。

    ```js
    // mod.js
    function A() {
      this.foo = 'hello';
    }
    
    if (!global._foo) {
      global._foo = new A();
    }
    
    module.exports = global._foo;
    ```

    然后，加载上面的`mod.js`。

    ```js
    const a = require('./mod.js');
    console.log(a.foo);
    ```

    上面代码中，变量`a`任何时候加载的都是`A`的同一个实例。

    但是，这里有一个问题，全局变量`global._foo`是可写的，任何文件都可以修改。

    ```js
    global._foo = { foo: 'world' };
    
    const a = require('./mod.js');
    console.log(a.foo);
    ```

    上面的代码，会使得加载`mod.js`的脚本都失真。

    为了防止这种情况出现，我们就可以使用 Symbol。

    ```js
    // mod.js
    const FOO_KEY = Symbol.for('foo');
    
    function A() {
      this.foo = 'hello';
    }
    
    if (!global[FOO_KEY]) {
      global[FOO_KEY] = new A();
    }
    
    module.exports = global[FOO_KEY];
    ```

    上面代码中，可以保证`global[FOO_KEY]`不会被无意间覆盖，但还是可以被改写。

    ```js
    global[Symbol.for('foo')] = { foo: 'world' };
    
    const a = require('./mod.js');
    ```

    如果键名使用`Symbol`方法生成，那么外部将无法引用这个值，当然也就无法改写。

    ```js
    // mod.js
    const FOO_KEY = Symbol('foo');
    
    // 后面代码相同 ……
    ```

    上面代码将导致其他脚本都无法引用`FOO_KEY`。但这样也有一个问题，就是如果多次执行这个脚本，每次得到的`FOO_KEY`都是不一样的。虽然 Node 会将脚本的执行结果缓存，一般情况下，不会多次执行同一个脚本，但是用户可以手动清除缓存，所以也不是绝对可靠。

  - #### JS 内置的 Symbol 值

    除了定义自己使用的 Symbol 值以外，ES6 还提供了 11 个内置的 Symbol 值。

    - `Symbol.hasInstance`：对象的`Symbol.hasInstance`属性，指向一个内部方法。当其他对象使用`instanceof`运算符，判断是否为该对象的实例时，会调用这个方法。比如，`foo instanceof Foo`在语言内部，实际调用的是`Foo[Symbol.hasInstance](foo)`。

      ```js
      class MyClass {
        [Symbol.hasInstance](foo) {
          return foo instanceof Array;
        }
      }
      
      [1, 2, 3] instanceof new MyClass() // true
      ```

      上面代码中，`MyClass`是一个类，`new MyClass()`会返回一个实例。该实例的`Symbol.hasInstance`方法，会在进行`instanceof`运算时自动调用，判断左侧的运算子是否为`Array`的实例。

      下面是另一个例子。

      ```js
      class Even {
        static [Symbol.hasInstance](obj) {
          return Number(obj) % 2 === 0;
        }
      }
      
      // 等同于
      const Even = {
        [Symbol.hasInstance](obj) {
          return Number(obj) % 2 === 0;
        }
      };
      
      1 instanceof Even // false
      2 instanceof Even // true
      12345 instanceof Even // false
      ```

    - `Symbol.isConcatSpreadable`：对象的`Symbol.isConcatSpreadable`属性等于一个布尔值，表示该对象用于`Array.prototype.concat()`时，是否可以展开。

      ```js
      let arr1 = ['c', 'd'];
      ['a', 'b'].concat(arr1, 'e') // ['a', 'b', 'c', 'd', 'e']
      arr1[Symbol.isConcatSpreadable] // undefined
      
      let arr2 = ['c', 'd'];
      arr2[Symbol.isConcatSpreadable] = false;
      ['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
      ```

      上面代码说明，数组的默认行为是可以展开，`Symbol.isConcatSpreadable`默认等于`undefined`。该属性等于`true`时，也有展开的效果。

      类似数组的对象正好相反，默认不展开。它的`Symbol.isConcatSpreadable`属性设为`true`，才可以展开。

      ```js
      let obj = {length: 2, 0: 'c', 1: 'd'};
      ['a', 'b'].concat(obj, 'e') // ['a', 'b', obj, 'e']
      
      obj[Symbol.isConcatSpreadable] = true;
      ['a', 'b'].concat(obj, 'e') // ['a', 'b', 'c', 'd', 'e']
      ```

      `Symbol.isConcatSpreadable`属性也可以定义在类里面。

      ```js
      class A1 extends Array {
        constructor(args) {
          super(args);
          this[Symbol.isConcatSpreadable] = true;
        }
      }
      class A2 extends Array {
        constructor(args) {
          super(args);
        }
        get [Symbol.isConcatSpreadable] () {
          return false;
        }
      }
      let a1 = new A1();
      a1[0] = 3;
      a1[1] = 4;
      let a2 = new A2();
      a2[0] = 5;
      a2[1] = 6;
      [1, 2].concat(a1).concat(a2)
      // [1, 2, 3, 4, [5, 6]]
      ```

      上面代码中，类`A1`是可展开的，类`A2`是不可展开的，所以使用`concat`时有不一样的结果。

      注意，`Symbol.isConcatSpreadable`的位置差异，`A1`是定义在实例上，`A2`是定义在类本身，效果相同。

    - `Symbol.species`：对象的`Symbol.species`属性，指向一个构造函数。创建衍生对象时，会使用该属性。

      ```js
      class MyArray extends Array {
      }
      
      const a = new MyArray(1, 2, 3);
      const b = a.map(x => x);
      const c = a.filter(x => x > 1);
      
      b instanceof MyArray // true
      c instanceof MyArray // true
      ```

      上面代码中，子类`MyArray`继承了父类`Array`，`a`是`MyArray`的实例，`b`和`c`是`a`的衍生对象。你可能会认为，`b`和`c`都是调用数组方法生成的，所以应该是数组（`Array`的实例），但实际上它们也是`MyArray`的实例。

      `Symbol.species`属性就是为了解决这个问题而提供的。现在，我们可以为`MyArray`设置`Symbol.species`属性。

      ```js
      class MyArray extends Array {
        static get [Symbol.species]() { return Array; }
      }
      ```

      上面代码中，由于定义了`Symbol.species`属性，创建衍生对象时就会使用这个属性返回的函数，作为构造函数。这个例子也说明，定义`Symbol.species`属性要采用`get`取值器。默认的`Symbol.species`属性等同于下面的写法。

      ```js
      static get [Symbol.species]() {
        return this;
      }
      ```

      现在，再来看前面的例子。

      ```js
      class MyArray extends Array {
        static get [Symbol.species]() { return Array; }
      }
      
      const a = new MyArray();
      const b = a.map(x => x);
      
      b instanceof MyArray // false
      b instanceof Array // true
      ```

      上面代码中，`a.map(x => x)`生成的衍生对象，就不是`MyArray`的实例，而直接就是`Array`的实例。

      再看一个例子。

      ```js
      class T1 extends Promise {
      }
      
      class T2 extends Promise {
        static get [Symbol.species]() {
          return Promise;
        }
      }
      
      new T1(r => r()).then(v => v) instanceof T1 // true
      new T2(r => r()).then(v => v) instanceof T2 // false
      ```

      上面代码中，`T2`定义了`Symbol.species`属性，`T1`没有。结果就导致了创建衍生对象时（`then`方法），`T1`调用的是自身的构造方法，而`T2`调用的是`Promise`的构造方法。

      总之，`Symbol.species`的作用在于，实例对象在运行过程中，需要再次调用自身的构造函数时，会调用该属性指定的构造函数。它主要的用途是，有些类库是在基类的基础上修改的，那么子类使用继承的方法时，作者可能希望返回基类的实例，而不是子类的实例。

    - `Symbol.match`：对象的`Symbol.match`属性，指向一个函数。当执行`str.match(myObject)`时，如果该属性存在，会调用它，返回该方法的返回值。

      ```js
      String.prototype.match(regexp)
      // 等同于
      regexp[Symbol.match](this)
      
      class MyMatcher {
        [Symbol.match](string) {
          return 'hello world'.indexOf(string);
        }
      }
      
      'e'.match(new MyMatcher()) // 1
      ```

    - `Symbol.replace`：对象的`Symbol.replace`属性，指向一个方法，当该对象被`String.prototype.replace`方法调用时，会返回该方法的返回值。

      ```js
      String.prototype.replace(searchValue, replaceValue)
      // 等同于
      searchValue[Symbol.replace](this, replaceValue)
      ```

      下面是一个例子。

      ```js
      const x = {};
      x[Symbol.replace] = (...s) => console.log(s);
      
      'Hello'.replace(x, 'World') // ["Hello", "World"]
      ```

      `Symbol.replace`方法会收到两个参数，第一个参数是`replace`方法正在作用的对象，上面例子是`Hello`，第二个参数是替换后的值，上面例子是`World`。

    - `Symbol.search`：对象的`Symbol.search`属性，指向一个方法，当该对象被`String.prototype.search`方法调用时，会返回该方法的返回值。

      ```js
      String.prototype.search(regexp)
      // 等同于
      regexp[Symbol.search](this)
      
      class MySearch {
        constructor(value) {
          this.value = value;
        }
        [Symbol.search](string) {
          return string.indexOf(this.value);
        }
      }
      'foobar'.search(new MySearch('foo')) // 0
      ```

    - `Symbol.split`：对象的`Symbol.split`属性，指向一个方法，当该对象被`String.prototype.split`方法调用时，会返回该方法的返回值。

      ```js
      String.prototype.split(separator, limit)
      // 等同于
      separator[Symbol.split](this, limit)
      ```

      下面是一个例子。

      ```js
      class MySplitter {
        constructor(value) {
          this.value = value;
        }
        [Symbol.split](string) {
          let index = string.indexOf(this.value);
          if (index === -1) {
            return string;
          }
          return [
            string.substr(0, index),
            string.substr(index + this.value.length)
          ];
        }
      }
      
      'foobar'.split(new MySplitter('foo'))
      // ['', 'bar']
      
      'foobar'.split(new MySplitter('bar'))
      // ['foo', '']
      
      'foobar'.split(new MySplitter('baz'))
      // 'foobar'
      ```

      上面方法使用`Symbol.split`方法，重新定义了字符串对象的`split`方法的行为。

    - `Symbol.iterator`：对象的`Symbol.iterator`属性，指向该对象的默认迭代器方法。该属性就是迭代器（Iterator）接口，一个对象具有该属性就代表部署了迭代器接口。

      ```js
      const myIterable = {};
      myIterable[Symbol.iterator] = function* () {
        yield 1;
        yield 2;
        yield 3;
      };
      
      [...myIterable] // [1, 2, 3]
      ```

      对象进行`for...of`循环时，会调用`Symbol.iterator`方法，返回该对象的默认迭代器，详细介绍参见《Iterator 和 for...of 循环》一章。

      ```js
      class Collection {
        *[Symbol.iterator]() {
          let i = 0;
          while(this[i] !== undefined) {
            yield this[i];
            ++i;
          }
        }
      }
      
      let myCollection = new Collection();
      myCollection[0] = 1;
      myCollection[1] = 2;
      
      for(let value of myCollection) {
        console.log(value);
      }
      // 1
      // 2
      ```

    - `Symbol.toPrimitive`：对象的`Symbol.toPrimitive`属性，指向一个方法。该对象被转为原始类型的值时，会调用这个方法，返回该对象对应的原始类型值。

      `Symbol.toPrimitive`被调用时，会接受一个字符串参数，表示当前运算的模式，一共有三种模式。

      - `number`：该场合需要转成数值
      - `string`：该场合需要转成字符串
      - `default`：该场合可以转成数值，也可以转成字符串

      ```js
      let obj = {
        [Symbol.toPrimitive](hint) {
          switch (hint) {
            case 'number':
              return 123;
            case 'string':
              return 'str';
            case 'default':
              return 'default';
            default:
              throw new Error();
           }
         }
      };
      
      2 * obj // 246
      3 + obj // '3default'
      obj == 'default' // true
      String(obj) // 'str'
      ```

    - `Symbol.toStringTag`：对象的`Symbol.toStringTag`属性，用来设定一个字符串（设为其他类型的值无效，但不报错）。在目标对象上面调用`Object.prototype.toString()`方法时，如果`Symbol.toStringTag`属性存在，该属性设定的字符串会出现在`toString()`方法返回的字符串之中，表示对象的类型。也就是说，这个属性可以用来定制`[object Object]`或`[object Array]`中`object`后面的那个大写字符串。

      ```js
      // 例一
      ({[Symbol.toStringTag]: 'Foo'}.toString())
      // "[object Foo]"
      
      // 例二
      class Collection {
        get [Symbol.toStringTag]() {
          return 'xxx';
        }
      }
      let x = new Collection();
      Object.prototype.toString.call(x) // "[object xxx]"
      ```

      > ES6 新增内置对象的`Symbol.toStringTag`属性值如下：
      >
      > - `JSON[Symbol.toStringTag]`：'JSON'
      > - `Math[Symbol.toStringTag]`：'Math'
      > - Module 对象`M[Symbol.toStringTag]`：'Module'
      > - `ArrayBuffer.prototype[Symbol.toStringTag]`：'ArrayBuffer'
      > - `DataView.prototype[Symbol.toStringTag]`：'DataView'
      > - `Map.prototype[Symbol.toStringTag]`：'Map'
      > - `Promise.prototype[Symbol.toStringTag]`：'Promise'
      > - `Set.prototype[Symbol.toStringTag]`：'Set'
      > - `%TypedArray%.prototype[Symbol.toStringTag]`：'Uint8Array'等
      > - `WeakMap.prototype[Symbol.toStringTag]`：'WeakMap'
      > - `WeakSet.prototype[Symbol.toStringTag]`：'WeakSet'
      > - `%MapIteratorPrototype%[Symbol.toStringTag]`：'Map Iterator'
      > - `%SetIteratorPrototype%[Symbol.toStringTag]`：'Set Iterator'
      > - `%StringIteratorPrototype%[Symbol.toStringTag]`：'String Iterator'
      > - `Symbol.prototype[Symbol.toStringTag]`：'Symbol'
      > - `Generator.prototype[Symbol.toStringTag]`：'Generator'
      > - `GeneratorFunction.prototype[Symbol.toStringTag]`：'GeneratorFunction'

    - ~~（了解即可，我们不用`with`语法）`Symbol.unscopables`：对象的`Symbol.unscopables`属性，指向一个对象。该对象指定了使用`with`关键字时，哪些属性会被`with`环境排除。~~

      ```js
      Array.prototype[Symbol.unscopables]
      // {
      //   copyWithin: true,
      //   entries: true,
      //   fill: true,
      //   find: true,
      //   findIndex: true,
      //   includes: true,
      //   keys: true
      // }
      
      Object.keys(Array.prototype[Symbol.unscopables])
      // ['copyWithin', 'entries', 'fill', 'find', 'findIndex', 'includes', 'keys']
      ```

      上面代码说明，数组有 7 个属性，会被`with`命令排除。

      ```js
      // 没有 unscopables 时
      class MyClass {
        foo() { return 1; }
      }
      
      var foo = function () { return 2; };
      
      with (MyClass.prototype) {
        foo(); // 1
      }
      
      // 有 unscopables 时
      class MyClass {
        foo() { return 1; }
        get [Symbol.unscopables]() {
          return { foo: true };
        }
      }
      
      var foo = function () { return 2; };
      
      with (MyClass.prototype) {
        foo(); // 2
      }
      ```

      上面代码通过指定`Symbol.unscopables`属性，使得`with`语法块不会在当前作用域寻找`foo`属性，即`foo`将指向外层作用域的变量。

- ## Set 和 Map 数据结构

  JS 中的集合有两种：Set和Map。集合不同于数组，JS 中的集合是**有序、不可重复、无下标**的容器。

  - #### Set

    - ##### 基本用法：

      ES6 提供了新的数据结构 Set。它类似于数组，但是没有下标，且成员的值都是唯一的。并且`Set`是有序的，它的遍历顺序就是插入顺序。这点和数组一样。

      `Set`本身是一个构造函数，用来生成 Set 集合。

      ```js
      const s = new Set();
      
      [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));
      
      for (let i of s) {
        console.log(i);
      }
      // 2 3 5 4
      ```

      上面代码通过`add()`方法向 Set 结构加入成员，结果表明 Set 结构不会添加重复的值。

      `Set()`函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化。

      ```js
      // 例一
      const set = new Set([1, 2, 3, 4, 4]);
      [...set]
      // [1, 2, 3, 4]
      
      // 例二
      const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
      items.size // 5
      
      // 例三
      const set = new Set(document.querySelectorAll('div'));
      set.size // 56
      
      // 类似于
      const set = new Set();
      document
       .querySelectorAll('div')
       .forEach(div => set.add(div));
      set.size // 56
      ```

      上面代码中，例一和例二都是`Set`函数接受数组作为参数，例三是接受类似数组的对象作为参数。

      上面代码也展示了一种去除数组重复成员的方法。

      ```js
      // 去除数组的重复成员
      [...new Set(array)]
      ```

      上面的方法也可以用于，去除字符串里面的重复字符。

      ```js
      [...new Set('ababbc')].join('')  // "abc"
      ```

      向 Set 加入值的时候，不会发生类型转换，所以`5`和`"5"`是两个不同的值。Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，它类似于精确相等运算符（`===`），主要的区别是向 Set 加入值时认为`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身。

      ```js
      let set = new Set();
      let a = NaN;
      let b = NaN;
      set.add(a);
      set.add(b);
      set // Set {NaN}
      ```

      上面代码向 Set 实例添加了两次`NaN`，但是只会加入一个。这表明，在 Set 内部，两个`NaN`是相等的。

      另外，两个对象总是不相等的。

      ```js
      let set = new Set();
      
      set.add({});
      set.size // 1
      
      set.add({});
      set.size // 2
      ```

      上面代码表示，由于两个空对象不相等，所以它们被视为两个值。

      `Array.from()`方法可以将 Set 结构转为数组。

      ```js
      const items = new Set([1, 2, 3, 4, 5]);
      const array = Array.from(items);
      ```

      这就提供了去除数组重复成员的另一种方法。

      ```js
      function dedupe(array) {
        return Array.from(new Set(array));
      }
      
      dedupe([1, 1, 2, 3]) // [1, 2, 3]
      ```

    - ##### Set 实例的属性和方法：

      Set 有以下实例属性：

      - `Set.prototype.size`：返回`Set`实例的成员总数。

      Set 的实例方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）。下面先介绍四个操作方法。

      - `Set.prototype.add(value)`：添加某个值，返回 Set 结构本身。
      - `Set.prototype.delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
      - `Set.prototype.has(value)`：返回一个布尔值，表示该值是否为`Set`的成员。
      - `Set.prototype.clear()`：清除所有成员，没有返回值。

      上面这些属性和方法的实例如下。

      ```js
      s.add(1).add(2).add(2);
      // 注意2被加入了两次
      
      s.size // 2
      
      s.has(1) // true
      s.has(2) // true
      s.has(3) // false
      
      s.delete(2) // true
      s.has(2) // false
      ```

    - ##### 遍历方法：

      Set 结构的实例有四个遍历方法，可以用于遍历成员。

      需要特别指出的是，`Set`是有序的，它的遍历顺序就是插入顺序。这个特性有时非常有用，比如使用 Set 保存一个回调函数列表，调用时就能保证按照添加顺序调用。
  
      1. ###### `keys()`，`values()`，`entries()`：
  
         `keys`方法、`values`方法、`entries`方法返回的都是迭代器对象（详见《Iterator 对象》一章）。由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以`keys`方法和`values`方法的行为完全一致。
  
         ```js
         let set = new Set(['red', 'green', 'blue']);
         
         for (let item of set.keys()) {
           console.log(item);
         }
         // red
         // green
         // blue
         
         for (let item of set.values()) {
           console.log(item);
         }
         // red
         // green
         // blue
         
         for (let item of set.entries()) {
           console.log(item);
         }
         // ["red", "red"]
         // ["green", "green"]
         // ["blue", "blue"]
         ```
  
         上面代码中，`entries`方法返回的迭代器，同时包括键名和键值，所以每次输出一个数组，它的两个成员完全相等。
  
         Set 实现了`Iterator`接口，它的迭代器函数就是它的`values`方法。
  
         ```js
         Set.prototype[Symbol.iterator] === Set.prototype.values  // true
         ```
  
         这意味着，可以直接用`for...of`循环来遍历 Set。
  
         ```js
         let set = new Set(['red', 'green', 'blue']);
         
         for (let x of set) {
           console.log(x);
         }
         // red
         // green
         // blue
         ```
  
      2. ###### `forEach()`：
  
         Set 集合与数组一样，也拥有`forEach`方法，用于对每个成员执行某种操作，没有返回值。
  
         ```js
         let set = new Set([1, 4, 9]);
         set.forEach((value, key, set) => console.log(key + ' : ' + value))
         // 1 : 1
         // 4 : 4
         // 9 : 9
         ```
  
         上面代码说明，`forEach`方法的参数就是一个处理函数。该函数的参数与数组的`forEach`一致，依次为键值、键名、集合本身（上例省略了该参数）。这里需要注意，Set 结构的键名就是键值（两者是同一个值），因此第一个参数与第二个参数的值永远都是一样的。
  
         另外，`forEach`方法同样可以有第二个参数，表示绑定处理函数内部的`this`对象。
  
      3. ###### 遍历的应用：
  
         由于 Set 是可迭代的结构，因此扩展运算符（`...`）也可以用于 Set。
  
         ```js
         let set = new Set(['red', 'green', 'blue']);
         let arr = [...set];
         // ['red', 'green', 'blue']
         ```
  
         扩展运算符和 Set 结构相结合，就可以去除数组的重复成员。
  
         ```js
         let arr = [3, 5, 2, 2, 5, 5];
         let unique = [...new Set(arr)];
         // [3, 5, 2]
         ```
  
         而且，数组的`map`和`filter`方法也可以间接用于 Set 了。
  
         ```js
         let set = new Set([1, 2, 3]);
         set = new Set([...set].map(x => x * 2));
         // 返回Set结构：{2, 4, 6}
         
         let set = new Set([1, 2, 3, 4, 5]);
         set = new Set([...set].filter(x => (x % 2) == 0));
         // 返回Set结构：{2, 4}
         ```
  
         因此使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。
  
         ```js
         let a = new Set([1, 2, 3]);
         let b = new Set([4, 3, 2]);
         
         // 并集
         let union = new Set([...a, ...b]);
         // Set {1, 2, 3, 4}
         
         // 交集
         let intersect = new Set([...a].filter(x => b.has(x)));
         // set {2, 3}
         
         // （a 相对于 b 的）差集
         let difference = new Set([...a].filter(x => !b.has(x)));
         // Set {1}
         ```
  
         如果想在遍历操作中，同步改变原来的 Set 结构，目前没有直接的方法，但有两种变通方法。一种是利用原 Set 结构映射出一个新的结构，然后赋值给原来的 Set 结构；另一种是利用`Array.from`方法。
  
         ```js
         // 方法一
         let set = new Set([1, 2, 3]);
         set = new Set([...set].map(val => val * 2));
         // set的值是2, 4, 6
         
         // 方法二
         let set = new Set([1, 2, 3]);
         set = new Set(Array.from(set, val => val * 2));
         // set的值是2, 4, 6
         ```
  
         上面代码提供了两种方法，直接在遍历操作中改变原来的 Set 结构。
  
    - ##### 集合运算：
  
      [ES2025](https://github.com/tc39/proposal-set-methods) 为 Set 集合添加了以下7个集合运算的实例方法：
      
      - `Set.prototype.intersection(other)`：交集
      - `Set.prototype.union(other)`：并集
      - `Set.prototype.difference(other)`：差集
      - `Set.prototype.symmetricDifference(other)`：对称差集
      - `Set.prototype.isSubsetOf(other)`：判断是否为子集
      - `Set.prototype.isSupersetOf(other)`：判断是否为超集
      - `Set.prototype.isDisjointFrom(other)`：判断是否不相交
      
      以上方法的参数都必须是 Set 结构，或者是一个类似于 Set 的结构（拥有`size`属性，以及`keys()`和`has()`方法）。
  
      `.union()`是并集运算，返回包含两个集合中存在的所有成员的集合。
  
      ```js
      const frontEnd = new Set(["JS", "HTML", "CSS"]);
      const backEnd = new Set(["Python", "Java", "JS"]);
      
      const all = frontEnd.union(backEnd);
      // Set {"JS", "HTML", "CSS", "Python", "Java"}
      ```
  
      `.intersection()`是交集运算，返回同时包含在两个集合中的成员的集合。
  
      ```js
      const frontEnd = new Set(["JS", "HTML", "CSS"]);
      const backEnd = new Set(["Python", "Java", "JS"]);
      
      const frontAndBackEnd = frontEnd.intersection(backEnd);
      // Set {"JS"}
      ```
  
      `.difference()`是差集运算，返回第一个集合中存在但第二个集合中不存在的所有成员的集合。
  
      ```js
      const frontEnd = new Set(["JS", "HTML", "CSS"]);
      const backEnd = new Set(["Python", "Java", "JS"]);
      
      const onlyFrontEnd = frontEnd.difference(backEnd);
      // Set {"HTML", "CSS"}
      
      const onlyBackEnd = backEnd.difference(frontEnd);
      // Set {"Python", "Java"}
      ```
  
      `.symmetryDifference()`是对称差集，返回两个集合的所有独一无二成员的集合，即去除了重复的成员。
  
      ```js
      const frontEnd = new Set(["JS", "HTML", "CSS"]);
      const backEnd = new Set(["Python", "Java", "JS"]);
      
      const onlyFrontEnd = frontEnd.symmetricDifference(backEnd);
      // Set {"HTML", "CSS", "Python", "Java"} 
      
      const onlyBackEnd = backEnd.symmetricDifference(frontEnd);
      // Set {"Python", "Java", "HTML", "CSS"}
      ```
  
      注意，返回结果中的成员顺序，由添加到集合的顺序决定。
  
      `.isSubsetOf()`返回一个布尔值，判断第一个集合是否为第二个集合的子集，即第一个集合的所有成员都是第二个集合的成员。
  
      ```js
      const frontEnd = new Set(["JS", "HTML", "CSS"]);
      const declarative = new Set(["HTML", "CSS"]);
      
      declarative.isSubsetOf(frontEnd);  // true
      
      frontEndLanguages.isSubsetOf(declarativeLanguages);  // false
      ```
  
      任何集合都是自身的子集。
  
      ```js
      frontEnd.isSubsetOf(frontEnd);  // true
      ```
  
      `isSupersetOf()`返回一个布尔值，表示第一个集合是否为第二个集合的超集，即第二个集合的所有成员都是第一个集合的成员。
  
      ```js
      const frontEnd = new Set(["JS", "HTML", "CSS"]);
      const declarative = new Set(["HTML", "CSS"]);
      
      declarative.isSupersetOf(frontEnd);  // false
      
      frontEnd.isSupersetOf(declarative);  // true
      ```
  
      任何集合都是自身的超集。
  
      ```js
      frontEnd.isSupersetOf(frontEnd);  // true
      ```
  
      `.isDisjointFrom()`判断两个集合是否不相交，即没有共同成员。
  
      ```js
      const frontEnd = new Set(["JS", "HTML", "CSS"]);
      const interpreted = new Set(["JS", "Ruby", "Python"]);
      const compiled = new Set(["Java", "C++", "TypeScript"]);
      
      interpreted.isDisjointFrom(compiled);  // true
      
      frontEnd.isDisjointFrom(interpreted);  // false
      ```
  
  - #### `WeakSet`
  
    - ##### 含义：
  
      WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别：
  
      1. `WeakSet` 的成员只能是对象和 `Symbol` 值，而不能是其他类型的值。
  
         ```js
         const ws = new WeakSet();
         ws.add(1) // 报错
         ws.add(Symbol()) // 不报错
         ```
  
         > 以上试图向 `WeakSet` 添加一个数值和`Symbol`值，结果前者报错了，因为 `WeakSet` 只能放置对象和 Symbol 值。
  
      2. 其次，`WeakSet` 中的对象都是弱引用，即垃圾回收机制不考虑 `WeakSet` 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 `WeakSet` 之中。
  
         > 这是因为垃圾回收机制根据对象的可达性（reachability）来判断回收，如果对象还能被访问到，垃圾回收机制就不会释放这块内存。结束使用该值之后，有时会忘记取消引用，导致内存无法释放，进而可能会引发内存泄漏。WeakSet 里面的引用，都不计入垃圾回收机制，所以就不存在这个问题。因此，WeakSet 适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在 WeakSet 里面的引用就会自动消失。
         >
         > 由于上面这个特点，WeakSet 的成员是不适合引用的，因为它会随时消失。另外，由于 WeakSet 内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 **WeakSet 不可遍历**。
  
      这些特点同样适用于本章后面要介绍的 WeakMap 结构。
  
    - ##### 语法：
  
      WeakSet 是一个构造函数，可以使用`new`命令，创建 WeakSet 数据结构。
  
      ```js
      const ws = new WeakSet();
      ```
  
      作为构造函数，WeakSet 可以接受一个数组或类似数组的对象作为参数（实际上，任何具有 Iterable 接口的对象，都可以作为 WeakSet 的参数）。该数组的所有成员，都会自动成为 WeakSet 实例对象的成员。
  
      ```js
      const a = [[1, 2], [3, 4]];
      const ws = new WeakSet(a);
      // WeakSet {[1, 2], [3, 4]}
      ```
  
      上面代码中，`a`是一个数组，它有两个成员，也都是数组。将`a`作为 WeakSet 构造函数的参数，`a`的成员会自动成为 WeakSet 的成员。
  
      注意，是`a`数组的成员成为 WeakSet 的成员，而不是`a`数组本身。这意味着，参数数组的成员只能是对象和 Symbol 值。
  
      ```js
      const b = [3, 4];
      const ws = new WeakSet(b);  // Uncaught TypeError: Invalid value used in weak set(…)
      ```
  
      上面代码中，数组`b`的成员不是对象，加入 WeakSet 就会报错。
  
      WeakSet 结构有以下三个实例方法：
  
      - `WeakSet.prototype.add(value)`：向 WeakSet 实例添加一个新成员，返回 WeakSet 结构本身。
      - `WeakSet.prototype.delete(value)`：清除 WeakSet 实例的指定成员，清除成功返回`true`，如果在 WeakSet 中找不到该成员或该成员不是对象，返回`false`。
      - `WeakSet.prototype.has(value)`：返回一个布尔值，表示某个值是否在 WeakSet 实例之中。
  
      下面是一个例子。
  
      ```js
      const ws = new WeakSet();
      const obj = {};
      const foo = {};
      
      ws.add(window);
      ws.add(obj);
      
      ws.has(window); // true
      ws.has(foo); // false
      
      ws.delete(window); // true
      ws.has(window); // false
      ```
  
      **WeakSet 没有`size`属性和`clear()`方法，并且它是不可被遍历的**。
  
      ```js
      ws.size // undefined
      ws.forEach // undefined
      ws.clear // undefined
      
      ws.forEach(function(item){ console.log('WeakSet has ' + item)})
      // TypeError: undefined is not a function
      ```
      
      上面代码试图获取`size`、`clear`和`forEach`属性，结果都不能成功。
      
      WeakSet 不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。**WeakSet 的一个用处是，储存 DOM 节点**。这样就不用担心这些节点从文档移除时，会引发内存泄漏。
      
      下面是 WeakSet 的另一个例子。
      
      ```js
      const foos = new WeakSet()
      class Foo {
        constructor() {
          foos.add(this)
        }
        method () {
          if (!foos.has(this)) {
            throw new TypeError('Foo.prototype.method 只能在Foo的实例上调用！');
          }
        }
      }
      ```
      
      上面代码保证了`Foo`的实例方法，只能在`Foo`的实例上调用。这里使用 WeakSet 的好处是，`foos`对实例的引用，不会被计入内存回收机制，所以删除实例的时候，不用考虑`foos`，也不会出现内存泄漏。
  
  - #### `Map`
  
    - ##### 含义和基本用法：
  
      JS 的对象本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。
  
      ```js
      const data = {};
      const element = document.getElementById('myDiv');
      
      data[element] = 'metadata';
      data['[object HTMLDivElement]'] // "metadata"
      ```
  
      上面代码原意是将一个 DOM 节点作为对象`data`的键，但是由于对象只接受字符串作为键名，所以`element`被自动转为字符串`[object HTMLDivElement]`。
  
      为了解决这个问题，ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应，是一种更完善的 Hash 结构实现。如果你需要“键值对”的数据结构，Map 比 Object 更合适。
  
      ```js
      const m = new Map();
      const o = {p: 'Hello World'};
      
      m.set(o, 'content')
      m.get(o) // "content"
      
      m.has(o) // true
      m.delete(o) // true
      m.has(o) // false
      ```
  
      上面代码使用 Map 结构的`set`方法，将对象`o`当作`m`的一个键，然后又使用`get`方法读取这个键，接着使用`delete`方法删除了这个键。
  
      上面的例子展示了如何向 Map 添加成员。作为构造函数，Map 也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。
  
      ```js
      const map = new Map([
        ['name', '张三'],
        ['title', 'Author']
      ]);
      
      map.size // 2
      map.has('name') // true
      map.get('name') // "张三"
      map.has('title') // true
      map.get('title') // "Author"
      ```
  
      上面代码在新建 Map 实例时，就指定了两个键`name`和`title`。
  
      `Map`构造函数接受数组作为参数，实际上执行的是下面的算法。
  
      ```js
      const items = [
        ['name', '张三'],
        ['title', 'Author']
      ];
      
      const map = new Map();
      
      items.forEach(
        ([key, value]) => map.set(key, value)
      );
      ```
  
      事实上，不仅仅是数组，任何具有 Iterator 接口、且每个成员都是一个双元素的数组的数据结构（详见《Iterator》一章）都可以当作`Map`构造函数的参数。这就是说，`Set`和`Map`都可以用来生成新的 Map。
  
      ```js
      const set = new Set([
        ['foo', 1],
        ['bar', 2]
      ]);
      const m1 = new Map(set);
      m1.get('foo') // 1
      
      const m2 = new Map([['baz', 3]]);
      const m3 = new Map(m2);
      m3.get('baz') // 3
      ```
  
      上面代码中，我们分别使用 Set 对象和 Map 对象，当作`Map`构造函数的参数，结果都生成了新的 Map 对象。
  
      如果对同一个键多次赋值，后面的值将覆盖前面的值。
  
      ```js
      const map = new Map();
      
      map
        .set(1, 'aaa')
        .set(1, 'bbb');
      
      map.get(1) // "bbb"
      ```
  
      上面代码对键`1`连续赋值两次，后一次的值覆盖前一次的值。
  
      如果读取一个 Map 中不存在的键，则返回`undefined`。
  
      ```js
      new Map().get('asfddfsasadf')  // undefined
      ```
  
      注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。这一点要非常小心。
  
      ```js
      const map = new Map();
      
      map.set(['a'], 555);
      map.get(['a']) // undefined
      ```
  
      上面代码的`set`和`get`方法，表面是针对同一个键，但实际上这是两个不同的数组实例，内存地址是不一样的，因此`get`方法无法读取该键，返回`undefined`。
  
      同理，同样的值的两个实例，在 Map 结构中被视为两个键。
  
      ```js
      const map = new Map();
      
      const k1 = ['a'];
      const k2 = ['a'];
      
      map
        .set(k1, 111)
        .set(k2, 222);
      
      map.get(k1) // 111
      map.get(k2) // 222
      ```
  
      上面代码中，变量`k1`和`k2`的值是一样的，但是它们在 Map 结构中被视为两个键。
  
      由上可知，Map 的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键。这就解决了同名属性碰撞（clash）的问题，我们扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名。
  
      Map 内部判断两个键相等与否和 Set 是一样的，都是用的“Same-value-zero equality”。它类似于精确相等运算符（`===`），主要的区别是向 Map 加入键时认为`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身。
  
      ```js
      let map = new Map();
      
      map.set(-0, 123);
      map.get(+0) // 123
      
      map.set(true, 1);
      map.set('true', 2);
      map.get(true) // 1
      
      map.set(undefined, 3);
      map.set(null, 4);
      map.get(undefined) // 3
      
      map.set(NaN, 123);
      map.get(NaN) // 123
      ```
  
    - ##### 实例的属性和操作方法：
  
      Map 结构的实例有以下属性和操作方法。
  
      - `size`属性：返回 Map 结构的成员总数。
  
        ```js
        const map = new Map();
        map.set('foo', true);
        map.set('bar', false);
        
        map.size // 2
        ```
  
      - `set(key, value)`：设置键名`key`对应的键值为`value`，然后返回整个 Map 结构。如果`key`已经有值，则键值会被更新，否则就新生成该键。
  
        ```js
        const m = new Map();
        
        m.set('edition', 6)        // 键是字符串
        m.set(262, 'standard')     // 键是数值
        m.set(undefined, 'nah')    // 键是 undefined
        ```
  
        `set`方法返回的是当前的`Map`对象，因此可以采用链式写法。
  
        ```js
        let map = new Map()
          .set(1, 'a')
          .set(2, 'b')
          .set(3, 'c');
        ```
  
      - `get(key)`：读取`key`对应的键值，如果找不到`key`，返回`undefined`。
  
        ```js
        const m = new Map();
        
        const hello = function() {console.log('hello');};
        m.set(hello, 'Hello ES6!') // 键是函数
        
        m.get(hello)  // Hello ES6!
        ```
  
      - `has(key)`：返回一个布尔值，表示某个键是否在当前 Map 对象之中。
  
        ```js
        const m = new Map();
        
        m.set('edition', 6);
        m.set(262, 'standard');
        m.set(undefined, 'nah');
        
        m.has('edition')     // true
        m.has('years')       // false
        m.has(262)           // true
        m.has(undefined)     // true
        ```
  
      - `delete(key)`：删除某个键。删除成功返回`true`，反之返回`false`。
  
        ```js
        const m = new Map();
        m.set(undefined, 'nah');
        m.has(undefined)     // true
        
        m.delete(undefined)
        m.has(undefined)       // false
        ```
  
      - `clear()`：删除Map集合中的所有成员，没有返回值。
  
        ```js
        let map = new Map();
        map.set('foo', true);
        map.set('bar', false);
        
        map.size // 2
        map.clear()
        map.size // 0
        ```
  
    - ##### 遍历方法
  
      和 Set 集合类似，Map 集合也提供了三个迭代器生成函数和一个遍历方法。
  
      - `Map.prototype.keys()`：返回键名的迭代器。
      - `Map.prototype.values()`：返回键值的迭代器。
      - `Map.prototype.entries()`：返回所有成员的迭代器。
      - `Map.prototype.forEach()`：遍历 Map 的所有成员。
  
      并且，**Map 集合也是有序的，它的遍历顺序就是插入顺序。**
  
      ```js
      const map = new Map([
        ['F', 'no'],
        ['T',  'yes'],
      ]);
      
      for (let key of map.keys()) {
        console.log(key);
      }
      // "F"
      // "T"
      
      for (let value of map.values()) {
        console.log(value);
      }
      // "no"
      // "yes"
      
      for (let item of map.entries()) {
        console.log(item[0], item[1]);
      }
      // "F" "no"
      // "T" "yes"
      
      // 或者
      for (let [key, value] of map.entries()) {
        console.log(key, value);
      }
      // "F" "no"
      // "T" "yes"
      
      // 等同于使用map.entries()
      for (let [key, value] of map) {
        console.log(key, value);
      }
      // "F" "no"
      // "T" "yes"
      ```
  
      上面代码最后的那个例子，表示 Map 结构的默认迭代器接口（`Symbol.iterator`属性），就是`entries`方法。
  
      ```js
      map[Symbol.iterator] === map.entries  // true
      ```
      
      Map 结构转为数组结构，比较快速的方法是使用扩展运算符（`...`）。
      
      ```js
      const map = new Map([
        [1, 'one'],
        [2, 'two'],
        [3, 'three'],
      ]);
      
      [...map.keys()]
      // [1, 2, 3]
      
      [...map.values()]
      // ['one', 'two', 'three']
      
      [...map.entries()]
      // [[1,'one'], [2, 'two'], [3, 'three']]
      
      [...map]
      // [[1,'one'], [2, 'two'], [3, 'three']]
      ```
      
      结合数组的`map`方法、`filter`方法，可以实现 Map 的遍历和过滤（Map 本身没有`map`和`filter`方法）。
      
      ```js
      const map0 = new Map()
        .set(1, 'a')
        .set(2, 'b')
        .set(3, 'c');
      
      const map1 = new Map(
        [...map0].filter(([k, v]) => k < 3)
      );
      // 产生 Map 结构 {1 => 'a', 2 => 'b'}
      
      const map2 = new Map(
        [...map0].map(([k, v]) => [k * 2, '_' + v])
          );
      // 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}
      ```
      
      此外，Map 还有一个`forEach`方法，与数组的`forEach`方法类似，也可以实现遍历。
      
      ```js
      map.forEach(function(value, key, map) {
        console.log("Key: %s, Value: %s", key, value);
      });
      ```
      
      Map 的`forEach`方法同样可以接受第二个参数，用来绑定`this`。
      
      ```js
      const reporter = {
        report: function(key, value) {
          console.log("Key: %s, Value: %s", key, value);
        }
      };
      
      map.forEach(function(value, key, map) {
        this.report(key, value);
      }, reporter);
      ```
      
      上面代码中，`forEach`方法的回调函数的`this`，就指向`reporter`。
      
    - ##### 与其他数据结构的互相转换
  
      1. ###### Map 转为数组：
  
        前面已经提过，Map 转为数组最方便的方法，就是使用扩展运算符（`...`）。
  
        ```js
        const myMap = new Map()
          .set(true, 7)
          .set({foo: 3}, ['abc']);
        [...myMap]
        // [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
        ```
  
      2. 数组转为 Map：
  
        将数组传入 Map 构造函数，就可以转为 Map。
  
        ```js
        new Map([
          [true, 7],
          [{foo: 3}, ['abc']]
        ])
        // Map {
        //   true => 7,
        //   Object {foo: 3} => ['abc']
        // }
        ```
  
      3. ###### Map 转为对象：
  
        如果所有 Map 的键都是字符串，它可以无损地转为对象。
  
        ```js
        function strMapToObj(strMap) {
          let obj = Object.create(null);
          for (let [k,v] of strMap) {
            obj[k] = v;
          }
          return obj;
        }
        
        const myMap = new Map()
          .set('yes', true)
          .set('no', false);
        strMapToObj(myMap)
        // { yes: true, no: false }
        ```
  
        如果有非字符串的键名，那么这个键名会被转成字符串，再作为对象的键名。
  
      4. ###### 对象转为 Map：
  
        对象转为 Map 可以通过`Object.entries()`。
  
        ```js
        let obj = {"a":1, "b":2};
        let map = new Map(Object.entries(obj));
        ```
  
        此外，也可以自己实现一个转换函数。
  
        ```js
        function objToStrMap(obj) {
          let strMap = new Map();
          for (let k of Object.keys(obj)) {
            strMap.set(k, obj[k]);
          }
          return strMap;
        }
        
        objToStrMap({yes: true, no: false})
        // Map {"yes" => true, "no" => false}
        ```
  
      5. ###### Map 转为 JSON：
  
        Map 转为 JSON 要区分两种情况。一种情况是，Map 的键名都是字符串，这时可以选择转为对象 JSON。
  
        ```js
        function strMapToJson(strMap) {
          return JSON.stringify(strMapToObj(strMap));
        }
        
        let myMap = new Map().set('yes', true).set('no', false);
        strMapToJson(myMap)
        // '{"yes":true,"no":false}'
        ```
  
        另一种情况是，Map 的键名有非字符串，这时可以选择转为数组 JSON。
  
        ```js
        function mapToArrayJson(map) {
          return JSON.stringify([...map]);
        }
        
        let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
        mapToArrayJson(myMap)
        // '[[true,7],[{"foo":3},["abc"]]]'
        ```
  
      6. ###### JSON 转为 Map：
  
        JSON 转为 Map，正常情况下，所有键名都是字符串。
  
        ```js
        function jsonToStrMap(jsonStr) {
          return objToStrMap(JSON.parse(jsonStr));
        }
        
        jsonToStrMap('{"yes": true, "no": false}')
        // Map {'yes' => true, 'no' => false}
        ```
  
        但是，有一种特殊情况，整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为 Map。这往往是 Map 转为数组 JSON 的逆操作。
  
        ```js
        function jsonToMap(jsonStr) {
          return new Map(JSON.parse(jsonStr));
        }
        
        jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
        // Map {true => 7, Object {foo: 3} => ['abc']}
        ```
  
  - #### `WeakMap`
  
    - ##### 含义：
  
      `WeakMap`结构与`Map`结构类似，也是用于生成键值对的集合。
  
      ```js
      // WeakMap 可以使用 set 方法添加成员
      const wm1 = new WeakMap();
      const key = {foo: 1};
      wm1.set(key, 2);
      wm1.get(key) // 2
      
      // WeakMap 也可以接受一个数组，
      // 作为构造函数的参数
      const k1 = [1, 2, 3];
      const k2 = [4, 5, 6];
      const wm2 = new WeakMap([[k1, 'foo'], [k2, 'bar']]);
      wm2.get(k2) // "bar"
      ```
  
      类似于`WeakSet`，`WeakMap`与`Map`的区别同样是两点：
  
      1. `WeakMap`只接受对象和 Symbol 值作为键名，不接受其他类型的值作为键名。
  
         ```js
         const map = new WeakMap();
         map.set(1, 2) // 报错
         map.set(null, 2) // 报错
         map.set(Symbol(), 2) // 不报错
         ```
  
         > 上面代码中，如果将数值`1`和`null`作为 WeakMap 的键名，都会报错，将 Symbol 值作为键名不会报错。
  
      2. 其次，`WeakMap`的**键名**所指向的对象，不计入垃圾回收机制。
  
      `WeakMap`的设计目的在于，有时我们想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。请看下面的例子。
  
      ```js
      const e1 = document.getElementById('foo');
      const e2 = document.getElementById('bar');
      const arr = [
        [e1, 'foo 元素'],
        [e2, 'bar 元素'],
      ];
      ```
  
      上面代码中，`e1`和`e2`是两个对象，我们通过`arr`数组对这两个对象添加一些文字说明。这就形成了`arr`对`e1`和`e2`的引用。
  
      一旦不再需要这两个对象，我们就必须手动删除这个引用，否则垃圾回收机制就不会释放`e1`和`e2`占用的内存。
  
      ```js
      // 不需要 e1 和 e2 的时候
      // 必须手动删除引用
      arr [0] = null;
      arr [1] = null;
      ```
  
      上面这样的写法显然很不方便。一旦忘了写，就会造成内存泄露。
  
      WeakMap 就是为了解决这个问题而诞生的，它的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。
  
      基本上，如果你要往对象上添加数据，又不想干扰垃圾回收机制，就可以使用 WeakMap。一个典型应用场景是，在网页的 DOM 元素上添加数据，就可以使用`WeakMap`结构。当该 DOM 元素被清除，其所对应的`WeakMap`记录就会自动被移除。
  
      ```js
      const wm = new WeakMap();
      
      const element = document.getElementById('example');
      
      wm.set(element, 'some information');
      wm.get(element) // "some information"
      ```
  
      上面代码中，先新建一个 WeakMap 实例。然后，将一个 DOM 节点作为键名存入该实例，并将一些附加信息作为键值，一起存放在 WeakMap 里面。这时，WeakMap 里面对`element`的引用就是弱引用，不会被计入垃圾回收机制。
  
      也就是说，上面的 DOM 节点对象除了 WeakMap 的弱引用外，其他位置对该对象的引用一旦消除，该对象占用的内存就会被垃圾回收机制释放。WeakMap 保存的这个键值对，也会自动消失。
  
      总之，`WeakMap`的专用场合就是，它的键所对应的对象，可能会在将来消失。`WeakMap`结构有助于防止内存泄漏。
  
      注意，**WeakMap 弱引用的只是键名，而不是键值。键值依然是正常引用**。
  
      ```js
      const wm = new WeakMap();
      let key = {};
      let obj = {foo: 1};
      
      wm.set(key, obj);
      obj = null;
      wm.get(key)
      // Object {foo: 1}
      ```
  
      上面代码中，键值`obj`是正常引用。所以，即使在 WeakMap 外部消除了`obj`的引用，WeakMap 内部的引用依然存在。
  
    - ##### `WeakMap` 的语法：
  
      WeakMap 与 Map 在 API 上的区别主要是两个，一是没有遍历操作（即没有`keys()`、`values()`和`entries()`方法），也没有`size`属性。因为没有办法列出所有键名，某个键名是否存在完全不可预测，跟垃圾回收机制是否运行相关。这一刻可以取到键名，下一刻垃圾回收机制突然运行了，这个键名就没了，为了防止出现不确定性，就统一规定不能取到键名。二是无法清空，即不支持`clear`方法。因此，`WeakMap`只有四个方法可用：`get()`、`set()`、`has()`、`delete()`。
  
      ```js
      const wm = new WeakMap();
      
      // size、forEach、clear 方法都不存在
      wm.size // undefined
      wm.forEach // undefined
      wm.clear // undefined
      ```
  
    - ##### （了解）`WeakMap` 的示例：
  
      WeakMap 的例子很难演示，因为无法观察它里面的引用会自动消失。此时，其他引用都解除了，已经没有引用指向 WeakMap 的键名了，导致无法证实那个键名是不是存在。
  
      根据贺师俊老师[提示](https://github.com/ruanyf/es6tutorial/issues/362#issuecomment-292109104)，如果引用所指向的值占用特别多的内存，就可以通过 Node 的`process.memoryUsage`方法看出来。根据这个思路，网友[vtxf](https://github.com/ruanyf/es6tutorial/issues/362#issuecomment-292451925)补充了下面的例子。
  
      首先，打开 Node 命令行。
  
      ```bash
      node --expose-gc
      ```
  
      上面代码中，`--expose-gc`参数表示允许手动执行垃圾回收机制。
  
      然后，执行下面的代码。
  
      ```js
      // 手动执行一次垃圾回收，保证获取的内存使用状态准确
      > global.gc();
      undefined
      
      // 查看内存占用的初始状态，heapUsed 为 4M 左右
      > process.memoryUsage();
      { rss: 21106688,
        heapTotal: 7376896,
        heapUsed: 4153936,
        external: 9059 }
      
      > let wm = new WeakMap();
      undefined
      
      // 新建一个变量 key，指向一个 5*1024*1024 的数组
      > let key = new Array(5 * 1024 * 1024);
      undefined
      
      // 设置 WeakMap 实例的键名，也指向 key 数组
      // 这时，key 数组实际被引用了两次，
      // 变量 key 引用一次，WeakMap 的键名引用了第二次
      // 但是，WeakMap 是弱引用，对于引擎来说，引用计数还是1
      > wm.set(key, 1);
      WeakMap {}
      
      > global.gc();
      undefined
      
      // 这时内存占用 heapUsed 增加到 45M 了
      > process.memoryUsage();
      { rss: 67538944,
        heapTotal: 7376896,
        heapUsed: 45782816,
        external: 8945 }
      
      // 清除变量 key 对数组的引用，
      // 但没有手动清除 WeakMap 实例的键名对数组的引用
      > key = null;
      null
      
      // 再次执行垃圾回收
      > global.gc();
      undefined
      
      // 内存占用 heapUsed 变回 4M 左右，
      // 可以看到 WeakMap 的键名引用没有阻止 gc 对内存的回收
      > process.memoryUsage();
      { rss: 20639744,
        heapTotal: 8425472,
        heapUsed: 3979792,
        external: 8956 }
      ```
  
      上面代码中，只要外部的引用消失，WeakMap 内部的引用，就会自动被垃圾回收清除。由此可见，有了 WeakMap 的帮助，解决内存泄漏就会简单很多。
  
      Chrome 浏览器的 Dev Tools 的 Memory 面板，有一个垃圾桶的按钮，可以强制垃圾回收（garbage collect）。这个按钮也能用来观察 WeakMap 里面的引用是否消失。
  
    - ##### `WeakMap` 的用途：
  
      前文说过，WeakMap 应用的典型场合就是 DOM 节点作为键名。下面是一个例子。
  
      ```js
      let myWeakmap = new WeakMap();
      
      myWeakmap.set(
        document.getElementById('logo'),
        {timesClicked: 0})
      ;
      
      document.getElementById('logo').addEventListener('click', function() {
        let logoData = myWeakmap.get(document.getElementById('logo'));
        logoData.timesClicked++;
      }, false);
      ```
  
      上面代码中，`document.getElementById('logo')`是一个 DOM 节点，每当发生`click`事件，就更新一下状态。我们将这个状态作为键值放在 WeakMap 里，对应的键名就是这个节点对象。一旦这个 DOM 节点删除，该状态就会自动消失，不存在内存泄漏风险。
  
      WeakMap 的另一个用处是部署私有属性。
  
      ```js
      const _counter = new WeakMap();
      const _action = new WeakMap();
      
      class Countdown {
        constructor(counter, action) {
          _counter.set(this, counter);
          _action.set(this, action);
        }
        dec() {
          let counter = _counter.get(this);
          if (counter < 1) return;
          counter--;
          _counter.set(this, counter);
          if (counter === 0) {
            _action.get(this)();
          }
        }
      }
      
      const c = new Countdown(2, () => console.log('DONE'));
      
      c.dec()
      c.dec()
      // DONE
      ```
  
      上面代码中，`Countdown`类的两个内部属性`_counter`和`_action`，是实例的弱引用，所以如果删除实例，它们也就随之消失，不会造成内存泄漏。
  
  - #### `WeakRef`
  
    WeakSet 和 WeakMap 是基于弱引用的数据结构，[ES2021](https://github.com/tc39/proposal-weakrefs) 更进一步，提供了 WeakRef 对象，用于直接创建对象的弱引用。
  
    ```js
    let target = {};
    let wr = new WeakRef(target);
    ```
  
    上面示例中，`target`是原始对象，构造函数`WeakRef()`创建了一个基于`target`的新对象`wr`。这里，`wr`就是一个 WeakRef 的实例，属于对`target`的弱引用，垃圾回收机制不会计入这个引用，也就是说，`wr`的引用不会妨碍原始对象`target`被垃圾回收机制清除。
  
    **WeakRef 实例对象有一个`deref()`方法，如果原始对象存在，该方法返回原始对象；如果原始对象已经被垃圾回收机制清除，该方法返回`undefined`。**
  
    ```js
    let target = {};
    let wr = new WeakRef(target);
    
    let obj = wr.deref();
    if (obj) { // 如果 target 未被垃圾回收机制清除
      // ...
    }
    ```
  
    上面示例中，**`deref()`方法可以判断原始对象是否已被清除。**
  
    **弱引用对象的一大用处，就是作为缓存，未被清除时可以从缓存取值，一旦清除缓存就自动失效**。
  
    ```js
    function makeWeakCached(f) {
      const cache = new Map();
      return key => {
        const ref = cache.get(key);
        if (ref) {
          const cached = ref.deref();
          if (cached !== undefined) return cached;
        }
    
        const fresh = f(key);
        cache.set(key, new WeakRef(fresh));
        return fresh;
      };
    }
    
    const getImageCached = makeWeakCached(getImage);
    ```
  
    上面示例中，`makeWeakCached()`用于建立一个缓存，缓存里面保存对原始文件的弱引用。
  
    标准规定，**一旦使用`WeakRef()`创建了原始对象的弱引用，那么在本轮事件循环（event loop），原始对象肯定不会被清除，只会在后面的事件循环才可能会被清除。**
  
  - #### `FinalizationRegistry`
  
    [ES2021](https://github.com/tc39/proposal-weakrefs#finalizers) 引入了 `FinalizationRegistry`，用来**指定目标对象被垃圾回收机制清除以后，所要执行的回调函数**。用法：
  
    1. 首先，新建一个注册表实例。
  
       ```js
       const registry = new FinalizationRegistry(heldValue => {
         // ....
       });
       ```
  
       上面代码中，**`FinalizationRegistry`是系统提供的构造函数，返回一个清理器注册表实例，里面登记了所要执行的回调函数。**回调函数作为`FinalizationRegistry()`的参数传入，它本身有一个参数`heldValue`。
  
    2. 然后，注册表实例的`register()`方法，用来注册所要观察的目标对象。
  
       ```js
       registry.register(theObject, "some value");
       ```
  
       上面示例中，`theObject`就是所要观察的目标对象，一旦该对象被垃圾回收机制清除，注册表就会在清除完成后，调用早前注册的回调函数，并将`some value`作为参数（前面的`heldValue`）传入回调函数。
  
       注意：
  
       - 如果浏览器窗口关闭或者进程意外退出，注册的回调函数就不会运行了。
       - **注册表不对目标对象`theObject`构成强引用，属于弱引用。**因为强引用的话，原始对象就不会被垃圾回收机制清除，这就失去使用注册表的意义了。
       - **回调函数的参数（`heldValue`）可以是任意类型的值**，字符串、数值、布尔值、对象，甚至可以是`undefined`。
  
    3. 最后，**如果以后还想取消已经注册的回调函数，则要向`register()`传入第三个参数，作为标记值**。
    
       这个标记值必须是对象，一般都用原始对象。接着，再使用注册表实例对象的`unregister()`方法，传入这个标记值来取消注册。（`registry()`的第3个和第1个参数可以不是同一个对象）
    
       ```js
       registry.register(theObject, "some value", theObject);  // 第3个和第1个参数可以不是同一个对象
       // ...其他操作...
       registry.unregister(theObject);
       ```
  
       上面代码中，`register()`方法的第三个参数就是标记值`theObject`。取消回调函数时，要使用`unregister()`方法，并将标记值作为该方法的参数。这里`register()`方法对第三个参数的引用，也属于弱引用。如果没有这个参数，则回调函数无法取消。
  
       注意：由于回调函数被调用以后，就不再存在于注册表之中了，所以执行`unregister()`应该是在回调函数还没被调用之前。
    
    下面使用`FinalizationRegistry`，对前一节的缓存函数进行增强。
    
    ```js
    function makeWeakCached(f) {
      const cache = new Map();
      const cleanup = new FinalizationRegistry(key => {
        const ref = cache.get(key);
        if (ref && !ref.deref()) cache.delete(key);
      });
    
      return key => {
        const ref = cache.get(key);
        if (ref) {
          const cached = ref.deref();
          if (cached !== undefined) return cached;
        }
    
        const fresh = f(key);
        cache.set(key, new WeakRef(fresh));
        cleanup.register(fresh, key);
        return fresh;
      };
    }
    
    const getImageCached = makeWeakCached(getImage);
    ```
  
    上面示例与前一节的例子相比，就是增加一个清理器注册表，一旦缓存的原始对象被垃圾回收机制清除，会自动执行一个回调函数。该回调函数会清除缓存里面已经失效的键。
    
    下面是另一个例子。
    
    ```js
    class Thingy {
      #file;
      #cleanup = file => {
        console.error(
          `The \`release\` method was never called for the \`Thingy\` for the file "${file.name}"`
        );
      };
      #registry = new FinalizationRegistry(this.#cleanup);
    
      constructor(filename) {
        this.#file = File.open(filename);
        this.#registry.register(this, this.#file, this.#file);
      }
    
      release() {
        if (this.#file) {
          this.#registry.unregister(this.#file);
          File.close(this.#file);
          this.#file = null;
        }
      }
    }
    ```
    
    上面示例中，如果由于某种原因，`Thingy`类的实例对象没有调用`release()`方法，就被垃圾回收机制清除了，那么清理器就会调用回调函数`#cleanup()`，输出一条错误信息。
    
    **由于无法知道清理器何时会执行，所以最好避免使用它**。另外，如果浏览器窗口关闭或者进程意外退出，清理器则不会运行。
  

------

