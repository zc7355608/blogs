# Less

------

> Less是一个CSS预处理器，文件后缀是`.less`。它扩充了CSS语言，使CSS具备逻辑性、计算能力。（注意：浏览器不能识别Less代码，目前需要将Less源码通过预处理成为CSS代码，浏览器必须引入CSS代码）
>
> 目前阶段，我们用vscode的EasyLESS插件来做less代码的预处理。（只要less代码一保存，当前目录中就会自动生成css文件）

- **注释**：less中的注释除了`/**/`，还可以出现`单行注释//`，单行注释是less的注释，不会在css文件中生成。

- **less运算**：`+`加、`-`减、`*`乘，可以直接做运算（第2个数后面记得加单位），而除法要么用小括号`()`括起来，要么用`./`，如：

  ```less
  .container {
  	height: 100 + 20px;
  	height: 100 * 2rem;
  	height: 100 ./ 50px;
  	height: (100 / 50px);//推荐用小括号做除法
      //2个数都加了单位，以第1个数单位为准
      height: (100px / 50rem);
  }
  ```

- **less嵌套**：目的是快速生成后代选择器。如：

  ```less
  .father {
  	color: red;
  	.son {
  		font-size: 17px;
  	}
  }
  ```

  > 就相当于：

  ```css
  .father {
  	color: red;
  }
  .father .son {
  	font-size: 17px;
  }
  ```

  > &相当于当前选择器：

  ```less
  .father {
  	color: red;
  	&:hover {}
  }
  //上面的&:hovver相当于：（不会生成后代）
  .father:hover {}
  ```

- **变量**：它就是一个装数据的容器，方便后面多次引用。使用方式：

  1. 先定义：`@变量名: 值;`
  2. 使用：`属性: @变量名;`

- **导入与导出**：

  - 导入其他less文件：语法`@import "文件路径";`（如果是less文件，可以省略文件后缀`.less`）

  - 导出当前的less文件：我们也可以通过less的导出语法，来控制生成的css文件的路径和文件名。在less文件**第一行**添加：

    `// out: url`，如：`// out: ./mycss/`，或者：`// out: ./mycss/a.css`

  - 禁止导出：less文件**第一行**添加：`// out: false`，此时保存并不会生成css文件。

------

