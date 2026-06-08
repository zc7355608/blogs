- ## 字符串的扩展

  - #### 字符的 Unicode 码点表示法
  
    JS 允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的 UTF16 码元。但是，这种表示法只限于码点在`\u0000`~`\uFFFF`之间的字符。超出这个范围的字符，必须用两个双字节的UTF-16代理对形式。
  
    ```js
    "\uD842\uDFB7"
    // "𠮷"
    "\u20BB7"
    // " 7"
    ```
  
    上面代码表示，如果直接在`\u`后面跟上超过`0xFFFF`的数值（比如`\u20BB7`），JS 会理解成`\u20BB7`加字符`7`。由于`\u20BB`是一个不可打印字符，所以只会显示一个空格，后面跟着一个`7`。
  
    ES6 增加了对 Unicode 码点的支持，**只要将Unicode码点放入大括号，就能正确解析该字符**：
  
    ```js
    "\u{20BB7}"
    // "𠮷"
    
    "\u{41}\u{42}\u{43}"
    // "ABC"
    
    let hello = 123;
    hell\u{6F} // 123
    
    '\u{1F680}' === '\uD83D\uDE80'
    // true
    ```
  
    上面代码中，最后一个例子表明，大括号表示法与四字节的 UTF-16 编码是等价的。
  
    有了这种表示法之后，JS 共有 6 种方法可以表示一个字符。
  
    ```js
    '\z' === 'z'  // true
    '\172' === 'z' // true
    '\x7A' === 'z' // true
    '\u007A' === 'z' // true
    '\u{7A}' === 'z' // true
    ```

  - #### 字符串的遍历器接口
  
    ES6 为字符串添加了遍历器接口（详见《Iterator》一章），使得字符串可以被`for...of`循环遍历。

    ```js
    for (let codePoint of 'foo') {
      console.log(codePoint)
    }
    // "f"
    // "o"
    // "o"
    ```
  
    除了遍历字符串，这个遍历器最大的优点是**可以识别大于`0xFFFF`的码点**，传统的`for`循环无法识别这样的码点。
  
    ```js
    let text = String.fromCodePoint(0x20BB7);
    
    for (let i = 0; i < text.length; i++) {
      console.log(text[i]);
    }
    // " "
    // " "
    
    for (let i of text) {
      console.log(i);
    }
    // "𠮷"
    ```
  
    上面代码中，字符串`text`只有一个字符，但是`for`循环会认为它包含两个字符（都不可打印），而`for...of`循环会正确识别出这一个字符。
  
  - #### ~~直接输入 `U+2028` 和 `U+2029`（不理解）~~
  
    JS 字符串允许直接输入字符，以及输入字符的转义形式。举例来说，“中”的 Unicode 码点是 U+4e2d，你可以直接在字符串里面输入这个汉字，也可以输入它的转义形式`\u4e2d`，两者是等价的。
  
    ```js
    '中' === '\u4e2d' // true
    ```
  
    但是，JS 规定有5个字符，不能在字符串里面直接使用，只能使用转义形式。
  
    - U+005C：反斜杠（reverse solidus)
    - U+000D：回车（carriage return）
    - U+2028：行分隔符（line separator）
    - U+2029：段分隔符（paragraph separator）
    - U+000A：换行符（line feed）
  
    举例来说，字符串里面不能直接包含反斜杠，一定要转义写成`\\`或者`\u005c`。
  
    这个规定本身没有问题，麻烦在于 JSON 格式允许字符串里面直接使用 U+2028（行分隔符）和 U+2029（段分隔符）。这样一来，服务器输出的 JSON 被`JSON.parse`解析，就有可能直接报错。
  
    ```js
    const json = '"\u2028"';
    JSON.parse(json); // 可能报错
    ```
  
    JSON 格式已经冻结（RFC 7159），没法修改了。为了消除这个报错，[ES2019](https://github.com/tc39/proposal-json-superset) 允许 JS 字符串直接输入 U+2028（行分隔符）和 U+2029（段分隔符）。
  
    ```js
    const PS = eval("'\u2029'");
    ```
  
    根据这个提案，上面的代码不会报错。
  
    注意，模板字符串现在就允许直接输入这两个字符。另外，正则表达式依然不允许直接输入这两个字符，这是没有问题的，因为 JSON 本来就不允许直接包含正则表达式。
  
  - #### `JSON.stringify()` 的改造
  
    根据标准，JSON 数据在网络传输、或者保存到文件时，必须使用 UTF-8 编码。但是，现在的`JSON.stringify()`方法有可能返回不符合 UTF-8 标准的字符串。因为 JS 的字符串内部使用 UTF-16 编码，而 UTF-16 编码中的某个字符（比如 `\uD834`），可能没有对应合法的 UTF-8 字符。
  
    具体来说，UTF-8 标准规定，`0xD800`到`0xDFFF`之间的码点，不能单独使用，必须配对使用。比如，`\uD834\uDF06`是两个码点，但是必须放在一起配对使用，代表字符`𝌆`。这是为了表示码点大于`0xFFFF`的字符的一种变通方法。单独使用`\uD834`和`\uDF06`这两个码点是不合法的，或者颠倒顺序也不行，因为`\uDF06\uD834`并没有对应的字符。
  
    `JSON.stringify()`的问题在于，它可能返回`0xD800`到`0xDFFF`之间的单个码点。
  
    ```js
    JSON.stringify('\u{D834}') // "\u{D834}"
    ```
  
    为了确保返回的是合法的 UTF-8 字符，[ES2019](https://github.com/tc39/proposal-well-formed-stringify) 改变了`JSON.stringify()`的行为：如果遇到`0xD800`到`0xDFFF`之间的单个码点，或者不存在的配对形式，它会返回转义字符串，留给应用自己决定如何去处理。
  
    ```js
    JSON.stringify('\u{D834}') // ""\\uD834""
    JSON.stringify('\uDF06\uD834') // ""\\udf06\\ud834""
    ```
  
  - #### 模板字符串
  
    传统的 JS 语言，输出模板通常是这样写的（下面使用了 jQuery 的方法）。
  
    ```js
    $('#result').append(
      'There are <b>' + basket.count + '</b> ' +
      'items in your basket, ' +
      '<em>' + basket.onSale +
      '</em> are on sale!'
    );
    ```
  
    上面这种写法相当繁琐不方便，ES6 引入了模板字符串解决这个问题。
  
    ```js
    $('#result').append(`
      There are <b>${basket.count}</b> items
      in your basket, <em>${basket.onSale}</em>
      are on sale!
    `);
    ```
  
    模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。
  
    ```js
    // 普通字符串
    `In JavaScript '\n' is a line-feed.`
    
    // 多行字符串
    `In JavaScript this is
     not legal.`
    
    console.log(`string text line 1
    string text line 2`);
    
    // 字符串中嵌入变量
    let name = "Bob", time = "today";
    `Hello ${name}, how are you ${time}?`
    ```
  
    上面代码中的模板字符串，都是用反引号表示。如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。
  
    ```js
    let greeting = `\`Yo\` World!`;
    ```
  
    如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。
  
    ```js
    $('#list').html(`
    <ul>
      <li>first</li>
      <li>second</li>
    </ul>
    `);
    ```
  
    上面代码中，所有模板字符串的空格和换行，都是被保留的，比如`<ul>`标签前面会有一个换行。如果你不想要这个换行，可以使用`trim`方法消除它。
  
    ```js
    $('#list').html(`
    <ul>
      <li>first</li>
      <li>second</li>
    </ul>
    `.trim());
    ```
  
    模板字符串中嵌入变量，需要将变量名写在`${}`之中。
  
    ```js
    function authorize(user, action) {
      if (!user.hasPrivilege(action)) {
        throw new Error(
          // 传统写法为
          // 'User '
          // + user.name
          // + ' is not authorized to do '
          // + action
          // + '.'
          `User ${user.name} is not authorized to do ${action}.`);
      }
    }
    ```
  
    大括号内部可以放入任意的 JS 表达式，可以进行运算，以及引用对象属性。
  
    ```js
    let x = 1;
    let y = 2;
    
    `${x} + ${y} = ${x + y}`
    // "1 + 2 = 3"
    
    `${x} + ${y * 2} = ${x + y * 2}`
    // "1 + 4 = 5"
    
    let obj = {x: 1, y: 2};
    `${obj.x + obj.y}`
    // "3"
    ```
  
    模板字符串之中还能调用函数。
  
    ```js
    function fn() {
      return "Hello World";
    }
    
    `foo ${fn()} bar`
    // foo Hello World bar
    ```
  
    **如果大括号中的值不是字符串，将按照一般的规则转为字符串**。比如，大括号中是一个对象，将默认调用对象的`toString`方法。
  
    模板字符串甚至还能嵌套。
  
    ```js
    const tmpl = addrs => `
      <table>
      ${addrs.map(addr => `
        <tr><td>${addr.first}</td></tr>
        <tr><td>${addr.last}</td></tr>
      `).join('')}
      </table>
    `;
    ```
  
    上面代码中，模板字符串的变量之中，又嵌入了另一个模板字符串，使用方法如下。
  
    ```js
    const data = [
      { first: '<Jane>', last: 'Bond' },
      { first: 'Lars', last: '<Croft>' },
    ];
    
    console.log(tmpl(data));
    // <table>
    //
    //   <tr><td><Jane></td></tr>
    //   <tr><td>Bond</td></tr>
    //
    //   <tr><td>Lars</td></tr>
    //   <tr><td><Croft></td></tr>
    //
    // </table>
    ```
  
    如果需要引用模板字符串本身，在需要时执行，可以写成函数。
  
    ```js
    let func = (name) => `Hello ${name}!`;
    func('Jack') // "Hello Jack!"
    ```
  
    上面代码中，模板字符串写成了一个函数的返回值。执行这个函数，就相当于执行这个模板字符串了。
  
  - #### ~~（了解）实例：模板编译~~
  
    下面，我们来看一个通过模板字符串，生成正式模板的实例。
  
    ```js
    let template = `
    <ul>
      <% for(let i=0; i < data.supplies.length; i++) { %>
        <li><%= data.supplies[i] %></li>
      <% } %>
    </ul>
    `;
    ```
  
    上面代码在模板字符串之中，放置了一个常规模板。该模板使用`<%...%>`放置 JS 代码，使用`<%= ... %>`输出 JS 表达式。
  
    怎么编译这个模板字符串呢？
  
    一种思路是将其转换为 JS 表达式字符串。
  
    ```js
    echo('<ul>');
    for(let i=0; i < data.supplies.length; i++) {
      echo('<li>');
      echo(data.supplies[i]);
      echo('</li>');
    };
    echo('</ul>');
    ```
  
    这个转换使用正则表达式就行了。
  
    ```js
    let evalExpr = /<%=(.+?)%>/g;
    let expr = /<%([\s\S]+?)%>/g;
    
    template = template
      .replace(evalExpr, '`); \n  echo( $1 ); \n  echo(`')
      .replace(expr, '`); \n $1 \n  echo(`');
    
    template = 'echo(`' + template + '`);';
    ```
  
    然后，将`template`封装在一个函数里面返回，就可以了。
  
    ```js
    let script =
    `(function parse(data){
      let output = "";
    
      function echo(html){
        output += html;
      }
    
      ${ template }
    
      return output;
    })`;
    
    return script;
    ```
  
    将上面的内容拼装成一个模板编译函数`compile`。
  
    ```js
    function compile(template){
      const evalExpr = /<%=(.+?)%>/g;
      const expr = /<%([\s\S]+?)%>/g;
    
      template = template
        .replace(evalExpr, '`); \n  echo( $1 ); \n  echo(`')
        .replace(expr, '`); \n $1 \n  echo(`');
    
      template = 'echo(`' + template + '`);';
    
      let script =
      `(function parse(data){
        let output = "";
    
        function echo(html){
          output += html;
        }
    
        ${ template }
    
        return output;
      })`;
    
      return script;
    }
    ```
  
    `compile`函数的用法如下。
  
    ```js
    let parse = eval(compile(template));
    div.innerHTML = parse({ supplies: [ "broom", "mop", "cleaner" ] });
    //   <ul>
    //     <li>broom</li>
    //     <li>mop</li>
    //     <li>cleaner</li>
    //   </ul>
    ```
  
  - #### 标签模板
  
    模板字符串的功能，不仅仅是上面这些。它可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。这被称为“标签模板”功能（tagged template）。
  
    ```js
    alert`hello`
    // 等同于
    alert(['hello'])
    ```
  
    标签模板其实不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数。
  
    但是，如果模板字符里面有变量，就不是简单的调用了，而是会将模板字符串先处理成多个参数，再调用函数。
  
    ```js
    let a = 5;
    let b = 10;
    
    tag`Hello ${ a + b } world ${ a * b }`;
    // 等同于
    tag(['Hello ', ' world ', ''], 15, 50);
    ```
  
    上面代码中，模板字符串前面有一个标识名`tag`，它是一个函数。整个表达式的返回值，就是`tag`函数处理模板字符串后的返回值。
  
    函数`tag`依次会接收到多个参数。
  
    ```js
    function tag(stringArr, value1, value2){
      // ...
    }
    
    // 等同于
    
    function tag(stringArr, ...values){
      // ...
    }
    ```
  
    `tag`函数的**第一个参数是一个字符串数组**，该数组的成员是模板字符串中那些没有变量替换的部分，也就是说，变量替换只发生在数组的第一个成员与第二个成员之间、第二个成员与第三个成员之间，以此类推。
  
    `tag`函数的其他参数，都是模板字符串各个变量被替换后的值。由于本例中，模板字符串含有两个变量，因此`tag`会接受到`value1`和`value2`两个参数。
  
    `tag`函数所有参数的实际值如下。
  
    - 第一个参数：`['Hello ', ' world ', '']`
    - 第二个参数: 15
    - 第三个参数：50
  
    也就是说，`tag`函数实际上以下面的形式调用。
  
    ```js
    tag(['Hello ', ' world ', ''], 15, 50)
    ```
  
    我们可以按照需要编写`tag`函数的代码。下面是`tag`函数的一种写法，以及运行结果。
  
    ```js
    let a = 5;
    let b = 10;
    
    function tag(s, v1, v2) {
      console.log(s[0]);
      console.log(s[1]);
      console.log(s[2]);
      console.log(v1);
      console.log(v2);
    
      return "OK";
    }
    
    tag`Hello ${ a + b } world ${ a * b}`;
    // "Hello "
    // " world "
    // ""
    // 15
    // 50
    // "OK"
    ```
  
    下面是一个更复杂的例子。
  
    ```js
    let total = 30;
    let msg = passthru`The total is ${total} (${total*1.05} with tax)`;
    
    function passthru(literals) {
      let result = '';
      let i = 0;
    
      while (i < literals.length) {
        result += literals[i++];
        if (i < arguments.length) {
          result += arguments[i];
        }
      }
    
      return result;
    }
    
    msg // "The total is 30 (31.5 with tax)"
    ```
  
    上面这个例子展示了，如何将各个参数按照原来的位置拼合回去。
  
    `passthru`函数采用 rest 参数的写法如下。
  
    ```js
    function passthru(literals, ...values) {
      let output = "";
      let index;
      for (index = 0; index < values.length; index++) {
        output += literals[index] + values[index];
      }
    
      output += literals[index]
      return output;
    }
    ```
  
    “标签模板”的一个重要应用，就是过滤 HTML 字符串，防止用户输入恶意内容。
  
    ```js
    let message =
      SaferHTML`<p>${sender} has sent you a message.</p>`;
    
    function SaferHTML(templateData) {
      let s = templateData[0];
      for (let i = 1; i < arguments.length; i++) {
        let arg = String(arguments[i]);
    
        // Escape special characters in the substitution.
        s += arg.replace(/&/g, "&amp;")
                .replace(/</g, "&lt;")
                .replace(/>/g, "&gt;");
    
        // Don't escape special characters in the template.
        s += templateData[i];
      }
      return s;
    }
    ```
  
    上面代码中，`sender`变量往往是用户提供的，经过`SaferHTML`函数处理，里面的特殊字符都会被转义。
  
    ```js
    let sender = '<script>alert("abc")</script>'; // 恶意代码
    let message = SaferHTML`<p>${sender} has sent you a message.</p>`;
    
    message
    // <p>&lt;script&gt;alert("abc")&lt;/script&gt; has sent you a message.</p>
    ```
  
    标签模板的另一个应用，就是多语言转换（国际化处理）。
  
    ```js
    i18n`Welcome to ${siteName}, you are visitor number ${visitorNumber}!`
    // "欢迎访问xxx，您是第xxxx位访问者！"
    ```
  
    模板字符串本身并不能取代 Mustache 之类的模板库，因为没有条件判断和循环处理功能，但是通过标签函数，你可以自己添加这些功能。
  
    ```js
    // 下面的hashTemplate函数
    // 是一个自定义的模板处理函数
    let libraryHtml = hashTemplate`
      <ul>
        #for book in ${myBooks}
          <li><i>#{book.title}</i> by #{book.author}</li>
        #end
      </ul>
    `;
    ```
  
    除此之外，你甚至可以使用标签模板，在 JS 语言之中嵌入其他语言。
  
    ```js
    jsx`
      <div>
        <input
          ref='input'
          onChange='${this.handleChange}'
          defaultValue='${this.state.value}' />
          ${this.state.value}
       </div>
    `
    ```
  
    上面的代码通过`jsx`函数，将一个 DOM 字符串转为 React 对象。你可以在 GitHub 找到`jsx`函数的[具体实现](https://gist.github.com/lygaret/a68220defa69174bdec5)。
  
    下面则是一个假想的例子，通过`java`函数，在 JS 代码之中运行 Java 代码。
  
    ```js
    java`
    class HelloWorldApp {
      public static void main(String[] args) {
        System.out.println("Hello World!"); // Display the string.
      }
    }
    `
    HelloWorldApp.main();
    ```
  
    模板处理函数的第一个参数（模板字符串数组），还有一个`raw`属性。
  
    ```js
    console.log`123`
    // ["123", raw: Array[1]]
    ```
  
    上面代码中，`console.log`接受的参数，实际上是一个数组。该数组有一个**`raw`属性，保存的是转义后的原字符串**。
  
    请看下面的例子。
  
    ```js
    tag`First line\nSecond line`
    
    function tag(strings) {
      console.log(strings.raw[0]);
      // strings.raw[0] 为 "First line\\nSecond line"
      // 打印输出 "First line\nSecond line"
    }
    ```
  
    上面代码中，`tag`函数的第一个参数`strings`，有一个`raw`属性，也指向一个数组。该数组的成员与`strings`数组完全一致。比如，`strings`数组是`["First line\nSecond line"]`，那么`strings.raw`数组就是`["First line\\nSecond line"]`。两者唯一的区别，就是字符串里面的斜杠都被转义了。比如，strings.raw 数组会将`\n`视为`\\`和`n`两个字符，而不是换行符。这是为了方便取得转义之前的原始模板而设计的。
  
  - #### 模板字符串的限制
  
    前面提到标签模板里面，可以内嵌其他语言。但是，模板字符串默认会将字符串转义，导致无法嵌入其他语言。
  
    举例来说，标签模板里面可以嵌入 LaTEX 语言。
  
    ```js
    function latex(strings) {
      // ...
    }
    
    let document = latex`
    \newcommand{\fun}{\textbf{Fun!}}  // 正常工作
    \newcommand{\unicode}{\textbf{Unicode!}} // 报错
    \newcommand{\xerxes}{\textbf{King!}} // 报错
    
    Breve over the h goes \u{h}ere // 报错
    `
    ```
  
    上面代码中，变量`document`内嵌的模板字符串，对于 LaTEX 语言来说完全是合法的，但是 JS 引擎会报错。原因就在于字符串的转义。
  
    模板字符串会将`\u00FF`和`\u{42}`当作 Unicode 字符进行转义，所以`\unicode`解析时报错；而`\x56`会被当作十六进制字符串转义，所以`\xerxes`会报错。也就是说，`\u`和`\x`在 LaTEX 里面有特殊含义，但是 JS 将它们转义了。
  
    为了解决这个问题，ES2018 [放松](https://tc39.github.io/proposal-template-literal-revision/)了对标签模板里面的字符串转义的限制。如果遇到不合法的字符串转义，就返回`undefined`，而不是报错，并且从`raw`属性上面可以得到原始字符串。
  
    ```js
    function tag(strs) {
      strs[0] === undefined
      strs.raw[0] === "\\unicode and \\u{55}";
    }
    tag`\unicode and \u{55}`
    ```
  
    上面代码中，模板字符串原本是应该报错的，但是由于放松了对字符串转义的限制，所以不报错了，JS 引擎将第一个字符设置为`undefined`，但是`raw`属性依然可以得到原始字符串，因此`tag`函数还是可以对原字符串进行处理。
  
    注意，这种对字符串转义的放松，**只在标签模板解析字符串时生效**，不是标签模板的场合，依然会报错。
  
    ```js
    let bad = `bad escape sequence: \unicode`; // 报错
    ```
  
- ## 字符串的新增方法

  - #### 静态方法

    - `String.fromCodePoint()`：根据指定的 Unicode 码点返回一个字符串。该方法可以识别大于`0xFFFF`的字符，弥补了`String.fromCharCode()`方法的不足。在作用上，正好与下面的`codePointAt()`方法相反。

      ```js
      String.fromCodePoint(0x20BB7)  // "𠮷"
      String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'  // true
      ```
  
      上面代码中，如果`String.fromCodePoint`方法有多个参数，则它们对应的字符合并成一个字符串返回。
  
    - `String.raw(strings, ...substitutions)`：返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，常用于获取模板字符串的原始字符串形式。
  
      ```js
      String.raw`Hi\n${2+3}!`
      // 实际返回 "Hi\\n5!"，显示的是转义后的结果 "Hi\n5!"
      
      String.raw`Hi\u000A!`;
      // 实际返回 "Hi\\u000A!"，显示的是转义后的结果 "Hi\u000A!"
      ```

      如果原字符串的斜杠已经转义，那么`String.raw()`会进行再次转义。
  
      ```js
      String.raw`Hi\\n`
      // 返回 "Hi\\\\n"
      
      String.raw`Hi\\n` === "Hi\\\\n" // true
      ```
  
      `String.raw()`方法可以作为处理模板字符串的基本方法，它会将所有变量替换，而且对斜杠进行转义，方便下一步作为字符串来使用。
  
      `String.raw()`本质上是一个正常的函数，只是专用于模板字符串的**标签函数**。
  
      它的第一个参数是一个格式正确的模板字符串数组对象（伪数组），例如 `{ raw: ['foo', 'bar', 'baz'] }`，且具有 `raw` 属性。第二个参数是可变长参数，传入要在模板中替换的值。

      ```js
      // `foo${1 + 2}bar`
      // 等同于
      String.raw({ raw: ['foo', 'bar'] }, 1 + 2) // "foo3bar"
      ```
  
      上面代码中，`String.raw()`方法的第一个参数是一个对象，它的`raw`属性等同于原始的模板字符串解析后得到的数组。不过一般没人这么调用，很麻烦。
  
      作为函数，`String.raw()`的代码实现基本如下：

      ```js
      String.raw = function (strings, ...values) {
        let output = '';
        let index;
        for (index = 0; index < values.length; index++) {
          output += strings.raw[index] + values[index];
        }
      
        output += strings.raw[index]
        return output;
      }
      ```
  
  - #### 实例方法
  
    - `codePointAt(index)`：返回一个非负整数，该整数是从给定索引开始的字符的 Unicode 码点值。请注意，参数索引仍然基于 UTF-16 码元，而不是 Unicode 码位。
  
      JS 内部，字符以 UTF-16 的格式储存，每个字符固定为`2`个字节。对于那些需要`4`个字节储存的字符（Unicode 码点大于`0xFFFF`的字符），JS 会认为它们是两个字符。
  
      ```js
      var s = "𠮷";
      
      s.length // 2
      s.charAt(0) // ''
      s.charAt(1) // ''
      s.charCodeAt(0) // 55362
      s.charCodeAt(1) // 57271
      ```
  
      上面代码中，汉字“𠮷”（注意，这个字不是“吉祥”的“吉”）的码点是`0x20BB7`，UTF-16 编码为`0xD842 0xDFB7`（十进制为`55362 57271`），需要`4`个字节储存。对于这种`4`个字节的字符，JS 不能正确处理，字符串长度会误判为`2`，而且`charAt()`方法无法读取整个字符、`charCodeAt()`方法只能分别返回前两个字节和后两个字节的值。
  
      ES6 提供了`codePointAt()`方法，能够正确处理 4 个字节储存的字符，返回一个字符的 Unicode 码点。
  
      ```js
      let s = '𠮷a';
      
      s.codePointAt(0) // 134071
      s.codePointAt(1) // 57271
      
      s.codePointAt(2) // 97
      ```
  
      `codePointAt()`方法的参数，是字符在字符串中的位置（从 0 开始）。上面代码中，JS 将“𠮷a”视为三个字符，`codePointAt` 方法在第一个字符上，正确地识别了“𠮷”，返回了它的十进制码点 134071（即十六进制的`20BB7`）。在第二个字符（即“𠮷”的后两个字节）和第三个字符“a”上，`codePointAt()`方法的结果与`charCodeAt()`方法相同。
  
      总之，`codePointAt()`方法会正确返回 32 位的 UTF-16 字符的码点。对于那些两个字节储存的常规字符，它的返回结果与`charCodeAt()`方法相同。
  
      `codePointAt()`方法返回的是码点的十进制值，如果想要十六进制的值，可以使用`toString()`方法转换一下。
  
      ```js
      let s = '𠮷a';
      
      s.codePointAt(0).toString(16) // "20bb7"
      s.codePointAt(2).toString(16) // "61"
      ```
  
      你可能注意到了，`codePointAt()`方法的参数，仍然是不正确的。比如，上面代码中，字符`a`在字符串`s`的正确位置序号应该是 1，但是必须向`codePointAt()`方法传入 2。解决这个问题的一个办法是使用`for...of`循环，因为它会正确识别 32 位的 UTF-16 字符。
  
      ```js
      let s = '𠮷a';
      for (let ch of s) {
        console.log(ch.codePointAt(0).toString(16));
      }
      // 20bb7
      // 61
      ```
  
      另一种方法也可以，使用扩展运算符（`...`）进行展开运算。
  
      ```js
      let arr = [...'𠮷a']; // arr.length === 2
      arr.forEach(
        ch => console.log(ch.codePointAt(0).toString(16))
      );
      // 20bb7
      // 61
      ```
  
      `codePointAt()`方法是**测试一个字符由两个字节还是由四个字节组成**的最简单方法。
  
      ```js
      function is32Bit(c) {
        return c.codePointAt(0) > 0xFFFF;
      }
      
      is32Bit("𠮷") // true
      is32Bit("a") // false
      ```
  
    - `normalize()`：返回该字符串的 Unicode 标准化形式。
  
      许多欧洲语言有语调符号和重音符号。为了表示它们，Unicode 提供了两种方法。一种是直接新增一个带重音符号的字符，比如`Ǒ`（\u01D1）。另一种是提供合成符号（combining character），即原字符与重音符号的合成，两个字符合成一个字符，比如`O`（\u004F）和`ˇ`（\u030C）合成`Ǒ`（\u004F\u030C）。
  
      这两种表示方法，在视觉和语义上都等价，但是由于它们对应的 UTF-16 码元不同，因此在 JS 中是不相等。
  
      ```js
      '\u01D1'==='\u004F\u030C' //false
      
      '\u01D1'.length // 1
      '\u004F\u030C'.length // 2
      ```
  
      上面代码表示，JS 将合成字符视为两个字符，导致两种表示方法不相等。
  
      ES6 提供字符串实例的`normalize()`方法，用来**将字符的不同表示方法统一为同样的形式**，这称为 **Unicode 标准化**。
  
      ```js
      '\u01D1'.normalize() === '\u004F\u030C'.normalize() // true
      ```
  
      `normalize`方法可以接受一个参数来指定`normalize`的方式，参数的四个可选值如下：
  
      - `NFC`，默认参数，表示“标准等价合成”（Normalization Form Canonical Composition），返回多个简单字符的合成字符。所谓“标准等价”指的是视觉和语义上的等价。
      - `NFD`，表示“标准等价分解”（Normalization Form Canonical Decomposition），即在标准等价的前提下，返回合成字符分解的多个简单字符。
      - `NFKC`，表示“兼容等价合成”（Normalization Form Compatibility Composition），返回合成字符。所谓“兼容等价”指的是语义上存在等价，但视觉上不等价，比如“囍”和“喜喜”。（这只是用来举例，`normalize`方法不能识别中文。）
      - `NFKD`，表示“兼容等价分解”（Normalization Form Compatibility Decomposition），即在兼容等价的前提下，返回合成字符分解的多个简单字符。
  
      ```js
      '\u004F\u030C'.normalize('NFC').length // 1
      '\u004F\u030C'.normalize('NFD').length // 2
      ```
  
      上面代码表示，`NFC`参数返回字符的合成形式，`NFD`参数返回字符的分解形式。
  
      不过，**`normalize`方法目前不能识别三个或三个以上字符的合成**。这种情况下，还是只能使用正则表达式，通过 Unicode 编号区间判断。
  
    - `includes()`, `startsWith()`, `endsWith()`：传统上，JS 只有`indexOf`方法，可以用来确定一个字符串是否包含在另一个字符串中。ES6 又提供了三种新方法：
  
      - `includes()`：返回布尔值，表示是否找到了参数字符串。
      - `startsWith()`：返回布尔值，表示参数字符串是否在原字符串的头部。
      - `endsWith()`：返回布尔值，表示参数字符串是否在原字符串的尾部。
  
      ```js
      let s = 'Hello world!';
      
      s.startsWith('Hello') // true
      s.endsWith('!') // true
      s.includes('o') // true
      ```
  
      这三个方法都支持第二个参数，表示开始搜索的位置。
  
      ```js
      let s = 'Hello world!';
      
      s.startsWith('world', 6) // true
      s.endsWith('Hello', 5) // true
      s.includes('Hello', 6) // false
      ```
  
      上面代码表示，使用第二个参数`n`时，`endsWith`的行为与其他两个方法有所不同。它针对前`n`个字符，而其他两个方法针对从第`n`个位置直到字符串结束。
  
    - `repeat()`：返回一个新字符串，表示将原字符串重复`n`次。
  
      ```js
      'x'.repeat(3) // "xxx"
      'hello'.repeat(2) // "hellohello"
      'na'.repeat(0) // ""
      ```
  
      参数如果是小数，会被取整。
  
      ```js
      'na'.repeat(2.9) // "nana"
      ```
  
      如果`repeat`的参数是负数或者`Infinity`，会报错。
  
      ```js
      'na'.repeat(Infinity)
      // RangeError
      'na'.repeat(-1)
      // RangeError
      ```
  
      但是，如果参数是 0 到-1 之间的小数，则等同于 0，这是因为会先进行取整运算。0 到-1 之间的小数，取整以后等于`-0`，`repeat`视同为 0。
  
      ```js
      'na'.repeat(-0.9) // ""
      ```
  
      参数`NaN`等同于 0。
  
      ```js
      'na'.repeat(NaN) // ""
      ```
  
      如果`repeat`的参数是字符串，则会先转换成数字。
  
      ```js
      'na'.repeat('na') // ""
      'na'.repeat('3') // "nanana"
      ```
  
    - `padStart()`，`padEnd()`：ES2017 引入了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。`padStart()`用于头部补全，`padEnd()`用于尾部补全。
  
      ```js
      'x'.padStart(5, 'ab') // 'ababx'
      'x'.padStart(4, 'ab') // 'abax'
      
      'x'.padEnd(5, 'ab') // 'xabab'
      'x'.padEnd(4, 'ab') // 'xaba'
      ```
  
      上面代码中，`padStart()`和`padEnd()`一共接受两个参数，第一个参数是字符串补全生效的最大长度，第二个参数是用来补全的字符串。
  
      如果原字符串的长度，等于或大于最大长度，则字符串补全不生效，返回原字符串。
  
      ```js
      'xxx'.padStart(2, 'ab') // 'xxx'
      'xxx'.padEnd(2, 'ab') // 'xxx'
      ```
  
      如果用来补全的字符串与原字符串，两者的长度之和超过了最大长度，则会截去超出位数的补全字符串。
  
      ```js
      'abc'.padStart(10, '0123456789')
      // '0123456abc'
      ```
  
      如果省略第二个参数，默认使用空格补全长度。
  
      ```js
      'x'.padStart(4) // '   x'
      'x'.padEnd(4) // 'x   '
      ```
  
      `padStart()`的常见用途是为数值补全指定位数。下面代码生成 10 位的数值字符串。
  
      ```js
      '1'.padStart(10, '0') // "0000000001"
      '12'.padStart(10, '0') // "0000000012"
      '123456'.padStart(10, '0') // "0000123456"
      ```
  
      另一个用途是提示字符串格式。
  
      ```js
      '12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
      '09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
      ```
  
    - `trimStart()`，`trimEnd()`：[ES2019](https://github.com/tc39/proposal-string-left-right-trim) 对字符串实例新增了`trimStart()`和`trimEnd()`这两个方法。它们的行为与`trim()`一致，`trimStart()`消除字符串头部的空格，`trimEnd()`消除尾部的空格。它们返回的都是新字符串，不会修改原始字符串。
  
      ```js
      const s = '  abc  ';
      
      s.trim() // "abc"
      s.trimStart() // "abc  "
      s.trimEnd() // "  abc"
      ```
  
      上面代码中，`trimStart()`只消除头部的空格，保留尾部的空格。`trimEnd()`也是类似行为。
  
      除了空格键，这两个方法对字符串头部（或尾部）的 tab 键、换行符等不可见的空白符号也有效。
  
      浏览器还部署了额外的两个方法，`trimLeft()`是`trimStart()`的别名，`trimRight()`是`trimEnd()`的别名。
  
    - `matchAll()`：返回一个正则表达式在当前字符串的所有匹配，详见《正则的扩展》的一章。
  
    - `replaceAll()`：字符串的实例方法`replace()`只能替换第一个匹配。
  
      ```js
      'aabbcc'.replace('b', '_')
      // 'aa_bcc'
      ```
  
      上面例子中，`replace()`只将第一个`b`替换成了下划线。
  
      如果要替换所有的匹配，不得不使用正则表达式的`g`修饰符。
  
      ```js
      'aabbcc'.replace(/b/g, '_')
      // 'aa__cc'
      ```
  
      正则表达式毕竟不是那么方便和直观，[ES2021](https://github.com/tc39/proposal-string-replaceall) 引入了`replaceAll()`方法，可以一次性替换所有匹配。
  
      ```js
      'aabbcc'.replaceAll('b', '_')
      // 'aa__cc'
      ```
  
      它的用法与`replace()`相同，返回一个新字符串，不会改变原字符串。
  
      ```js
      String.prototype.replaceAll(searchValue, replacement)
      ```
  
      上面代码中，`searchValue`是搜索模式，可以是一个字符串，也可以是一个全局的正则表达式（带有`g`修饰符）。
  
      如果`searchValue`是一个不带有`g`修饰符的正则表达式，`replaceAll()`会报错。这一点跟`replace()`不同。
  
      ```js
      // 不报错
      'aabbcc'.replace(/b/, '_')
      
      // 报错
      'aabbcc'.replaceAll(/b/, '_')
      ```
  
      上面例子中，`/b/`不带有`g`修饰符，会导致`replaceAll()`报错。
  
      `replaceAll()`的第二个参数`replacement`是一个字符串，表示替换的文本，其中可以使用一些特殊字符串。
  
      - `$&`：匹配的字符串。
      - `$``：匹配结果前面的文本。
      - `$'`：匹配结果后面的文本。
      - `$n`：匹配成功的第`n`组内容，`n`是从1开始的自然数。这个参数生效的前提是，第一个参数必须是正则表达式。
      - `$$`：指代美元符号`$`。
  
      下面是一些例子。
  
      ```js
      // $& 表示匹配的字符串，即`b`本身
      // 所以返回结果与原字符串一致
      'abbc'.replaceAll('b', '$&')
      // 'abbc'
      
      // $` 表示匹配结果之前的字符串
      // 对于第一个`b`，$` 指代`a`
      // 对于第二个`b`，$` 指代`ab`
      'abbc'.replaceAll('b', '$`')
      // 'aaabc'
      
      // $' 表示匹配结果之后的字符串
      // 对于第一个`b`，$' 指代`bc`
      // 对于第二个`b`，$' 指代`c`
      'abbc'.replaceAll('b', `$'`)
      // 'abccc'
      
      // $1 表示正则表达式的第一个组匹配，指代`ab`
      // $2 表示正则表达式的第二个组匹配，指代`bc`
      'abbc'.replaceAll(/(ab)(bc)/g, '$2$1')
      // 'bcab'
      
      // $$ 指代 $
      'abc'.replaceAll('b', '$$')
      // 'a$c'
      ```
  
      `replaceAll()`的第二个参数`replacement`除了为字符串，也可以是一个函数，该函数的返回值将替换掉第一个参数`searchValue`匹配的文本。
  
      ```js
      'aabbcc'.replaceAll('b', () => '_')
      // 'aa__cc'
      ```
  
      上面例子中，`replaceAll()`的第二个参数是一个函数，该函数的返回值会替换掉所有`b`的匹配。
  
      这个替换函数可以接受多个参数。第一个参数是捕捉到的匹配内容，第二个参数是捕捉到的组匹配（有多少个组匹配，就有多少个对应的参数）。此外，最后还可以添加两个参数，倒数第二个参数是捕捉到的内容在整个字符串中的位置，最后一个参数是原字符串。
  
      ```js
      const str = '123abc456';
      const regex = /(\d+)([a-z]+)(\d+)/g;
      
      function replacer(match, p1, p2, p3, offset, string) {
        return [p1, p2, p3].join(' - ');
      }
      
      str.replaceAll(regex, replacer)
      // 123 - abc - 456
      ```
  
      上面例子中，正则表达式有三个组匹配，所以`replacer()`函数的第一个参数`match`是捕捉到的匹配内容（即字符串`123abc456`），后面三个参数`p1`、`p2`、`p3`则依次为三个组匹配。
  
    - `at()`：接受一个整数值并返回一个新的字符串，该字符串由位于指定偏移量处的单个 UTF-16 码元组成。和`[]`不同的是，该方法支持负整数。负整数从字符串中的最后一个字符开始倒数。
  
      ```js
      const str = 'hello';
      str.at(1) // "e"
      str.at(-1) // "o"
      ```
  
      如果参数位置超出了字符串范围，`at()`返回`undefined`。
  
    - `toWellFormed()`：返回一个字符串，其中该字符串的所有[单独代理项](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String#utf-16_字符、unicode_码位和字素簇)都被替换为 Unicode 替换字符 U+FFFD。
  
      ES2024 引入了新的字符串方法`toWellFormed()`，用来处理 Unicode 的代理字符对问题（surrogates）。
  
      JS 语言内部使用 UTF-16 格式，表示每个字符。UTF-16 只有16位，只能表示码点在`U+0000`到`U+FFFF`之间的 Unicode 字符。对于码点大于`U+FFFF`的 Unicode 字符（即码点大于16位的字符，`U+10000`到`U+10FFFF`），解决办法是使用代理字符对，即用两个 UTF-16 字符组合表示。
  
      具体来说，UTF-16 规定，`U+D800`至`U+DFFF`是空字符段，专门留给代理字符对使用。只要遇到这个范围内的码点，就知道它是代理字符对，本身没有意义，必须两个字符结合在一起解读。其中，前一个字符的范围规定为`0xD800`到`0xDBFF`之间，后一个字符的范围规定为`0xDC00`到`0xDFFF`之间。举例来说，码点`U+1D306`对应的字符为`𝌆`，它写成 UTF-16 就是`0xD834 0xDF06`。
  
      但是，字符串里面可能会出现单个代理字符对，即`U+D800`至`U+DFFF`里面的字符，它没有配对的另一个字符，无法进行解读，导致出现各种状况。
  
      `toWellFormed()`方法就是为了解决这个问题，它不改变原始字符串，返回一个新的字符串，将原始字符串里面的单个代理字符对，都替换为`U+FFFD`，从而可以在任何正常处理字符串的函数里面使用。
  
      ```js
      "ab\uD800".toWellFormed() // 'ab�'
      ```
  
      上面示例中，`\uD800`是单个的代理字符对，单独使用时没有意义。`toWellFormed()`将这个字符转为`\uFFFD`。
  
      再看下面的例子，`encodeURI()`遇到单个的代理字符对，会报错。
  
      ```js
      const illFormed = "https://example.com/search?q=\uD800";
      
      encodeURI(illFormed) // 报错
      ```
  
      `toWellFormed()`将其转换格式后，再使用`encodeURI()`就不会报错了。
  
      ```js
      const illFormed = "https://example.com/search?q=\uD800";
      
      encodeURI(illFormed.toWellFormed()) // 正确
      ```
  
- ## 数值的扩展

  - #### 数值分隔符

    欧美语言中，较长的数值允许每三位添加一个分隔符（通常是一个逗号），增加数值的可读性。比如，`1000`可以写作`1,000`。

    [ES2021](https://github.com/tc39/proposal-numeric-separator)，允许 JS 的数值使用下划线（`_`）作为分隔符。

    ```js
    let budget = 1_000_000_000_000;
    budget === 10 ** 12 // true
    ```

    这个数值分隔符没有指定间隔的位数，也就是说，可以每三位添加一个分隔符，也可以每一位、每两位、每四位添加一个。

    ```js
    123_00 === 12_300 // true
    
    12345_00 === 123_4500 // true
    12345_00 === 1_234_500 // true
    ```

    小数和科学计数法也可以使用数值分隔符。

    ```js
    // 小数
    0.000_001
    
    // 科学计数法
    1e10_000
    ```

    数值分隔符有几个使用注意点：

    - 不能放在数值的最前面（leading）或最后面（trailing）。
    - 不能两个或两个以上的分隔符连在一起。
    - 小数点的前后不能有分隔符。
    - 科学计数法里面，表示指数的`e`或`E`前后不能有分隔符。

    下面的写法都会报错。

    ```js
    // 全部报错
    3_.141
    3._141
    1_e12
    1e_12
    123__456
    _1464301
    1464301_
    ```

    除了十进制，其他进制的数值也可以使用分隔符。

    ```js
    // 二进制
    0b1010_0001_1000_0101
    // 十六进制
    0xA0_B0_C0
    ```

    可以看到，数值分隔符可以按字节顺序分隔数值，这在操作二进制位时，非常有用。

    注意，分隔符不能紧跟着进制的前缀`0b`、`0B`、`0o`、`0O`、`0x`、`0X`。

    ```js
    // 报错
    0_b111111000
    0b_111111000
    ```

    数值分隔符只是一种书写便利，对于 JS 内部数值的存储和输出，并没有影响。

    ```js
    let num = 12_345;
    
    num // 12345
    num.toString() // 12345
    ```

    上面示例中，变量`num`的值为`12_345`，但是内部存储和输出的时候，都不会有数值分隔符。

    当使用这3个函数将字符串转成数值时：`Number()\Number.parseInt()\Number.parseFloat()`，不支持数字字符串中使用数值分隔符。主要原因是语言的设计者认为，数值分隔符主要是为了编码时书写数值的方便，而不是为了处理外部输入的数据。

    ```js
    Number('123_456') // NaN
    parseInt('123_456') // 123
    ```

  - #### `Number.isInteger()`

    `Number.isInteger()`用来判断一个数值是否为整数。

    ```js
  Number.isInteger(25) // true
    Number.isInteger(25.1) // false
    ```
  
    JS 内部，整数和浮点数采用的是同样的储存方法，所以 25 和 25.0 被视为同一个值。
  
    ```js
    Number.isInteger(25) // true
    Number.isInteger(25.0) // true
    ```
  
  如果参数不是数值，`Number.isInteger`返回`false`。
  
  ```js
    Number.isInteger() // false
  Number.isInteger(null) // false
    Number.isInteger('15') // false
    Number.isInteger(true) // false
    ```
  
    注意，由于 JS 采用 IEEE 754 标准，数值存储为64位双精度格式，数值精度最多可以达到 53 个二进制位（1 个隐藏位与 52 个有效位）。如果数值的精度超过这个限度，第54位及后面的位就会被丢弃，这种情况下，**`Number.isInteger`可能会误判**。
  
    ```js
    Number.isInteger(3.0000000000000002) // true
    ```

    上面代码中，`Number.isInteger`的参数明明不是整数，但是会返回`true`。原因就是这个小数的精度达到了小数点后16个十进制位，转成二进制位超过了53个二进制位，导致最后的那个`2`被丢弃了。

    类似的情况还有，如果一个数值的绝对值小于`Number.MIN_VALUE`（5E-324），即小于 JS 能够分辨的最小值，会被自动转为 0。这时，`Number.isInteger`也会误判。

    ```js
    Number.isInteger(5E-324) // false
    Number.isInteger(5E-325) // true
    ```
  
    上面代码中，`5E-325`由于值太小，会被自动转为0，因此返回`true`。
  
    总之，如果对数据精度的要求较高，不建议使用`Number.isInteger()`判断一个数值是否为整数。
  
  - #### `Number.EPSILON`
  
    **Epsilon**（希腊字母 `ε`）在数学和计算机科学中，传统上用来表示 **一个非常小的正数**，通常代表"可忽略的误差"或"最小的有意义增量"。

    ES6 在`Number`对象上面，新增一个极小的常量`Number.EPSILON`（ε）。根据规格，它表示 1 与大于 1 的最小浮点数之间的差。

    对于 64 位浮点数来说，大于 1 的最小浮点数相当于二进制的`1.00..001`，小数点后面有连续 51 个零。这个值减去 1 之后，就等于 2 的 -52 次方。（与`number`类型的精度有关）

    ```js
  Number.EPSILON === Math.pow(2, -52)
    // true
    Number.EPSILON
    // 2.220446049250313e-16
    Number.EPSILON.toFixed(20)
  // "0.00000000000000022204"
    ```

    **`Number.EPSILON`实际上是 JS 能够表示的最小精度**。误差如果小于这个值，就可以认为已经没有意义了，即不存在误差了。
  
    引入一个这么小的量的目的，在于为浮点数计算，设置一个误差范围。我们知道浮点数计算是不精确的。
  
  ```js
    0.1 + 0.2
  // 0.30000000000000004
    
    0.1 + 0.2 - 0.3
    // 5.551115123125783e-17
    
    5.551115123125783e-17.toFixed(20)
    // '0.00000000000000005551'
  ```
  
  上面代码解释了，为什么比较`0.1 + 0.2`与`0.3`得到的结果是`false`。
  
    ```js
    0.1 + 0.2 === 0.3 // false
  ```
  
  `Number.EPSILON`可以用来设置“能够接受的误差范围”。比如，误差范围设为 2 的-50 次方（即`Number.EPSILON * Math.pow(2, 2)`），即如果两个浮点数的差小于这个值，我们就认为这两个浮点数相等。
  
  ```js
    5.551115123125783e-17 < Number.EPSILON * Math.pow(2, 2)  // true
    ```
  
    因此，`Number.EPSILON`的实质是一个可以接受的最小误差范围。

    ```js
  function withinErrorMargin (left, right) {
      return Math.abs(left - right) < Number.EPSILON * Math.pow(2, 2);
  }
    
  0.1 + 0.2 === 0.3 // false
    withinErrorMargin(0.1 + 0.2, 0.3) // true
  
    1.1 + 1.3 === 2.4 // false
  withinErrorMargin(1.1 + 1.3, 2.4) // true
    ```
  
    上面的代码为浮点数运算，部署了一个误差检查函数。
  
  - #### 安全整数和 `Number.isSafeInteger()`
  
    JS 能够准确表示的整数范围在`(-2^53 - 2^53)`之间（不含两个端点），超过这个范围，无法精确表示这个值。
  
  ```js
    Math.pow(2, 53) // 9007199254740992
  
    9007199254740992  // 9007199254740992
  9007199254740993  // 9007199254740992
    
    Math.pow(2, 53) === Math.pow(2, 53) + 1
    // true
    ```
  
    上面代码中，超出 2 的 53 次方之后，一个数就不精确了。
  
    ES6 引入了`Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_INTEGER`这两个常量，用来表示这个范围的上下限。
  
    ```js
  Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
    // true
  Number.MAX_SAFE_INTEGER === 9007199254740991
    // true
    
    Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
  // true
    Number.MIN_SAFE_INTEGER === -9007199254740991
  // true
    ```
  
    上面代码中，可以看到 JS 能够精确表示的极限。

    `Number.isSafeInteger()`则是用来判断一个整数是否落在这个范围之内。

    ```js
    Number.isSafeInteger('a') // false
    Number.isSafeInteger(null) // false
    Number.isSafeInteger(NaN) // false
    Number.isSafeInteger(Infinity) // false
    Number.isSafeInteger(-Infinity) // false
    
    Number.isSafeInteger(3) // true
    Number.isSafeInteger(1.2) // false
    Number.isSafeInteger(9007199254740990) // true
    Number.isSafeInteger(9007199254740992) // false
  
    Number.isSafeInteger(Number.MIN_SAFE_INTEGER - 1) // false
  Number.isSafeInteger(Number.MIN_SAFE_INTEGER) // true
    Number.isSafeInteger(Number.MAX_SAFE_INTEGER) // true
  Number.isSafeInteger(Number.MAX_SAFE_INTEGER + 1) // false
    ```

    这个函数的实现很简单，就是跟安全整数的两个边界值比较一下。
  
    ```js
    Number.isSafeInteger = function (n) {
      return (typeof n === 'number' &&
        Math.round(n) === n &&
        Number.MIN_SAFE_INTEGER <= n &&
        n <= Number.MAX_SAFE_INTEGER);
    }
  ```
  
  实际使用这个函数时，需要注意。**验证运算结果是否落在安全整数的范围内，不要只验证运算结果，而要同时验证参与运算的每个值。**
  
  ```js
    Number.isSafeInteger(9007199254740993)
    // false
    Number.isSafeInteger(990)
    // true
    Number.isSafeInteger(9007199254740993 - 990)
    // true
    9007199254740993 - 990
    // 返回结果 9007199254740002
    // 正确答案应该是 9007199254740003
    ```
  
  上面代码中，`9007199254740993`不是一个安全整数，但是`Number.isSafeInteger`会返回结果，显示计算结果是安全的。这是因为，这个数超出了精度范围，导致在计算机内部，以`9007199254740992`的形式储存。
  
  ```js
    9007199254740993 === 9007199254740992  // true
  ```
  
    所以，如果只验证运算结果是否为安全整数，很可能得到错误结果。下面的函数可以同时验证两个运算数和运算结果。
  
    ```js
    function trusty (left, right, result) {
      if (
        Number.isSafeInteger(left) &&
        Number.isSafeInteger(right) &&
        Number.isSafeInteger(result)
      ) {
        return result;
      }
      throw new RangeError('Operation cannot be trusted!');
    }
    
    trusty(9007199254740993, 990, 9007199254740993 - 990)
    // RangeError: Operation cannot be trusted!
  
    trusty(1, 2, 3)
  // 3
    ```
  
  - #### `Math` 对象的扩展
  
    ES6 在 Math 对象上新增了 17 个与数学相关的方法。所有这些方法都是静态方法，只能在 Math 对象上调用。
  
    - `Math.trunc()`：去除一个数的小数部分，返回整数部分。
  
    ```js
      Math.trunc(4.1) // 4
    Math.trunc(4.9) // 4
      Math.trunc(-4.1) // -4
      Math.trunc(-4.9) // -4
      Math.trunc(-0.1234) // -0
      ```
  
      对于非数值，`Math.trunc`内部使用`Number`方法将其先转为数值。
  
      ```js
      Math.trunc('123.456') // 123
      Math.trunc(true) //1
      Math.trunc(false) // 0
    Math.trunc(null) // 0
      ```

      对于空值和无法截取整数的值，返回`NaN`。
  
      ```js
    Math.trunc(NaN);      // NaN
      Math.trunc('foo');    // NaN
    Math.trunc();         // NaN
      Math.trunc(undefined) // NaN
      ```
  
      对于没有部署这个方法的环境，可以用下面的代码模拟。
  
      ```js
      Math.trunc = Math.trunc || function(x) {
        return x < 0 ? Math.ceil(x) : Math.floor(x);
      };
      ```
  
    - `Math.sign()`：判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。
  
      它会返回五种值。
  
      - 参数为正数，返回`+1`；
      - 参数为负数，返回`-1`；
      - 参数为 0，返回`0`；
    - 参数为-0，返回`-0`;
      - 其他值，返回`NaN`。

      ```js
    Math.sign(-5) // -1
      Math.sign(5) // +1
    Math.sign(0) // +0
      Math.sign(-0) // -0
      Math.sign(NaN) // NaN
      ```
  
      如果参数是非数值，会自动转为数值。对于那些无法转为数值的值，会返回`NaN`。
  
      ```js
    Math.sign('')  // 0
      Math.sign(true)  // +1
    Math.sign(false)  // 0
      Math.sign(null)  // 0
      Math.sign('9')  // +1
      Math.sign('foo')  // NaN
      Math.sign()  // NaN
      Math.sign(undefined)  // NaN
      ```

      对于没有部署这个方法的环境，可以用下面的代码模拟。

      ```js
      Math.sign = Math.sign || function(x) {
        x = +x; // convert to a number
        if (x === 0 || isNaN(x)) {
          return x;
        }
      return x > 0 ? 1 : -1;
      };
    ```
  
    - `Math.cbrt()`：返回一个数的立方根。
  
      ```js
      Math.cbrt(-1) // -1
    Math.cbrt(0)  // 0
      Math.cbrt(1)  // 1
    Math.cbrt(2)  // 1.2599210498948732
      ```

      对于非数值，`Math.cbrt()`方法内部也是先使用`Number()`方法将其转为数值。
  
      ```js
      Math.cbrt('8') // 2
      Math.cbrt('hello') // NaN
    ```
  
      对于没有部署这个方法的环境，可以用下面的代码模拟。
  
      ```js
      Math.cbrt = Math.cbrt || function(x) {
        var y = Math.pow(Math.abs(x), 1/3);
        return x < 0 ? -y : y;
    };
      ```

    - `Math.clz32()`：将参数转为 32 位无符号整数的形式，然后返回这个 32 位值里面有多少个前导 0。
  
      ```js
      Math.clz32(0) // 32
      Math.clz32(1) // 31
      Math.clz32(1000) // 22
      Math.clz32(0b01000000000000000000000000000000) // 1
      Math.clz32(0b00100000000000000000000000000000) // 2
      ```
  
    上面代码中，0 的二进制形式全为 0，所以有 32 个前导 0；1 的二进制形式是`0b1`，只占 1 位，所以 32 位之中有 31 个前导 0；1000 的二进制形式是`0b1111101000`，一共有 10 位，所以 32 位之中有 22 个前导 0。
  
    `clz32`这个函数名就来自”count leading zero bits in 32-bit binary representation of a number“（计算一个数的 32 位二进制形式的前导 0 的个数）的缩写。
  
      左移运算符（`<<`）与`Math.clz32`方法直接相关。
  
      ```js
      Math.clz32(0) // 32
      Math.clz32(1) // 31
      Math.clz32(1 << 1) // 30
      Math.clz32(1 << 2) // 29
      Math.clz32(1 << 29) // 2
    ```
  
    对于小数，`Math.clz32`方法只考虑整数部分。
  
      ```js
      Math.clz32(3.2) // 30
      Math.clz32(3.9) // 30
      ```
  
    对于空值或其他类型的值，`Math.clz32`方法会将它们先转为数值，然后再计算。
  
    ```js
      Math.clz32() // 32
      Math.clz32(NaN) // 32
      Math.clz32(Infinity) // 32
      Math.clz32(null) // 32
    Math.clz32('foo') // 32
      Math.clz32([]) // 32
    Math.clz32({}) // 32
      Math.clz32(true) // 31
      ```
  
    - `Math.imul()`：返回两个数以 32 位带符号整数形式相乘的结果，返回的也是一个 32 位的带符号整数。
  
      ```js
    Math.imul(2, 4)   // 8
      Math.imul(-1, 8)  // -8
    Math.imul(-2, -2) // 4
      ```
  
      如果只考虑最后 32 位，大多数情况下，`Math.imul(a, b)`与`a * b`的结果是相同的，即该方法等同于`(a * b)|0`的效果（超过 32 位的部分溢出）。之所以需要部署这个方法，是因为 JS 有精度限制，超过 2 的 53 次方的值无法精确表示。这就是说，对于那些很大的数的乘法，低位数值往往都是不精确的，`Math.imul`方法可以返回正确的低位数值。
  
      ```js
      (0x7fffffff * 0x7fffffff)|0 // 0
      ```

      上面这个乘法算式，返回结果为 0。但是由于这两个二进制数的最低位都是  1，所以这个结果肯定是不正确的，因为根据二进制乘法，计算结果的二进制最低位应该也是 1。这个错误就是因为它们的乘积超过了 2 的 53  次方，JS 无法保存额外的精度，就把低位的值都变成了 0。`Math.imul`方法可以返回正确的值 1。

      ```js
    Math.imul(0x7fffffff, 0x7fffffff) // 1
      ```

    - `Math.fround()`：返回一个数的32位单精度浮点数形式。
  
      对于32位单精度格式来说，数值精度是24个二进制位（1 位隐藏位与 23 位有效位），所以对于 -224 至 224 之间的整数（不含两个端点），返回结果与参数本身一致。
  
      ```js
      Math.fround(0)   // 0
      Math.fround(1)   // 1
    Math.fround(2 ** 24 - 1)   // 16777215
      ```

      如果参数的绝对值大于 224，返回的结果便开始丢失精度。
  
      ```js
      Math.fround(2 ** 24)       // 16777216
    Math.fround(2 ** 24 + 1)   // 16777216
      ```

      `Math.fround`方法的主要作用，是**将64位双精度浮点数转为32位单精度浮点数**。如果小数的精度超过24个二进制位，返回值就会不同于原值，否则返回值不变（即与64位双精度值一致）。
  
      ```js
      // 未丢失有效精度
      Math.fround(1.125) // 1.125
      Math.fround(7.25)  // 7.25
      
      // 丢失精度
      Math.fround(0.3)   // 0.30000001192092896
      Math.fround(0.7)   // 0.699999988079071
    Math.fround(1.0000000123) // 1
      ```

      对于 `NaN` 和 `Infinity`，此方法返回原值。对于其它类型的非数值，`Math.fround` 方法会先将其转为数值，再返回单精度浮点数。
  
      ```js
      Math.fround(NaN)      // NaN
      Math.fround(Infinity) // Infinity
    
      Math.fround('5')      // 5
    Math.fround(true)     // 1
      Math.fround(null)     // 0
      Math.fround([])       // 0
      Math.fround({})       // NaN
    ```
  
    对于没有部署这个方法的环境，可以用下面的代码模拟。
  
      ```js
      Math.fround = Math.fround || function (x) {
      return new Float32Array([x])[0];
      };
    ```
  
  - `Math.hypot()`：返回所有参数的平方和的平方根。
  
      ```js
      Math.hypot(3, 4);        // 5
      Math.hypot(3, 4, 5);     // 7.0710678118654755
      Math.hypot();            // 0
    Math.hypot(NaN);         // NaN
      Math.hypot(3, 4, 'foo'); // NaN
    Math.hypot(3, 4, '5');   // 7.0710678118654755
      Math.hypot(-3);          // 3
      ```
  
      上面代码中，3 的平方加上 4 的平方，等于 5 的平方。

      如果参数不是数值，`Math.hypot`方法会将其转为数值。只要有一个参数无法转为数值，就会返回 `NaN`。

    - `Math.f16round()`：ES2025 新增了 `Math.f16round()` 方法，返回最接近输入值的16位半精度浮点数。
  
      ```js
      Math.f16round(5) // 5
      Math.f16round(5.05) // 5.05078125
      ```
  
      16位浮点数共使用16个二进制位，其中指数使用5位，符号位使用1位，精度使用10位，因此可以表示 ±65,504 范围内的值，精度可以到达 1/1024。如果一个数超出了值的范围，则该方法返回 `Infinity`。
  
      ```js
    Math.f16round(100000) // Infinity
      ```

    - ##### 对数方法：
  
      ES6 新增了 4 个对数相关方法。
  
      - `Math.expm1()`：返回 e^x - 1，即`Math.exp(x) - 1`。
  
        ```js
        Math.expm1(-1) // -0.6321205588285577
        Math.expm1(0)  // 0
        Math.expm1(1)  // 1.718281828459045
      ```
  
      对于没有部署这个方法的环境，可以用下面的代码模拟。
  
        ```js
        Math.expm1 = Math.expm1 || function(x) {
          return Math.exp(x) - 1;
        };
      ```
  
    - `Math.log1p()`：返回`1 + x`的自然对数，即`Math.log(1 + x)`。如果`x`小于`-1`，返回`NaN`。
  
        ```js
        Math.log1p(1)  // 0.6931471805599453
        Math.log1p(0)  // 0
        Math.log1p(-1) // -Infinity
        Math.log1p(-2) // NaN
        ```
  
        对于没有部署这个方法的环境，可以用下面的代码模拟。

        ```js
      Math.log1p = Math.log1p || function(x) {
          return Math.log(1 + x);
      };
        ```

      - `Math.log10()`：返回以 10 为底的`x`的对数。如果`x`小于 0，则返回 `NaN`。

        ```js
      Math.log10(2)      // 0.3010299956639812
        Math.log10(1)      // 0
      Math.log10(0)      // -Infinity
        Math.log10(-2)     // NaN
        Math.log10(100000) // 5
        ```
  
        对于没有部署这个方法的环境，可以用下面的代码模拟。

        ```js
      Math.log10 = Math.log10 || function(x) {
          return Math.log(x) / Math.LN10;
        };
        ```
  
      - `Math.log2()`：返回以 2 为底的`x`的对数。如果`x`小于 0，则返回 `NaN`。

        ```js
      Math.log2(3)       // 1.584962500721156
        Math.log2(2)       // 1
      Math.log2(1)       // 0
        Math.log2(0)       // -Infinity
        Math.log2(-2)      // NaN
        Math.log2(1024)    // 10
        Math.log2(1 << 29) // 29
        ```
  
      对于没有部署这个方法的环境，可以用下面的代码模拟。
  
      ```js
        Math.log2 = Math.log2 || function(x) {
          return Math.log(x) / Math.LN2;
        };
        ```
  
  - ##### 双曲函数方法：
  
    ES6 新增了 6 个双曲函数方法：
  
    - `Math.sinh(x)` 返回`x`的双曲正弦（hyperbolic sine）
      - `Math.cosh(x)` 返回`x`的双曲余弦（hyperbolic cosine）
      - `Math.tanh(x)` 返回`x`的双曲正切（hyperbolic tangent）
      - `Math.asinh(x)` 返回`x`的反双曲正弦（inverse hyperbolic sine）
      - `Math.acosh(x)` 返回`x`的反双曲余弦（inverse hyperbolic cosine）
      - `Math.atanh(x)` 返回`x`的反双曲正切（inverse hyperbolic tangent）
  
  - #### `BigInt` 数据类型

    - ##### 简介：

      JS 所有数字都保存成 64 位浮点数，这给数值的表示带来了两大限制。一是数值的精度只能到 53 个二进制位（相当于 16 个十进制位），大于这个范围的整数，JS 是无法精确表示，这使得 JS  不适合进行科学和金融方面的精确计算。二是大于或等于2的1024次方的数值，JS 无法表示，会返回`Infinity`。
  
      ```js
      // 超过 53 个二进制位的数值，无法保持精度
      Math.pow(2, 53) === Math.pow(2, 53) + 1 // true
    
      // 超过 2 的 1024 次方的数值，无法表示
    Math.pow(2, 1024) // Infinity
      ```

      [ES2020](https://github.com/tc39/proposal-bigint) 引入了一种新的数据类型 BigInt（大整数），来解决这个问题，这是 ECMAScript 的第八种数据类型。BigInt 只用来表示整数，没有位数的限制，任何位数的整数都可以精确表示。
  
      ```js
      const a = 2172141653n;
      const b = 15346349309n;
      
      // BigInt 可以保持精度
      a * b // 33334444555566667777n
      
    // 普通整数无法保持精度
      Number(a) * Number(b) // 33334444555566670000
    ```
  
      为了与 Number 类型区别，**BigInt 类型的数据必须添加后缀`n`**。
  
      ```js
      1234 // 普通整数
    1234n // BigInt
      
    // BigInt 的运算
      1n + 2n // 3n
    ```
  
      BigInt 同样可以使用各种进制表示，都要加上后缀`n`。
  
      ```js
      0b1101n // 二进制
      0o777n // 八进制
    0xFFn // 十六进制
      ```

      BigInt 与普通整数是两种值，它们之间并不相等。

      ```js
    42n === 42 // false
      ```
  
      `typeof`运算符对于 BigInt 类型的数据返回`bigint`。
  
      ```js
      typeof 123n // 'bigint'
      ```

      **BigInt 可以使用负号（`-`），但是不能使用正号（`+`）**，因为会与 `asm.js` 冲突。

      ```js
      -42n // 正确
      +42n // 报错
      ```
  
      JS 以前不能计算70的阶乘（即`70!`），因为超出了可以表示的精度。
  
      ```js
      let p = 1;
      for (let i = 1; i <= 70; i++) {
      p *= i;
      }
    console.log(p); // 1.197857166996989e+100
      ```
  
      现在支持大整数了，就可以算了，浏览器的开发者工具运行下面代码，就 OK。
  
      ```js
      let p = 1n;
      for (let i = 1n; i <= 70n; i++) {
      p *= i;
      }
    console.log(p); // 11978571...00000000n
      ```
  
    - ##### `BigInt` 函数：
  
      JS 原生提供`BigInt()`工具函数，它可以将其他类型的值转为 BigInt 类型。转换规则基本与`Number()`一致。

      ```js
    BigInt(123) // 123n
      BigInt('123') // 123n
      BigInt(false) // 0n
      BigInt(true) // 1n
    ```
  
    `BigInt()`函数必须有参数，而且参数必须可以正常转为数值，下面的用法都会报错。
  
      ```js
      new BigInt() // TypeError
    BigInt(undefined) //TypeError
      BigInt(null) // TypeError
    BigInt('123n') // SyntaxError
      BigInt('abc') // SyntaxError
      ```
  
      上面代码中，尤其值得注意字符串`123n`无法解析成 Number 类型，所以会报错。

      参数如果是小数，也会报错。

      ```js
      BigInt(1.5) // RangeError
      BigInt('1.5') // SyntaxError
      ```
  
      BigInt 继承了 Object 对象的两个实例方法。
  
    - `BigInt.prototype.toString()`
      - `BigInt.prototype.valueOf()`

      它还继承了 Number 对象的一个实例方法。
  
      - `BigInt.prototype.toLocaleString()`
  
      JS 的 BigInt 本身**不会溢出**，会无限增长。但在某些场景（加密、哈希、底层协议、模拟硬件），你需要**截断到固定位数**。因此 BigInt 提供了两个静态方法，用于将任意大的 BigInt 强制转换成固定位数的 BigInt：
  
      - `BigInt.asIntN(width, BigInt)`：给定的 BigInt 截断为`-2^(width-1)` ~ `2^(width-1)-1`之间的值。
    - `BigInt.asUintN(width, BigInt)`： 给定的 BigInt 截断为`0` ~ `2^width - 1`之间的值。
  
    ```js
      const max = 2n ** (64n - 1n) - 1n;
    
      BigInt.asIntN(32, max) // -1n
      BigInt.asUintN(32, max) // 4294967295n
      ```
  
      上面代码中，`max`是一个64位的 BigInt，如果转为32位，前面的32位都会被舍弃。
  
    对于二进制数组 `ArrayBuffer`，BigInt 新增了两个类型`BigUint64Array`和`BigInt64Array`，这两种数据类型返回的都是64位 BigInt。`DataView`对象的实例方法`DataView.prototype.getBigInt64()`和`DataView.prototype.getBigUint64()`，返回的也是 BigInt。
  
  - ##### 转换规则：
  
      可以使用`Boolean()`、`Number()`和`String()`这三个方法，将 BigInt 可以转为布尔值、数值和字符串类型。
  
      ```js
      Boolean(0n) // false
      Boolean(1n) // true
      Number(1n)  // 1
    String(1n)  // "1"
      ```

      上面代码中，注意最后一个例子，转为字符串时后缀`n`会消失。

      另外，取反运算符（`!`）也可以将 BigInt 转为布尔值。
  
      ```js
      !0n // true
    !1n // false
      ```

    - ##### 数学运算：
  
      数学运算方面，BigInt 类型的`+`、`-`、`*`和`**`这四个二元运算符，与 Number 类型的行为一致。除法运算`/`会舍去小数部分，返回一个整数。

      ```js
      9n / 5n  // 1n
      ```
  
      **几乎所有的数值运算符都可以用在 BigInt**，但是有两个例外：
  
      - 逻辑右移位运算符`>>>`
      - 一元的求正运算符`+`
  
      上面两个运算符用在 BigInt 会报错。前者是因为`>>>`运算符是不带符号的，但是 BigInt 总是带有符号的，导致该运算无意义，完全等同于右移运算符`>>`。后者是因为**一元运算符`+`在 asm.js 里面总是返回 Number 类型，为了不破坏 asm.js 就规定`+1n`会报错**。

      **BigInt 不能与普通数值进行混合运算**。

      ```js
    1n + 1.3 // 报错
      ```
  
      上面代码报错是因为无论返回的是 BigInt 或 Number，都会导致丢失精度信息。比如`(2n**53n + 1n) + 0.5`这个表达式，如果返回 BigInt 类型，`0.5`这个小数部分会丢失；如果返回 Number 类型，有效精度只能保持 53 位，导致精度下降。
  
      同样的原因，如果一个标准库函数的参数预期是 Number 类型，但是得到的是一个 BigInt，就会报错。
  
    ```js
      // 错误的写法
    Math.sqrt(4n) // 报错
      
    // 正确的写法
      Math.sqrt(Number(4n)) // 2
      ```
  
      上面代码中，`Math.sqrt`的参数预期是 Number 类型，如果是 BigInt 就会报错，必须先用`Number`方法转一下类型，才能进行计算。
  
      asm.js 里面，`|0`跟在一个数值的后面会返回一个32位整数。根据不能与 Number 类型混合运算的规则，BigInt 如果与`|0`进行运算会报错。
  
    ```js
      1n | 0 // 报错
    ```
  
  - ##### 其他运算：
  
    BigInt 对应的布尔值，与 Number 类型一致，即`0n`会转为`false`，其他值转为`true`。
  
    ```js
      if (0n) {
        console.log('if');
      } else {
        console.log('else');
      }
      // else
    ```
  
    上面代码中，`0n`对应`false`，所以会进入`else`子句。
  
    **比较运算符（比如`>`）和相等运算符（`==`）允许 BigInt 与其他类型的值混合计算，因为这样做不会损失精度**。
  
      ```js
      0n < 1 // true
      0n < true // true
    0n == 0 // true
      0n == false // true
    0n === 0 // false
      ```

      **BigInt 与字符串混合运算时，会先转为字符串，再进行运算**。
  
      ```js
      '' + 123n // "123"
    ```


------

