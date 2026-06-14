## `read` 命令

- #### 用法

  > 有时，脚本需要在执行过程中，由用户提供一部分数据，这时可以使用`read`命令。它将用户的输入存入一个变量，方便后面的代码使用。用户按下回车键，就表示输入结束。
  >
  > `read`命令的格式如下。
  >
  > ```
  > read [-options] [variable...]
  > ```
  >
  > 上面语法中，`options`是参数选项，`variable`是用来保存输入数值的一个或多个变量名。如果没有提供变量名，环境变量`REPLY`会包含用户输入的一整行数据。
  >
  > 下面是一个例子`demo.sh`。
  >
  > ```
  > #!/bin/bash
  > 
  > echo -n "输入一些文本 > "
  > read text
  > echo "你的输入：$text"
  > ```
  >
  > 上面例子中，先显示一行提示文本，然后会等待用户输入文本。用户输入的文本，存入变量`text`，在下一行显示出来。
  >
  > ```
  > $ bash demo.sh
  > 输入一些文本 > 你好，世界
  > 你的输入：你好，世界
  > ```
  >
  > `read`可以接受用户输入的多个值。
  >
  > ```
  > #!/bin/bash
  > echo Please, enter your firstname and lastname
  > read FN LN
  > echo "Hi! $LN, $FN !"
  > ```
  >
  > 上面例子中，`read`根据用户的输入，同时为两个变量赋值。
  >
  > 如果用户的输入项少于`read`命令给出的变量数目，那么额外的变量值为空。如果用户的输入项多于定义的变量，那么多余的输入项会包含到最后一个变量中。
  >
  > 如果`read`命令之后没有定义变量名，那么环境变量`REPLY`会包含所有的输入。
  >
  > ```
  > #!/bin/bash
  > # read-single: read multiple values into default variable
  > echo -n "Enter one or more values > "
  > read
  > echo "REPLY = '$REPLY'"
  > ```
  >
  > 上面脚本的运行结果如下。
  >
  > ```
  > $ read-single
  > Enter one or more values > a b c d
  > REPLY = 'a b c d'
  > ```
  >
  > `read`命令除了读取键盘输入，可以用来读取文件。
  >
  > ```
  > #!/bin/bash
  > 
  > filename='/etc/hosts'
  > 
  > while read myline
  > do
  >   echo "$myline"
  > done < $filename
  > ```
  >
  > 上面的例子通过`read`命令，读取一个文件的内容。`done`命令后面的定向符`<`，将文件内容导向`read`命令，每次读取一行，存入变量`myline`，直到文件读取完毕。

- #### 参数

  > `read`命令的参数如下。
  >
  > **（1）-t 参数**
  >
  > `read`命令的`-t`参数，设置了超时的秒数。如果超过了指定时间，用户仍然没有输入，脚本将放弃等待，继续向下执行。
  >
  > ```
  > #!/bin/bash
  > 
  > echo -n "输入一些文本 > "
  > if read -t 3 response; then
  >   echo "用户已经输入了"
  > else
  >   echo "用户没有输入"
  > fi
  > ```
  >
  > 上面例子中，输入命令会等待3秒，如果用户超过这个时间没有输入，这个命令就会执行失败。`if`根据命令的返回值，转入`else`代码块，继续往下执行。
  >
  > 环境变量`TMOUT`也可以起到同样作用，指定`read`命令等待用户输入的时间（单位为秒）。
  >
  > ```
  > $ TMOUT=3
  > $ read response
  > ```
  >
  > 上面例子也是等待3秒，如果用户还没有输入，就会超时。
  >
  > **（2）-p 参数**
  >
  > `-p`参数指定用户输入的提示信息。
  >
  > ```
  > read -p "Enter one or more values > "
  > echo "REPLY = '$REPLY'"
  > ```
  >
  > 上面例子中，先显示`Enter one or more values >`，再接受用户的输入。
  >
  > **（3）-a 参数**
  >
  > `-a`参数把用户的输入赋值给一个数组，从零号位置开始。
  >
  > ```
  > $ read -a people
  > alice duchess dodo
  > $ echo ${people[2]}
  > dodo
  > ```
  >
  > 上面例子中，用户输入被赋值给一个数组`people`，这个数组的2号成员就是`dodo`。
  >
  > **（4）-n 参数**
  >
  > `-n`参数指定只读取若干个字符作为变量值，而不是整行读取。
  >
  > ```
  > $ read -n 3 letter
  > abcdefghij
  > $ echo $letter
  > abc
  > ```
  >
  > 上面例子中，变量`letter`只包含3个字母。
  >
  > **（5）-e 参数**
  >
  > `-e`参数允许用户输入的时候，使用`readline`库提供的快捷键，比如自动补全。具体的快捷键可以参阅《行操作》一章。
  >
  > ```
  > #!/bin/bash
  > 
  > echo Please input the path to the file:
  > 
  > read -e fileName
  > 
  > echo $fileName
  > ```
  >
  > 上面例子中，`read`命令接受用户输入的文件名。这时，用户可能想使用 Tab 键的文件名“自动补全”功能，但是`read`命令的输入默认不支持`readline`库的功能。`-e`参数就可以允许用户使用自动补全。
  >
  > **（6）其他参数**
  >
  > - `-d delimiter`：定义字符串`delimiter`的第一个字符作为用户输入的结束，而不是一个换行符。
  > - `-r`：raw 模式，表示不把用户输入的反斜杠字符解释为转义字符。
  > - `-s`：使得用户的输入不显示在屏幕上，这常常用于输入密码或保密信息。
  > - `-u fd`：使用文件描述符`fd`作为输入。

- #### `IFS` 变量

  > `read`命令读取的值，默认是以空格分隔。可以通过自定义环境变量`IFS`（内部字段分隔符，Internal Field Separator 的缩写），修改分隔标志。
  >
  > `IFS`的默认值是空格、Tab 符号、换行符号，通常取第一个（即空格）。
  >
  > 如果把`IFS`定义成冒号（`:`）或分号（`;`），就可以分隔以这两个符号分隔的值，这对读取文件很有用。
  >
  > ```
  > #!/bin/bash
  > # read-ifs: read fields from a file
  > 
  > FILE=/etc/passwd
  > 
  > read -p "Enter a username > " user_name
  > file_info="$(grep "^$user_name:" $FILE)"
  > 
  > if [ -n "$file_info" ]; then
  >   IFS=":" read user pw uid gid name home shell <<< "$file_info"
  >   echo "User = '$user'"
  >   echo "UID = '$uid'"
  >   echo "GID = '$gid'"
  >   echo "Full Name = '$name'"
  >   echo "Home Dir. = '$home'"
  >   echo "Shell = '$shell'"
  > else
  >   echo "No such user '$user_name'" >&2
  >   exit 1
  > fi
  > ```
  >
  > 上面例子中，`IFS`设为冒号，然后用来分解`/etc/passwd`文件的一行。`IFS`的赋值命令和`read`命令写在一行，这样的话，`IFS`的改变仅对后面的命令生效，该命令执行后`IFS`会自动恢复原来的值。如果不写在一行，就要采用下面的写法。
  >
  > ```
  > OLD_IFS="$IFS"
  > IFS=":"
  > read user pw uid gid name home shell <<< "$file_info"
  > IFS="$OLD_IFS"
  > ```
  >
  > 另外，上面例子中，`<<<`是 Here 字符串，用于将变量值转为标准输入，因为`read`命令只能解析标准输入。
  >
  > 如果`IFS`设为空字符串，就等同于将整行读入一个变量。
  >
  > ```
  > #!/bin/bash
  > input="/path/to/txt/file"
  > while IFS= read -r line
  > do
  >   echo "$line"
  > done < "$input"
  > ```
  >
  > 上面的命令可以逐行读取文件，每一行存入变量`line`，打印出来以后再读取下一行。

## 条件判断

> 本章介绍 Bash 脚本的条件判断语法。

- #### `if` 结构

  > `if`是最常用的条件判断结构，只有符合给定条件时，才会执行指定的命令。它的语法如下。
  >
  > ```
  > if commands; then
  >   commands
  > [elif commands; then
  >   commands...]
  > [else
  >   commands]
  > fi
  > ```
  >
  > 这个命令分成三个部分：`if`、`elif`和`else`。其中，后两个部分是可选的。
  >
  > `if`关键字后面是主要的判断条件，`elif`用来添加在主条件不成立时的其他判断条件，`else`则是所有条件都不成立时要执行的部分。
  >
  > ```
  > if test $USER = "foo"; then
  >   echo "Hello foo."
  > else
  >   echo "You are not foo."
  > fi
  > ```
  >
  > 上面的例子中，判断条件是环境变量`$USER`是否等于`foo`，如果等于就输出`Hello foo.`，否则输出其他内容。
  >
  > `if`和`then`写在同一行时，需要分号分隔。分号是 Bash 的命令分隔符。它们也可以写成两行，这时不需要分号。
  >
  > ```
  > if true
  > then
  >   echo 'hello world'
  > fi
  > 
  > if false
  > then
  >   echo 'it is false' # 本行不会执行
  > fi
  > ```
  >
  > 上面的例子中，`true`和`false`是两个特殊命令，前者代表操作成功，后者代表操作失败。`if true`意味着命令部分总是会执行，`if false`意味着命令部分永远不会执行。
  >
  > 除了多行的写法，`if`结构也可以写成单行。
  >
  > ```
  > $ if true; then echo 'hello world'; fi
  > hello world
  > 
  > $ if false; then echo "It's true."; fi
  > ```
  >
  > 注意，`if`关键字后面也可以是一条命令，该条命令执行成功（返回值`0`），就意味着判断条件成立。
  >
  > ```
  > $ if echo 'hi'; then echo 'hello world'; fi
  > hi
  > hello world
  > ```
  >
  > 上面命令中，`if`后面是一条命令`echo 'hi'`。该命令会执行，如果返回值是`0`，则执行`then`的部分。
  >
  > `if`后面可以跟任意数量的命令。这时，所有命令都会执行，但是判断真伪只看最后一个命令，即使前面所有命令都失败，只要最后一个命令返回`0`，就会执行`then`的部分。
  >
  > ```
  > $ if false; true; then echo 'hello world'; fi
  > hello world
  > ```
  >
  > 上面例子中，`if`后面有两条命令（`false;true;`），第二条命令（`true`）决定了`then`的部分是否会执行。
  >
  > `elif`部分可以有多个。
  >
  > ```
  > #!/bin/bash
  > 
  > echo -n "输入一个1到3之间的数字（包含两端）> "
  > read character
  > if [ "$character" = "1" ]; then
  >     echo 1
  > elif [ "$character" = "2" ]; then
  >     echo 2
  > elif [ "$character" = "3" ]; then
  >     echo 3
  > else
  >     echo 输入不符合要求
  > fi
  > ```
  >
  > 上面例子中，如果用户输入`3`，就会连续判断3次。

- #### `test` 命令

  > `if`结构的判断条件，一般使用`test`命令，有三种形式。
  >
  > ```
  > # 写法一
  > test expression
  > 
  > # 写法二
  > [ expression ]
  > 
  > # 写法三
  > [[ expression ]]
  > ```
  >
  > 上面三种形式是等价的，但是第三种形式还支持正则判断，前两种不支持。
  >
  > 上面的`expression`是一个表达式。这个表达式为真，`test`命令执行成功（返回值为`0`）；表达式为伪，`test`命令执行失败（返回值为`1`）。注意，第二种和第三种写法，`[`和`]`与内部的表达式之间必须有空格。
  >
  > ```
  > $ test -f /etc/hosts
  > $ echo $?
  > 0
  > 
  > $ [ -f /etc/hosts ]
  > $  echo $?
  > 0
  > ```
  >
  > 上面的例子中，`test`命令采用两种写法，判断`/etc/hosts`文件是否存在，这两种写法是等价的。命令执行后，返回值为`0`，表示该文件确实存在。
  >
  > 实际上，`[`这个字符是`test`命令的一种简写形式，可以看作是一个独立的命令，这解释了为什么它后面必须有空格。
  >
  > 下面把`test`命令的三种形式，用在`if`结构中，判断一个文件是否存在。
  >
  > ```bash
  > # 写法一
  > if test -e /tmp/foo.txt ; then
  >   echo "Found foo.txt"
  > fi
  > 
  > # 写法二
  > if [ -e /tmp/foo.txt ] ; then
  >   echo "Found foo.txt"
  > fi
  > 
  > # 写法三
  > if [[ -e /tmp/foo.txt ]] ; then
  >   echo "Found foo.txt"
  > fi
  > ```

- #### 判断表达式

  > `if`关键字后面，跟的是一个命令。这个命令可以是`test`命令，也可以是其他命令。命令的返回值为`0`表示判断成立，否则表示不成立。因为这些命令主要是为了得到返回值，所以可以视为表达式。
  >
  > 常用的判断表达式有下面这些。

  - ##### 文件判断

    > 以下表达式用来判断文件状态。
    >
    > - `[ -b file ]`：如果 file 存在并且是一个块（设备）文件，则为`true`。
    > - `[ -c file ]`：如果 file 存在并且是一个字符（设备）文件，则为`true`。
    > - `[ -d file ]`：如果 file 存在并且是一个目录，则为`true`。
    > - `[ -e file ]`：如果 file 存在，则为`true`。
    > - `[ -f file ]`：如果 file 存在并且是一个普通文件，则为`true`。
    > - `[ -g file ]`：如果 file 存在并且设置了组 ID，则为`true`。
    > - `[ -G file ]`：如果 file 存在并且属于有效的组 ID，则为`true`。
    > - `[ -h file ]`：如果 file 存在并且是符号链接，则为`true`。
    > - `[ -k file ]`：如果 file 存在并且设置了它的“sticky bit”，则为`true`。
    > - `[ -L file ]`：如果 file 存在并且是一个符号链接，则为`true`。
    > - `[ -N file ]`：如果 file 存在并且自上次读取后已被修改，则为`true`。
    > - `[ -O file ]`：如果 file 存在并且属于有效的用户 ID，则为`true`。
    > - `[ -p file ]`：如果 file 存在并且是一个命名管道，则为`true`。
    > - `[ -r file ]`：如果 file 存在并且可读（当前用户有可读权限），则为`true`。
    > - `[ -s file ]`：如果 file 存在且其长度大于零，则为`true`。
    > - `[ -S file ]`：如果 file 存在且是一个网络 socket，则为`true`。
    > - `[ -t fd ]`：如果 fd 是一个文件描述符，并且重定向到终端，则为`true`。 这可以用来判断是否重定向了标准输入／输出／错误。
    > - `[ -u file ]`：如果 file 存在并且设置了 setuid 位，则为`true`。
    > - `[ -w file ]`：如果 file 存在并且可写（当前用户拥有可写权限），则为`true`。
    > - `[ -x file ]`：如果 file 存在并且可执行（有效用户有执行／搜索权限），则为`true`。
    > - `[ FILE1 -nt FILE2 ]`：如果 FILE1 比 FILE2 的更新时间更近，或者 FILE1 存在而 FILE2 不存在，则为`true`。
    > - `[ FILE1 -ot FILE2 ]`：如果 FILE1 比 FILE2 的更新时间更旧，或者 FILE2 存在而 FILE1 不存在，则为`true`。
    > - `[ FILE1 -ef FILE2 ]`：如果 FILE1 和 FILE2 引用相同的设备和 inode 编号，则为`true`。
    >
    > 下面是一个示例。
    >
    > ```
    > #!/bin/bash
    > 
    > FILE=~/.bashrc
    > 
    > if [ -e "$FILE" ]; then
    >   if [ -f "$FILE" ]; then
    >     echo "$FILE is a regular file."
    >   fi
    >   if [ -d "$FILE" ]; then
    >     echo "$FILE is a directory."
    >   fi
    >   if [ -r "$FILE" ]; then
    >     echo "$FILE is readable."
    >   fi
    >   if [ -w "$FILE" ]; then
    >     echo "$FILE is writable."
    >   fi
    >   if [ -x "$FILE" ]; then
    >     echo "$FILE is executable/searchable."
    >   fi
    > else
    >   echo "$FILE does not exist"
    >   exit 1
    > fi
    > ```
    >
    > 上面代码中，`$FILE`要放在双引号之中，这样可以防止变量`$FILE`为空，从而出错。因为`$FILE`如果为空，这时`[ -e $FILE ]`就变成`[ -e ]`，这会被判断为真。而`$FILE`放在双引号之中，`[ -e "$FILE" ]`就变成`[ -e "" ]`，这会被判断为伪。

  - ##### 字符串判断

    > 以下表达式用来判断字符串。
    >
    > - `[ string ]`：如果`string`不为空（长度大于0），则判断为真。
    > - `[ -n string ]`：如果字符串`string`的长度大于零，则判断为真。
    > - `[ -z string ]`：如果字符串`string`的长度为零，则判断为真。
    > - `[ string1 = string2 ]`：如果`string1`和`string2`相同，则判断为真。
    > - `[ string1 == string2 ]` 等同于`[ string1 = string2 ]`。
    > - `[ string1 != string2 ]`：如果`string1`和`string2`不相同，则判断为真。
    > - `[ string1 '>' string2 ]`：如果按照字典顺序`string1`排列在`string2`之后，则判断为真。
    > - `[ string1 '<' string2 ]`：如果按照字典顺序`string1`排列在`string2`之前，则判断为真。
    >
    > 注意，`test`命令内部的`>`和`<`，必须用引号引起来（或者是用反斜杠转义）。否则，它们会被 shell 解释为重定向操作符。
    >
    > 下面是一个示例。
    >
    > ```
    > #!/bin/bash
    > 
    > ANSWER=maybe
    > 
    > if [ -z "$ANSWER" ]; then
    >   echo "There is no answer." >&2
    >   exit 1
    > fi
    > if [ "$ANSWER" = "yes" ]; then
    >   echo "The answer is YES."
    > elif [ "$ANSWER" = "no" ]; then
    >   echo "The answer is NO."
    > elif [ "$ANSWER" = "maybe" ]; then
    >   echo "The answer is MAYBE."
    > else
    >   echo "The answer is UNKNOWN."
    > fi
    > ```
    >
    > 上面代码中，首先确定`$ANSWER`字符串是否为空。如果为空，就终止脚本，并把退出状态设为`1`。注意，这里的`echo`命令把错误信息`There is no answer.`重定向到标准错误，这是处理错误信息的常用方法。如果`$ANSWER`字符串不为空，就判断它的值是否等于`yes`、`no`或者`maybe`。
    >
    > 注意，字符串判断时，变量要放在双引号之中，比如`[ -n "$COUNT" ]`，否则变量替换成字符串以后，`test`命令可能会报错，提示参数过多。另外，如果不放在双引号之中，变量为空时，命令会变成`[ -n ]`，这时会判断为真。如果放在双引号之中，`[ -n "" ]`就判断为伪。

  - ##### 整数判断

    > 下面的表达式用于判断整数。
    >
    > - `[ integer1 -eq integer2 ]`：如果`integer1`等于`integer2`，则为`true`。
    > - `[ integer1 -ne integer2 ]`：如果`integer1`不等于`integer2`，则为`true`。
    > - `[ integer1 -le integer2 ]`：如果`integer1`小于或等于`integer2`，则为`true`。
    > - `[ integer1 -lt integer2 ]`：如果`integer1`小于`integer2`，则为`true`。
    > - `[ integer1 -ge integer2 ]`：如果`integer1`大于或等于`integer2`，则为`true`。
    > - `[ integer1 -gt integer2 ]`：如果`integer1`大于`integer2`，则为`true`。
    >
    > 下面是一个用法的例子。
    >
    > ```
    > #!/bin/bash
    > 
    > INT=-5
    > 
    > if [ -z "$INT" ]; then
    >   echo "INT is empty." >&2
    >   exit 1
    > fi
    > if [ $INT -eq 0 ]; then
    >   echo "INT is zero."
    > else
    >   if [ $INT -lt 0 ]; then
    >     echo "INT is negative."
    >   else
    >     echo "INT is positive."
    >   fi
    >   if [ $((INT % 2)) -eq 0 ]; then
    >     echo "INT is even."
    >   else
    >     echo "INT is odd."
    >   fi
    > fi
    > ```
    >
    > 上面例子中，先判断变量`$INT`是否为空，然后判断是否为`0`，接着判断正负，最后通过求余数判断奇偶。

  - ##### 正则判断

    > `[[ expression ]]`这种判断形式，支持正则表达式。
    >
    > ```
    > [[ string1 =~ regex ]]
    > ```
    >
    > 上面的语法中，`regex`是一个正则表示式，`=~`是正则比较运算符。
    >
    > 下面是一个例子。
    >
    > ```
    > #!/bin/bash
    > 
    > INT=-5
    > 
    > if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    >   echo "INT is an integer."
    >   exit 0
    > else
    >   echo "INT is not an integer." >&2
    >   exit 1
    > fi
    > ```
    >
    > 上面代码中，先判断变量`INT`的字符串形式，是否满足`^-?[0-9]+$`的正则模式，如果满足就表明它是一个整数。

  - ##### `test` 判断的逻辑运算

    > 通过逻辑运算，可以把多个`test`判断表达式结合起来，创造更复杂的判断。三种逻辑运算`AND`，`OR`，和`NOT`，都有自己的专用符号。
    >
    > - `AND`运算：符号`&&`，也可使用参数`-a`。
    > - `OR`运算：符号`||`，也可使用参数`-o`。
    > - `NOT`运算：符号`!`。
    >
    > 下面是一个`AND`的例子，判断整数是否在某个范围之内。
    >
    > ```
    > #!/bin/bash
    > 
    > MIN_VAL=1
    > MAX_VAL=100
    > 
    > INT=50
    > 
    > if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    >   if [[ $INT -ge $MIN_VAL && $INT -le $MAX_VAL ]]; then
    >     echo "$INT is within $MIN_VAL to $MAX_VAL."
    >   else
    >     echo "$INT is out of range."
    >   fi
    > else
    >   echo "INT is not an integer." >&2
    >   exit 1
    > fi
    > ```
    >
    > 上面例子中，`&&`用来连接两个判断条件：大于等于`$MIN_VAL`，并且小于等于`$MAX_VAL`。
    >
    > 使用否定操作符`!`时，最好用圆括号确定转义的范围。
    >
    > ```
    > if [ ! \( $INT -ge $MIN_VAL -a $INT -le $MAX_VAL \) ]; then
    >     echo "$INT is outside $MIN_VAL to $MAX_VAL."
    > else
    >     echo "$INT is in range."
    > fi
    > ```
    >
    > 上面例子中，`test`命令内部使用的圆括号，必须使用引号或者转义，否则会被 Bash 解释。
    >
    > 使用`-a`连接两个判断条件不太直观，一般推荐使用`&&`代替，上面的脚本可以改写成下面这样。
    >
    > ```bash
    > if !([ $INT -ge $MIN_VAL ] && [ $INT -le $MAX_VAL ]); then
    >   echo "$INT is outside $MIN_VAL to $MAX_VAL."
    > else
    >   echo "$INT is in range."
    > fi
    > ```

  - ##### 算术判断

    > Bash 还提供了`((...))`作为算术条件，进行算术运算的判断。
    >
    > ```
    > if ((3 > 2)); then
    >   echo "true"
    > fi
    > ```
    >
    > 上面代码执行后，会打印出`true`。
    >
    > 注意，算术判断不需要使用`test`命令，而是直接使用`((...))`结构。这个结构的返回值，决定了判断的真伪。
    >
    > 如果算术计算的结果是非零值，则表示判断成立。这一点跟命令的返回值正好相反，需要小心。
    >
    > ```
    > $ if ((1)); then echo "It is true."; fi
    > It is true.
    > $ if ((0)); then echo "It is true."; else echo "it is false."; fi
    > It is false.
    > ```
    >
    > 上面例子中，`((1))`表示判断成立，`((0))`表示判断不成立。
    >
    > 算术条件`((...))`也可以用于变量赋值。
    >
    > ```
    > $ if (( foo = 5 ));then echo "foo is $foo"; fi
    > foo is 5
    > ```
    >
    > 上面例子中，`(( foo = 5 ))`完成了两件事情。首先把`5`赋值给变量`foo`，然后根据返回值`5`，判断条件为真。
    >
    > 注意，赋值语句返回等号右边的值，如果返回的是`0`，则判断为假。
    >
    > ```
    > $ if (( foo = 0 ));then echo "It is true.";else echo "It is false."; fi
    > It is false.
    > ```
    >
    > 下面是用算术条件改写的数值判断脚本。
    >
    > ```
    > #!/bin/bash
    > 
    > INT=-5
    > 
    > if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    >   if ((INT == 0)); then
    >     echo "INT is zero."
    >   else
    >     if ((INT < 0)); then
    >       echo "INT is negative."
    >     else
    >       echo "INT is positive."
    >     fi
    >     if (( ((INT % 2)) == 0)); then
    >       echo "INT is even."
    >     else
    >       echo "INT is odd."
    >     fi
    >   fi
    > else
    >   echo "INT is not an integer." >&2
    >   exit 1
    > fi
    > ```
    >
    > 只要是算术表达式，都能用于`((...))`语法，详见《Bash 的算术运算》一章。

  - ##### 普通命令的逻辑运算

    > 如果`if`结构使用的不是`test`命令，而是普通命令，比如上一节的`((...))`算术运算，或者`test`命令与普通命令混用，那么可以使用 Bash 的命令控制操作符`&&`（AND）和`||`（OR），进行多个命令的逻辑运算。
    >
    > ```
    > $ command1 && command2
    > $ command1 || command2
    > ```
    >
    > 对于`&&`操作符，先执行`command1`，只有`command1`执行成功后， 才会执行`command2`。对于`||`操作符，先执行`command1`，只有`command1`执行失败后， 才会执行`command2`。
    >
    > ```
    > $ mkdir temp && cd temp
    > ```
    >
    > 上面的命令会创建一个名为`temp`的目录，执行成功后，才会执行第二个命令，进入这个目录。
    >
    > ```
    > $ [ -d temp ] || mkdir temp
    > ```
    >
    > 上面的命令会测试目录`temp`是否存在，如果不存在，就会执行第二个命令，创建这个目录。这种写法非常有助于在脚本中处理错误。
    >
    > ```
    > [ ! -d temp ] && exit 1
    > ```
    >
    > 上面的命令中，如果`temp`子目录不存在，脚本会终止，并且返回值为`1`。
    >
    > 下面就是`if`与`&&`结合使用的写法。
    >
    > ```
    > if [ condition ] && [ condition ]; then
    >   command
    > fi
    > ```
    >
    > 下面是一个示例。
    >
    > ```
    > #! /bin/bash
    > 
    > filename=$1
    > word1=$2
    > word2=$3
    > 
    > if grep $word1 $filename && grep $word2 $filename
    > then
    >   echo "$word1 and $word2 are both in $filename."
    > fi
    > ```
    >
    > 上面的例子只有在指定文件里面，同时存在搜索词`word1`和`word2`，就会执行`if`的命令部分。
    >
    > 下面的示例演示如何将一个`&&`判断表达式，改写成对应的`if`结构。
    >
    > ```bash
    > [[ -d "$dir_name" ]] && cd "$dir_name" && rm *
    > 
    > # 等同于
    > 
    > if [[ ! -d "$dir_name" ]]; then
    >   echo "No such directory: '$dir_name'" >&2
    >   exit 1
    > fi
    > if ! cd "$dir_name"; then
    >   echo "Cannot cd to '$dir_name'" >&2
    >   exit 1
    > fi
    > if ! rm *; then
    >   echo "File deletion failed. Check results" >&2
    >   exit 1
    > fi
    > ```

- #### `case` 结构

  > `case`结构用于多值判断，可以为每个值指定对应的命令，跟包含多个`elif`的`if`结构等价，但是语义更好。它的语法如下。
  >
  > ```
  > case expression in
  >   pattern )
  >     commands ;;
  >   pattern )
  >     commands ;;
  >   ...
  > esac
  > ```
  >
  > 上面代码中，`expression`是一个表达式，`pattern`是表达式的值或者一个模式，可以有多条，用来匹配多个值，每条以两个分号（`;`）结尾。
  >
  > ```
  > #!/bin/bash
  > 
  > echo -n "输入一个1到3之间的数字（包含两端）> "
  > read character
  > case $character in
  >   1 ) echo 1
  >     ;;
  >   2 ) echo 2
  >     ;;
  >   3 ) echo 3
  >     ;;
  >   * ) echo 输入不符合要求
  > esac
  > ```
  >
  > 上面例子中，最后一条匹配语句的模式是`*`，这个通配符可以匹配其他字符和没有输入字符的情况，类似`if`的`else`部分。
  >
  > 下面是另一个例子。
  >
  > ```
  > #!/bin/bash
  > 
  > OS=$(uname -s)
  > 
  > case "$OS" in
  >   FreeBSD) echo "This is FreeBSD" ;;
  >   Darwin) echo "This is Mac OSX" ;;
  >   AIX) echo "This is AIX" ;;
  >   Minix) echo "This is Minix" ;;
  >   Linux) echo "This is Linux" ;;
  >   *) echo "Failed to identify this OS" ;;
  > esac
  > ```
  >
  > 上面的例子判断当前是什么操作系统。
  >
  > `case`的匹配模式可以使用各种通配符，下面是一些例子。
  >
  > - `a)`：匹配`a`。
  > - `a|b)`：匹配`a`或`b`。
  > - `[[:alpha:]])`：匹配单个字母。
  > - `???)`：匹配3个字符的单词。
  > - `*.txt)`：匹配`.txt`结尾。
  > - `*)`：匹配任意输入，通常作为`case`结构的最后一个模式。
  >
  > ```
  > #!/bin/bash
  > 
  > echo -n "输入一个字母或数字 > "
  > read character
  > case $character in
  >   [[:lower:]] | [[:upper:]] ) echo "输入了字母 $character"
  >                               ;;
  >   [0-9] )                     echo "输入了数字 $character"
  >                               ;;
  >   * )                         echo "输入不符合要求"
  > esac
  > ```
  >
  > 上面例子中，使用通配符`[[:lower:]] | [[:upper:]]`匹配字母，`[0-9]`匹配数字。
  >
  > Bash 4.0之前，`case`结构只能匹配一个条件，然后就会退出`case`结构。Bash 4.0之后，允许匹配多个条件，这时可以用`;;&`终止每个条件块。
  >
  > ```
  > #!/bin/bash
  > # test.sh
  > 
  > read -n 1 -p "Type a character > "
  > echo
  > case $REPLY in
  >   [[:upper:]])    echo "'$REPLY' is upper case." ;;&
  >   [[:lower:]])    echo "'$REPLY' is lower case." ;;&
  >   [[:alpha:]])    echo "'$REPLY' is alphabetic." ;;&
  >   [[:digit:]])    echo "'$REPLY' is a digit." ;;&
  >   [[:graph:]])    echo "'$REPLY' is a visible character." ;;&
  >   [[:punct:]])    echo "'$REPLY' is a punctuation symbol." ;;&
  >   [[:space:]])    echo "'$REPLY' is a whitespace character." ;;&
  >   [[:xdigit:]])   echo "'$REPLY' is a hexadecimal digit." ;;&
  > esac
  > ```
  >
  > 执行上面的脚本，会得到下面的结果。
  >
  > ```
  > $ test.sh
  > Type a character > a
  > 'a' is lower case.
  > 'a' is alphabetic.
  > 'a' is a visible character.
  > 'a' is a hexadecimal digit.
  > ```
  >
  > 可以看到条件语句结尾添加了`;;&`以后，在匹配一个条件之后，并没有退出`case`结构，而是继续判断下一个条件。

## 循环

> Bash 提供三种循环语法`for`、`while`和`until`。

- #### `while` 循环

  > `while`循环有一个判断条件，只要符合条件，就不断循环执行指定的语句。
  >
  > ```
  > while condition; do
  >   commands
  > done
  > ```
  >
  > 上面代码中，只要满足条件`condition`，就会执行命令`commands`。然后，再次判断是否满足条件`condition`，只要满足，就会一直执行下去。只有不满足条件，才会退出循环。
  >
  > 循环条件`condition`可以使用`test`命令，跟`if`结构的判断条件写法一致。
  >
  > ```
  > #!/bin/bash
  > 
  > number=0
  > while [ "$number" -lt 10 ]; do
  >   echo "Number = $number"
  >   number=$((number + 1))
  > done
  > ```
  >
  > 上面例子中，只要变量`$number`小于10，就会不断加1，直到`$number`等于10，然后退出循环。
  >
  > 关键字`do`可以跟`while`不在同一行，这时两者之间不需要使用分号分隔。
  >
  > ```
  > while true
  > do
  >   echo 'Hi, while looping ...';
  > done
  > ```
  >
  > 上面的例子会无限循环，可以按下 Ctrl + c 停止。
  >
  > `while`循环写成一行，也是可以的。
  >
  > ```
  > $ while true; do echo 'Hi, while looping ...'; done
  > ```
  >
  > `while`的条件部分也可以是执行一个命令。
  >
  > ```
  > $ while echo 'ECHO'; do echo 'Hi, while looping ...'; done
  > ```
  >
  > 上面例子中，判断条件是`echo 'ECHO'`。由于这个命令总是执行成功，所以上面命令会产生无限循环。
  >
  > `while`的条件部分可以执行任意数量的命令，但是执行结果的真伪只看最后一个命令的执行结果。
  >
  > ```
  > $ while true; false; do echo 'Hi, looping ...'; done
  > ```
  >
  > 上面代码运行后，不会有任何输出，因为`while`的最后一个命令是`false`。

- #### `until` 循环

  > `until`循环与`while`循环恰好相反，只要不符合判断条件（判断条件失败），就不断循环执行指定的语句。一旦符合判断条件，就退出循环。
  >
  > ```
  > until condition; do
  >   commands
  > done
  > ```
  >
  > 关键字`do`可以与`until`不写在同一行，这时两者之间不需要分号分隔。
  >
  > ```
  > until condition
  > do
  >   commands
  > done
  > ```
  >
  > 下面是一个例子。
  >
  > ```
  > $ until false; do echo 'Hi, until looping ...'; done
  > Hi, until looping ...
  > Hi, until looping ...
  > Hi, until looping ...
  > ^C
  > ```
  >
  > 上面代码中，`until`的部分一直为`false`，导致命令无限运行，必须按下 Ctrl + c 终止。
  >
  > ```
  > #!/bin/bash
  > 
  > number=0
  > until [ "$number" -ge 10 ]; do
  >   echo "Number = $number"
  >   number=$((number + 1))
  > done
  > ```
  >
  > 上面例子中，只要变量`number`小于10，就会不断加1，直到`number`大于等于10，就退出循环。
  >
  > `until`的条件部分也可以是一个命令，表示在这个命令执行成功之前，不断重复尝试。
  >
  > ```
  > until cp $1 $2; do
  >   echo 'Attempt to copy failed. waiting...'
  >   sleep 5
  > done
  > ```
  >
  > 上面例子表示，只要`cp $1 $2`这个命令执行不成功，就5秒钟后再尝试一次，直到成功为止。
  >
  > `until`循环都可以转为`while`循环，只要把条件设为否定即可。上面这个例子可以改写如下。
  >
  > ```
  > while ! cp $1 $2; do
  >   echo 'Attempt to copy failed. waiting...'
  >   sleep 5
  > done
  > ```
  >
  > 一般来说，`until`用得比较少，完全可以统一都使用`while`。

- #### `for...in` 循环

  > `for...in`循环用于遍历列表的每一项。
  >
  > ```
  > for variable in list
  > do
  >   commands
  > done
  > ```
  >
  > 上面语法中，`for`循环会依次从`list`列表中取出一项，作为变量`variable`，然后在循环体中进行处理。
  >
  > 关键词`do`可以跟`for`写在同一行，两者使用分号分隔。
  >
  > ```
  > for variable in list; do
  >   commands
  > done
  > ```
  >
  > 下面是一个例子。
  >
  > ```
  > #!/bin/bash
  > 
  > for i in word1 word2 word3; do
  >   echo $i
  > done
  > ```
  >
  > 上面例子中，`word1 word2 word3`是一个包含三个单词的列表，变量`i`依次等于`word1`、`word2`、`word3`，命令`echo $i`则会相应地执行三次。
  >
  > 列表可以由通配符产生。
  >
  > ```
  > for i in *.png; do
  >   ls -l $i
  > done
  > ```
  >
  > 上面例子中，`*.png`会替换成当前目录中所有 PNG 图片文件，变量`i`会依次等于每一个文件。
  >
  > 列表也可以通过子命令产生。
  >
  > ```
  > #!/bin/bash
  > 
  > count=0
  > for i in $(cat ~/.bash_profile); do
  >   count=$((count + 1))
  >   echo "Word $count ($i) contains $(echo -n $i | wc -c) characters"
  > done
  > ```
  >
  > 上面例子中，`cat ~/.bash_profile`命令会输出`~/.bash_profile`文件的内容，然后通过遍历每一个词，计算该文件一共包含多少个词，以及每个词有多少个字符。
  >
  > `in list`的部分可以省略，这时`list`默认等于脚本的所有参数`$@`。但是，为了可读性，最好还是不要省略，参考下面的例子。
  >
  > ```
  > for filename; do
  >   echo "$filename"
  > done
  > 
  > # 等同于
  > 
  > for filename in "$@" ; do
  >   echo "$filename"
  > done
  > ```
  >
  > 在函数体中也是一样的，`for...in`循环省略`in list`的部分，则`list`默认等于函数的所有参数。

- #### `for` 循环

  > `for`循环还支持 C 语言的循环语法。
  >
  > ```
  > for (( expression1; expression2; expression3 )); do
  >   commands
  > done
  > ```
  >
  > 上面代码中，`expression1`用来初始化循环条件，`expression2`用来决定循环结束的条件，`expression3`在每次循环迭代的末尾执行，用于更新值。
  >
  > 注意，循环条件放在双重圆括号之中。另外，圆括号之中使用变量，不必加上美元符号`$`。
  >
  > 它等同于下面的`while`循环。
  >
  > ```
  > (( expression1 ))
  > while (( expression2 )); do
  >   commands
  >   (( expression3 ))
  > done
  > ```
  >
  > 下面是一个例子。
  >
  > ```
  > for (( i=0; i<5; i=i+1 )); do
  >   echo $i
  > done
  > ```
  >
  > 上面代码中，初始化变量`i`的值为0，循环执行的条件是`i`小于5。每次循环迭代结束时，`i`的值加1。
  >
  > `for`条件部分的三个语句，都可以省略。
  >
  > ```
  > for ((;;))
  > do
  >   read var
  >   if [ "$var" = "." ]; then
  >     break
  >   fi
  > done
  > ```
  >
  > 上面脚本会反复读取命令行输入，直到用户输入了一个点（`.`）为止，才会跳出循环。

- #### `break`，`continue`

  > Bash 提供了两个内部命令`break`和`continue`，用来在循环内部跳出循环。
  >
  > `break`命令立即终止循环，程序继续执行循环块之后的语句，即不再执行剩下的循环。
  >
  > ```
  > #!/bin/bash
  > 
  > for number in 1 2 3 4 5 6
  > do
  >   echo "number is $number"
  >   if [ "$number" = "3" ]; then
  >     break
  >   fi
  > done
  > ```
  >
  > 上面例子只会打印3行结果。一旦变量`$number`等于3，就会跳出循环，不再继续执行。
  >
  > `continue`命令立即终止本轮循环，开始执行下一轮循环。
  >
  > ```
  > #!/bin/bash
  > 
  > while read -p "What file do you want to test?" filename
  > do
  >   if [ ! -e "$filename" ]; then
  >     echo "The file does not exist."
  >     continue
  >   fi
  > 
  >   echo "You entered a valid file.."
  > done
  > ```
  >
  > 上面例子中，只要用户输入的文件不存在，`continue`命令就会生效，直接进入下一轮循环（让用户重新输入文件名），不再执行后面的打印语句。

- #### `select` 结构

  > `select`结构主要用来生成简单的菜单。它的语法与`for...in`循环基本一致。
  >
  > ```
  > select name
  > [in list]
  > do
  >   commands
  > done
  > ```
  >
  > Bash 会对`select`依次进行下面的处理。
  >
  > 1. `select`生成一个菜单，内容是列表`list`的每一项，并且每一项前面还有一个数字编号。
  > 2. Bash 提示用户选择一项，输入它的编号。
  > 3. 用户输入以后，Bash 会将该项的内容存在变量`name`，该项的编号存入环境变量`REPLY`。如果用户没有输入，就按回车键，Bash 会重新输出菜单，让用户选择。
  > 4. 执行命令体`commands`。
  > 5. 执行结束后，回到第一步，重复这个过程。
  >
  > 下面是一个例子。
  >
  > ```
  > #!/bin/bash
  > # select.sh
  > 
  > select brand in Samsung Sony iphone symphony Walton
  > do
  >   echo "You have chosen $brand"
  > done
  > ```
  >
  > 执行上面的脚本，Bash 会输出一个品牌的列表，让用户选择。
  >
  > ```
  > $ ./select.sh
  > 1) Samsung
  > 2) Sony
  > 3) iphone
  > 4) symphony
  > 5) Walton
  > #?
  > ```
  >
  > 如果用户没有输入编号，直接按回车键。Bash 就会重新输出一遍这个菜单，直到用户按下`Ctrl + c`，退出执行。
  >
  > `select`可以与`case`结合，针对不同项，执行不同的命令。
  >
  > ```
  > #!/bin/bash
  > 
  > echo "Which Operating System do you like?"
  > 
  > select os in Ubuntu LinuxMint Windows8 Windows10 WindowsXP
  > do
  >   case $os in
  >     "Ubuntu"|"LinuxMint")
  >       echo "I also use $os."
  >     ;;
  >     "Windows8" | "Windows10" | "WindowsXP")
  >       echo "Why don't you try Linux?"
  >     ;;
  >     *)
  >       echo "Invalid entry."
  >       break
  >     ;;
  >   esac
  > done
  > ```
  >
  > 上面例子中，`case`针对用户选择的不同项，执行不同的命令。

## 函数

- #### 简介

  > 函数（function）是可以重复使用的代码片段，有利于代码的复用。它与别名（alias）的区别是，别名只适合封装简单的单个命令，函数则可以封装复杂的多行命令。
  >
  > 函数总是在当前 Shell 执行，这是跟脚本的一个重大区别，Bash 会新建一个子 Shell 执行脚本。如果函数与脚本同名，函数会优先执行。但是，函数的优先级不如别名，即如果函数与别名同名，那么别名优先执行。
  >
  > Bash 函数定义的语法有两种。
  >
  > ```
  > # 第一种
  > fn() {
  >   # codes
  > }
  > 
  > # 第二种
  > function fn() {
  >   # codes
  > }
  > ```
  >
  > 上面代码中，`fn`是自定义的函数名，函数代码就写在大括号之中。这两种写法是等价的。
  >
  > 下面是一个简单函数的例子。
  >
  > ```
  > hello() {
  >   echo "Hello $1"
  > }
  > ```
  >
  > 上面代码中，函数体里面的`$1`表示函数调用时的第一个参数。
  >
  > 调用时，就直接写函数名，参数跟在函数名后面。
  >
  > ```
  > $ hello world
  > Hello world
  > ```
  >
  > 下面是一个多行函数的例子，显示当前日期时间。
  >
  > ```
  > today() {
  >   echo -n "Today's date is: "
  >   date +"%A, %B %-d, %Y"
  > }
  > ```
  >
  > 删除一个函数，可以使用`unset`命令。
  >
  > ```
  > unset -f functionName
  > ```
  >
  > 查看当前 Shell 已经定义的所有函数，可以使用`declare`命令。
  >
  > ```
  > $ declare -f
  > ```
  >
  > 上面的`declare`命令不仅会输出函数名，还会输出所有定义。输出顺序是按照函数名的字母表顺序。由于会输出很多内容，最好通过管道命令配合`more`或`less`使用。
  >
  > `declare`命令还支持查看单个函数的定义。
  >
  > ```
  > $ declare -f functionName
  > ```
  >
  > `declare -F`可以输出所有已经定义的函数名，不含函数体。
  >
  > ```bash
  > $ declare -F
  > ```

- #### 参数变量

  > 函数体内可以使用参数变量，获取函数参数。函数的参数变量，与脚本参数变量是一致的。
  >
  > - `$1`~`$9`：函数的第一个到第9个的参数。
  > - `$0`：函数所在的脚本名。
  > - `$#`：函数的参数总数。
  > - `$@`：函数的全部参数，参数之间使用空格分隔。
  > - `$*`：函数的全部参数，参数之间使用变量`$IFS`值的第一个字符分隔，默认为空格，但是可以自定义。
  >
  > 如果函数的参数多于9个，那么第10个参数可以用`${10}`的形式引用，以此类推。
  >
  > 下面是一个示例脚本`test.sh`。
  >
  > ```
  > #!/bin/bash
  > # test.sh
  > 
  > function alice {
  >   echo "alice: $@"
  >   echo "$0: $1 $2 $3 $4"
  >   echo "$# arguments"
  > 
  > }
  > 
  > alice in wonderland
  > ```
  >
  > 运行该脚本，结果如下。
  >
  > ```
  > $ bash test.sh
  > alice: in wonderland
  > test.sh: in wonderland
  > 2 arguments
  > ```
  >
  > 上面例子中，由于函数`alice`只有第一个和第二个参数，所以第三个和第四个参数为空。
  >
  > 下面是一个日志函数的例子。
  >
  > ```
  > function log_msg {
  >   echo "[`date '+ %F %T'` ]: $@"
  > }
  > ```
  >
  > 使用方法如下。
  >
  > ```bash
  > $ log_msg "This is sample log message"
  > [ 2018-08-16 19:56:34 ]: This is sample log message
  > ```

- #### `return` 命令

  > `return`命令用于从函数返回一个值。函数执行到这条命令，就不再往下执行了，直接返回了。
  >
  > ```
  > function func_return_value {
  >   return 10
  > }
  > ```
  >
  > 函数将返回值返回给调用者。如果命令行直接执行函数，下一个命令可以用`$?`拿到返回值。
  >
  > ```
  > $ func_return_value
  > $ echo "Value returned by function is: $?"
  > Value returned by function is: 10
  > ```
  >
  > `return`后面不跟参数，只用于返回也是可以的。
  >
  > ```bash
  > function name {
  >   commands
  >   return
  > }
  > ```

- #### 全局变量和局部变量，`local` 命令

  > Bash 函数体内直接声明的变量，属于全局变量，整个脚本都可以读取。这一点需要特别小心。
  >
  > ```
  > # 脚本 test.sh
  > fn () {
  >   foo=1
  >   echo "fn: foo = $foo"
  > }
  > 
  > fn
  > echo "global: foo = $foo"
  > ```
  >
  > 上面脚本的运行结果如下。
  >
  > ```
  > $ bash test.sh
  > fn: foo = 1
  > global: foo = 1
  > ```
  >
  > 上面例子中，变量`$foo`是在函数`fn`内部声明的，函数体外也可以读取。
  >
  > 函数体内不仅可以声明全局变量，还可以修改全局变量。
  >
  > ```
  > #! /bin/bash
  > foo=1
  > 
  > fn () {
  >   foo=2
  > }
  > 
  > fn
  > 
  > echo $foo
  > ```
  >
  > 上面代码执行后，输出的变量`$foo`值为2。
  >
  > 函数里面可以用`local`命令声明局部变量。
  >
  > ```
  > #! /bin/bash
  > # 脚本 test.sh
  > fn () {
  >   local foo
  >   foo=1
  >   echo "fn: foo = $foo"
  > }
  > 
  > fn
  > echo "global: foo = $foo"
  > ```
  >
  > 上面脚本的运行结果如下。
  >
  > ```
  > $ bash test.sh
  > fn: foo = 1
  > global: foo =
  > ```
  >
  > 上面例子中，`local`命令声明的`$foo`变量，只在函数体内有效，函数体外没有定义。

------

