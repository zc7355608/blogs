- ## `Element` 节点(to learn)

  `Element`节点对象对应网页的 HTML 元素。每一个 HTML 元素，在 DOM 树上都会转化成一个`Element`节点对象（以下简称元素节点）。
  
  元素节点的`nodeType`属性都是`1`。
  
  ```js
  var p = document.querySelector('p');
  p.nodeName // "P"
  p.nodeType // 1
  ```
  
  `Element`对象继承了`Node`接口，因此`Node`的属性和方法在`Element`对象都存在。
  
  此外，不同的 HTML 元素对应的元素节点是不一样的，浏览器使用不同的构造函数，生成不同的元素节点，比如`<a>`元素的构造函数是`HTMLAnchorElement()`，`<button>`是`HTMLButtonElement()`。因此，元素节点不是一种对象，而是许多种对象，这些对象除了继承`Element`对象的属性和方法，还有各自独有的属性和方法。
  
  - #### 实例属性
  
    - ##### 元素特性的相关属性：
  
      **（1）Element.id**
  
      `Element.id`属性返回指定元素的`id`属性，该属性可读写。
  
      ```js
      // HTML 代码为 <p id="foo">
      var p = document.querySelector('p');
      p.id // "foo"
      ```
  
      注意，`id`属性的值是大小写敏感，即浏览器能正确识别`<p id="foo">`和`<p id="FOO">`这两个元素的`id`属性，但是最好不要这样命名。
  
      **（2）Element.tagName**
  
      `Element.tagName`属性返回指定元素的大写标签名，与`nodeName`属性的值相等。
  
      ```js
      // HTML代码为
      // <span id="myspan">Hello</span>
      var span = document.getElementById('myspan');
      span.id // "myspan"
      span.tagName // "SPAN"
      ```
  
      **（3）Element.dir**
  
      `Element.dir`属性用于读写当前元素的文字方向，可能是从左到右（`"ltr"`），也可能是从右到左（`"rtl"`）。
  
      **（4）Element.accessKey**
  
      `Element.accessKey`属性用于读写分配给当前元素的快捷键。
  
      ```js
      // HTML 代码如下
      // <button accesskey="h" id="btn">点击</button>
      var btn = document.getElementById('btn');
      btn.accessKey // "h"
      ```
  
      上面代码中，`btn`元素的快捷键是`h`，按下`Alt + h`就能将焦点转移到它上面。
  
      **（5）Element.draggable**
  
      `Element.draggable`属性返回一个布尔值，表示当前元素是否可拖动。该属性可读写。
  
      **（6）Element.lang**
  
      `Element.lang`属性返回当前元素的语言设置。该属性可读写。
  
      ```js
      // HTML 代码如下
      // <html lang="en">
      document.documentElement.lang // "en"
      ```
  
      **（7）Element.tabIndex**
  
      `Element.tabIndex`属性返回一个整数，表示当前元素在 Tab 键遍历时的顺序。该属性可读写。
  
      `tabIndex`属性值如果是负值（通常是`-1`），则 Tab 键不会遍历到该元素。如果是正整数，则按照顺序，从小到大遍历。如果两个元素的`tabIndex`属性的正整数值相同，则按照出现的顺序遍历。遍历完所有`tabIndex`为正整数的元素以后，再遍历所有`tabIndex`等于`0`、或者属性值是非法值、或者没有`tabIndex`属性的元素，顺序为它们在网页中出现的顺序。
  
      **（8）Element.title**
  
      `Element.title`属性用来读写当前元素的 HTML 属性`title`。该属性通常用来指定，鼠标悬浮时弹出的文字提示框。
  
    - ##### 元素状态的相关属性：
  
      **（1）Element.hidden**
  
      `Element.hidden`属性返回一个布尔值，表示当前 HTML 元素的`hidden`属性的值。该属性可读写，用来控制当前元素是否可见。
  
      ```js
      var btn = document.getElementById('btn');
      var mydiv = document.getElementById('mydiv');
      
      btn.addEventListener('click', function () {
        mydiv.hidden = !mydiv.hidden;
      }, false);
      ```
  
      注意，该属性与 CSS 设置是互相独立的。CSS 对当前元素可见性的设置，`Element.hidden`并不能反映出来。也就是说，这个属性并不能用来判断当前元素的实际可见性。
  
      CSS 设置的优先级高于`Element.hidden`。如果 CSS 指定了该元素不可见（`display: none`）或可见（`visibility: visible`），那么`Element.hidden`并不能改变该元素实际的可见性。换言之，这个属性只在 CSS 没有明确设定当前元素的可见性时才有效。
  
      **（2）Element.contentEditable，Element.isContentEditable**
  
      HTML 元素可以设置`contentEditable`属性，使得元素的内容可以编辑。
  
      ```html
      <div contenteditable>123</div>
      ```
  
      上面代码中，`<div>`元素有`contenteditable`属性，因此用户可以在网页上编辑这个区块的内容。
  
      `Element.contentEditable`属性返回一个字符串，表示是否设置了`contenteditable`属性，有三种可能的值。该属性可写。
  
      - `"true"`：元素内容可编辑
      - `"false"`：元素内容不可编辑
      - `"inherit"`：元素是否可编辑，继承了父元素的设置
  
      `Element.isContentEditable`属性返回一个布尔值，同样表示是否设置了`contenteditable`属性。该属性只读。
  
    - ##### `Element.attributes`：
  
      `Element.attributes`属性返回一个类似数组的对象，成员是当前元素节点的所有属性节点，详见《属性的操作》一章。
  
      ```js
      var p = document.querySelector('p');
      var attrs = p.attributes;
      
      for (var i = attrs.length - 1; i >= 0; i--) {
        console.log(attrs[i].name + '->' + attrs[i].value);
      }
      ```
  
      上面代码遍历`p`元素的所有属性。
  
    - ##### `Element.className`，`Element.classList`：
  
      `className`属性用来读写当前元素节点的`class`属性。它的值是一个字符串，每个`class`之间用空格分割。
  
      `classList`属性返回一个类似数组的对象，当前元素节点的每个`class`就是这个对象的一个成员。
  
      ```js
      // HTML 代码 <div class="one two three" id="myDiv"></div>
      var div = document.getElementById('myDiv');
      
      div.className
      // "one two three"
      
      div.classList
      // {
      //   0: "one"
      //   1: "two"
      //   2: "three"
      //   length: 3
      // }
      ```
  
      上面代码中，`className`属性返回一个空格分隔的字符串，而`classList`属性指向一个类似数组的对象，该对象的`length`属性（只读）返回当前元素的`class`数量。
  
      `classList`对象有下列方法。
  
      - `add()`：增加一个 class。
      - `remove()`：移除一个 class。
      - `contains()`：检查当前元素是否包含某个 class。
      - `toggle()`：将某个 class 移入或移出当前元素。
      - `item()`：返回指定索引位置的 class。
      - `toString()`：将 class 的列表转为字符串。
  
      ```js
      var div = document.getElementById('myDiv');
      
      div.classList.add('myCssClass');
      div.classList.add('foo', 'bar');
      div.classList.remove('myCssClass');
      div.classList.toggle('myCssClass'); // 如果 myCssClass 不存在就加入，否则移除
      div.classList.contains('myCssClass'); // 返回 true 或者 false
      div.classList.item(0); // 返回第一个 Class
      div.classList.toString();
      ```
  
      下面比较一下，`className`和`classList`在添加和删除某个 class 时的写法。
  
      ```js
      var foo = document.getElementById('foo');
      
      // 添加class
      foo.className += 'bold';
      foo.classList.add('bold');
      
      // 删除class
      foo.classList.remove('bold');
      foo.className = foo.className.replace(/^bold$/, '');
      ```
  
      `toggle`方法可以接受一个布尔值，作为第二个参数。如果为`true`，则添加该属性；如果为`false`，则去除该属性。
  
      ```js
      el.classList.toggle('abc', boolValue);
      
      // 等同于
      if (boolValue) {
        el.classList.add('abc');
      } else {
        el.classList.remove('abc');
      }
      ```
  
    - ##### `Element.dataset`：
  
      网页元素可以自定义`data-`属性，用来添加数据。
  
      ```html
      <div data-timestamp="1522907809292"></div>
      ```
  
      上面代码中，`<div>`元素有一个自定义的`data-timestamp`属性，用来为该元素添加一个时间戳。
  
      `Element.dataset`属性返回一个对象，可以从这个对象读写`data-`属性。
  
      ```js
      // <article
      //   id="foo"
      //   data-columns="3"
      //   data-index-number="12314"
      //   data-parent="cars">
      //   ...
      // </article>
      var article = document.getElementById('foo');
      article.dataset.columns // "3"
      article.dataset.indexNumber // "12314"
      article.dataset.parent // "cars"
      ```
  
      注意，`dataset`上面的各个属性返回都是字符串。
  
      HTML 代码中，`data-`属性的属性名，只能包含英文字母、数字、连词线（`-`）、点（`.`）、冒号（`:`）和下划线（`_`）。它们转成 JS 对应的`dataset`属性名，规则如下。
  
      - 开头的`data-`会省略。
      - 如果连词线后面跟了一个英文字母，那么连词线会取消，该字母变成大写。
      - 其他字符不变。
  
      因此，`data-abc-def`对应`dataset.abcDef`，`data-abc-1`对应`dataset["abc-1"]`。
  
      除了使用`dataset`读写`data-`属性，也可以使用`Element.getAttribute()`和`Element.setAttribute()`，通过完整的属性名读写这些属性。
  
      ```js
      var mydiv = document.getElementById('mydiv');
      
      mydiv.dataset.foo = 'bar';
      mydiv.getAttribute('data-foo') // "bar"
      ```
  
    - ##### `Element.innerHTML`：
  
      `Element.innerHTML`属性返回一个字符串，等同于该元素包含的所有 HTML 代码。该属性可读写，常用来设置某个节点的内容。它能改写所有元素节点的内容，包括`<HTML>`和`<body>`元素。
  
      如果将`innerHTML`属性设为空，等于删除所有它包含的所有节点。
  
      ```js
      el.innerHTML = '';
      ```
  
      上面代码等于将`el`节点变成了一个空节点，`el`原来包含的节点被全部删除。
  
      注意，读取属性值的时候，如果文本节点包含`&`、小于号（`<`）和大于号（`>`），`innerHTML`属性会将它们转为实体形式`&`、`<`、`>`。如果想得到原文，建议使用`element.textContent`属性。
  
      ```js
      // HTML代码如下 <p id="para"> 5 > 3 </p>
      document.getElementById('para').innerHTML
      // 5 &gt; 3
      ```
  
      写入的时候，如果插入的文本包含 HTML 标签，会被解析成为节点对象插入 DOM。注意，如果文本之中含有`<script>`标签，虽然可以生成`script`节点，但是插入的代码不会执行。
  
      ```js
      var name = "<script>alert('haha')</script>";
      el.innerHTML = name;
      ```
  
      上面代码将脚本插入内容，脚本并不会执行。但是，`innerHTML`还是有安全风险的。
  
      ```js
      var name = "<img src=x onerror=alert(1)>";
      el.innerHTML = name;
      ```
  
      上面代码中，`alert`方法是会执行的。因此为了安全考虑，如果插入的是文本，最好用`textContent`属性代替`innerHTML`。
  
    - ##### `Element.outerHTML`：
  
      `Element.outerHTML`属性返回一个字符串，表示当前元素节点的所有 HTML 代码，包括该元素本身和所有子元素。
  
      ```js
      // HTML 代码如下
      // <div id="d"><p>Hello</p></div>
      var d = document.getElementById('d');
      d.outerHTML
      // '<div id="d"><p>Hello</p></div>'
      ```
  
      `outerHTML`属性是可读写的，对它进行赋值，等于替换掉当前元素。
  
      ```js
      // HTML 代码如下
      // <div id="container"><div id="d">Hello</div></div>
      var container = document.getElementById('container');
      var d = document.getElementById('d');
      container.firstChild.nodeName // "DIV"
      d.nodeName // "DIV"
      
      d.outerHTML = '<p>Hello</p>';
      container.firstChild.nodeName // "P"
      d.nodeName // "DIV"
      ```
  
      上面代码中，变量`d`代表子节点，它的`outerHTML`属性重新赋值以后，内层的`div`元素就不存在了，被`p`元素替换了。但是，变量`d`依然指向原来的`div`元素，这表示被替换的`DIV`元素还存在于内存中。
  
      注意，如果一个节点没有父节点，设置`outerHTML`属性会报错。
  
      ```js
      var div = document.createElement('div');
      div.outerHTML = '<p>test</p>';
      // DOMException: This element has no parent node.
      ```
  
      上面代码中，`div`元素没有父节点，设置`outerHTML`属性会报错。
  
    - ##### `Element.clientHeight`，`Element.clientWidth`：
  
      `Element.clientHeight`属性返回一个整数值，表示元素节点的 CSS 高度（单位像素），只对块级元素生效，对于行内元素返回`0`。如果块级元素没有设置 CSS 高度，则返回实际高度。
  
      除了元素本身的高度，它还包括`padding`部分，但是不包括`border`、`margin`。如果有水平滚动条，还要减去水平滚动条的高度。注意，这个值始终是整数，如果是小数会被四舍五入。
  
      `Element.clientWidth`属性返回元素节点的 CSS 宽度，同样只对块级元素有效，也是只包括元素本身的宽度和`padding`，如果有垂直滚动条，还要减去垂直滚动条的宽度。
  
      `document.documentElement`的`clientHeight`属性，返回当前视口的高度（即浏览器窗口的高度），等同于`window.innerHeight`属性减去水平滚动条的高度（如果有的话）。`document.body`的高度则是网页的实际高度。一般来说，`document.body.clientHeight`大于`document.documentElement.clientHeight`。
  
      ```js
      // 视口高度
      document.documentElement.clientHeight
      
      // 网页总高度
      document.body.clientHeight
      ```
  
    - ##### `Element.clientLeft`，`Element.clientTop`：
  
      `Element.clientLeft`属性等于元素节点左边框（left border）的宽度（单位像素），不包括左侧的`padding`和`margin`。如果没有设置左边框，或者是行内元素（`display: inline`），该属性返回`0`。该属性总是返回整数值，如果是小数，会四舍五入。
  
      `Element.clientTop`属性等于网页元素顶部边框的宽度（单位像素），其他特点都与`clientLeft`相同。
  
    - ##### `Element.scrollHeight`，`Element.scrollWidth`：
  
      `Element.scrollHeight`属性返回一个整数值（小数会四舍五入），表示当前元素的总高度（单位像素），包括溢出容器、当前不可见的部分。它包括`padding`，但是不包括`border`、`margin`以及水平滚动条的高度（如果有水平滚动条的话），还包括伪元素（`::before`或`::after`）的高度。
  
      `Element.scrollWidth`属性表示当前元素的总宽度（单位像素），其他地方都与`scrollHeight`属性类似。这两个属性只读。
  
      整张网页的总高度可以从`document.documentElement`或`document.body`上读取。
  
      ```js
      // 返回网页的总高度
      document.documentElement.scrollHeight
      document.body.scrollHeight
      ```
  
      注意，如果元素节点的内容出现溢出，即使溢出的内容是隐藏的，`scrollHeight`属性仍然返回元素的总高度。
  
      ```js
      // HTML 代码如下
      // <div id="myDiv" style="height: 200px; overflow: hidden;">...</div>
      document.getElementById('myDiv').scrollHeight // 200
      ```
  
      上面代码中，即使`myDiv`元素的 CSS 高度只有200像素，且溢出部分不可见，但是`scrollHeight`仍然会返回该元素的原始高度。
  
    - ##### `Element.scrollLeft`，`Element.scrollTop`：
  
      `Element.scrollLeft`属性表示当前元素的水平滚动条向右侧滚动的像素数量，`Element.scrollTop`属性表示当前元素的垂直滚动条向下滚动的像素数量。对于那些没有滚动条的网页元素，这两个属性总是等于0。
  
      如果要查看整张网页的水平的和垂直的滚动距离，要从`document.documentElement`元素上读取。
  
      ```js
      document.documentElement.scrollLeft
      document.documentElement.scrollTop
      ```
  
      这两个属性都可读写，设置该属性的值，会导致浏览器将当前元素自动滚动到相应的位置。
  
    - ##### `Element.offsetParent`：
  
      `Element.offsetParent`属性返回最靠近当前元素的、并且 CSS 的`position`属性不等于`static`的上层元素。
  
      ```html
      <div style="position: absolute;">
        <p>
          <span>Hello</span>
        </p>
      </div>
      ```
  
      上面代码中，`span`元素的`offsetParent`属性就是`div`元素。
  
      该属性主要用于确定子元素位置偏移的计算基准，`Element.offsetTop`和`Element.offsetLeft`就是`offsetParent`元素计算的。
  
      如果该元素是不可见的（`display`属性为`none`），或者位置是固定的（`position`属性为`fixed`），则`offsetParent`属性返回`null`。
  
      ```html
      <div style="position: absolute;">
        <p>
          <span style="display: none;">Hello</span>
        </p>
      </div>
      ```
  
      上面代码中，`span`元素的`offsetParent`属性是`null`。
  
      如果某个元素的所有上层节点的`position`属性都是`static`，则`Element.offsetParent`属性指向`<body>`元素。
  
    - ##### `Element.offsetHeight`，`Element.offsetWidth`：
  
      `Element.offsetHeight`属性返回一个整数，表示元素的 CSS 垂直高度（单位像素），包括元素本身的高度、padding 和 border，以及水平滚动条的高度（如果存在滚动条）。
  
      `Element.offsetWidth`属性表示元素的 CSS 水平宽度（单位像素），其他都与`Element.offsetHeight`一致。
  
      这两个属性都是只读属性，只比`Element.clientHeight`和`Element.clientWidth`多了边框的高度或宽度。如果元素的 CSS 设为不可见（比如`display: none;`），则返回`0`。
  
    - ##### `Element.offsetLeft`，`Element.offsetTop`：
  
      `Element.offsetLeft`返回当前元素左上角相对于`Element.offsetParent`节点的水平位移，`Element.offsetTop`返回垂直位移，单位为像素。通常，这两个值是指相对于父节点的位移。
  
      下面的代码可以算出元素左上角相对于整张网页的坐标。
  
      ```js
      function getElementPosition(e) {
        var x = 0;
        var y = 0;
        while (e !== null)  {
          x += e.offsetLeft;
          y += e.offsetTop;
          e = e.offsetParent;
        }
        return {x: x, y: y};
      }
      ```
  
    - ##### `Element.style`：
  
      每个元素节点都有`style`用来读写该元素的行内样式信息，具体介绍参见《CSS 操作》一章。
  
    - ##### `Element.children`，`Element.childElementCount`：
  
      `Element.children`属性返回一个类似数组的对象（`HTMLCollection`实例），包括当前元素节点的所有子元素。如果当前元素没有子元素，则返回的对象包含零个成员。
  
      ```js
      if (para.children.length) {
        var children = para.children;
          for (var i = 0; i < children.length; i++) {
            // ...
          }
      }
      ```
  
      上面代码遍历了`para`元素的所有子元素。
  
      这个属性与`Node.childNodes`属性的区别是，它只包括元素类型的子节点，不包括其他类型的子节点。
  
      `Element.childElementCount`属性返回当前元素节点包含的子元素节点的个数，与`Element.children.length`的值相同。
  
    - ##### `Element.firstElementChild`，`Element.lastElementChild`：
  
      `Element.firstElementChild`属性返回当前元素的第一个元素子节点，`Element.lastElementChild`返回最后一个元素子节点。
  
      如果没有元素子节点，这两个属性返回`null`。
  
    - ##### `Element.nextElementSibling`，`Element.previousElementSibling`：
  
      `Element.nextElementSibling`属性返回当前元素节点的后一个同级元素节点，如果没有则返回`null`。
  
      ```js
      // HTML 代码如下
      // <div id="div-01">Here is div-01</div>
      // <div id="div-02">Here is div-02</div>
      var el = document.getElementById('div-01');
      el.nextElementSibling
      // <div id="div-02">Here is div-02</div>
      ```
  
      `Element.previousElementSibling`属性返回当前元素节点的前一个同级元素节点，如果没有则返回`null`。
  
  - #### 实例方法
  
    - ##### 属性相关方法：
  
      元素节点提供六个方法，用来操作属性。
  
      - `getAttribute()`：读取某个属性的值
      - `getAttributeNames()`：返回当前元素的所有属性名
      - `setAttribute()`：写入属性值
      - `hasAttribute()`：某个属性是否存在
      - `hasAttributes()`：当前元素是否有属性
      - `removeAttribute()`：删除属性
  
      这些方法的介绍请看《属性的操作》一章。
  
    - ##### `Element.querySelector()`：
  
      `Element.querySelector`方法接受 CSS 选择器作为参数，返回父元素的第一个匹配的子元素。如果没有找到匹配的子元素，就返回`null`。
  
      ```js
      var content = document.getElementById('content');
      var el = content.querySelector('p');
      ```
  
      上面代码返回`content`节点的第一个`p`元素。
  
      `Element.querySelector`方法可以接受任何复杂的 CSS 选择器。
  
      ```js
      document.body.querySelector("style[type='text/css'], style:not([type])");
      ```
  
      注意，这个方法无法选中伪元素。
  
      它可以接受多个选择器，它们之间使用逗号分隔。
  
      ```js
      element.querySelector('div, p')
      ```
  
      上面代码返回`element`的第一个`div`或`p`子元素。
  
      需要注意的是，浏览器执行`querySelector`方法时，是先在全局范围内搜索给定的 CSS 选择器，然后过滤出哪些属于当前元素的子元素。因此，会有一些违反直觉的结果，下面是一段 HTML 代码。
  
      ```html
      <div>
      <blockquote id="outer">
        <p>Hello</p>
        <div id="inner">
          <p>World</p>
        </div>
      </blockquote>
      </div>
      ```
  
      那么，像下面这样查询的话，实际上返回的是第一个`p`元素，而不是第二个。
  
      ```js
      var outer = document.getElementById('outer');
      outer.querySelector('div p')
      // <p>Hello</p>
      ```
  
    - ##### `Element.querySelectorAll()`：
  
      `Element.querySelectorAll`方法接受 CSS 选择器作为参数，返回一个`NodeList`实例，包含所有匹配的子元素。
  
      ```js
      var el = document.querySelector('#test');
      var matches = el.querySelectorAll('div.highlighted > p');
      ```
  
      该方法的执行机制与`querySelector`方法相同，也是先在全局范围内查找，再过滤出当前元素的子元素。因此，选择器实际上针对整个文档的。
  
      它也可以接受多个 CSS 选择器，它们之间使用逗号分隔。如果选择器里面有伪元素的选择器，则总是返回一个空的`NodeList`实例。
  
    - ##### `Element.getElementsByClassName()`：
  
      `Element.getElementsByClassName`方法返回一个`HTMLCollection`实例，成员是当前元素节点的所有具有指定 class 的子元素节点。该方法与`document.getElementsByClassName`方法的用法类似，只是搜索范围不是整个文档，而是当前元素节点。
  
      ```js
      element.getElementsByClassName('red test');
      ```
  
      注意，该方法的参数大小写敏感。
  
      由于`HTMLCollection`实例是一个活的集合，`document`对象的任何变化会立刻反应到实例，下面的代码不会生效。
  
      ```js
      // HTML 代码如下
      // <div id="example">
      //   <p class="foo"></p>
      //   <p class="foo"></p>
      // </div>
      var element = document.getElementById('example');
      var matches = element.getElementsByClassName('foo');
      
      for (var i = 0; i< matches.length; i++) {
        matches[i].classList.remove('foo');
        matches.item(i).classList.add('bar');
      }
      // 执行后，HTML 代码如下
      // <div id="example">
      //   <p></p>
      //   <p class="foo bar"></p>
      // </div>
      ```
  
      上面代码中，`matches`集合的第一个成员，一旦被拿掉 class 里面的`foo`，就会立刻从`matches`里面消失，导致出现上面的结果。
  
    - ##### `Element.getElementsByTagName()`：
  
      `Element.getElementsByTagName()`方法返回一个`HTMLCollection`实例，成员是当前节点的所有匹配指定标签名的子元素节点。该方法与`document.getElementsByClassName()`方法的用法类似，只是搜索范围不是整个文档，而是当前元素节点。
  
      ```js
      var table = document.getElementById('forecast-table');
      var cells = table.getElementsByTagName('td');
      ```
  
      注意，该方法的参数是大小写不敏感的，因为 HTML 标签名也是大小写不敏感。
  
    - ##### `Element.closest()`：
  
      `Element.closest`方法接受一个 CSS 选择器作为参数，返回匹配该选择器的、最接近当前节点的一个祖先节点（包括当前节点本身）。如果没有任何节点匹配 CSS 选择器，则返回`null`。
  
      ```js
      // HTML 代码如下
      // <article>
      //   <div id="div-01">Here is div-01
      //     <div id="div-02">Here is div-02
      //       <div id="div-03">Here is div-03</div>
      //     </div>
      //   </div>
      // </article>
      
      var div03 = document.getElementById('div-03');
      
      // div-03 最近的祖先节点
      div03.closest("#div-02") // div-02
      div03.closest("div div") // div-03
      div03.closest("article > div") //div-01
      div03.closest(":not(div)") // article
      ```
  
      上面代码中，由于`closest`方法将当前节点也考虑在内，所以第二个`closest`方法返回`div-03`。
  
    - ##### `Element.matches()`：
  
      `Element.matches`方法返回一个布尔值，表示当前元素是否匹配给定的 CSS 选择器。
  
      ```js
      if (el.matches('.someClass')) {
        console.log('Match!');
      }
      ```
  
    - ##### 事件相关方法：
  
      以下三个方法与`Element`节点的事件相关。这些方法都继承自`EventTarget`接口，详见相关章节。
  
      - `Element.addEventListener()`：添加事件的回调函数
      - `Element.removeEventListener()`：移除事件监听函数
      - `Element.dispatchEvent()`：触发事件
  
      ```js
      element.addEventListener('click', listener, false);
      element.removeEventListener('click', listener, false);
      
      var event = new Event('click');
      element.dispatchEvent(event);
      ```
  
    - ##### `Element.scrollIntoView()`：
  
      `Element.scrollIntoView`方法滚动当前元素，进入浏览器的可见区域，类似于设置`window.location.hash`的效果。
  
      ```js
      el.scrollIntoView(); // 等同于el.scrollIntoView(true)
      el.scrollIntoView(false);
      ```
  
      该方法可以接受一个布尔值作为参数。如果为`true`，表示元素的顶部与当前区域的可见部分的顶部对齐（前提是当前区域可滚动）；如果为`false`，表示元素的底部与当前区域的可见部分的尾部对齐（前提是当前区域可滚动）。如果没有提供该参数，默认为`true`。
  
    - ##### `Element.getBoundingClientRect()`：
  
      `Element.getBoundingClientRect`方法返回一个对象，提供当前元素节点的大小、位置等信息，基本上就是 CSS 盒状模型的所有信息。
  
      ```js
      var rect = obj.getBoundingClientRect();
      ```
  
      上面代码中，`getBoundingClientRect`方法返回的`rect`对象，具有以下属性（全部为只读）。
  
      - `x`：元素左上角相对于视口的横坐标
      - `y`：元素左上角相对于视口的纵坐标
      - `height`：元素高度
      - `width`：元素宽度
      - `left`：元素左上角相对于视口的横坐标，与`x`属性相等
      - `right`：元素右边界相对于视口的横坐标（等于`x + width`）
      - `top`：元素顶部相对于视口的纵坐标，与`y`属性相等
      - `bottom`：元素底部相对于视口的纵坐标（等于`y + height`）
  
      由于元素相对于视口（viewport）的位置，会随着页面滚动变化，因此表示位置的四个属性值，都不是固定不变的。如果想得到绝对位置，可以将`left`属性加上`window.scrollX`，`top`属性加上`window.scrollY`。
  
      注意，`getBoundingClientRect`方法的所有属性，都把边框（`border`属性）算作元素的一部分。也就是说，都是从边框外缘的各个点来计算。因此，`width`和`height`包括了元素本身 + `padding` + `border`。
  
      另外，上面的这些属性，都是继承自原型的属性，`Object.keys`会返回一个空数组，这一点也需要注意。
  
      ```js
      var rect = document.body.getBoundingClientRect();
      Object.keys(rect) // []
      ```
  
      上面代码中，`rect`对象没有自身属性，而`Object.keys`方法只返回对象自身的属性，所以返回了一个空数组。
  
    - ##### `Element.getClientRects()`：
  
      `Element.getClientRects`方法返回一个类似数组的对象，里面是当前元素在页面上形成的所有矩形（所以方法名中的`Rect`用的是复数）。每个矩形都有`bottom`、`height`、`left`、`right`、`top`和`width`六个属性，表示它们相对于视口的四个坐标，以及本身的高度和宽度。
  
      对于盒状元素（比如`<div>`和`<p>`），该方法返回的对象中只有该元素一个成员。对于行内元素（比如`<span>`、`<a>`、`<em>`），该方法返回的对象有多少个成员，取决于该元素在页面上占据多少行。这是它和`Element.getBoundingClientRect()`方法的主要区别，后者对于行内元素总是返回一个矩形。
  
      ```html
      <span id="inline">Hello World Hello World Hello World</span>
      ```
  
      上面代码是一个行内元素`<span>`，如果它在页面上占据三行，`getClientRects`方法返回的对象就有三个成员，如果它在页面上占据一行，`getClientRects`方法返回的对象就只有一个成员。
  
      ```js
      var el = document.getElementById('inline');
      el.getClientRects().length // 3
      el.getClientRects()[0].left // 8
      el.getClientRects()[0].right // 113.908203125
      el.getClientRects()[0].bottom // 31.200000762939453
      el.getClientRects()[0].height // 23.200000762939453
      el.getClientRects()[0].width // 105.908203125
      ```
  
      这个方法主要用于判断行内元素是否换行，以及行内元素的每一行的位置偏移。
  
      注意，如果行内元素包括换行符，那么该方法会把换行符考虑在内。
  
      ```html
      <span id="inline">
        Hello World
        Hello World
        Hello World
      </span>
      ```
  
      上面代码中，`<span>`节点内部有三个换行符，即使 HTML 语言忽略换行符，将它们显示为一行，`getClientRects()`方法依然会返回三个成员。如果行宽设置得特别窄，上面的`<span>`元素显示为6行，那么就会返回六个成员。
  
    - ##### `Element.insertAdjacentElement()`：
  
      `Element.insertAdjacentElement`方法在相对于当前元素的指定位置，插入一个新的节点。该方法返回被插入的节点，如果插入失败，返回`null`。
  
      ```js
      element.insertAdjacentElement(position, element);
      ```
  
      `Element.insertAdjacentElement`方法一共可以接受两个参数，第一个参数是一个字符串，表示插入的位置，第二个参数是将要插入的节点。第一个参数只可以取如下的值。
  
      - `beforebegin`：当前元素之前
      - `afterbegin`：当前元素内部的第一个子节点前面
      - `beforeend`：当前元素内部的最后一个子节点后面
      - `afterend`：当前元素之后
  
      注意，`beforebegin`和`afterend`这两个值，只在当前节点有父节点时才会生效。如果当前节点是由脚本创建的，没有父节点，那么插入会失败。
  
      ```js
      var p1 = document.createElement('p')
      var p2 = document.createElement('p')
      p1.insertAdjacentElement('afterend', p2) // null
      ```
  
      上面代码中，`p1`没有父节点，所以插入`p2`到它后面就失败了。
  
      如果插入的节点是一个文档里现有的节点，它会从原有位置删除，放置到新的位置。
  
    - ##### `Element.insertAdjacentHTML()`，`Element.insertAdjacentText()`：
  
      `Element.insertAdjacentHTML`方法用于将一个 HTML 字符串，解析生成 DOM 结构，插入相对于当前节点的指定位置。
  
      ```js
      element.insertAdjacentHTML(position, text);
      ```
  
      该方法接受两个参数，第一个是一个表示指定位置的字符串，第二个是待解析的 HTML 字符串。第一个参数只能设置下面四个值之一。
  
      - `beforebegin`：当前元素之前
      - `afterbegin`：当前元素内部的第一个子节点前面
      - `beforeend`：当前元素内部的最后一个子节点后面
      - `afterend`：当前元素之后
  
      ```js
      // HTML 代码：<div id="one">one</div>
      var d1 = document.getElementById('one');
      d1.insertAdjacentHTML('afterend', '<div id="two">two</div>');
      // 执行后的 HTML 代码：
      // <div id="one">one</div><div id="two">two</div>
      ```
  
      该方法只是在现有的 DOM 结构里面插入节点，这使得它的执行速度比`innerHTML`方法快得多。
  
      注意，该方法不会转义 HTML 字符串，这导致它不能用来插入用户输入的内容，否则会有安全风险。
  
      `Element.insertAdjacentText`方法在相对于当前节点的指定位置，插入一个文本节点，用法与`Element.insertAdjacentHTML`方法完全一致。
  
      ```js
      // HTML 代码：<div id="one">one</div>
      var d1 = document.getElementById('one');
      d1.insertAdjacentText('afterend', 'two');
      // 执行后的 HTML 代码：
      // <div id="one">one</div>two
      ```
  
    - ##### `Element.remove()`：
  
      `Element.remove`方法继承自 ChildNode 接口，用于将当前元素节点从它的父节点移除。
  
      ```js
      var el = document.getElementById('mydiv');
      el.remove();
      ```

      上面代码将`el`节点从 DOM 树里面移除。

    - ##### `Element.focus()`，`Element.blur()`：
  
      `Element.focus`方法用于将当前页面的焦点，转移到指定元素上。
  
      ```
      document.getElementById('my-span').focus();
      ```
  
      该方法可以接受一个对象作为参数。参数对象的`preventScroll`属性是一个布尔值，指定是否将当前元素停留在原始位置，而不是滚动到可见区域。
  
      ```js
      function getFocus() {
        document.getElementById('btn').focus({preventScroll:false});
      }
      ```
  
      上面代码会让`btn`元素获得焦点，并滚动到可见区域。
  
      最后，从`document.activeElement`属性可以得到当前获得焦点的元素。
  
      `Element.blur`方法用于将焦点从当前元素移除。
  
    - ##### `Element.click()`：
  
      `Element.click`方法用于在当前元素上模拟一次鼠标点击，相当于触发了`click`事件。
  
- ## 属性的操作

  HTML 元素包括标签名和若干个键值对，这个键值对就称为“属性”（attribute）。

  ```html
  <a id="test" href="http://www.example.com">
    链接
  </a>
  ```

  上面代码中，`a`元素包括两个属性：`id`属性和`href`属性。

  属性本身是一个对象（`Attr`对象），但是实际上，这个对象极少使用。一般都是通过元素节点对象（`HTMlElement`对象）来操作属性。本章介绍如何操作这些属性。

  - #### `Element.attributes` 属性

    元素对象有一个`attributes`属性，返回一个类似数组的动态对象，成员是该元素标签的所有属性节点对象，属性的实时变化都会反映在这个节点对象上。其他类型的节点对象，虽然也有`attributes`属性，但返回的都是`null`，因此可以把这个属性视为元素对象独有的。

    单个属性可以通过序号引用，也可以通过属性名引用。

    ```js
    // HTML 代码如下
    // <body bgcolor="yellow" onload="">
    document.body.attributes[0]
    document.body.attributes.bgcolor
    document.body.attributes['ONLOAD']
    ```

    注意，上面代码的三种方法，返回的都是属性节点对象，而不是属性值。

    属性节点对象有`name`和`value`属性，对应该属性的属性名和属性值，等同于`nodeName`属性和`nodeValue`属性。

    ```js
    // HTML代码为
    // <div id="mydiv">
    var n = document.getElementById('mydiv');
    
    n.attributes[0].name // "id"
    n.attributes[0].nodeName // "id"
    
    n.attributes[0].value // "mydiv"
    n.attributes[0].nodeValue // "mydiv"
    ```

    下面代码可以遍历一个元素节点的所有属性。

    ```js
    var para = document.getElementsByTagName('p')[0];
    var result = document.getElementById('result');
    
    if (para.hasAttributes()) {
      var attrs = para.attributes;
      var output = '';
      for(var i = attrs.length - 1; i >= 0; i--) {
        output += attrs[i].name + '->' + attrs[i].value;
      }
      result.textContent = output;
    } else {
      result.textContent = 'No attributes to show';
    }
    ```

  - #### 元素的标准属性

    HTML 元素的标准属性（即在标准中定义的属性），会自动成为元素节点对象的属性。

    ```js
    var a = document.getElementById('test');
    a.id // "test"
    a.href // "http://www.example.com/"
    ```

    上面代码中，`a`元素标签的属性`id`和`href`，自动成为节点对象的属性。

    这些属性都是可写的。

    ```js
    var img = document.getElementById('myImage');
    img.src = 'http://www.example.com/image.jpg';
    ```

    上面的写法，会立刻替换掉`img`对象的`src`属性，即会显示另外一张图片。

    这种修改属性的方法，常常用于添加表单的属性。

    ```js
    var f = document.forms[0];
    f.action = 'submit.php';
    f.method = 'POST';
    ```

    上面代码为表单添加提交网址和提交方法。

    注意，这种用法虽然可以读写属性，但是无法删除属性，`delete`运算符在这里不会生效。

    HTML 元素的属性名是大小写不敏感的，但是 JS 对象的属性名是大小写敏感的。转换规则是，转为 JS 属性名时，一律采用小写。如果属性名包括多个单词，则采用骆驼拼写法，即从第二个单词开始，每个单词的首字母采用大写，比如`onClick`。

    有些 HTML 属性名是 JS 的保留字，转为 JS 属性时，必须改名。主要是以下两个。

    - `for`属性改为`htmlFor`
    - `class`属性改为`className`

    另外，HTML 属性值一般都是字符串，但是 JS 属性会自动转换类型。比如，将字符串`true`转为布尔值，将`onClick`的值转为一个函数，将`style`属性的值转为一个`CSSStyleDeclaration`对象。因此，可以对这些属性赋予各种类型的值。

  - #### 属性操作的标准方法

    元素节点提供六个方法，用来操作属性。

    - `getAttribute()`
    - `getAttributeNames()`
    - `setAttribute()`
    - `hasAttribute()`
    - `hasAttributes()`
    - `removeAttribute()`

    这有几点注意。

    （1）适用性

    这六个方法对所有属性（包括用户自定义的属性）都适用。

    （2）返回值

    `getAttribute()`只返回字符串，不会返回其他类型的值。

    （3）属性名

    这些方法只接受属性的标准名称，不用改写保留字，比如`for`和`class`都可以直接使用。另外，这些方法对于属性名是大小写不敏感的。

    ```js
    var image = document.images[0];
    image.setAttribute('class', 'myImage');
    ```

    上面代码中，`setAttribute`方法直接使用`class`作为属性名，不用写成`className`。

    - ##### `Element.getAttribute()`：

      `Element.getAttribute`方法返回当前元素节点的指定属性。如果指定属性不存在，则返回`null`。

      ```js
      // HTML 代码为
      // <div id="div1" align="left">
      var div = document.getElementById('div1');
      div.getAttribute('align') // "left"
      ```

    - ##### `Element.getAttributeNames()`：

      `Element.getAttributeNames()`返回一个数组，成员是当前元素的所有属性的名字。如果当前元素没有任何属性，则返回一个空数组。使用`Element.attributes`属性，也可以拿到同样的结果，唯一的区别是它返回的是类似数组的对象。

      ```js
      var mydiv = document.getElementById('mydiv');
      
      mydiv.getAttributeNames().forEach(function (key) {
        var value = mydiv.getAttribute(key);
        console.log(key, value);
      })
      ```

      上面代码用于遍历某个节点的所有属性。

    - ##### `Element.setAttribute()`：

      `Element.setAttribute`方法用于为当前元素节点新增属性。如果同名属性已存在，则相当于编辑已存在的属性。该方法没有返回值。

      ```js
      // HTML 代码为
      // <button>Hello World</button>
      var b = document.querySelector('button');
      b.setAttribute('name', 'myButton');
      b.setAttribute('disabled', true);
      ```

      上面代码中，`button`元素的`name`属性被设成`myButton`，`disabled`属性被设成`true`。

      这里有两个地方需要注意，首先，属性值总是字符串，其他类型的值会自动转成字符串，比如布尔值`true`就会变成字符串`true`；其次，上例的`disable`属性是一个布尔属性，对于`<button>`元素来说，这个属性不需要属性值，只要设置了就总是会生效，因此`setAttribute`方法里面可以将`disabled`属性设成任意值。

    - ##### `Element.hasAttribute()`：

      `Element.hasAttribute`方法返回一个布尔值，表示当前元素节点是否包含指定属性。

      ```js
      var d = document.getElementById('div1');
      
      if (d.hasAttribute('align')) {
        d.setAttribute('align', 'center');
      }
      ```

      上面代码检查`div`节点是否含有`align`属性。如果有，则设置为居中对齐。

    - ##### `Element.hasAttributes()`：

      `Element.hasAttributes`方法返回一个布尔值，表示当前元素是否有属性，如果没有任何属性，就返回`false`，否则返回`true`。

      ```js
      var foo = document.getElementById('foo');
      foo.hasAttributes() // true
      ```

    - ##### `Element.removeAttribute()`：

      `Element.removeAttribute`方法移除指定属性。该方法没有返回值。

      ```js
      // HTML 代码为
      // <div id="div1" align="left" width="200px">
      document.getElementById('div1').removeAttribute('align');
      // 现在的HTML代码为
      // <div id="div1" width="200px">
      ```

  - #### `dataset` 属性

    有时，需要在HTML元素上附加数据，供 JS 脚本使用。一种解决方法是自定义属性。

    ```html
    <div id="mydiv" foo="bar">
    ```

    上面代码为`div`元素自定义了`foo`属性，然后可以用`getAttribute()`和`setAttribute()`读写这个属性。

    ```js
    var n = document.getElementById('mydiv');
    n.getAttribute('foo') // bar
    n.setAttribute('foo', 'baz')
    ```

    这种方法虽然可以达到目的，但是会使得 HTML 元素的属性不符合标准，导致网页代码通不过校验。

    更好的解决方法是，使用标准提供的`data-*`属性。

    ```html
    <div id="mydiv" data-foo="bar">
    ```

    然后，使用元素节点对象的`dataset`属性，它指向一个对象，可以用来操作 HTML 元素标签的`data-*`属性。

    ```js
    var n = document.getElementById('mydiv');
    n.dataset.foo // bar
    n.dataset.foo = 'baz'
    ```

    上面代码中，通过`dataset.foo`读写`data-foo`属性。

    删除一个`data-*`属性，可以直接使用`delete`命令。

    ```js
    delete document.getElementById('myDiv').dataset.foo;
    ```

    除了`dataset`属性，也可以用`getAttribute('data-foo')`、`removeAttribute('data-foo')`、`setAttribute('data-foo')`、`hasAttribute('data-foo')`等方法操作`data-*`属性。

    注意，`data-`后面的属性名有限制，只能包含字母、数字、连词线（`-`）、点（`.`）、冒号（`:`）和下划线（`_`)。而且，属性名不应该使用`A`到`Z`的大写字母，比如不能有`data-helloWorld`这样的属性名，而要写成`data-hello-world`。

    转成`dataset`的键名时，连词线后面如果跟着一个小写字母，那么连词线会被移除，该小写字母转为大写字母，其他字符不变。反过来，`dataset`的键名转成属性名时，所有大写字母都会被转成连词线+该字母的小写形式，其他字符不变。比如，`dataset.helloWorld`会转成`data-hello-world`。

- ## `Text` 节点和 `DocumentFragment` 节点

  - #### `Text` 节点的概念

    文本节点（`Text`）代表元素节点（`Element`）和属性节点（`Attribute`）的文本内容。如果一个节点只包含一段文本，那么它就有一个文本子节点，代表该节点的文本内容。

    通常我们使用父节点的`firstChild`、`nextSibling`等属性获取文本节点，或者使用`Document`节点的`createTextNode`方法创造一个文本节点。

    ```js
    // 获取文本节点
    var textNode = document.querySelector('p').firstChild;
    
    // 创造文本节点
    var textNode = document.createTextNode('Hi');
    document.querySelector('div').appendChild(textNode);
    ```

    浏览器原生提供一个`Text`构造函数。它返回一个文本节点实例。它的参数就是该文本节点的文本内容。

    ```js
    // 空字符串
    var text1 = new Text();
    
    // 非空字符串
    var text2 = new Text('This is a text node');
    ```

    注意，由于空格也是一个字符，所以哪怕只有一个空格，也会形成文本节点。比如，`<p> </p>`包含一个空格，它的子节点就是一个文本节点。

    文本节点除了继承`Node`接口，还继承了`CharacterData`接口。`Node`接口的属性和方法请参考《Node 接口》一章，这里不再重复介绍了，以下的属性和方法大部分来自`CharacterData`接口。

  - #### `Text` 节点的属性

    - ##### `data`：

      `data`属性等同于`nodeValue`属性，用来设置或读取文本节点的内容。

      ```js
      // 读取文本内容
      document.querySelector('p').firstChild.data
      // 等同于
      document.querySelector('p').firstChild.nodeValue
      
      // 设置文本内容
      document.querySelector('p').firstChild.data = 'Hello World';
      ```

    - ##### `wholeText`：

      `wholeText`属性将当前文本节点与毗邻的文本节点，作为一个整体返回。大多数情况下，`wholeText`属性的返回值，与`data`属性和`textContent`属性相同。但是，某些特殊情况会有差异。

      举例来说，HTML 代码如下。

      ```html
      <p id="para">A <em>B</em> C</p>
      ```

      这时，文本节点的`wholeText`属性和`data`属性，返回值相同。

      ```js
      var el = document.getElementById('para');
      el.firstChild.wholeText // "A "
      el.firstChild.data // "A "
      ```

      但是，一旦移除`<em>`节点，`wholeText`属性与`data`属性就会有差异，因为这时其实`<p>`节点下面包含了两个毗邻的文本节点。

      ```js
      el.removeChild(para.childNodes[1]);
      el.firstChild.wholeText // "A C"
      el.firstChild.data // "A "
      ```

    - ##### `length`：

      `length`属性返回当前文本节点的文本长度。

      ```js
      (new Text('Hello')).length // 5
      ```

    - ##### `nextElementSibling`，`previousElementSibling`：

      `nextElementSibling`属性返回紧跟在当前文本节点后面的那个同级元素节点。如果取不到元素节点，则返回`null`。

      ```js
      // HTML 为
      // <div>Hello <em>World</em></div>
      var tn = document.querySelector('div').firstChild;
      tn.nextElementSibling
      // <em>World</em>
      ```

      `previousElementSibling`属性返回当前文本节点前面最近的同级元素节点。如果取不到元素节点，则返回`null：`。

  - #### `Text` 节点的方法

    - ##### `appendData()`，`deleteData()`，`insertData()`，`replaceData()`，`subStringData()`：

      以下5个方法都是编辑`Text`节点文本内容的方法。

      - `appendData()`：在`Text`节点尾部追加字符串。
      - `deleteData()`：删除`Text`节点内部的子字符串，第一个参数为子字符串开始位置，第二个参数为子字符串长度。
      - `insertData()`：在`Text`节点插入字符串，第一个参数为插入位置，第二个参数为插入的子字符串。
      - `replaceData()`：用于替换文本，第一个参数为替换开始位置，第二个参数为需要被替换掉的长度，第三个参数为新加入的字符串。
      - `subStringData()`：用于获取子字符串，第一个参数为子字符串在`Text`节点中的开始位置，第二个参数为子字符串长度。

      ```js
      // HTML 代码为
      // <p>Hello World</p>
      var pElementText = document.querySelector('p').firstChild;
      
      pElementText.appendData('!');
      // 页面显示 Hello World!
      pElementText.deleteData(7, 5);
      // 页面显示 Hello W
      pElementText.insertData(7, 'Hello ');
      // 页面显示 Hello WHello
      pElementText.replaceData(7, 5, 'World');
      // 页面显示 Hello WWorld
      pElementText.substringData(7, 10);
      // 页面显示不变，返回"World "
      ```

    - ##### `remove()`：

      `remove`方法用于移除当前`Text`节点。

      ```js
      // HTML 代码为
      // <p>Hello World</p>
      document.querySelector('p').firstChild.remove()
      // 现在 HTML 代码为
      // <p></p>
      ```

    - ##### `splitText()`：

      `splitText`方法将`Text`节点一分为二，变成两个毗邻的`Text`节点。它的参数就是分割位置（从零开始），分割到该位置的字符前结束。如果分割位置不存在，将报错。

      分割后，该方法返回分割位置后方的字符串，而原`Text`节点变成只包含分割位置前方的字符串。

      ```js
      // html 代码为 <p id="p">foobar</p>
      var p = document.getElementById('p');
      var textnode = p.firstChild;
      
      var newText = textnode.splitText(3);
      newText // "bar"
      textnode // "foo"
      ```

      父元素节点的`normalize`方法可以将毗邻的两个`Text`节点合并。

      接上面的例子，文本节点的`splitText`方法将一个`Text`节点分割成两个，父元素的`normalize`方法可以实现逆操作，将它们合并。

      ```js
      p.childNodes.length // 2
      
      // 将毗邻的两个 Text 节点合并
      p.normalize();
      p.childNodes.length // 1
      ```

  - #### `DocumentFragment` 节点

    `DocumentFragment`节点代表一个文档的片段，本身就是一个完整的 DOM 树形结构。它没有父节点，`parentNode`返回`null`，但是可以插入任意数量的子节点。它不属于当前文档，操作`DocumentFragment`节点，要比直接操作 DOM 树快得多。

    它一般用于构建一个 DOM 结构，然后插入当前文档。`document.createDocumentFragment`方法，以及浏览器原生的`DocumentFragment`构造函数，可以创建一个空的`DocumentFragment`节点。然后再使用其他 DOM 方法，向其添加子节点。

    ```js
    var docFrag = document.createDocumentFragment();
    // 等同于
    var docFrag = new DocumentFragment();
    
    var li = document.createElement('li');
    li.textContent = 'Hello World';
    docFrag.appendChild(li);
    
    document.querySelector('ul').appendChild(docFrag);
    ```

    上面代码创建了一个`DocumentFragment`节点，然后将一个`li`节点添加在它里面，最后将`DocumentFragment`节点移动到原文档。

    注意，`DocumentFragment`节点本身不能被插入当前文档。当它作为`appendChild()`、`insertBefore()`、`replaceChild()`等方法的参数时，是它的所有子节点插入当前文档，而不是它自身。一旦`DocumentFragment`节点被添加进当前文档，它自身就变成了空节点（`textContent`属性为空字符串），可以被再次使用。如果想要保存`DocumentFragment`节点的内容，可以使用`cloneNode`方法。

    ```js
    document
      .querySelector('ul')
      .appendChild(docFrag.cloneNode(true));
    ```

    上面这样添加`DocumentFragment`节点进入当前文档，不会清空`DocumentFragment`节点。

    下面是一个例子，使用`DocumentFragment`反转一个指定节点的所有子节点的顺序。

    ```js
    function reverse(n) {
      var f = document.createDocumentFragment();
      while(n.lastChild) f.appendChild(n.lastChild);
      n.appendChild(f);
    }
    ```

    `DocumentFragment`节点对象没有自己的属性和方法，全部继承自`Node`节点和`ParentNode`接口。也就是说，`DocumentFragment`节点比`Node`节点多出以下四个属性。

    - `children`：返回一个动态的`HTMLCollection`集合对象，包括当前`DocumentFragment`对象的所有子元素节点。
    - `firstElementChild`：返回当前`DocumentFragment`对象的第一个子元素节点，如果没有则返回`null`。
    - `lastElementChild`：返回当前`DocumentFragment`对象的最后一个子元素节点，如果没有则返回`null`。
    - `childElementCount`：返回当前`DocumentFragment`对象的所有子元素数量。

------

