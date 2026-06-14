## 数组

> 数组（array）是一个包含多个值的变量。成员的编号从0开始，数量没有上限，也没有要求成员被连续索引。

- #### 创建数组

  > 数组可以采用逐个赋值的方法创建。
  >
  > ```
  > ARRAY[INDEX]=value
  > ```
  >
  > 上面语法中，`ARRAY`是数组的名字，可以是任意合法的变量名。`INDEX`是一个大于或等于零的整数，也可以是算术表达式。注意数组第一个元素的下标是0， 而不是1。
  >
  > 下面创建一个三个成员的数组。
  >
  > ```
  > $ array[0]=val
  > $ array[1]=val
  > $ array[2]=val
  > ```
  >
  > 数组也可以采用一次性赋值的方式创建。
  >
  > ```
  > ARRAY=(value1 value2 ... valueN)
  > 
  > # 等同于
  > 
  > ARRAY=(
  >   value1
  >   value2
  >   value3
  > )
  > ```
  >
  > 采用上面方式创建数组时，可以按照默认顺序赋值，也可以在每个值前面指定位置。
  >
  > ```
  > $ array=(a b c)
  > $ array=([2]=c [0]=a [1]=b)
  > 
  > $ days=(Sun Mon Tue Wed Thu Fri Sat)
  > $ days=([0]=Sun [1]=Mon [2]=Tue [3]=Wed [4]=Thu [5]=Fri [6]=Sat)
  > ```
  >
  > 只为某些值指定位置，也是可以的。
  >
  > ```
  > names=(hatter [5]=duchess alice)
  > ```
  >
  > 上面例子中，`hatter`是数组的0号位置，`duchess`是5号位置，`alice`是6号位置。
  >
  > 没有赋值的数组元素的默认值是空字符串。
  >
  > 定义数组的时候，可以使用通配符。
  >
  > ```
  > $ mp3s=( *.mp3 )
  > ```
  >
  > 上面例子中，将当前目录的所有 MP3 文件，放进一个数组。
  >
  > 先用`declare -a`命令声明一个数组，也是可以的。
  >
  > ```
  > $ declare -a ARRAYNAME
  > ```
  >
  > `read -a`命令则是将用户的命令行输入，存入一个数组。
  >
  > ```
  > $ read -a dice
  > ```
  >
  > 上面命令将用户的命令行输入，存入数组`dice`。

- #### 读取数组

  - ##### 读取单个元素

    > 读取数组指定位置的成员，要使用下面的语法。
    >
    > ```
    > $ echo ${array[i]}     # i 是索引
    > ```
    >
    > 上面语法里面的大括号是必不可少的，否则 Bash 会把索引部分`[i]`按照原样输出。
    >
    > ```
    > $ array[0]=a
    > 
    > $ echo ${array[0]}
    > a
    > 
    > $ echo $array[0]
    > a[0]
    > ```
    >
    > 上面例子中，数组的第一个元素是`a`。如果不加大括号，Bash 会直接读取`$array`首成员的值，然后将`[0]`按照原样输出。

  - ##### 读取所有成员

    > `@`和`*`是数组的特殊索引，表示返回数组的所有成员。
    >
    > ```
    > $ foo=(a b c d e f)
    > $ echo ${foo[@]}
    > a b c d e f
    > ```
    >
    > 这两个特殊索引配合`for`循环，就可以用来遍历数组。
    >
    > ```
    > for i in "${names[@]}"; do
    >   echo $i
    > done
    > ```
    >
    > `@`和`*`放不放在双引号之中，是有差别的。
    >
    > ```
    > $ activities=( swimming "water skiing" canoeing "white-water rafting" surfing )
    > $ for act in ${activities[@]}; \
    > do \
    > echo "Activity: $act"; \
    > done
    > 
    > Activity: swimming
    > Activity: water
    > Activity: skiing
    > Activity: canoeing
    > Activity: white-water
    > Activity: rafting
    > Activity: surfing
    > ```
    >
    > 上面的例子中，数组`activities`实际包含5个成员，但是`for...in`循环直接遍历`${activities[@]}`，导致返回7个结果。为了避免这种情况，一般把`${activities[@]}`放在双引号之中。
    >
    > ```
    > $ for act in "${activities[@]}"; \
    > do \
    > echo "Activity: $act"; \
    > done
    > 
    > Activity: swimming
    > Activity: water skiing
    > Activity: canoeing
    > Activity: white-water rafting
    > Activity: surfing
    > ```
    >
    > 上面例子中，`${activities[@]}`放在双引号之中，遍历就会返回正确的结果。
    >
    > `${activities[*]}`不放在双引号之中，跟`${activities[@]}`不放在双引号之中是一样的。
    >
    > ```
    > $ for act in ${activities[*]}; \
    > do \
    > echo "Activity: $act"; \
    > done
    > 
    > Activity: swimming
    > Activity: water
    > Activity: skiing
    > Activity: canoeing
    > Activity: white-water
    > Activity: rafting
    > Activity: surfing
    > ```
    >
    > `${activities[*]}`放在双引号之中，所有成员就会变成单个字符串返回。
    >
    > ```
    > $ for act in "${activities[*]}"; \
    > do \
    > echo "Activity: $act"; \
    > done
    > 
    > Activity: swimming water skiing canoeing white-water rafting surfing
    > ```
    >
    > 所以，拷贝一个数组的最方便方法，就是写成下面这样。
    >
    > ```
    > $ hobbies=( "${activities[@]}" )
    > ```
    >
    > 上面例子中，数组`activities`被拷贝给了另一个数组`hobbies`。
    >
    > 这种写法也可以用来为新数组添加成员。
    >
    > ```
    > $ hobbies=( "${activities[@]}" diving )
    > ```
    >
    > 上面例子中，新数组`hobbies`在数组`activities`的所有成员之后，又添加了一个成员。

  - ##### 默认位置

    > 如果读取数组成员时，没有读取指定哪一个位置的成员，默认使用`0`号位置。
    >
    > ```
    > $ declare -a foo
    > $ foo=A
    > $ echo ${foo[0]}
    > A
    > ```
    >
    > 上面例子中，`foo`是一个数组，赋值的时候不指定位置，实际上是给`foo[0]`赋值。
    >
    > 引用一个不带下标的数组变量，则引用的是`0`号位置的数组元素。
    >
    > ```
    > $ foo=(a b c d e f)
    > $ echo ${foo}
    > a
    > $ echo $foo
    > a
    > ```
    >
    > 上面例子中，引用数组元素的时候，没有指定位置，结果返回的是`0`号位置。

- #### 数组的长度

  > 要想知道数组的长度（即一共包含多少成员），可以使用下面两种语法。
  >
  > ```
  > ${#array[*]}
  > ${#array[@]}
  > ```
  >
  > 下面是一个例子。
  >
  > ```
  > $ a[100]=foo
  > 
  > $ echo ${#a[*]}
  > 1
  > 
  > $ echo ${#a[@]}
  > 1
  > ```
  >
  > 上面例子中，把字符串赋值给`100`位置的数组元素，这时的数组只有一个元素。
  >
  > 注意，如果用这种语法去读取具体的数组成员，就会返回该成员的字符串长度。这一点必须小心。
  >
  > ```
  > $ a[100]=foo
  > $ echo ${#a[100]}
  > 3
  > ```
  >
  > 上面例子中，`${#a[100]}`实际上是返回数组第100号成员`a[100]`的值（`foo`）的字符串长度。

- #### 提取数组序号

  > `${!array[@]}`或`${!array[*]}`，可以返回数组的成员序号，即哪些位置是有值的。
  >
  > ```
  > $ arr=([5]=a [9]=b [23]=c)
  > $ echo ${!arr[@]}
  > 5 9 23
  > $ echo ${!arr[*]}
  > 5 9 23
  > ```
  >
  > 上面例子中，数组的5、9、23号位置有值。
  >
  > 利用这个语法，也可以通过`for`循环遍历数组。
  >
  > ```bash
  > arr=(a b c d)
  > 
  > for i in ${!arr[@]};do
  >   echo ${arr[i]}
  > done
  > ```

- #### 提取数组成员

  > `${array[@]:position:length}`的语法可以提取数组成员。
  >
  > ```
  > $ food=( apples bananas cucumbers dates eggs fajitas grapes )
  > $ echo ${food[@]:1:1}
  > bananas
  > $ echo ${food[@]:1:3}
  > bananas cucumbers dates
  > ```
  >
  > 上面例子中，`${food[@]:1:1}`返回从数组1号位置开始的1个成员，`${food[@]:1:3}`返回从1号位置开始的3个成员。
  >
  > 如果省略长度参数`length`，则返回从指定位置开始的所有成员。
  >
  > ```
  > $ echo ${food[@]:4}
  > eggs fajitas grapes
  > ```
  >
  > 上面例子返回从4号位置开始到结束的所有成员。

- #### 追加数组成员

  > 数组末尾追加成员，可以使用`+=`赋值运算符。它能够自动地把值追加到数组末尾。否则，就需要知道数组的最大序号，比较麻烦。
  >
  > ```bash
  > $ foo=(a b c)
  > $ echo ${foo[@]}
  > a b c
  > 
  > $ foo+=(d e f)
  > $ echo ${foo[@]}
  > a b c d e f
  > ```

- #### 删除数组

  > 删除一个数组成员，使用`unset`命令。
  >
  > ```
  > $ foo=(a b c d e f)
  > $ echo ${foo[@]}
  > a b c d e f
  > 
  > $ unset foo[2]
  > $ echo ${foo[@]}
  > a b d e f
  > ```
  >
  > 上面例子中，删除了数组中的第三个元素，下标为2。
  >
  > 将某个成员设为空值，可以从返回值中“隐藏”这个成员。
  >
  > ```
  > $ foo=(a b c d e f)
  > $ foo[1]=''
  > $ echo ${foo[@]}
  > a c d e f
  > ```
  >
  > 上面例子中，将数组的第二个成员设为空字符串，数组的返回值中，这个成员就“隐藏”了。
  >
  > 注意，这里是“隐藏”，而不是删除，因为这个成员仍然存在，只是值变成了空值。
  >
  > ```
  > $ foo=(a b c d e f)
  > $ foo[1]=''
  > $ echo ${#foo[@]}
  > 6
  > $ echo ${!foo[@]}
  > 0 1 2 3 4 5
  > ```
  >
  > 上面代码中，第二个成员设为空值后，数组仍然包含6个成员。
  >
  > 由于空值就是空字符串，所以下面这样写也有隐藏效果，但是不建议这种写法。
  >
  > ```
  > $ foo[1]=
  > ```
  >
  > 上面的写法也相当于“隐藏”了数组的第二个成员。
  >
  > 直接将数组变量赋值为空字符串，相当于“隐藏”数组的第一个成员。
  >
  > ```
  > $ foo=(a b c d e f)
  > $ foo=''
  > $ echo ${foo[@]}
  > b c d e f
  > ```
  >
  > 上面的写法相当于“隐藏”了数组的第一个成员。
  >
  > `unset ArrayName`可以清空整个数组。
  >
  > ```bash
  > $ unset ARRAY
  > 
  > $ echo ${ARRAY[*]}
  > <--no output-->
  > ```

- #### 关联数组

  > Bash 的新版本支持关联数组。关联数组使用字符串而不是整数作为数组索引。
  >
  > `declare -A`可以声明关联数组。
  >
  > ```
  > declare -A colors
  > colors["red"]="#ff0000"
  > colors["green"]="#00ff00"
  > colors["blue"]="#0000ff"
  > ```
  >
  > 关联数组必须用带有`-A`选项的`declare`命令声明创建。相比之下，整数索引的数组，可以直接使用变量名创建数组，关联数组就不行。
  >
  > 访问关联数组成员的方式，几乎与整数索引数组相同。
  >
  > ```bash
  > echo ${colors["blue"]}
  > ```

## `set` 命令，`shopt` 命令

> set命令是 Bash 脚本的重要环节，却常常被忽视，导致脚本的安全性和可维护性出问题。本章介绍set的基本用法，帮助你写出更安全的 Bash 脚本。

- #### 简介

  > 我们知道，Bash 执行脚本时，会创建一个子 Shell。
  >
  > ```
  > $ bash script.sh
  > ```
  >
  > 上面代码中，`script.sh`是在一个子 Shell 里面执行。这个子 Shell 就是脚本的执行环境，Bash 默认给定了这个环境的各种参数。
  >
  > `set`命令用来修改子 Shell 环境的运行参数，即定制环境。一共有十几个参数可以定制，[官方手册](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)有完整清单，本章介绍其中最常用的几个。
  >
  > 顺便提一下，如果命令行下不带任何参数，直接运行`set`，会显示所有的环境变量和 Shell 函数。
  >
  > ```bash
  > $ set
  > ```

- #### `set -u`

  > 执行脚本时，如果遇到不存在的变量，Bash 默认忽略它。
  >
  > ```
  > #!/usr/bin/env bash
  > 
  > echo $a
  > echo bar
  > ```
  >
  > 上面代码中，`$a`是一个不存在的变量。执行结果如下。
  >
  > ```
  > $ bash script.sh
  > 
  > bar
  > ```
  >
  > 可以看到，`echo $a`输出了一个空行，Bash 忽略了不存在的`$a`，然后继续执行`echo bar`。大多数情况下，这不是开发者想要的行为，遇到变量不存在，脚本应该报错，而不是一声不响地往下执行。
  >
  > `set -u`就用来改变这种行为。脚本在头部加上它，遇到不存在的变量就会报错，并停止执行。
  >
  > ```
  > #!/usr/bin/env bash
  > set -u
  > 
  > echo $a
  > echo bar
  > ```
  >
  > 运行结果如下。
  >
  > ```
  > $ bash script.sh
  > bash: script.sh:行4: a: 未绑定的变量
  > ```
  >
  > 可以看到，脚本报错了，并且不再执行后面的语句。
  >
  > `-u`还有另一种写法`-o nounset`，两者是等价的。
  >
  > ```bash
  > set -o nounset
  > ```

- #### `set -x`

  > 默认情况下，脚本执行后，只输出运行结果，没有其他内容。如果多个命令连续执行，它们的运行结果就会连续输出。有时会分不清，某一段内容是什么命令产生的。
  >
  > `set -x`用来在运行结果之前，先输出执行的那一行命令。
  >
  > ```
  > #!/usr/bin/env bash
  > set -x
  > 
  > echo bar
  > ```
  >
  > 执行上面的脚本，结果如下。
  >
  > ```
  > $ bash script.sh
  > + echo bar
  > bar
  > ```
  >
  > 可以看到，执行`echo bar`之前，该命令会先打印出来，行首以`+`表示。这对于调试复杂的脚本是很有用的。
  >
  > `-x`还有另一种写法`-o xtrace`。
  >
  > ```
  > set -o xtrace
  > ```
  >
  > 脚本当中如果要关闭命令输出，可以使用`set +x`。
  >
  > ```
  > #!/bin/bash
  > 
  > number=1
  > 
  > set -x
  > if [ $number = "1" ]; then
  >   echo "Number equals 1"
  > else
  >   echo "Number does not equal 1"
  > fi
  > set +x
  > ```
  >
  > 上面的例子中，只对特定的代码段打开命令输出。

- #### Bash 的错误处理

  > 如果脚本里面有运行失败的命令（返回值非`0`），Bash 默认会继续执行后面的命令。
  >
  > ```
  > #!/usr/bin/env bash
  > 
  > foo
  > echo bar
  > ```
  >
  > 上面脚本中，`foo`是一个不存在的命令，执行时会报错。但是，Bash 会忽略这个错误，继续往下执行。
  >
  > ```
  > $ bash script.sh
  > script.sh:行3: foo: 未找到命令
  > bar
  > ```
  >
  > 可以看到，Bash 只是显示有错误，并没有终止执行。
  >
  > 这种行为很不利于脚本安全和除错。实际开发中，如果某个命令失败，往往需要脚本停止执行，防止错误累积。这时，一般采用下面的写法。
  >
  > ```
  > command || exit 1
  > ```
  >
  > 上面的写法表示只要`command`有非零返回值，脚本就会停止执行。
  >
  > 如果停止执行之前需要完成多个操作，就要采用下面三种写法。
  >
  > ```
  > # 写法一
  > command || { echo "command failed"; exit 1; }
  > 
  > # 写法二
  > if ! command; then echo "command failed"; exit 1; fi
  > 
  > # 写法三
  > command
  > if [ "$?" -ne 0 ]; then echo "command failed"; exit 1; fi
  > ```
  >
  > 另外，除了停止执行，还有一种情况。如果两个命令有继承关系，只有第一个命令成功了，才能继续执行第二个命令，那么就要采用下面的写法。
  >
  > ```bash
  > command1 && command2
  > ```

- #### `set -e`

  > 上面这些写法多少有些麻烦，容易疏忽。`set -e`从根本上解决了这个问题，它使得脚本只要发生错误，就终止执行。
  >
  > ```
  > #!/usr/bin/env bash
  > set -e
  > 
  > foo
  > echo bar
  > ```
  >
  > 执行结果如下。
  >
  > ```
  > $ bash script.sh
  > script.sh:行4: foo: 未找到命令
  > ```
  >
  > 可以看到，第4行执行失败以后，脚本就终止执行了。
  >
  > `set -e`根据返回值来判断，一个命令是否运行失败。但是，某些命令的非零返回值可能不表示失败，或者开发者希望在命令失败的情况下，脚本继续执行下去。这时可以暂时关闭`set -e`，该命令执行结束后，再重新打开`set -e`。
  >
  > ```
  > set +e
  > command1
  > command2
  > set -e
  > ```
  >
  > 上面代码中，`set +e`表示关闭`-e`选项，`set -e`表示重新打开`-e`选项。
  >
  > 还有一种方法是使用`command || true`，使得该命令即使执行失败，脚本也不会终止执行。
  >
  > ```
  > #!/bin/bash
  > set -e
  > 
  > foo || true
  > echo bar
  > ```
  >
  > 上面代码中，`true`使得这一行语句总是会执行成功，后面的`echo bar`会执行。
  >
  > `-e`还有另一种写法`-o errexit`。
  >
  > ```bash
  > set -o errexit
  > ```

- #### `set -o pipefail`

  > `set -e`有一个例外情况，就是不适用于管道命令。
  >
  > 所谓管道命令，就是多个子命令通过管道运算符（`|`）组合成为一个大的命令。Bash 会把最后一个子命令的返回值，作为整个命令的返回值。也就是说，只要最后一个子命令不失败，管道命令总是会执行成功，因此它后面命令依然会执行，`set -e`就失效了。
  >
  > 请看下面这个例子。
  >
  > ```
  > #!/usr/bin/env bash
  > set -e
  > 
  > foo | echo a
  > echo bar
  > ```
  >
  > 执行结果如下。
  >
  > ```
  > $ bash script.sh
  > a
  > script.sh:行4: foo: 未找到命令
  > bar
  > ```
  >
  > 上面代码中，`foo`是一个不存在的命令，但是`foo | echo a`这个管道命令会执行成功，导致后面的`echo bar`会继续执行。
  >
  > `set -o pipefail`用来解决这种情况，只要一个子命令失败，整个管道命令就失败，脚本就会终止执行。
  >
  > ```
  > #!/usr/bin/env bash
  > set -eo pipefail
  > 
  > foo | echo a
  > echo bar
  > ```
  >
  > 运行后，结果如下。
  >
  > ```
  > $ bash script.sh
  > a
  > script.sh:行4: foo: 未找到命令
  > ```
  >
  > 可以看到，`echo bar`没有执行。

- #### `set -E`

  > 一旦设置了`-e`参数，会导致函数内的错误不会被`trap`命令捕获（参考《trap 命令》一章）。`-E`参数可以纠正这个行为，使得函数也能继承`trap`命令。
  >
  > ```
  > #!/bin/bash
  > set -e
  > 
  > trap "echo ERR trap fired!" ERR
  > 
  > myfunc()
  > {
  >   # 'foo' 是一个不存在的命令
  >   foo
  > }
  > 
  > myfunc
  > ```
  >
  > 上面示例中，`myfunc`函数内部调用了一个不存在的命令`foo`，导致执行这个函数会报错。
  >
  > ```
  > $ bash test.sh
  > test.sh:行9: foo：未找到命令
  > ```
  >
  > 但是，由于设置了`set -e`，函数内部的报错并没有被`trap`命令捕获，需要加上`-E`参数才可以。
  >
  > ```
  > #!/bin/bash
  > set -Eeuo pipefail
  > 
  > trap "echo ERR trap fired!" ERR
  > 
  > myfunc()
  > {
  >   # 'foo' 是一个不存在的命令
  >   foo
  > }
  > 
  > myfunc
  > ```
  >
  > 执行上面这个脚本，就可以看到`trap`命令生效了。
  >
  > ```bash
  > $ bash test.sh
  > test.sh:行9: foo：未找到命令
  > ERR trap fired!
  > ```

- #### 其他参数

  > `set`命令还有一些其他参数。
  >
  > - `set -n`：等同于`set -o noexec`，不运行命令，只检查语法是否正确。
  > - `set -f`：等同于`set -o noglob`，表示不对通配符进行文件名扩展。
  > - `set -v`：等同于`set -o verbose`，表示打印 Shell 接收到的每一行输入。
  > - `set -o noclobber`：防止使用重定向运算符`>`覆盖已经存在的文件。
  >
  > 上面的`-f`和`-v`参数，可以分别使用`set +f`、`set +v`关闭。

- #### `set` 命令总结

  > 上面重点介绍的`set`命令的几个参数，一般都放在一起使用。
  >
  > ```
  > # 写法一
  > set -Eeuxo pipefail
  > 
  > # 写法二
  > set -Eeux
  > set -o pipefail
  > ```
  >
  > 这两种写法建议放在所有 Bash 脚本的头部。
  >
  > 另一种办法是在执行 Bash 脚本的时候，从命令行传入这些参数。
  >
  > ```bash
  > $ bash -euxo pipefail script.sh
  > ```

- #### `shopt` 命令

  > `shopt`命令用来调整 Shell 的参数，跟`set`命令的作用很类似。之所以会有这两个类似命令的主要原因是，`set`是从 Ksh 继承的，属于 POSIX 规范的一部分，而`shopt`是 Bash 特有的。
  >
  > 直接输入`shopt`可以查看所有参数，以及它们各自打开和关闭的状态。
  >
  > ```
  > $ shopt
  > ```
  >
  > `shopt`命令后面跟着参数名，可以查询该参数是否打开。
  >
  > ```
  > $ shopt globstar
  > globstar  off
  > ```
  >
  > 上面例子表示`globstar`参数默认是关闭的。
  >
  > **（1）-s**
  >
  > `-s`用来打开某个参数。
  >
  > ```
  > $ shopt -s optionNameHere
  > ```
  >
  > **（2）-u**
  >
  > `-u`用来关闭某个参数。
  >
  > ```
  > $ shopt -u optionNameHere
  > ```
  >
  > 举例来说，`histappend`这个参数表示退出当前 Shell 时，将操作历史追加到历史文件中。这个参数默认是打开的，如果使用下面的命令将其关闭，那么当前 Shell 的操作历史将替换掉整个历史文件。
  >
  > ```
  > $ shopt -u histappend
  > ```
  >
  > **（3）-q**
  >
  > `-q`的作用也是查询某个参数是否打开，但不是直接输出查询结果，而是通过命令的执行状态（`$?`）表示查询结果。如果状态为`0`，表示该参数打开；如果为`1`，表示该参数关闭。
  >
  > ```
  > $ shopt -q globstar
  > $ echo $?
  > 1
  > ```
  >
  > 上面命令查询`globstar`参数是否打开。返回状态为`1`，表示该参数是关闭的。
  >
  > 这个用法主要用于脚本，供`if`条件结构使用。下面例子是如果打开了这个参数，就执行`if`结构内部的语句。
  >
  > ```bash
  > if (shopt -q globstar); then
  >   ...
  > fi
  > ```

## 脚本除错

- #### 常见错误

  > 编写 Shell 脚本的时候，一定要考虑到命令失败的情况，否则很容易出错。
  >
  > ```
  > #! /bin/bash
  > 
  > dir_name=/path/not/exist
  > 
  > cd $dir_name
  > rm *
  > ```
  >
  > 上面脚本中，如果目录`$dir_name`不存在，`cd $dir_name`命令就会执行失败。这时，就不会改变当前目录，脚本会继续执行下去，导致`rm *`命令删光当前目录的文件。
  >
  > 如果改成下面的样子，也会有问题。
  >
  > ```
  > cd $dir_name && rm *
  > ```
  >
  > 上面脚本中，只有`cd $dir_name`执行成功，才会执行`rm *`。但是，如果变量`$dir_name`为空，`cd`就会进入用户主目录，从而删光用户主目录的文件。
  >
  > 下面的写法才是正确的。
  >
  > ```
  > [[ -d $dir_name ]] && cd $dir_name && rm *
  > ```
  >
  > 上面代码中，先判断目录`$dir_name`是否存在，然后才执行其他操作。
  >
  > 如果不放心删除什么文件，可以先打印出来看一下。
  >
  > ```
  > [[ -d $dir_name ]] && cd $dir_name && echo rm *
  > ```
  >
  > 上面命令中，`echo rm *`不会删除文件，只会打印出来要删除的文件。

- #### bash的 `-x` 参数

  > `bash`的`-x`参数可以在执行每一行命令之前，打印该命令。一旦出错，这样就比较容易追查。
  >
  > 下面是一个脚本`script.sh`。
  >
  > ```
  > # script.sh
  > echo hello world
  > ```
  >
  > 加上`-x`参数，执行每条命令之前，都会显示该命令。
  >
  > ```
  > $ bash -x script.sh
  > + echo hello world
  > hello world
  > ```
  >
  > 上面例子中，行首为`+`的行，显示该行是所要执行的命令，下一行才是该命令的执行结果。
  >
  > 下面再看一个`-x`写在脚本内部的例子。
  >
  > ```
  > #! /bin/bash -x
  > # trouble: script to demonstrate common errors
  > 
  > number=1
  > if [ $number = 1 ]; then
  >   echo "Number is equal to 1."
  > else
  >   echo "Number is not equal to 1."
  > fi
  > ```
  >
  > 上面的脚本执行之后，会输出每一行命令。
  >
  > ```
  > $ trouble
  > + number=1
  > + '[' 1 = 1 ']'
  > + echo 'Number is equal to 1.'
  > Number is equal to 1.
  > ```
  >
  > 输出的命令之前的`+`号，是由系统变量`PS4`决定，可以修改这个变量。
  >
  > ```
  > $ export PS4='$LINENO + '
  > $ trouble
  > 5 + number=1
  > 7 + '[' 1 = 1 ']'
  > 8 + echo 'Number is equal to 1.'
  > Number is equal to 1.
  > ```
  >
  > 另外，`set`命令也可以设置 Shell 的行为参数，有利于脚本除错，详见《set 命令》一章。

- #### 环境变量

  > 有一些环境变量常用于除错。

  - ##### `LINENO`

    > 变量`LINENO`返回它在脚本里面的行号。
    >
    > ```
    > #!/bin/bash
    > 
    > echo "This is line $LINENO"
    > ```
    >
    > 执行上面的脚本`test.sh`，`$LINENO`会返回`3`。
    >
    > ```bash
    > $ ./test.sh
    > This is line 3
    > ```

  - ##### `FUNCNAME`

    > 变量`FUNCNAME`返回一个数组，内容是当前的函数调用堆栈。该数组的0号成员是当前调用的函数，1号成员是调用当前函数的函数，以此类推。
    >
    > ```
    > #!/bin/bash
    > 
    > function func1()
    > {
    >   echo "func1: FUNCNAME0 is ${FUNCNAME[0]}"
    >   echo "func1: FUNCNAME1 is ${FUNCNAME[1]}"
    >   echo "func1: FUNCNAME2 is ${FUNCNAME[2]}"
    >   func2
    > }
    > 
    > function func2()
    > {
    >   echo "func2: FUNCNAME0 is ${FUNCNAME[0]}"
    >   echo "func2: FUNCNAME1 is ${FUNCNAME[1]}"
    >   echo "func2: FUNCNAME2 is ${FUNCNAME[2]}"
    > }
    > 
    > func1
    > ```
    >
    > 执行上面的脚本`test.sh`，结果如下。
    >
    > ```
    > $ ./test.sh
    > func1: FUNCNAME0 is func1
    > func1: FUNCNAME1 is main
    > func1: FUNCNAME2 is
    > func2: FUNCNAME0 is func2
    > func2: FUNCNAME1 is func1
    > func2: FUNCNAME2 is main
    > ```
    >
    > 上面例子中，执行`func1`时，变量`FUNCNAME`的0号成员是`func1`，1号成员是调用`func1`的主脚本`main`。执行`func2`时，变量`FUNCNAME`的0号成员是`func2`，1号成员是调用`func2`的`func1`。

  - ##### `BASH_SOURCE`

    > 变量`BASH_SOURCE`返回一个数组，内容是当前的脚本调用堆栈。该数组的0号成员是当前执行的脚本，1号成员是调用当前脚本的脚本，以此类推，跟变量`FUNCNAME`是一一对应关系。
    >
    > 下面有两个子脚本`lib1.sh`和`lib2.sh`。
    >
    > ```
    > # lib1.sh
    > function func1()
    > {
    >   echo "func1: BASH_SOURCE0 is ${BASH_SOURCE[0]}"
    >   echo "func1: BASH_SOURCE1 is ${BASH_SOURCE[1]}"
    >   echo "func1: BASH_SOURCE2 is ${BASH_SOURCE[2]}"
    >   func2
    > }
    > # lib2.sh
    > function func2()
    > {
    >   echo "func2: BASH_SOURCE0 is ${BASH_SOURCE[0]}"
    >   echo "func2: BASH_SOURCE1 is ${BASH_SOURCE[1]}"
    >   echo "func2: BASH_SOURCE2 is ${BASH_SOURCE[2]}"
    > }
    > ```
    >
    > 然后，主脚本`main.sh`调用上面两个子脚本。
    >
    > ```
    > #!/bin/bash
    > # main.sh
    > 
    > source lib1.sh
    > source lib2.sh
    > 
    > func1
    > ```
    >
    > 执行主脚本`main.sh`，会得到下面的结果。
    >
    > ```
    > $ ./main.sh
    > func1: BASH_SOURCE0 is lib1.sh
    > func1: BASH_SOURCE1 is ./main.sh
    > func1: BASH_SOURCE2 is
    > func2: BASH_SOURCE0 is lib2.sh
    > func2: BASH_SOURCE1 is lib1.sh
    > func2: BASH_SOURCE2 is ./main.sh
    > ```
    >
    > 上面例子中，执行函数`func1`时，变量`BASH_SOURCE`的0号成员是`func1`所在的脚本`lib1.sh`，1号成员是主脚本`main.sh`；执行函数`func2`时，变量`BASH_SOURCE`的0号成员是`func2`所在的脚本`lib2.sh`，1号成员是调用`func2`的脚本`lib1.sh`。

  - ##### `BASH_LINENO`

    > 变量`BASH_LINENO`返回一个数组，内容是每一轮调用对应的行号。`${BASH_LINENO[$i]}`跟`${FUNCNAME[$i]}`是一一对应关系，表示`${FUNCNAME[$i]}`在调用它的脚本文件`${BASH_SOURCE[$i+1]}`里面的行号。
    >
    > 下面有两个子脚本`lib1.sh`和`lib2.sh`。
    >
    > ```
    > # lib1.sh
    > function func1()
    > {
    >   echo "func1: BASH_LINENO is ${BASH_LINENO[0]}"
    >   echo "func1: FUNCNAME is ${FUNCNAME[0]}"
    >   echo "func1: BASH_SOURCE is ${BASH_SOURCE[1]}"
    > 
    >   func2
    > }
    > # lib2.sh
    > function func2()
    > {
    >   echo "func2: BASH_LINENO is ${BASH_LINENO[0]}"
    >   echo "func2: FUNCNAME is ${FUNCNAME[0]}"
    >   echo "func2: BASH_SOURCE is ${BASH_SOURCE[1]}"
    > }
    > ```
    >
    > 然后，主脚本`main.sh`调用上面两个子脚本。
    >
    > ```
    > #!/bin/bash
    > # main.sh
    > 
    > source lib1.sh
    > source lib2.sh
    > 
    > func1
    > ```
    >
    > 执行主脚本`main.sh`，会得到下面的结果。
    >
    > ```
    > $ ./main.sh
    > func1: BASH_LINENO is 7
    > func1: FUNCNAME is func1
    > func1: BASH_SOURCE is main.sh
    > func2: BASH_LINENO is 8
    > func2: FUNCNAME is func2
    > func2: BASH_SOURCE is lib1.sh
    > ```
    >
    > 上面例子中，函数`func1`是在`main.sh`的第7行调用，函数`func2`是在`lib1.sh`的第8行调用的。

## `mktemp` 命令，`trap` 命令

> Bash 脚本有时需要创建临时文件或临时目录。常见的做法是，在`/tmp`目录里面创建文件或目录，这样做有很多弊端，使用`mktemp`命令是最安全的做法。

- #### 临时文件的安全问题

  > 直接创建临时文件，尤其在`/tmp`目录里面，往往会导致安全问题。
  >
  > 首先，`/tmp`目录是所有人可读写的，任何用户都可以往该目录里面写文件。创建的临时文件也是所有人可读的。
  >
  > ```
  > $ touch /tmp/info.txt
  > $ ls -l /tmp/info.txt
  > -rw-r--r-- 1 ruanyf ruanyf 0 12月 28 17:12 /tmp/info.txt
  > ```
  >
  > 上面命令在`/tmp`目录直接创建文件，该文件默认是所有人可读的。
  >
  > 其次，如果攻击者知道临时文件的文件名，他可以创建符号链接，链接到临时文件，可能导致系统运行异常。攻击者也可能向脚本提供一些恶意数据。因此，临时文件最好使用不可预测、每次都不一样的文件名，防止被利用。
  >
  > 最后，临时文件使用完毕，应该删除。但是，脚本意外退出时，往往会忽略清理临时文件。
  >
  > 生成临时文件应该遵循下面的规则：
  >
  > - 创建前检查文件是否已经存在。
  > - 确保临时文件已成功创建。
  > - 临时文件必须有权限的限制。
  > - 临时文件要使用不可预测的文件名。
  > - 脚本退出时，要删除临时文件（使用`trap`命令）。

- #### `mktemp` 命令的用法

  > `mktemp`命令就是为安全创建临时文件而设计的。虽然在创建临时文件之前，它不会检查临时文件是否存在，但是它支持唯一文件名和清除机制，因此可以减轻安全攻击的风险。
  >
  > 直接运行`mktemp`命令，就能生成一个临时文件。
  >
  > ```
  > $ mktemp
  > /tmp/tmp.4GcsWSG4vj
  > 
  > $ ls -l /tmp/tmp.4GcsWSG4vj
  > -rw------- 1 ruanyf ruanyf 0 12月 28 12:49 /tmp/tmp.4GcsWSG4vj
  > ```
  >
  > 上面命令中，`mktemp`命令生成的临时文件名是随机的，而且权限是只有用户本人可读写。
  >
  > Bash 脚本使用`mktemp`命令的用法如下。
  >
  > ```
  > #!/bin/bash
  > 
  > TMPFILE=$(mktemp)
  > echo "Our temp file is $TMPFILE"
  > ```
  >
  > 为了确保临时文件创建成功，`mktemp`命令后面最好使用 OR 运算符（`||`），保证创建失败时退出脚本。
  >
  > ```
  > #!/bin/bash
  > 
  > TMPFILE=$(mktemp) || exit 1
  > echo "Our temp file is $TMPFILE"
  > ```
  >
  > 为了保证脚本退出时临时文件被删除，可以使用`trap`命令指定退出时的清除操作。
  >
  > ```bash
  > #!/bin/bash
  > 
  > trap 'rm -f "$TMPFILE"' EXIT
  > 
  > TMPFILE=$(mktemp) || exit 1
  > echo "Our temp file is $TMPFILE"
  > ```

- #### `mktemp` 命令的参数

  > `-d`参数可以创建一个临时目录。
  >
  > ```
  > $ mktemp -d
  > /tmp/tmp.Wcau5UjmN6
  > ```
  >
  > `-p`参数可以指定临时文件所在的目录。默认是使用`$TMPDIR`环境变量指定的目录，如果这个变量没设置，那么使用`/tmp`目录。
  >
  > ```
  > $ mktemp -p /home/ruanyf/
  > /home/ruanyf/tmp.FOKEtvs2H3
  > ```
  >
  > `-t`参数可以指定临时文件的文件名模板，模板的末尾必须至少包含三个连续的`X`字符，表示随机字符，建议至少使用六个`X`。默认的文件名模板是`tmp.`后接十个随机字符。
  >
  > ```bash
  > $ mktemp -t mytemp.XXXXXXX
  > /tmp/mytemp.yZ1HgZV
  > ```

- #### `trap` 命令

  > `trap`命令用来在 Bash 脚本中响应系统信号。
  >
  > 最常见的系统信号就是 SIGINT（中断），即按 Ctrl + C 所产生的信号。`trap`命令的`-l`参数，可以列出所有的系统信号。
  >
  > ```
  > $ trap -l
  >  1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
  >  6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
  > 11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
  > 16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
  > 21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
  > 26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
  > 31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
  > 38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
  > 43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
  > 48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
  > 53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
  > 58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
  > 63) SIGRTMAX-1	64) SIGRTMAX
  > ```
  >
  > `trap`的命令格式如下。
  >
  > ```
  > $ trap [动作] [信号1] [信号2] ...
  > ```
  >
  > 上面代码中，“动作”是一个 Bash 命令，“信号”常用的有以下几个：
  >
  > - HUP：编号1，脚本与所在的终端脱离联系。
  > - INT：编号2，用户按下 Ctrl + C，意图让脚本终止运行。
  > - QUIT：编号3，用户按下 Ctrl + 斜杠，意图退出脚本。
  > - KILL：编号9，该信号用于杀死进程。
  > - TERM：编号15，这是`kill`命令发出的默认信号。
  > - EXIT：编号0，这不是系统信号，而是 Bash 脚本特有的信号，不管什么情况，只要退出脚本就会产生。
  >
  > `trap`命令响应`EXIT`信号的写法如下。
  >
  > ```
  > $ trap 'rm -f "$TMPFILE"' EXIT
  > ```
  >
  > 上面命令中，脚本遇到`EXIT`信号时，就会执行`rm -f "$TMPFILE"`。
  >
  > trap 命令的常见使用场景，就是在 Bash 脚本中指定退出时执行的清理命令。
  >
  > ```
  > #!/bin/bash
  > 
  > trap 'rm -f "$TMPFILE"' EXIT
  > 
  > TMPFILE=$(mktemp) || exit 1
  > ls /etc > $TMPFILE
  > if grep -qi "kernel" $TMPFILE; then
  >   echo 'find'
  > fi
  > ```
  >
  > 上面代码中，不管是脚本正常执行结束，还是用户按 Ctrl + C 终止，都会产生`EXIT`信号，从而触发删除临时文件。
  >
  > 注意，`trap`命令必须放在脚本的开头。否则，它上方的任何命令导致脚本退出，都不会被它捕获。
  >
  > 如果`trap`需要触发多条命令，可以封装一个 Bash 函数。
  >
  > ```bash
  > function egress {
  >   command1
  >   command2
  >   command3
  > }
  > 
  > trap egress EXIT
  > ```

## 启动环境

- #### Session

  > 用户每次使用 Shell，都会开启一个与 Shell 的 Session（对话）。
  >
  > Session 有两种类型：登录 Session 和非登录 Session，也可以叫做 login shell 和 non-login shell。

  - ##### 登录 Session

    > 登录 Session 是用户登录系统以后，系统为用户开启的原始 Session，通常需要用户输入用户名和密码进行登录。
    >
    > 登录 Session 一般进行整个系统环境的初始化，启动的初始化脚本依次如下。
    >
    > - `/etc/profile`：所有用户的全局配置脚本。
    > - `/etc/profile.d`目录里面所有`.sh`文件
    > - `~/.bash_profile`：用户的个人配置脚本。如果该脚本存在，则执行完就不再往下执行。
    > - `~/.bash_login`：如果`~/.bash_profile`没找到，则尝试执行这个脚本（C shell 的初始化脚本）。如果该脚本存在，则执行完就不再往下执行。
    > - `~/.profile`：如果`~/.bash_profile`和`~/.bash_login`都没找到，则尝试读取这个脚本（Bourne shell 和 Korn shell 的初始化脚本）。
    >
    > Linux 发行版更新的时候，会更新`/etc`里面的文件，比如`/etc/profile`，因此不要直接修改这个文件。如果想修改所有用户的登陆环境，就在`/etc/profile.d`目录里面新建`.sh`脚本。
    >
    > 如果想修改你个人的登录环境，一般是写在`~/.bash_profile`里面。下面是一个典型的`.bash_profile`文件。
    >
    > ```
    > # .bash_profile
    > PATH=/sbin:/usr/sbin:/bin:/usr/bin:/usr/local/bin
    > PATH=$PATH:$HOME/bin
    > 
    > SHELL=/bin/bash
    > MANPATH=/usr/man:/usr/X11/man
    > EDITOR=/usr/bin/vi
    > PS1='\h:\w\$ '
    > PS2='> '
    > 
    > if [ -f ~/.bashrc ]; then
    > . ~/.bashrc
    > fi
    > 
    > export PATH
    > export EDITOR
    > ```
    >
    > 可以看到，这个脚本定义了一些最基本的环境变量，然后执行了`~/.bashrc`。
    >
    > `bash`命令的`--login`参数，会强制执行登录 Session 会执行的脚本。
    >
    > ```
    > $ bash --login
    > ```
    >
    > `bash`命令的`--noprofile`参数，会跳过上面这些 Profile 脚本。
    >
    > ```bash
    > $ bash --noprofile
    > ```

  - ##### 非登录 Session

    > 非登录 Session 是用户进入系统以后，手动新建的 Session，这时不会进行环境初始化。比如，在命令行执行`bash`命令，就会新建一个非登录 Session。
    >
    > 非登录 Session 的初始化脚本依次如下。
    >
    > - `/etc/bash.bashrc`：对全体用户有效。
    > - `~/.bashrc`：仅对当前用户有效。
    >
    > 对用户来说，`~/.bashrc`通常是最重要的脚本。非登录 Session 默认会执行它，而登录 Session 一般也会通过调用执行它。每次新建一个 Bash 窗口，就相当于新建一个非登录 Session，所以`~/.bashrc`每次都会执行。注意，执行脚本相当于新建一个非互动的 Bash 环境，但是这种情况不会调用`~/.bashrc`。
    >
    > `bash`命令的`--norc`参数，可以禁止在非登录 Session 执行`~/.bashrc`脚本。
    >
    > ```
    > $ bash --norc
    > ```
    >
    > `bash`命令的`--rcfile`参数，指定另一个脚本代替`.bashrc`。
    >
    > ```bash
    > $ bash --rcfile testrc
    > ```

  - ##### `.bash_logout`

    > `~/.bash_logout`脚本在每次退出 Session 时执行，通常用来做一些清理工作和记录工作，比如删除临时文件，记录用户在本次 Session 花费的时间。
    >
    > 如果没有退出时要执行的命令，这个文件也可以不存在。

- #### 启动选项

  > 为了方便 Debug，有时在启动 Bash 的时候，可以加上启动参数。
  >
  > - `-n`：不运行脚本，只检查是否有语法错误。
  > - `-v`：输出每一行语句运行结果前，会先输出该行语句。
  > - `-x`：每一个命令处理之前，先输出该命令，再执行该命令。
  >
  > ```bash
  > $ bash -n scriptname
  > $ bash -v scriptname
  > $ bash -x scriptname
  > ```

- #### 键盘绑定

  > Bash 允许用户定义自己的快捷键。全局的键盘绑定文件默认为`/etc/inputrc`，你可以在主目录创建自己的键盘绑定文件`.inputrc`文件。如果定义了这个文件，需要在其中加入下面这行，保证全局绑定不会被遗漏。
  >
  > ```
  > $include /etc/inputrc
  > ```
  >
  > `.inputrc`文件里面的快捷键，可以像这样定义，`"\C-t":"pwd\n"`表示将`Ctrl + t`绑定为运行`pwd`命令。

## 命令提示符

> 用户进入 Bash 以后，Bash 会显示一个命令提示符，用来提示用户在该位置后面输入命令。

- #### 环境变量 PS1

  > 命令提示符通常是美元符号`$`，对于根用户则是井号`#`。这个符号是环境变量`PS1`决定的，执行下面的命令，可以看到当前命令提示符的定义。
  >
  > ```
  > $ echo $PS1
  > ```
  >
  > Bash 允许用户自定义命令提示符，只要改写这个变量即可。改写后的`PS1`，可以放在用户的 Bash 配置文件`.bashrc`里面，以后新建 Bash 对话时，新的提示符就会生效。要在当前窗口看到修改后的提示符，可以执行下面的命令。
  >
  > ```
  > $ source ~/.bashrc
  > ```
  >
  > 命令提示符的定义，可以包含特殊的转义字符，表示特定内容。
  >
  > - `\a`：响铃，计算机发出一记声音。
  > - `\d`：以星期、月、日格式表示当前日期，例如“Mon May 26”。
  > - `\h`：本机的主机名。
  > - `\H`：完整的主机名。
  > - `\j`：运行在当前 Shell 会话的工作数。
  > - `\l`：当前终端设备名。
  > - `\n`：一个换行符。
  > - `\r`：一个回车符。
  > - `\s`：Shell 的名称。
  > - `\t`：24小时制的`hours:minutes:seconds`格式表示当前时间。
  > - `\T`：12小时制的当前时间。
  > - `\@`：12小时制的`AM/PM`格式表示当前时间。
  > - `\A`：24小时制的`hours:minutes`表示当前时间。
  > - `\u`：当前用户名。
  > - `\v`：Shell 的版本号。
  > - `\V`：Shell 的版本号和发布号。
  > - `\w`：当前的工作路径。
  > - `\W`：当前目录名。
  > - `\!`：当前命令在命令历史中的编号。
  > - `\#`：当前 shell 会话中的命令数。
  > - `\$`：普通用户显示为`$`字符，根用户显示为`#`字符。
  > - `\[`：非打印字符序列的开始标志。
  > - `\]`：非打印字符序列的结束标志。
  >
  > 举例来说，`[\u@\h \W]\$`这个提示符定义，显示出来就是`[user@host ~]$`（具体的显示内容取决于你的系统）。
  >
  > ```
  > [user@host ~]$ echo $PS1
  > [\u@\h \W]\$
  > ```
  >
  > 改写`PS1`变量，就可以改变这个命令提示符。
  >
  > ```
  > $ PS1="\A \h \$ "
  > 17:33 host $
  > ```
  >
  > 注意，`$`后面最好跟一个空格，这样的话，用户的输入与提示符就不会连在一起。

- #### 颜色

  > 默认情况下，命令提示符是显示终端预定义的颜色。Bash 允许自定义提示符颜色。
  >
  > 使用下面的代码，可以设定其后文本的颜色。
  >
  > - `\033[0;30m`：黑色
  > - `\033[1;30m`：深灰色
  > - `\033[0;31m`：红色
  > - `\033[1;31m`：浅红色
  > - `\033[0;32m`：绿色
  > - `\033[1;32m`：浅绿色
  > - `\033[0;33m`：棕色
  > - `\033[1;33m`：黄色
  > - `\033[0;34m`：蓝色
  > - `\033[1;34m`：浅蓝色
  > - `\033[0;35m`：粉红
  > - `\033[1;35m`：浅粉色
  > - `\033[0;36m`：青色
  > - `\033[1;36m`：浅青色
  > - `\033[0;37m`：浅灰色
  > - `\033[1;37m`：白色
  >
  > 举例来说，如果要将提示符设为红色，可以将`PS1`设成下面的代码。
  >
  > ```
  > PS1='\[\033[0;31m\]<\u@\h \W>\$'
  > ```
  >
  > 但是，上面这样设置以后，用户在提示符后面输入的文本也是红色的。为了解决这个问题， 可以在结尾添加另一个特殊代码`\[\033[00m\]`，表示将其后的文本恢复到默认颜色。
  >
  > ```
  > PS1='\[\033[0;31m\]<\u@\h \W>\$\[\033[00m\]'
  > ```
  >
  > 除了设置前景颜色，Bash 还允许设置背景颜色。
  >
  > - `\033[0;40m`：蓝色
  > - `\033[1;44m`：黑色
  > - `\033[0;41m`：红色
  > - `\033[1;45m`：粉红
  > - `\033[0;42m`：绿色
  > - `\033[1;46m`：青色
  > - `\033[0;43m`：棕色
  > - `\033[1;47m`：浅灰色
  >
  > 下面是一个带有红色背景的提示符。
  >
  > ```tex
  > PS1='\[\033[0;41m\]<\u@\h \W>\$\[\033[0m\] '
  > ```

- #### 环境变量 PS2，PS3，PS4

  > 除了`PS1`，Bash 还提供了提示符相关的另外三个环境变量。
  >
  > 环境变量`PS2`是命令行折行输入时系统的提示符，默认为`>`。
  >
  > ```
  > $ echo "hello
  > > world"
  > ```
  >
  > 上面命令中，输入`hello`以后按下回车键，系统会提示继续输入。这时，第二行显示的提示符就是`PS2`定义的`>`。
  >
  > 环境变量`PS3`是使用`select`命令时，系统输入菜单的提示符。
  >
  > 环境变量`PS4`默认为`+`。它是使用 Bash 的`-x`参数执行脚本时，每一行命令在执行前都会先打印出来，并且在行首出现的那个提示符。
  >
  > 比如下面是脚本`test.sh`。
  >
  > ```
  > #!/bin/bash
  > 
  > echo "hello world"
  > ```
  >
  > 使用`-x`参数执行这个脚本。
  >
  > ```
  > $ bash -x test.sh
  > + echo 'hello world'
  > hello world
  > ```
  >
  > 上面例子中，输出的第一行前面有一个`+`，这就是变量`PS4`定义的。

------

