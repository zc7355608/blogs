- ## `Location` 对象，`URL` 对象，`URLSearchParams` 对象  (to learn)

  URL 是互联网的基础设施之一。浏览器提供了一些原生对象，用来管理 URL。

  - #### `Location` 对象

    `Location`对象是浏览器提供的原生对象，提供 URL 相关的信息和操作方法。通过`window.location`和`document.location`属性，可以拿到这个对象。

    - ##### 属性：

      `Location`对象提供以下属性。

      - `Location.href`：整个 URL。
      - `Location.protocol`：当前 URL 的协议，包括冒号（`:`）。
      - `Location.host`：主机。如果端口不是协议默认的`80`和`433`，则还会包括冒号（`:`）和端口。
      - `Location.hostname`：主机名，不包括端口。
      - `Location.port`：端口号。
      - `Location.pathname`：URL 的路径部分，从根路径`/`开始。
      - `Location.search`：查询字符串部分，从问号`?`开始。
      - `Location.hash`：片段字符串部分，从`#`开始。
      - `Location.username`：域名前面的用户名。
      - `Location.password`：域名前面的密码。
      - `Location.origin`：URL 的协议、主机名和端口。

      ```js
      // 当前网址为
      // http://user:passwd@www.example.com:4097/path/a.html?x=111#part1
      document.location.href
      // "http://user:passwd@www.example.com:4097/path/a.html?x=111#part1"
      document.location.protocol
      // "http:"
      document.location.host
      // "www.example.com:4097"
      document.location.hostname
      // "www.example.com"
      document.location.port
      // "4097"
      document.location.pathname
      // "/path/a.html"
      document.location.search
      // "?x=111"
      document.location.hash
      // "#part1"
      document.location.username
      // "user"
      document.location.password
      // "passwd"
      document.location.origin
      // "http://user:passwd@www.example.com:4097"
      ```

      这些属性里面，只有`origin`属性是只读的，其他属性都可写。

      注意，如果对`Location.href`写入新的 URL 地址，浏览器会立刻跳转到这个新地址。

      ```js
      // 跳转到新网址
      document.location.href = 'http://www.example.com';
      ```

      这个特性常常用于让网页自动滚动到新的锚点。

      ```js
      document.location.href = '#top';
      // 等同于
      document.location.hash = '#top';
      ```

      直接改写`location`，相当于写入`href`属性。

      ```js
      document.location = 'http://www.example.com';
      // 等同于
      document.location.href = 'http://www.example.com';
      ```

      另外，`Location.href`属性是浏览器唯一允许跨域写入的属性，即非同源的窗口可以改写另一个窗口（比如子窗口与父窗口）的`Location.href`属性，导致后者的网址跳转。`Location`的其他属性都不允许跨域写入。

    - ##### 方法：

      **（1）`Location.assign()`**

      `assign`方法接受一个 URL 字符串作为参数，使得浏览器立刻跳转到新的 URL。如果参数不是有效的 URL 字符串，则会报错。

      ```js
      // 跳转到新的网址
      document.location.assign('http://www.example.com')
      ```

      **（2）`Location.replace()`**

      `replace`方法接受一个 URL 字符串作为参数，使得浏览器立刻跳转到新的 URL。如果参数不是有效的 URL 字符串，则会报错。

      它与`assign`方法的差异在于，`replace`会在浏览器的浏览历史`History`里面删除当前网址，也就是说，一旦使用了该方法，后退按钮就无法回到当前网页了，相当于在浏览历史里面，使用新的 URL 替换了老的 URL。它的一个应用是，当脚本发现当前是移动设备时，就立刻跳转到移动版网页。

      ```js
      // 跳转到新的网址
      document.location.replace('http://www.example.com')
      ```

      **（3）`Location.reload()`**

      `reload`方法使得浏览器重新加载当前网址，相当于按下浏览器的刷新按钮。

      它接受一个布尔值作为参数。如果参数为`true`，浏览器将向服务器重新请求这个网页，并且重新加载后，网页将滚动到头部（即`scrollTop === 0`）。如果参数是`false`或为空，浏览器将从本地缓存重新加载该网页，并且重新加载后，网页的视口位置是重新加载前的位置。

      ```js
      // 向服务器重新请求当前网址
      window.location.reload(true);
      ```

      **（4）`Location.toString()`**

      `toString`方法返回整个 URL 字符串，相当于读取`Location.href`属性。

  - #### URL 的编码和解码

    网页的 URL 只能包含合法的字符。合法字符分成两类：

    - URL 元字符：分号（`;`），逗号（`,`），斜杠（`/`），问号（`?`），冒号（`:`），at（`@`），`&`，等号（`=`），加号（`+`），美元符号（`$`），井号（`#`）
    - 语义字符：`a-z`，`A-Z`，`0-9`，连词号（`-`），下划线（`_`），点（`.`），感叹号（`!`），波浪线（`~`），星号（`*`），单引号（`'`），圆括号（`()`）

    除了以上字符，其他字符出现在 URL 之中都必须转义，规则是根据操作系统的默认编码，将每个字节转为百分号（`%`）加上两个大写的十六进制字母。

    比如，UTF-8 的操作系统上，`http://www.example.com/q=春节`这个 URL 之中，汉字“春节”不是 URL 的合法字符，所以被浏览器自动转成`http://www.example.com/q=%E6%98%A5%E8%8A%82`。其中，“春”转成了`%E6%98%A5`，“节”转成了`%E8%8A%82`。这是因为“春”和“节”的 UTF-8 编码分别是`E6 98 A5`和`E8 8A 82`，将每个字节前面加上百分号，就构成了 URL 编码。

    JS 提供四个 URL 的编码/解码方法。

    - ##### `encodeURI()`：

      `encodeURI()`方法用于转码整个 URL。它的参数是一个字符串，代表整个 URL。它会将元字符和语义字符之外的字符，都进行转义。

      ```js
      encodeURI('http://www.example.com/q=春节')
      // "http://www.example.com/q=%E6%98%A5%E8%8A%82"
      ```

    - ##### `encodeURIComponent()`：

      `encodeURIComponent()`方法用于转码 URL 的组成部分，会转码除了语义字符之外的所有字符，即元字符也会被转码。所以，它不能用于转码整个 URL。它接受一个参数，就是 URL 的片段。

      ```js
      encodeURIComponent('春节')
      // "%E6%98%A5%E8%8A%82"
      encodeURIComponent('http://www.example.com/q=春节')
      // "http%3A%2F%2Fwww.example.com%2Fq%3D%E6%98%A5%E8%8A%82"
      ```

      上面代码中，`encodeURIComponent()`会连 URL 元字符一起转义，所以如果转码整个 URL 就会出错。

    - ##### `decodeURI()`：

      `decodeURI()`方法用于整个 URL 的解码。它是`encodeURI()`方法的逆运算。它接受一个参数，就是转码后的 URL。

      ```js
      decodeURI('http://www.example.com/q=%E6%98%A5%E8%8A%82')
      // "http://www.example.com/q=春节"
      ```

    - ##### `decodeURIComponent()`：

      `decodeURIComponent()`用于URL 片段的解码。它是`encodeURIComponent()`方法的逆运算。它接受一个参数，就是转码后的 URL 片段。

      ```js
      decodeURIComponent('%E6%98%A5%E8%8A%82')
      // "春节"
      ```

  - #### `URL` 接口

    浏览器原生提供`URL()`接口，它是一个构造函数，用来构造、解析和编码 URL。一般情况下，通过`window.URL`可以拿到这个构造函数。

    - ##### 构造函数：

      `URL()`作为构造函数，可以生成 URL 实例。它接受一个表示 URL 的字符串作为参数。如果参数不是合法的 URL，会报错。

      ```js
      var url = new URL('http://www.example.com/index.html');
      url.href
      // "http://www.example.com/index.html"
      ```

      上面示例生成了一个 URL 实例，用来代表指定的网址。

      除了字符串，`URL()`的参数也可以是另一个 URL 实例。这时，`URL()`会自动读取该实例的`href`属性，作为实际参数。

      如果 URL 字符串是一个相对路径，那么需要表示绝对路径的第二个参数，作为计算基准。

      ```js
      var url1 = new URL('index.html', 'http://example.com');
      url1.href
      // "http://example.com/index.html"
      
      var url2 = new URL('page2.html', 'http://example.com/page1.html');
      url2.href
      // "http://example.com/page2.html"
      
      var url3 = new URL('..', 'http://example.com/a/b.html')
      url3.href
      // "http://example.com/"
      ```

      上面代码中，返回的 URL 实例的路径都是在第二个参数的基础上，切换到第一个参数得到的。最后一个例子里面，第一个参数是`..`，表示上层路径。

    - ##### 实例属性：

      URL 实例的属性与`Location`对象的属性基本一致，返回当前 URL 的信息。

      - `URL.href`：返回整个 URL
      - `URL.protocol`：返回协议，以冒号`:`结尾
      - `URL.hostname`：返回域名
      - `URL.host`：返回域名与端口，包含`:`号，默认的80和443端口会省略
      - `URL.port`：返回端口
      - `URL.origin`：返回协议、域名和端口
      - `URL.pathname`：返回路径，以斜杠`/`开头
      - `URL.search`：返回查询字符串，以问号`?`开头
      - `URL.searchParams`：返回一个`URLSearchParams`实例，该属性是`Location`对象没有的
      - `URL.hash`：返回片段识别符，以井号`#`开头
      - `URL.password`：返回域名前面的密码
      - `URL.username`：返回域名前面的用户名

      ```js
      var url = new URL('http://user:passwd@www.example.com:4097/path/a.html?x=111#part1');
      
      url.href
      // "http://user:passwd@www.example.com:4097/path/a.html?x=111#part1"
      url.protocol
      // "http:"
      url.hostname
      // "www.example.com"
      url.host
      // "www.example.com:4097"
      url.port
      // "4097"
      url.origin
      // "http://www.example.com:4097"
      url.pathname
      // "/path/a.html"
      url.search
      // "?x=111"
      url.searchParams
      // URLSearchParams {}
      url.hash
      // "#part1"
      url.password
      // "passwd"
      url.username
      // "user"
      ```

      这些属性里面，只有`origin`属性是只读的，其他属性都可写，并且会立即生效。

      ```js
      var url = new URL('http://example.com/index.html#part1');
      
      url.pathname = 'index2.html';
      url.href // "http://example.com/index2.html#part1"
      
      url.hash = '#part2';
      url.href // "http://example.com/index2.html#part2"
      ```

      上面代码中，改变 URL 实例的`pathname`属性和`hash`属性，都会实时反映在 URL 实例当中。

    - ##### 静态方法：

      **（1）URL.createObjectURL()**

      `URL.createObjectURL()`方法用来为上传/下载的文件、流媒体文件生成一个 URL 字符串。这个字符串代表了`File`对象或`Blob`对象的 URL。

      ```js
      // HTML 代码如下
      // <div id="display"/>
      // <input
      //   type="file"
      //   id="fileElem"
      //   multiple
      //   accept="image/*"
      //   onchange="handleFiles(this.files)"
      //  >
      var div = document.getElementById('display');
      
      function handleFiles(files) {
        for (var i = 0; i < files.length; i++) {
          var img = document.createElement('img');
          img.src = window.URL.createObjectURL(files[i]);
          div.appendChild(img);
        }
      }
      ```

      上面代码中，`URL.createObjectURL()`方法用来为上传的文件生成一个 URL 字符串，作为`<img>`元素的图片来源。

      该方法生成的 URL 就像下面的样子。

      ```url
      blob:http://localhost/c745ef73-ece9-46da-8f66-ebes574789b1
      ```

      注意，每次使用`URL.createObjectURL()`方法，都会在内存里面生成一个 URL 实例。如果不再需要该方法生成的 URL 字符串，为了节省内存，可以使用`URL.revokeObjectURL()`方法释放这个实例。

      **（2）URL.revokeObjectURL()**

      `URL.revokeObjectURL()`方法用来释放`URL.createObjectURL()`方法生成的 URL 实例。它的参数就是`URL.createObjectURL()`方法返回的 URL 字符串。

      下面为上一段的示例加上`URL.revokeObjectURL()`。

      ```js
      var div = document.getElementById('display');
      
      function handleFiles(files) {
        for (var i = 0; i < files.length; i++) {
          var img = document.createElement('img');
          img.src = window.URL.createObjectURL(files[i]);
          div.appendChild(img);
          img.onload = function() {
            window.URL.revokeObjectURL(this.src);
          }
        }
      }
      ```

      上面代码中，一旦图片加载成功以后，为本地文件生成的 URL 字符串就没用了，于是可以在`img.onload`回调函数里面，通过`URL.revokeObjectURL()`方法卸载这个 URL 实例。

  - #### `URLSearchParams` 对象

    `URLSearchParams`对象是浏览器的原生对象，用来构造、解析和处理 URL 的查询字符串（即 URL 问号后面的部分）。

    它本身也是一个构造函数，可以生成实例。参数可以为查询字符串，起首的问号`?`有没有都行，也可以是对应查询字符串的数组或对象。

    ```js
    // 方法一：传入字符串
    var params = new URLSearchParams('?foo=1&bar=2');
    // 等同于
    var params = new URLSearchParams(document.location.search);
    
    // 方法二：传入数组
    var params = new URLSearchParams([['foo', 1], ['bar', 2]]);
    
    // 方法三：传入对象
    var params = new URLSearchParams({'foo' : 1 , 'bar' : 2});
    ```

    `URLSearchParams`会对查询字符串自动编码。

    ```js
    var params = new URLSearchParams({'foo': '你好'});
    params.toString() // "foo=%E4%BD%A0%E5%A5%BD"
    ```

    上面代码中，`foo`的值是汉字，`URLSearchParams`对其自动进行 URL 编码。

    浏览器向服务器发送表单数据时，可以直接使用`URLSearchParams`实例作为表单数据。

    ```js
    const params = new URLSearchParams({foo: 1, bar: 2});
    fetch('https://example.com/api', {
      method: 'POST',
      body: params
    }).then(...)
    ```

    上面代码中，`fetch`命令向服务器发送命令时，可以直接使用`URLSearchParams`实例。

    `URLSearchParams`可以与`URL()`接口结合使用。

    ```js
    var url = new URL(window.location);
    var foo = url.searchParams.get('foo') || 'somedefault';
    ```

    上面代码中，URL 实例的`searchParams`属性就是一个`URLSearchParams`实例，所以可以使用`URLSearchParams`接口的`get`方法。

    `URLSearchParams`实例有遍历器接口，可以用`for...of`循环遍历（详见《ES6 标准入门》的《Iterator》一章）。

    ```js
    var params = new URLSearchParams({'foo': 1 , 'bar': 2});
    
    for (var p of params) {
      console.log(p[0] + ': ' + p[1]);
    }
    // foo: 1
    // bar: 2
    ```

    `URLSearchParams`没有实例属性，只有实例方法。

    - ##### `URLSearchParams.toString()`：

      `toString`方法返回实例的字符串形式。

      ```js
      var url = new URL('https://example.com?foo=1&bar=2');
      var params = new URLSearchParams(url.search);
      
      params.toString() // "foo=1&bar=2'
      ```

      那么需要字符串的场合，会自动调用`toString`方法。

      ```js
      var params = new URLSearchParams({version: 2.0});
      window.location.href = location.pathname + '?' + params;
      ```

      上面代码中，`location.href`赋值时，可以直接使用`params`对象。这时就会自动调用`toString`方法。

    - ##### `URLSearchParams.append()`：

      `append()`方法用来追加一个查询参数。它接受两个参数，第一个为键名，第二个为键值，没有返回值。

      ```js
      var params = new URLSearchParams({'foo': 1 , 'bar': 2});
      params.append('baz', 3);
      params.toString() // "foo=1&bar=2&baz=3"
      ```

      `append()`方法不会识别是否键名已经存在。

      ```js
      var params = new URLSearchParams({'foo': 1 , 'bar': 2});
      params.append('foo', 3);
      params.toString() // "foo=1&bar=2&foo=3"
      ```

      上面代码中，查询字符串里面`foo`已经存在了，但是`append`依然会追加一个同名键。

    - ##### `URLSearchParams.delete()`：

      `delete()`方法用来删除指定的查询参数。它接受键名作为参数。

      ```js
      var params = new URLSearchParams({'foo': 1 , 'bar': 2});
      params.delete('bar');
      params.toString() // "foo=1"
      ```

    - ##### `URLSearchParams.has()`：

      `has()`方法返回一个布尔值，表示查询字符串是否包含指定的键名。

      ```js
      var params = new URLSearchParams({'foo': 1 , 'bar': 2});
      params.has('bar') // true
      params.has('baz') // false
      ```

    - ##### `URLSearchParams.set()`：

      `set()`方法用来设置查询字符串的键值。

      它接受两个参数，第一个是键名，第二个是键值。如果是已经存在的键，键值会被改写，否则会被追加。

      ```js
      var params = new URLSearchParams('?foo=1');
      params.set('foo', 2);
      params.toString() // "foo=2"
      params.set('bar', 3);
      params.toString() // "foo=2&bar=3"
      ```

      上面代码中，`foo`是已经存在的键，`bar`是还不存在的键。

      如果有多个的同名键，`set`会移除现存所有的键。

      ```js
      var params = new URLSearchParams('?foo=1&foo=2');
      params.set('foo', 3);
      params.toString() // "foo=3"
      ```

      下面是一个替换当前 URL 的例子。

      ```js
      // URL: https://example.com?version=1.0
      var params = new URLSearchParams(location.search.slice(1));
      params.set('version', '2.0');
      
      window.history.replaceState({}, '', location.pathname + `?` + params);
      // URL: https://example.com?version=2.0
      ```

    - ##### `URLSearchParams.get()`，`URLSearchParams.getAll()`：

      `get()`方法用来读取查询字符串里面的指定键。它接受键名作为参数。

      ```js
      var params = new URLSearchParams('?foo=1');
      params.get('foo') // "1"
      params.get('bar') // null
      ```

      两个地方需要注意。第一，它返回的是字符串，如果原始值是数值，需要转一下类型；第二，如果指定的键名不存在，返回值是`null`。

      如果有多个的同名键，`get`返回位置最前面的那个键值。

      ```js
      var params = new URLSearchParams('?foo=3&foo=2&foo=1');
      params.get('foo') // "3"
      ```

      上面代码中，查询字符串有三个`foo`键，`get`方法返回最前面的键值`3`。

      `getAll()`方法返回一个数组，成员是指定键的所有键值。它接受键名作为参数。

      ```js
      var params = new URLSearchParams('?foo=1&foo=2');
      params.getAll('foo') // ["1", "2"]
      ```

      上面代码中，查询字符串有两个`foo`键，`getAll`返回的数组就有两个成员。

    - ##### `URLSearchParams.sort()`：

      `sort()`方法对查询字符串里面的键进行排序，规则是按照 Unicode 码点从小到大排列。

      该方法没有返回值，或者说返回值是`undefined`。

      ```js
      var params = new URLSearchParams('c=4&a=2&b=3&a=1');
      params.sort();
      params.toString() // "a=2&a=1&b=3&c=4"
      ```

      上面代码中，如果有两个同名的键`a`，它们之间不会排序，而是保留原始的顺序。

    - ##### `URLSearchParams.keys()`，`URLSearchParams.values()`，`URLSearchParams.entries()`：

      这三个方法都返回一个遍历器对象，供`for...of`循环遍历。它们的区别在于，`keys`方法返回的是键名的遍历器，`values`方法返回的是键值的遍历器，`entries`返回的是键值对的遍历器。

      ```js
      var params = new URLSearchParams('a=1&b=2');
      
      for(var p of params.keys()) {
        console.log(p);
      }
      // a
      // b
      
      for(var p of params.values()) {
        console.log(p);
      }
      // 1
      // 2
      
      for(var p of params.entries()) {
        console.log(p);
      }
      // ["a", "1"]
      // ["b", "2"]
      ```

      如果直接对`URLSearchParams`进行遍历，其实内部调用的就是`entries`接口。

      ```js
      for (var p of params) {}
      // 等同于
      for (var p of params.entries()) {}
      ```

- ## `ArrayBuffer` 对象，`Blob` 对象

  - #### `ArrayBuffer` 对象

    ArrayBuffer 对象表示一段二进制数据，用来模拟内存里面的数据。通过这个对象，JS 可以读写二进制数据。这个对象可以看作内存数据的表达。

    这个对象是 ES6 才写入标准的，普通的网页编程用不到它，为了教程体系的完整，下面只提供一个简略的介绍，详细介绍请看《ES6 标准入门》里面的章节。

    浏览器原生提供`ArrayBuffer()`构造函数，用来生成实例。它接受一个整数作为参数，表示这段二进制数据占用多少个字节。

    ```js
    var buffer = new ArrayBuffer(8);
    ```

    上面代码中，实例对象`buffer`占用8个字节。

    ArrayBuffer 对象有实例属性`byteLength`，表示当前实例占用的内存长度（单位字节）。

    ```js
    var buffer = new ArrayBuffer(8);
    buffer.byteLength // 8
    ```

    ArrayBuffer 对象有实例方法`slice()`，用来复制一部分内存。它接受两个整数参数，分别表示复制的开始位置（从0开始）和结束位置（复制时不包括结束位置），如果省略第二个参数，则表示一直复制到结束。

    ```js
    var buf1 = new ArrayBuffer(8);
    var buf2 = buf1.slice(0);
    ```

    上面代码表示复制原来的实例。

  - #### `Blob` 对象

    - ##### 简介：

      Blob 对象表示一个二进制文件的数据内容，比如一个图片文件的内容就可以通过 Blob 对象读写。它通常用来读写文件，它的名字是  Binary Large Object （二进制大型对象）的缩写。它与 ArrayBuffer 的区别在于，它用于操作二进制文件，而  ArrayBuffer 用于操作内存。

      浏览器原生提供`Blob()`构造函数，用来生成实例对象。

      ```js
      new Blob(array [, options])
      ```

      `Blob`构造函数接受两个参数。第一个参数是数组，成员是字符串或二进制对象，表示新生成的`Blob`实例对象的内容；第二个参数是可选的，是一个配置对象，目前只有一个属性`type`，它的值是一个字符串，表示数据的 MIME 类型，默认是空字符串。

      ```js
      var htmlFragment = ['<a id="a"><b id="b">hey!</b></a>'];
      var myBlob = new Blob(htmlFragment, {type : 'text/html'});
      ```

      上面代码中，实例对象`myBlob`包含的是字符串。生成实例的时候，数据类型指定为`text/html`。

      下面是另一个例子，Blob 保存 JSON 数据。

      ```js
      var obj = { hello: 'world' };
      var blob = new Blob([ JSON.stringify(obj) ], {type : 'application/json'});
      ```

    - ##### 实例属性和实例方法：

      `Blob`具有两个实例属性`size`和`type`，分别返回数据的大小和类型。

      ```js
      var htmlFragment = ['<a id="a"><b id="b">hey!</b></a>'];
      var myBlob = new Blob(htmlFragment, {type : 'text/html'});
      
      myBlob.size // 32
      myBlob.type // "text/html"
      ```

      `Blob`具有一个实例方法`slice`，用来拷贝原来的数据，返回的也是一个`Blob`实例。

      ```js
      myBlob.slice(start, end, contentType)
      ```

      `slice`方法有三个参数，都是可选的。它们依次是起始的字节位置（默认为0）、结束的字节位置（默认为`size`属性的值，该位置本身将不包含在拷贝的数据之中）、新实例的数据类型（默认为空字符串）。

    - ##### 获取文件信息：

      文件选择器`<input type="file">`用来让用户选取文件。出于安全考虑，浏览器不允许脚本自行设置这个控件的`value`属性，即文件必须是用户手动选取的，不能是脚本指定的。一旦用户选好了文件，脚本就可以读取这个文件。

      文件选择器返回一个 `FileList` 对象，该对象是一个类似数组的成员，每个成员都是一个 `File` 实例对象。`File` 实例对象是一个特殊的 `Blob` 实例，增加了`name`和`lastModifiedDate`属性。

      ```js
      // HTML 代码如下
      // <input type="file" accept="image/*" multiple onchange="fileinfo(this.files)"/>
      
      function fileinfo(files) {
        for (var i = 0; i < files.length; i++) {
          var f = files[i];
          console.log(
            f.name, // 文件名，不含路径
            f.size, // 文件大小，Blob 实例属性
            f.type, // 文件类型，Blob 实例属性
            f.lastModifiedDate // 文件的最后修改时间
          );
        }
      }
      ```

      除了文件选择器，拖放 API 的`dataTransfer.files`返回的也是一个 `FileList` 对象，它的成员因此也是 `File` 实例对象。

    - ##### 下载文件：

      AJAX 请求时，如果指定`responseType`属性为`blob`，下载下来的就是一个 `Blob` 对象。

      ```js
      function getBlob(url, callback) {
        var xhr = new XMLHttpRequest();
        xhr.open('GET', url);
        xhr.responseType = 'blob';
        xhr.onload = function () {
          callback(xhr.response);
        }
        xhr.send(null);
      }
      ```

      上面代码中，`xhr.response`拿到的就是一个 `Blob` 对象。

    - ##### 生成 URL：

      浏览器允许使用`URL.createObjectURL()`方法，针对 `Blob` 对象生成一个临时 URL，以便于某些 API 使用。这个 URL 以`blob://`开头，表明对应一个 `Blob` 对象，协议头后面是一个识别符，用来唯一对应内存里面的 `Blob` 对象。这一点与`data://URL`（URL 包含实际数据）和`file://URL`（本地文件系统里面的文件）都不一样。

      ```js
      var droptarget = document.getElementById('droptarget');
      
      droptarget.ondrop = function (e) {
        var files = e.dataTransfer.files;
        for (var i = 0; i < files.length; i++) {
          var type = files[i].type;
          if (type.substring(0,6) !== 'image/')
            continue;
          var img = document.createElement('img');
          img.src = URL.createObjectURL(files[i]);
          img.onload = function () {
            this.width = 100;
            document.body.appendChild(this);
            URL.revokeObjectURL(this.src);
          }
        }
      }
      ```

      上面代码通过为拖放的图片文件生成一个 URL，产生它们的缩略图，从而使得用户可以预览选择的文件。

      浏览器处理 Blob URL 就跟普通的 URL 一样，如果 Blob 对象不存在，返回404状态码；如果跨域请求，返回403状态码。Blob URL  只对 GET 请求有效，如果请求成功，返回200状态码。由于 Blob URL 就是普通 URL，因此可以下载。

    - ##### 读取文件：

      取得 Blob 对象以后，可以通过`FileReader`对象，读取 `Blob` 对象的内容，即文件内容。

      `FileReader` 对象提供四个方法，处理 Blob 对象。Blob 对象作为参数传入这些方法，然后以指定的格式返回。

      - `FileReader.readAsText()`：返回文本，需要指定文本编码，默认为 UTF-8。
      - `FileReader.readAsArrayBuffer()`：返回 `ArrayBuffer` 对象。
      - `FileReader.readAsDataURL()`：返回 Data URL。
      - `FileReader.readAsBinaryString()`：返回原始的二进制字符串。

      下面是`FileReader.readAsText()`方法的例子，用来读取文本文件。

      ```js
      // HTML 代码如下
      // <input type="file" onchange="readfile(this.files[0])"></input>
      // <pre id="output"></pre>
      function readfile(f) {
        var reader = new FileReader();
        reader.readAsText(f);
        reader.onload = function () {
          var text = reader.result;
          var out = document.getElementById('output');
          out.innerHTML = '';
          out.appendChild(document.createTextNode(text));
        }
        reader.onerror = function(e) {
          console.log('Error', e);
        };
      }
      ```

      上面代码中，通过指定 `FileReader` 实例对象的`onload`监听函数，在实例的`result`属性上拿到文件内容。

      下面是`FileReader.readAsArrayBuffer()`方法的例子，用于读取二进制文件。

      ```js
      // HTML 代码如下
      // <input type="file" onchange="typefile(this.files[0])"></input>
      function typefile(file) {
        // 文件开头的四个字节，生成一个 Blob 对象
        var slice = file.slice(0, 4);
        var reader = new FileReader();
        // 读取这四个字节
        reader.readAsArrayBuffer(slice);
        reader.onload = function (e) {
          var buffer = reader.result;
          // 将这四个字节的内容，视作一个32位整数
          var view = new DataView(buffer);
          var magic = view.getUint32(0, false);
          // 根据文件的前四个字节，判断它的类型
          switch(magic) {
            case 0x89504E47: file.verified_type = 'image/png'; break;
            case 0x47494638: file.verified_type = 'image/gif'; break;
            case 0x25504446: file.verified_type = 'application/pdf'; break;
            case 0x504b0304: file.verified_type = 'application/zip'; break;
          }
          console.log(file.name, file.verified_type);
        };
      }
      ```

- ## `File` 对象，`FileList` 对象，`FileReader` 对象

  - #### `File` 对象

    File 对象代表一个文件，用来读写文件信息。它继承了 Blob 对象，或者说是一种特殊的 Blob 对象，所有可以使用 Blob 对象的场合都可以使用它。

    最常见的使用场合是表单的文件上传控件（`<input type="file">`），用户选中文件以后，浏览器就会生成一个数组，里面是每一个用户选中的文件，它们都是 File 实例对象。

    ```js
    // HTML 代码如下
    // <input id="fileItem" type="file">
    var file = document.getElementById('fileItem').files[0];
    file instanceof File // true
    ```

    上面代码中，`file`是用户选中的第一个文件，它是 File 的实例。

    - ##### 构造函数：

      浏览器原生提供一个`File()`构造函数，用来生成 File 实例对象。

      ```js
      new File(array, name [, options])
      ```

      `File()`构造函数接受三个参数。

      - array：一个数组，成员可以是二进制对象或字符串，表示文件的内容。
      - name：字符串，表示文件名或文件路径。
      - options：配置对象，设置实例的属性。该参数可选。

      第三个参数配置对象，可以设置两个属性。

      - type：字符串，表示实例对象的 MIME 类型，默认值为空字符串。
      - lastModified：时间戳，表示上次修改的时间，默认为`Date.now()`。

      下面是一个例子。

      ```js
      var file = new File(
        ['foo'],
        'foo.txt',
        {
          type: 'text/plain',
        }
      );
      ```

    - ##### 实例属性和实例方法：

      File 对象有以下实例属性。

      - File.lastModified：最后修改时间
      - File.name：文件名或文件路径
      - File.size：文件大小（单位字节）
      - File.type：文件的 MIME 类型

      ```js
      var myFile = new File([], 'file.bin', {
        lastModified: new Date(2018, 1, 1),
      });
      myFile.lastModified // 1517414400000
      myFile.name // "file.bin"
      myFile.size // 0
      myFile.type // ""
      ```

      上面代码中，由于`myFile`的内容为空，也没有设置 MIME 类型，所以`size`属性等于0，`type`属性等于空字符串。

      File 对象没有自己的实例方法，由于继承了 Blob 对象，因此可以使用 Blob 的实例方法`slice()`。

  - #### `FileList` 对象

    `FileList`对象是一个类似数组的对象，代表一组选中的文件，每个成员都是一个 File 实例。它主要出现在两个场合。

    - 文件控件节点（`<input type="file">`）的`files`属性，返回一个 FileList 实例。
    - 拖拉一组文件时，目标区的`DataTransfer.files`属性，返回一个 FileList 实例。

    ```js
    // HTML 代码如下
    // <input id="fileItem" type="file">
    var files = document.getElementById('fileItem').files;
    files instanceof FileList // true
    ```

    上面代码中，文件控件的`files`属性是一个 FileList 实例。

    FileList 的实例属性主要是`length`，表示包含多少个文件。

    FileList 的实例方法主要是`item()`，用来返回指定位置的实例。它接受一个整数作为参数，表示位置的序号（从零开始）。但是，由于 FileList 的实例是一个类似数组的对象，可以直接用方括号运算符，即`myFileList[0]`等同于`myFileList.item(0)`，所以一般用不到`item()`方法。

  - #### `FileReader` 对象

    FileReader 对象用于读取 File 对象或 Blob 对象所包含的文件内容。

    浏览器原生提供一个`FileReader`构造函数，用来生成 FileReader 实例。

    ```js
    var reader = new FileReader();
    ```

    FileReader 有以下的实例属性。

    - FileReader.error：读取文件时产生的错误对象
    - FileReader.readyState：整数，表示读取文件时的当前状态。一共有三种可能的状态，`0`表示尚未加载任何数据，`1`表示数据正在加载，`2`表示加载完成。
    - FileReader.result：读取完成后的文件内容，有可能是字符串，也可能是一个 ArrayBuffer 实例。
    - FileReader.onabort：`abort`事件（用户终止读取操作）的监听函数。
    - FileReader.onerror：`error`事件（读取错误）的监听函数。
    - FileReader.onload：`load`事件（读取操作完成）的监听函数，通常在这个函数里面使用`result`属性，拿到文件内容。
    - FileReader.onloadstart：`loadstart`事件（读取操作开始）的监听函数。
    - FileReader.onloadend：`loadend`事件（读取操作结束）的监听函数。
    - FileReader.onprogress：`progress`事件（读取操作进行中）的监听函数。

    下面是监听`load`事件的一个例子。

    ```js
    // HTML 代码如下
    // <input type="file" onchange="onChange(event)">
    
    function onChange(event) {
      var file = event.target.files[0];
      var reader = new FileReader();
      reader.onload = function (event) {
        console.log(event.target.result)
      };
    
      reader.readAsText(file);
    }
    ```

    上面代码中，每当文件控件发生变化，就尝试读取第一个文件。如果读取成功（`load`事件发生），就打印出文件内容。

    FileReader 有以下实例方法。

    - FileReader.abort()：终止读取操作，`readyState`属性将变成`2`。
    - FileReader.readAsArrayBuffer()：以 ArrayBuffer 的格式读取文件，读取完成后`result`属性将返回一个 ArrayBuffer 实例。
    - FileReader.readAsBinaryString()：读取完成后，`result`属性将返回原始的二进制字符串。
    - FileReader.readAsDataURL()：读取完成后，`result`属性将返回一个 Data URL 格式（Base64 编码）的字符串，代表文件内容。对于图片文件，这个字符串可以用于`<img>`元素的`src`属性。注意，这个字符串不能直接进行 Base64 解码，必须把前缀`data:*/*;base64,`从字符串里删除以后，再进行解码。
    - FileReader.readAsText()：读取完成后，`result`属性将返回文件内容的文本字符串。该方法的第一个参数是代表文件的 Blob 实例，第二个参数是可选的，表示文本编码，默认为 UTF-8。

    下面是一个例子。

    ```js
    /* HTML 代码如下
      <input type="file" onchange="previewFile()">
      <img src="" height="200">
    */
    
    function previewFile() {
      var preview = document.querySelector('img');
      var file    = document.querySelector('input[type=file]').files[0];
      var reader  = new FileReader();
    
      reader.addEventListener('load', function () {
        preview.src = reader.result;
      }, false);
    
      if (file) {
        reader.readAsDataURL(file);
      }
    }
    ```

    上面代码中，用户选中图片文件以后，脚本会自动读取文件内容，然后作为一个 Data URL 赋值给`<img>`元素的`src`属性，从而把图片展示出来。

------

