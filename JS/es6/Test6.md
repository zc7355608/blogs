- ## Proxy

  - #### 概述

    Proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。（不理解的话先忽略这句话）

    Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

    ```js
    var obj = new Proxy({}, {
      get: function (target, propKey, receiver) {
        console.log(`getting ${propKey}!`);
        return Reflect.get(target, propKey, receiver);
      },
      set: function (target, propKey, value, receiver) {
        console.log(`setting ${propKey}!`);
        return Reflect.set(target, propKey, value, receiver);
      }
    });
    ```

    上面代码对一个空对象（目标对象）架设了一层拦截，返回了空对象的代理对象。之后再对该空对象的访问和修改，都通过这个代理对象来做。这样代理对象对目标对象进行访问和修改时，就可以进行一些额外的操作。这里暂时先不解释具体的语法，只看运行结果。对设置了拦截行为的对象`obj`，去读写它的属性，就会得到下面的结果。

    ```js
    obj.count = 1
    //  setting count!
    ++obj.count
    //  getting count!
    //  setting count!
    //  2
    ```

    上面代码说明，Proxy 实际上重载（overload）了点运算符，即用自己的定义覆盖了语言的原始定义。

    ES6 原生提供 Proxy 构造函数，用来生成 Proxy 实例。

    ```js
    var proxy = new Proxy(target, handler);
    ```

    **Proxy 对象的所有用法，都是上面这种形式，不同的只是`handler`参数的写法**。其中，`new Proxy()`表示生成一个`Proxy`实例，`target`参数表示所要拦截的目标对象，**`handler`参数也是一个对象，用来定制拦截行为。**

    下面是另一个拦截读取属性行为的例子。

    ```js
    var proxy = new Proxy({}, {
      get: function(target, propKey) {  // 分别是目标对象和所要访问的属性
        return 35;
      }
    });
    
    proxy.time // 35
    proxy.name // 35
    proxy.title // 35
    ```

    上面代码中，作为构造函数，`Proxy`接受两个参数。第一个参数是所要代理的目标对象（上例是一个空对象），即如果没有`Proxy`的介入，操作原来要访问的就是这个对象；第二个参数是一个配置对象，对于每一个被代理的操作，需要提供一个对应的处理函数，该函数将拦截对应的操作。比如，上面代码中，配置对象有一个`get`方法，用来拦截对目标对象属性的访问请求。**`get`方法的两个参数分别是目标对象和所要访问的属性**。可以看到，由于拦截函数总是返回`35`，所以访问任何属性都得到`35`。

    注意，要使得`Proxy`起作用，必须针对`Proxy`实例（上例是`proxy`对象）进行操作，而不是针对目标对象（上例是空对象）进行操作。

    如果`handler`中没有设置任何拦截，那就等同于直接通向原对象。

    ```js
    var target = {};
    var handler = {};
    var proxy = new Proxy(target, handler);
    proxy.a = 'b';
    target.a // "b"
    ```

    上面代码中，`handler`是一个空对象，没有任何拦截效果，访问`proxy`就等同于访问`target`。

    一个技巧是将 Proxy 对象，设置到`object.proxy`属性，从而可以在`object`对象上调用。

    ```js
    const target = { name: "Alice" }; // 目标对象
    const handler = {
      get(target, prop) {
        console.log(`读取属性: ${prop}`);
        return target[prop];
      }
    };
    
    // 将 Proxy 实例挂载到 object.proxy
    const object = { 
      proxy: new Proxy(target, handler) 
    };
    
    // 通过 object.proxy 触发代理逻辑
    console.log(object.proxy.name); 
    // 输出: 
    // 读取属性: name
    // Alice
    ```

    Proxy 实例也可以作为其他对象的原型对象。

    ```js
    var proxy = new Proxy({}, {
      get: function(target, propKey) {
        return 35;
      }
    });
    
    let obj = Object.create(proxy);
    obj.time // 35
    ```

    上面代码中，`proxy`对象是`obj`对象的原型，`obj`对象本身并没有`time`属性，所以根据原型链，会在`proxy`对象上读取该属性，导致被拦截。

    同一个拦截器函数，可以设置拦截多个操作。

    ```js
    var handler = {
      get: function(target, name) {
        if (name === 'prototype') {
          return Object.prototype;
        }
        return 'Hello, ' + name;
      },
    
      // 当作普通函数调用时
      apply: function(target, thisBinding, args) {
        return args[0];
      },
    
      // 当作构造器调用时
      construct: function(target, args) {
        return {value: args[1]};
      }
    };
    
    var fproxy = new Proxy(function(x, y) {
      return x + y;
    }, handler);
    
    fproxy(1, 2) // 1
    new fproxy(1, 2) // {value: 2}
    fproxy.prototype === Object.prototype // true
    fproxy.foo === "Hello, foo" // true
    ```

    **对于可以设置、但没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果**。

    下面是 Proxy 支持的拦截操作一览，一共 13 种：

    - `get(target, propKey, receiver)`：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。

    - `set(target, propKey, value, receiver)`：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。

    - `apply(target, object, args)`：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。

    - `construct(target, args)`：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。

    - `has(target, propKey)`：拦截`HasProperty`操作，即判断对象是否具有某个属性（包括原型链）。它返回一个布尔值，表示是否包含。

    - `deleteProperty(target, propKey)`：拦截`delete`操作符（`delete proxy[propKey]`）的操作，返回一个布尔值。

    - `getPrototypeOf(target)`：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。

    - `setPrototypeOf(target, proto)`：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。

    - `defineProperty(target, propKey, propDesc)`：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。

    - `ownKeys(target)`：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象自身所有的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。

    - `getOwnPropertyDescriptor(target, propKey)`：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。

    - `preventExtensions(target)`：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。

    - `isExtensible(target)`：拦截`Object.isExtensible(proxy)`，返回一个布尔值。

      > 为什么 `seal/freeze` 不能被拦截？
      >
      > 核心原因在于 **`Object.seal()` 和 `Object.freeze()` 在规范层面被定义为“组合操作”**，它们内部会先调用 `preventExtensions`，然后再设置属性的 `configurable` 和 `writable` 特性。（seal是设置 configurable 为 false，freeze是将 configurable 和 writable 都设为 false）

  - #### `Proxy` 的拦截方法

    下面是上面这些拦截方法的详细介绍。

    - `get(target, propKey, receiver)`：`get`方法用于拦截某个属性的读取操作，可以接受三个参数，依次为目标对象、属性名和 proxy 实例本身（严格地说，是操作行为所针对的对象），其中最后一个参数可选。

      `get`方法的用法，上文已经有一个例子，下面是另一个拦截读取操作的例子。

      ```js
      var person = {
        name: "张三"
      };
      
      var proxy = new Proxy(person, {
        get: function(target, propKey) {
          if (propKey in target) {
            return target[propKey];
          } else {
            throw new ReferenceError("Prop name \"" + propKey + "\" does not exist.");
          }
        }
      });
      
      proxy.name // "张三"
      proxy.age // 抛出一个错误
      ```

      上面代码表示，如果访问目标对象不存在的属性，会抛出一个错误。如果没有这个拦截函数，访问不存在的属性，只会返回`undefined`。

      `get`方法可以继承。

      ```js
      let proto = new Proxy({}, {
        get(target, propertyKey, receiver) {
          console.log('GET ' + propertyKey);
          return target[propertyKey];
        }
      });
      
      let obj = Object.create(proto);
      obj.foo // "GET foo"
      ```

      上面代码中，拦截操作定义在`Prototype`对象上面，所以如果读取`obj`对象继承的属性时，拦截会生效。

      下面的例子使用`get`拦截，实现数组读取负数的索引。

      ```js
      function createArray(...elements) {
        let handler = {
          get(target, propKey, receiver) {
            let index = Number(propKey);
            if (index < 0) {
              propKey = String(target.length + index);
            }
            return Reflect.get(target, propKey, receiver);
          }
        };
      
        let target = [];
        target.push(...elements);
        return new Proxy(target, handler);
      }
      
      let arr = createArray('a', 'b', 'c');
      arr[-1] // c
      ```

      上面代码中，数组的位置参数是`-1`，就会输出数组的倒数第一个成员。

      利用 Proxy，可以将读取属性的操作（`get`），转变为执行某个函数，从而实现属性的链式操作。

      ```js
      var pipe = function (value) {
        var funcStack = [];
        var oproxy = new Proxy({} , {
          get : function (pipeObject, fnName) {
            if (fnName === 'get') {
              return funcStack.reduce(function (val, fn) {
                return fn(val);
              },value);
            }
            funcStack.push(window[fnName]);
            return oproxy;
          }
        });
      
        return oproxy;
      }
      
      var double = n => n * 2;
      var pow    = n => n * n;
      var reverseInt = n => n.toString().split("").reverse().join("") | 0;
      
      pipe(3).double.pow.reverseInt.get; // 63
      ```

      上面代码设置 Proxy 以后，达到了将函数名链式使用的效果。

      下面的例子则是利用`get`拦截，实现一个生成各种 DOM 节点的通用函数`dom`。

      ```js
      const dom = new Proxy({}, {
        get(target, property) {
          return function(attrs = {}, ...children) {
            const el = document.createElement(property);
            for (let prop of Object.keys(attrs)) {
              el.setAttribute(prop, attrs[prop]);
            }
            for (let child of children) {
              if (typeof child === 'string') {
                child = document.createTextNode(child);
              }
              el.appendChild(child);
            }
            return el;
          }
        }
      });
      
      const el = dom.div({},
        'Hello, my name is ',
        dom.a({href: '//example.com'}, 'Mark'),
        '. I like:',
        dom.ul({},
          dom.li({}, 'The web'),
          dom.li({}, 'Food'),
          dom.li({}, '…actually that\'s it')
        )
      );
      
      document.body.appendChild(el);
      ```

      下面是一个`get`方法的第三个参数的例子，它总是指向原始的读操作所在的那个对象，一般情况下就是 Proxy 实例。

      ```js
      const proxy = new Proxy({}, {
        get: function(target, key, receiver) {
          return receiver;
        }
      });
      proxy.getReceiver === proxy // true
      ```

      上面代码中，`proxy`对象的`getReceiver`属性会被`get()`拦截，得到的返回值就是`proxy`对象。

      ```js
      const proxy = new Proxy({}, {
        get: function(target, key, receiver) {
          return receiver;
        }
      });
      
      const d = Object.create(proxy);
      d.a === d // true
      ```

      上面代码中，`d`对象本身没有`a`属性，所以读取`d.a`的时候，会去`d`的原型`proxy`对象找。这时，`receiver`就指向`d`，代表原始的读操作所在的那个对象。

      **如果目标对象的属性是不可写（writable）且不可配置（configurable）的，那么 `get` 代理后返回的值必须与该属性的原始值严格相等**。否则通过 Proxy 对象访问该属性会报错。

      ```js
      const target = Object.defineProperties({}, {
        foo: {
          value: 123,
          writable: false,
          configurable: false
        },
      });
      
      const handler = {
        get(target, propKey) {
          return 'abc';
        }
      };
      
      const proxy = new Proxy(target, handler);
      
      proxy.foo
      // TypeError: Invariant check failed
      ```

    - `set(target, propKey, value, receiver)`：`set`方法用来拦截某个属性的赋值操作，可以接受四个参数，依次为目标对象、属性名、属性值和 Proxy 实例本身，其中最后一个参数可选。

      假定`Person`对象有一个`age`属性，该属性应该是一个不大于 200 的整数，那么可以使用`Proxy`保证`age`的属性值符合要求。

      ```js
      let validator = {
        set: function(obj, prop, value) {
          if (prop === 'age') {
            if (!Number.isInteger(value)) {
              throw new TypeError('The age is not an integer');
            }
            if (value > 200) {
              throw new RangeError('The age seems invalid');
            }
          }
      
          // 对于满足条件的 age 属性以及其他属性，直接保存
          obj[prop] = value;
          return true;
        }
      };
      
      let person = new Proxy({}, validator);
      
      person.age = 100;
      
      person.age // 100
      person.age = 'young' // 报错
      person.age = 300 // 报错
      ```

      上面代码中，由于设置了存值函数`set`，任何不符合要求的`age`属性赋值，都会抛出一个错误，这是数据验证的一种实现方法。利用`set`方法，还可以数据绑定，即每当对象发生变化时，会自动更新 DOM。

      有时，我们会在对象上面设置内部属性，属性名的第一个字符使用下划线开头，表示这些属性不应该被外部使用。结合`get`和`set`方法，就可以做到防止这些内部属性被外部读写。

      ```js
      const handler = {
        get (target, key) {
          invariant(key, 'get');
          return target[key];
        },
        set (target, key, value) {
          invariant(key, 'set');
          target[key] = value;
          return true;
        }
      };
      function invariant (key, action) {
        if (key[0] === '_') {
          throw new Error(`Invalid attempt to ${action} private "${key}" property`);
        }
      }
      const target = {};
      const proxy = new Proxy(target, handler);
      proxy._prop
      // Error: Invalid attempt to get private "_prop" property
      proxy._prop = 'c'
      // Error: Invalid attempt to set private "_prop" property
      ```

      上面代码中，只要读写的属性名的第一个字符是下划线，一律抛错，从而达到禁止读写内部属性的目的。

      下面是`set`方法第四个参数的例子。

      ```js
      const handler = {
        set: function(obj, prop, value, receiver) {
          obj[prop] = receiver;
          return true;
        }
      };
      const proxy = new Proxy({}, handler);
      proxy.foo = 'bar';
      proxy.foo === proxy // true
      ```

      上面代码中，`set`方法的第四个参数`receiver`，指的是原始的操作行为所在的那个对象，一般情况下是`proxy`实例本身，请看下面的例子。

      ```js
      const handler = {
        set: function(target, prop, value, receiver) {
          target[prop] = receiver;
          return true;
        }
      };
      const proxy = new Proxy({}, handler);
      const myObj = {};
      Object.setPrototypeOf(myObj, proxy);
      
      myObj.foo = 'bar';
      myObj.foo === myObj // true
      ```

      上面代码中，设置`myObj.foo`属性的值时，`myObj`并没有`foo`属性，因此引擎会到`myObj`的原型链去找`foo`属性。`myObj`的原型对象`proxy`是一个 Proxy 实例，设置它的`foo`属性会触发`set`方法。这时，第四个参数`receiver`就指向原始赋值行为所在的对象`myObj`。

      注意，**如果目标对象自身的某个属性不可写，那么`set`方法将不起作用**。

      ```js
      const obj = {};
      Object.defineProperty(obj, 'foo', {
        value: 'bar',
        writable: false
      });
      
      const handler = {
        set: function(obj, prop, value, receiver) {
          obj[prop] = value;
          return true;
        }
      };
      
      const proxy = new Proxy(obj, handler);
      proxy.foo = 'baz';
      proxy.foo // "bar"
      ```

      上面代码中，`obj.foo`属性不可写，Proxy 对这个属性的`set`代理将不会生效。

      注意，`set`代理应当返回一个布尔值。**严格模式下，`set`代理如果没有返回`true`，就会报错**。

      ```js
      'use strict';
      const handler = {
        set: function(obj, prop, value, receiver) {
          obj[prop] = receiver;
          // 无论有没有下面这一行，都会报错
          return false;
        }
      };
      const proxy = new Proxy({}, handler);
      proxy.foo = 'bar';
      // TypeError: 'set' on proxy: trap returned falsish for property 'foo'
      ```

      上面代码中，严格模式下，`set`代理返回`false`或者`undefined`，都会报错。

    - `apply(target, object, args)`：`apply`方法拦截函数的调用、`call`和`apply`操作。

      `apply`方法可以接受三个参数，分别是目标对象、目标对象的上下文对象（`this`）和目标对象的参数数组。

      ```js
      var handler = {
        apply (target, ctx, args) {
          return Reflect.apply(...arguments);
        }
      };
      ```

      下面是一个例子。

      ```js
      var target = function () { return 'I am the target'; };
      var handler = {
        apply: function () {
          return 'I am the proxy';
        }
      };
      
      var p = new Proxy(target, handler);
      
      p()
      // "I am the proxy"
      ```

      上面代码中，变量`p`是 Proxy 的实例，当它作为函数调用时（`p()`），就会被`apply`方法拦截，返回一个字符串。

      下面是另外一个例子。

      ```js
      var twice = {
        apply (target, ctx, args) {
          return Reflect.apply(...arguments) * 2;
        }
      };
      function sum (left, right) {
        return left + right;
      };
      var proxy = new Proxy(sum, twice);
      proxy(1, 2) // 6
      proxy.call(null, 5, 6) // 22
      proxy.apply(null, [7, 8]) // 30
      ```

      上面代码中，每当执行`proxy`函数（直接调用或`call`和`apply`调用），就会被`apply`方法拦截。

      另外，直接调用`Reflect.apply`方法，也会被拦截。

      ```js
      Reflect.apply(proxy, null, [9, 10]) // 38
      ```

    - `construct(target, args)`：`construct()`方法用于拦截`new`命令，下面是拦截对象的写法。

      ```js
      const handler = {
        construct (target, args, newTarget) {
          return new target(...args);
        }
      };
      ```
      
      `construct()`方法可以接受三个参数。
      
      - `target`：目标对象。
      - `args`：构造函数的参数数组。
      - `newTarget`：创造实例对象时，`new`命令作用的构造函数（下面例子的`p`）。
      
      ```js
      const p = new Proxy(function () {}, {
        construct: function(target, args) {
          console.log('called: ' + args.join(', '));
          return { value: args[0] * 10 };
        }
      });
      
      (new p(1)).value
      // "called: 1"
      // 10
      ```
      
      **`construct()`方法返回的必须是一个对象，否则会报错**。
      
      ```js
      const p = new Proxy(function() {}, {
        construct: function(target, argumentsList) {
          return 1;
        }
      });
      
      new p() // 报错
      // Uncaught TypeError: 'construct' on proxy: trap returned non-object ('1')
      ```
      
      另外，由于**`construct()`拦截的是构造函数，所以它的目标对象必须是函数**，否则就会报错。
      
      ```js
      const p = new Proxy({}, {
        construct: function(target, argumentsList) {
          return {};
        }
      });
      
      new p() // 报错
      // Uncaught TypeError: p is not a constructor
      ```
      
      上面例子中，拦截的目标对象不是一个函数，而是一个对象（`new Proxy()`的第一个参数），导致报错。
      
      注意，**`construct()`方法中的`this`指向的是`handler`**，而不是实例对象。
      
      ```js
      const handler = {
        construct: function(target, args) {
          console.log(this === handler);
          return new target(...args);
        }
      }
      
      let p = new Proxy(function () {}, handler);
      new p() // true
      ```
      
    - `has(target, propKey)`：`has()`方法用来拦截`HasProperty`操作，即判断对象是否具有某个属性时（包括原型链），这个方法会生效。典型的操作就是`in`运算符。`has()`方法可以接受两个参数，分别是目标对象、需查询的属性名。

      下面的例子使用`has()`方法隐藏某些属性，不被`in`运算符发现。

      ```js
    var handler = {
        has (target, key) {
          if (key[0] === '_') {
            return false;
          }
          return key in target;
        }
      };
      var target = { _prop: 'foo', prop: 'foo' };
      var proxy = new Proxy(target, handler);
      '_prop' in proxy // false
      ```
      
      上面代码中，如果原对象的属性名的第一个字符是下划线，`proxy.has()`就会返回`false`，从而不会被`in`运算符发现。

      **如果原对象不可配置或者禁止扩展，这时`has()`拦截会报错**。

      ```js
    var obj = { a: 10 };
      Object.preventExtensions(obj);
      
      var p = new Proxy(obj, {
        has: function(target, prop) {
          return false;
        }
      });
      
      'a' in p // TypeError is thrown
      ```
      
      上面代码中，`obj`对象禁止扩展，结果使用`has`拦截就会报错。也就是说，如果某个属性不可配置（或者目标对象不可扩展），则`has()`方法就不得“隐藏”（即返回`false`）目标对象的该属性。

      值得注意的是，`has()`方法拦截的是`HasProperty`操作，而不是`HasOwnProperty`操作，即`has()`方法不判断一个属性是对象自身的属性，还是继承的属性。

      另外，虽然`for...in`循环用到了`in`运算符，但是`has()`拦截对`for...in`循环不生效。

      ```js
    let stu1 = {name: '张三', score: 59};
      let stu2 = {name: '李四', score: 99};
      
      let handler = {
        has(target, prop) {
          if (prop === 'score' && target[prop] < 60) {
            console.log(`${target.name} 不及格`);
            return false;
          }
          return prop in target;
        }
      }
      
      let oproxy1 = new Proxy(stu1, handler);
      let oproxy2 = new Proxy(stu2, handler);
      
      'score' in oproxy1
      // 张三 不及格
      // false
      
      'score' in oproxy2
      // true
      
      for (let a in oproxy1) {
        console.log(oproxy1[a]);
      }
      // 张三
      // 59
      
      for (let b in oproxy2) {
        console.log(oproxy2[b]);
      }
      // 李四
      // 99
      ```
      
      上面代码中，`has()`拦截只对`in`运算符生效，对`for...in`循环不生效，导致不符合要求的属性没有被`for...in`循环所排除。

    - `deleteProperty(target, propKey)`：`deleteProperty`方法用于拦截`delete`操作，如果这个方法抛出错误或者返回`false`，当前属性就无法被`delete`命令删除。

      ```js
      var handler = {
        deleteProperty (target, key) {
          invariant(key, 'delete');
          delete target[key];
          return true;
        }
      };
      function invariant (key, action) {
        if (key[0] === '_') {
          throw new Error(`Invalid attempt to ${action} private "${key}" property`);
        }
      }
      
      var target = { _prop: 'foo' };
      var proxy = new Proxy(target, handler);
      delete proxy._prop
      // Error: Invalid attempt to delete private "_prop" property
      ```

      上面代码中，`deleteProperty`方法拦截了`delete`操作符，删除第一个字符为下划线的属性会报错。

      注意，目标对象自身的不可配置（configurable）的属性，不能被`deleteProperty`方法删除，否则报错。

    - `getPrototypeOf(target)`：`getPrototypeOf()`方法主要用来拦截获取对象原型。具体来说，拦截下面这些操作。

      - `Object.prototype.__proto__`
      - `Object.prototype.isPrototypeOf()`
      - `Object.getPrototypeOf()`
      - `Reflect.getPrototypeOf()`
      - `instanceof`

      下面是一个例子。

      ```js
      var proto = {};
      var p = new Proxy({}, {
        getPrototypeOf(target) {
          return proto;
        }
      });
      Object.getPrototypeOf(p) === proto // true
      ```

      上面代码中，`getPrototypeOf()`方法拦截`Object.getPrototypeOf()`，返回`proto`对象。

      注意：

      1. `getPrototypeOf()`方法的返回值必须是对象或者`null`，否则报错。
      2. 如果目标对象不可扩展（non-extensible）， `getPrototypeOf()`方法必须返回目标对象的原型对象。

    - `setPrototypeOf(target, proto)`：`setPrototypeOf()`方法主要用来拦截`Object.setPrototypeOf()`方法。

      下面是一个例子。

      ```js
      var handler = {
        setPrototypeOf (target, proto) {
          throw new Error('Changing the prototype is forbidden');
        }
      };
      var proto = {};
      var target = function () {};
      var proxy = new Proxy(target, handler);
      Object.setPrototypeOf(proxy, proto);
      // Error: Changing the prototype is forbidden
      ```

      上面代码中，只要修改`target`的原型对象，就会报错。

      注意：

      1. 该方法只能返回布尔值，否则会被自动转为布尔值。
      2. 如果目标对象不可扩展（non-extensible），`setPrototypeOf()`方法不得改变目标对象的原型。

    - `defineProperty(target, propKey, propDesc)`：该方法拦截了`Object.defineProperty()`操作。

      ```js
      var handler = {
        defineProperty (target, key, descriptor) {
          return false;
        }
      };
      var target = {};
      var proxy = new Proxy(target, handler);
      proxy.foo = 'bar' // 不会生效
      ```

      上面代码中，`defineProperty()`方法内部没有任何操作，只返回`false`，导致添加新属性总是无效。

      注意：

      1. 如果目标对象不可扩展（non-extensible），则`defineProperty()`不能增加目标对象上不存在的属性，否则会报错。
      2. 如果目标对象的某个属性不可写（writable）或不可配置（configurable），则`defineProperty()`方法不得改变这两个设置。

    - `ownKeys(target)`：`ownKeys()`方法用来拦截对象自身属性的读取操作。具体来说，拦截以下操作。

      - `Object.getOwnPropertyNames()`
      - `Object.getOwnPropertySymbols()`
      - `Object.keys()`
      - `for...in`循环

      下面是拦截`Object.keys()`的例子。

      ```js
      let target = {
        a: 1,
        b: 2,
        c: 3
      };
      
      let handler = {
        ownKeys(target) {
          return ['a'];
        }
      };
      
      let proxy = new Proxy(target, handler);
      
      Object.keys(proxy)
      // [ 'a' ]
      ```

      上面代码拦截了对于`target`对象的`Object.keys()`操作，只返回`a`、`b`、`c`三个属性之中的`a`属性。

      下面的例子是拦截第一个字符为下划线的属性名。

      ```js
      let target = {
        _bar: 'foo',
        _prop: 'bar',
        prop: 'baz'
      };
      
      let handler = {
        ownKeys (target) {
          return Reflect.ownKeys(target).filter(key => key[0] !== '_');
        }
      };
      
      let proxy = new Proxy(target, handler);
      for (let key of Object.keys(proxy)) {
        console.log(target[key]);
      }
      // "baz"
      ```

      注意，使用`Object.keys()`方法时，有三类属性会被`ownKeys()`方法自动过滤，不会返回。

      - 目标对象上不存在的属性
      - 属性名为 Symbol 值
      - 不可遍历（`enumerable`）的属性

      ```js
      let target = {
        a: 1,
        b: 2,
        c: 3,
        [Symbol.for('secret')]: '4',
      };
      
      Object.defineProperty(target, 'key', {
        enumerable: false,
        configurable: true,
        writable: true,
        value: 'static'
      });
      
      let handler = {
        ownKeys(target) {
          return ['a', 'd', Symbol.for('secret'), 'key'];
        }
      };
      
      let proxy = new Proxy(target, handler);
      
      Object.keys(proxy)
      // ['a']
      ```

      上面代码中，`ownKeys()`方法之中，显式返回不存在的属性（`d`）、Symbol 值（`Symbol.for('secret')`）、不可遍历的属性（`key`），结果都被自动过滤掉。

      `ownKeys()`方法还可以拦截`Object.getOwnPropertyNames()`。

      ```js
      var p = new Proxy({}, {
        ownKeys: function(target) {
          return ['a', 'b', 'c'];
        }
      });
      
      Object.getOwnPropertyNames(p)
      // [ 'a', 'b', 'c' ]
      ```

      `for...in`循环也受到`ownKeys()`方法的拦截。

      ```js
      const obj = { hello: 'world' };
      const proxy = new Proxy(obj, {
        ownKeys: function () {
          return ['a', 'b'];
        }
      });
      
      for (let key in proxy) {
        console.log(key); // 没有任何输出
      }
      ```

      上面代码中，`ownkeys()`指定只返回`a`和`b`属性，由于`obj`没有这两个属性，因此`for...in`循环不会有任何输出。

      `ownKeys()`方法返回的数组成员，只能是字符串或 Symbol 值。如果有其他类型的值，或者返回的根本不是数组，就会报错。

      ```js
      var obj = {};
      
      var p = new Proxy(obj, {
        ownKeys: function(target) {
          return [123, true, undefined, null, {}, []];
        }
      });
      
      Object.getOwnPropertyNames(p)
      // Uncaught TypeError: 123 is not a valid property name
      ```

      上面代码中，`ownKeys()`方法虽然返回一个数组，但是每一个数组成员都不是字符串或 Symbol 值，因此就报错了。

      如果目标对象自身包含不可配置的属性，则该属性必须被`ownKeys()`方法返回，否则报错。

      ```js
      var obj = {};
      Object.defineProperty(obj, 'a', {
        configurable: false,
        enumerable: true,
        value: 10 }
      );
      
      var p = new Proxy(obj, {
        ownKeys: function(target) {
          return ['b'];
        }
      });
      
      Object.getOwnPropertyNames(p)
      // Uncaught TypeError: 'ownKeys' on proxy: trap result did not include 'a'
      ```

      上面代码中，`obj`对象的`a`属性是不可配置的，这时`ownKeys()`方法返回的数组之中，必须包含`a`，否则会报错。

      另外，如果目标对象是不可扩展的（non-extensible），这时`ownKeys()`方法返回的数组之中，必须包含原对象的所有属性，且不能包含多余的属性，否则报错。

      ```js
      var obj = {
        a: 1
      };
      
      Object.preventExtensions(obj);
      
      var p = new Proxy(obj, {
        ownKeys: function(target) {
          return ['a', 'b'];
        }
      });
      
      Object.getOwnPropertyNames(p)
      // Uncaught TypeError: 'ownKeys' on proxy: trap returned extra keys but proxy target is non-extensible
      ```

      上面代码中，`obj`对象是不可扩展的，这时`ownKeys()`方法返回的数组之中，包含了`obj`对象的多余属性`b`，所以导致了报错。

    - `getOwnPropertyDescriptor(target, propKey)`：拦截`Object.getOwnPropertyDescriptor()`，返回一个属性描述对象或者`undefined`。

      ```js
      var handler = {
        getOwnPropertyDescriptor (target, key) {
          if (key[0] === '_') {
            return;
          }
          return Object.getOwnPropertyDescriptor(target, key);
        }
      };
      var target = { _foo: 'bar', baz: 'tar' };
      var proxy = new Proxy(target, handler);
      Object.getOwnPropertyDescriptor(proxy, 'wat')
      // undefined
      Object.getOwnPropertyDescriptor(proxy, '_foo')
      // undefined
      Object.getOwnPropertyDescriptor(proxy, 'baz')
      // { value: 'tar', writable: true, enumerable: true, configurable: true }
      ```

      上面代码中，`handler.getOwnPropertyDescriptor()`方法对于第一个字符为下划线的属性名会返回`undefined`。

    - `preventExtensions()`：`preventExtensions()`方法拦截`Object.preventExtensions()`。**该方法必须返回一个布尔值，否则会被自动转为布尔值**。

      这个方法有一个限制，只有目标对象不可扩展时（即`Object.isExtensible(proxy)`为`false`），`proxy.preventExtensions`才能返回`true`，否则会报错。
      
      ```js
      var proxy = new Proxy({}, {
        preventExtensions: function(target) {
          return true;
        }
      });
      
      Object.preventExtensions(proxy)
      // Uncaught TypeError: 'preventExtensions' on proxy: trap returned truish but the proxy target is extensible
      ```

      上面代码中，`proxy.preventExtensions()`方法返回`true`，但这时`Object.isExtensible(proxy)`会返回`true`，因此报错。

      为了防止出现这个问题，通常要在`proxy.preventExtensions()`方法里面，调用一次`Object.preventExtensions()`。

      ```js
      var proxy = new Proxy({}, {
        preventExtensions: function(target) {
          console.log('called');
          Object.preventExtensions(target);
          return true;
        }
      });
      
      Object.preventExtensions(proxy)
      // "called"
      // Proxy {}
      ```
      
    - `isExtensible()`：`isExtensible()`方法拦截`Object.isExtensible()`操作。

      ```js
      var p = new Proxy({}, {
        isExtensible: function(target) {
          console.log("called");
          return true;
        }
      });
      
      Object.isExtensible(p)
      // "called"
      // true
      ```

      上面代码设置了`isExtensible()`方法，在调用`Object.isExtensible`时会输出`called`。

      注意，该方法只能返回布尔值，否则返回值会被自动转为布尔值。

      这个方法有一个强限制，它的返回值必须与目标对象的`isExtensible`属性保持一致，否则就会抛出错误。

      ```js
      Object.isExtensible(proxy) === Object.isExtensible(target)
      ```

      下面是一个例子。

      ```js
      var p = new Proxy({}, {
        isExtensible: function(target) {
          return false;
        }
      });
      
      Object.isExtensible(p)
      // Uncaught TypeError: 'isExtensible' on proxy: trap result does not reflect extensibility of proxy target (which is 'true')
      ```

  - #### `Proxy.revocable()`

    `Proxy.revocable()`方法返回一个可取消的 Proxy 实例。`Proxy.revocable()`方法返回一个对象，该对象的`proxy`属性是`Proxy`实例，`revoke`属性是一个函数，可以取消`Proxy`实例。

    ```js
    let target = {};
    let handler = {};
    
    let {proxy, revoke} = Proxy.revocable(target, handler);
    
    proxy.foo = 123;
    proxy.foo // 123
    
    revoke();
    proxy.foo // TypeError: Revoked
    ```

    上面代码中，当执行`revoke()`之后，再访问`Proxy`实例，就会抛出一个错误。此时只能通过`target`来访问目标对象，代理对象和目标对象之间的关联被彻底切断。

    `Proxy.revocable()`的一个使用场景是，目标对象不允许直接访问，必须通过代理访问，一旦访问结束，就收回代理权，不允许再次访问。

  - #### `this` 问题

    虽然 Proxy 可以代理针对目标对象的访问，但它不是目标对象的透明代理。即**不做任何拦截的情况下，也无法保证与目标对象的行为一致**。主要原因就是**在 Proxy 代理的情况下，目标对象内部的`this`关键字会指向 Proxy 代理**。

    ```js
    const target = {
      m: function () {
        console.log(this === proxy);
      }
    };
    const handler = {};
    
    const proxy = new Proxy(target, handler);
    
    target.m() // false
    proxy.m()  // true
    ```

    上面代码中，一旦`proxy`代理`target`，`target.m()`内部的`this`就是指向`proxy`，而不是`target`。所以，虽然`proxy`没有做任何拦截，`target.m()`和`proxy.m()`返回不一样的结果。

    下面是一个例子，由于`this`指向的变化，导致 Proxy 无法代理目标对象。

    ```js
    const _name = new WeakMap();
    
    class Person {
      constructor(name) {
        _name.set(this, name);
      }
      get name() {
        return _name.get(this);
      }
    }
    
    const jane = new Person('Jane');
    jane.name // 'Jane'
    
    const proxy = new Proxy(jane, {});
    proxy.name // undefined
    ```

    上面代码中，目标对象`jane`的`name`属性，实际保存在外部`WeakMap`对象`_name`上面，通过`this`键区分。由于通过`proxy.name`访问时，`this`指向`proxy`，导致无法取到值，所以返回`undefined`。

    此外，**有些原生对象的内部属性，只有通过正确的`this`才能拿到，所以 Proxy 也无法代理这些原生对象的属性。**

    ```js
    const target = new Date();
    const handler = {};
    const proxy = new Proxy(target, handler);
    
    proxy.getDate();
    // TypeError: this is not a Date object.
    ```

    上面代码中，`getDate()`方法只能在`Date`对象实例上面拿到，如果`this`不是`Date`对象实例就会报错。这时，`this`绑定原始对象，就可以解决这个问题。

    ```js
    const target = new Date('2015-01-01');
    const handler = {
      get(target, prop) {
        if (prop === 'getDate') {
          return target.getDate.bind(target);
        }
        return Reflect.get(target, prop);
      }
    };
    const proxy = new Proxy(target, handler);
    
    proxy.getDate() // 1
    ```

    另外，**Proxy 拦截函数内部的`this`，指向的是`handler`对象。**

    ```js
    const handler = {
      get: function (target, key, receiver) {
        console.log(this === handler);
        return 'Hello, ' + key;
      },
      set: function (target, key, value) {
        console.log(this === handler);
        target[key] = value;
        return true;
      }
    };
    
    const proxy = new Proxy({}, handler);
    
    proxy.foo
    // true
    // Hello, foo
    
    proxy.foo = 1
    // true
    ```

    上面例子中，`get()`和`set()`拦截函数内部的`this`，指向的都是`handler`对象。

  - #### Proxy的应用——将属性访问自动映射为 HTTP 请求

    传统的做法：每增加一个 API 接口，就需要手动写一个方法。

    ```js
    // 传统方式：手动为每个 API 写方法
    const service = {
      employees() {
        return httpGet('http://example.com/data/employees');
      },
      products() {
        return httpGet('http://example.com/data/products');
      },
      orders() {
        return httpGet('http://example.com/data/orders');
      }
      // 每增加一个 API 都要手动添加...
    };
    
    // 调用接口请求数据
    service.employees().then(json => {
      const employees = JSON.parse(json);
      // ···
    });
    ```
    
    Proxy 的做法：用一个 Proxy 自动处理所有属性。
    
    ```js
    function createWebService(baseUrl) {
      return new Proxy({}, {
        get(target, propKey, receiver) {
          return () => httpGet(baseUrl + '/' + propKey);
        }
      });
    }
    
    // 调用接口请求数据
    const service = createWebService('http://example.com/data');
    // 不需要预先定义任何方法，直接用就行
    service.employees();  // 自动变成请求 employees 接口
    service.products();   // 自动变成请求 products 接口
    service.orders();     // 自动变成请求 orders 接口
    ```
    
    上面代码新建了一个 Web 服务的接口`createWebService`，Proxy 拦截这个对象的任意属性，所以不用为每一种数据写一个适配方法。
    
    同理，Proxy 也可以用来实现数据库的 ORM 层。
    
    ```js
    function createORM(tableName) {
      return new Proxy({}, {
        get(target, propKey) {
          // findById, findAll, where 等操作
          return (params) => {
            return db.query(`SELECT * FROM ${tableName} WHERE ${propKey} = ?`, params);
          };
        }
      });
    }
    
    const users = createORM('users');
    users.findById(1);      // SELECT * FROM users WHERE findById = 1
    users.findByName('张三'); // SELECT * FROM users WHERE findByName = '张三'
    ```
    
    这就是 Proxy 的"元编程"能力——**让你在运行时动态地改变语言默认行为**，用极少的代码实现强大的功能。

- ## Reflect

  - #### 概述

    `Reflect`对象与`Proxy`对象一样，也是 ES6 为了操作对象而提供的新 API。`Reflect`对象的设计目的有这样几个：

    - 将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。现阶段，某些方法同时在`Object`和`Reflect`对象上部署，未来的新方法将只部署在`Reflect`对象上。也就是说，从`Reflect`对象上可以拿到语言内部的方法。

    - 修改某些`Object`方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`。

       ```js
       // 老写法
       try {
         Object.defineProperty(target, property, attributes);
         // success
       } catch (e) {
         // failure
       }
       
       // 新写法
       if (Reflect.defineProperty(target, property, attributes)) {
         // success
       } else {
         // failure
       }
       ```

    - 让`Object`操作都变成函数行为。某些`Object`操作是命令式，比如`name in obj`和`delete obj[name]`，而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为。

       ```js
       // 老写法（函数也是对象，因此in运算符后还可以跟构造函数）
       'assign' in Object // true
       
       // 新写法
       Reflect.has(Object, 'assign') // true
       ```

    - **`Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法**。这就让`Proxy`对象可以方便地调用对应的`Reflect`方法来完成默认行为，而 Reflect 则作为 Proxy 修改行为的基础。也就是说，不管`Proxy`怎么修改默认行为，你总可以在`Reflect`上获取默认行为。

       ```js
       Proxy(target, {
         set: function(target, name, value, receiver) {
           var success = Reflect.set(target, name, value, receiver);
           if (success) {
             console.log('property ' + name + ' on ' + target + ' set to ' + value);
           }
           return success;
         }
       });
       ```

       上面代码中，`Proxy`方法拦截`target`对象的属性赋值行为。它采用`Reflect.set`方法将值赋值给对象的属性，确保完成原有的行为，然后再部署额外的功能。

       下面是另一个例子。

       ```js
       var loggedObj = new Proxy(obj, {
         get(target, name) {
           console.log('get', target, name);
           return Reflect.get(target, name);
         },
         deleteProperty(target, name) {
           console.log('delete' + name);
           return Reflect.deleteProperty(target, name);
         },
         has(target, name) {
           console.log('has' + name);
           return Reflect.has(target, name);
         }
       });
       ```

       上面代码中，每一个`Proxy`对象的拦截操作（`get`、`delete`、`has`），内部都调用对应的`Reflect`方法，保证原生行为能够正常执行。添加的工作，就是将每一个操作输出一行日志。

       有了`Reflect`对象以后，很多操作会更易读。

       ```js
       // 老写法
       Function.prototype.apply.call(Math.floor, undefined, [1.75]) // 1
       
       // 新写法
       Reflect.apply(Math.floor, undefined, [1.75]) // 1
       ```

  - #### 静态方法

    `Reflect`对象一共有 13 个静态方法。这些方法的作用，大部分与`Object`对象的同名方法的作用都是相同的，而且它与`Proxy`对象的方法是一一对应的。下面是对它们的解释。

    - `Reflect.get(target, name, receiver)`：`Reflect.get`方法查找并返回`target`对象的`name`属性，如果没有该属性，则返回`undefined`。

      ```js
      var myObject = {
        foo: 1,
        bar: 2,
        get baz() {
          return this.foo + this.bar;
        },
      }
      
      Reflect.get(myObject, 'foo') // 1
      Reflect.get(myObject, 'bar') // 2
      Reflect.get(myObject, 'baz') // 3
      ```

      如果`name`属性部署了读取函数（getter），则读取函数的`this`绑定为第三个参数`receiver`。

      ```js
      var myObject = {
        foo: 1,
        bar: 2,
        get baz() {
          return this.foo + this.bar;
        },
      };
      
      var myReceiverObject = {
        foo: 4,
        bar: 4,
      };
      
      Reflect.get(myObject, 'baz', myReceiverObject) // 8
      ```

      如果第一个参数不是对象，`Reflect.get`方法会报错。

      ```js
      Reflect.get(1, 'foo') // 报错
      Reflect.get(false, 'foo') // 报错
      ```

    - `Reflect.set(target, name, value, receiver)`：`Reflect.set`方法设置`target`对象的`name`属性等于`value`。

      ```js
      var myObject = {
        foo: 1,
        set bar(value) {
          return this.foo = value;
        },
      }
      
      myObject.foo // 1
      
      Reflect.set(myObject, 'foo', 2);
      myObject.foo // 2
      
      Reflect.set(myObject, 'bar', 3)
      myObject.foo // 3
      ```

      如果`name`属性设置了赋值函数，则赋值函数的`this`绑定为第三个参数`receiver`。

      ```js
      var myObject = {
        foo: 4,
        set bar(value) {
          return this.foo = value;
        },
      };
      
      var myReceiverObject = {
        foo: 0,
      };
      
      Reflect.set(myObject, 'bar', 1, myReceiverObject);
      myObject.foo // 4
      myReceiverObject.foo // 1
      ```

      注意，**如果 `Proxy`对象和 `Reflect`对象联合使用，前者拦截赋值操作，后者完成赋值的默认行为，而且传入了`receiver`，那么`Reflect.set`会触发`Proxy.defineProperty`拦截方法**。

      ```js
      let p = {
        a: 'a'
      };
      
      let handler = {
        set(target, key, value, receiver) {
          console.log('set');
          Reflect.set(target, key, value, receiver)
        },
        defineProperty(target, key, attribute) {
          console.log('defineProperty');
          Reflect.defineProperty(target, key, attribute);
        }
      };
      
      let obj = new Proxy(p, handler);
      obj.a = 'A';
      // set
      // defineProperty
      ```

      上面代码中，`Proxy.set`拦截里面使用了`Reflect.set`，而且传入了`receiver`，导致触发`Proxy.defineProperty`拦截。这是因为`Proxy.set`的`receiver`参数总是指向当前的 `Proxy`实例（即上例的`obj`），而`Reflect.set`一旦传入`receiver`，就会将属性赋值到`receiver`上面（即`obj`），导致触发`defineProperty`拦截。如果`Reflect.set`没有传入`receiver`，那么就不会触发`defineProperty`拦截。

      ```js
      let p = {
        a: 'a'
      };
      
      let handler = {
        set(target, key, value, receiver) {
          console.log('set');
          Reflect.set(target, key, value)
        },
        defineProperty(target, key, attribute) {
          console.log('defineProperty');
          Reflect.defineProperty(target, key, attribute);
        }
      };
      
      let obj = new Proxy(p, handler);
      obj.a = 'A';
      // set
      ```

      之所以会这样，背后的原理是：

      当 `receiver !== target` 时，为了保持属性赋值的正确语义（属性应该设置在 `receiver` 上，而不是 `target` 上），规范强制要求调用 `receiver.[[DefineOwnProperty]]`，而这恰好会触发 Proxy 的 `defineProperty`。

      如果第一个参数不是对象，`Reflect.set`会报错。

      ```js
      Reflect.set(1, 'foo', {}) // 报错
      Reflect.set(false, 'foo', {}) // 报错
      ```

    - `Reflect.has(obj, name)`：`Reflect.has`方法对应`name in obj`里面的`in`运算符。

      ```js
      var myObject = {
        foo: 1,
      };
      
      // 旧写法
      'foo' in myObject // true
      
      // 新写法
      Reflect.has(myObject, 'foo') // true
      ```

      如果`Reflect.has()`方法的第一个参数不是对象，会报错。

    - `Reflect.deleteProperty(obj, name)`：`Reflect.deleteProperty`方法等同于`delete obj[name]`，用于删除对象的属性。

      ```js
      const myObj = { foo: 'bar' };
      
      // 旧写法
      delete myObj.foo;
      
      // 新写法
      Reflect.deleteProperty(myObj, 'foo');
      ```

      该方法返回一个布尔值。如果删除成功，或者被删除的属性不存在，返回`true`；删除失败，被删除的属性依然存在，返回`false`。

      如果`Reflect.deleteProperty()`方法的第一个参数不是对象，会报错。

    - `Reflect.apply(func, thisArg, args)`：`Reflect.apply`方法等同于`Function.prototype.apply.call(func, thisArg, args)`，用于绑定`this`对象后执行给定函数。

      一般来说，如果要绑定一个函数的`this`对象，可以这样写`fn.apply(obj, args)`，但是如果函数定义了自己的`apply`方法，就只能写成`Function.prototype.apply.call(fn, obj, args)`，采用`Reflect`对象可以简化这种操作。

      ```js
      const ages = [11, 33, 12, 54, 18, 96];
      
      // 旧写法
      const youngest = Math.min.apply(Math, ages);
      const oldest = Math.max.apply(Math, ages);
      const type = Object.prototype.toString.call(youngest);
      
      // 新写法
      const youngest = Reflect.apply(Math.min, Math, ages);
      const oldest = Reflect.apply(Math.max, Math, ages);
      const type = Reflect.apply(Object.prototype.toString, youngest, []);
      ```

    - `Reflect.construct(target, args)`：`Reflect.construct`方法等同于`new target(...args)`，这提供了一种不使用`new`，来调用构造函数的方法。

      ```js
      function Greeting(name) {
        this.name = name;
      }
      
      // new 的写法
      const instance = new Greeting('张三');
      
      // Reflect.construct 的写法
      const instance = Reflect.construct(Greeting, ['张三']);
      ```

      如果`Reflect.construct()`方法的第一个参数不是函数，会报错。

    - `Reflect.getPrototypeOf(obj)`：`Reflect.getPrototypeOf`方法用于读取对象的`__proto__`属性，对应`Object.getPrototypeOf(obj)`。

      ```js
      const myObj = new FancyThing();
      
      // 旧写法
      Object.getPrototypeOf(myObj) === FancyThing.prototype;
      
      // 新写法
      Reflect.getPrototypeOf(myObj) === FancyThing.prototype;
      ```

      `Reflect.getPrototypeOf`和`Object.getPrototypeOf`的一个区别是，如果参数不是对象，`Object.getPrototypeOf`会将这个参数转为对象，然后再运行，而`Reflect.getPrototypeOf`会报错。

      ```js
      Object.getPrototypeOf(1) // Number {[[PrimitiveValue]]: 0}
      Reflect.getPrototypeOf(1) // 报错
      ```

    - `Reflect.setPrototypeOf(obj, newProto)`：`Reflect.setPrototypeOf`方法用于设置目标对象的原型（prototype），对应`Object.setPrototypeOf(obj, newProto)`方法。它返回一个布尔值，表示是否设置成功。

      ```js
      const myObj = {};
      
      // 旧写法
      Object.setPrototypeOf(myObj, Array.prototype);
      
      // 新写法
      Reflect.setPrototypeOf(myObj, Array.prototype);
      
      myObj.length // 0
      ```

      如果无法设置目标对象的原型（比如，目标对象禁止扩展），`Reflect.setPrototypeOf`方法返回`false`。

      ```js
      Reflect.setPrototypeOf({}, null)  // true
      Reflect.setPrototypeOf(Object.freeze({}), null)  // false
      ```

      如果第一个参数不是对象，`Object.setPrototypeOf`会返回第一个参数本身，而`Reflect.setPrototypeOf`会报错。

      ```js
      Object.setPrototypeOf(1, {})
      // 1
      
      Reflect.setPrototypeOf(1, {})
      // TypeError: Reflect.setPrototypeOf called on non-object
      ```

      如果第一个参数是`undefined`或`null`，`Object.setPrototypeOf`和`Reflect.setPrototypeOf`都会报错。

      ```js
      Object.setPrototypeOf(null, {})
      // TypeError: Object.setPrototypeOf called on null or undefined
      
      Reflect.setPrototypeOf(null, {})
      // TypeError: Reflect.setPrototypeOf called on non-object
      ```

    - `Reflect.defineProperty(target, propertyKey, attributes)`：`Reflect.defineProperty()`方法基本等同于`Object.defineProperty()`，用来为对象定义属性。未来，后者会被逐渐废除，请从现在开始就使用`Reflect.defineProperty`代替它。

      ```js
      function MyDate() {
        /*…*/
      }
      
      // 旧写法
      Object.defineProperty(MyDate, 'now', {
        value: () => Date.now()
      });
      
      // 新写法
      Reflect.defineProperty(MyDate, 'now', {
        value: () => Date.now()
      });
      ```

      如果`Reflect.defineProperty`的第一个参数不是对象，就会抛出错误，比如`Reflect.defineProperty(1, 'foo')`。

      这个方法可以与`Proxy.defineProperty`配合使用。

      ```js
      const p = new Proxy({}, {
        defineProperty(target, prop, descriptor) {
          console.log(descriptor);
          return Reflect.defineProperty(target, prop, descriptor);
        }
      });
      
      p.foo = 'bar';
      // {value: "bar", writable: true, enumerable: true, configurable: true}
      
      p.foo // "bar"
      ```

      上面代码中，`Proxy.defineProperty`对属性赋值设置了拦截，然后使用`Reflect.defineProperty`完成了赋值。

    - `Reflect.getOwnPropertyDescriptor(target, propertyKey)`：`Reflect.getOwnPropertyDescriptor()`基本等同于`Object.getOwnPropertyDescriptor()`，用于得到指定属性的描述对象，将来会替代掉后者。

      ```js
      var myObject = {};
      Object.defineProperty(myObject, 'hidden', {
        value: true,
        enumerable: false,
      });
      
      // 旧写法
      var theDescriptor = Object.getOwnPropertyDescriptor(myObject, 'hidden');
      
      // 新写法
      var theDescriptor = Reflect.getOwnPropertyDescriptor(myObject, 'hidden');
      ```

      `Reflect.getOwnPropertyDescriptor`和`Object.getOwnPropertyDescriptor`的一个区别是，如果第一个参数不是对象，`Object.getOwnPropertyDescriptor(1, 'foo')`不报错，返回`undefined`，而`Reflect.getOwnPropertyDescriptor(1, 'foo')`会抛出错误，表示参数非法。

    - `Reflect.isExtensible(target)`：`Reflect.isExtensible()`对应`Object.isExtensible()`，返回一个布尔值，表示当前对象是否可扩展。

      ```js
      const myObject = {};
      
      // 旧写法
      Object.isExtensible(myObject) // true
      
      // 新写法
      Reflect.isExtensible(myObject) // true
      ```

      如果参数不是对象，`Object.isExtensible`会返回`false`，因为非对象本来就是不可扩展的，而`Reflect.isExtensible`会报错。

      ```js
      Object.isExtensible(1) // false
      Reflect.isExtensible(1) // 报错
      ```

    - `Reflect.preventExtensions(target)`：`Reflect.preventExtensions()`对应`Object.preventExtensions()`方法，用于让一个对象变为不可扩展。它返回一个布尔值，表示是否操作成功。

      ```js
      var myObject = {};
      
      // 旧写法
      Object.preventExtensions(myObject) // Object {}
      
      // 新写法
      Reflect.preventExtensions(myObject) // true
      ```

      如果参数不是对象，`Object.preventExtensions`在 ES5 环境报错，在 ES6 环境返回传入的参数，而`Reflect.preventExtensions`会报错。

      ```js
      // ES5 环境
      Object.preventExtensions(1) // 报错
      
      // ES6 环境
      Object.preventExtensions(1) // 1
      
      // 新写法
      Reflect.preventExtensions(1) // 报错
      ```

    - `Reflect.ownKeys(target)`：`Reflect.ownKeys`方法用于返回对象的所有属性，基本等同于`Object.getOwnPropertyNames`与`Object.getOwnPropertySymbols`之和。

      ```js
      var myObject = {
        foo: 1,
        bar: 2,
        [Symbol.for('baz')]: 3,
        [Symbol.for('bing')]: 4,
      };
      
      // 旧写法
      Object.getOwnPropertyNames(myObject)
      // ['foo', 'bar']
      
      Object.getOwnPropertySymbols(myObject)
      //[Symbol(baz), Symbol(bing)]
      
      // 新写法
      Reflect.ownKeys(myObject)
      // ['foo', 'bar', Symbol(baz), Symbol(bing)]
      ```

      如果`Reflect.ownKeys()`方法的第一个参数不是对象，会报错。

  - #### 实例：使用 Proxy 实现观察者模式

    观察者模式（Observer mode）指的是函数自动观察数据对象，一旦对象有变化，函数就会自动执行。

    ```js
    const person = observable({
      name: '张三',
      age: 20
    });
    
    function print() {
      console.log(`${person.name}, ${person.age}`)
    }
    
    observe(print);
    person.name = '李四';
    // 输出
    // 李四, 20
    ```

    上面代码中，数据对象`person`是观察目标，函数`print`是观察者。一旦数据对象发生变化，`print`就会自动执行。

    下面，使用 Proxy 写一个观察者模式的最简单实现，即实现`observable`和`observe`这两个函数。思路是`observable`函数返回一个原始对象的 Proxy 代理，拦截赋值操作，触发充当观察者的各个函数。

    ```js
    const queuedObservers = new Set();
    
    const observe = fn => queuedObservers.add(fn);
    const observable = obj => new Proxy(obj, {set});
    
    function set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver);
      queuedObservers.forEach(observer => observer());
      return result;
    }
    ```

    上面代码中，先定义了一个`Set`集合，所有观察者函数都放进这个集合。然后，`observable`函数返回原始对象的代理，拦截赋值操作。拦截函数`set`之中，会自动执行所有观察者。

- ## Iterator 和 for...of 循环

  - #### Iterator（迭代器）的概念

    JS 原有的表示“集合”的数据结构，主要是数组（`Array`）和对象（`Object`），ES6 又添加了`Map`和`Set`。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是`Map`，`Map`的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构。

    迭代器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。**任何数据结构只要部署 Iterator 接口，就可以完成遍历操作（即依次处理该数据结构的所有成员）**。

    > 对象部署 Iterator 接口，就是说该对象要具有`Symbol.iterator`属性（无论是原型还是自身）。该属性的值是一个**迭代器生成函数**，该函数返回一个迭代器对象（后面会讲）。

    Iterator 的作用有三个：一是为各种数据结构，提供一个统一的、简便的访问接口；二是使得数据结构的成员能够按某种次序排列；三是 ES6 创造了一种新的遍历命令`for...of`循环，Iterator 接口主要供`for...of`消费。

    Iterator 的遍历过程是这样的：

    1. 创建一个**迭代器对象**，指向当前数据结构的起始位置。

       > 迭代器对象的本质，就是一个有 `next()` 方法的普通对象，无论是原型还是本身有`next()`方法。

    2. 第一次调用迭代器对象的`next`方法，可以将指针指向数据结构的第一个成员。

    3. 第二次调用迭代器对象的`next`方法，指针就指向数据结构的第二个成员。

    4. 不断调用迭代器对象的`next`方法，直到它指向数据结构的结束位置。

    **每一次调用`next`方法，都会返回数据结构的当前成员的信息对象**。具体来说，这个**信息对象就是一个包含`value`和`done`属性的对象。其中，`value`属性是当前成员的值，`done`属性是一个布尔值，表示遍历是否结束**。

    注意：**`for...of`循环不会遍历`done`属性值为`true`的信息对象**。

    下面是一个模拟`next`方法返回值的例子。

    ```js
    var it = makeIterator(['a', 'b']);
    
    it.next() // { value: "a", done: false }
    it.next() // { value: "b", done: false }
    it.next() // { value: undefined, done: true }
    
    function makeIterator(array) {
      var nextIndex = 0;
      return {
        next: function() {
          return nextIndex < array.length ?
            {value: array[nextIndex++], done: false} :
            {value: undefined, done: true};
        }
      };
    }
    ```

    上面代码定义了一个`makeIterator`函数，它是一个**迭代器对象的生成函数（迭代器生成函数/生成器函数），作用就是返回一个迭代器对象**。对数组`['a', 'b']`执行这个函数，就会返回该数组的迭代器对象`it`。

    迭代器对象的`next`方法，用来移动指针。开始时，指针指向数组的开始位置。然后，每次调用`next`方法，指针就会指向数组的下一个成员。第一次调用，指向`a`；第二次调用，指向`b`。

    `next`方法返回一个对象，表示当前数据成员的信息。这个对象具有`value`和`done`两个属性，`value`属性返回当前位置的成员，`done`属性是一个布尔值，表示遍历是否结束，即是否还有必要再一次调用`next`方法。

    总之，调用迭代器对象的`next`方法，就可以遍历事先给定的数据结构。

    **对于迭代器的信息对象来说，`done`和`value`属性都是可以省略的**。`done`默认是`false`，`value`默认是`undefined`。因此上面的`makeIterator`函数可以简写成下面的形式。

    ```js
    function makeIterator(array) {
      var nextIndex = 0;
      return {
        next: function() {
          return nextIndex < array.length ?
            {value: array[nextIndex++]} :
            {done: true};
        }
      };
    }
    ```

    由于 Iterator 只是把接口规格加到数据结构之上，所以，迭代器与它所遍历的那个数据结构，实际上是分开的，完全可以写出没有对应数据结构的迭代器对象，或者说用迭代器对象模拟出数据结构。下面是一个无限运行的迭代器对象的例子。

    ```js
    var it = idMaker();
    
    it.next().value // 0
    it.next().value // 1
    it.next().value // 2
    // ...
    
    function idMaker() {
      var index = 0;
    
      return {
        next: function() {
          return {value: index++, done: false};
        }
      };
    }
    ```

    上面的例子中，迭代器生成函数`idMaker`，返回一个迭代器对象。但是并没有对应的数据结构，或者说，迭代器对象自己描述了一个数据结构出来。

    如果使用 TS 的写法，迭代器接口（Iterable）、迭代器对象（Iterator）和`next`方法返回值的规格可以描述如下。

    ```ts
    interface Iterable {
      [Symbol.iterator]() : Iterator,
    }
    
    interface Iterator {
      next(value?: any) : IterationResult,
    }
    
    interface IterationResult {
      value: any,
      done: boolean,
    }
    ```

  - #### 默认 Iterator 接口

    Iterator 接口的目的，就是为所有数据结构，提供了一种统一的访问机制，即`for...of`循环（详见下文）。**当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找 Iterator 接口。**

    一种数据结构只要部署了 Iterator 接口，我们就称这种数据结构是“可遍历的”（iterable）。

    ES6 规定，**默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）**。`Symbol.iterator`属性本身是一个函数，就是当前数据结构默认的**迭代器生成函数**。执行这个函数，会返回一个迭代器对象。

    ```js
    const obj = {
      [Symbol.iterator] : function () {
        return {
          next: function () {
            return {
              value: 1,
              done: true
            };
          }
        };
      }
    };
    ```

    上面代码中，对象`obj`是可遍历的（iterable），因为具有`Symbol.iterator`属性。执行这个属性，会返回一个迭代器对象。该对象的根本特征就是具有`next`方法。每次调用`next`方法，都会返回一个代表当前成员的信息对象，具有`value`和`done`两个属性。

    ES6 的有些数据结构原生具备 Iterator 接口（即`Symbol.iterator`属性），即不用任何处理，就可以被`for...of`循环遍历（比如数组）。原因在于，这些数据结构原生部署了`Symbol.iterator`属性（详见下文）。凡是部署了`Symbol.iterator`属性的数据结构，就称为部署了迭代器接口。调用这个接口，就会返回一个迭代器对象。

    原生具备 Iterator 接口的数据结构如下。

    - Array
    - Map
    - Set
    - String
    - TypedArray
    - 函数的 `arguments`
    - NodeList 对象

    下面的例子是数组的`Symbol.iterator`属性。

    ```js
    let arr = ['a', 'b', 'c'];
    let iter = arr[Symbol.iterator]();
    
    iter.next() // { value: 'a', done: false }
    iter.next() // { value: 'b', done: false }
    iter.next() // { value: 'c', done: false }
    iter.next() // { value: undefined, done: true }
    ```

    上面代码中，变量`arr`是一个数组，原生就具有迭代器接口，部署在`arr`的`Symbol.iterator`属性上面。所以，调用这个属性，就得到迭代器对象。

    对于原生部署 Iterator 接口的数据结构，不用自己写迭代器生成函数，`for...of`循环会自动遍历它们。除此之外，其他数据结构（主要是对象）的 Iterator 接口，都需要自己在`Symbol.iterator`属性上面部署，这样才会被`for...of`循环遍历。

    对象（Object）之所以没有默认部署 Iterator 接口，是因为对象的哪个属性先遍历，哪个属性后遍历是不确定的，需要开发者手动指定。**本质上，迭代器是一种线性处理，对于任何非线性的数据结构，部署迭代器接口，就等于部署一种线性转换**。不过，严格地说，对象部署迭代器接口并不是很必要，因为这时对象实际上被当作 Map 结构使用，ES5 没有 Map 结构，而 ES6 原生提供了。

    一个对象如果要具备可被`for...of`循环调用的 Iterator 接口，就必须在`Symbol.iterator`的属性上部署迭代器生成函数（原型链上具有该函数也行）。所谓迭代器生成函数，就是一个可以生成迭代器对象的函数。

    ```js
    class RangeIterator {
      constructor(start, stop) {
        this.value = start;
        this.stop = stop;
      }
    
      [Symbol.iterator]() { return this; }  // 原型上的迭代器生成方法
    
      next() {  // 该方法在原型上
        var value = this.value;
        if (value < this.stop) {
          this.value++;
          return {done: false, value: value};
        }
        return {done: true, value: undefined};
      }
    }
    
    function range(start, stop) {
      return new RangeIterator(start, stop);
    }
    
    for (var value of range(0, 3)) {
      console.log(value); // 0, 1, 2
    }
    ```

    上面代码是一个类部署 Iterator 接口的写法。`Symbol.iterator`属性对应一个迭代器生成函数，执行后返回了当前对象。由于当前对象的原型上具有`next()`方法，因此当前对象就是一个迭代器对象。

    下面是通过迭代器实现“链表”结构的例子。

    ```js
    function Obj(value) {
      this.value = value;
      this.next = null;
    }
    
    Obj.prototype[Symbol.iterator] = function() {
      var iterator = { next: next };
    
      var current = this;
    
      function next() {
        if (current) {
          var value = current.value;
          current = current.next;
          return { done: false, value: value };
        }
        return { done: true };
      }
      return iterator;
    }
    
    var one = new Obj(1);
    var two = new Obj(2);
    var three = new Obj(3);
    
    one.next = two;
    two.next = three;
    
    for (var i of one){
      console.log(i); // 1, 2, 3
    }
    ```

    上面代码首先在构造函数的原型链上部署`Symbol.iterator`方法，调用该方法会返回迭代器对象`iterator`，调用该对象的`next`方法，在返回一个值的同时，自动将内部指针移到下一个实例。

    下面是另一个为对象添加 Iterator 接口的例子。

    ```js
    let obj = {
      data: [ 'hello', 'world' ],
      [Symbol.iterator]() {
        const self = this;
        let index = 0;
        return {
          next() {
            if (index < self.data.length) {
              return {
                value: self.data[index++],
                done: false
              };
            }
            return { value: undefined, done: true };
          }
        };
      }
    };
    ```

    对于伪数组对象，有一个简便的方法可以为其部署 Iterator 接口，就是`Symbol.iterator`方法直接引用数组的 Iterator 接口。

    ```js
    NodeList.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator];
    // 或者
    NodeList.prototype[Symbol.iterator] = [][Symbol.iterator];
    
    [...document.querySelectorAll('div')] // 可以执行了
    ```

    NodeList 对象是类似数组的对象，本来就具有遍历接口，可以直接遍历。上面代码中，我们将它的遍历接口改成数组的`Symbol.iterator`属性，可以看到没有任何影响。

    下面是另一个伪数组对象调用数组的`Symbol.iterator`方法的例子。

    ```js
    let iterable = {
      0: 'a',
      1: 'b',
      2: 'c',
      length: 3,
      [Symbol.iterator]: Array.prototype[Symbol.iterator]
    };
    for (let item of iterable) {
      console.log(item); // 'a', 'b', 'c'
    }
    ```

    注意，普通对象部署数组的`Symbol.iterator`方法，并无效果。

    ```js
    let iterable = {
      a: 'a',
      b: 'b',
      c: 'c',
      length: 3,
      [Symbol.iterator]: Array.prototype[Symbol.iterator]
    };
    for (let item of iterable) {
      console.log(item); // undefined, undefined, undefined
    }
    ```

    **如果`Symbol.iterator`方法对应的不是迭代器生成函数（即会返回一个迭代器对象），将会报错**。

    ```js
    var obj = {};
    
    obj[Symbol.iterator] = () => 1;
    
    [...obj] // TypeError: [] is not a function
    ```

    上面代码中，变量`obj`的`Symbol.iterator`方法对应的不是迭代器生成函数，因此报错。

    **有了迭代器接口，数据结构就可以用`for...of`循环遍历（详见下文），也可以使用`while`循环遍历。**

    ```js
    var $iterator = ITERABLE[Symbol.iterator]();
    var $result = $iterator.next();
    while (!$result.done) {
      var x = $result.value;
      // ...
      $result = $iterator.next();
    }
    ```

    上面代码中，`ITERABLE`代表某种可遍历的数据结构，`$iterator`是它的迭代器对象。迭代器对象每次移动指针（`next`方法），都检查一下返回值的`done`属性，如果遍历还没结束，就移动迭代器对象的指针到下一步（`next`方法），不断循环。

  - #### 调用 Iterator 接口的场合

    有一些场合会默认调用 Iterator 接口（即`Symbol.iterator`方法），除了下文的`for...of`循环，还有其他几个场合。

    1. **解构赋值**

       对数组和 Set 结构等可迭代的结构进行解构赋值时，会默认调用`Symbol.iterator`方法。

       ```js
       let set = new Set().add('a').add('b').add('c');
       
       let [x,y] = set;
       // x='a'; y='b'
       
       let [first, ...rest] = set;
       // first='a'; rest=['b','c'];
       ```

    2. **扩展运算符（用于可迭代结构时）**

       扩展运算符（...）也会调用默认的 Iterator 接口。

       ```js
       // 例一
       var str = 'hello';
       [...str] //  ['h','e','l','l','o']
       
       // 例二
       let arr = ['b', 'c'];
       ['a', ...arr, 'd']
       // ['a', 'b', 'c', 'd']
       ```

       上面代码的扩展运算符内部就调用 Iterator 接口。

       实际上，这提供了一种简便机制，可以将任何部署了 Iterator 接口的数据结构，转为数组。也就是说，只要某个数据结构部署了 Iterator 接口，就可以对它使用扩展运算符，将其转为数组。

       ```js
       let arr = [...iterable];
       ```

    3. **yield\***

       `yield*`后面跟的是一个可遍历的结构，它会调用该结构的迭代器接口。

       ```js
       let generator = function* () {
         yield 1;
         yield* [2,3,4];
         yield 5;
       };
       
       var iterator = generator();
       
       iterator.next() // { value: 1, done: false }
       iterator.next() // { value: 2, done: false }
       iterator.next() // { value: 3, done: false }
       iterator.next() // { value: 4, done: false }
       iterator.next() // { value: 5, done: false }
       iterator.next() // { value: undefined, done: true }
       ```

    4. **其他场合**

       由于数组的遍历会调用迭代器接口，所以任何接受数组作为参数的场合，其实都调用了迭代器接口。下面是一些例子：
       
       - `for...of`
       - `Array.from()`
       - `Map(), Set(), WeakMap(), WeakSet()`（比如`new Map([['a',1],['b',2]])`）
       - `Promise.all()`
       - `Promise.race()`
       
       这些内置方法的参数都是数组，实际上内部都是通过调用数组的迭代器接口来获取的元素。

  - #### 字符串的 Iterator 接口

    字符串是一个伪数组的对象，也原生具有 Iterator 接口。

    ```js
    var someString = "hi";
    typeof someString[Symbol.iterator]
    // "function"
    
    var iterator = someString[Symbol.iterator]();
    
    iterator.next()  // { value: "h", done: false }
    iterator.next()  // { value: "i", done: false }
    iterator.next()  // { value: undefined, done: true }
    ```

    上面代码中，调用`Symbol.iterator`方法返回一个迭代器对象，在这个迭代器上可以调用 next 方法，实现对于字符串的遍历。

    可以覆盖原生的`Symbol.iterator`方法，达到修改迭代器行为的目的。

    ```js
    var str = new String("hi");
    
    [...str] // ["h", "i"]
    
    str[Symbol.iterator] = function() {
      return {
        next: function() {
          if (this._first) {
            this._first = false;
            return { value: "bye", done: false };
          } else {
            return { done: true };
          }
        },
        _first: true
      };
    };
    
    [...str] // ["bye"]
    str // "hi"
    ```

    上面代码中，字符串 str 的`Symbol.iterator`方法被修改了，所以扩展运算符（`...`）返回的值变成了`bye`，而字符串本身还是`hi`。

  - #### Iterator 接口与 Generator 函数

    `Symbol.iterator()`方法的最简单实现，还是使用下一章要介绍的 Generator 函数。

    ```js
    let myIterable = {
      [Symbol.iterator]: function* () {
        yield 1;
        yield 2;
        yield 3;
      }
    };
    [...myIterable] // [1, 2, 3]
    
    // 或者采用下面的简洁写法
    
    let obj = {
      * [Symbol.iterator]() {
        yield 'hello';
        yield 'world';
      }
    };
    
    for (let x of obj) {
      console.log(x);
    }
    // "hello"
    // "world"
    ```

    上面代码中，`Symbol.iterator()`方法几乎不用部署任何代码，只要用 yield 命令给出每一步的返回值即可。

  - #### 迭代器对象的 `return()`，`throw()`

    **迭代器对象除了具有`next()`方法，还可以具有`return()`方法和`throw()`方法**。如果你自己写迭代器对象生成函数，那么`next()`方法是必须部署的，`return()`方法和`throw()`方法是否部署是可选的。

    迭代器对象的 `return()` 方法会在**迭代提前终止**时被自动调用，用于执行清理操作（如关闭文件、释放网络连接等）。

    `return()`方法的使用场合是，如果`for...of`循环提前退出（通常是因为出错，或者有`break`语句），就会调用`return()`方法。如果一个对象在完成遍历前，需要清理或释放资源，就可以部署`return()`方法。
  
    ```js
    function readLinesSync(file) {
      return {
        [Symbol.iterator]() {
          return {
            next() {
              return { done: false };
            },
            return() {
              file.close();
              return { done: true };
            }
          };
        },
      };
    }
    ```

    上面代码中，函数`readLinesSync`接受一个文件对象作为参数，返回一个迭代器对象，其中除了`next()`方法，还部署了`return()`方法。下面的两种情况，都会触发执行`return()`方法。
  
    ```js
    // 情况一
    for (let line of readLinesSync(fileName)) {
      console.log(line);
      break;
    }
    
    // 情况二
    for (let line of readLinesSync(fileName)) {
      console.log(line);
      throw new Error();
    }
    ```

    上面代码中，情况一输出文件的第一行以后，就会执行`return()`方法，关闭这个文件；情况二会在**执行`return()`方法关闭文件之后，再抛出错误**。

    注意，**`return()`方法必须返回一个信息对象**，这是 Generator 语法决定的。

    `throw()`方法主要是配合 Generator 函数使用，一般的迭代器对象用不到这个方法。请参阅《Generator 函数》一章。

  - #### for...of 循环
  
    ES6 借鉴 C++、Java、C# 和 Python 语言，**引入了`for...of`循环，作为遍历所有数据结构的统一的方法**。
  
    **一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有 iterator 接口，就可以用`for...of`循环遍历它的成员**。也就是说，`for...of`循环内部调用的就是数据结构的`Symbol.iterator`方法。

    `for...of`循环可以使用的范围包括数组、Set 和 Map 结构、某些类似数组的对象（比如`arguments`对象、DOM中的 NodeList 对象）、后文的 Generator 对象，以及字符串。

    - ##### 数组

      数组原生具备`iterator`接口（即默认部署了`Symbol.iterator`属性），`for...of`循环本质上就是调用这个接口产生的迭代器，可以用下面的代码证明。
  
      ```js
      const arr = ['red', 'green', 'blue'];
      
      for(let v of arr) {
        console.log(v); // red green blue
      }
      
      const obj = {};
      obj[Symbol.iterator] = arr[Symbol.iterator].bind(arr);
      
      for(let v of obj) {
        console.log(v); // red green blue
      }
      ```

      上面代码中，空对象`obj`部署了数组`arr`的`Symbol.iterator`属性，结果`obj`的`for...of`循环，产生了与`arr`完全一样的结果。

      `for...of`循环可以代替数组实例的`forEach`方法。
  
      ```js
      const arr = ['red', 'green', 'blue'];
      
      arr.forEach(function (element, index) {
        console.log(element); // red green blue
        console.log(index);   // 0 1 2
      });
      ```

      JS 原有的`for...in`循环，遍历数组时只能获得数组的键名下标，而 ES6 的`for...of`则允许遍历获得键值。
  
      ```js
      var arr = ['a', 'b', 'c', 'd'];
      
      for (let a in arr) {
        console.log(a); // 0 1 2 3
      }
      
      for (let a of arr) {
        console.log(a); // a b c d
      }
      ```

      上面代码表明，`for...in`循环读取键名，`for...of`循环读取键值。如果要通过`for...of`循环，获取数组的索引，可以借助数组实例的`entries`方法和`keys`方法（参见《数组的扩展》一章）。

      `for...of`循环调用迭代器接口，数组的迭代器接口只返回具有数字索引的属性值。这一点跟`for...in`循环也不一样。
  
      ```js
      let arr = [3, 5, 7];
      arr.foo = 'hello';
      
      for (let i in arr) {
        console.log(i); // "0", "1", "2", "foo"
      }
      
      for (let i of arr) {
        console.log(i); //  "3", "5", "7"
      }
      ```

      上面代码中，`for...of`循环不会返回数组`arr`的`foo`属性。

      因此，一般只用`for in`来遍历对象，数组则通过实例方法或`for of`来遍历。

    - ##### Set 和 Map 结构

      Set 和 Map 结构也原生具有 Iterator 接口，可以直接使用`for...of`循环。
  
      ```js
      var engines = new Set(["Gecko", "Trident", "Webkit", "Webkit"]);
      for (var e of engines) {
        console.log(e);
      }
      // Gecko
      // Trident
      // Webkit
      
      var es6 = new Map();
      es6.set("edition", 6);
      es6.set("committee", "TC39");
      es6.set("standard", "ECMA-262");
      for (var [name, value] of es6) {
        console.log(name + ": " + value);
      }
      // edition: 6
      // committee: TC39
      // standard: ECMA-262
      ```

      上面代码演示了如何遍历 Set 结构和 Map 结构。值得注意的地方有两个，首先，遍历的顺序是按照各个成员被添加进数据结构的顺序。其次，Set 结构遍历时，返回的是一个值，而 Map 结构遍历时，返回的是一个数组，该数组的两个成员分别为当前 Map 成员的键名和键值。
  
      ```js
      let map = new Map().set('a', 1).set('b', 2);
      for (let pair of map) {
        console.log(pair);
      }
      // ['a', 1]
      // ['b', 2]
      
      for (let [key, value] of map) {
        console.log(key + ' : ' + value);
      }
      // a : 1
      // b : 2
      ```

    - ##### 计算生成的数据结构

      有些数据结构是在现有数据结构的基础上，计算生成的。比如，ES6 的数组、Set、Map 都部署了以下三个方法，调用后都返回迭代器对象。
  
      - `entries()` 返回一个迭代器对象，用来遍历`[键名, 键值]`组成的数组。对于数组，键名就是索引值；对于 Set，键名与键值相同。Map 结构的 Iterator 接口，默认就是调用`entries`方法。
      - `keys()` 返回一个迭代器对象，用来遍历所有的键名。
      - `values()` 返回一个迭代器对象，用来遍历所有的键值。

      这三个方法调用后生成的迭代器对象，所遍历的都是计算生成的数据结构。
  
      ```js
      let arr = ['a', 'b', 'c'];
      for (let pair of arr.entries()) {
        console.log(pair);
      }
      // [0, 'a']
      // [1, 'b']
      // [2, 'c']
      ```

    - ##### 类似数组的对象

      类似数组的对象包括好几类。下面是`for...of`循环用于字符串、DOM NodeList 对象、`arguments`对象的例子。
  
      ```js
      // 字符串
      let str = "hello";
      
      for (let s of str) {
        console.log(s); // h e l l o
      }
      
      // DOM NodeList对象
      let paras = document.querySelectorAll("p");
      
      for (let p of paras) {
        p.classList.add("test");
      }
      
      // arguments对象
      function printArgs() {
        for (let x of arguments) {
          console.log(x);
        }
      }
      printArgs('a', 'b');
      // 'a'
      // 'b'
      ```

      对于字符串来说，`for...of`循环还有一个特点，就是会正确识别 32 位 UTF-16 字符。
  
      ```js
      for (let x of 'a\uD83D\uDC0A') {
        console.log(x);
      }
      // 'a'
      // '\uD83D\uDC0A'
      ```

      并不是所有类似数组的对象都具有 Iterator 接口，一个简便的解决方法，就是使用`Array.from`方法将其转为数组。
  
      ```js
      let arrayLike = { length: 2, 0: 'a', 1: 'b' };
      
      // 报错
      for (let x of arrayLike) {
        console.log(x);
      }
      
      // 正确
      for (let x of Array.from(arrayLike)) {
        console.log(x);
      }
      ```

    - ##### 对象

      对于普通的对象，`for...of`结构不能直接使用，会报错，必须部署了 Iterator 接口后才能使用。但是，这样情况下，`for...in`循环依然可以用来遍历键名。
  
      ```js
      let es6 = {
        edition: 6,
        committee: "TC39",
        standard: "ECMA-262"
      };
      
      for (let e in es6) {
        console.log(e);
      }
      // edition
      // committee
      // standard
      
      for (let e of es6) {
        console.log(e);
      }
      // TypeError: es6[Symbol.iterator] is not a function
      ```

      上面代码表示，对于普通的对象，`for...in`循环可以遍历键名，`for...of`循环会报错。

      一种解决方法是，使用`Object.keys`方法将对象的键名生成一个数组，然后遍历这个数组。
  
      ```js
      for (var key of Object.keys(someObject)) {
        console.log(key + ': ' + someObject[key]);
      }
      ```

      另一个方法是使用 Generator 函数将对象重新包装一下。
  
      ```js
      const obj = { a: 1, b: 2, c: 3 }
      
      function* entries(obj) {
        for (let key of Object.keys(obj)) {
          yield [key, obj[key]];
        }
      }
      
      for (let [key, value] of entries(obj)) {
        console.log(key, '->', value);
      }
      // a -> 1
      // b -> 2
      // c -> 3
      ```

    - ##### 与其他遍历语法的比较

      以数组为例，JS 提供多种遍历语法。最原始的写法就是`for`循环。
  
      ```js
      for (var index = 0; index < myArray.length; index++) {
        console.log(myArray[index]);
      }
      ```

      这种写法比较麻烦，因此数组提供内置的`forEach`方法。
  
      ```js
      myArray.forEach(function (value) {
        console.log(value);
      });
      ```

      这种写法的问题在于，`forEach()`无法中途跳出循环，`break`命令或`return`命令都不能奏效。

      > `forEach(callback)`中回调函数`return`的返回值无任何意义，仅仅只是提前退出当前回调的执行（类似`continue`），但不会终止整个循环。
  
      `for...in`循环可以遍历数组的键名。
  
      ```js
      for (var index in myArray) {
        console.log(myArray[index]);
      }
      ```
  
      `for...in`循环有几个缺点。
  
      - 数组的键名是数字，但是`for...in`循环是以字符串作为键名“0”、“1”、“2”等等。
      - `for...in`循环不仅遍历数字键名，还会遍历手动添加的其他键，甚至包括原型链上的键。
      - 某些情况下，`for...in`循环会以任意顺序遍历键名。

      总之，**`for...in`循环主要是为遍历对象而设计的，不适用于遍历数组。**
  
      `for...of`循环相比上面几种做法，有一些显著的优点。
  
      ```js
      for (let value of myArray) {
        console.log(value);
      }
      ```
  
      - 有着同`for...in`一样的简洁语法，但是没有`for...in`那些缺点。
      - 不同于`forEach`方法，`for...of`循环可以与`break`、`continue`和`return`配合使用。
      - 提供了遍历所有数据结构的统一操作接口。
  
      下面是一个使用 break 语句，跳出`for...of`循环的例子。
  
      ```js
      for (var n of fibonacci) {
        if (n > 1000)
          break;
        console.log(n);
      }
      ```
  
      上面的例子，会输出斐波纳契数列小于等于 1000 的项。如果当前项大于 1000，就会使用`break`语句跳出`for...of`循环。
  
  - #### Iterator
  
    ES2015 新增了一个内置对象`Iterator`，它是一个符合[迭代器协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#迭代器协议)的对象，其提供了 `next()` 方法用以返回迭代器结果对象（信息对象）。
  
    我们自己写的具有 `next()` 方法的普通迭代器对象，可以用 `Iterator.from()`，将普通迭代器对象传入后，包装为 `Iterator` 实例。
  
    > 也可以将普通迭代器对象的原型设置为 `Iterator.prototype`，这样也能将其转为 `Iterator` 实例。但是推荐使用 `Iterator.from()` 来做。
  
    所有内置迭代器都继承了 `Iterator` 类。`Iterator` 类提供了 [`[Symbol.iterator]()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Iterator/Symbol.iterator) 方法，该方法返回迭代器对象本身，使迭代器也[可迭代](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#可迭代协议)。它还提供了一些使用迭代器的工具方法：
  
    ```js
    const arr = ['a', '', 'b', '', 'c', '', 'd', '', 'e'];
    
    arr.values() // creates an iterator
      .filter(x => x.length > 0)
      .drop(1)
      .take(3)
      .map(x => `=${x}=`)
      .toArray()
    // ['=b=', '=c=', '=d=']
    ```
  
    上面示例中，arr 是一个数组，它的 `values()` 方法返回的是一个迭代器对象，以前要使用 for...of 循环来处理，现在有了工具方法，就可以直接链式处理了。
  
    迭代器对象的工具方法，基本上与数组方法是对应的。
  
    - ##### 返回迭代器对象的方法：
  
      - `iterator.filter(filterFn)`
      - `iterator.map(mapFn)`
      - `iterator.flatMap(mapFn)`
  
    - ##### 返回布尔值的方法：
  
      - `iterator.some(fn)`
      - `iterator.every(fn)`
  
    - ##### 返回其他值的方法：
  
      - `iterator.find(fn)`
      - `iterator.reduce(reducer, initialValue?)`
  
    - ##### 不返回值的方法：
  
      - `iterator.forEach(fn)`
  
    以下是迭代器对象独有的方法。
  
    - `iterator.drop(limit)`：返回一个迭代器对象，丢弃前 limit 个成员。
    - `iterator.take(limit)`：返回一个迭代器对象，包含前 limit 个成员。
    - `iterator.toArray()`：返回一个数组，包含所有成员。

------

