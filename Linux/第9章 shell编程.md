![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=DNbXn&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
# Linux系统结构
Linux操作系统是一种开放源代码的类UNIX操作系统，它的结构分为内核、Shell和应用程序三个层次。

1. 内核层

内核是Linux系统的核心部分，它负责管理系统各种硬件设备、文件系统、内存管理和进程管理等核心任务。Linux内核设计了良好的模块化结构，可以动态地加载和卸载内核模块，这使得内核可以兼容各种不同的硬件设备和外围设备。

2. Shell层

Shell是Linux系统的命令行解释器，它负责将用户输入的命令解释并执行。Linux系统上有多种Shell，其中最常用的是Bash Shell。Bash Shell 提供了各种丰富的功能和处理能力，如通配符、重定向、管道、变量等等。

3. 应用层

应用层是Linux系统上的各种应用程序和服务，包括文本编辑器、图形界面、Web服务器、邮件服务器、数据库服务器等。在Linux系统中，应用程序通常以开放源代码方式呈现，用户可以自由学习和使用，也可以根据需求自己编写、修改或扩展。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/21376908/1690870480456-7ade66a7-3979-4942-924e-b60806220df9.png#averageHue=%23eeeeed&clientId=u1a083e63-72b8-4&from=paste&height=370&id=qnFX0&originHeight=493&originWidth=681&originalType=binary&ratio=1&rotation=0&showTitle=false&size=16310&status=done&style=shadow&taskId=u22fe8c1f-059a-4a8b-bee1-dd28686e1cb&title=&width=511)

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=z65fg&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
# 都有哪些shell
Shell被翻译为“壳”。在Linux内核外面包了一个壳。
Shell是一种用于与操作系统进行交互的命令行解释器。它是一种脚本语言，可以通过编写一系列的命令和脚本来执行操作系统的功能和任务。
我们在终端中编写的命令都是Shell命令。例如：ls、grep......等。

在Linux系统中，常见的Shell包括以下几种：

1.  Bourne Shell（/bin/sh）：是Unix系统最早的shell程序，由史蒂夫·伯恩斯（Steve Bourne）编写。该shell程序是许多Linux发行版中默认使用的程序。 
2.  Bourne-Again SHell（/bin/bash）：是GNU项目的一部分，是Bourne Shell的增强版，目前在大部分Linux发行版中是默认的shell程序。 
3.  C Shell （/bin/csh）：是Bill Joy编写的一个具有面向对象设计理念的shell程序，它采用与C语言相似的语法和控制结构。C Shell中的命令提示符为%号。 
4.  TENEX C Shell（/bin/tcsh）：是C Shell的增强版，它对历史命令和别名等方面进行了改进，同时也支持C Shell中的所有特性。TENEX C Shell中的命令提示符也为%号。 
5.  Korn Shell（/bin/ksh）：是由David Korn编写的shell程序，它是Bourne Shell和C Shell的结合，拥有两种不同的工作模式：交互模式和批处理模式。 
6.  Z Shell（/bin/zsh）：是一个功能强大的shell程序，它是Bourne Shell的增强版，具有缩写、自动完成、句法高亮等功能，同时也支持Korn Shell、C Shell以及Bourne Shell的语法和命令。 

每种Shell都有其特定的语法和功能，但它们通常都具有共同的基本功能，如变量操作、条件语句、循环语句和命令执行等。

Shell脚本在自动化任务、系统管理、批处理作业等方面非常有用。开发人员和系统管理员可以使用Shell脚本来编写一系列的命令和逻辑，以便自动化执行常见的任务和操作。

在CentOS中，可以使用`chsh`命令切换默认Shell。`chsh`命令允许您更改用户的登录Shell。以下是切换Shell的步骤：

1. 查看已安装的Shell。
```shell
cat /etc/shells
```
该命令将打印出系统上已安装的Shell列表。

2. 使用以下命令更改Shell。
```shell
chsh -s /bin/zsh
```
在上面的命令中，`/bin/zsh`是要更改的Shell的路径。请将其替换为您想要使用的Shell路径。

3.  输入要更改Shell的用户密码以确认更改。 
4.  使用以下命令检查Shell是否已更改： 
```shell
echo $SHELL
```
此命令将打印新Shell的路径。
注意：只有具有Superuser权限的用户才能切换其他用户的Shell，您可能需要使用`sudo`或者以root用户身份执行上述命令。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=NQLeg&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
# shell的基础语法
## 注释
单行注释是一行文本，它以井号（#）开头，从该字符开始一直延伸到该行的结束。在此行内，任何内容都会被 Shell 解释器忽略：
```shell
# 这是一行注释
echo "Hello, World!"  # 这是一行带有注释的代码
```
Shell 脚本也支持多行注释，这种注释也被称为块注释。块注释在脚本中可以用于分组一系列代码，或将一段代码置于未被执行的状态。在 Bash Shell 中，块注释通常使用以下语法：
```shell
: '
这里是注释内容。
可以是多行，直到下面一行的单独引号为止。
'
```
下面是一个示例，说明这种块注释的使用方法：
```shell
#!/bin/bash

: '
这是一个多行注释块。
我们将在这里添加一些注释，并将一些代码放在这里，以防它们被执行。

echo "Hello, World!"
'

echo "这行是被执行的。"
```
在此示例中，我们使用了一个多行注释块，该块包含我们想要添加的注释和未被执行的代码。通过使用这种方式添加注释，我们可以让脚本更具可读性。

注意：第一行代码的作用是告知系统采用bash解释器执行以下代码。可以省略，如果省略会默认使用当前会话的shell解释器。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=G9CyF&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
## 变量
当我们在Linux操作系统中使用命令行环境时，Shell变量是非常重要的一个部分。它们可以用来存储和操作数据，从而让我们更加方便地管理文件、程序和系统。
在Linux中，有三种类型的Shell变量，包括：

1.  环境变量 
2.  本地变量 
3.  特殊变量 
### 环境变量 
环境变量是Linux操作系统中最常用的变量类型之一。它们是在Shell会话外设置的，可以由多个脚本和进程共享。在Linux中，环境变量没有固定的值，而是在需要时通过脚本或命令进行设置或更新。
要查看当前所有环境变量，可以运行以下命令：
```shell
printenv
```
或
```shell
env
```
要设置一个新的环境变量，请使用“export”命令，例如：
```shell
export MY_VAR="Hello World"
```
要使用环境变量，在变量名称前必须加上$符号，例如：
```shell
echo $MY_VAR
```

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=MFNei&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 本地变量
本地变量是一种临时变量，在Shell会话中设置和使用。与环境变量不同，本地变量仅限于当前Shell会话，不会被其他脚本或命令使用。设置本地变量可以使用“=”号操作符，例如：
```shell
MY_VAR="Hello World"
```
类似于环境变量，在使用本地变量时，变量名称前必须加上$符号。例如：
```shell
echo $MY_VAR
```

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=a1DrK&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 特殊变量
特殊变量是在Shell中预定义的变量名称，具有特殊的含义。这些变量与当前Shell会话有关，可以用于许多不同的用途，包括文件和目录操作、命令历史记录和处理脚本参数等等。
以下是常见的一些特殊变量：

- `$0`: 当前脚本的文件名
- `$1, $2...`: 脚本参数列表中的第1个、第2个参数等等  (例如：./first.sh abc def，在执行这个脚本时，第一个参数abc，第二个参数def。)
- `$#`: 脚本参数的数量
- `$*`: 所有脚本参数的列表
- `$@`: 所有脚本参数的列表（作为独立字符串）
- `$$`: 当前脚本的进程ID号
- `$?`: 上一个命令的退出状态，一个数值。

例如：
```shell
#!/bin/bash
echo "The name of this script is: $0"
echo "The first parameter is: $1"
echo "The total number of parameters is: $#"
echo "All parameters are: $@"
echo "The current process ID is: $$"
echo "The last exit status was: $?"
```
此脚本将输出有关脚本本身和参数的信息。
总结：
在Linux中，Shell变量是非常重要和有用的一部分。使用环境变量、本地变量和特殊变量可以让我们更加精确地控制和管理我们的Linux系统。掌握这些变量类型及其用法，可以让我们更加高效地编写Shell脚本并提高我们的日常工作效率。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=IRgO8&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
## 控制语句
Shell编程中，控制语句可用于控制程序的执行流程和执行次数，主要包括条件判断和循环。
条件判断主要通过if语句来实现，if语句用于检查指定条件是否成立，并根据条件的真或假来执行相应的语句块。
循环主要通过for循环、while循环和until循环来实现。循环语句用于反复执行某些语句块，直到满足指定条件为止。
### if语句详解
if语句用于检查指定条件是否成立，并根据条件判断的结果执行不同的语句块。if语法格式如下：
```shell
if condition 
then
  command1
  command2
  ...
elif condition2 
then
  command3
  command4
  ...
else
  command5
  command6
  ...
fi
```
其中，`condition`是要检查的条件，可以是命令、变量、表达式等任何Shell语法，`then`表示条件成立要执行的语句块，如果有多个条件，可以使用`elif`语句来添加多个条件，每个条件使用`then`分别表示要执行的语句块，如果所有条件都不成立，则执行`else`分支中的语句块。
if语句的执行过程是从上到下依次判断每个条件，如果条件成立，则执行对应的语句块，执行完后跳出if语句。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=iPKBA&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### for循环详解
for循环用于遍历指定列表或值的集合，并执行相应的语句块。for循环语法格式如下：
```shell
for var in list
do
  command1
  command2
  ...
done
```
其中，`var`是一个临时的变量名，用于存储当前循环的值，`list`是一个值或者多个带有空格或换行符分隔的值组成的列表。在每一次循环迭代时，`var`会被`list`列表中的一个值所替换，直到`list`中的所有值都被处理完为止。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=QV0KA&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### while循环详解
while循环用于不断执行语句块，直到满足指定条件为止。while循环语法格式如下：
```shell
while condition
do
  command1
  command2
  ...
done
```
其中，`condition`是要检查的条件，如果条件为真，则执行`do`语句块中的命令，执行完后再回到`while`语句中检查条件是否依然为真，如果条件仍为真，则继续执行命令块，否则跳出循环。

注意：在shell编程中 $((...)) 被称为算术扩展运算符，做数学运算的，并且将运算结果返回。$(...)运算符会将结果直接返回。例如：

- $((j+1))，如果j是5的话，结果就会返回6 （注意，使用这个运算符的时候，括号里面不能有空格）
- $(echo "hello world")，会将"hello world"打印，然后再将"hello world"字符串返回。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=RbrE0&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### until循环详解
until循环用于不断执行语句块，直到满足指定条件为止。和while循环不同的是，直到指定条件为假时才会停止循环。until循环语法格式如下：

```shell
until condition
do
  command1
  command2
  ...
done
```
其中，`condition`是要检查的条件，如果条件为假，则执行`do`语句块中的命令，执行完后再回到`until`语句中检查条件是否依然为假，如果条件仍为假，则继续执行命令块，否则跳出循环。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=RI6MT&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### break和continue语句
break和continue是控制循环的两个重要关键字。

- break语句用于跳出当前循环块，例如在for循环和while循环中使用该语句时，可以跳出当前循环并停止迭代。break语句可以嵌套在多重循环中，用于跳出内层循环和外层循环，其语法格式如下：

```shell
while condition
do
  command1
  command2
  if condition2 
	then
    break
  fi
  command3
  ...
done
```

在上述示例中，如果在执行`command2`时，条件`condition2`成立，那么会执行`break`语句，跳出循环块并停止迭代。

- continue语句用于跳过本次循环迭代，直接进入下一次的迭代。在for循环和while循环中使用该语句时，可以用于跳过本次迭代，执行下一次迭代。其语法格式如下：

```shell
while condition
do
  command1
  command2
  if condition2
  then
    continue
  fi
  command3
  ...
done
```

在上述示例中，如果在执行`command2`时，条件`condition2`成立，那么会执行`continue`语句，跳过本次迭代，直接进入下一次迭代。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=qUkmx&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### shell中的中括号：[ ]
中括号有两种使用方法：

1. 用于比较操作符：用于比较两个值的大小或者判断两个值是否相等。例如：
- `-eq`: 判断两个值是否相等（equal to），例如`[ $a -eq $b ]` 
- `-ne`: 判断两个值是否不相等（not equal to），例如`[ $a -ne $b ]`
- `-lt`: 判断左边的值是否小于右边的值（less than），例如`[ $a -lt $b ]`
- `-gt`: 判断左边的值是否大于右边的值（greater than），例如`[ $a -gt $b ]`
- `-le`: 判断左边的值是否小于等于右边的值（less than or equal to），例如`[ $a -le $b ]`
- `-ge`: 判断左边的值是否大于等于右边的值（greater than or equal to），例如`[ $a -ge $b ]`

在Shell中，比较操作符可以用于中括号[]中，例如:`[ $a -eq $b ]`。在比较时，要注意两个值之间必须有空格分隔，否则会出现语法错误。

2. 用于测试表达式：用于测试某个表达式是否成立。例如：
- `-f`: 判断某个文件是否存在并且是一个常规文件（regular file），例如`[ -f file.txt ]`
- `-d`: 判断某个文件是否存在并且是一个目录（directory），例如`[ -d dir ]`
- `-z`: 判断某个字符串是否为空（zero length），例如`[ -z "$str" ]`
- `-n`: 判断某个字符串是否非空（not zero length），例如`[ -n "$str" ]`
- `-e`: 判断某个文件或目录是否存在（exist），例如`[ -e file.txt ]`

在Shell中，测试表达式也可以用于中括号[]中，例如:`[ -f file.txt ]`。在多数Linux发行版中，测试表达式可以用中括号[]或者test命令实现，例如:`test -f file.txt`等价于`[ -f file.txt ]`。
需要注意的是，中括号中的空格很重要，空格缺少会导致语法错误。另外，在使用中括号[]时，要注意变量用双引号括起来，避免空值引起的语法错误。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=XnPUq&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 示例代码

下面是一个示例代码，演示了如何使用if、for、while、until以及break和continue语句：

```shell
#!/bin/bash

# 条件判断
if [ -f file.txt ] 
then
  echo "file.txt exists."
elif [ -d dir ] 
then
  echo "dir exists."
else
  echo "file.txt and dir not found."
fi

# for循环
for i in 1 2 3 4 5
do
  echo "The value of i is: $i"
done

# while循环
j=0
while [ $j -lt 5 ]
do
  echo "The value of j is: $j"
  j=$((j+1))
done

# until循环
k=0
until [ $k -ge 5 ]
do
  echo "The value of k is: $k"
  k=$((k+1))
done

# break语句和continue语句
for l in 1 2 3 4 5
do
  if [ $l -eq 3 ] 
	then
    continue
  fi
  echo "The value of l is: $l"
  if [ $l -eq 4 ] 
	then
    break
  fi
done
```

运行上述代码后，会输出如下结果：

```
file.txt and dir not found.
The value of i is: 1
The value of i is: 2
The value of i is: 3
The value of i is: 4
The value of i is: 5
The value of j is: 0
The value of j is: 1
The value of j is: 2
The value of j is: 3
The value of j is: 4
The value of k is: 0
The value of k is: 1
The value of k is: 2
The value of k is: 3
The value of k is: 4
The value of l is: 1
The value of l is: 2
The value of l is: 4
```

以上就是关于Shell中控制语句的详细讲解。希望能帮助你更好地理解Shell编程中的基本语法和控制流程。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=Vna07&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
## 函数
### 什么是函数？
在Shell编程中，函数是一种可重用的代码块，可以提高程序的可读性和可维护性。通过定义一个函数，可以把一段代码封装起来，赋予其一个名称，可以在程序的其他部分反复调用。
Shell脚本中的函数非常灵活，可以使用各种Shell语句和命令来编写，如if语句、for循环、while循环等。
### 定义函数
下面是一个示例函数的定义：

```shell
function say_hello() {
  echo "Hello, world!"
}
```

在上述示例中，我们定义了一个函数名为`say_hello`，它的执行结果是输出`Hello, world!`字符串。函数定义的语法格式如下：

```shell
function_name() {
    commands
}
```

使用function关键字来定义函数是可选的。当使用function关键字时，要注意不要加空格，否则会出现语法错误。函数体中可以包含任意数量的命令和语句。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=YeqVz&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 调用函数

成功定义一个函数后，可以在程序的任何地方调用它。只需要使用函数的名称，再加上一对小括号，即可调用函数。例如：

```shell
say_hello
```

上述示例将会调用函数`say_hello`，执行函数体中的命令，输出`Hello, world!`字符串。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=wvYb5&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 传递参数

Shell函数也支持传递参数。在调用函数时，可以把参数传递给函数，让函数使用这些参数来完成特定的任务。例如：

```shell
function greet() {
  echo "Hello, $1 $2"
}

greet "John" "Doe"
```

上述示例中，我们定义了一个名为`greet`的函数，它输入参数$1和$2，并把这些参数用于输出字符串`Hello, $1 $2`。我们调用`greet`函数，并把参数"John"和"Doe"传递给它，最终输出`Hello, John Doe`字符串。

在函数中，参数可以使用$1、$2、$3等占位符来引用。$1表示第一个参数，$2表示第二个参数，以此类推。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=tlfgn&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 示例代码

下面是一个示例代码，演示了如何定义和调用Shell中的函数：

```shell
#!/bin/sh

# 定义函数say_hello
say_hello() {
  echo "Hello, world!"
}

# 调用函数say_hello
say_hello

# 定义函数greet
greet() {
  echo "Hello, $1 $2"
}

# 调用函数greet
greet "John" "Doe"
```

运行上述代码后，会输出如下结果：

```
Hello, world!
Hello, John Doe
```
## 输出重定向
在Shell编程中，标准输出是指向终端的输出，而错误输出是指向标准输出的。重定向是一种将输出从一个点重定向到另一个点的方法。在Shell中，可以使用输出重定向来将程序的输出重定向到文件或者另一个命令中。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=gOncY&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 标准输出重定向

Shell中的标准输出重定向可以将程序的标准输出重定向到一个文件中。在命令中使用符号`>`和一个文件名，即可将输出重定向到指定的文件中。例如：

```
ls > files.txt
```

上述命令将会执行`ls`命令，并把输出重定向到文件`files.txt`中。如果`files.txt`文件不存在，则会创建该文件并写入输出内容。如果文件已经存在，则会覆盖原文件内容。

如果不想覆盖文件，可以使用`>>`符号来追加输出内容。例如：

```
ls >> files.txt
```

上述命令将会执行`ls`命令，并把输出追加到文件`files.txt`的末尾。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=pFVdf&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 错误输出重定向

与标准输出重定向类似，Shell中也提供了错误输出重定向的功能。可以使用符号`2>`和一个文件名来将错误输出重定向到指定文件中。例如：

```
ls wrong_dir 2> error.txt
```

上述命令将会执行`ls`命令，并把错误输出重定向到文件`error.txt`中。

与标准输出类似，在错误输出中使用`>>`符号可以追加错误输出。例如：

```
ls wrong_dir 2>> error.txt
```

上述命令将会执行`ls`命令，并把错误输出内容追加到文件`error.txt`的末尾。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=NL9Da&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 输出复制与分割

在Shell中，可以使用符号`|`将一个命令的输出作为另一个命令的输入。这种语法被称为管道。管道可以将一个命令的输出传递给另一个命令，使得多个命令可以协作运行。

例如：

```
ls | grep file | wc -l
```

上述命令将会执行`ls`命令并将输出传递给`grep`命令，将其中包含字符串`file`的行筛选出来，最后将输出传递给`wc -l`命令，计算有多少行输出。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=WmGnX&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 示例代码

下面是一个示例代码，演示了Shell中输出重定向的各种用法：

```shell
#!/bin/sh

# 标准输出重定向
ls > file_list.txt
# 错误输出重定向
ls wrong_dir 2> error.txt
# 输出复制与分割
ls | grep file | wc -l
```

运行上述代码后，会在当前目录下生成名为`file_list.txt`和`error.txt`的两个文件，并统计`ls`命令输出包含字符串`file`的行数。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=I7IrI&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
## 输入重定向
在Shell编程中，输入重定向是一种将指定的输入文件或者另一个命令的输出作为当前程序的输入流的方法。在Shell中，可以使用输入重定向来简化数据输入操作，以便更方便地进行编程。
### 输入重定向符号

在Shell中，输入重定向使用符号`<`和文件名来指定输入源。例如：

```
sort < file.txt
```

上述命令将会使用`sort`命令来对文件`file.txt`进行排序，其中`<`符号表示将`file.txt`文件作为输入流传递给`sort`命令。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=WNF5H&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
### 示例代码

下面是一个示例代码，演示了Shell中输入重定向的各种用法：

```shell
#!/bin/sh

# 从文件中读取输入
sort < input.txt > output.txt

# 组合使用输入重定向和管道
# sed 's/foo/bar/' 作用是：将内容中的所有foo替换成bar
cat file1.txt | sed 's/foo/bar/' | sort > result.txt
```

运行上述代码后，会在当前目录下生成名为`output.txt`和`result.txt`的文件。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=ZEK4q&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
# 实现数据库自动备份
下面是一个用Shell编写的自动备份数据库的脚本，可以根据实际情况进行修改和调整：

```shell
#!/bin/bash

# 设置备份目录
backupDir="/home/backup"

# 设置需要备份的数据库名称和用户名、密码
dbUser="root"
dbPass="123456"
dbName="database_name"

# 设置备份文件名，包括日期和时间
backupFile="$backupDir/${dbName}_$(date +%Y%m%d_%H%M%S).sql"

# 执行备份命令
mysqldump -u$dbUser -p$dbPass $dbName > $backupFile

# 压缩备份文件
gzip -f $backupFile

# 删除7天前的备份文件
find $backupDir -mtime +7 -type f -name "${dbName}*.sql.gz" -delete
```

脚本的实现流程如下：

1.  首先设置备份目录和要备份的数据库的用户名、密码和名称。 
2.  然后，设置备份文件名，这里使用了当前日期和时间来命名备份文件。 
3.  接下来，使用mysqldump命令备份数据库并将结果重定向到备份文件中。 
4.  备份完成后，使用gzip命令将备份文件压缩。 
5.  最后，使用find命令删除7天前的备份文件，以避免占用过多磁盘空间。 

将这个脚本保存到一个文件中，如backup_db.sh，并添加可执行权限：

```shell
chmod +x backup_db.sh
```

通过编辑crontab文件，可以将这个脚本设置为定期自动运行，比如每天执行一次备份。打开终端，输入：
```shell
crontab -e
```

在文件末尾添加下面的一行：

```
0 0 * * * /path/to/backup_db.sh

#  20 12 * * * /path/to/backup_db.sh 表示每天12:20会执行一次脚本。
```

保存并退出crontab文件，这样在每天午夜0点进行自动备份。如果需要调整自动备份的时间，也可以修改crontab文件中的时间字段。

![标头.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/21376908/1692002570088-3338946f-42b3-4174-8910-7e749c31e950.jpeg#averageHue=%23f9f8f8&clientId=uc5a67c34-8a0d-4&from=paste&height=78&id=YcEX4&originHeight=78&originWidth=1400&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23158&status=done&style=shadow&taskId=u98709943-fd0b-4e51-821c-a3fc0aef219&title=&width=1400)
