- ## 标准库

  - #### Object 对象

    JS 原生提供`Object`对象，JS 的所有其他对象都继承自`Object`对象，即那些对象都是`Object`的实例。
    
    在 JS  中，`new Object()` 和 `{}` 都可以用来创建一个新对象，**它们在本质上是相同的**，但存在一些细微的差异和使用场景的区别：
    
    1. 字面量方式 `{}` 创建对象是 JS 引擎直接优化的语法，无需调用构造函数，创建速度更快。`new Object()` 的性能稍低，需解析构造函数。并且代码还很冗余。
    2. 如果全局 `Object` 构造函数被修改，`new Object()` 的行为会受影响，而 `{}` 不受影响。
    
    因此**优先使用 `{}` 来创建对象**，更简洁、安全且性能更好。但其实两种方式在本质上是没有区别的。
  
    `Object`对象的原生方法分成两类：`Object`本身的静态方法与`Object`的实例方法。所谓“静态方法”就是直接定义在`Object`构造函数上的方法，通过`函数名.`来调用；所谓实例方法就是定义在`Object`原型对象`Object.prototype`上的方法，通过`实例.`调用，它可以被`Object`实例所共享。
  
    - ##### `Object()`：
    
      `Object`本身是一个函数，可以当作工具方法使用，将任意值转为对象。这个方法常用于保证某个值一定是对象。
    
      如果参数为空（或者为`undefined`和`null`），`Object()`返回一个空对象。
    
      如果参数是原始类型的值，`Object`方法将其转为对应的包装对象的实例。
    
      如果`Object`方法的参数是一个对象（包括函数和数组），它总是返回该对象，即不用转换。
    
      利用这一点，可以写一个判断变量是否为对象的函数：
  
      ```js
      function isObject(value) {
        return value === Object(value);
      }
      isObject([]) // true
      isObject(true) // false
      ```
  
    - ##### `Object()`构造函数：
    
      `Object`不仅可以当作工具函数使用，还可以当作构造函数使用，即前面可以使用`new`命令。
    
      `Object`构造函数的首要用途，是直接通过它来生成新对象。注意，通过`var obj = new Object()`的写法生成新对象，与字面量的写法`var obj = {}`是等价的。或者说，后者只是前者的一种简便写法。
    
      `Object`构造函数的用法与工具方法很相似，几乎一模一样。使用时，可以接受一个参数，如果该参数是一个对象，则直接返回该对象；如果是一个原始类型的值，则返回该值对应的包装对象。
    
      虽然用法相似，但是`Object(value)`与`new Object(value)`两者的语义是不同的，`Object(value)`表示将`value`转成一个对象，`new Object(value)`则表示新生成一个对象，它的值是`value`。
    
    - ##### `Object`的静态方法：
    
      - `Object.keys(obj)`，`Object.getOwnPropertyNames(obj)`：它们都可以遍历参数对象的属性，返回一个字符串数组，数组的成员是该对象自身的（而不是继承的）所有属性名。区别是`Object.getOwnPropertyNames`还可以返回不可枚举的属性名（秘密属性也可以访问到）。前者更常用。
    
      - `Object.create()`：通过指定原型对象和实例属性，生成一个新的对象。该方法的第一个参数必须是对象或`null`，作为新对象的原型；第二个参数是一个属性描述对象，它所描述的属性会被添加到新的实例对象上，作为该对象自身的属性：
    
        ```js
        var obj = Object.create({}, {
          p1: {
            value: 123,
            enumerable: true,
            configurable: true,
            writable: true,
          },
          p2: {
            value: 'abc',
            enumerable: true,
            configurable: true,
            writable: true,
          }
        });
        
        // 等同于
        var obj = Object.create({});
        obj.p1 = 123;
        obj.p2 = 'abc';
        ```
    
      - `Object.getPrototypeOf(obj)`：获取参数对象的`prototype`原型对象。这是获取原型对象的标准方法，而不是通过对象的`__proto__`属性去获取。
    
      - `Object.setPrototypeOf(obj, proto)`：为参数对象设置原型，返回该参数对象。它接受两个参数，第1个是现有对象，第2个是原型对象。
    
    - ##### `Object`的实例方法：
    
      - `Object.prototype.valueOf()`：返回一个对象的“值”，默认情况下返回对象本身。 `valueOf`方法的主要用途是，JS 进行自动类型转换时会默认调用这个方法。该方法通常用于被子类型重写/覆盖。
    
      - `Object.prototype.toString()`：返回当前对象对应的字符串形式，默认情况下返回类型字符串。可以通过自定义`toString`方法，可以让对象在自动类型转换时，得到想要的字符串形式。
    
        数组、字符串、函数、Date 对象都自定义了各自的`toString`方法，覆盖了`Object.prototype.toString`方法。
        
        由于实例对象可能会自定义`toString`方法，覆盖掉`Object.prototype.toString`方法，所以为了得到类型字符串，最好直接使用`Object.prototype.toString`方法。通过函数的`call`方法，可以在任意值上调用这个方法，帮助我们判断这个值的类型。
        
        ```js
        Object.prototype.toString.call(value)
        ```
        
        不同数据类型的`Object.prototype.toString`方法返回值如下：
        
        - 数值：返回`[object Number]`。
        - 字符串：返回`[object String]`。
        - 布尔值：返回`[object Boolean]`。
        - undefined：返回`[object Undefined]`。
        - null：返回`[object Null]`。
        - 数组：返回`[object Array]`。
        - arguments 对象：返回`[object Arguments]`。
        - 函数：返回`[object Function]`。
        - Error 对象：返回`[object Error]`。
        - Date 对象：返回`[object Date]`。
        - RegExp 对象：返回`[object RegExp]`。
        - 其他对象：返回`[object Object]`。
    
      - `Object.prototype.toLocaleString()`：返回当前对象对应的本地字符串形式。
    
        这个方法的主要作用是留出一个接口，让各种不同的对象实现自己版本的`toLocaleString`，用来返回针对某些地域的特定的值。
    
        目前，主要有`Array、Number、Date`这三个对象自定义了`toLocaleString`方法。
    
      - `Object.prototype.hasOwnProperty('proName')`：判断当前对象自身是否拥有该属性，继承自原型的属性不算。
    
      - `Object.prototype.isPrototypeOf(obj)`：判断当前对象是否为参数对象的原型。只要当前实例对象在参数对象的原型链上，`isPrototypeOf`方法都返回`true`。
    
      - （不推荐使用）`Object.prototype.__proto__`：返回该对象的原型。该属性可读可写。（Node/浏览器环境专属）
    
        根据 JS 语言标准，`__proto__`属性只有浏览器才需要部署，其他环境可以没有这个属性。它前后的两根下划线，表明它本质是一个内部属性，不应该对使用者暴露。因此，应该尽量少用这个属性，而是用`Object.getPrototypeOf()`和`Object.setPrototypeOf()`进行原型对象的读写操作。
    
      - `Object.prototype.propertyIsEnumerable()`：判断某个属性是否可遍历。注意，这个方法只能用于判断**对象自身的属性**，对于继承的属性一律返回`false`。
    
    - ##### 控制对象状态：
    
      有时需要冻结对象的读写状态，防止对象被改变。JS 提供了三种冻结方法，最弱的一种是`Object.preventExtensions`，其次是`Object.seal`，最强的是`Object.freeze`。
    
      - `Object.preventExtensions()`：防止对象扩展。即该对象不能再有新的属性被添加，但可以修改现有属性的值或删除属性。
      - `Object.isExtensible()`：判断对象是否可扩展。
      - `Object.seal()`：密封一个对象。密封后的对象不能添加新属性，也不能删除已有属性（但还是可写的），并且所有现有属性的可配置性（`configurable`）会被设置为false。
      - `Object.isSealed()`：判断一个对象是否被密封。
      - `Object.freeze()`：冻结一个对象。被冻结的对象不能添加、删除或修改任何属性，所有属性都变为只读。此时这个对象实际上变成了常量。
      - `Object.isFrozen()`：判断一个对象是否被冻结。
    
      上面的三个方法锁定对象的可写性有一个**漏洞：可以通过改变原型对象，来为对象增加属性**。
    
  - #### 属性描述对象
  
    JS 提供了一个内部数据结构，用来描述对象的属性，控制它的行为，比如该属性是否可写、可遍历等等。这个内部数据结构称为“属性描述对象”（attributes object）。每个属性都有自己对应的属性描述对象，保存该属性的一些元信息。
  
    下面是属性描述对象的一个例子：
  
    ```js
    {
      value: 123,
      writable: false,
      enumerable: true,
      configurable: false,
    }
    ```
  
    属性描述符有2种主要类型：数据描述符和访问器描述符。**数据描述符**是一个具有可写或不可写值的属性（具有 [`value` 或 `writable`] ）。**访问器描述符**是由 `getter/setter` 函数对描述的属性（具有 [`get` 或 `set`] ）。
  
    描述符只能是这两种类型之一，不能同时为两者。如果描述符同时具有 [`value` 或 `writable`] 和 [`get` 或 `set`] 键，则会抛出异常。
  
    如果描述符没有 `get` 或 `set` 键，它将被视为数据描述符。
  
    ###### `Object`构造器上有3个静态方法，用于访问和设置属性描述对象：
  
    - `Object.getOwnPropertyDescriptor(obj, 'proName')`：获取某个属性的**属性描述对象**。它的第一个参数是目标对象，第二个参数是一个字符串，对应目标对象的某个属性名。该方法只能用于**对象自身的属性**，不能用于继承的属性。
  
    - `Object.defineProperty()`：该方法允许通过属性描述对象，定义或修改一个对象的属性，然后返回修改后的对象。如果属性已经存在，`Object.defineProperty()`方法相当于更新该对象属性的属性描述对象。实参中的属性描述对象会和原属性描述对象进行合并。用法：
  
      ```js
      Object.defineProperty(object, propertyName, attributesObject)
      ```
      
      `Object.defineProperty`方法接受三个参数，分别是：属性所在的对象、属性名字符串、属性描述对象。
      
      举例来说，定义`obj.p`可以写成下面这样。
      
      ```js
      var obj = Object.defineProperty({}, 'p', {
        value: 123,
        writable: false,
        enumerable: true,
        configurable: false
      });
      
      obj.p // 123
      
      obj.p = 246;
      obj.p // 123
      ```
      
      上面代码中，`Object.defineProperty()`方法定义了`obj.p`属性。由于属性描述对象的`writable`属性为`false`，所以`obj.p`属性不能被重新赋值。注意，这里的`Object.defineProperty`方法的第一个参数是`{}`（一个新建的空对象），`p`属性直接定义在这个空对象上面，然后返回这个对象，这是`Object.defineProperty()`的常见用法。
      
      如果属性已经存在，`Object.defineProperty()`方法相当于更新该属性的属性描述对象。
      
    - `Object.defineProperties()`：通过属性描述对象，一次性定义多个属性。
  
      ```js
      var obj = Object.defineProperties({}, {
        p1: { value: 123, enumerable: true },
        p2: { value: 'abc', enumerable: true },
        p3: { get: function () { return this.p1 + this.p2 },
          enumerable:true,
          configurable:true
        }
      });
      
      obj.p1 // 123
      obj.p2 // "abc"
      obj.p3 // "123abc"
      ```
      
      上面代码中，`Object.defineProperties()`同时定义了`obj`对象的三个属性。其中，`p3`属性定义了取值函数`get`，即每次读取该属性，都会调用这个取值函数。
  
    ###### 属性描述对象的各个属性称为“元属性”，因为它们可以看作是控制属性的属性：
  
    - `value`：`value`属性是目标属性的值。默认为`undefined`。
  
      ```js
      var obj = {};
      obj.p = 123;
      
      Object.getOwnPropertyDescriptor(obj, 'p').value
      // 123
      
      Object.defineProperty(obj, 'p', { value: 246 });
      obj.p // 246
      ```
  
      上面代码是通过属性描述对象的`value`属性，读写`obj.p`的例子。
  
    - `writable`：`writable`属性是一个布尔值，决定了目标属性的值（`value`）是否可以被修改。默认为`false`。
  
      ```js
      var obj = {};
      
      Object.defineProperty(obj, 'a', {
        value: 37,
        writable: false
      });
      
      obj.a // 37
      obj.a = 25;
      obj.a // 37
      ```
  
      上面代码中，`obj.a`的`writable`属性是`false`。然后，改变`obj.a`的值，不会有任何效果，只会默默失败。严格模式下会报错，即使赋相同的值。
      
      如果属性的`configurable`为`true`，那么还是可以通过`Object.defineProperty`去改属性的值。
  
      注意：如果原型对象上某个属性的`writable`为`false`，那么子对象自身将无法添加该属性了。（严格模式下还会报错）
  
      ```js
      var proto = Object.defineProperty({}, 'foo', {
        value: 'a',
        writable: false
      });
      
      var obj = Object.create(proto);
      
      obj.foo = 'b';
      obj.foo // 'a'
      ```
  
      除非通过覆盖属性描述对象来绕过这个限制：（这种情况下，原型链会被完全忽视）
  
      ```js
      var proto = Object.defineProperty({}, 'foo', {
        value: 'a',
        writable: false
      });
      
      var obj = Object.create(proto);
      Object.defineProperty(obj, 'foo', {
        value: 'b'
      });
      
      obj.foo // "b"
      ```
  
    - `enumerable`：`enumerable`（可遍历性）返回一个布尔值，表示目标属性是否可遍历。默认为`false`。
  
      JS 的早期版本，`for...in`循环是基于`in`运算符的。我们知道，`in`运算符不管某个属性是对象自身的还是继承的，都会返回`true`。这显然不太合理，后来就引入了“可遍历性”这个概念。只有可遍历的属性，才会被`for...in`循环遍历，同时还规定`toString`这一类实例对象继承的原生属性，都是不可遍历的，这样就保证了`for...in`循环的可用性。
      
      具体来说，如果一个属性的`enumerable`为`false`，下面三个操作不会取到该属性：
      
      - `for..in`循环
      - `Object.keys`方法
      - `JSON.stringify`方法
      
      因此，`enumerable`可以用来设置“秘密”属性。但不是真正的私有属性，还是可以直接获取它的值。
      
      `JSON.stringify`方法会排除`enumerable`为`false`的属性，有时可以利用这一点。如果对象的 JSON 格式输出要排除某些属性，就可以把这些属性的`enumerable`设为`false`。
  
    - `configurable`：`configurable`(可配置性）返回一个布尔值，决定了是否可以修改属性描述对象。默认为`false`。
  
      也就是说，`configurable`为`false`时，`writable`、`enumerable`、`configurable`、`get/set` 都不能被修改了。另外，可配置性也决定了目标属性是否可以被`delete`关键字删除。
      
      注意，当`configurable`为`false`时，`writable`属性只有在`false`改为`true`时会报错，`true`改为`false`是允许的。
      
    - 存取器（`getter/setter`）：除了直接定义以外，属性还可以用存取器（accessor）定义。其中，存值函数称为`setter`，使用属性描述对象的`set`属性；取值函数称为`getter`，使用属性描述对象的`get`属性。一旦对目标属性定义了存取器，那么存取的时候，都将执行对应的函数。利用这个功能，可以实现许多高级特性，比如定制属性的读取和赋值行为。
  
      ```js
      var obj = Object.defineProperty({}, 'p', {
        get: function () {
          return 'getter';
        },
        set: function (value) {
          console.log('setter: ' + value);
        }
      });
      
      obj.p // "getter"
      obj.p = 123 // "setter: 123"
      ```
      
      上面代码中，`obj.p`定义了`get`和`set`属性。`obj.p`取值时，就会调用`get`；赋值时，就会调用`set`。
      
      注意，取值函数`get`不能接受参数，存值函数`set`只能接受一个参数（即属性的值）。
  
      JS 还提供了存取器的另一种写法：（ES5）
  
      ```js
      // 写法二
      var obj = {
        get p() {
          return 'getter';
        },
        set p(value) {
          console.log('setter: ' + value);
        }
      };
      ```
  
      上面两种写法，虽然属性`p`的读取和赋值行为是一样的，但是有一些细微的区别。
      
      第一种写法，属性`p`的`configurable`和`enumerable`默认都是`false`，从而导致属性`p`是不可遍历的；第二种写法，属性`p`的`configurable`和`enumerable`默认为`true`，因此属性`p`是可遍历的。因此，实际开发中，写法二更常用。
  
      注意：一旦定义了取值函数`get`或存值函数`set`，就不能同时再设置`writable`和`value`属性了。
  
  - #### Array 对象
  
    `Array`是 JS 的原生对象，同时也是一个构造函数，通过它也可以创建数组。
  
    ```js
    var arr = new Array(2);  // 等价于 var arr = [,,]
    arr.length // 2
    arr // [ empty x 2 ]
    ```
  
    上面代码中，`Array()`构造函数的参数`2`，表示生成一个两个成员的数组，每个位置都是空位（hole）。
  
    如果没有使用`new`关键字，运行结果也是一样的。
  
    ```js
    var arr = Array(2);
    // 等同于
    var arr = new Array(2);
    ```
  
    考虑到语义性，以及与其他构造函数用法保持一致，建议总是加上`new`。
  
    > 注意：在 JS 中，无论是通过构造函数 `new Array()` 还是数组字面量 `[]` 创建的数组，**本质都是 `Array` 构造函数的实例**，共享相同的原型链和方法。数组字面量`[]`只是 `new Array()` 的语法糖而已，底层仍然调用 `Array` 构造函数。
  
    `Array()`构造函数有一个很大的缺陷，不同的参数个数会导致不一致的行为。
  
    ```js
    // 无参数时，返回一个空数组
    new Array() // []
    
    // 单个正整数参数，表示返回的新数组的长度
    new Array(1) // [ empty ]
    new Array(2) // [ empty x 2 ]
    
    // 非正整数的数值作为参数，会报错
    new Array(3.2) // RangeError: Invalid array length
    new Array(-3) // RangeError: Invalid array length
    
    // 单个非数值（比如字符串、布尔值、对象等）作为参数，
    // 则该参数是返回的新数组的成员
    new Array('abc') // ['abc']
    new Array([1]) // [Array[1]]
    
    // 多参数时，所有参数都是返回的新数组的成员
    new Array(1, 2) // [1, 2]
    new Array('a', 'b', 'c') // ['a', 'b', 'c']
    ```
  
    可以看到，`Array()`作为构造函数，行为很不一致。因此，不建议使用它生成新数组，**直接使用数组字面量是更好的做法**。
  
    ```js
    // bad
    var arr = new Array(1, 2);
    
    // good
    var arr = [1, 2];
    ```
  
    注意，如果参数是一个正整数，返回数组的成员都是空位。虽然读取的时候返回`undefined`，但实际上该位置没有任何值。虽然这时可以读取到`length`属性，但是取不到键名。
  
    ```js
    var a = new Array(3);
    var b = [undefined, undefined, undefined];
    
    a.length // 3
    b.length // 3
    
    a[0] // undefined
    b[0] // undefined
    
    0 in a // false
    0 in b // true
    ```
  
    上面代码中，`a`是`Array()`生成的一个长度为3的空数组，`b`是一个三个成员都是`undefined`的数组，这两个数组是不一样的。读取键值的时候，`a`和`b`都返回`undefined`，但是`a`的键名（成员的序号）都是空的，`b`的键名是有值的。
  
    - ##### 静态方法
  
      - `Array.isArray()`：该方法返回一个布尔值，表示参数是否为数组。它可以弥补`typeof`运算符的不足。
  
        ```js
        var arr = [1, 2, 3];
        
        typeof arr // "object"
        Array.isArray(arr) // true
        ```
  
        > 上面代码中，`typeof`运算符只能显示数组的类型是`Object`，而`Array.isArray`方法可以识别数组。
  
    - ##### 实例方法
  
      - `valueOf()`：`valueOf`方法是一个所有对象都拥有的方法，表示对该对象求值。不同对象的`valueOf`方法不尽一致，数组的`valueOf`方法返回数组本身。
  
        ```js
        var arr = [1, 2, 3];
        arr.valueOf() // [1, 2, 3]
        ```
  
      - `toString()`：`toString`方法也是对象的通用方法，数组的`toString`方法返回数组的字符串形式。
  
        ```js
        var arr = [1, 2, 3];
        arr.toString() // "1,2,3"
        
        var arr = [1, 2, 3, [4, 5, 6]];
        arr.toString() // "1,2,3,4,5,6"
        ```
  
      - `push()，pop()`：`push`方法用于在数组的末端添加一个或多个元素，并返回添加新元素后的数组长度。注意，该方法会修改原数组。
  
        ```js
        var arr = [];
        
        arr.push(1) // 1
        arr.push('a') // 2
        arr.push(true, {}) // 4
        arr // [1, 'a', true, {}]
        ```
  
        上面代码使用`push`方法，往数组中添加了四个成员。
  
        `pop`方法用于删除数组的最后一个元素，并返回该元素。注意，该方法会修改原数组。
  
        ```js
        var arr = ['a', 'b', 'c'];
        
        arr.pop() // 'c'
        arr // ['a', 'b']
        ```
  
        对空数组使用`pop`方法，不会报错，而是返回`undefined`。
  
        ```js
        [].pop() // undefined
        ```
  
        `push`和`pop`结合使用，就构成了“后进先出”的栈结构（stack）。
  
        ```js
        var arr = [];
        arr.push(1, 2);
        arr.push(3);
        arr.pop();
        arr // [1, 2]
        ```
  
        上面代码中，`3`是最后进入数组的，但是最早离开数组。
  
      - `shift()，unshift()`：`shift()`方法用于删除数组的第一个元素，并返回该元素。注意，该方法会修改原数组。
  
        ```js
        var a = ['a', 'b', 'c'];
        
        a.shift() // 'a'
        a // ['b', 'c']
        ```
  
        上面代码中，使用`shift()`方法以后，原数组就变了。
  
        `shift()`方法可以遍历并清空一个数组。
  
        ```js
        var list = [1, 2, 3, 4];
        var item;
        
        while (item = list.shift()) {
          console.log(item);
        }
        
        list // []
        ```
  
        上面代码通过`list.shift()`方法每次取出一个元素，从而遍历数组。它的前提是数组元素不能是`0`或任何布尔值等于`false`的元素，因此这样的遍历不是很可靠。
  
        `push()`和`shift()`结合使用，就构成了“先进先出”的队列结构（queue）。
  
        `unshift()`方法用于在数组的第一个位置添加元素，并返回添加新元素后的数组长度。注意，该方法会修改原数组。
  
        ```js
        var a = ['a', 'b', 'c'];
        
        a.unshift('x'); // 4
        a // ['x', 'a', 'b', 'c']
        ```
  
        `unshift()`方法可以接受多个参数，这些参数都会添加到目标数组头部。
  
        ```js
        var arr = [ 'c', 'd' ];
        arr.unshift('a', 'b') // 4
        arr // [ 'a', 'b', 'c', 'd' ]
        ```
  
      - `join()`：`join()`方法以指定参数作为分隔符，将所有数组成员连接为一个字符串返回。如果不提供参数，默认用逗号分隔。
  
        ```js
        var a = [1, 2, 3, 4];
        
        a.join(' ') // '1 2 3 4'
        a.join(' | ') // "1 | 2 | 3 | 4"
        a.join() // "1,2,3,4"
        ```
  
        如果数组成员是`undefined`或`null`或空位，会被转成空字符串。
  
        ```js
        [undefined, null].join('#')
        // '#'
        
        ['a',, 'b'].join('-')
        // 'a--b'
        ```
  
        通过`call`方法，这个方法也可以用于字符串或类似数组的对象。
  
        ```js
        Array.prototype.join.call('hello', '-')
        // "h-e-l-l-o"
        
        var obj = { 0: 'a', 1: 'b', length: 2 };
        Array.prototype.join.call(obj, '-')
        // 'a-b'
        ```
  
      - `concat()`：`concat`方法用于多个数组的合并。它将新数组的成员，添加到原数组成员的后部，然后返回一个新数组，原数组不变。
  
        ```js
        ['hello'].concat(['world'])
        // ["hello", "world"]
        
        ['hello'].concat(['world'], ['!'])
        // ["hello", "world", "!"]
        
        [].concat({a: 1}, {b: 2})
        // [{ a: 1 }, { b: 2 }]
        
        [2].concat({a: 1})
        // [2, {a: 1}]
        ```
  
        除了数组作为参数，`concat`也接受其他类型的值作为参数，添加到目标数组尾部。
  
        ```js
        [1, 2, 3].concat(4, 5, 6)
        // [1, 2, 3, 4, 5, 6]
        ```
  
        如果数组成员包括对象，`concat`方法返回当前数组的一个浅拷贝。所谓“浅拷贝”，指的是新数组拷贝的是对象的引用。
  
      - `reverse()`：`reverse`方法用于颠倒排列数组元素，返回改变后的数组。注意，该方法将修改原数组。
      
        ```js
        var a = ['a', 'b', 'c'];
        
        a.reverse() // ["c", "b", "a"]
        a // ["c", "b", "a"]
        ```
      
      - `sort()`：`sort`方法对数组成员进行排序，默认是按照字典顺序排序。排序后，原数组将被改变。
      
        ```js
        ['d', 'c', 'b', 'a'].sort()
        // ['a', 'b', 'c', 'd']
        
        [4, 3, 2, 1].sort()
        // [1, 2, 3, 4]
        
        [11, 101].sort()
        // [101, 11]
        
        [10111, 1101, 111].sort()
        // [10111, 1101, 111]
        ```
      
        上面代码的最后两个例子，需要特殊注意。`sort()`方法不是按照大小排序，而是按照字典顺序。也就是说，数值会被先转成字符串，再按照字典顺序进行比较，所以`101`排在`11`的前面。
  
        如果想让`sort`方法按照自定义方式排序，可以传入一个函数作为参数。
  
        ```js
        [10111, 1101, 111].sort(function (a, b) {
          return a - b;
        })
        // [111, 1101, 10111]
        ```
      
        上面代码中，`sort`的参数函数本身接受两个参数，表示进行比较的两个数组成员。如果该函数的返回值大于`0`，表示第一个成员排在第二个成员后面；其他情况下，都是第一个元素排在第二个元素前面。
        
        ```js
        [
          { name: "张三", age: 30 },
          { name: "李四", age: 24 },
          { name: "王五", age: 28  }
        ].sort(function (o1, o2) {
          return o1.age - o2.age;
        })
        // [
        //   { name: "李四", age: 24 },
        //   { name: "王五", age: 28  },
        //   { name: "张三", age: 30 }
        // ]
        ```
        
        注意，**自定义的排序函数应该返回数值**，否则不同的浏览器可能有不同的实现，不能保证结果都一致。
        
        ```js
        // bad
        [1, 4, 2, 6, 0, 6, 2, 6].sort((a, b) => a > b)
        
        // good
        [1, 4, 2, 6, 0, 6, 2, 6].sort((a, b) => a - b)
        ```
        
        上面代码中，前一种排序算法返回的是布尔值，这是不推荐使用的。后一种是数值，才是更好的写法。
        
        `sort`方法传入的这个自定义函数，在不同浏览器上被调用的次数是不同的。因为不同的JS引擎使用的排序算法不同。如果想指定某个排序算法，那么就不要使用`Array.prototype.sort()`方法，而是自己实现数组排序。
      
      - `slice()`：`slice`（分片）方法用于提取目标数组的一部分，返回一个新数组，原数组不变。
      
        ```js
        arr.slice(start, end);
        ```
  
        它的第一个参数为起始位置（从0开始，会包括在返回的新数组之中），第二个参数为终止位置（但该位置的元素本身不包括在内）。如果省略第二个参数，则一直返回到原数组的最后一个成员。
      
        ```js
        var a = ['a', 'b', 'c'];
        
        a.slice(0) // ["a", "b", "c"]
        a.slice(1) // ["b", "c"]
        a.slice(1, 2) // ["b"]
        a.slice(2, 6) // ["c"]
        a.slice() // ["a", "b", "c"]
        ```
  
        上面代码中，最后一个例子**`slice()`没有参数，实际上等于返回一个原数组的拷贝**。
      
        如果`slice()`方法的参数是负数，则表示倒数计算的位置。
      
        ```js
        var a = ['a', 'b', 'c'];
        a.slice(-2) // ["b", "c"]
        a.slice(-2, -1) // ["b"]
        ```
      
        上面代码中，`-2`表示倒数计算的第二个位置，`-1`表示倒数计算的第一个位置。
      
        如果第一个参数大于等于数组长度，或者第二个参数小于第一个参数，则返回空数组。
      
        ```js
        var a = ['a', 'b', 'c'];
        a.slice(4) // []
        a.slice(2, 1) // []
        ```
      
        `slice()`方法的一个重要应用，是将类似数组的对象转为真正的数组。
      
        ```js
        Array.prototype.slice.call({ 0: 'a', 1: 'b', length: 2 })
        // ['a', 'b']
        
        Array.prototype.slice.call(document.querySelectorAll("div"));
        Array.prototype.slice.call(arguments);
        ```
      
        上面代码的参数都不是数组，但是通过`call`方法，在它们上面调用`slice()`方法，就可以把它们转为真正的数组。
      
      - `splice()`：`splice`（剪接）方法用于删除原数组的一部分成员，并可以在删除的位置添加新的数组成员，返回值是被删除的元素组成的数组。注意，该方法会修改原数组。
      
        ```js
        arr.splice(start, count, addElement1, addElement2, ...);
        ```
      
        `splice`的第一个参数是删除的起始位置（从0开始），第二个参数是被删除的元素个数。如果后面还有更多的参数，则表示这些就是要被插入数组的新元素。
      
        ```js
        var a = ['a', 'b', 'c', 'd', 'e', 'f'];
        a.splice(4, 2) // ["e", "f"]
        a // ["a", "b", "c", "d"]
        ```
      
        上面代码从原数组4号位置，删除了两个数组成员。
      
        ```js
        var a = ['a', 'b', 'c', 'd', 'e', 'f'];
        a.splice(4, 2, 1, 2) // ["e", "f"]
        a // ["a", "b", "c", "d", 1, 2]
        ```
      
        上面代码除了删除成员，还插入了两个新成员。
      
        起始位置如果是负数，就表示从倒数位置开始删除。
      
        ```js
        var a = ['a', 'b', 'c', 'd', 'e', 'f'];
        a.splice(-4, 2) // ["c", "d"]
        ```
      
        上面代码表示，从倒数第四个位置`c`开始删除两个成员。
      
        如果只是单纯地插入元素，`splice`方法的第二个参数可以设为`0`。
      
        ```js
        var a = [1, 1, 1];
        
        a.splice(1, 0, 2) // []
        a // [1, 2, 1, 1]
        ```
      
        如果只提供第一个参数，等同于将原数组在指定位置拆分成两个数组。
      
        ```js
        var a = [1, 2, 3, 4];
        a.splice(2) // [3, 4]
      a // [1, 2]
        ```
  
      - `indexOf()，lastIndexOf()`：`indexOf`方法返回给定元素在数组中第一次出现的位置，如果没有出现则返回`-1`。
      
        ```js
        var a = ['a', 'b', 'c'];
        
        a.indexOf('b') // 1
        a.indexOf('y') // -1
        ```
      
        `indexOf`方法还可以接受第二个参数，表示搜索的开始位置。
      
        ```js
        ['a', 'b', 'c'].indexOf('a', 1) // -1
        ```
      
        上面代码从1号位置开始搜索字符`a`，结果为`-1`，表示没有搜索到。
      
        `lastIndexOf`方法返回给定元素在数组中最后一次出现的位置，如果没有出现则返回`-1`。
      
        ```js
        var a = [2, 5, 9, 2];
        a.lastIndexOf(2) // 3
        a.lastIndexOf(7) // -1
        ```
      
        注意，这两个方法不能用来搜索`NaN`的位置，即它们无法确定数组成员是否包含`NaN`。
      
        ```js
        [NaN].indexOf(NaN) // -1
        [NaN].lastIndexOf(NaN) // -1
        ```
      
        这是因为**这两个方法内部，使用严格相等运算符（`===`）进行比较**，而`NaN`是唯一一个不等于自身的值。
      
      - `forEach()`：`forEach()`方法与`map()`方法很相似，也是对数组的所有成员依次执行参数函数。但是，`forEach()`方法不返回值，只用来操作数据。这就是说，如果数组遍历的目的是为了得到返回值，那么要使用`map()`方法。
      
        > 注意：`forEach(callback)`中回调函数中的`return`语句无任何意义，仅仅只是提前退出当前回调的执行（类似`continue`），但不会终止整个循环。
      
        `forEach()`的用法与`map()`方法一致，参数是一个函数，该函数同样接受三个参数：当前值、当前位置、整个数组。
      
        ```js
        function log(element, index, array) {
          console.log('[' + index + '] = ' + element);
        }
        
        [2, 5, 9].forEach(log);
        // [0] = 2
        // [1] = 5
        // [2] = 9
        ```
      
        上面代码中，`forEach()`遍历数组不是为了得到返回值，而是为了在屏幕输出内容，所以不必使用`map()`方法。
      
        `forEach()`方法也可以接受第二个参数，绑定参数函数的`this`变量。
      
        ```js
        var out = [];
        
        [1, 2, 3].forEach(function(elem) {
          this.push(elem * elem);
        }, out);
        
        out // [1, 4, 9]
        ```
      
        上面代码中，空数组`out`是`forEach()`方法的第二个参数，结果，回调函数内部的`this`关键字就指向`out`。
      
        注意，`forEach()`方法无法中断执行，总是会将所有成员遍历完。如果希望符合某种条件时，就中断遍历，要使用`for`循环。
      
        ```js
        var arr = [1, 2, 3];
        
        for (var i = 0; i < arr.length; i++) {
          if (arr[i] === 2) break;
          console.log(arr[i]);
        }
        // 1
        ```
      
        上面代码中，执行到数组的第二个成员时，就会中断执行。`forEach()`方法做不到这一点。
      
        `forEach()`方法也会跳过数组的空位。
      
        ```js
        var log = function (n) {
          console.log(n + 1);
        };
        
        [1, undefined, 2].forEach(log)
        // 2
        // NaN
        // 3
        
        [1, null, 2].forEach(log)
        // 2
        // 1
        // 3
        
        [1, , 2].forEach(log)
        // 2
        // 3
        ```
      
        上面代码中，`forEach()`方法不会跳过`undefined`和`null`，但会跳过空位。
      
      - `map()`：`map()`方法将数组的所有成员依次传入参数函数，然后把每一次的执行结果组成一个新数组返回。
      
        ```js
        var numbers = [1, 2, 3];
        
        numbers.map(function (n) {
          return n + 1;
        });
        // [2, 3, 4]
        
        numbers
        // [1, 2, 3]
        ```
      
        上面代码中，`numbers`数组的所有成员依次执行参数函数，运行结果组成一个新数组返回，原数组没有变化。
  
        `map()`方法接受一个函数作为参数。该函数调用时，`map()`方法向它传入三个参数：当前成员、当前位置和数组本身。
      
        ```js
        [1, 2, 3].map(function(elem, index, arr) {
          return elem * index;
        });
        // [0, 2, 6]
        ```
      
        上面代码中，`map()`方法的回调函数有三个参数，`elem`为当前成员的值，`index`为当前成员的位置，`arr`为原数组（`[1, 2, 3]`）。
      
        `map()`方法还可以接受第二个参数，用来绑定回调函数内部的`this`变量（详见《this 变量》一章）。
      
        ```js
        var arr = ['a', 'b', 'c'];
        
        [1, 2].map(function (e) {
          return this[e];
        }, arr)
        // ['b', 'c']
        ```
      
        上面代码通过`map()`方法的第二个参数，将回调函数内部的`this`对象，指向`arr`数组。
      
        如果数组有空位，`map()`方法的回调函数在这个位置不会执行，会跳过数组的空位。
      
        ```js
        var f = function (n) { return 'a' };
        
        [1, undefined, 2].map(f) // ["a", "a", "a"]
        [1, null, 2].map(f) // ["a", "a", "a"]
        [1, , 2].map(f) // ["a", , "a"]
        ```
      
        上面代码中，`map()`方法不会跳过`undefined`和`null`，但是会跳过空位。
      
      - `filter()`：`filter()`方法用于过滤数组成员，满足条件的成员组成一个新数组返回。
      
        它的参数是一个函数，所有数组成员依次执行该函数，返回结果为`true`的成员组成一个新数组返回。该方法不会修改原数组。
      
        ```js
        [1, 2, 3, 4, 5].filter(function (elem) {
          return (elem > 3);
        })
        // [4, 5]
        ```
      
        上面代码将大于`3`的数组成员，作为一个新数组返回。
      
        ```js
        var arr = [0, 1, 'a', false];
        
        arr.filter(Boolean)
        // [1, "a"]
        ```
      
        上面代码中，`filter()`方法返回数组`arr`里面所有布尔值为`true`的成员。
      
        `filter()`方法的参数函数可以接受三个参数：当前成员，当前位置和整个数组。
      
        ```js
        [1, 2, 3, 4, 5].filter(function (elem, index, arr) {
          return index % 2 === 0;
        });
        // [1, 3, 5]
        ```
      
        上面代码返回偶数位置的成员组成的新数组。
      
        `filter()`方法还可以接受第二个参数，用来绑定参数函数内部的`this`变量。
      
        ```js
        var obj = { MAX: 3 };
        var myFilter = function (item) {
          if (item > this.MAX) return true;
        };
        
        var arr = [2, 8, 3, 4, 1, 3, 2, 9];
        arr.filter(myFilter, obj) // [8, 4, 9]
        ```
      
        上面代码中，过滤器`myFilter()`内部有`this`变量，它可以被`filter()`方法的第二个参数`obj`绑定，返回大于`3`的成员。
      
      - `some()，every()`：这两个方法类似“断言”（assert），返回一个布尔值，表示判断数组成员是否符合某种条件。
      
        它们接受一个函数作为参数，所有数组成员依次执行该函数。该函数接受三个参数：当前成员、当前位置和整个数组，然后返回一个布尔值。
      
        `some`方法是只要一个成员的返回值是`true`，则整个`some`方法的返回值就是`true`，否则返回`false`。
      
        ```js
        var arr = [1, 2, 3, 4, 5];
        arr.some(function (elem, index, arr) {
          return elem >= 3;
        });
        // true
        ```
      
        上面代码中，如果数组`arr`有一个成员大于等于3，`some`方法就返回`true`。
      
        `every`方法是所有成员的返回值都是`true`，整个`every`方法才返回`true`，否则返回`false`。
      
        ```js
        var arr = [1, 2, 3, 4, 5];
        arr.every(function (elem, index, arr) {
          return elem >= 3;
        });
        // false
        ```
      
        上面代码中，数组`arr`并非所有成员大于等于`3`，所以返回`false`。
      
        注意，对于空数组，`some`方法返回`false`，`every`方法返回`true`，回调函数都不会执行。
      
        ```js
        function isEven(x) { return x % 2 === 0 }
        
        [].some(isEven) // false
        [].every(isEven) // true
        ```
      
        `some`和`every`方法还可以接受第二个参数，用来绑定参数函数内部的`this`变量。
      
      - `reduce()，reduceRight()`：`reduce()`方法和`reduceRight()`方法依次处理数组的每个成员，最终累计为一个值。它们的差别是，`reduce()`是从左到右处理（从第一个成员到最后一个成员），`reduceRight()`则是从右到左（从最后一个成员到第一个成员），其他完全一样。
      
        ```js
        [1, 2, 3, 4, 5].reduce(function (pre, cur, index, arr) {
          console.log(pre, cur);
          return pre + cur;
        }, initValue)
        // 1 2
        // 3 3
        // 6 4
        // 10 5
        //最后结果：15
        ```
      
        上面代码中，`reduce()`方法用来求出数组所有成员的和。`reduce()`的参数是一个函数，数组每个成员都会依次执行这个函数。如果数组有 n 个成员，这个参数函数就会执行 n - 1 次。
      
        - 第一次执行：`pre`是数组的第一个成员`1`，`cur`是数组的第二个成员`2`。
        - 第二次执行：`pre`为上一轮的返回值`3`，`cur`为第三个成员`3`。
        - 第三次执行：`pre`为上一轮的返回值`6`，`cur`为第四个成员`4`。
        - 第四次执行：`pre`为上一轮返回值`10`，`cur`为第五个成员`5`。至此所有成员遍历完成，整个方法的返回值就是最后一轮的返回值`15`。
      
        `reduce()`方法和`reduceRight()`方法的第一个参数都是一个函数。该函数接受以下四个参数。
      
        1. 累积变量`pre`。第一次执行时，默认为数组的第一个成员；以后每次执行时，都是上一轮的返回值。
        2. 当前变量`cur`。第一次执行时，默认为数组的第二个成员；以后每次执行时，都是下一个成员。
        3. 当前位置`index`。一个整数，表示第二个参数（当前变量）的下标，默认为`1`。
        4. 原数组。
      
        这四个参数之中，只有前两个是必须的，后两个则是可选的。
      
        如果要对累积变量指定初值，可以把它放在`reduce()`方法和`reduceRight()`方法的第二个参数。
      
        ```js
        [1, 2, 3, 4, 5].reduce(function (a, b) {
          return a + b;
        }, 10);
        // 25
        ```
      
        上面代码指定参数`a`的初值为10，所以数组从10开始累加，最终结果为25。注意，这时`b`是从数组的第一个成员开始遍历，参数函数会执行5次。
      
        **建议总是加上第二个参数，这样比较符合直觉**，每个数组成员都会依次执行`reduce()`方法的参数函数。另外，**第二个参数可以防止空数组报错**。
      
        ```js
        function add(prev, cur) {
          return prev + cur;
        }
        
        [].reduce(add)
        // TypeError: Reduce of empty array with no initial value
        [].reduce(add, 1)
        // 1
        ```
      
        上面代码中，由于空数组取不到累积变量的初始值，`reduce()`方法会报错。这时，加上第二个参数，就能保证总是会返回一个值。
      
        下面是一个`reduceRight()`方法的例子。
      
        ```js
        function subtract(prev, cur) {
          return prev - cur;
        }
        
        [3, 2, 1].reduce(subtract) // 0
        [3, 2, 1].reduceRight(subtract) // -4
        ```
      
        上面代码中，`reduce()`方法相当于`3`减去`2`再减去`1`，`reduceRight`方法相当于`1`减去`2`再减去`3`。
      
        由于这两个方法会遍历数组，所以实际上可以用来做一些遍历相关的操作。比如，找出字符长度最长的数组成员。
      
        ```js
        function findLongest(entries) {
          return entries.reduce(function (longest, entry) {
            return entry.length > longest.length ? entry : longest;
          }, '');
        }
        
        findLongest(['aaa', 'bb', 'c']) // "aaa"
        ```
      
        上面代码中，`reduce()`的参数函数会将字符长度较长的那个数组成员，作为累积值。这导致遍历所有成员之后，累积值就是字符长度最长的那个成员。
  
  - #### 包装对象
  
    对象是 JS 语言最主要的数据类型，三种原始类型的值（数值、字符串、布尔值）在一定条件下，也会自动转为对象，也就是原始类型的“包装对象”（wrapper）。
  
    所谓“包装对象”，指的是与数值、字符串、布尔值分别相对应的`Number`、`String`、`Boolean`这三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。
  
    ```js
    var v1 = new Number(123);
    var v2 = new String('abc');
    var v3 = new Boolean(true);
    
    typeof v1 // "object"
    typeof v2 // "object"
    typeof v3 // "object"
    
    v1 === 123 // false
    v2 === 'abc' // false
    v3 === true // false
    ```
  
    上面代码中，基于原始类型的值，生成了三个对应的包装对象。可以看到，`v1`、`v2`、`v3`都是对象，且与对应的简单类型值不相等。
  
    包装对象的设计目的，首先是使得“对象”这种类型可以覆盖 JS 所有的值，整门语言有一个通用的数据模型，其次是使得原始类型的值也有办法调用自己的方法。
  
    `Number`、`String`和`Boolean`这三个原生对象，如果不作为构造函数调用（即调用时不加`new`），而是作为普通函数调用，常常用于将任意类型的值转为数值、字符串和布尔值。
  
    ```js
    // 字符串转为数值
    Number('123') // 123
    
    // 数值转为字符串
    String(123) // "123"
    
    // 数值转为布尔值
    Boolean(123) // true
    ```
  
    上面这种数据类型的转换，详见《数据类型转换》一节。
  
    总结一下，这三个对象作为构造函数使用（带有`new`）时，可以创建原始类型值对应的包装对象；作为普通函数使用时（不带有`new`），可以将任意类型的值，转为原始类型的值。
  
    **注意：**
  
    1. 在 JS 中，`Symbol` 和 `BigInt` 作为原始类型，也有其对应的包装对象。它们的包装对象主要用于在特定场景下提供方法调用。
  
    2. 不同于 `Number`、`String` 等包装类，`Symbol` 和 `BigInt` 不是构造函数，不能直接通过 `new` 关键字创建它俩的包装对象。只能是将值作为参数传到`Object()`函数中来创建对应的包装对象实例：
  
       ```js
       Object(123n)
       Object(Symbol())
       ```
  
    三种包装对象各自提供了许多实例方法，详见后文。这里介绍两种它们共同具有、从`Object`对象继承的2个实例方法：
  
    - `valueOf()`：`valueOf()`方法返回包装对象实例对应的原始类型的值。
  
      **Tips：**
      
      在编程中，*valueOf* 方法用于返回对象的原始值。不同编程语言中，*valueOf* 方法的实现和用途有所不同：
      
      在 Java 中，*valueOf* 方法是一个静态方法，用于将给定参数转换为相应的包装类对象。该方法可以接收基本数据类型或字符串作为参数，并返回相应的包装类对象。
      
      ```java
      Integer x = Integer.valueOf(9);
      ```
      
      而在 JS 中，*valueOf* 方法用于返回包装对象实例对应的原始类型的值。
      
      ```js
      new Number(123).valueOf()  // 123
      new String('abc').valueOf() // "abc"
      new Boolean(true).valueOf() // true
      ```
      
    - `toString()`：`toString()`方法返回包装对象对应的原始值，它的字符串形式。
  
      ```js
      new Number(123).toString() // "123"
      new String('abc').toString() // "abc"
      new Boolean(true).toString() // "true"
      ```
    ##### 原始类型与实例对象的自动转换：
  
    某些场合，原始类型的值会自动当作包装对象调用，即调用包装对象的属性和方法。这时，JS 引擎会自动将原始类型的值转为包装对象实例（临时的），并在**使用后立刻销毁该实例**。
  
    比如，字符串可以调用`length`属性，返回字符串的长度。
  
    ```js
    'abc'.length // 3
    ```
  
    上面代码中，`abc`是一个字符串，本身不是对象，不能调用`length`属性。JS 引擎自动将其转为包装对象，在这个对象上调用`length`属性。调用结束后，这个临时对象就会被销毁。这就叫原始类型与实例对象的自动转换。
  
    ```js
    var str = 'abc';
    str.length // 3
    
    // 等同于
    var strObj = new String(str)
    // String {
    //   0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"
    // }
    strObj.length // 3
    ```
  
    上面代码中，字符串`abc`的包装对象提供了多个属性，`length`只是其中之一。
  
    **自动转换生成的包装对象是只读的**。所以，字符串无法添加新属性。
  
    ```js
    var s = 'Hello World';
    s.x = 123;
    s.x // undefined
    ```
  
    上面代码为字符串`s`添加了一个`x`属性，结果无效，总是返回`undefined`。
  
    另一方面，调用结束后，包装对象实例会自动销毁。这意味着，下一次调用字符串的属性时，实际是调用一个重新生成的新对象，而不是上一次调用时生成的那个对象，所以取不到赋值在上一个对象的属性。如果要为字符串添加属性，只有在它的原型对象`String.prototype`上定义（参见《面向对象编程》章节）。
  
  - #### Boolean 对象
  
    `Boolean`对象是 JS 的三大包装对象之一。它的构造函数主要用于生成布尔值的包装对象实例。
    
    ```js
    var b = new Boolean(true);
    
    typeof b // "object"
    b.valueOf() // true
    ```
    
    上面代码的变量`b`是一个`Boolean`对象的实例，它的类型是对象，值为布尔值`true`。
    
    注意，`false`对应的包装对象实例，布尔运算结果也是`true`。
    
    ```js
    if (new Boolean(false)) {
      console.log('true');
    } // true
    
    if (new Boolean(false).valueOf()) {
      console.log('true');
    } // 无输出
    ```
    
    上面代码的第一个例子之所以得到`true`，是因为`false`对应的包装对象实例是一个对象，进行逻辑运算时，被自动转化成布尔值`true`（因为所有对象对应的布尔值都是`true`）。而实例的`valueOf`方法，则返回实例对应的原始值，本例为`false`。
  
    ##### Boolean() 函数的类型转换作用：
  
    `Boolean()`当作工具函数，可以将任意值强转为布尔值。这时`Boolean`就是一个单纯的工具方法。
    
    ```js
    Boolean(undefined) // false
    Boolean(null) // false
    Boolean(0) // false
    Boolean('') // false
    Boolean(NaN) // false
    
    Boolean(1) // true
    Boolean('false') // true
    Boolean([]) // true
    Boolean({}) // true
    Boolean(function () {}) // true
    Boolean(/foo/) // true
    ```
    
    上面代码中几种得到`true`的情况，都值得认真记住。
    
    顺便提一下，连续进行2次的否运算（`!!`）也可以将任意值转为对应的布尔值。
    
    ```js
    !!undefined // false
    !!null // false
    !!0 // false
    !!'' // false
    !!NaN // false
    
    !!1 // true
    !!'false' // true
    !![] // true
    !!{} // true
    !!function(){} // true
    !!/foo/ // true
    ```
    
  - #### Number 对象
  
    `Number`对象是数值对应的包装对象，可以作为构造函数使用，也可以作为工具函数使用。
  
    作为构造函数时，它用于生成值为`number`类型的包装对象。
  
    ```js
    var n = new Number(1);
    typeof n // "object"
    ```
  
    上面代码中，`Number`对象作为构造函数使用，返回一个值为`1`的对象。
  
    作为工具函数时，它可以将任何类型的值转为数值。
  
    ```js
    Number(true) // 1
    ```
  
    上面代码将布尔值`true`转为数值`1`。`Number`作为工具函数的用法，详见《数据类型转换》一章。
  
    - ##### 静态属性
  
      `Number` 对象拥有以下一些静态属性（即直接定义在`Number`对象上的属性，而不是定义在实例上的属性）。
  
      - `Number.POSITIVE_INFINITY`：正的无限，指向`Infinity`。
      - `Number.NEGATIVE_INFINITY`：负的无限，指向`-Infinity`。
      - `Number.NaN`：表示非数值，指向`NaN`。
      - `Number.MAX_VALUE/Number.MIN_VALUE`：`number`类型可表示的最大和最小值。
      - `Number.MAX_SAFE_INTEGER/Number.MIN_SAFE_INTEGER`：`number`类型可精确表示的、最大和最小的整数，即`+/-9007199254740991`。
  
    - ##### 实例方法
  
      `Number`对象有4个实例方法，可以将数值转换成指定的格式。
  
      - `Number.prototype.toString()`：`Number`对象部署了自己的`toString`方法，用来将一个数值转为字符串形式。
  
        ```js
        (10).toString() // "10"
        ```
  
        `toString`方法可以接受一个参数，表示输出的进制。如果省略这个参数，默认将数值先转为十进制，再输出字符串；否则，就根据参数指定的进制，将一个数字转化成某个进制的字符串。
  
        ```js
        (10).toString(2) // "1010"
        (10).toString(8) // "12"
        (10).toString(16) // "a"
        ```
  
        上面代码中，`10`一定要放在括号里，这样表明后面的点表示调用对象属性。如果不加括号，这个点会被 JS 引擎解释成小数点，从而报错。
  
        ```js
        10.toString(2)
        // SyntaxError: Unexpected token ILLEGAL
        ```
  
        只要能够让 JS 引擎不混淆小数点和对象的点运算符，各种写法都能用。除了为`10`加上括号，还可以在`10`后面加两个点，JS 会把第一个点理解成小数点（即`10.0`），把第二个点理解成调用对象属性，从而得到正确结果。
  
        ```js
        10..toString(2)
        // "1010"
        
        // 其他方法还包括
        10 .toString(2) // "1010"
        10.0.toString(2) // "1010"
        ```
  
        这实际上意味着，可以直接对一个小数使用`toString`方法。
  
        ```js
        10.5.toString() // "10.5"
        10.5.toString(2) // "1010.1"
        10.5.toString(8) // "12.4"
        10.5.toString(16) // "a.8"
        ```
  
        通过方括号运算符也可以调用`toString`方法。
  
        ```js
        10['toString'](2) // "1010"
        ```
  
        `toString`方法只能将十进制的数，转为其他进制的字符串。如果要将其他进制的数，转回十进制，需要使用`parseInt`方法。
  
      - `Number.prototype.toLocaleString()`：该方法接受一个地区码作为参数，返回一个字符串，表示当前数字在该地区的当地书写形式。
  
        ```js
        (123).toLocaleString('zh-Hans-CN-u-nu-hanidec')  // "一二三"
        ```
  
        该方法还可以接受第二个参数配置对象，用来定制指定用途的返回字符串。该对象的`style`属性指定输出样式，默认值是`decimal`，表示输出十进制形式。如果值为`percent`，表示输出百分数。
  
        ```js
        (123).toLocaleString('zh-Hans-CN', { style: 'percent' })  // "12,300%"
        ```
  
        如果`style`属性的值为`currency`，则可以搭配`currency`属性，输出指定格式的货币字符串形式。
  
        ```js
        (123).toLocaleString('zh-Hans-CN', { style: 'currency', currency: 'CNY' })
        // "￥123.00"
        
        (123).toLocaleString('de-DE', { style: 'currency', currency: 'EUR' })
        // "123,00 €"
        
        (123).toLocaleString('en-US', { style: 'currency', currency: 'USD' })
        // "$123.00"
        ```
  
        如果`Number.prototype.toLocaleString()`省略了参数，则由浏览器自行决定如何处理，通常会使用操作系统的地区设定。注意，该方法如果使用浏览器不认识的地区码，会抛出一个错误。
  
        ```js
        (123).toLocaleString('123') // 出错
        ```
  
      - `Number.prototype.toFixed()`：先将一个数转为指定位数的小数，然后返回这个小数对应的字符串。
  
        ```js
        (10).toFixed(2) // "10.00"
        10.005.toFixed(2) // "10.01"
        ```
  
        上面代码中，`10`和`10.005`先转成2位小数，然后转成字符串。其中`10`必须放在括号里，否则后面的点会被处理成小数点。
  
        `toFixed()`方法的参数为小数位数，有效范围为0到100，超出这个范围将抛出 RangeError 错误。
  
        JS 的 `toFixed()` 方法在处理小数部分的舍入时，**并不完全遵循标准的四舍五入规则**，而是采用了一种称为 **"银行家舍入法"（Banker's Rounding）** 的规则，即 **四舍六入五取偶**（IEEE 754 标准）。
  
        由于浮点数的原因，小数`5`的四舍五入是不确定的，使用的时候必须小心。
  
        ```js
        (10.055).toFixed(2) // 10.05
        (10.005).toFixed(2) // 10.01
        ```
  
      - `Number.prototype.toExponential()`：`toExponential`方法用于将一个数转为科学计数法形式。
  
        ```js
        (10).toExponential()  // "1e+1"
        (10).toExponential(1) // "1.0e+1"
        (10).toExponential(2) // "1.00e+1"
        
        (1234).toExponential()  // "1.234e+3"
        (1234).toExponential(1) // "1.2e+3"
        (1234).toExponential(2) // "1.23e+3"
        ```
  
        `toExponential`方法的参数是小数点后有效数字的位数，范围为0到100，超出这个范围，会抛出一个 RangeError 错误。
  
      - `Number.prototype.toPrecision()`：`Number.prototype.toPrecision()`方法用于将一个数转为指定位数的有效数字。
  
        ```js
        (12.34).toPrecision(1) // "1e+1"
        (12.34).toPrecision(2) // "12"
        (12.34).toPrecision(3) // "12.3"
        (12.34).toPrecision(4) // "12.34"
        (12.34).toPrecision(5) // "12.340"
        ```
  
        该方法的参数为有效数字的位数，范围是1到100，超出这个范围会抛出 RangeError 错误。
  
        该方法用于四舍五入时不太可靠，跟浮点数不是精确储存有关。
  
        ```js
        (12.35).toPrecision(3) // "12.3"
        (12.25).toPrecision(3) // "12.3"
        (12.15).toPrecision(3) // "12.2"
        (12.45).toPrecision(3) // "12.4"
        ```
  
  - #### String 对象
  
    `String`对象是 JS 原生提供的三个包装对象之一，用来生成字符串对应的包装对象。
  
    ```js
    var s1 = 'abc';
    var s2 = new String('abc');
    
    typeof s1 // "string"
    typeof s2 // "object"
    
    s2.valueOf() // "abc"
    ```
  
    上面代码中，变量`s1`是字符串，`s2`是对象。由于`s2`是字符串对象，`s2.valueOf`方法返回的就是它所对应的原始字符串。
  
    字符串的包装对象是一个类似数组的对象（即**伪数组**）。
  
    ```js
    new String('abc')
    // String {0: "a", 1: "b", 2: "c", length: 3}
    
    (new String('abc'))[1] // "b"
    'abc'[1]  // 其实也是创建了一个临时的包装对象然后再取值的
    ```
  
    上面代码中，字符串`abc`对应的字符串对象，有数值键（`0`、`1`、`2`）和`length`属性，所以可以像数组那样取值。
  
    除了用作构造函数，`String`对象还可以当作工具方法使用，将任意类型的值转为字符串。
  
    ```js
    String(true) // "true"
    String(5) // "5"
    ```
  
    上面代码将布尔值`true`和数值`5`，分别转换为字符串。
  
    ##### 静态方法：
  
    - `String.fromCharCode()`：该方法返回由指定的 UTF-16 码元序列创建的字符串。
  
      ```js
      String.fromCharCode() // ""
      String.fromCharCode(97) // "a"
      String.fromCharCode(104, 101, 108, 108, 111)
      // "hello"
      ```
  
      上面代码中，`String.fromCharCode`方法的参数为空，就返回空字符串；否则，返回参数对应的 Unicode 字符串。
      
      注意，**该方法不支持码元大于`0xFFFF`的字符，即传入的参数不能大于`0xFFFF`（即十进制的 65535）**。
      
      ```js
      String.fromCharCode(0x20BB7)  // "ஷ"
      String.fromCharCode(0x20BB7) === String.fromCharCode(0x0BB7)  // true
      ```
      
      上面代码中，`String.fromCharCode`参数`0x20BB7`大于`0xFFFF`，导致返回结果出错。`0x20BB7`对应的字符是汉字`𠮷`，但是返回结果却是另一个字符（码点`0x0BB7`）。这是因为`String.fromCharCode`发现参数值大于`0xFFFF`，大于 `0xFFFF` 的数字会被截断为最后的 16 位。（即忽略`0x20BB7`最高位的`2`）。
      
      这种现象的根本原因在于，码点大于`0xFFFF`的字符占用四个字节，而 JS 默认只支持两个字节的字符（ES5）。这种情况下，必须把`0x20BB7`拆成两个字符的UTF-16编码表示：
      
      ```js
      String.fromCharCode(0xD842, 0xDFB7)  // "𠮷"
      ```
      
      上面代码中，`0x20BB7`拆成两个字符`0xD842`和`0xDFB7`（即两个两字节字符，合成一个四字节字符），就能得到正确的结果。码点大于`0xFFFF`的字符的四字节表示法，由 UTF-16 编码方法决定。
  
    ##### 实例方法：
  
    - `String.prototype.charCodeAt()`：返回一个十进制整数，表示给定索引处的 UTF-16 码元，其值介于 `0` 和 `65535` 之间。相当于`String.fromCharCode()`的逆操作。
  
      ```js
      'abc'.charCodeAt(1) // 98
      ```
  
      上面代码中，`abc`的`1`号位置的字符是`b`，它的 UTF-16 码元是`98`。
  
      如果没有任何参数，`charCodeAt`返回首字符的 UTF-16 码元。
  
      ```js
      'abc'.charCodeAt() // 97
      ```
  
      如果参数为负数，或大于等于字符串的长度，`charCodeAt`返回`NaN`。
  
      ```js
      'abc'.charCodeAt(-1) // NaN
      'abc'.charCodeAt(4) // NaN
      ```
  
      注意，`charCodeAt`方法返回的 UTF-16 码元不会大于65536（0xFFFF），也就是说，只返回两个字节的字符的码元。如果遇到码元大于 65536 的字符（四个字节的字符），必须连续使用两次`charCodeAt`，不仅读入`charCodeAt(i)`，还要读入`charCodeAt(i+1)`，将两个值放在一起、转换后才能得到准确的字符。
  
      ```js
      let str = "𪚥"; // 一个罕见汉字，码点是 U+2A6A5（大于 0xFFFF）
      console.log(str.length);  // 输出2，不是 1
      
      // 手动组合代理对
      let first = str.charCodeAt(0);   // 0xD869 (高位代理)
      let second = str.charCodeAt(1);  // 0xDEA5 (低位代理)
      
      // 将代理对转换回完整的码点
      let codePoint = ((first - 0xD800) * 0x400) + (second - 0xDC00) + 0x10000;
      console.log(codePoint.toString(16));  // 输出: "2a6a5"
      console.log(codePoint);               // 输出: 173733
      ```
  
      **Tips**：
  
      以下是将一对 UTF-16 码元转换为 Unicode 码位的可能算法，改编自 [Unicode 常问问题](https://unicode.org/faq/utf_bom.html#utf16-3)：
  
      ```js
      // 常量
      const LEAD_OFFSET = 0xd800 - (0x10000 >> 10);
      const SURROGATE_OFFSET = 0x10000 - (0xd800 << 10) - 0xdc00;
      
      function utf16ToUnicode(lead, trail) {
        return (lead << 10) + trail + SURROGATE_OFFSET;
      }
      function unicodeToUTF16(codePoint) {
        const lead = LEAD_OFFSET + (codePoint >> 10);
        const trail = 0xdc00 + (codePoint & 0x3ff);
        return [lead, trail];
      }
      
      const str = "𠮷";
      console.log(utf16ToUnicode(str.charCodeAt(0), str.charCodeAt(1))); // 134071
      console.log(str.codePointAt(0)); // 134071
      ```
  
    - `String.prototype.charAt()`：`charAt`方法返回指定位置的字符，参数是从`0`开始编号的位置。
  
      ```js
      var s = new String('abc');
      
      s.charAt(1) // "b"
      s.charAt(s.length - 1) // "c"
      ```
  
      如果参数为负数，或大于等于字符串的长度，`charAt`返回空字符串。
  
      ```js
      'abc'.charAt(-1) // ""
      'abc'.charAt(3) // ""
      ```
  
      这个方法完全可以用数组下标替代。
  
      ```js
      'abc'.charAt(1) // "b"
      'abc'[1] // "b"
      ```
  
    - `String.prototype.concat()`：`concat`方法用于连接两个字符串，返回一个新字符串，不改变原字符串。
  
      ```js
      var s1 = 'abc';
      var s2 = 'def';
      
      s1.concat(s2) // "abcdef"
      s1 // "abc"
      ```
  
      该方法可以接受多个参数。
  
      ```js
      'a'.concat('b', 'c') // "abc"
      ```
  
      如果参数不是字符串，`concat`方法会将其先转为字符串，然后再连接。
  
      ```js
      var one = 1;
      var two = 2;
      var three = '3';
      
      ''.concat(one, two, three) // "123"
      one + two + three // "33"
      ```
  
      上面代码中，`concat`方法将参数先转成字符串再连接，所以返回的是一个三个字符的字符串。
  
      作为对比，加号运算符在两个运算数都是数值时，不会转换类型，所以返回的是一个两个字符的字符串。
  
    - `String.prototype.slice()`：`slice`（切片）方法用于从原字符串取出子字符串并返回，不改变原字符串。它的第一个参数是子字符串的开始位置，第二个参数是子字符串的结束位置（不含该位置）。
  
      ```js
      'JavaScript'.slice(0, 4) // "Java"
      ```
  
      如果省略第二个参数，则表示子字符串一直到原字符串结束。
  
      ```js
      'JavaScript'.slice(4) // "Script"
      ```
  
      如果参数是负值，表示从结尾开始倒数计算的位置，即该负值加上字符串长度。
  
      ```js
      'JavaScript'.slice(-6) // "Script"
      'JavaScript'.slice(0, -6) // "Java"
      'JavaScript'.slice(-2, -1) // "p"
      ```
  
      如果第一个参数大于第二个参数（正数情况下），`slice()`方法返回一个空字符串。
  
      ```js
      'JavaScript'.slice(2, 1) // ""
      ```
  
    - ~~（不推荐、慎用）`String.prototype.substring()`~~：`substring`方法用于从原字符串取出子字符串并返回，不改变原字符串，跟`slice`方法很相像。它的第一个参数表示子字符串的开始位置，第二个位置表示结束位置（返回结果不含该位置）。
  
      ```js
      'JavaScript'.substring(0, 4) // "Java"
      ```
  
      如果省略第二个参数，则表示子字符串一直到原字符串的结束。
  
      ```js
      'JavaScript'.substring(4) // "Script"
      ```
  
      相较于`slice`方法，该方法有2个坑：
  
      1. 如果第一个参数大于第二个参数，`substring`方法会自动更换两个参数的位置。
  
         ```js
         'JavaScript'.substring(10, 4) // "Script"
         // 等同于
         'JavaScript'.substring(4, 10) // "Script"
         ```
  
         上面代码中，调换`substring`方法的两个参数，都得到同样的结果。
  
      2. 如果参数是负数，`substring`方法会自动将负数转为0。
  
         ```js
         'JavaScript'.substring(-3) // "JavaScript"
         'JavaScript'.substring(4, -3) // "Java"
         ```
  
         上面代码中，第二个例子的参数`-3`会自动变成`0`，等同于`'JavaScript'.substring(4, 0)`。由于第二个参数小于第一个参数，会自动互换位置，所以返回`Java`。
  
      由于这些规则违反直觉，因此不建议使用`substring`方法，应该**优先使用`slice`**。
  
    - ~~（已废弃，不推荐使用）`String.prototype.substr()`：`substr`方法用于从原字符串取出子字符串并返回，不改变原字符串，跟`slice`和`substring`方法的作用相同。~~
  
      `substr`方法的第一个参数是子字符串的开始位置（从0开始计算），第二个参数是子字符串的长度。
  
      ```js
      'JavaScript'.substr(4, 6) // "Script"
      ```
  
      如果省略第二个参数，则表示子字符串一直到原字符串的结束。
  
      ```js
      'JavaScript'.substr(4) // "Script"
      ```
  
      如果第一个参数是负数，表示倒数计算的字符位置。如果第二个参数是负数，将被自动转为0，因此会返回空字符串。
  
      ```js
      'JavaScript'.substr(-6) // "Script"
      'JavaScript'.substr(4, -1) // ""
      ```
  
      上面代码中，第二个例子的参数`-1`自动转为`0`，表示子字符串长度为`0`，所以返回空字符串。
  
    - `String.prototype.indexOf()`，`String.prototype.lastIndexOf()`：`indexOf`方法用于确定一个字符串在另一个字符串中第一次出现的位置，返回结果是匹配开始的位置。如果返回`-1`，就表示不匹配。
  
      ```js
      'hello world'.indexOf('o') // 4
      'JavaScript'.indexOf('script') // -1
      ```
  
      `indexOf`方法还可以接受第二个参数，表示从该位置开始向后匹配。
  
      ```js
      'hello world'.indexOf('o', 6) // 7
      ```
  
      `lastIndexOf`方法的用法跟`indexOf`方法一致，主要的区别是`lastIndexOf`从尾部开始匹配，`indexOf`则是从头部开始匹配。
  
      ```js
      'hello world'.lastIndexOf('o') // 7
      ```
  
      另外，`lastIndexOf`的第二个参数表示从该位置起向前匹配。
  
      ```js
      'hello world'.lastIndexOf('o', 6) // 4
      ```
  
    - `String.prototype.trim()`：`trim`方法用于去除字符串两端的空格，返回一个新字符串，不改变原字符串。
  
      ```js
      '  hello world  '.trim()
      // "hello world"
      ```
  
      该方法去除的不仅是空格，还包括制表符（`\t`、`\v`）、换行符（`\n`）和回车符（`\r`）。
  
      ```js
      '\r\nabc \t'.trim() // 'abc'
      ```
  
    - `String.prototype.toLowerCase()`，`String.prototype.toUpperCase()`：`toLowerCase`方法用于将一个字符串全部转为小写，`toUpperCase`则是全部转为大写。它们都返回一个新字符串，不改变原字符串。
  
      ```js
      'Hello World'.toLowerCase()  // "hello world"
      'Hello World'.toUpperCase()  // "HELLO WORLD"
      ```
  
    - `String.prototype.match()`：`match`方法用于确定原字符串是否匹配某个子字符串，返回一个数组，成员为匹配的第一个字符串。如果没有找到匹配，则返回`null`。
  
      ```js
      'cat, bat, sat, fat'.match('at') // ["at"]
      'cat, bat, sat, fat'.match('xt') // null
      ```
  
      返回的数组还有`index`属性和`input`属性，分别表示匹配字符串开始的位置和原始字符串。
  
      ```js
      var matches = 'cat, bat, sat, fat'.match('at');
      matches.index // 1
      matches.input // "cat, bat, sat, fat"
      ```
  
      `match`方法还可以使用正则表达式作为参数，详见《正则表达式》一章。
  
    - `String.prototype.search()`：`search`方法的用法基本等同于`match`，但是返回值为匹配的第一个位置。如果没有找到匹配，则返回`-1`。
  
      ```js
      'cat, bat, sat, fat'.search('at') // 1
      ```
  
      `search`方法也可以使用正则表达式作为参数，详见《正则表达式》一节。
  
    - `String.prototype.replace()`：`replace`方法用于替换匹配的子字符串，一般情况下只替换第一个匹配（除非使用带有`g`修饰符的正则表达式）。
  
      ```js
      'aaa'.replace('a', 'b') // "baa"
      ```
  
      `replace`方法还可以使用正则表达式作为参数，详见《正则表达式》一节。
  
    - `String.prototype.split()`：`split`方法按照给定规则分割字符串，返回一个由分割出来的子字符串组成的数组。
  
      ```js
      'a|b|c'.split('|') // ["a", "b", "c"]
      ```
  
      如果分割规则为空字符串，则返回数组的成员是原字符串的每一个字符。
  
      ```js
      'a|b|c'.split('') // ["a", "|", "b", "|", "c"]
      ```
  
      如果省略参数，则返回数组的唯一成员就是原字符串。
  
      ```js
      'a|b|c'.split() // ["a|b|c"]
      ```
  
      如果满足分割规则的两个部分紧邻着（即两个分割符中间没有其他字符），则返回数组之中会有一个空字符串。
  
      ```js
      'a||c'.split('|') // ['a', '', 'c']
      ```
  
      如果满足分割规则的部分处于字符串的开头或结尾（即它的前面或后面没有其他字符），则返回数组的第一个或最后一个成员是一个空字符串。
  
      ```js
      '|b|c'.split('|') // ["", "b", "c"]
      'a|b|'.split('|') // ["a", "b", ""]
      ```
  
      `split`方法还可以接受第二个参数，限定返回数组的最大成员数。
  
      ```js
      'a|b|c'.split('|', 0) // []
      'a|b|c'.split('|', 1) // ["a"]
      'a|b|c'.split('|', 2) // ["a", "b"]
      'a|b|c'.split('|', 3) // ["a", "b", "c"]
      'a|b|c'.split('|', 4) // ["a", "b", "c"]
      ```
  
      上面代码中，`split`方法的第二个参数，决定了返回数组的成员数。
  
      `split`方法还可以使用正则表达式作为参数，详见《正则表达式》一节。
  
    - `String.prototype.localeCompare()`：`localeCompare`方法用于比较两个字符串。它返回一个整数，如果小于0，表示第一个字符串小于第二个字符串；如果等于0，表示两者相等；如果大于0，表示第一个字符串大于第二个字符串。
  
      ```js
      'apple'.localeCompare('banana') // -1
      'apple'.localeCompare('apple') // 0
      ```
  
      该方法的最大特点，就是会考虑自然语言的顺序。举例来说，正常情况下，大写的英文字母小于小写字母。
  
      ```js
      'B' < 'a' // true
      ```
  
      上面代码中，字母`B`小于字母`a`。因为 JS 采用的是 Unicode 码点比较，`B`的码点是66，而`a`的码点是97。
  
      但是，`localeCompare`方法会考虑自然语言的排序情况，将`B`排在`a`的前面。
  
      ```js
      'B'.localeCompare('a') // 1
      ```
  
      上面代码中，`localeCompare`方法返回整数1，表示`B`较大。
  
      `localeCompare`还可以有第二个参数，指定所使用的语言（默认是英语），然后根据该语言的规则进行比较。
  
      ```js
      'ä'.localeCompare('z', 'de') // -1
      'ä'.localeCompare('z', 'sv') // 1
      ```
  
      上面代码中，`de`表示德语，`sv`表示瑞典语。德语中，`ä`小于`z`，所以返回`-1`；瑞典语中，`ä`大于`z`，所以返回`1`。
  
  - #### Math 对象
  
    `Math`是 JS 中原生的工具函数，提供各种数学功能。该对象不是构造函数，不能生成实例，所有的属性和方法都必须在`Math`对象上调用。
  
    - ##### 静态属性
  
      `Math`对象的静态属性，提供以下一些数学常数。这些属性都是**只读的**，不能修改。
  
      - `Math.E`：常数`e`。
      - `Math.LN2`：2 的自然对数。
      - `Math.LN10`：10 的自然对数。
      - `Math.LOG2E`：以 2 为底的`e`的对数。
      - `Math.LOG10E`：以 10 为底的`e`的对数。
      - `Math.PI`：常数`π`。
      - `Math.SQRT1_2`：0.5 的平方根。
      - `Math.SQRT2`：2 的平方根。
  
    - ##### 静态方法
  
      `Math`对象提供以下一些静态方法。
  
      - `Math.abs()`：绝对值
  
      - `Math.ceil()`：向上取整
  
      - `Math.floor()`：向下取整
  
      - `Math.max()/Math.min()`：返回参数之中最大的那个值、最小的那个值。如果参数为空，`Math.min`返回`Infinity`，`Math.max`返回`-Infinity`，这在逻辑上是合理的。
  
      - `Math.pow()`：幂运算。ES6 出现了幂运算符（`**`）之后，这个方法就用的少了。
  
      - `Math.sqrt()`：`Math.sqrt`方法返回参数值的平方根。如果参数是一个负值，则返回`NaN`。
  
      - `Math.log()`：返回以`e`为底的自然对数值。
  
        ```js
        Math.log(Math.E) // 1
        Math.log(10) // 2.302585092994046
        ```
  
        如果要计算以10为底的对数，可以先用`Math.log`求出自然对数，然后除以`Math.LN10`；求以2为底的对数，可以除以`Math.LN2`。
  
        ```js
        Math.log(100)/Math.LN10 // 2
        Math.log(8)/Math.LN2 // 3
        ```
  
      - `Math.exp()`：`Math.exp`方法返回常数`e`的参数次方。
  
      - `Math.round()`：标准的四舍五入。注意，它对负数的处理（主要是对`0.5`的处理）。
  
        ```js
        Math.round(0.1) // 0
        Math.round(0.5) // 1
        Math.round(0.6) // 1
        // 等同于
        Math.floor(x + 0.5)
        
        Math.round(-1.1) // -1
        Math.round(-1.5) // -1
        Math.round(-1.6) // -2
        ```
  
      - `Math.random()`：`Math.random()`返回`[0,1)`之间的一个伪随机数，可能等于0，但是一定小于1。
  
        任意范围的随机数生成函数如下。
  
        ```js
        function getRandomArbitrary(min, max) {
          return Math.random() * (max - min) + min;
        }
        
        getRandomArbitrary(1.5, 6.5)
        // 2.4942810038223864
        ```
  
        任意范围的随机整数生成函数如下。
  
        ```js
        function getRandomInt(min, max) {
          return Math.floor(Math.random() * (max - min + 1)) + min;
        }
        
        getRandomInt(1, 6) // 5
        ```
  
        返回随机字符的例子如下。
  
        ```js
        function random_str(length) {
          var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
          ALPHABET += 'abcdefghijklmnopqrstuvwxyz';
          ALPHABET += '0123456789-_';
          var str = '';
          for (var i = 0; i < length; ++i) {
            var rand = Math.floor(Math.random() * ALPHABET.length);
            str += ALPHABET.substring(rand, rand + 1);
          }
          return str;
        }
        
        random_str(6) // "NdQKOr"
        ```
  
        上面的`random_str`函数接受一个整数作为参数，返回变量`ALPHABET`内的随机字符所组成的指定长度的字符串。
  
    - ##### 三角函数方法
  
      `Math`对象还提供一系列三角函数方法：
  
      - `Math.sin()`：返回参数的正弦（参数为弧度值）
      - `Math.cos()`：返回参数的余弦（参数为弧度值）
      - `Math.tan()`：返回参数的正切（参数为弧度值）
      - `Math.asin()`：返回参数的反正弦（返回值为弧度值）
      - `Math.acos()`：返回参数的反余弦（返回值为弧度值）
      - `Math.atan()`：返回参数的反正切（返回值为弧度值）
  
      ```js
      Math.sin(0) // 0
      Math.cos(0) // 1
      Math.tan(0) // 0
      
      Math.sin(Math.PI / 2) // 1
      
      Math.asin(1) // 1.5707963267948966
      Math.acos(1) // 0
      Math.atan(1) // 0.7853981633974483
      ```
  
  - #### Date 对象
  
    `Date`对象是 JS 原生的时间对象，它以国际标准时间（UTC）1970年1月1日00:00:00作为时间的零点，可以表示的时间范围是前后各1亿天（单位是毫秒`ms`）。
  
    - ##### 普通函数的用法
  
      `Date`对象可以作为普通函数直接调用，返回一个代表当前时间的字符串。
  
      ```js
      Date()  // "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
      ```
  
      **注意，即使带有参数，`Date`作为普通函数使用时，返回的还是当前时间**。
  
      ```js
      Date(2000, 1, 1)  // "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
      ```
  
      上面代码说明，无论有没有参数，直接调用`Date`总是返回当前时间。
  
    - ##### 构造函数的用法
  
      `Date`还可以当作构造函数使用。对它使用`new`命令，会返回一个`Date`对象的实例。如果不加参数，实例代表的就是当前时间。
  
      ```js
      var today = new Date();
      ```
  
      `Date`实例有一个独特的地方。其他对象求值的时候，都是默认调用`.valueOf()`方法，但是`Date`实例求值的时候，默认调用的是`toString()`方法（自动类型转换走的`string`路线）。这导致对`Date`实例求值，返回的是一个字符串，代表该实例对应的时间。
  
      ```js
      var today = new Date();
      
      today // "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
      
      // 等同于
      today.toString() // "Tue Dec 01 2015 09:34:43 GMT+0800 (CST)"
      ```
      
      上面代码中，`today`是`Date`的实例，直接求值等同于调用`toString`方法。
  
      作为构造函数时，`Date`对象可以接受多种格式的参数，返回一个该参数对应的时间实例。
  
      ```js
      // 参数为时间零点开始计算的毫秒数
      new Date(1378218728000)
      // Tue Sep 03 2013 22:32:08 GMT+0800 (CST)
      
      // 参数为日期字符串
      new Date('January 6, 2013');
      // Sun Jan 06 2013 00:00:00 GMT+0800 (CST)
      
      // 参数为多个整数，
      // 代表年、月、日、小时、分钟、秒、毫秒
      new Date(2013, 0, 1, 0, 0, 0, 0)
      // Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
      ```
      
      关于`Date`构造函数的参数，有几点说明：
  
      1. 参数可以是负整数，代表1970年元旦之前的时间。
  
         ```js
         new Date(-1378218728000)  // Fri Apr 30 1926 17:27:52 GMT+0800 (CST)
         ```
      
      2. 只要是能被`Date.parse()`方法解析的字符串，都可以当作参数。
  
         ```js
         new Date('2013-2-15')
         new Date('2013/2/15')
         new Date('02/15/2013')
         new Date('2013-FEB-15')
         new Date('FEB, 15, 2013')
         new Date('FEB 15, 2013')
         new Date('February, 15, 2013')
         new Date('February 15, 2013')
         new Date('15 Feb 2013')
         new Date('15, February, 2013')
         // Fri Feb 15 2013 00:00:00 GMT+0800 (CST)
         ```
      
         上面多种日期字符串的写法，返回的都是同一个时间。
  
      3. 参数为年、月、日等多个整数时，年和月是不能省略的，其他参数都可以省略的。也就是说，这时至少需要两个参数，因为如果只使用“年”这一个参数，`Date`会将其解释为毫秒数。
  
         ```js
         new Date(2013)  // Thu Jan 01 1970 08:00:02 GMT+0800 (CST)
         ```
      
         上面代码中，2013被解释为毫秒数，而不是年份。
  
         ```js
         new Date(2013, 0)
         // Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
         new Date(2013, 0, 1)
         // Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
         new Date(2013, 0, 1, 0)
         // Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
         new Date(2013, 0, 1, 0, 0, 0, 0)
         // Tue Jan 01 2013 00:00:00 GMT+0800 (CST)
         ```
      
         上面代码中，不管有几个参数，返回的都是2013年1月1日零点。
  
      4. 各个参数的取值范围如下。
  
         - 年：使用四位数年份，比如`2000`。如果写成两位数或个位数，则加上`1900`，即`10`代表1910年。如果是负数，表示公元前。
         - 月：`0`表示一月，依次类推，`11`表示12月。
         - 日：`1`到`31`。
         - 小时：`0`到`23`。
         - 分钟：`0`到`59`。
         - 秒：`0`到`59`
         - 毫秒：`0`到`999`。
      
         注意，**月份从`0`开始计算**，但是，天数从`1`开始计算。另外，除了日期的默认值为`1`，小时、分钟、秒钟和毫秒的默认值都是`0`。
         
         **这些参数如果超出了正常范围，会被自动折算**。比如，如果月设为`15`，就折算为下一年的4月。
         
         ```js
         new Date(2013, 15)
         // Tue Apr 01 2014 00:00:00 GMT+0800 (CST)
         new Date(2013, 0, 0)
         // Mon Dec 31 2012 00:00:00 GMT+0800 (CST)
         ```
         
         上面代码的第二个例子，日期设为`0`，就代表上个月的最后一天。
         
         参数还可以使用负数，表示扣去的时间。
         
         ```js
         new Date(2013, -1)
         // Sat Dec 01 2012 00:00:00 GMT+0800 (CST)
         new Date(2013, 0, -1)
         // Sun Dec 30 2012 00:00:00 GMT+0800 (CST)
         ```
         
         上面代码中，分别对月和日使用了负数，表示从基准日扣去相应的时间。
      
    - ##### 日期的运算
  
      类型自动转换时，`Date`实例如果转为数值，则等于对应的毫秒数；如果转为字符串，则等于对应的日期字符串。所以，**两个日期实例对象进行减法运算时，返回的是它们间隔的毫秒数；进行加法运算时，返回的是两个字符串连接而成的新字符串。**
  
      ```js
      var d1 = new Date(2000, 2, 1);
      var d2 = new Date(2000, 3, 1);
      
      d2 - d1
      // 2678400000
      d2 + d1
      // "Sat Apr 01 2000 00:00:00 GMT+0800 (CST)Wed Mar 01 2000 00:00:00 GMT+0800 (CST)"
      ```
  
    - ##### 静态方法
  
      - `Date.now()`：返回当前时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数，相当于 Unix 时间戳乘以1000。
  
        ```js
        Date.now() // 1364026285194
        ```
  
      - `Date.parse()`：该方法用来解析日期字符串，返回该时间距离时间零点（1970年1月1日 00:00:00）的毫秒数（`number`类型）。如果解析失败，返回`NaN`。
  
        日期字符串应该符合 RFC 2822 和 ISO 8061 这两个标准，即`YYYY-MM-DDTHH:mm:ss.sssZ`格式，其中最后的`Z`表示时区。但是，其他格式也可以被解析，请看下面的例子。
  
        ```js
        Date.parse('Aug 9, 1995')
        Date.parse('January 26, 2011 13:51:50')
        Date.parse('Mon, 25 Dec 1995 13:30:00 GMT')
        Date.parse('Mon, 25 Dec 1995 13:30:00 +0430')
        Date.parse('2011-10-10')
        Date.parse('2011-10-10T14:48:00')
        ```
  
        上面的日期字符串都可以解析。
  
      - `Date.UTC()`：该方法接受年、月、日等变量作为参数，返回该时间距离时间零点（1970年1月1日 00:00:00 UTC）的毫秒数。
  
        ```js
        // 格式
        Date.UTC(year, month[, date[, hrs[, min[, sec[, ms]]]]])
        
        // 用法
        Date.UTC(2011, 0, 1, 2, 3, 4, 567)
        // 1293847384567
        ```
  
        该方法的参数的用法与`Date`构造函数完全一致，比如月从`0`开始计算，日期从`1`开始计算。区别在于`Date.UTC`方法的参数，会被解释为 UTC 时间（世界标准时间），**`Date`构造函数的参数会被解释为当前时区的时间**。
  
    - ##### 实例方法
  
      - `valueOf()`：返回实例对象距离时间零点（1970年1月1日00:00:00 UTC）对应的毫秒数（`number`类型），该方法等同于`getTime`方法。
  
        ```js
        var d = new Date();
        
        d.valueOf() // 1362790014817
        d.getTime() // 1362790014817
        ```
  
        预期为数值的场合，`Date`实例会自动调用该方法，所以可以用下面的方法计算时间的间隔。
  
        ```js
        var start = new Date();
        // ...
        var end = new Date();
        var elapsed = end - start;
        ```
  
      - ###### to 类方法
  
        从`Date`对象返回一个日期和时间字符串。
  
        - `Date.prototype.toString()`：`toString`方法返回一个完整的日期字符串。
  
          ```js
          var d = new Date(2013, 0, 1);
          
          d.toString()
          // "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"
          d
          // "Tue Jan 01 2013 00:00:00 GMT+0800 (CST)"
          ```
  
          因为`toString`是默认的调用方法，所以如果直接读取`Date`实例，就相当于调用这个方法。
  
        - `Date.prototype.toUTCString()`：返回对应的 UTC 时间，也就是比北京时间晚8个小时。
  
          ```js
          var d = new Date(2013, 0, 1);
          
          d.toUTCString()
          // "Mon, 31 Dec 2012 16:00:00 GMT"
          ```
          
        - `Date.prototype.toISOString()`：返回对应时间的 ISO8601 写法。
  
          ```js
        var d = new Date(2013, 0, 1);
          
          d.toISOString()
          // "2012-12-31T16:00:00.000Z"
          ```
          
          注意，`toISOString`方法返回的总是 UTC 时区的时间。
      
        - `Date.prototype.toJSON()`：返回一个符合 JSON 格式的 ISO 日期字符串，与`toISOString`方法的返回结果完全相同。
  
          ```js
          var d = new Date(2013, 0, 1);
          
          d.toJSON()
          // "2012-12-31T16:00:00.000Z"
          ```
      
          大部分时候，`toJSON()`和`toISOString()`是完全相同的，只是它们对非法日期的处理略有不同。
      
          并且严格来说，它们的设计目的和底层行为存在关键区别：
      
          - `toISOString()`：专门用于**生成 ISO 8601 格式字符串**，是核心功能。
          - `toJSON()`：专门用于 **JSON 序列化**，是接口实现。它内部“借用”了 `toISOString()` 来实现。
      
        - `Date.prototype.toDateString()`：返回日期字符串（不含小时、分和秒）。
      
          ```js
          var d = new Date(2013, 0, 1);
          d.toDateString() // "Tue Jan 01 2013"
          ```
          
        - `Date.prototype.toTimeString()`：返回时间字符串（不含年月日）。
      
          ```js
          var d = new Date(2013, 0, 1);
          d.toTimeString() // "00:00:00 GMT+0800 (CST)"
          ```
          
        - **本地时间**：以下3个方法，可以将 Date 实例转为表示本地时间的字符串。
      
          - `Date.prototype.toLocaleString()`：完整的本地时间。
          - `Date.prototype.toLocaleDateString()`：本地日期（不含小时、分和秒）。
          - `Date.prototype.toLocaleTimeString()`：本地时间（不含年月日）。
      
          下面是用法实例。
      
          ```js
          var d = new Date(2013, 0, 1);
          
          d.toLocaleString()
          // 中文版浏览器为"2013/1/1 00:00:00"
          // 英文版浏览器为"1/1/2013 12:00:00 AM"
          
          d.toLocaleDateString()
          // 中文版浏览器为"2013/1/1"
          // 英文版浏览器为"1/1/2013"
          
          d.toLocaleTimeString()
          // 中文版浏览器为"00:00:00"
          // 英文版浏览器为"12:00:00 AM"
          ```
      
          这三个方法都有两个可选的参数。
      
          ```js
          dateObj.toLocaleString([locales[, options]])
          dateObj.toLocaleDateString([locales[, options]])
          dateObj.toLocaleTimeString([locales[, options]])
          ```
      
          这两个参数中，`locales`是一个指定所用语言的字符串，`options`是一个配置对象。下面是`locales`的例子，分别采用`en-US`和`zh-CN`语言设定。
      
          ```js
          var d = new Date(2013, 0, 1);
          
          d.toLocaleString('en-US') // "1/1/2013, 12:00:00 AM"
          d.toLocaleString('zh-CN') // "2013/1/1 00:00:00"
          
          d.toLocaleDateString('en-US') // "1/1/2013"
          d.toLocaleDateString('zh-CN') // "2013/1/1"
          
          d.toLocaleTimeString('en-US') // "12:00:00 AM"
          d.toLocaleTimeString('zh-CN') // "00:00:00"
          ```
      
          `options`配置对象有以下属性。
      
          - `dateStyle`：可能的值为`full`、`long`、`medium`、`short`。
          - `timeStyle`：可能的值为`full`、`long`、`medium`、`short`。
          - `month`：可能的值为`numeric`、`2-digit`、`long`、`short`、`narrow`。
          - `year`：可能的值为`numeric`、`2-digit`。
          - `weekday`：可能的值为`long`、`short`、`narrow`。
          - `day`、`hour`、`minute`、`second`：可能的值为`numeric`、`2-digit`。
          - `timeZone`：可能的值为 IANA 的时区数据库。
          - `timeZoneName`：可能的值为`long`、`short`。
          - `hour12`：24小时周期还是12小时周期，可能的值为`true`、`false`。
      
          下面是用法实例。
      
          ```js
          var d = new Date(2013, 0, 1);
          
          d.toLocaleDateString('en-US', {
            weekday: 'long',
            year: 'numeric',
            month: 'long',
            day: 'numeric'
          })
          // "Tuesday, January 1, 2013"
          
          d.toLocaleDateString('en-US', {
            day: "2-digit",
            month: "long",
            year: "2-digit"
          });
          // "January 01, 13"
          
          d.toLocaleTimeString('en-US', {
            timeZone: 'UTC',
            timeZoneName: 'short'
          })
          // "4:00:00 PM UTC"
          
          d.toLocaleTimeString('en-US', {
            timeZone: 'Asia/Shanghai',
            timeZoneName: 'long'
          })
          // "12:00:00 AM China Standard Time"
          
          d.toLocaleTimeString('en-US', {
            hour12: false
          })
          // "00:00:00"
          
          d.toLocaleTimeString('en-US', {
            hour12: true
          })
          // "12:00:00 AM"
          ```
      
      - ###### get 类方法
      
        `Date`对象提供了一系列`get*`方法，用来获取`Date`对象的日期和时间。
      
        - `getTime()`：返回实例距离1970年1月1日00:00:00的毫秒数，等同于`valueOf`方法。
        - `getDate()`：返回实例对象对应每个月的几号（从1开始）。
        - `getDay()`：返回星期几，星期日为0，星期一为1，以此类推。
        - `getFullYear()`：返回四位的年份。
        - `getMonth()`：返回月份（从0开始）。
        - `getHours()`：返回小时（0-23）。
        - `getMilliseconds()`：返回毫秒（0-999）。
        - `getMinutes()`：返回分钟（0-59）。
        - `getSeconds()`：返回秒（0-59）。
        - `getTimezoneOffset()`：返回当前时间与 UTC 的时区差异，以分钟表示，返回结果考虑到了夏令时因素。
      
        所有这些`get*`方法返回的都是整数，不同方法返回值的范围不一样。
      
        - 分钟和秒：0 到 59
        - 小时：0 到 23
        - 星期：0（星期天）到 6（星期六）
        - 日期：1 到 31
        - 月份：0（一月）到 11（十二月）
      
        ```js
        var d = new Date('January 6, 2013');
        
        d.getDate() // 6
        d.getMonth() // 0
        d.getFullYear() // 2013
        d.getTimezoneOffset() // -480
        ```
      
        上面代码中，最后一行返回`-480`，即 UTC 时间减去当前时间，单位是分钟。`-480`表示 UTC 比当前时间少480分钟，即当前时区比 UTC 早8个小时。
      
        下面是一个例子，计算本年度还剩下多少天。
      
        ```js
        function leftDays() {
          var today = new Date();
          var endYear = new Date(today.getFullYear(), 11, 31, 23, 59, 59, 999);
          var msPerDay = 24 * 60 * 60 * 1000;
          return Math.round((endYear.getTime() - today.getTime()) / msPerDay);
        }
        ```
      
        上面这些`get*`方法返回的都是当前时区的时间，`Date`对象还提供了这些方法对应的 UTC 版本，用来返回 UTC 时间。
      
        - `getUTCDate()`
        - `getUTCFullYear()`
        - `getUTCMonth()`
        - `getUTCDay()`
        - `getUTCHours()`
        - `getUTCMinutes()`
        - `getUTCSeconds()`
        - `getUTCMilliseconds()`
      
        ```js
        var d = new Date('January 6, 2013');
        
        d.getDate() // 6
        d.getUTCDate() // 5
        ```
      
        上面代码中，实例对象`d`表示当前时区（东八时区）的1月6日0点0分0秒，这个时间对于当前时区来说是1月6日，所以`getDate`方法返回6，对于 UTC 时区来说是1月5日，所以`getUTCDate`方法返回5。
      
      - ###### set 类方法
      
        `Date`对象提供了一系列`set*`方法，用来设置`Date`对象的日期和时间。
      
        - `setDate(date)`：设置实例对象对应的每个月的几号（1-31），返回改变后毫秒时间戳。
        - `setFullYear(year [, month, date])`：设置四位年份。
        - `setHours(hour [, min, sec, ms])`：设置小时（0-23）。
        - `setMilliseconds(ms)`：设置毫秒（0-999）。
        - `setMinutes(min [, sec, ms])`：设置分钟（0-59）。
        - `setMonth(month [, date])`：设置月份（0-11）。
        - `setSeconds(sec [, ms])`：设置秒（0-59）。
        - `setTime(milliseconds)`：设置毫秒时间戳。
      
        这些方法基本是跟`get*`方法一一对应的，但是没有`setDay`方法，因为星期几是计算出来的，而不是设置的。另外，需要注意的是，凡是涉及到设置月份，都是从0开始算的，即`0`是1月，`11`是12月。
      
        ```js
        var d = new Date ('January 6, 2013');
        
        d // Sun Jan 06 2013 00:00:00 GMT+0800 (CST)
        d.setDate(9) // 1357660800000
        d // Wed Jan 09 2013 00:00:00 GMT+0800 (CST)
        ```
      
        `set*`方法的参数都会自动折算。以`setDate()`为例，如果参数超过当月的最大天数，则向下一个月顺延，如果参数是负数，表示从上个月的最后一天开始减去的天数。
      
        ```js
        var d1 = new Date('January 6, 2013');
        
        d1.setDate(32) // 1359648000000
        d1 // Fri Feb 01 2013 00:00:00 GMT+0800 (CST)
        
        var d2 = new Date ('January 6, 2013');
        
        d2.setDate(-1) // 1356796800000
        d2 // Sun Dec 30 2012 00:00:00 GMT+0800 (CST)
        ```
      
        上面代码中，`d1.setDate(32)`将日期设为1月份的32号，因为1月份只有31号，所以自动折算为2月1日。`d2.setDate(-1)`表示设为上个月的倒数第二天，即12月30日。
      
        `set`类方法和`get`类方法，可以结合使用，得到相对时间。
      
        ```js
        var d = new Date();
        
        // 将日期向后推1000天
        d.setDate(d.getDate() + 1000);
        // 将时间设为6小时后
        d.setHours(d.getHours() + 6);
        // 将年份设为去年
        d.setFullYear(d.getFullYear() - 1);
        ```
      
        `set*`系列方法除了`setTime()`，都有对应的 UTC 版本，即设置 UTC 时区的时间。
      
        - `setUTCDate()`
        - `setUTCFullYear()`
        - `setUTCHours()`
        - `setUTCMilliseconds()`
        - `setUTCMinutes()`
        - `setUTCMonth()`
        - `setUTCSeconds()`
      
        ```js
        var d = new Date('January 6, 2013');
        d.getUTCHours() // 16
        d.setUTCHours(22) // 1357423200000
        d // Sun Jan 06 2013 06:00:00 GMT+0800 (CST)
        ```
      
        上面代码中，本地时区（东八时区）的1月6日0点0分，是 UTC 时区的前一天下午16点。设为 UTC 时区的22点以后，就变为本地时区的上午6点。
  
  - #### JSON 对象
  
    - ##### JSON 格式
  
      JSON 格式（JavaScript Object Notation 的缩写）是一种标准的、轻量级的“数据交换格式”（就是文本/字符串的规范），2001年由 Douglas Crockford 提出，目的是取代繁琐笨重的 XML 格式。
    
      在实际的开发中，有两种数据交换格式用的最多：
    
      1. JSON
      2. XML：缺点是体积较大、解析麻烦。优点是语法严谨。通常银行的数据交换会用XML。HTML和XML有一个父亲：SGML（标准通用的标记语言）。HTML主要做页面展示，所以语法松散，很随意。XML主要做数据存储和数据描述的，所以语法相当严格。XML文件可以用浏览器打开，验证是否有语法错误（浏览器打开如果显示的是xml的代码就没问题）。XML也常用来做程序的配置文件（重结构轻数据）。
    
      相比 XML 格式，JSON 格式有两个显著的优点：体积小、易解析，书写简单，一目了然；且符合 JS 的原生语法，可以由解释引擎直接处理，不用另外添加解析代码。所以，JSON 迅速被接受，已经成为各大网站交换数据的标准格式，并被写入标准。
  
      每个 JSON 都是一个值（不能是两个或更多的值），JSON 对值的类型和格式有严格的规定：
    
      1. JSON 的格式和 JS 对象的格式很相似，都是`{ key:value,... }`的格式。不同的是，JSON 的 key 必须是字符串且必须加**双引号**（单引号、飘号都不行），value 可以是 JS 中的：`string、number、boolean、null、数组[]、对象{}`。
    
         注意：
    
         1. 数组或对象的最后一个成员的后面，不能有多余的逗号。
         2. 其中`number`类型必须用十进制表示，且`NaN`, `Infinity`, `-Infinity`和`undefined`都不是合法的 JSON 值。
    
      2. **一个合法的JSON 数据必须以`{}/[]`作为最外层**。由于 JSON 值可以是 JS 对象和 JS 数组，因此 JSON 可以表示复杂的树形结构。
    
      3. 标准的`.json`文件是不支持注释的，并且为了保证各个环境下的兼容性，`.json`文件的最外层都应该是对象`{}`。另外`.json`文件中的换行和空格不会有任何影响，仅仅是方便阅读。
    
      以下都是合法的 JSON：
    
      ```json
      ["one", "two", "three"]
      
      { "one": 1, "two": 2, "three": 3 }
      
      {"names": ["张三", "李四"] }
      
      [ { "name": "张三"}, {"name": "李四"} ]
      ```
    
      以下都是不合法的 JSON：
    
      ```json
      { name: "张三", 'age': 32 }  // 属性名必须使用双引号
      
      [32, 64, 128, 0xFFF] // 不能使用十六进制值
      
      { "name": "张三", "age": undefined } // 不能使用 undefined
      
      {
        "name": "张三",
        "birthday": new Date('Fri, 26 Aug 2011 07:13:10 GMT'),
        "getName": function () {
          return this.name;
        }
      } // 属性值不能使用函数和日期对象
      ```
    
      注意，`null`、空数组`[]`和空对象`{}`都是合法的 JSON 值。
    
    - ##### JSON 对象
    
      从数据格式上来看，`JSON`对象仍是合法的 JS 对象。但由于 JSON 只是一个普通的字符串，因此 JS 提供了两个静态方法`JSON.stringify()`和`JSON.parse()`，专门用来处理 JSON 格式的数据。JSON 字符串和 JS 对象可以用这两个方法来进行互相转换。
      
      注意：与大多数全局对象不同，`JSON` 不是一个构造函数。不能将它与 `new` 运算符 一起使用，也不能将 `JSON` 对象作为函数调用。`JSON` 的所有属性和方法都是静态的（就像`Math`一样）。
    
    - ##### `JSON.stringify()`
    
      - ###### 基本用法
    
        `JSON.stringify()`方法用于将一个值转为 JSON 字符串。该字符串符合 JSON 格式，并且可以被`JSON.parse()`方法还原。
        
        ```js
        JSON.stringify('abc') // ""abc""
        JSON.stringify(1) // "1"
        JSON.stringify(false) // "false"
        JSON.stringify([]) // "[]"
        JSON.stringify({}) // "{}"
        
        JSON.stringify([1, "false", false])  // '[1,"false",false]'
        
        JSON.stringify({ name: "张三" })  // '{"name":"张三"}'
        ```
        
        上面代码将各种类型的值，转成 JSON 字符串。
        
        注意，对于原始类型的字符串，转换结果会带双引号。
        
        ```js
        JSON.stringify('foo') === "foo" // false
        JSON.stringify('foo') === "\"foo\"" // true
        ```
        
        上面代码中，字符串`foo`，被转成了`"foo"`（带了双引号）。**这是因为将来还原的时候，内层双引号可以让 JS 引擎知道，这是一个字符串，而不是其他类型的值**。
        
        ```js
        JSON.stringify(false) // false
        JSON.stringify('false') // "false"
        ```
        
        上面代码中，如果不是内层的双引号，将来还原的时候，引擎就无法知道原始值是布尔值还是字符串。
        
        **如果对象的属性是`undefined`、函数或 XML 对象，该属性会被`JSON.stringify()`忽略**。
        
        ```js
        var obj = {
          a: undefined,
          b: function () {}
        };
        
        JSON.stringify(obj) // "{}"
        ```
        
        上面代码中，对象`obj`的`a`属性是`undefined`，而`b`属性是一个函数，结果都被`JSON.stringify`忽略。
        
        **如果数组的成员是`undefined`、函数或 XML 对象，则这些值被转成`null`**。
        
        ```js
        var arr = [undefined, function () {}];
        JSON.stringify(arr) // "[null,null]"
        ```
        
        上面代码中，数组`arr`的成员是`undefined`和函数，它们都被转成了`null`。
        
        **RegExp对象会被转成空对象**。
        
        ```js
        JSON.stringify(/foo/) // "{}"
        ```
        
        **`JSON.stringify()`方法会忽略对象的不可遍历的属性**。
        
        ```js
        var obj = {};
        Object.defineProperties(obj, {
         'foo': {
             value: 1,
               enumerable: true
            },
            'bar': {
               value: 2,
               enumerable: false
            }
        });
        
        JSON.stringify(obj); // "{"foo":1}"
        ```
        
        上面代码中，`bar`是`obj`对象的不可遍历属性，`JSON.stringify`方法会忽略这个属性。
        
      - ###### 第二个参数
    
        `JSON.stringify()`方法还可以接受一个数组作为第二个参数，指定参数对象的哪些属性需要转成字符串。
        
        ```js
        var obj = {
          'prop1': 'value1',
          'prop2': 'value2',
          'prop3': 'value3'
        };
        
        JSON.stringify(obj, ['prop1', 'prop2'])  // "{"prop1":"value1","prop2":"value2"}"
        ```
        
        上面代码中，`JSON.stringify()`方法的第二个参数指定，只转`prop1`和`prop2`两个属性。
        
        **这个类似白名单的数组，只对对象的属性有效，对数组无效**。
        
        ```js
        JSON.stringify(['a', 'b'], ['0'])
        // "["a","b"]"
        
        JSON.stringify({0: 'a', 1: 'b'}, ['0'])
        // "{"0":"a"}"
        ```
        
        上面代码中，第二个参数指定 JSON 格式只转`0`号属性，实际上对数组是无效的，只对对象有效。
        
        第二个参数还可以是一个函数，用来更改`JSON.stringify()`的返回值。
        
        ```js
        function f(key, value) {
          if (typeof value === "number") {
            value = 2 * value;
          }
          return value;
        }
        
        JSON.stringify({ a: 1, b: 2 }, f)  // '{"a": 2,"b": 4}'
        ```
        
        上面代码中的`f`函数，接受两个参数，分别是被转换的对象的键名和键值。如果键值是数值，就将它乘以`2`，否则就原样返回。
        
        注意，这个处理函数是递归处理所有的键。
        
        ```js
        var obj = {a: {b: 1}};
        
        function f(key, value) {
          console.log("["+ key +"]:" + value);
          return value;
        }
        
        JSON.stringify(obj, f) // '{"a":{"b":1}}'
        // []:[object Object]
        // [a]:[object Object]
        // [b]:1
        ```
        
        上面代码中，对象`obj`一共会被`f`函数处理三次。第一次键名为空，键值是整个对象`obj`；第二次键名为`a`，键值是`{b: 1}`；第三次键名为`b`，键值为1。
        
        递归处理中，每一次处理的对象，都是前一次返回的值。
        
        ```js
        var obj = {a: 1};
        
        function f(key, value) {
          if (typeof value === 'object') {
            return {b: 2};
          }
          return value * 2;
        }
        
        JSON.stringify(obj, f)  // "{"b": 4}"
        ```
        
        上面代码中，`f`函数修改了对象`obj`，接着`JSON.stringify()`方法就递归处理修改后的对象`obj`。
        
        **如果处理函数返回`undefined`或没有返回值，则该属性会被忽略**。
        
        ```js
        function f(key, value) {
          if (typeof(value) === "string") {
            return undefined;
          }
          return value;
        }
        
        JSON.stringify({ a: "abc", b: 123 }, f)  // '{"b": 123}'
        ```
        
        上面代码中，`a`属性经过处理后，返回`undefined`，于是该属性被忽略了。
        
      - ###### 第三个参数
      
        `JSON.stringify()`还可以接受第三个参数，用于增加返回的 JSON 字符串的可读性。
        
        默认返回的是单行字符串，对于大型的 JSON 对象，可读性非常差。第三个参数使得每个属性单独占据一行，并且可以给每个属性前面添加指定的前缀（不超过10个字符）。
        
        ```js
        // 默认输出
        JSON.stringify({ p1: 1, p2: 2 })
        // JSON.stringify({ p1: 1, p2: 2 })
        
        // 分行输出
        JSON.stringify({ p1: 1, p2: 2 }, null, '\t')
        /*
        {
          "p1": 1,
          "p2": 2
        }
        */
        ```
        
        上面例子中，第三个属性`\t`在每个属性前面添加一个制表符，然后分行显示。
        
        第三个属性如果是一个数字，则表示每个属性前面添加的空格数量（最多不超过10个）。
        
        ```js
        JSON.stringify({ p1: 1, p2: 2 }, null, 2);
        /*
        {
          "p1": 1,
          "p2": 2
        }
        */
        ```
        
      - ###### 参数对象的 `toJSON()` 方法
      
        如果被转换的 JS 对象中自定义了一个名为`toJSON`的方法，那么`JSON.stringify()`会使用这个方法的返回值作为参数，而忽略原对象的其他属性。该方法用来给那些不能被转换为 JSON 的类型（如`Date`），指定转换后的 JSON 值。
        
        下面是一个普通的对象。
        
        ```js
        var user = {
          firstName: '三',
          lastName: '张',
        
          get fullName(){
            return this.lastName + this.firstName;
          },
        };
        
        JSON.stringify(user)
        // "{"firstName":"三","lastName":"张","fullName":"张三"}"
        ```
        
        现在，为这个对象加上`toJSON()`方法。
        
        ```js
        var user = {
          firstName: '三',
          lastName: '张',
        
          get fullName(){
            return this.lastName + this.firstName;
          },
        
          toJSON: function () {
            return {
              name: this.lastName + this.firstName
            };
          },
        };
        
        JSON.stringify(user)  // "{"name":"张三"}"
        ```
        
        上面代码中，`JSON.stringify()`发现参数对象有`toJSON()`方法，就直接使用这个方法的返回值作为参数，而忽略原对象的其他参数。
        
        `Date`对象就有一个自己的`toJSON()`方法。
        
        ```js
        var date = new Date('2015-01-01');
        date.toJSON() // "2015-01-01T00:00:00.000Z"
        JSON.stringify(date) // ""2015-01-01T00:00:00.000Z""
        ```
        
        上面代码中，`JSON.stringify()`发现处理的是`Date`对象实例，就会调用这个实例对象的`toJSON()`方法，将该方法的返回值作为参数。
        
        `toJSON()`方法的一个应用是，将正则对象自动转为字符串。因为`JSON.stringify()`默认不能转换正则对象，但是设置了`toJSON()`方法以后，就可以转换正则对象了。
        
        ```js
        var obj = {
          reg: /foo/
        };
        
        // 不设置 toJSON 方法时
        JSON.stringify(obj) // "{"reg":{}}"
        
        // 设置 toJSON 方法时
        RegExp.prototype.toJSON = RegExp.prototype.toString;
        JSON.stringify(/foo/) // ""/foo/""
        ```
        
        上面代码在正则对象的原型上面部署了`toJSON()`方法，将其指向`toString()`方法，因此转换成 JSON 格式时，正则对象就先调用`toJSON()`方法转为字符串，然后再被`JSON.stringify()`方法处理。
      
    - ##### `JSON.parse()`
    
      `JSON.parse()`可以将 JSON 字符串转换成 JS 对象。
      
      ```js
      JSON.parse('{}') // {}
      JSON.parse('true') // true
      JSON.parse('"foo"') // "foo"
      JSON.parse('[1, 5, "false"]') // [1, 5, "false"]
      JSON.parse('null') // null
      
      var o = JSON.parse('{"name": "张三"}');
      o.name // 张三
      ```
      
      如果传入的字符串不是有效的 JSON 格式，`JSON.parse()`方法将报错。
      
      ```js
      JSON.parse("'String'") // illegal single quotes
      // SyntaxError: Unexpected token ILLEGAL
      ```
      
      上面代码中，双引号字符串中是一个单引号字符串，因为单引号字符串不符合 JSON 格式，所以报错。
      
      为了处理解析错误，可以将`JSON.parse()`方法放在`try...catch`代码块中。
      
      ```js
      try {
        JSON.parse("'String'");
      } catch(e) {
        console.log('parsing error');
      }
      ```
      
      `JSON.parse()`方法的第二个参数可以接受一个**处理函数**，用法与`JSON.stringify()`方法类似。
      
      ```js
      function f(key, value) {
        if (key === 'a') {
          return value + 10;
        }
        return value;
      }
      
      JSON.parse('{"a": 1, "b": 2}', f)
      // {a: 11, b: 2}
      ```
      
      上面代码中，`JSON.parse()`的第二个参数是一个函数，如果键名是`a`，该函数会将键值加上10。
      
      `JSON.parse()`和`JSON.stringify()`可以结合使用来实现简单的**对象深拷贝**：`JSON.parse(JSON.stringify(obj))`。
      
      这种写法虽然巧妙，但是有限制，要求对象内部不能有 JSON 不支持的数据类型，比如函数、正则对象、日期对象等。

------

