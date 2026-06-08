# jQuery

> - 什么是jQuery，顾名思义就是JavaScript和查询（Query）。它是一个用于辅助开发的JS库，核心思想是：write less,do more
> - jQuery还有一个作用就是它能兼容市面上主流的浏览器如：ie、firefox、chrome，并且插件很全，出错后还有健全的错误提示信息。并且它是免费开源的，语法也比原生js简单很多。
> - 要使用直接在官网下载JS文件引入到HTML中即可。有两个版本，一个是带min的版本，去掉了多余的空格换行和注释，运行效率高，项目完成上线时用；另一个不带min的是开发版，里面代码方便开发者阅读。
>
> （注意：jqeury2.0版本之后不再支持IE6\7\8，因为目前IE浏览器都不用了，所以如果IE端开发需要用jquery2.0之前的版本。目前仍在维护的新版本是3.x，2.x不支持ie8目前只修复bug不再维护，1.x支持ie8同样不再维护，开发环境中我们一般用1.x）

- #### jquery的第一个程序：

  1. 首先在HTML中引入jquery的js文件：

     ```html
     <script src="./jq/jquery-3.7.1/jquery-3.7.1.min.js"></script>
     ```

  2. 然后在<script>标签中使用jquery的函数：

     ```js
     $(function(){
         alert("hello js")
     })
     ```

     > 以上代码的说明：
     > 	jquery中有一个核心函数叫“jQuery”（可以被简化为一个“$”符），调用该函数可以传进去一个匿名函数，表示当页面加载完毕就执行该函数，相当于`window.onload=function(){}`

- #### 关于jQuery对象：

  - `$(document)`：`jQuery()`函数中可以传一个dom对象，作用是将JS中的dom对象转成jq中的“jQuery对象”，我们要使用jq中的属性和方法必须保证该对象是jQuery对象，而不是普通js的dom对象。**二者的属性和方法不能混合使用**。（通常为了将js的dom对象和jq对象区分开，我们会将jq对象的变量名开头加“$”；如`$p`表示该变量指向的是一个jq对象）
  - jq对象怎么转成dom对象：“jQuery对象”本质上是dom伪数组，数组中的每个元素都是一个js的dom，所以jq对象转成js对象可以通过数组下标的方式：`$jqObj[0]`，或者调用jq对象的get方法：`$jq.get(0)`，`$jq.eq(0)`是取dom数组中下标为0的dom对象。

- #### jQuery中的事件监听：

  > - 可以通过`addEventListener()`函数来给dom对象绑定一些事件，这是JS的事件监听。而JQuery中将事件监听封装成了不同的函数，语法：`$dom.事件名(function(){})`，如果jq对象对应的dom对象有多个，那么会全部注册上该函数。
  > - jquery中另一种事件监听是通过`$dom.on('事件名', function(){})`函数，这种方式可以一次绑定多个事件，多个的话事件名称用空格隔开，推荐用这种。
  > - 事件解绑用：`$dom.off('事件名')`，如果没有参数就全部解绑
  >
  > （事件监听函数的方式返回值都还是原来的jq对象，方便我们做链式操作）
  >
  > 实际上面第一个程序的完整写法是：
  >
  > ```js
  > //当页面的dom元素加载完毕后执行ready()里的函数，以上只是简写形式
  > $(document).ready(function(){
  >     alert("hello js")
  > })
  > ```

- #### 关于jQuery中的选择器：

  > - 我们可以通过，给`$('选择器')`函数中传进去一个**CSS的选择器字符串**，直接选中HTML中的dom将其转成jq对象。
  >
  > - jquery中的选择器完全兼容CSS选择器，并且还添加了一些更灵活的语法。它会从上到下，顺序地将所有满足条件的dom对象都封装成jq对象（dom数组）

- #### 关于jQuery中给选择器加过滤：

  > 有时我们不希望将选中的所有dom对象都封装成jq对象，只封装满足条件的那几个dom，此时可以给选择器再加过滤。语法：

  - `$("选择器:first")`：只获取第一个dom对象
  - `$("选择器:last")`： 只获取最后一个dom对象
  - `$("选择器:eq(i)")`：只获取数组下标为i的dom对象
  - `$("选择器:lt(i)")`：获取数组下标小于i的dom对象，不包含i
  - `$("选择器:gt(i)")`：获取数组下标大于i的dom对象，不包含i

- #### jQuery对象的常用实例函数：

  - `css()`：获取和设置dom元素的样式。设置：`$obj.css('样式名','值')`，还可以通过对象参数来设置多个样式：`$obj.css({'样式名':'值','样式名':'值'..})`。获取样式：`$obj.css("color")`
  - `addClass()`：添加类名。`removeClass()`删除类名。`toggleClass()`切换类名
  - `val()`：读取dom数组中第一个dom对象的value属性。传值表示将数组中所有的dom对象的value属性值全部设置为该值。
  - `text()`：相当于`innerText`，`html()`相当于`innerHTML`，传参表示设置所有dom对象的`innerText`和`innerHTML`
  - `attr('属性名')`：读取dom数组中第一个dom对象对应的属性值。如果给第2个参数表示给所有的dom对象的该属性赋值。
  - `removeAttr("属性名")`：删除所有dom的对应属性
  - `prop('属性名')`：获取第一个dom对象的布尔属性，返回布尔值。如果给第2个参数true表示给所有dom对象的该属性设置为true
  - `remove()`：删除所有dom对象，以及其子对象
  - `empty()`：将该dom对象置空，就是标签置空
  - `append(ele)`：为所有dom对象“追加”子元素。参数也支持字符串或其他类型。`prepend(ele)`是头部追加，`before(ele)`是指定追加到某个dom元素前面，`after(ele)`是指定追加到后面
  - `each(function(index,element){})`：遍历jquery对象里的所有dom。它和下面的静态函数`$.each()`是类似的。

- #### jQuery构造器的静态函数：

  > - `$.each(jq对象, function(index, element){})`：该函数可以对JS数组（包括jq对象）、JS对象（包括JSON对象）进行循环遍历，每次循环都会调用一次处理函数。如果是JS数组：第1个参数是数组的下标，第2个参数数组元素；如果是JS对象：第1个参数是对象的key，第2个参数是对象的value

- #### jQuery对象的原型上增加实例成员：

  ```js
  $.fn.extend({
      //属性和方法
      doSome: function(){},
      gender: '女'
  })
  //如果就扩展一个属性，可以直接：$.fn.属性名 = 值
  ```

- jQuery构造器上增加静态成员：

  ```js
  $.extend ({
  	//属性和方法
  	doAdd: function(){},
  	country: 'China'
   })
  //如果就扩展一个属性，可以直接：$.属性名=值
  ```

------

### 表单校验插件validate的使用：

> 它是一个基于jquery的插件，官网：https://jqueryvalidation.org，使用：

```js
//页面加载完毕再进行校验
$(function(){
    //首先要选中要校验的form表单，然后调用validate方法，参数是一个js对象
    $('form').validate({
        //rules属性用于定义校验规则
        rules: {
            input标签的name属性值: "规则",
            input标签的name属性值: {	//如果规则有多个或规则有具体的值，就要用对象
                规则1: 值,
                规则2: 值,
                ..
            },
            ...
        }

        //messages属性定义校验失败时的提示信息
        messages: {
            input标签的name属性值: "提示信息",
            ...
        }
    })
})
```

> 还可以自定义规则：
>
> 在调用`validate`方法之前，通过：`$.validator.addMethod('规则名称',function(value,element,params){ return true/false })`函数来增加规则，其中`value`是输入框用户输入的内容，`element`就是input元素dom对象，`params`就是给规则设置的值；记得在函数中返回布尔类型值

------

