# Python

> - Python是**吉多·范罗苏姆（Guido van Rossum）**在1989年圣诞节期间，为了打发无聊的圣诞节而创建的解释型语言（类似JS）。它虽没有C/C++那种编译型语言的执行速度，但由于它的**优雅、明确、简单**，学习成本极低且能做的事情多，因此在开发者及非开发者间被广泛使用。如今，Python广泛使用在**web开发、自动化测试、AI、大数据..**等多个领域。
>
> - Python为我们提供了非常完善的基础代码库，覆盖了网络、文件、GUI、数据库、文本等大量内容。除此之外Python还有大量的第三方库及文档资料供开发者使用。
>
> - Python源代码以`.py`结尾，运行它需要下载Python的解释器。当我们从官网下载了对应平台的解释器安装后，你会得到Python解释器（它是负责运行Python程序的）、一个命令行交互环境、一个简单的集成开发环境（IDE）、以及一些Python文档。
>
> - Windows系统中通过在命令行执行`py/python`即可进入Python提供的交互环境中执行Python代码了。或者用`py/python 文件名`来解释执行Python源程序。（Mac/Linux系统是`python3`命令）
>
> - 目前，Python有两个版本，一个是2.x版，一个是3.x版，这两个版本是不兼容的。由于3.x版越来越普及，我们的教程将以最新的Python 3.x版本为基础。请确保你的电脑上安装的Python版本是最新的3.x，这样，你才能无痛学习这个教程。
>
> - 由于整个Python语言从规范到解释器都是开源的，所以理论上，只要水平够高，任何人都可以编写Python解释器来执行Python代码（当然难度很大）。事实上，确实存在多种Python解释器：
>
>   - **CPython**（官方）：当我们从[Python官方网站](https://www.python.org/)下载并安装好Python 3.x后，我们就直接获得了一个官方版本的解释器：CPython。这个解释器是用C语言开发的，所以叫CPython。在命令行下运行`python`就是启动官方的CPython解释器。CPython是使用最广的Python解释器，教程的所有代码也都在CPython下执行。
>
>   - **IPython**：IPython是基于CPython的一个交互式解释器，也就是说，IPython只是在交互方式上有所增强，但是执行Python代码的功能和CPython是完全一样的。CPython用`>>>`作为提示符，而IPython用`In [序号]:`作为提示符。
>
>   - **PyPy**：PyPy是另一个Python解释器，它的目标是执行速度。PyPy采用[JIT技术](http://en.wikipedia.org/wiki/Just-in-time_compilation)，对Python代码进行动态编译（注意不是解释），所以可以显著提高Python代码的执行速度。虽然绝大部分Python代码都可以在PyPy下运行，但是PyPy和CPython是有一些不同的，这就导致相同的Python代码在两种解释器下执行可能会有不同的结果。如果你的代码要放到PyPy下执行，就需要了解[PyPy和CPython的不同点](http://pypy.readthedocs.org/en/latest/cpython_differences.html)。
>
>   - **Jython**：Jython是运行在Java平台上的Python解释器，可以直接把Python代码编译成Java字节码执行。
>
>   - **IronPython**：IronPython和Jython类似，只不过IronPython是运行在微软.Net平台上的Python解释器，可以直接把Python代码编译成.Net的字节码。
>
>     > 虽然Python的解释器很多，但使用最广泛的还是CPython。如果要和Java或.Net平台交互，最好的办法不是用Jython或IronPython，而是通过网络调用来交互，确保各程序之间的独立性。
>
> - **Python交互模式**：在命令行模式下敲命令`python`，就看到类似如下的一堆文本输出，然后就进入到Python交互模式，它的提示符是`>>>`。在Python交互模式下输入`exit()`并回车，就退出了Python交互模式，并回到命令行模式。这个模式类似JS在F12控制台中执行JS代码，它是用于调试Python代码的。

- ### 第1个Python程序

  ```python
  #!/usr/bin/env python3
  # -*- coding: utf-8 -*-
  name = input('please enter your name: ') # input()可以提示并等待用户输入，返回值是输入的字符串
  print('Hello', name)  # print()函数可以接受多个字符串，默认用空格“ ”隔开，并且结束符为\n
  '''
  please enter your name: 张三
  Hello 张三'''
  ```

  > - 第1行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；
  > - 第2行注释是为了告诉Python解释器，按照UTF-8编码读取Python源码。

  ###### 注意：Python3/Go/Node使用UTF8编码，因此文件名可以是中文。而Python2使用Ascii编码，因此文件名必须是字母、数字、下划线的组合。

- ### Python基础语法

  - 以`#`开头的语句是Python中的单行注释。Python中没有多行注释。

  - Python程序是**大小写敏感**的。

  - 和Java不同的是，Python是**动态语言**，也就是定义变量时不用指定类型，类型随意。如：`name=1`，name指向了字面量1的引用地址。

  - Python中，标识符必须是字母、数字、下划线的组合，且不能以数字开头。（Python3虽然能用中文字符，但不推荐）

  - Python中的逻辑运算符不是`&、|、!`，而是用`and、or、not`。

  - Python中只有`elif`没有`else if`。

  - Python中只有2种循环：`for in`和`while`。其中for in只能用于可迭代对象（list、tuple、字符串、dict、set..）。

  - Python中，`/`做除法时，结果总是浮点数。而`//`才是正常的取整除法。

  - Python一般是一行一条语句，语句后的分号`;`可加可不加。但如果一行中有多条语句，则需要用分号`;`隔开。

  - Python中，当某个语句以冒号`:`结尾时，其后缩进的语句被视为代码块。（按照约定俗成的惯例，应始终使用*4个空格*的缩进）

    > 缩进的坏处就是“复制－粘贴”功能失效了，这是最坑爹的地方。当你重构Python代码时，粘贴过去的代码必须重新检查缩进是否正确，因为这会影响执行。此外，IDE很难像格式Java代码那样格式化Python代码。

  - Python中可以用`%`来格式化字符串：（`%s`是格式化符号，类似C语言）

    ```python
    print('Hello, %s' % 'world')  # %s表示用字符串替换，%d表示用整数替换，有几个%?占位符，后面就按序跟几个值。如果只有一个%?，括号可以省略
    print('Hi, %s, you have $%d.' % ('Michael', 1000000))
    ```

  - Python中没有switch语句，只有match语句。match语句中依次用`case xxx`匹配，并且可以在最后（且仅能在最后）加一个`case _`表示“任意值”。match没有case穿透：

    ```python
    match 值: # 值还可以是list列表
        case x if x == 'A': # 表示将值赋给x，若x<10成立时匹配
            print('score is A.')
        case 'B':
            print('score is B.')
        case 'C'|'D'|'E':
            print('score is C.')
        case _: # _表示匹配到其他任何情况
            print('score is ???.')
    ```

  - pass语句：`pass`语句就是一个普通的Python语句，但它什么也不做。因此`pass`语句通常被作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个`pass`，让代码能跑起来。

- ### Python的数据类型（字面量）

  - 整数：和Java是类似的。不同的是，Python允许在数值中间以`_`分隔，如：`10_000`和`10000`、`0xa1_c4`和`0xa1c4`是完全一样的。

  - 浮点数：和Java类似。

    > Python中的整数和浮点数都没有长度限制，但是超出一定范围就直接表示为`inf`无限大。

  - 字符串：字符串是以单引号`'`或双引号`"`括起来的任意文本。

    > - 多行字符串：Python允许用`'''...'''`的格式来表示包含多行内容的字符串（Java15之后也有这个特性）。通常会用它去定义一个未被使用的字符串，来当作多行注释使用。
    >
    > - Python中，字符串前加r字符串前缀符（`r''`）表示：`''`内部的字符串不转义。
    >
    > - Python中，字符串可以乘上一个数n，表示将该字符串复制n份并拼接起来。
    >
    > - Python中，字符串可以使用`[]`索引的方式来获取某个字符（类似JS）。
    >
    > - 对于单个字符的编码，Python提供了`ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符：
    >
    >   ```python
    >   ord('中')  # 20013
    >   chr(25991)  # '文'
    >   ```
    >
    > - 另一种格式化字符串的方法是使用字符串的`format()`方法，它会用传入的参数依次替换字符串内的占位符`{0}`、`{1}`……，不过这种方式写起来比`%`符要麻烦得多：
    >
    >   ```python
    >   print('Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125))  # Hello, 小明, 成绩提升了17.1%
    >   ```
    >
    > - 字符串拼接（f-string）：（f也是一个字符串前缀符）
    >
    >   ```python
    >   r = 2.5
    >   s = 3.14 * r ** 2
    >   print(f'The area of a circle with radius {r} is {s:.2f}')  # The area of a circle with radius 2.5 is 19.62
    >   # 上述代码中，{r}被变量r的值替换，{s:.2f}被变量s的值替换
    >   ```

  - 布尔值：`True/False`

  - 空值：`None`

  - 字节序列：Python对`bytes`类型（字节序列）的数据用带`b`前缀的字符串表示，如：`x = b'ABC'`，其中每个字符占1字节。

    > 字符串和字节序列分别有`'abc'.encode('utf-8')`和`b'abc'.decode('ascii')`方法可以编码和解码。

  - 列表（list）：Python中没有数组，只有list列表。列表就像JS中的数组，是可变、有序的存储序列，里面可以存放不同类型的数据：`classmates = ['Michael', 123, True]`。通过`len()`函数可以获取列表的长度。还可以通过负号`classmates[-n]`来获取列表中倒数第n个元素。

  - 元组（tuple）：元组和列表的区别是，**元组一旦初始化元素就不可变了**（浅层次）。因此在定义一个tuple元组时，元组内的每个元素都必须确定下来。定义元组：`myTuple = (1, True, 'hello')`。

    > **注意**：只有1个元素的tuple定义时后面必须加一个逗号`,`来消除歧义：`t = (1,)`。定义空的元组：`t = ()`。

  - 字典（dict）：Python内置了字典dict的支持，全称dictionary，对应Java中的map，使用键-值（K-V）存储数据，具有极快的查找速度。定义dick：

    ```python
    d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
    d['Michael']  # 95
    d['Adam'] = 67  # 字典中添加元素
    ```

    > - 字典的key必须是**不可变值**。在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list就不能作为key。
    > - 可以用`in`操作符判断字典中是否包含某个K，语法：`K in 字典`，返回布尔值。
    > - 或者用字典身上的get方法，若key不存在，返回`None`或自己指定的值：`d.get('Thomas', '不存在')`
    > - 从字典中删除某个元素：`d.pop(K)`。

  - set：set类似Java中的set集合，无序不可重复、无下标，只存储Key。如：`s = {'a', Ture, 123}`。

    > - 往set中添加元素：`s.add(元素)`，删除元素：`s.remove(元素)`
    > - 2个set之间可以用`&、|`符做集合的交并集运算。

- ### 函数

  - ##### 定义函数：在Python中，定义一个函数要使用`def`关键字，如：

    ```python
    def my_abs(x):
        if x >= 0: # 注意：缩进前一定要有冒号:
            return x
        else:
            return -x
    print(my_abs(-99))
    ```

    > - Python中，如果一个函数没有`return`，函数执行完毕后也会返回一个`None`，默认有一个return。因此`return None`可以简写为`return`或干脆不写。
    > - Python的函数看似可以返回多个值：`return x,y`，但实际上，Python会自动将这多个值包装为tuple元组。
    > - Python中的函数名其实就是指向一个函数对象的引用，因此完全可以把函数名赋给一个变量（Python是非强制类型语言），相当于给这个函数起了一个*别名*：`a = abs; a(-1) #输出1`

  - ##### 函数的参数：

    > Python的函数定义非常简单，但灵活度却非常大。除了正常定义的必选参数（形参）外，还可以使用**默认参数、可变参数、关键字参数、命名关键字参数**，这使函数定义出来的接口不但能处理复杂的参数，还可以简化调用者的代码。

    - **默认参数**：类似JS。注意：Python中定义默认参数时，形参必须指向一个不可变的值！（因为**默认参数存在副作用**）

    - **可变参数**：可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。Python中的可变长参数是在函数形参前加`*`号，此时形参是个元组tuple。并且传参时可以用`*`号把参数（list/tuple）中的所有元素作为可变参数传进去：`a(*students)`相当于`a(students[0],students[0],..)`。

    - **关键字参数**：类似可变参数，关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。如：

      ```python
      def person(name, age, **kw):
          print('name:', name, 'age:', age, 'other:', kw)
      person('Michael', 30)  # name: Michael age: 30 other: {}
      person('Adam', 45, gender='M', job='Engineer')  # name: Adam age: 45 other: {'gender': 'M', 'job': 'Engineer'}
      ```

      > 类似可变参数，关键字参数也可以直接传一个dict字典：
      >
      > ```python
      > extra = {'city': 'Beijing', 'job': 'Engineer'}
      > person('Jack', 24, **extra)
      > ```
      >
      > `**extra`表示把`extra`这个dict的所有K-V用关键字参数传入到函数的`**kw`参数中（值传递）。

    - **命名关键字参数**：如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收`city`和`job`作为关键字参数。这种方式定义的函数如下：

      ```python
      def person(name, age, *, city, job):
          print(name, age, city, job)
      ```

      > - 传命名关键字参数时必须指定实参名：`person('Jack', 24, job='Engineer')`。
      >
      > - 和关键字参数不同，命名关键字参数需要一个特殊分隔符`*`，`*`后面的参数被视为命名关键字参数。
      >
      > - 如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了：
      >
      >   ```python
      >   def person(name, age, *args, city, job):
      >       print(name, age, args, city, job)
      >   ```
      >
      > - 命名关键字参数可以继续指定默认值：
      >
      >   ```python
      >   def person(name, age, *, city='Beijing', job):
      >       print(name, age, city, job)
      >   ```

    ###### 以上几种形式的函数参数可以任意组合使用。但是注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数、关键字参数。

  - ##### Python内置了很多有用的函数，我们可以直接使用：type()/isxxx()

    - ###### 内置的类型转换函数有：

      ```python
      int(12.34)  # 12
      float('12.34')  # 12.34
      str(100)  # '100'
      bool(0)  # False
      set([1,1,2,3])  # {1,2,3}
      ```

- ### 高级用法：

  - 切片操作符（`:`）：它可以取list、tuple、字符串的一部分，`students[0:3]`表示取容器中下标在[0,3)范围内的元素。

    > - `:`前的索引默认是0：`arr[:3]`，`:`后的索引默认是最大的索引：`arr[-1:]`。复制：`arr[:]`
    > - 前10个元素，每两个取一个：`arr[:10:2]`。

  - 列表生成式：

  - 生成器：

  - 迭代器：

- 
