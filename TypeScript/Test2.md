- ### 接口

  > 接口（interface）是对象的模板，可以看作是一种类型约定。使用了某个模板的对象，就拥有了指定的类型结构。接口的主要作用是对象、类、函数等（本质上都是对象）规定一种契约，这样可以确保代码的一致性和安全性。
  >
  > ```ts
  > interface Person {  // 接口的分隔符可以是（; , 换行）
  >     firstName: string
  >     lastName: string
  >     age: number
  > }
  > ```
  >
  > 上面示例中，定义了一个接口`Person`，它指定一个对象模板，都有三个属性`firstName`、`lastName`和`age`。任何实现这个接口的对象，都必须至少包含这三个属性，并且必须符合规定的类型。实现该接口很简单，只要指定它作为对象的类型即可：（**注意：TS 不区分对象自身的属性和原型上的属性，只要有就行**）
  >
  > ```ts
  > const p: Person = {
  >     firstName: "John",
  >     lastName: "Smith",
  >     age: 25,
  > }
  > ```
  >
  > **注意：**
  >
  > 1. 接口只能定义格式不能包含任何的实现。
  > 2. 接口描述的是类的对外接口，也就是实例的公开的属性和方法，不能定义私有的属性和方法。即接口是完全公开的。

  ###### TS中，`[]`方括号运算符用来取出接口中某个键的类型：

  ```ts
  interface Foo {
  	a: string;
  }
  type A = Foo["a"]  // string
  ```

  ###### 可以在接口的属性名前加`readonly`关键字来设置只读属性：

  ```ts
  const person: {
  	readonly age: number;  // 属性名前面加上readonly关键字，表示这个属性是只读属性，不能修改
  } = { age: 20 };
  person.age = 21;  // 报错
  ```

  ###### 严格字面量检查：

  > 如果变量的类型为接口，且赋值了一个对象字面量，此时会触发 TS 的**严格字面量检查**。此时该变量指向的对象中，有且仅能有接口中定义的这些属性，不能多也不能少：

  ```ts
  const p: {
      firstName: string
      lastName: string
      age: number
  } = {
      firstName: "John",
      lastName: "Smith",
      age: 25,
      sex: '男',  // 报错，不能多一个sex属性
  }
  ```

  > 同样报错：不能多一个sex属性。因为变量p的类型被自动推断为了对应的接口，此时也触发了严格字面量检查：

  ```ts
  const p = {
      firstName: "John",
      lastName: "Smith",
      age: 25,
  }
  p.sex = '女'  // 报错，不能多一个sex属性
  // p['sex'] = '女'  这种方式新增属性，在非严格模式下不会报错，相当于绕过了TS的类型检查，但是不推荐使用
  ```

  > TS 对字面量进行严格检查的目的，主要是防止拼写错误，可以使用*类型断言规避严格字面量检查*。

  > 编译器选项`suppressExcessPropertyErrors`也可以关闭多余属性检查。下面是它在 `tsconfig.json` 文件里面的写法：
  >
  > ```json
  > {
  >     "compilerOptions": {
  >     	"suppressExcessPropertyErrors": true
  >     }
  > }
  > ```

  ###### 最小可选属性规则：

  > 如果一个对象的所有属性都是可选的，会触发最小可选属性规则。
  >
  > ```ts
  > type Options = {
  >        a?: number;
  >        b?: number;
  >        c?: number;
  > }
  > const obj: Options = {
  >   	d: 123, // 报错
  > }
  > ```
  >
  > 上面示例中，类型`Options`是一个对象，它的所有属性都是可选的，这导致任何对象实际都符合`Options`类型。
  >
  > 为了避免这种情况，TS 添加了最小可选属性规则，规定这时属于`Options`类型的对象，必须至少存在一个可选属性，不能所有可选属性都不存在。这就是为什么上例的`myObj`对象会报错的原因。

  ###### 接口可以描述对象的各种语法：

  - ##### 对象的属性索引（属性名）：

    > TS 允许采用**属性名表达式**的写法来描述类型，称为“属性名的索引类型”。索引类型中最常见的就是属性名的字符串索引：

    ```ts
    interface A {
    	[prop: string]: number
    }
    ```

    > - 上面示例中，接口`A`的属性名类型就采用了表达式形式，写在方括号里面。`[prop: string]`的`prop`表示属性名，这个是可以随便起的，它的类型是`string`，即属性名类型为`string`。也就是说，不管这个对象有多少属性，只要属性名为字符串，该属性就必须是number类型。
    >
    > - 属性索引共有`string`、`number`和`symbol`三种类型。一个接口中，字符串索引和数值索引最多定义一个。字符串索引会约束该类型中所有名字为字符串的属性；属性的数值索引，其实约定的是数组成员的类型。
    >
    >   ```ts
    >   interface A {
    >   	[prop: number]: string
    >   }
    >   const obj: A = ["a", "b", "c"]
    >   ```
    >
    > - 如果一个接口同时定义了字符串索引和数值索引，那么数值索引必须兼容字符串索引的类型声明。因为在 JS 中，数值属性名最终会自动转换成字符串属性名。

  - ##### 对象的方法：

    ```ts
    // 写法一
    interface A {
    	f(x: boolean): string  // 该对象中必须包含f方法
    }
    // 写法二
    interface B {
    	f: (x: boolean) => string  // 该对象中必须包含f属性，它的值是一个函数
    }
    // 写法三
    interface C {
    	(x: boolean): string  // 类型C是一个函数，因为JS中，函数是特殊的对象
    }
    ```

    > - 前面加`new`关键字，表示构造函数：
    >
    >   ```ts
    >   interface StudentConstructor {
    >   	new (message?: string): Student  // 构造函数不能加名，没有意义
    >   }
    >   ```
    >
    > - 属性名可以采用表达式，所以下面的写法也是可以的。
    >
    >   ```ts
    >   const f = "f";
    >   interface A {
    >   	[f](x: boolean): string;
    >   }
    >   ```
    >
    > - 接口中的方法也可以重载。（了解即可）
    >
    >   ```ts
    >   interface A {
    >       f(): number;
    >       f(x: boolean): boolean;
    >       f(x: string, y: string): string;
    >   }
    >   ```
    >
    >   > 注意：接口里面的函数重载，不需要给出实现。但是，由于对象内部定义方法时，无法使用函数重载的语法，所以需要额外在对象外部给出函数方法的实现。
    >   >
    >   > ```ts
    >   > interface A {
    >   >        f(): number;
    >   >        f(x: boolean): boolean;
    >   >        f(x: string, y: string): string;
    >   > }
    >   > 
    >   > function MyFunc(): number;
    >   > function MyFunc(x: boolean): boolean;
    >   > function MyFunc(x: string, y: string): string;
    >   > function MyFunc(x?: boolean | string, y?: string): number | boolean | string {
    >   >        if (x === undefined && y === undefined) return 1;
    >   >        if (typeof x === "boolean" && y === undefined) return true;
    >   >        if (typeof x === "string" && typeof y === "string") return "hello";
    >   >        throw new Error("wrong parameters");
    >   > }
    >   > 
    >   > const a: A = {
    >   > 	f: MyFunc,
    >   > };
    >   > ```

  ###### 接口的继承：

  > 接口可以使用`extends`关键字继承其他的接口，并且可以多继承。多重接口继承，实际上相当于多个父接口合并之后再继承。

  - 如果子接口与父接口存在同名属性，那么子接口的属性会覆盖父接口的属性。（注意，子接口与父接口的同名属性必须是类型兼容的，不能有冲突）
  - 接口继承类：接口还可以继承类，即继承该类的所有成员。（某些类拥有私有成员和保护成员，接口可以继承这样的类，但是没有什么意义）

  ###### 接口合并：

  > 多个同名接口会自动合并成一个接口（当然合并时类型也要兼容）。
  >
  > 这是因为，JS 开发者常常对全局对象或者外部库，添加自己的属性和方法。那么，只要使用 interface 给出这些自定义属性和方法的类型，就能自动跟原始的 interface 合并，使得扩展外部类型非常方便。

  - 同名接口合并时，如果同名方法有不同的类型声明，那么会发生函数重载。而且，后面的定义比前面的定义具有更高的优先级。

    ```ts
    interface Cloner {
    	clone(animal: Animal): Animal;
    }
    interface Cloner {
    	clone(animal: Sheep): Sheep;
    }
    interface Cloner {
        clone(animal: Dog): Dog;
        clone(animal: Cat): Cat;
    }
    // 等同于
    interface Cloner {
        clone(animal: Dog): Dog;
        clone(animal: Cat): Cat;
        clone(animal: Sheep): Sheep;
        clone(animal: Animal): Animal;
    }
    ```

    > 上面示例中，`clone()`方法有不同的类型声明，会发生函数重载。这时，**越靠后的定义，优先级越高，排在函数重载的越前面。**比如，`clone(animal: Animal)`是最先出现的类型声明，就排在函数重载的最后，属于`clone()`函数最后匹配的类型。**这个规则有一个例外，同名方法之中，如果有一个参数是字面量类型，字面量类型有更高的优先级。**

  - 两个接口组成的联合类型若存在同名属性，那么该属性的类型也是联合类型。

    ```ts
    interface Circle {
        area: bigint;
    }
    interface Rectangle {
        area: number;
    }
    declare const s: Circle | Rectangle;
    s.area;  // bigint | number
    ```

- ### 类

  > 类（class）是面向对象编程的基本构件，封装了属性和方法，TS 给予了全面支持。

  - **属性的类型：**类的属性可以在顶层声明，也可以在构造方法内部声明。对于顶层声明的属性，可以在声明时同时给出类型。如果不给出类型，TS 会认为`x`和`y`的类型都是`any`。

    ```ts
    class Point {
        x!: number;
        y = 1;
        readonly id = "foo";  // 属性名前面加上 readonly 修饰符，就表示该属性是只读的，实例对象不能去修改。
        /* 注意：构造方法里面可以修改 readonly 只读属性的值 */
    }
    ```

    > 类的定义中，属性和方法后面的感叹号是非空断言，告诉 TS 它们都是非空的，后面会赋值。
    >
    > TS 有一个配置项`strictPropertyInitialization`，只要打开，就会检查类的属性是否设置了初值，如果没有就报错。但是某些情况下，不是在声明时赋值或在构造方法里面赋值。为了防止报错，此时就可以在属性名后面使用非空断言`!`，告诉 TS 它们都是非空的，后面会赋值。

    ###### 注意：类中的构造方法不能声明返回值类型，否则报错，因为构造方法总是返回实例对象。

  - **getter和setter：**接口中也可用`setter`和`getter`方法来描述对象的属性：

    ```ts
    interface Person {
        get fullName(): string;
        set fullName(name: string);
    }
    ```

    > 属性字段只是对`getter/setter`的便捷写法，上面和下面两种表达方式是等价的：

    ```ts
    interface Person {
        get fullName(): string;
    }
    ```

    > TS 对存取器有以下规则：
    >
    > 1. 如果某个属性只有`get`方法，没有`set`方法，那么该属性会被识别为只读属性。
    > 2. `get`方法与`set`方法的可访问性必须一致，要么都为公开方法，要么都为私有方法。
    > 3. （了解）TS 5.1 版之前，`set`方法的参数类型，必须兼容`get`方法的返回值类型，否则报错。TS 5.1 版做出了改变，现在两者可以不兼容。

  - **属性索引：**和接口一样，类中同样允许定义属性索引。

    ```ts
    class MyClass {
        [s: string]: boolean | ((s: string) => boolean)
        get(s: string) {
            return this[s] as boolean
        }
    }
    ```

  - **类实现接口（`implements`）：**类通过`implements`关键字来实现接口，从而为类指定一组检查条件。

    > `implements`关键字后面，不仅可以是接口，也可以是另一个类。这时，后面的类将被当作接口。
    >
    > ```ts
    > class Car {
    >        id: number = 1
    >        move(): void {}
    > }
    > class MyCar implements Car {
    >        id = 2  // 不可省略
    >        move(): void {}  // 不可省略
    > }
    > ```

  - **类与接口的合并：**TS 不允许两个同名的类，但是如果一个类和一个接口同名，那么接口会被合并进类的类型定义中。

    ```ts
    class A {
        x: number = 1
    }
    interface A {
        y: number
    }
    let a = new A()
    a.y = 10
    a.x  // 1
    a.y  // 10
    ```

    > 上面示例中，类`A`与接口`A`同名，后者会被合并进前者的类型定义。

  - **类的类型：**TS 中的类本身也是一种类型。

    ```ts
    class Color {
        name: string;
        constructor(name: string) {
            this.name = name;
        }
    }
    const green: Color = new Color("green");
    ```

    > 上面示例中，定义了一个类`Color`。它的类名就代表一种类型，实例对象`green`就属于该类型。
    >
    > 但是如果要拿类中的构造函数的类型，就不能用类名了：
    >
    > ```ts
    > // 报错
    > function createColor(ColorClass: Color, x: number, y: number) {
    > 	return new ColorClass(x, y);
    > }
    > ```
    >
    > ColorClass变量的类型是Color，但是前面要加上`typeof`，表示取Color类的类型。
    >
    > ```ts
    > function createColor(ColorClass: typeof Color, x: number, y: number): Color {
    > 	return new ColorClass(x, y);
    > }
    > ```
    >
    > 这是因为在 JS 中，类只是构造函数的一种语法糖，本质上是构造函数。加了`typeof 变量`之后，让Color被当作构造函数名，此时取的是该构造函数的类型，也就是`new (x: number, y: number) => Color`。或者直接这样做：
    >
    > ```ts
    > function createColor(
    > 	ColorClass: new (x: number, y: number) => Color,
    > // 或者用接口的写法：
    > 	// ColorClass: {
    > 	//	new (x: number, y: number): Color;
    > 	// },
    > 	x: number,
    > 	y: number,
    > ): Color {
    > 	return new ColorClass(x, y);
    > }
    > ```

  - **结构类型原则：**

    > 类同样遵循**结构类型原则**。一个对象只要满足类的实例结构，就跟该类属于同一个类型。
    >
    > 由于这种情况，运算符`instanceof`不适用于判断某个对象是否跟某个类属于同一类型，它只是原型链上的判断。
    >
    > **注意，确定两个类的兼容关系时，只检查实例成员，不考虑静态成员和构造方法。**
    >
    > 如果类中存在私有成员（private）或保护成员（protected），那么确定兼容关系时，TS 要求私有成员和保护成员来自同一个类，这意味着两个类需要存在继承关系。

  - **类中的访问控制权限修饰符**：TS中，类的内部成员的外部可访问性，由三个可访问性修饰符（access modifiers）控制：`public`、`private`和`protected`。这三个修饰符的位置，都写在属性或方法的最前面。

    - **public（默认）**：公开的，可以在任何地方被访问。

    - **protected**：受保护的，可以被其自身以及其子类访问。

    - **private**：私有的，只能在本类体中使用。

      > 严格地说，`private`定义的私有成员，并不是真正意义的私有成员。一方面，编译成 JS 后，`private`关键字就被剥离了，这时外部访问该成员就不会报错。另一方面，由于前一个原因，**TS 对于访问`private`成员没有严格禁止，使用方括号写法（`[]`）或者`in`运算符，实例对象就能访问该成员**。
      >
      > 由于`private`存在这些问题，加上它是 ES6 标准发布前出台的，而 ES6 引入了自己的私有成员写法`#propName`。因此**推荐使用 ES6 的私有写法，获得真正意义的私有成员**。

  - **构造方法的简写形式：**TS 对构造方法提供了一种简写形式。

    > 简写前：

    ```ts
    class Point {
    	x: number;
    	private readonly y: number;
    
    	constructor(x: number, y: number) {
    		this.x = x;
    		this.y = y;
    	}
    }
    ```

    > 简写后：

    ```ts
    class Point {
    	constructor(public x: number, private readonly y: number) {}  // 大括号中仍然可以写其他的代码
    }
    ```

    > 除了`public`修饰符，构造方法的参数名只要有`private`、`protected`、`readonly`修饰符，都会自动声明对应修饰符的实例属性。

  - **`override`关键字：**

    > 有些时候，我们继承他人的类，可能会在不知不觉中，就覆盖了他人的方法。为了防止这种情况，TS 4.3 引入了`override`关键字。
    >
    > ```ts
    > class B extends A {
    >   override show() {
    >     // ...
    >   }
    >   override hide() {
    >     // ...
    >   }
    > }
    > ```
    >
    > 上面示例中，B 类的`show()`方法和`hide()`方法前面加了 override 关键字，明确表明作者的意图，就是要覆盖 A 类里面的这两个同名方法。这时，如果 A 类没有定义自己的`show()`方法和`hide()`方法，就会报错。

    > 但是，这依然没有解决，子类无意中覆盖父类同名方法的问题。因此，TS 又提供了一个编译参数`noImplicitOverride`。一旦打开这个参数，子类覆盖父类的同名方法就会报错，除非使用了`override`关键字。

- ### 抽象类

  > TS 允许在类的定义前面加上关键字`abstract`，来定义一个**抽象类（abastract class）**。

  - 抽象类是不能被实例化的，它是用于被子类继承的，只能当作其他类的模板。
  - 抽象类的子类也可以是抽象类。
  - 抽象类中可以有抽象成员，也可以有非抽象成员（即属性名和方法名前带有`abstract`关键字，且不能有具体的实现代码）。抽象成员只能在抽象类中，因此继承抽象类的（非抽象）子类必须实现抽象类中的所有抽象成员。
  - 抽象类不能被实例化，它的构造函数只能在子类的构造函数中调用。
  - 抽象类是半公开的。抽象成员不能被`private`修饰，否则无法在子类中实现该成员。

- ### 泛型

  > 泛型是一种编程语言特性，允许在定义*函数、类、接口*等时使用占位符来表示类型，而不是具体的类型。使用泛型时需要提供类型参数（如果可以根据值推断出类型，或者泛型有默认值，那么使用时不提供类型参数也行）。类型参数的名字，可以随便取，但是必须为合法的标识符。习惯上，类型参数的第一个字符往往采用大写字母。一般会使用`T`（type 的第一个字母）作为类型参数的名字。如果有多个类型参数，则使用 T 后面的 U、V 等字母命名，各个参数之间使用逗号（“,”）分隔。
  >
  > 泛型可以将类型参数化，使代码可以适应不同的类型需求，同时保持类型安全。泛型是一种在编写可重用、灵活且类型安全的代码时非常有用的功能。泛型的优势包括：
  >
  > 1. **代码重用：** 可以编写与特定类型无关的通用代码，提高代码的复用性。
  > 2. **类型安全：** 在编译时进行类型检查，避免在运行时出现类型错误。
  > 3. **抽象性：** 允许编写更抽象和通用的代码，适应不同的数据类型和数据结构。
  >
  > 总之，泛型可以理解成一段类型逻辑，需要类型参数来表达。有了类型参数以后，可以在输入类型与输出类型之间，建立一一对应关系。
  >
  > ###### 注意：如果某个类型用了泛型，那么使用时就需要为泛型指定具体的类型，否则会报错。（除非可以根据值推断出类型参数，或者泛型有默认值）
  
  - **泛型函数：**
  
    ```ts
    // 写法1：
    function getId<T>(arg: T): T {
        return arg;
    }
    // 写法2：
    let myId: <T>(arg: T) => T = getId;
    // 写法3：
    let myId: { <T>(arg: T): T } = getId;
    ```
  
  - **泛型接口：**
  
    ```ts
    interface Box<T> {
        contents: T;
    }
    let box: Box<string>;
    ```
  
  - **泛型类：**
  
    ```ts
    class Pair<K, V> {
        key!: K;
        value: V;
    }
    // 匿名类表达式使用泛型：
    const Container = class<T> {
    	constructor(private readonly data: T) {}
    };
    ```
  
    > **注意：**泛型类描述的是类的实例，不包括静态属性和静态方法，因为这两者定义在类的本身。因此，它们不能引用类型参数。
  
  - **类型别名的泛型写法：**type 命令定义的类型别名，也可以使用泛型。
  
    ```ts
    type Nullable<T> = T | undefined | null;
    
    type Container<T> = { value: T };
    const a: Container<number> = { value: 0 };
    const b: Container<string> = { value: "b" };
    ```
  
  - **类型参数的默认值：**类型参数还可以设置默认值。使用时，如果没有给出类型参数的值，就会使用默认值。
  
    ```ts
    function getFirst<T = string>(arr: T[]): T {
        return arr[0];
    }
    getFirst([1, 2, 3]); // 正确
    ```
  
    > 但是，因为 TS 会从实际参数推断出`T`的值，从而覆盖掉默认值，所以上面的代码不会报错。
  
    > 一旦类型参数有默认值，就表示它是可选参数。如果有多个类型参数，可选参数必须在必选参数之后。
    >
    
  - **数组的泛型表示：**
  
    > 之前说过，数组类型有一种表示方法是`Array<T>`。这就是泛型的写法，`Array`是 TS 原生的一个类型接口，`T`是它的类型参数。事实上，在 TS 内部，数组类型的另一种写法`number[]`、`string[]`，只是`Array<number>`、`Array<string>`的简写形式。
    >
    > 在 TS 内部，`Array`是一个泛型接口，类型定义基本是下面的样子。
    >
    > ```ts
    > interface Array<T> {
    >        length: number;
    >        pop(): T | undefined;
    >        push(...items: T[]): number;
    >        // ...
    > }
    > ```
    >
    > 其他的 TS 内部数据结构，比如`Map`、`Set`和`Promise`，其实也是泛型接口，完整的写法是`Map<K, V>`、`Set<T>`和`Promise<T>`。
    >
    > TS 默认还提供一个`ReadonlyArray<T>`接口，表示只读数组。
    >
    > ```ts
    > function doStuff(values: ReadonlyArray<string>) {
    >    	values.push("hello!"); // 报错
    > }
    > ```
  
  - **类型参数的约束条件：**
  
    > TS 提供了一种语法，允许在类型参数上面写明约束条件，如果不满足条件，编译时就会报错。这样也可以有良好的语义，对类型参数进行说明。
    >
    > ```ts
    > function comp<T extends { length: number }>(a: T, b: T) {
    >        if (a.length >= b.length) {
    >            return a;
    >        }
    >        return b;
    > }
    > ```
    >
  
    > **注意：**
    >
    > - `extends`后面可以跟**接口、基本类型、联合类型、交叉类型**。
    >
    > - 类型参数可以同时设置约束条件和默认值，前提是默认值必须满足约束条件（没有`super`）。
    >
    > - 如果有多个类型参数，一个类型参数的约束条件，可以引用其他参数。
    >
    >   ```ts
    >   <T, U extends T>
    >   // 或者
    >   <T extends U, U>
    >   ```
    >
    >   > 但是，约束条件不能引用类型参数自身。
    >   >
    >   > ```ts
    >   > <T extends T>               // 报错
    >   > <T extends U, U extends T>  // 报错
    >   > ```
  
  - **使用注意点：**
  
    1. **尽量少用泛型。**泛型虽然灵活，但是会加大代码的复杂性，使其变得难读难写。一般来说，只要使用了泛型，类型声明通常都不太易读，容易写得很复杂。因此，可以不用泛型就不要用。
    2. **类型参数越少越好。**多一个类型参数，多一道替换步骤，加大复杂性。因此，类型参数越少越好。
    3. **类型参数需要出现两次。**如果类型参数在定义后只出现一次，那么很可能是不必要的。
    4. **泛型可以嵌套。**
  
- ### TS 模块

  > 任何包含`import`或`export`语句的文件，就是一个 TS 模块。如果文件不包含 export 语句，就是一个全局的脚本文件。
  >
  > 如果一个文件不包含 export 语句，但是希望把它当作一个模块（即内部变量对外不可见），可以在脚本头部添加一行语句：`export {};`，这行语句不产生任何实际作用，但会让当前文件被当作模块处理，所有它的代码都变成了内部代码。
  >
  > 在 TS 中编写基于模块的代码时，需要考虑三个主要方面：
  >
  > - **语法**: 我想使用什么语法来导入和导出内容？
  >
  > - **模块解析**: 模块名称（或路径）与磁盘上的文件之间是什么关系？
  > - **模块输出目标**: 我的输出 JS 模块应该是什么样子？

  - #### TS的模块语法：
  
    > TS 默认支持 ESM 模块化语法，除此之外还允许输出和输入类型。TS 编译器会根据`tsconfig.json`中的`module`配置项，将 TS 模块代码编译为 ES6、CommonJS 或 AMD 的 JS 模块代码，最终再通过打包工具（如Webpack）将 JS 模块打包为Bundle。
  
    > a.ts：
    >
    > ```ts
    > export type Bool = true | false;
    > ```
    >
    > b.ts：
    >
    > ```ts
    > import { Bool } from "./a";
    > let isActive: Bool = true;
    > ```
    >
    > 上面示例中，import 语句加载的是一个类型。（注意，加载文件写成`./a`，没有写脚本文件的后缀名。TS 允许加载模块时，省略模块文件的后缀名，它会自动定位，将`./a`定位到`./a.ts`）
    >
    > 编译时，可以两个脚本同时编译。
    >
    > ```shell
    > $ tsc a.ts b.ts
    > ```
    >
    > 上面命令会将`a.ts`和`b.ts`分别编译成`a.js`和`b.js`。
    >
    > 也可以只编译`b.ts`，因为它是入口脚本，tsc 会自动编译它依赖的所有脚本。
    >
    > ```shell
    > $ tsc b.ts
    > ```
    >
    > 上面命令发现`b.ts`依赖`a.ts`，就会自动寻找`a.ts`，也将其同时编译，因此编译产物还是`a.js`和`b.js`两个文件。
  
    > 但是有时需要知道import导入的是一个类型还是实实在在的变量，因为导入的类型本质上是给TS编译器看的，运行时并没有类型代码。因此为了区分导入的是类型还是变量，TS对 ESM 模块化做了扩展：
    >
    > 1. 在导入的类型前加`type`关键字：`import { type A, a } from './a'`，说明A是一个TS类型，而a是变量。
    > 2. 用TS中的`import type`语句单独导入所有的TS类型：`import type { A } from './a'`，（**注意只能导入TS类型**）
    >
    > import type 语句也可以输入默认类型。
    >
    > ```ts
    > import type DefaultType from 'moduleA';
    > ```
    >
    > import type 在一个名称空间下，输入所有类型的写法如下。
    >
    > ```ts
    > import type * as TypeNS from 'moduleA';
    > ```
  
    > 同样的，为了区分export导出的是类型还是变量，导出类型也有两种语法：（**注意导出的是 TS 类型**）
    >
    > ```ts
    > type A = "a";
    > type B = "b";
    > // 方法一
    > export { type A, type B };
    > // 方法二
    > export type { A, B };
    > ```
    >
    > 注意：默认导出 TS 类型，没有前面加`type`的这种写法。
    >
    > ```ts
    > interface A { name: string }
    > export default A;
    > ```
  
    > **importsNotUsedAsValues 编译设置：**（过时了，目前推荐使用`verbatimModuleSyntax`）
    >
    > TS 特有的输入类型（type）的 import 语句，编译成 JS 时怎么处理的呢？
    >
    > TS 提供了`importsNotUsedAsValues`编译设置项，有三个值。
    >
    > 1. `remove`：这是默认值，自动删除所有类型的 import 语句。
    > 2. `preserve`：删除类型的 import 并保留该语句，只是删掉了其中涉及类型的部分。这会引发该导入语句的执行，因此会保留该文件中的副作用。
    > 3. `error`：保留输入类型的 import 语句（与`preserve`相同），但是必须用`import type`的写法，否则报错。
  
  - #### CommonJS 模块
  
    > TS 具有 ES 模块语法，会被编译为 ESM、CommonJS 或 AMD 模块代码（在*大多数情况下*会被编译为 CommonJS 模块代码）。以下这种语法可以确保您的 TS 代码，与编译后的 CommonJS 模块化语法之间存在一对一的匹配关系。
  
    - `import =` 语句：
  
      > ```ts
      > import fs = require("fs")
      > const code = fs.readFileSync("hello.ts", "utf8")
      > ```
      >
      > 相当于：`const fs = require("fs")`
  
    - `export =` 语句：
  
      > ```ts
      >let obj = { foo: 123 }
      > export = obj
      > ```
      > 
      > 相当于：`module.exports = obj`
  
    ###### 注意：`import=`和`export=`它俩是一对，用`export=`导出的模块只能用`import=`导入。
  
  - #### 模块定位
  
    > 模块定位（module resolution）指的是确定 import 语句和 export 语句里面的模块文件位置。
    >
    > 编译参数`moduleResolution`，用来指定具体使用哪一种定位算法。常用的算法有两种：**经典策略**和**Node策略**。
    >
    > 如果没有指定`moduleResolution`，它的默认值与编译参数`module`有关。`module`设为`commonjs`时（编译后的 JS 脚本采用 CommonJS 模块格式），`moduleResolution`的默认值为`Node`，即采用Node策略。其他情况下（`module`设为 es2015、 esnext、amd, system, umd 等等），都采用经典策略。（`moduleResolution`、`baseUrl`、`paths`、`rootDirs`这些配置项都会影响TS的模块定位策略）
  
    > **相对模块和非相对模块：**
    >
    > 加载模块时，根据模块的路径，可以分为**相对模块**和**非相对模块**两种。
    >
    > 相对模块指的是路径以`/`、`./`、`../`开头的模块（分别相对于磁盘根、当前路径、上级路径）。非相对模块指的是不带有基路径前缀、以模块名为前缀的模块。
    >
    > 相对模块通常用于加载内部自己写的模块。非相对模块的定位，是由`baseUrl`属性或`paths`模块映射而确定的，通常用于加载 NodeJS 的内置模块、以及`node_modules/`目录下的第三方模块。
    >
    > ```ts
    > import Entry from "./components/Entry"  // 相对路径
    > import * as $ from "jquery"  // 模块路径
    > ```
  
    > **路径映射：**（针对于非相对模块，对于相对模块没有影响）
    >
    > TS 允许开发者在`tsconfig.json`文件里面，手动指定脚本模块的路径。
    >
    > - `baseUrl`：值是字符串，指定非相对模块的基路径（没有任何前缀的模块路径）。
    > - `paths`：指定某个非相对模块去哪些路径下查找该模块文件。它的值是对象，其中对象的value是一个字符串数组，可以指定多个路径。如果第一个脚本路径不存在，那么就加载第二个路径，以此类推。
    > - `rootDirs`：指定多个项目根目录（`string[]`）。模块定位时必须查找的其他目录。
    >
    > **tsc 的`--traceResolution`参数：**
    >
    > 由于模块定位的过程很复杂，tsc 命令有一个`--traceResolution`参数，能够在编译时在命令行显示模块定位的每一步。
    >
    > **tsc 的`--noResolve`参数：**
    >
    > tsc 命令的`--noResolve`参数，表示在命令行显示模块定位步骤时，只考虑在命令行传入的模块。
  
    - ##### Node策略：
  
      > Node 策略就是模拟 Node.js 的模块加载方法。以下是Node策略的模块查找规则：
      >
      > - **相对路径**依然是以当前脚本的路径作为“基准路径”。比如，脚本文件`a.ts`里面有一行代码`let x = require("./b");`，TS 按照以下顺序查找。
      >
      >   1. 当前目录是否包含`b.ts`、`b.tsx`、`b.d.ts`。
      >   2. 当前目录是否有子目录`b`，该子目录是否存在文件`package.json`，该文件的`types`字段是否指定了入口文件，如果是的就加载该文件。
      >   3. 当前目录的子目录`b`是否包含`index.ts`、`index.tsx`、`index.d.ts`。
      >
      > - **模块路径**则是以当前脚本的路径（`baseUrl`）作为起点，逐级向上层目录查找是否存在子目录`node_modules`。比如，脚本文件`a.js`有一行`let x = require("b");`，TS 按照以下顺序进行查找。
      >
      >   1. 当前目录的子目录`node_modules`是否包含`b.ts`、`b.tsx`、`b.d.ts`。
      >   2. 当前目录的子目录`node_modules`，是否包含子目录`b`，其中是否包含文件`package.json`，该文件的`types`字段是否指定了入口文件，如果是的就加载该文件。否则在该目录中查找`index.ts`、`index.tsx`、`index.d.ts`。
      >   3. 当前目录的子目录`node_modules`里面，是否包含子目录`@types`，在该目录中查找文件`b.d.ts`。
      >   5. 进入上一层目录，重复上面 3 步，直到找到为止。（即`node_module`的自动向上查找特性）
      >   
      >比如，当`baseUrl`设置为`./src`，此时导入模块`import $ from 'jquery'`后，就会先去src下找`jquery.ts`或`jquery/index.ts`，找不到的话就从src开始逐层向上找`node_modules`，看里面有没有jquery模块。
      
    - ##### 经典策略：
  
      > **经典模块解析策略**（`"moduleResolution": "classic"`）是 TS 早期版本的默认模块解析策略。它的行为相对简单，主要用于非 Node.js 环境（如浏览器环境）。以下是经典策略的模块查找规则：
      >
      > - **对于相对路径导入**（以 `./` 或 `../` 开头）：
      >   - 直接根据相对路径解析文件。
      >   - 例如：`import './moduleA'` 会查找当前文件所在目录下的 `moduleA.ts` 或 `moduleA.d.ts`。
      > - **对于非相对路径导入**（如 `import 'jquery'`）：
      >   - 从当前文件所在目录开始，逐级向上查找与模块名匹配的文件。
      >   - 查找顺序：
      >     1. 查找 `./moduleName.ts` 或 `./moduleName.d.ts`。
      >     2. 查找 `./moduleName/index.ts` 或 `./moduleName/index.d.ts`。
      >     3. 如果未找到，继续向上一级目录重复上述步骤，直到根目录。
  
  - #### TS的模块输出选项
  
    > 有两个选项会影响生成的 JS 模块代码：
    >
    > - `target`：用于确定哪些 JS 特性会被降级（转换为在旧的 JS 运行时中运行）以及哪些特性保持不变。
    > - `module`：设置编译生成哪种语法的 JS 模块代码（ES2020/CommonJS/UMD）。

------

TODO

> - **鸭子类型（Duck Typing）**：鸭子类型是动态类型语言中的一种风格，是多态（polymorphism）的一种表现形式。在这种风格中，一个对象有效的语义，不是由继承自特定的类或实现特定的接口，而是由*当前对象中所有的方法和属性*来决定。（TODO）
>
>   > 可以这样表述："当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。"
>
>  应用到编程中，这意味着：
> 
>  - **不关心对象是什么**：在鸭子类型中，我们不关心一个对象的具体类型是什么，只关心它能否完成我们需要的操作。
>     - **动态检查**：在运行时，系统会检查对象是否具有所需的方法或属性，而不是在编译时检查其类型。
>   - **灵活性高**：这种方式使得代码更加灵活，可以更容易地扩展和修改。
> 
>   > 举例说明：
>   >
>   > 假设你有一个函数 `makeSound`，它接受一个对象并调用其 `quack` 方法：
>  >
>     > ```ts
>  > function makeSound(duck: any) {
>   > 	duck.quack()
>  > }
>   > ```
>  >
>   > 在这个例子中，我们不关心传入的对象具体是什么类型，只要它有`quack`方法即可。如果传入的对象有一个`quack`方法，那么这个函数就能正常工作。因此参数duck设置为any。但是如果将duck参数的类型指定为`Duck`，那么就必须确保传入的对象实现了`Duck`接口，否则编译报错。
>   >
>   > 总之：鸭子类型强调的是对象的行为而不是其类型，这使得代码更加灵活和动态。


> 子类的那些只设置类型、没有设置初值的顶层成员在基类中被赋值后，会在子类被重置为`undefined`，解决方法就是使用`declare`命令，去声明顶层成员的类型，告诉 TS 这些成员的赋值由基类实现。
>
> 注意，静态成员不能使用泛型的类型参数。
>
> 类的继承：
>
> 注意，`extends`关键字后面不一定是类名，可以是一个表达式，只要它的类型是构造函数就可以了。

> ## 顶层属性的处理方法
>
> 对于类的顶层属性，TypeScript 早期的处理方法，与后来的 ES2022 标准不一致。这会导致某些代码的运行结果不一样。
>
> 类的顶层属性在 TypeScript 里面，有两种写法。
>
> ```
> class User {
>   // 写法一
>   age = 25;
> 
>   // 写法二
>   constructor(private currentYear: number) {}
> }
> ```
>
> 上面示例中，写法一是直接声明一个实例属性`age`，并初始化；写法二是顶层属性的简写形式，直接将构造方法的参数`currentYear`声明为实例属性。
>
> TypeScript 早期的处理方法是，先在顶层声明属性，但不进行初始化，等到运行构造方法时，再完成所有初始化。
>
> ```
> class User {
>   age = 25;
> }
> 
> // TypeScript 的早期处理方法
> class User {
>   age: number;
> 
>   constructor() {
>     this.age = 25;
>   }
> }
> ```
>
> 上面示例中，TypeScript 早期会先声明顶层属性`age`，然后等到运行构造函数时，再将其初始化为`25`。
>
> ES2022 标准里面的处理方法是，先进行顶层属性的初始化，再运行构造方法。这在某些情况下，会使得同一段代码在 TypeScript 和 JavaScript 下运行结果不一致。
>
> 这种不一致一般发生在两种情况。第一种情况是，顶层属性的初始化依赖于其他实例属性。
>
> ```
> class User {
>   age = this.currentYear - 1998;
> 
>   constructor(private currentYear: number) {
>     // 输出结果将不一致
>     console.log('Current age:', this.age);
>   }
> }
> 
> const user = new User(2023);
> ```
>
> 上面示例中，顶层属性`age`的初始化值依赖于实例属性`this.currentYear`。按照 TypeScript 的处理方法，初始化是在构造方法里面完成的，会输出结果为`25`。但是，按照 ES2022 标准的处理方法，初始化在声明顶层属性时就会完成，这时`this.currentYear`还等于`undefined`，所以`age`的初始化结果为`NaN`，因此最后输出的也是`NaN`。
>
> 第二种情况与类的继承有关，子类声明的顶层属性在父类完成初始化。
>
> ```
> interface Animal {
>   animalStuff: any;
> }
> 
> interface Dog extends Animal {
>   dogStuff: any;
> }
> 
> class AnimalHouse {
>   resident: Animal;
> 
>   constructor(animal:Animal) {
>     this.resident = animal;
>   }
> }
> 
> class DogHouse extends AnimalHouse {
>   resident: Dog;
> 
>   constructor(dog:Dog) {
>     super(dog);
>   }
> }
> ```
>
> 上面示例中，类`DogHouse`继承自`AnimalHouse`。它声明了顶层属性`resident`，但是该属性的初始化是在父类`AnimalHouse`完成的。不同的设置运行下面的代码，结果将不一致。
>
> ```
> const dog = {
>   animalStuff: 'animal',
>   dogStuff: 'dog'
> };
> 
> const dogHouse = new DogHouse(dog);
> 
> console.log(dogHouse.resident) // 输出结果将不一致
> ```
>
> 上面示例中，TypeScript 的处理方法，会使得`resident`属性能够初始化，所以输出参数对象的值。但是，ES2022 标准的处理方法是，顶层属性的初始化先于构造方法的运行。这使得`resident`属性不会得到赋值，因此输出为`undefined`。
>
> 为了解决这个问题，同时保证以前代码的行为一致，TypeScript 从3.7版开始，引入了编译设置`useDefineForClassFields`。这个设置设为`true`，则采用 ES2022 标准的处理方法，否则采用 TypeScript 早期的处理方法。
>
> 它的默认值与`target`属性有关，如果输出目标设为`ES2022`或者更高，那么`useDefineForClassFields`的默认值为`true`，否则为`false`。关于这个设置的详细说明，参见官方 3.7 版本的[发布说明](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#the-usedefineforclassfields-flag-and-the-declare-property-modifier)。
>
> 如果希望避免这种不一致，让代码在不同设置下的行为都一样，那么可以将所有顶层属性的初始化，都放到构造方法里面。
>
> ```
> class User  {
>   age: number;
> 
>   constructor(private currentYear: number) {
>     this.age = this.currentYear - 1998;
>     console.log('Current age:', this.age);
>   }
> }
> 
> const user = new User(2023);
> ```
>
> 上面示例中，顶层属性`age`的初始化就放在构造方法里面，那么任何情况下，代码行为都是一致的。
>
> 对于类的继承，还有另一种解决方法，就是使用`declare`命令，去声明子类顶层属性的类型，告诉 TypeScript 这些属性的初始化由父类实现。
>
> ```
> class DogHouse extends AnimalHouse {
>   declare resident: Dog;
> 
>   constructor(dog:Dog) {
>     super(dog);
>   }
> }
> ```
>
> 上面示例中，`resident`属性的类型声明前面用了`declare`命令。这种情况下，这一行代码在编译成 JavaScript 后就不存在，那么也就不会有行为不一致，无论是否设置`useDefineForClassFields`，输出结果都是一样的。
