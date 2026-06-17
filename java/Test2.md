- ## Java核心类

  - #### String

    > 在Java中，`String`是一个引用类型，它本身也是一个`class`。但是，Java编译器对`String`有特殊处理，即可以直接用`"..."`来表示一个字符串：
    >
    > ```java
    > String s1 = "Hello!";
    > ```
    >
    > 实际上字符串在`String`内部是通过一个`char[]`数组表示的（JDK8及之前），因此，按下面的写法也是可以的：
    >
    > ```java
    >String s2 = new String(new char[] {'H', 'e', 'l', 'l', 'o', '!'});
    > ```
    > 
    > 因为`String`太常用了，所以Java提供了`"..."`这种字符串字面量表示方法。
    >
    > Java字符串的一个重要特点就是字符串**不可变**。这种不可变性是通过内部的`private final char[]`字段，以及没有任何修改`char[]`的方法实现的。
    >
    
    - ##### 字符串比较：
    
      > 当我们想要比较两个字符串是否相同时，要特别注意，我们实际上是想比较字符串的内容是否相同。必须使用`equals()`方法而不能用`==`。
      >
    
    - ##### 类型转换：
    
      > 要把任意基本类型或引用类型转换为字符串，可以使用静态方法`String.valueOf()`。这是一个重载方法，编译器会根据参数自动选择合适的方法：
      >
      > ```java
      > String.valueOf(123); // "123"
      > String.valueOf(45.67); // "45.67"
      > String.valueOf(true); // "true"
      > String.valueOf(new Object()); // 类似java.lang.Object@636be97c
      > ```
      >
      > 要把字符串转换为其他类型，就需要根据情况。例如，把字符串转换为`int`类型：
      >
      > ```java
      > int n1 = Integer.parseInt("123"); // 123
      > int n2 = Integer.parseInt("ff", 16); // 按十六进制转换，255
      > ```
      >
      > 把字符串转换为`boolean`类型：
      >
      > ```java
      > boolean b1 = Boolean.parseBoolean("true"); // true
      > boolean b2 = Boolean.parseBoolean("FALSE"); // false
      > ```
      >
      > 要特别注意，`Integer`有个`getInteger(String)`方法，它不是将字符串转换为`int`，而是把该字符串对应的系统变量转换为`Integer`：
      >
      > ```java
      > Integer.getInteger("java.version"); // 版本号，11
      > ```
      >
    
    - ##### 转换为char[]：
    
      > `String`和`char[]/byte[]`类型可以互相转换，方法是：
      >
      > ```java
      > char[] cs = "Hello".toCharArray(); // String -> char[]
      > String s = new String(cs); // char[] -> String
      > 
      > byte[] bt = s.getBytes(); // String -> byte[]
      > String ss = new String(bt); // byte[] -> String
      > ```
      >
    
    - ##### 字符编码：
    
      > 在早期的计算机系统中，为了给字符编码，美国国家标准学会（American National Standard Institute：ANSI）制定了一套英文字母、数字和常用符号的编码，它占用一个字节，编码范围从`0`到`127`，最高位始终为`0`，称为`ASCII`编码。例如，字符`'A'`的编码是`0x41`，字符`'1'`的编码是`0x31`。
      >
      > 如果要把汉字也纳入计算机编码，很显然一个字节是不够的。`GB2312`标准使用两个字节表示一个汉字，其中第一个字节的最高位始终为`1`，以便和`ASCII`编码区分开。例如，汉字`'中'`的`GB2312`编码是`0xd6d0`。
      >
      > 类似的，日文有`Shift_JIS`编码，韩文有`EUC-KR`编码，这些编码因为标准不统一，同时使用，就会产生冲突。
      >
      > 为了统一全球所有语言的编码，全球统一码联盟发布了`Unicode`编码，它把世界上主要语言都纳入同一个编码，这样，中文、日文、韩文和其他语言就不会冲突。
      >
      > `Unicode`编码需要两个或者更多字节表示，我们可以比较中英文字符在`ASCII`、`GB2312`和`Unicode`的编码：
      >
      > 英文字符`'A'`的`ASCII`编码和`Unicode`编码：
      >
      > ```
      >          ┌────┐
      > ASCII:   │ 41 │
      >          └────┘
      >          ┌────┬────┐
      > Unicode: │ 00 │ 41 │
      >          └────┴────┘
      > ```
      >
      > 英文字符的`Unicode`编码就是简单地在前面添加一个`00`字节。
      >
      > 中文字符`'中'`的`GB2312`编码和`Unicode`编码：
      >
      > ```
      >          ┌────┬────┐
      > GB2312:  │ d6 │ d0 │
      >          └────┴────┘
      >          ┌────┬────┐
      > Unicode: │ 4e │ 2d │
      >          └────┴────┘
      > ```
      >
      > 那我们经常使用的`UTF-8`又是什么编码呢？因为英文字符的`Unicode`编码高字节总是`00`，包含大量英文的文本会浪费空间，所以，出现了`UTF-8`编码，它是一种变长编码，用来把固定长度的`Unicode`编码变成1～4字节的变长编码。通过`UTF-8`编码，英文字符`'A'`的`UTF-8`编码变为`0x41`，正好和`ASCII`码一致，而中文`'中'`的`UTF-8`编码为3字节`0xe4b8ad`。
      >
      > `UTF-8`编码的另一个好处是容错能力强。如果传输过程中某些字符出错，不会影响后续字符，因为`UTF-8`编码依靠高字节位来确定一个字符究竟是几个字节，它经常用来作为传输编码。
      >
      > 在Java中，`char`类型实际上就是两个字节的`Unicode`（UTF16）编码。如果我们要手动把字符串转换成其他编码，可以这样做：
      >
      > ```java
      > byte[] b1 = "Hello".getBytes(); // 按系统默认编码转换，不推荐
      > byte[] b2 = "Hello".getBytes("UTF-8"); // 按UTF-8编码转换
      > byte[] b2 = "Hello".getBytes("GBK"); // 按GBK编码转换
      > byte[] b3 = "Hello".getBytes(StandardCharsets.UTF_8); // 按UTF-8编码转换
      > ```
      >
      > 注意：转换编码后，就不再是`char`类型，而是`byte`类型表示的数组。
      >
      > 如果要把已知编码的`byte[]`转换为`String`，可以这样做：
      >
      > ```java
      > byte[] b = ...
      > String s1 = new String(b, "GBK"); // 按GBK转换
      > String s2 = new String(b, StandardCharsets.UTF_8); // 按UTF-8转换
      > ```
      >
      > 转换为`byte[]`时，始终优先考虑`UTF-8`编码。
      >
      > 始终牢记：Java的`String`和`char`在内存中总是以Unicode（UTF16）编码表示。
    
      ###### JDK9对String的优化：
    
      > 对于不同版本的JDK，`String`类在内存中有不同的优化方式。具体来说，早期JDK版本（JDK8及之前）的`String`总是以`char[]`存储，它的定义如下：
      >
      > ```java
      > public final class String {
      >     private final char[] value;
      >     private final int offset;
      >     private final int count;
      > }
      > ```
      >
      > 而较新的JDK版本（JDK9及之后）的`String`则以`byte[]`存储：如果`String`仅包含ASCII字符，则每个`byte`存储一个字符，否则，每两个`byte`存储一个字符，这样做的目的是为了节省内存，因为大量的长度较短的`String`通常仅包含ASCII字符：
      >
      > ```java
      > public final class String {
      >     private final byte[] value;
      >     private final byte coder; // 0 = LATIN1, 1 = UTF16
      > }
      > ```
      >
      > 对于使用者来说，`String`内部的优化不影响任何已有代码，因为它的`public`方法签名是不变的。
    
  - #### `StringBuilder`和`StringBuffer`
  
    > Java编译器对`String`做了特殊处理，使得我们可以直接用`+`拼接字符串。
    >
    > 考察下面的循环代码：
    >
    > ```java
    > String s = "";
    > for (int i = 0; i < 1000; i++) {
    >    	s = s + "," + i;
    > }
    > ```
    >
    > 虽然可以直接拼接字符串，但是，在循环中，每次循环都会创建新的字符串对象，然后扔掉旧的字符串。这样，绝大部分字符串都是临时对象，不但浪费内存，还会影响GC效率。
    >
    > 为了能高效拼接字符串，Java标准库提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，这样，往`StringBuilder`中新增字符时，不会创建新的临时对象：
    >
    > ```java
    >StringBuilder sb = new StringBuilder(1024);
    > for (int i = 0; i < 1000; i++) {
    >     sb.append(',');
    >     sb.append(i);
    >    }
    >    String s = sb.toString();
    > ```
    > 
    > `StringBuilder`还可以进行链式操作：
    >
    > ```java
    >// 链式操作
    > public class Main {
    >    public static void main(String[] args) {
    >         var sb = new StringBuilder(1024);
    >         sb.append("Mr ")
    >           .append("Bob")
    >              .append("!")
    >              .insert(0, "Hello, ");
    >            System.out.println(sb.toString());
    >        }
    >    }
    >    ```
    >    
    >    如果我们查看`StringBuilder`的源码，可以发现，进行链式操作的关键是，定义的`append()`方法会返回`this`，这样，就可以不断调用自身的其他方法。
    > 
    > 注意：对于普通的字符串`+`操作，并不需要我们将其改写为`StringBuilder`，因为Java编译器在编译时就自动把多个连续的`+`操作编码为`StringConcatFactory`的操作。在运行期，`StringConcatFactory`会自动把字符串连接操作优化为数组复制或者`StringBuilder`操作。
    >
    > 你可能还听说过`StringBuffer`，这是Java早期的一个`StringBuilder`的线程安全版本，它通过同步来保证多个线程操作`StringBuffer`也是安全的，但是同步会带来执行速度的下降，因此现在很少使用。
    >
    
  - #### `StringJoiner`
  
    > 要高效拼接字符串，应该使用`StringBuilder`。很多时候，我们拼接的字符串像这样：
    >
    > ```java
    >// 输出: Hello Bob, Alice, Grace!
    > public class Main {
    >     public static void main(String[] args) {
    >         String[] names = {"Bob", "Alice", "Grace"};
    >            var sb = new StringBuilder();
    >            sb.append("Hello ");
    >            for (String name : names) {
    >            	sb.append(name).append(", ");
    >            }
    >            // 注意去掉最后的", ":
    >            sb.delete(sb.length() - 2, sb.length());
    >            sb.append("!");
    >            System.out.println(sb.toString());
    >        }
    >    }
    >    ```
    > 
    > 类似用分隔符拼接数组的需求很常见，所以Java标准库还提供了一个`StringJoiner`来干这个事：
    >
    > ```java
    >import java.util.StringJoiner;
    > public class Main {
    >    public static void main(String[] args) {
    >         String[] names = {"Bob", "Alice", "Grace"};
    >         var sj = new StringJoiner(", ");
    >         for (String name : names) {
    >            	sj.add(name);
    >            }
    >            System.out.println(sj.toString());
    >        }
    >    }
    >    ```
    >    
    >    慢着！用`StringJoiner`的结果少了前面的`"Hello "`和结尾的`"!"`。此时可以给`StringJoiner`指定“开头”和“结尾”：
    > 
    > ```java
    >import java.util.StringJoiner;
    > public class Main {
    >    public static void main(String[] args) {
    >         String[] names = {"Bob", "Alice", "Grace"};
    >        var sj = new StringJoiner(", ", "Hello ", "!");
    >         for (String name : names) {
    >         	sj.add(name);
    >         }
    >            System.out.println(sj.toString());
    >        }
    >    }
    >    ```
    >    
    >    `String`还提供了一个静态方法`String.join()`，这个方法在内部使用了`StringJoiner`来拼接字符串，在不需要指定“开头”和“结尾”的时候，用`String.join()`更方便：
    >    
    >    ```java
    > String[] names = {"Bob", "Alice", "Grace"};
    > var s = String.join(", ", names);
    >```
    > 
    
  - #### 包装类型
  
    > 我们已经知道，Java的数据类型分两种：
    >
    > - 基本类型：`byte`，`short`，`int`，`long`，`boolean`，`float`，`double`，`char`；
    > - 引用类型：所有`class`和`interface`类型。
    >
    > 引用类型可以赋值为`null`，表示空，但基本类型不能赋值为`null`：
    >
    > ```java
    > String s = null;
    > int n = null; // compile error!
    > ```
    >
    > 那么，如何把一个基本类型视为对象（引用类型）？
    >
    > 比如，想要把`int`基本类型变成一个引用类型，我们可以定义一个`Integer`类，它只包含一个实例字段`int`，这样，`Integer`类就可以视为`int`的包装类（Wrapper Class）：
    >
    > ```java
    >public class Integer {
    >     private int value;
    > 
    >     public Integer(int value) {
    >         this.value = value;
    >     }
    > 
    >     public int intValue() {
    >         return this.value;
    >     }
    > }
    > ```
    > 
    > 定义好了`Integer`类，我们就可以把`int`和`Integer`互相转换：
    >
    > ```java
    >Integer n = null;
    > Integer n2 = new Integer(99);
    >int n3 = n2.intValue();
    > ```
    > 
    > 实际上，因为包装类型非常有用，Java核心库为每种基本类型都提供了对应的包装类型：
    > 
    > | 基本类型 | 对应的引用类型      |
    >| :------- | :------------------ |
    > | boolean  | java.lang.Boolean   |
    >| byte     | java.lang.Byte      |
    > | short    | java.lang.Short     |
    >| int      | java.lang.Integer   |
    > | long     | java.lang.Long      |
    > | float    | java.lang.Float     |
    > | double   | java.lang.Double    |
    > | char     | java.lang.Character |
    > 
    > 我们可以直接使用，并不需要自己去定义：
    > 
    > ```java
    > // Integer:
    > public class Main {
    >    public static void main(String[] args) {
    >         int i = 100;
    >        // 通过new操作符创建Integer实例(不推荐使用,会有编译警告):
    >         Integer n1 = new Integer(i);
    >         // 通过静态方法valueOf(int)创建Integer实例:
    >         Integer n2 = Integer.valueOf(i);
    >         // 通过静态方法valueOf(String)创建Integer实例:
    >         Integer n3 = Integer.valueOf("100");
    >         System.out.println(n3.intValue());
    >     }
    > }
    > ```
    > 
    
    ###### 自动装箱/自动拆箱：
    
    > 因为`int`和`Integer`可以互相转换：
    >
    > ```java
    > int i = 100;
    >Integer n = Integer.valueOf(i);
    > int x = n.intValue();
    >```
    > 
    > 所以，Java编译器可以帮助我们自动在`int`和`Integer`之间转型：
    > 
    > ```java
    >Integer n = 100; // 编译器自动使用Integer.valueOf(int)
    > int x = n; // 编译器自动使用Integer.intValue()
    >```
    > 
    >这种直接把`int`变为`Integer`的赋值写法，称为自动装箱（Auto Boxing），反过来，把`Integer`变为`int`的赋值写法，称为自动拆箱（Auto Unboxing）。
    > 
    >注意：自动装箱和自动拆箱只发生在编译阶段，目的是为了少写代码。
    > 
    > **装箱和拆箱会影响代码的执行效率**，因为编译后的`class`代码是严格区分基本类型和引用类型的。并且，自动拆箱执行时可能会报`NullPointerException`：
    > 
    > ```java
    > // NullPointerException
    > public class Main {
    >     public static void main(String[] args) {
    >         Integer n = null;
    >         int i = n;
    >     }
    > }
    > ```
    > 
    
    ###### 不变类（被`final`修饰的类）：
    
    > **所有的包装类型都是不变类**。我们查看`Integer`的源码可知，它的核心代码如下：
    >
    > ```java
    > public final class Integer {
    >    private final int value;
    > }
    >```
    > 
    > 因此，一旦创建了`Integer`对象，该对象就是不变的。
    > 
    > 因为`Integer.valueOf()`可能始终返回同一个`Integer`实例，因此，在我们自己创建`Integer`的时候，以下两种方法：
    >
    > - 方法1：`Integer n = new Integer(100);`
    >- 方法2：`Integer n = Integer.valueOf(100);`
    > 
    >其中方法2更好，因为方法1总是创建新的`Integer`实例，方法2把内部优化留给`Integer`的实现者去做，即使在当前版本没有优化，也有可能在下一个版本进行优化。
    > 
    >我们把能创建“新”对象的静态方法称为静态工厂方法。`Integer.valueOf()`就是静态工厂方法，它尽可能地返回缓存的实例以节省内存。
    > 
    > **创建新对象时，优先选用静态工厂方法而不是new操作符。**
    > 
    > 如果我们考察`Byte.valueOf()`方法的源码，可以看到，标准库返回的`Byte`实例全部是缓存实例，但调用者并不关心静态工厂方法以何种方式创建新实例还是直接返回缓存的实例。
    
    ###### 进制转换：
    
    > `Integer`类本身还提供了大量方法，例如，最常用的静态方法`parseInt()`可以把字符串解析成一个整数：
    >
    > ```java
    > int x1 = Integer.parseInt("100"); // 100
    >int x2 = Integer.parseInt("100", 16); // 256,因为按16进制解析
    > ```
    >
    > `Integer`还可以把整数格式化为指定进制的字符串：
    > 
    > ```java
    > // Integer:
    >public class Main {
    >     public static void main(String[] args) {
    >        System.out.println(Integer.toString(100)); // "100",表示为10进制
    >         System.out.println(Integer.toString(100, 36)); // "2s",表示为36进制
    >        System.out.println(Integer.toHexString(100)); // "64",表示为16进制
    >         System.out.println(Integer.toOctalString(100)); // "144",表示为8进制
    >        System.out.println(Integer.toBinaryString(100)); // "1100100",表示为2进制
    >     }
    > }
    > ```
    > 
    > Java的包装类型还定义了一些有用的静态变量：
    > 
    > ```java
    > // boolean只有两个值true/false，其包装类型只需要引用Boolean提供的静态字段:
    > Boolean t = Boolean.TRUE;
    > Boolean f = Boolean.FALSE;
    > // int可表示的最大/最小值:
    > int max = Integer.MAX_VALUE; // 2147483647
    > int min = Integer.MIN_VALUE; // -2147483648
    >// long类型占用的bit和byte数量:
    > int sizeOfLong = Long.SIZE; // 64 (bits)
    >int bytesOfLong = Long.BYTES; // 8 (bytes)
    > ```
    >
    > 最后，所有的整数和浮点数的包装类型都继承自`Number`，因此，可以非常方便地直接通过包装类型获取各种基本类型：
    > 
    > ```java
    > // 向上转型为Number:
    > Number num = new Integer(999);
    >// 获取byte, int, long, float, double:
    > byte b = num.byteValue();
    >int n = num.intValue();
    > long ln = num.longValue();
    >float f = num.floatValue();
    > double d = num.doubleValue();
    > ```
    > 
    
    ###### 处理无符号整型：
    
    > 在Java中，并没有无符号整型（Unsigned）的基本数据类型。`byte`、`short`、`int`和`long`都是带符号整型，最高位是符号位。而C语言则提供了CPU支持的全部数据类型，包括无符号整型。无符号整型和有符号整型的转换在Java中就需要借助包装类型的静态方法完成。
    >
    > 例如，byte是有符号整型，范围是`-128`~`+127`，但如果把`byte`看作无符号整型，它的范围就是`0`~`255`。我们把一个负的`byte`按无符号整型转换为`int`：
    > 
    >```java
    > // Byte
    >public class Main {
    >     public static void main(String[] args) {
    >         byte x = -1;
    >         byte y = 127;
    >         System.out.println(Byte.toUnsignedInt(x)); // 255
    >        System.out.println(Byte.toUnsignedInt(y)); // 127
    >     }
    >}
    > ```
    >
    > 因为`byte`的`-1`的二进制表示是`11111111`，以无符号整型转换后的`int`就是`255`。
    >
    > 类似的，可以把一个`short`按unsigned转换为`int`，把一个`int`按unsigned转换为`long`。
    > 
    
  - #### `JavaBean`
  
    > 在Java中，有很多`class`的定义都符合这样的规范：
    >
    > - 若干`private`实例字段；
    > - 通过`public`方法来读写实例字段。
    >
    > 例如：
    >
    > ```java
    > public class Person {
    >     private String name;
    >     private int age;
    > 
    >     public String getName() { return this.name; }
    >     public void setName(String name) { this.name = name; }
    > 
    >     public int getAge() { return this.age; }
    >     public void setAge(int age) { this.age = age; }
    > }
    > ```
    >
    > 如果读写方法符合以下这种命名规范：
    >
    > ```java
    >// 读方法:
    > public Type getXyz()
    > // 写方法:
    > public void setXyz(Type value)
    > ```
    > 
    > 那么这种`class`被称为`JavaBean`：
    >
    > ![java-bean](https://liaoxuefeng.com/books/java/oop/core/javabean/javabean.jpg)
    >
    > 上面的字段是`xyz`，那么读写方法名分别以`get`和`set`开头，并且后接大写字母开头的字段名`Xyz`，因此两个读写方法名分别是`getXyz()`和`setXyz()`。
    >
    > `boolean`字段比较特殊，它的读方法一般命名为`isXyz()`：
    >
    > ```java
    >// 读方法:
    > public boolean isChild()
    >// 写方法:
    > public void setChild(boolean value)
    > ```
    > 
    > 我们通常把一组对应的读方法（`getter`）和写方法（`setter`）称为属性（`property`）。例如，`name`属性：
    > 
    > - 对应的读方法是`String getName()`
    >- 对应的写方法是`setName(String)`
    > 
    >只有`getter`的属性称为只读属性（read-only），例如，定义一个age只读属性：
    > 
    >- 对应的读方法是`int getAge()`
    > - 无对应的写方法`setAge(int)`
    > 
    >类似的，只有`setter`的属性称为只写属性（write-only）。
    > 
    >很明显，只读属性很常见，只写属性不常见。
    > 
    > 属性只需要定义`getter`和`setter`方法，不一定需要对应的字段。例如，`child`只读属性定义如下：
    >
    > ```java
    >public class Person {
    >     private String name;
    >    private int age;
    > 
    >    public String getName() { return this.name; }
    >     public void setName(String name) { this.name = name; }
    > 
    >     public int getAge() { return this.age; }
    >     public void setAge(int age) { this.age = age; }
    > 
    >     public boolean isChild() {
    >         return age <= 6;
    >     }
    > }
    > ```
    > 
    > 可以看出，`getter`和`setter`也是一种数据封装的方法。
    > 
    
    ###### JavaBean的作用：
    
    > JavaBean主要用来传递数据，即把一组数据组合成一个JavaBean便于传输。此外，JavaBean可以方便地被IDE工具分析，生成读写属性的代码，主要用在图形界面的可视化设计中。
    >
    > 通过IDE，可以快速生成`getter`和`setter`。例如，在Eclipse中，先输入以下代码：
    > 
    >```java
    > public class Person {
    >    private String name;
    >     private int age;
    > }
    > ```
    > 
    > 然后，点击右键，在弹出的菜单中选择“Source”，“Generate Getters and Setters”，在弹出的对话框中选中需要生成`getter`和`setter`方法的字段，点击确定即可由IDE自动完成所有方法代码。
    
    ###### 枚举JavaBean的所有属性：
    
    > 要枚举一个JavaBean的所有属性，可以直接使用Java核心库提供的`Introspector`：
    >
    > ```java
    > import java.beans.*;
    >
    > public class Main {
    >    public static void main(String[] args) throws Exception {
    >         BeanInfo info = Introspector.getBeanInfo(Person.class);
    >         for (PropertyDescriptor pd : info.getPropertyDescriptors()) {
    >             System.out.println(pd.getName());
    >             System.out.println("  " + pd.getReadMethod());
    >             System.out.println("  " + pd.getWriteMethod());
    >         }
    >     }
    > }
    > 
    > class Person {
    >     private String name;
    >     private int age;
    >
    >     public String getName() {
    >        return name;
    >     }
    >
    >     public void setName(String name) {
    >         this.name = name;
    >     }
    > 
    >     public int getAge() {
    >         return age;
    >    }
    > 
    >    public void setAge(int age) {
    >         this.age = age;
    >    }
    > }
    >```
    > 
    >运行上述代码，可以列出所有的属性，以及对应的读写方法。注意`class`属性是从`Object`继承的`getClass()`方法带来的。
    > 
    
  - ### 枚举
  
    > 在Java中，我们可以通过enum枚举来定义一组常量。枚举可以应用在`switch`语句中。因为枚举天生具有类型信息和有限个枚举常量，所以比`int`、`String`类型更适合用在`switch`语句中：
    >
    > ```java
    > // switch
    >    public class Main {
    >        public static void main(String[] args) {
    >            Weekday day = Weekday.SUN;
    >            switch(day) {
    >            case MON:
    >            case TUE:
    >            case WED:
    >         case THU:
    >         case FRI:
    >            System.out.println("Today is " + day + ". Work at office!");
    >             break;
    >        case SAT:
    >         case SUN:
    >            System.out.println("Today is " + day + ". Work at home!");
    >             break;
    >         default:
    >                throw new RuntimeException("cannot process " + day);
    >         }
    >     }
    >}
    > 
    >enum Weekday {
    >     MON, TUE, WED, THU, FRI, SAT, SUN;
    >}
    > ```
    > 
    >    加上`default`语句，可以在漏写某个枚举常量时自动报错，从而及时发现错误。
    >    
    >    `enum`类型的每个常量在JVM中只有一个唯一实例，所以可以直接用`==`比较。
    
    > 定义枚举类是通过关键字`enum`实现的，我们只需依次列出枚举的常量名。和`int`定义的常量相比，使用`enum`定义枚举有如下好处：
    >
    > 1. 首先，`enum`常量本身带有类型信息，即`Weekday.SUN`类型是`Weekday`，编译器会自动检查出类型错误。例如，下面的语句不可能编译通过：
    > 
    >       ```java
    >       int day = 1;
    >       if (day == Weekday.SUN) { // Compile error: bad operand types for binary operator '=='
    >       }
    >       ```
    >    
    >    2. 其次，不可能引用到非枚举的值，因为无法通过编译。
    > 
    > 3. 最后，不同类型的枚举不能互相比较或者赋值，因为类型不符。例如，不能给一个`Weekday`枚举类型的变量赋值为`Color`枚举类型的值：
    >
    >    ```java
    >   Weekday x = Weekday.SUN; // ok!
    >    Weekday y = Color.RED; // Compile error: incompatible types
    >   ```
    > 
    >    这就使得编译器可以在编译期自动检查出所有可能的潜在错误。
    
    ###### enum类型：
    
    > 通过`enum`定义的枚举类，和其他的`class`有什么区别？
    >
    > 答案是没有任何区别。`enum`定义的类型就是`class`，只不过它有以下几个特点：
    > 
    >    - 定义的`enum`类型总是继承自`java.lang.Enum`，且无法被继承；
    >    - 只能定义出`enum`的实例，而无法通过`new`操作符创建`enum`的实例；
    >    - 定义的每个实例都是引用类型的唯一实例；
    >    - 可以将`enum`类型用于`switch`语句。
    >    
    >    例如，我们定义的`Color`枚举类：
    >    
    > ```java
    > public enum Color {
    >    RED, GREEN, BLUE;
    > }
    >```
    > 
    >编译器编译出的`class`大概就像这样：
    > 
    > ```java
    >    public final class Color extends Enum { // 继承自Enum，标记为final class
    >     // 每个实例均为全局唯一:
    >     public static final Color RED = new Color();
    >    public static final Color GREEN = new Color();
    >     public static final Color BLUE = new Color();
    >    // private构造方法，确保外部无法调用new操作符:
    >     private Color() {}
    >}
    > ```
    > 
    >    所以，编译后的`enum`类和普通`class`并没有任何区别。但是我们自己无法按定义普通`class`那样来定义`enum`，必须使用`enum`关键字，这是Java语法规定的。
    >    
    >    因为`enum`是一个`class`，每个枚举的值都是`class`实例，因此，这些实例有一些方法：
    > 
    > - `name()`：返回常量名，例如：
    >
    >   ```java
    >  String s = Weekday.SUN.name(); // "SUN"
    >   ```
    >
    > - `ordinal()`：返回定义的常量的顺序，从0开始计数，例如：
    > 
    >   ```java
    >      int n = Weekday.MON.ordinal(); // 1
    >   ```
    > 
    >  改变枚举常量定义的顺序就会导致`ordinal()`返回值发生变化。例如：
    > 
    >  ```java
    >   public enum Weekday {
    >      SUN, MON, TUE, WED, THU, FRI, SAT;
    >   }
    >   ```
    >    
    >      和
    >    
    >   ```java
    >   public enum Weekday {
    >      MON, TUE, WED, THU, FRI, SAT, SUN;
    >   }
    >  ```
    > 
    >  的`ordinal`就是不同的。如果在代码中编写了类似`if(x.ordinal()==1)`这样的语句，就要保证`enum`的枚举顺序不能变。新增的常量必须放在最后。
    > 
    >   有些童鞋会想，`Weekday`的枚举常量如果要和`int`转换，使用`ordinal()`不是非常方便？比如这样写：
    >
    >   ```java
    >  String task = Weekday.MON.ordinal() + "/ppt";
    >   saveToFile(task);
    >  ```
    > 
    >   但是，如果不小心修改了枚举的顺序，编译器是无法检查出这种逻辑错误的。要编写健壮的代码，就不要依靠`ordinal()`的返回值。因为`enum`本身是`class`，所以我们可以定义`private`的构造方法，并且，给每个枚举常量添加字段：
    > 
    >   ```java
    >   // enum
    >   public class Main {
    >       public static void main(String[] args) {
    >           Weekday day = Weekday.SUN;
    >           if (day.dayValue == 6 || day.dayValue == 0) {
    >               System.out.println("Work at home!");
    >           } else {
    >               System.out.println("Work at office!");
    >           }
    >       }
    >   }
    >   
    >   enum Weekday {
    >      MON(1), TUE(2), WED(3), THU(4), FRI(5), SAT(6), SUN(0);
    >   
    >      public final int dayValue;
    >   
    >      private Weekday(int dayValue) {
    >           this.dayValue = dayValue;
    >      }
    >   }
    >  ```
    > 
    >   这样就无需担心顺序的变化，新增枚举常量时，也需要指定一个`int`值。
    > 
    >   注意：枚举类的字段也可以是非final类型，即可以在运行期修改，但是不推荐这样做！
    > 
    >  默认情况下，对枚举常量调用`toString()`会返回和`name()`一样的字符串。但是，`toString()`可以被覆写，而`name()`则不行。我们可以给`Weekday`添加`toString()`方法：
    > 
    >  ```java
    >   // enum
    >  public class Main {
    >       public static void main(String[] args) {
    >          Weekday day = Weekday.SUN;
    >           if (day.dayValue == 6 || day.dayValue == 0) {
    >               System.out.println("Today is " + day + ". Work at home!");
    >           } else {
    >               System.out.println("Today is " + day + ". Work at office!");
    >          }
    >       }
    >  }
    >   
    >  enum Weekday {
    >       MON(1, "星期一"), TUE(2, "星期二"), WED(3, "星期三"), THU(4, "星期四"), FRI(5, "星期五"), SAT(6, "星期六"), SUN(0, "星期日");
    >  
    >       public final int dayValue;
    >      private final String chinese;
    >   
    >      private Weekday(int dayValue, String chinese) {
    >           this.dayValue = dayValue;
    >           this.chinese = chinese;
    >       }
    >   
    >       @Override
    >       public String toString() {
    >          return this.chinese;
    >       }
    >  }
    >   ```
    >
    >   覆写`toString()`的目的是在输出时更有可读性。
    >
    >   注意：**判断枚举常量的名字，要始终使用`name()`方法，绝不能调用`toString()`！**
    
  - #### Record记录
  
    > 使用`String`、`Integer`等类型的时候，这些类型都是**不变类**，一个**不变类具有以下特点**：
    >
    > 1. 定义class时使用`final`，无法派生子类；
    > 2. 每个字段使用`final`，保证创建实例后无法修改任何字段。
    >
    > 假设我们希望定义一个`Point`类，有`x`、`y`两个变量，同时它是一个不变类，可以这么写：
    >
    > ```java
    > public final class Point {
    >     private final int x;
    >     private final int y;
    > 
    >     public Point(int x, int y) {
    >         this.x = x;
    >         this.y = y;
    >     }
    > 
    >     public int x() {
    >         return this.x;
    >     }
    > 
    >     public int y() {
    >         return this.y;
    >     }
    > }
    > ```
    >
    > 为了保证不变类的比较，还需要正确覆写`equals()`和`hashCode()`方法，这样才能在集合类中正常使用。后续我们会详细讲解正确覆写`equals()`和`hashCode()`，这里演示`Point`不变类的写法目的是，这些代码写起来都非常简单，但是很繁琐。
    
    ###### record：
    
    > 从Java 14开始，引入了新的`Record`类。我们定义`Record`类时，使用关键字`record`。把上述`Point`类改写为`Record`类，代码如下：
    >
    > ```java
    > // Record
    >public class Main {
    >     public static void main(String[] args) {
    >        Point p = new Point(123, 456);
    >         System.out.println(p.x());
    >         System.out.println(p.y());
    >         System.out.println(p);
    >     }
    > }
    > 
    > record Point(int x, int y) {}
    > ```
    > 
    > 仔细观察`Point`的定义：
    > 
    > ```java
    > record Point(int x, int y) {}
    > ```
    > 
    > 把上述定义改写为class，相当于以下代码：
    > 
    > ```java
    > final class Point extends Record {
    >    private final int x;
    >     private final int y;
    >
    >     public Point(int x, int y) {
    >        this.x = x;
    >         this.y = y;
    >    }
    > 
    >    public int x() {
    >         return this.x;
    >     }
    > 
    >     public int y() {
    >         return this.y;
    >     }
    > 
    >     public String toString() {
    >         return String.format("Point[x=%s, y=%s]", x, y);
    >     }
    > 
    >     public boolean equals(Object o) {
    >         ...
    >    }
    >     public int hashCode() {
    >        ...
    >     }
    >}
    > ```
    > 
    > 除了用`final`修饰class以及每个字段外，编译器还自动为我们创建了构造方法，和字段名同名的方法，以及覆写`toString()`、`equals()`和`hashCode()`方法。
    >
    > 换句话说，使用`record`关键字，可以一行写出一个不变类。
    >
    > 和`enum`类似，我们自己不能直接手动继承`Record`来实现，只能通过`record`关键字由编译器实现继承。
    >
    
    ###### 构造方法：
    
    > 编译器默认按照`record`声明的变量顺序自动创建一个构造方法，并在方法内给字段赋值。那么问题来了，如果我们要检查参数，应该怎么办？
    >
    > 假设`Point`类的`x`、`y`不允许负数，我们就得给`Point`的构造方法加上检查逻辑：
    > 
    >```java
    > public record Point(int x, int y) {
    >    public Point {
    >         if (x < 0 || y < 0) {
    >             throw new IllegalArgumentException();
    >         }
    >     }
    > }
    > ```
    > 
    > 注意到方法`public Point {...}`被称为Compact Constructor，它的目的是让我们编写检查逻辑，编译器最终生成的构造方法如下：
    > 
    > ```java
    > public final class Point extends Record {
    >     public Point(int x, int y) {
    >         // 这是我们编写的Compact Constructor:
    >         if (x < 0 || y < 0) {
    >             throw new IllegalArgumentException();
    >         }
    >         // 这是编译器继续生成的赋值代码:
    >         this.x = x;
    >         this.y = y;
    >    }
    >     ...
    >}
    > ```
    >
    > 作为`record`的`Point`仍然可以添加静态方法。一种常用的静态方法是`of()`方法，用来创建`Point`：
    >
    > ```java
    >public record Point(int x, int y) {
    >     public static Point of() {
    >         return new Point(0, 0);
    >     }
    >     public static Point of(int x, int y) {
    >         return new Point(x, y);
    >     }
    > }
    > ```
    > 
    > 这样我们可以写出更简洁的代码：
    > 
    > ```java
    > var z = Point.of();
    >var p = Point.of(123, 456);
    > ```
    >
    
  - #### `BigInteger`
  
    > 在Java中，由CPU原生提供的整型最大范围是64位`long`型整数。使用`long`型整数可以直接通过CPU指令进行计算，速度非常快。
    >
    > 如果我们使用的整数范围超过了`long`型怎么办？这个时候，就只能用软件来模拟一个大整数。`java.math.BigInteger`就是用来表示任意大小的整数。`BigInteger`内部用一个`int[]`数组来模拟一个非常大的整数：
    >
    > ```java
    > BigInteger bi = new BigInteger("1234567890");
    > System.out.println(bi.pow(5)); // 2867971860299718107233761438093672048294900000
    > ```
    >
    > 对`BigInteger`做运算的时候，只能使用实例方法，例如，加法运算：
    >
    > ```java
    >BigInteger i1 = new BigInteger("1234567890");
    > BigInteger i2 = new BigInteger("12345678901234567890");
    > BigInteger sum = i1.add(i2); // 12345678902469135780
    > ```
    > 
    > 和`long`型整数运算比，`BigInteger`不会有范围限制，但缺点是速度比较慢。
    >
    > 也可以把`BigInteger`转换成`long`型：
    >
    > ```java
    >BigInteger i = new BigInteger("123456789000");
    > System.out.println(i.longValue()); // 123456789000
    >System.out.println(i.multiply(i).longValueExact()); // java.lang.ArithmeticException: BigInteger out of long range
    > ```
    > 
    > 使用`longValueExact()`方法时，如果超出了`long`型的范围，会抛出`ArithmeticException`。
    > 
    > `BigInteger`和`Integer`、`Long`一样，也是不可变类，并且也继承自`Number`类。因为`Number`定义了转换为基本类型的几个方法：
    >
    > - 转换为`byte`：`byteValue()`
    >- 转换为`short`：`shortValue()`
    > - 转换为`int`：`intValue()`
    >- 转换为`long`：`longValue()`
    > - 转换为`float`：`floatValue()`
    >- 转换为`double`：`doubleValue()`
    > 
    > 因此，通过上述方法，可以把`BigInteger`转换成基本类型。如果`BigInteger`表示的范围超过了基本类型的范围，转换时将丢失高位信息，即结果不一定是准确的。**如果需要准确地转换成基本类型，可以使用`intValueExact()`、`longValueExact()`等方法，在转换时如果超出范围，将直接抛出`ArithmeticException`异常**。
    > 
    > 如果`BigInteger`的值甚至超过了`float`的最大范围（3.4x1038），那么返回的float是什么呢？
    > 
    > ```java
    >// BigInteger to float
    > import java.math.BigInteger;
    >
    > public class Main {
    >    public static void main(String[] args) {
    >         BigInteger n = new BigInteger("999999").pow(99);
    >         float f = n.floatValue();
    >         System.out.println(f); // Infinity
    >     }
    > }
    > ```
    > 
    
  - #### `BigDecimal`
  
    > 和`BigInteger`类似，`BigDecimal`可以表示一个任意大小且**精度完全准确**的浮点数。
    >
    > ```java
    > BigDecimal bd = new BigDecimal("123.4567");
    > System.out.println(bd.multiply(bd)); // 15241.55677489
    > ```
    >
    > `BigDecimal`用`scale()`表示小数位数，例如：
    >
    > ```java
    >BigDecimal d1 = new BigDecimal("123.45");
    > BigDecimal d2 = new BigDecimal("123.4500");
    > BigDecimal d3 = new BigDecimal("1234500");
    > System.out.println(d1.scale()); // 2,两位小数
    > System.out.println(d2.scale()); // 4
    > System.out.println(d3.scale()); // 0
    > ```
    > 
    > 通过`BigDecimal`的`stripTrailingZeros()`方法，可以将一个`BigDecimal`格式化为一个相等的，但去掉了末尾0的`BigDecimal`：
    >
    > ```java
    >BigDecimal d1 = new BigDecimal("123.4500");
    > BigDecimal d2 = d1.stripTrailingZeros();
    >System.out.println(d1.scale()); // 4
    > System.out.println(d2.scale()); // 2,因为去掉了00
    > 
    > BigDecimal d3 = new BigDecimal("1234500");
    > BigDecimal d4 = d3.stripTrailingZeros();
    > System.out.println(d3.scale()); // 0
    > System.out.println(d4.scale()); // -2
    > ```
    > 
    > **如果一个`BigDecimal`的`scale()`返回负数，例如，`-2`，表示这个数是个整数，并且末尾有2个0**。
    > 
    > 可以对一个`BigDecimal`设置它的`scale`，如果精度比原始值低，那么按照指定的方法进行四舍五入或者直接截断：
    >
    > ```java
    >import java.math.BigDecimal;
    > import java.math.RoundingMode;
    >
    > public class Main {
    >    public static void main(String[] args) {
    >         BigDecimal d1 = new BigDecimal("123.456789");
    >         BigDecimal d2 = d1.setScale(4, RoundingMode.HALF_UP); // 四舍五入，123.4568
    >         BigDecimal d3 = d1.setScale(4, RoundingMode.DOWN); // 直接截断，123.4567
    >         System.out.println(d2);
    >         System.out.println(d3);
    >        }
    >    }
    >    ```
    >    
    >    对`BigDecimal`做加、减、乘时，精度不会丢失，但是做除法时，存在无法除尽的情况，这时，就必须指定精度以及如何进行截断：
    >    
    >    ```java
    > BigDecimal d1 = new BigDecimal("123.456");
    > BigDecimal d2 = new BigDecimal("23.456789");
    >BigDecimal d3 = d1.divide(d2, 10, RoundingMode.HALF_UP); // 保留10位小数并四舍五入
    > BigDecimal d4 = d1.divide(d2); // 报错：ArithmeticException，因为除不尽
    >```
    > 
    >还可以对`BigDecimal`做除法的同时求余数：
    > 
    > ```java
    > import java.math.BigDecimal;
    > 
    > public class Main {
    >     public static void main(String[] args) {
    >        BigDecimal n = new BigDecimal("12.345");
    >         BigDecimal m = new BigDecimal("0.12");
    >        BigDecimal[] dr = n.divideAndRemainder(m);
    >         System.out.println(dr[0]); // 102
    >        System.out.println(dr[1]); // 0.105
    >     }
    > }
    > ```
    > 
    >    调用`divideAndRemainder()`方法时，返回的数组包含两个`BigDecimal`，分别是商和余数，其中商总是整数，余数不会大于除数。我们可以利用这个方法判断两个`BigDecimal`是否是整数倍数：
    >    
    >    ```java
    >    BigDecimal n = new BigDecimal("12.75");
    >    BigDecimal m = new BigDecimal("0.15");
    >    BigDecimal[] dr = n.divideAndRemainder(m);
    >    if (dr[1].signum() == 0) {
    > 	// n是m的整数倍
    > }
    >```
    > 
    >在比较两个`BigDecimal`的值是否相等时，要特别注意，使用`equals()`方法不但要求两个`BigDecimal`的值相等，还要求它们的`scale()`相等：
    > 
    >```java
    > BigDecimal d1 = new BigDecimal("123.456");
    > BigDecimal d2 = new BigDecimal("123.45600");
    > System.out.println(d1.equals(d2)); // false,因为scale不同
    > System.out.println(d1.equals(d2.stripTrailingZeros())); // true,因为d2去除尾部0后scale变为3
    > System.out.println(d1.compareTo(d2)); // 0 = 相等, -1 = d1 < d2, 1 = d1 > d2
    >    ```
    > 
    > 必须使用`compareTo()`方法来比较，它根据两个值的大小分别返回负数、正数和`0`，分别表示小于、大于和等于。
    >
    > **注意：总是使用compareTo()比较两个BigDecimal的值，不要使用equals()！**
    >
    > 如果查看`BigDecimal`的源码，可以发现，实际上一个`BigDecimal`是通过一个`BigInteger`和一个`scale`来表示的，即`BigInteger`表示一个完整的整数，而`scale`表示小数位数：
    >
    > ```java
    >public class BigDecimal extends Number implements Comparable<BigDecimal> {
    >     private final BigInteger intVal;
    >     private final int scale;
    > }
    > ```
    > 
    > `BigDecimal`也是从`Number`继承的，也是**不可变对象**。
    
  - #### 常用工具类
  
    > Java的核心库提供了大量的现成的类供我们使用。本节我们介绍几个常用的工具类。
    
    - Math：顾名思义，`Math`类就是用来进行数学计算的，它提供了大量的静态方法来便于我们实现数学计算。
    
      > 有些同学可能注意到Java标准库还提供了一个`StrictMath`，它提供了和`Math`几乎一模一样的方法。这两个类的区别在于，由于浮点数计算存在误差，不同的平台（例如x86和ARM）计算的结果可能不一致（指误差不同），因此，`StrictMath`保证所有平台计算结果都是完全相同的，而`Math`会尽量针对平台优化计算速度，所以，绝大多数情况下，使用`Math`就足够了。
    
    - HexFormat：在处理`byte[]`数组时，我们经常需要与十六进制字符串转换，自己写起来比较麻烦，用Java标准库提供的`HexFormat`则可以方便地帮我们转换。
    
      > 要将`byte[]`数组转换为十六进制字符串，可以用`formatHex()`方法：
      >
      > ```java
      > import java.util.HexFormat;
      > 
      > public class Main {
      >     public static void main(String[] args) throws InterruptedException {
      >         byte[] data = "Hello".getBytes();
      >         HexFormat hf = HexFormat.of();
      >         String hexData = hf.formatHex(data); // 48656c6c6f
      >     }
      > }
      > ```
      >
      > 如果要定制转换格式，则使用定制的`HexFormat`实例：
      >
      > ```java
      > // 分隔符为空格，添加前缀0x，大写字母:
      > HexFormat hf = HexFormat.ofDelimiter(" ").withPrefix("0x").withUpperCase();
      > hf.formatHex("Hello".getBytes())); // 0x48 0x65 0x6C 0x6C 0x6F
      > ```
      >
      > 从十六进制字符串到`byte[]`数组转换，使用`parseHex()`方法：
      >
      > ```java
      > byte[] bs = HexFormat.of().parseHex("48656c6c6f");
      > ```
      >
    
    - Random：`Random`用来**创建伪随机数**。所谓伪随机数，是指只要给定一个初始的种子，产生的随机数序列是完全一样的。
    
      > 要生成一个随机数，可以使用`nextInt()`、`nextLong()`、`nextFloat()`、`nextDouble()`：
      >
      > ```java
      > Random r = new Random();
      > r.nextInt(); // 2071575453,每次都不一样
      > r.nextInt(10); // 5,生成一个[0,10)之间的int
      > r.nextLong(); // 8811649292570369305,每次都不一样
      > r.nextFloat(); // 0.54335...生成一个[0,1)之间的float
      > r.nextDouble(); // 0.3716...生成一个[0,1)之间的double
      > ```
      >
      > 有童鞋问，每次运行程序，生成的随机数都是不同的，没看出**伪随机数**的特性来。这是因为我们创建`Random`实例时，如果不给定种子，就使用系统当前时间戳作为种子，因此每次运行时，种子不同，得到的伪随机数序列就不同。
      >
      > 如果我们在创建`Random`实例时指定一个种子，就会得到完全确定的随机数序列：
      >
      > ```java
      > import java.util.Random;
      > 
      > public class Main {
      >     public static void main(String[] args) {
      >         Random r = new Random(12345);
      >         for (int i = 0; i < 10; i++) {
      >         	System.out.println(r.nextInt(100));
      >         }
      >         // 51, 80, 41, 28, 55...
      >     }
      > }
      > ```
      >
      > **`Math.random()`实际上内部调用了`Random`类**，所以它也是伪随机数，只是我们无法指定种子。
    
    - SecureRandom：有伪随机数，就有真随机数。实际上真正的真随机数只能通过量子力学原理来获取，而我们想要的是一个不可预测的安全的随机数，`SecureRandom`就是用来创建安全的随机数的：
    
      > ```java
      > SecureRandom sr = new SecureRandom();
      > System.out.println(sr.nextInt(100));
      > ```
      >
      > `SecureRandom`无法指定种子，它使用RNG（random number generator）算法。JDK的`SecureRandom`实际上有多种不同的底层实现，有的使用安全随机种子加上伪随机数算法来产生安全的随机数，有的使用真正的随机数生成器。实际使用的时候，可以优先获取高强度的安全随机数生成器，如果没有提供，再使用普通等级的安全随机数生成器：
      >
      > ```java
      > import java.util.Arrays;
      > import java.security.SecureRandom;
      > import java.security.NoSuchAlgorithmException;
      > 
      > public class Main {
      > 	public static void main(String[] args) {
      >         SecureRandom sr = null;
      >         try {
      >             sr = SecureRandom.getInstanceStrong(); // 获取高强度安全随机数生成器
      >         } catch (NoSuchAlgorithmException e) {
      >             sr = new SecureRandom(); // 获取普通的安全随机数生成器
      >         }
      >         byte[] buffer = new byte[16];
      >         sr.nextBytes(buffer); // 用安全随机数填充buffer
      >         System.out.println(Arrays.toString(buffer));
      >     }
      > }
      > ```
      >
      > `SecureRandom`的安全性是通过操作系统提供的安全的随机种子来生成随机数。这个种子是通过CPU的热噪声、读写磁盘的字节、网络流量等各种随机事件产生的“熵”。
      >
      > 在密码学中，安全的随机数非常重要。如果使用不安全的伪随机数，所有加密体系都将被攻破。因此，时刻牢记必须使用`SecureRandom`来产生安全的随机数。
      >
      > **注意：需要使用安全随机数的时候，必须使用SecureRandom，绝不能使用Random！**
  
- ## 异常

- ## 反射

- ## 注解

  - #### 注解的定义：

    > 什么是注解（Annotation）？注解是放在Java源码的类、方法、字段、参数前的一种特殊“注释”：
    >
    > ```java
    > // this is a component:
    > @Resource("hello")
    > public class Hello {
    >        @Inject
    >        int n;
    > 
    >        @PostConstruct
    >        public void hello(@Param String name) {
    >        	System.out.println(name);
    >        }
    > 
    >        @Override
    >        public String toString() {
    >        	return "Hello";
    >        }
    > }
    > ```
    >
    > 注释会被编译器直接忽略，而注解则可以被编译器打包进入class文件，因此，注解是一种用作标注的“元数据”。
    >
    > 从JVM的角度看，注解本身对代码逻辑没有任何影响，如何使用注解完全由工具决定。
    >
    > Java的注解可以分为三类：
    >
    > 1. 第一类是由编译器使用的注解，例如：
    >
    >    - `@Override`：让编译器检查该方法是否正确地实现了覆写；
    >
    >    - `@SuppressWarnings`：告诉编译器忽略此处代码产生的警告。
    >
    >      > 这类注解不会被编译进入`.class`文件，它们在编译后就被编译器扔掉了。
    > 
    >2. 第二类是由工具处理`.class`文件使用的注解，比如有些工具会在加载class的时候，对class做动态修改，实现一些特殊的功能。这类注解会被编译进入`.class`文件，但加载结束后并不会存在于内存中。这类注解只被一些底层库使用，一般我们不必自己处理。
    > 
    >3. 第三类是在程序运行期能够读取的注解，它们在加载后一直存在于JVM中，这也是最常用的注解。例如，一个配置了`@PostConstruct`的方法会在调用构造方法后自动被调用（这是Java代码读取该注解实现的功能，JVM并不会识别该注解）。
    > 
    >定义一个注解时，还可以定义配置参数。配置参数可以包括：
    > 
    >- 所有基本类型；
    > - String；
    >- 枚举类型；
    > - 基本类型、String、Class以及枚举的数组。
    > 
    > 因为配置参数必须是常量，所以，上述限制保证了注解在定义时就已经确定了每个参数的值。
    > 
    >注解的配置参数可以有默认值，缺少某个配置参数时将使用默认值。
    > 
    >如果一个注解中属性只有一个，且名字是value的话，赋值时可以省略属性名；如果注解的属性只赋一个值时，数组的大括号可以省略；
    > 
    >如果只写注解，相当于全部使用默认值。
    > 
    >举个栗子，对以下代码：
    > 
    >```java
    > public class Hello {
    >    @Check(min=0, max=100, value=55)
    >     public int n;
    > 
    >     @Check(value=99)
    >     public int p;
    > 
    >     @Check(99) // @Check(value=99)
    >     public int x;
    > 
    >     @Check
    >     public int y;
    > }
    > ```
    > 
    > `@Check`就是一个注解。第一个`@Check(min=0, max=100, value=55)`明确定义了三个参数，第二个`@Check(value=99)`只定义了一个`value`参数，它实际上和`@Check(99)`是完全一样的。最后一个`@Check`表示所有参数都使用默认值。
    
  - #### 使用注解：
  
    > Java语言使用`@interface`语法来定义注解（`Annotation`），它的格式如下：
    >
    > ```java
    > public @interface Report {
    >        int type() default 0;
    >        String level() default "info";
    >        String value() default "";
    > }
    > ```
    >
    > 注解的参数类似无参数方法，可以用`default`设定一个默认值（强烈推荐）。最常用的参数应当命名为`value`。
    >

    ###### 元注解：
  
    > 有一些注解可以修饰其他注解，这些注解就称为元注解（meta annotation）。Java标准库已经定义了一些元注解，我们只需要使用元注解，通常不需要自己去编写元注解。
  
    1. `@Target`：最常用的元注解是`@Target`，它的类型是`ElementType[]`枚举数组。使用`@Target`可以定义`Annotation`注解能够被写在源码的哪些位置：
  
       > - 类或接口：`ElementType.TYPE`
       > - 字段：`ElementType.FIELD`
       > - 方法：`ElementType.METHOD`
       > - 构造方法：`ElementType.CONSTRUCTOR`
       > - 方法形参：`ElementType.PARAMETER`
       >
       > 例如，定义注解`@Report`可用在方法上，我们必须添加一个`@Target(ElementType.METHOD)`：
       >
       > ```java
       > @Target(ElementType.METHOD)  // 注解的属性只赋一个值时，数组的大括号可以省略
       > public @interface Report {
       >     int type() default 0;
       >     String level() default "info";
       >     String value() default "";
       > }
       > ```
       >
       > 定义注解`@Report`可用在方法或字段上，可以把`@Target`注解参数变为数组`{ ElementType.METHOD, ElementType.FIELD }`：
       >
       > ```java
       > @Target({ ElementType.METHOD, ElementType.FIELD })
       > public @interface Report {...}
       > ```
       >
  
    2. `@Retention`：定义了`Annotation`的生命周期，即被标注的注解最终保存到哪里，类型为`RetentionPolicy`枚举（保持性策略）：
  
       > - 仅编译期：`RetentionPolicy.SOURCE`，即只保存在源文件中。
       > - （默认）仅class文件：`RetentionPolicy.CLASS`，即保存生成到`.class`文件中。
       > - 运行期：`RetentionPolicy.RUNTIME`，即运行期有效，保存在class文件中并且可以被反射机制读取到。
       >
       > 因为通常我们自定义的`Annotation`都是`RUNTIME`，所以，务必要加上`@Retention(RetentionPolicy.RUNTIME)`这个元注解：
       >
       > ```java
       > @Retention(RetentionPolicy.RUNTIME)
       > public @interface Report {
       >     int type() default 0;
       >     String level() default "info";
       >     String value() default "";
       > }
       > ```
  
    3. `@Repeatable`：定义`Annotation`是否可重复。这个注解应用不是特别广泛。
  
       > ```java
       > @Repeatable(Reports.class)
       > @Target(ElementType.TYPE)
       > public @interface Report {
       >     int type() default 0;
       >     String level() default "info";
       >     String value() default "";
       > }
       > 
       > @Target(ElementType.TYPE)
       > public @interface Reports {
       >     Report[] value();
       > }
       > ```
       >
       > 经过`@Repeatable`修饰后，在某个类型声明处，就可以添加多个`@Report`注解：
       >
       > ```java
       > @Report(type=1, level="debug")
       > @Report(type=2, level="warning")
       > public class Hello {}
       > ```
  
    4. `@Inherited`：定义子类是否可继承父类定义的`Annotation`。`@Inherited`仅针对`@Target(ElementType.TYPE)`类型的`annotation`有效，并且仅针对`class`的继承，对`interface`的继承无效。
  
       > ```java
       > @Inherited
       > @Target(ElementType.TYPE)
       > public @interface Report {
       >     int type() default 0;
       >     String level() default "info";
       >     String value() default "";
       > }
       > ```
       >
       > 在使用的时候，如果一个类用到了`@Report`：
       >
       > ```java
       > @Report(type=1)
       > public class Person {}
       > ```
       >
       > 则它的子类默认也定义了该注解：
       >
       > ```java
       > public class Student extends Person {}
       > ```
  
  - #### 处理注解：
  
    > Java的注解本身对代码逻辑没有任何影响。根据`@Retention`的配置：
    >
    > - `SOURCE`类型的注解在编译期就被丢掉了；
    > - `CLASS`类型的注解仅保存在class文件中，它们不会被加载进JVM；
    > - `RUNTIME`类型的注解会被加载进JVM，并且在运行期可以被程序读取。
    >
    > 如何使用注解完全由工具决定。`SOURCE`类型的注解主要由编译器使用，因此我们一般只使用，不编写。`CLASS`类型的注解主要由底层工具库使用，涉及到class的加载，一般我们很少用到。只有`RUNTIME`类型的注解不但要使用，还经常需要编写。
    >
    > 因此，我们只讨论如何读取`RUNTIME`类型的注解。
    >
    > 因为**注解定义后也是一种`class`**，**所有的注解都继承自`java.lang.annotation.Annotation`**，因此，读取注解，需要使用反射API。
    >
    > Java提供的使用反射API读取`Annotation`的方法包括：
    >
    > 判断某个注解是否存在于`Class`、`Field`、`Method`或`Constructor`：
    >
    > - `Class.isAnnotationPresent(Class)`
    > - `Field.isAnnotationPresent(Class)`
    > - `Method.isAnnotationPresent(Class)`
    > - `Constructor.isAnnotationPresent(Class)`
    >
    > 例如：
    >
    > ```java
    > // 判断@Report是否存在于Person类:
    > Person.class.isAnnotationPresent(Report.class);
    > ```
    >
    > 使用反射API读取Annotation：
    >
    > - Class.getAnnotation(Class)
    >- Field.getAnnotation(Class)
    > - Method.getAnnotation(Class)
    > - Constructor.getAnnotation(Class)
    > 
    > 例如：
    >
    > ```java
    >// 获取Person定义的@Report注解:
    > Report report = Person.class.getAnnotation(Report.class);
    > int type = report.type();
    > String level = report.level();
    > ```
    > 
    > 使用反射API读取`Annotation`有两种方法。方法一是先判断`Annotation`是否存在，如果存在，就直接读取：
    >
    > ```java
    >Class cls = Person.class;
    > if (cls.isAnnotationPresent(Report.class)) {
    >    Report report = cls.getAnnotation(Report.class);
    >     ...
    > }
    > ```
    > 
    > 第二种方法是直接读取`Annotation`，如果`Annotation`不存在，将返回`null`：
    > 
    > ```java
    >Class cls = Person.class;
    > Report report = cls.getAnnotation(Report.class);
    >if (report != null) {...}
    > ```
    >
    > 读取方法、字段和构造方法的`Annotation`和Class类似。但要读取方法参数的`Annotation`就比较麻烦一点，因为方法参数本身可以看成一个数组，而每个参数又可以定义多个注解，所以，一次获取方法参数的所有注解就必须用一个二维数组来表示。例如，对于以下方法定义的注解：
    > 
    > ```java
    > public void hello(@NotNull @Range(max=5) String name, @NotNull String prefix) {}
    > ```
    > 
    > 要读取方法参数的注解，我们先用反射获取`Method`实例，然后读取方法参数的所有注解：
    >
    > ```java
    >// 获取Method实例:
    > Method m = ...
    >// 获取所有参数的Annotation:
    > Annotation[][] annos = m.getParameterAnnotations();
    > // 第一个参数（索引为0）的所有Annotation:
    > Annotation[] annosOfName = annos[0];
    > for (Annotation anno : annosOfName) {
    >    if (anno instanceof Range r) { // @Range注解
    >         r.max();
    >    }
    >     if (anno instanceof NotNull n) { // @NotNull注解
    >        //
    >     }
    > }
    > ```
    > 
  
- ## 泛型

- ## 集合

- ## IO

- ## 日期与时间

- ## 单元测试