# 浏览器端JS的webAPI（TODO）

------

## DOM

> - DOM用于操作HTML元素，我们要想让页面发生变化，就得用DOM来做。DOM是将HTML文档以树状结构在JS中呈现出来，每个html标签都在`document`对象（文档树对象）中可以获取到对应的dom对象，dom树直观的体现了标签之间的关系。dom树上就是一个个的dom对象，浏览器会根据html文档中的一个个标签声明一个个的dom对象（js对象），所有的标签属性都可以在对应的dom对象中找到对应的属性，我们只要获取这个对象，修改dom对象的属性，它就会自动映射到html标签上去。
> - 浏览器中内置的`document`对象就是整个html文档中DOM模块的根对象，里面有很多属性和方法供使用，它就是整个的dom树。JS的webAPI就是通过这种方式完成的和HTML页面的动态交互的。
> - 而在HTML文档（`document`对象）中，最大的根元素是`<html>`，对应的dom对象是`document.documentElement`，它是文档树的根dom，包含了整个HTML文档的结构。
>
> **比如：**html的div标签是不是有style属性，style中可以写css样式，我们通过`document`对象来获取到div对应的dom对象，修改dom对象的style属性值就是改它的css样式；而且这种通过js直接设置标签的style属性，相当于行内样式，优先级最高

- ### 获取文档树中的dom对象：

  1. css选择器方式：（最新的，重点掌握）

     ```js
     //通过css的选择器，获取document对象中，匹配到的第一个dom对象
     const domObj = document.querySelector('css选择器')
     // 参数中包含一个或多个css选择器的字符串
     const domArr = document.querySelectorAll('css选择器')//可以获取匹配到的多个dom对象，返回dom伪数组
     ```

  2. 其他获取dom的方式：（了解，太老了）

     ```js
     const domObj = document.getElementById('id名')
     //下面俩返回的也都是dom伪数组
     const domObj = document.getElementsByClassName('类名')
     const domObj = document.getElementsByTagName('标签名')
     ```

- ### 获取后，我们就可以通过dom来设置html中的标签了：

  - 每个dom对象都有`innerText`和`innerHTML`属性，可以获取和修改标签内部的字符串。区别是一个可以识别html语法一个不可以。

    > 其实之所以innerText不能识别html语法，是因为innerText中的文本浏览器会进行转义，将`<script>`转为`&lt;script&gt;`，所以页面看到的是普通文本。而innerHTML则不做任何处理，因此使用innerHTML要小心*XXS攻击*。

  - HTML标签里的所有属性，对应的dom对象中都有对应的属性存在，要改直接用`dom.属性名=值`的方式去改就行了，也可以用dom对象的`setAttribute('属性名','值')`方法来给标签的属性赋值。查询属性值可以`dom.属性名`，也可以用`dom.getAttribute('属性名')`来查询属性值。如果标签的属性是布尔属性，如`disabled`，可以这样设置该属性：`dom.属性名=true/false;`

  - 可以通过`dom`对象的属性和方法直接操作元素的样式：
    1. 直接通过`obj.style.css属性="值";`，如：`obj.style.backgroundColor = 'red'`（注意css属性名要要修改成驼峰命名方式的写法，属性值要用双引号括起来的字符串格式）
    2. 给dom设置类名是：`obj.className = '类名'`，因为`class`是关键字，所以设置class属性用`className`。但是这种方式可能会覆盖原来的类名使用不方便，所以H5新增了一种`classList`属性的方式追加和删除类名，语法：`obj.classList.add('类名')`，`remove('')`、`toggle('')`，分别是添加、删除和切换类名（切换：有就删掉，没有加上），还有`contains('类名')`判断是否包含某个类名。

- ### JS操作自定义属性：

  > H5新增了“data-”这个全局属性，就是为了方便我们拿标签中自己放的私有数据，在JS中统一通过dom对象的`dataset`属性去获取自定义属性。`dataset`是一个对象，标签中的`data-xxx`属性都在该对象中，对象的属性名就是“data-”后面的名字。

- ### 事件函数：

  > 可以通过调用dom对象上的函数来自动触发事件，不需要用户手动点击。如：button按钮对象上的`click()`可以触发“点击按钮”事件，form表单对象的`submit()`和`reset()`方法可以触发表单“提交”和“重置表单”事件，text类型input对象的`blur()、focus()、select()`方法可以触发“光标丢失”、“光标定位”、“文本选中”等事件。

- ### dom对象常用属性：

  > - `tagName`：dom元素对应的标签名，全部大写。
  > - `clientWidth`：获取html元素自身可见部分的宽度（只包含padding和content，不包含border、margin、滚动条等，），值为数字，单位px。
  > - `clientHeight`：获取html元素自身可见部分的高度。
  > - `offsetWidth`：获取html元素自身可见部分再加border的宽度，值为数字，单位px。
  > - `offsetHeight`：获取html元素自身可见部分再加border的高度，值为数字，单位px。
  > - `offsetLeft`：获取元素距离自己的“定位祖先元素”左边框的距离，它获取的是元素的位置（只读）。
  > - `offsetTop`：获取元素距离自己的“定位祖先元素”上边框的距离（只读）。

- ### FormData对象的使用：

  > `FormData`（表单数据对象）可以传进去`form`对象，快速获取表单数据（键值对形式），以便用`XMLHttpRequest`来发送表单数据。也可手动往该对象中添加数据，从而独立于表单使用。该对象中的key只能是`String`型，而value可以是`File、Blob、String`型。如果表单`enctype`为`multipart/form-data`，则会使用表单的`submit()`方法来发送数据。它的常用实例方法：
  >
  > - `new FormData()`：创建一个空的表单数据对象
  > - `new FormData(formDom)`：通过传进去form对象，来创建一个带数据的`FormData`对象
  > - `get('k',v)/getAll('k')`：通过字符串key来获取表单数据
  > - `set('k',v)`：设置表单数据
  > - `append('k',v)`：追加表单数据

- ### URLSearchParams对象的使用：

  > `URLSearchParams`可以帮我们将一个对象转成查询字符串（不带?），使用：
  >
  > ```js
  > const paramObj = new URLSearchParams({ name: 'zs' })
  > const queryString = paramObj.toString()//生成查询字符串：key=value&key=value...
  > ```

------

### 定时器：

> 浏览器的webAPI中给我们提供了2种定时器函数（全局函数）：`setInterval()`和`setTimeout()`
>
> - 间歇函数：`setInterval(函数, 间隔时间)`，会每隔一段时间就调用一次函数。其中函数参数可以是匿名函数，也可以是一个函数名（不带()小括号）。间隔事件是数值型，单位ms。看例子：
>
>   ```html
>   <script>
>       window.onload = function(){
>           displayTime = function(){
>               document.getElementById("div").innerText = new Date().toLocaleString()
>           }
>   
>           const btn1 = document.querySelector('#btn1')
>           const btn2 = document.querySelector('#btn2')
>           let num
>   
>           btn1.onclick = function(){
>               num = setInterval(displayTime, 1000)
>               console.log(num)
>               btn1.disabled = true
>               btn2.disabled = false
>           }
>   
>           btn2.onclick = function(){
>               console.log(num)
>               clearInterval(num)
>               btn1.disabled = false
>               btn2.disabled = true
>           }
>       }
>   </script>
>   <div style="color: pink; font-size: 20px;" id="div"></div>
>   <input type="button" value="显示网页时钟" id="btn1"/>
>   <input type="button" value="停止时钟" id="btn2" disabled/>
>   <button>点我</button>
>   ```
>
> - 延时函数：`setTimeout(回调函数, 等待的毫秒数)`，在页面打开加载完毕后，等待毫秒数结束后，会调用一次回调函数。如：
>
>   ```html
>   <img src="../../images/ad.png" alt="广告">
>   <script>
>   	//5秒自动关闭广告
>   	setTimeout(function(){
>   		document.body.removeChild(document.querySelector('img'))
>   	}, 5000)
>   </script>
>   ```
>
>   （**注意**：关闭延时函数的`clearTimeout()`方法不能写在延时函数`setTimeout()`中，如果必须在延时函数中关闭延时器，就令`timerId=null`）

> **关闭定时器**：定时器函数都有一个返回值，是该定时器的计数id（Number型），每次执行都会变。关闭定时器调用`clearInterval(id)`/`clearTimeout(id)`函数，将定时器的id作为参数传进去，定时器就关闭了。

------

### 事件监听/事件注册：

> 什么是事件？当HTML的按钮元素被点击，就表示按钮元素上发生了点击事件。而事件监听就是，监听一个元素，当该元素上发生了某个事件，就执行某个函数（回调函数）或某行代码，这就是事件监听，也叫在元素上注册事件。

- 事件监听的语法：`dom.addEventListener('事件名', 回调函数名[,是否开启捕获])`，可以多次调用来给一个事件绑定多个函数。

- 早期的事件监听是这样写的：`obj.onclick = function(e){}`，点击后调用该匿名函数。这种方式不支持同时绑定多个函数，会覆盖，而且不支持事件捕获（后面再说），并且优先级没有`addEventListener()`函数的优先级高。所以这种方式现在很少用了。

- 在事件绑定的回调函数中，函数的第1个参数会传进去一个“事件对象”，通常形参名用`event`或e来接收。事件对象就是一个对象，它内部有事件触发时的相关信息，例如：鼠标点击事件对象中，就有鼠标的位置信息。部分事件对象的属性：

  > `target`：事件发生在哪个标签上
  > `value`：获取当前的事件类型
  > `pageX/pageY`：获取鼠标在相对于当前文档页面中的坐标，通常是鼠标移动事件有的；
  > `clientX/clientY`：获取光标相对于视口左上角的位置;
  > `offsetX/offsetY`：获取光标相对于当前dom元素左上角的位置
  > `key`：获取键盘的键名字符串（以前是keyCode键值，已弃用），键值用`code`属性，但某些浏览器可能还没有实现

- 事件解绑：如果是`onclick=function(){}`这种，就直接`onclick=null`进行解绑（函数也是对象，赋值null覆盖掉就行了）。如果是`addEventListener()`的方式，必须用`btn.removeEventListener('事件名',fn)`，但如果注册的是匿名函数，就不能被解绑了。

- **事件流**：指事件完整执行过程中的流动路径。事件流共2个方向：捕获和冒泡。当一个事件发生时，它默认是从最内层的元素上依次流向最外层的元素上，这就是事件的冒泡。从父到子是捕获，从子到父是冒泡。

- **事件捕获**：事件流的执行从外到里就是捕获，默认没有开启，如果要开启需要在事件监听函数的第3个参数传true，表示在该元素上发生的事件流改成捕获，如：`addEventListener('事件',fun, true)`，这样该事件的流动方向就是从父元素到子元素，事件经过该元素会被捕获，等处理完成后才流向内部其他元素上。默认是`false`冒泡的。（注意：在`dom.onclick=function(){}`这种老的事件监听方式上是没有事件捕获的）

- **阻止事件传播**：当一个元素触发某个事件后，该事件会从小到大流动到最外层的元素上。如果想把事件就限制在当前元素内，就要阻止事件传播。用法：`事件对象.stopPropagation()`，就可以阻止事件的传播，冒泡和捕获都会阻止。

  > 事件委托：它是利用事件流的特性（冒泡）来解决一些开发需求的小技巧。优点是可以减少注册次数，提高程序性能。例如：给ul中的所有li注册点击事件，可以给ur元素注册事件而不是每个li注册。当ul中的li被点击，该事件会冒泡到ul上，ul去调用对应的函数，（注意：事件委托只适合在大盒子里，只有几个同类型的直接子标签，大盒子比较干净这种。如果子标签内还嵌套其他标签，就不适合了）

- **阻止事件的默认行为**：有时我们需要阻止事件的默认行为。如点击表单的提交按钮，表单的提交按钮会对点击事件进行监测，点击后会进行表单数据的提交。我们希望点击提交按钮后不要提交，此时可以通过事件对象的函数：`e.preventDefault()`来阻止该（点击）事件的默认行为（提交），就像没点按钮一样什么也不做。

------

### JS操作HTML文档结构：

> 我们可以通过dom元素对HTML元素进行增删，动态控制HTML文档结构。

- 查找附近节点：

  > dom对象有一些属性，可以获取附近元素的dom对象：

  - `dom.parentNode`：返回父节点dom对象，找不到则返回null
  - `dom.childNodes`：获取所有子节点dom，包括文本节点和注释信息等，返回伪数组
  - `dom.children`：仅获得所有的标签子节点，返回伪数组
  - `dom.previousElementSibling`：获取上一个兄弟节点dom
  - `dom.nextElementSibling`：获取下一个兄弟节点dom
  - `dom.nextSibling`：获取下一个兄弟节点dom（算上文本节点）

- 新增节点：

  > 1. 先通过`document`对象的方法创建一个新节点：`document.createElement('div')`，
  > 2. 再将新节点放在dom树上：`父元素dom.appendChild(newDom)`，将该dom追加到父元素的最后一个子元素后面。或者插入到某个子元素前面：`父元素dom.insertBefore(newDom, 子dom)`

- 新增文本节点：`document.createTextNode('这是一个文本节点')`

- 删除节点：

  > `父元素dom.removeChild(子dom)`，该元素在html源码中消失。

- 克隆节点：

  > `被克隆的dom.cloneNode(布尔值)`，复制一个和此dom相同的节点，参数true表示深克隆，“子孙节点”也复制。

------

## BOM

- ### 什么是BOM？

  > BOM（Browser Object Model）是浏览器对象模型。BOM模块的顶级根对象是`window`代表了整个浏览器，而浏览器内部的一个网页的`document`对象仅代表改页面，属于DOM。BOM的顶级对象是`window`，DOM的顶级对象是`document`，BOM包含DOM，因此`window`对象中有`document`对象。

  > `window`对象是浏览器环境下JS的全局对象，是JS的顶级对象。所有直接定义在<script>标签块中的全局变量和全局函数，都是`window`对象的属性和方法。`window`对象中的常用属性有：`navigator`、`location`、`document`、`history`、`screen`..

- ### JS的事件循环机制：

  > ​	浏览器有两个引擎，一个是渲染引擎是渲染html和css的，另一个是js解释器（谷歌是v8引擎）。
  >
  > ​	JS语言的一大特点就是**单线程**，同一时间只做一件事，但是同样也有不好的地方：如果js代码加载或执行时间过长，会造成页面渲染加载阻塞的感觉。所以为了解决这个问题，H5允许JS用脚本创建多个线程（如AJAX技术），于是JS中出现了同步和异步。JS的异步一般是通过“回调函数”来实现的（后面还有其他手段）。
  >
  > ​	一般同步任务都在主线程上执行，形成一个执行线，异步任务（事件、资源加载、定时器、ajax）相关的由浏览器分配异步任务进程单独去处理，当异步任务执行完毕后就推到**任务队列/消息队列**中。当主线程中同步任务执行完，解释器再去消息队列中找符合执行条件的、完成的待处理的异步任务，将其放到主线程中执行，依次循环。由于主线程是不断的获得任务、执行、再获得再执行，所以这种机制称为**事件循环**（event loop）机制。

- ### window对象中的常用属性：

  - `location`：该对象拆分并保存了当前页面URL地址的每个组成部分。它常用的属性和方法：

    > 属性：
    >
    > - `href`：获取完整的URL字符串，对其赋值可以完成页面跳转。
    > - `search`：获取get请求URL中的查询字符串，就是?以及后面的name=value部分
    > - `hash`：获取URL中的哈希值，就是#以及后面部分（如：#/xx）
    >
    > 方法：
    >
    > - `reload(true/false)`：刷新当前页面，类似f5，true表示强制刷新不走页面缓存，类似Ctrl + f5

  - `navigator`：记录了浏览器的相关信息。常用属性：`userAgent`，可以获取浏览器的版本、平台等相关信息，字符串型。常用来判断用户目前的设备，根据设备跳转到移动端页面：

    ```js
    (function(){
        const userAgent = navigator.userAgent;
        // 验证是否为Android或iPhone
        const android = userAgent.match(/(Android);?[\s\/]+([\d.]+)?/)
        const iphone = userAgent.match(/(iPhone\sOS)\s([\d_]+)/)
        // 如果是Android或iPhone，则跳转至移动端站点
        if (android || iphone){
            location.href = '移动端站点'
        }
    })();
    ```

  - `history`：它主要管理历史记录，该对象与浏览器地址栏的操作相对应，如前进、后退、历史记录等。常用方法：

    > - back()：相当于点了下后退按钮
    > - forward()：点了下前进按钮
    > - go(参数)：参数1表示前进一下，-1表示后退一下

  - **本地存储**：随着页面数据越来越多，为了满足各种需求，经常会在用户本地浏览器上存储一些数据，所以H5提出了本地存储，允许JS通过浏览器提供的webAPI，来将一些数据存储到浏览器本地。可以在浏览器的开发者工具中，通过Application面板的Local Storage来查看存的数据。本地存储的特性：

    > 1. 数据存储在本地浏览器硬盘上，设置、读取方便，刷新也不丢失数据。
    > 2. 容量较大，`localStorage`和`sessionStorage`对象每个容量约5M左右
    
    ###### 本地存储的API：
    
    > - `localStorage`：它是本地存储对象，存储的数据都是字符串。浏览器关闭也在。
    >  - 存：`localStorage.setItem(key, value)`，k,v数据都是以**键值对字符串**形式存储在本地浏览器中。这样同一个网站的数据就可以多个页面共享了。
    >   - 取：`localStorage.getItem(key)`，获取本地键对应的值数据（串）。
    >   - 删：`localStorage.removeItem(key)`，删除键对应的键值对数据。这些方法参数都是String型的。
    >   - 清空所有的本地存储：`localStorage.clear()`。
    >
    > - `sessionStorage`：它是会话存储，关闭浏览器存的数据就消失，用法和以上基本相同也是键值对字符串。
    
    ###### 注意：localStorage、sessionStorage都是同源才共享的。并且不同页面之间的sessionStorage是互相独立的，而且在页面跳转时会将当前页的sessionStorage拷贝一份携带过去，手动打开的标签页没有sessionStorage。
    
    > 但是localStorage并不是所有浏览器都支持，所以我们也可以用第3方库`store`来做。

------

### 介绍一个开源的轮播图插件：swiper

> 插件就是别人写好的JS代码。swiper是一个很流行的轮播图插件，直接官网下载即可。
>
> 下载到本地，将js和css文件复制到工作区，然后到demo文件夹找自己想要的网页，将style块、页内js代码复制来即可。想要自定义效果就去看官网的API文档，按文档添加代码即可。

------

### 介绍一个开源的收集表单数据的插件：form-serialize

> 下载对应的js文件引入后，直接用它的`serialize(form, {})`函数即可。第1个参数是表单的dom对象，第2个参数是配置对象，一般都这样写：`{ hash: true, empty: true }`，此时该方法的返回值是一个JS对象，对象的属性名是表单的name属性值。hash是设置数据的结构的，如果设置为false，此时返回的是查询字符串（不带?），empty如果为false，此时会忽略空值的元素

------

### 介绍一个开源的富文本编辑器插件：wangEditor

> 1. 首先引入对应的css和js文件

------

