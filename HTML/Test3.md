

- ## `<link>` 标签

  - #### 简介

    `<link>`标签主要用于将当前网页与相关的外部资源联系起来。它通常放在`<head>`元素里面。最常见的用途就是加载 CSS 样式表。该标签默认是异步加载的，不影响HTML文档的解析。
    
    ```html
    <link rel="stylesheet" type="text/css" href="theme.css">
    ```
    
    上面代码为网页加载样式表`theme.css`。
    
    除了默认样式表，网页还可以加载替代样式表，即默认不生效、需要用户手动切换的样式表。
    
    ```html
    <link href="default.css" rel="stylesheet" title="Default Style">
    <link href="fancy.css" rel="alternate stylesheet" title="Fancy">
    <link href="basic.css" rel="alternate stylesheet" title="Basic">
    ```
    
    上面代码中，`default.css`是默认样式表，默认就会生效。`fancy.css`和`basic.css`是替换样式表（`rel="alternate stylesheet"`），默认不生效。**`title`属性在这里是必需的**，用来在浏览器菜单里面列出这些样式表的名字，供用户选择，以替代默认样式表。（使用这种方式要注意查看各浏览器的支持情况，google默认不支持）
    
    `<link>`还可以加载网站的 favicon 图标文件。
    
    ```html
    <link rel="icon" type="image/x-icon" href="/favicon.ico">
    ```
    
    手机访问时，网站通常需要提供不同分辨率的图标文件。
    
    ```html
    <link rel="apple-touch-icon-precomposed" sizes="114x114" href="favicon114.png">
    <link rel="apple-touch-icon-precomposed" sizes="72x72" href="favicon72.png">
    ```
    
    上面代码指定 iPhone 设备需要的114像素和72像素的图标。
    
    `<link>`也用于提供文档的相关链接，比如下面是给出文档的 RSS Feed 地址。
    
    ```html
    <link rel="alternate" type="application/atom+xml" href="/blog/news/atom">
    ```

  - #### `href` 属性

    `href`属性表示`<link>`标签所链接的资源的 URL 地址。

  - #### `rel` 属性

    `rel`属性表示外部资源与当前文档之间的关系，是`<link>`标签的**必需属性**，可以视为对`href`属性所链接资源的说明。
    
    它可以但不限于取以下值：（多个值以空格隔开）
    
    - `alternate`：文档的另一种表现形式的链接，比如打印版。
    - `author`：文档作者的链接。
    - `dns-prefetch`：要求浏览器提前执行指定网址的 DNS 查询。
    - `help`：帮助文档的链接。
    - `icon`：加载文档的图标文件。
    - `license`：许可证链接。
    - `next`：系列文档下一篇的链接。
    - `pingback`：接收当前文档 pingback 请求的网址。
    - `preconnect`：要求浏览器提前与给定服务器，建立 HTTP 连接。
    - `prefetch`：要求浏览器提前下载并缓存指定资源，供下一个页面使用。它的优先级较低，浏览器可以不下载。
    - `preload`：要求浏览器提前下载并缓存指定资源，当前页面稍后就会用到。它的优先级较高，浏览器必须立即下载。
    - `prerender`：要求浏览器提前渲染指定链接。这样的话，用户稍后打开该链接，就会立刻显示，感觉非常快。
    - `prev`：表示当前文档是系列文档的一篇，这里给出上一篇文档的链接。
    - `search`：提供当前网页的搜索链接。
    - `stylesheet`：加载一张样式表。
    
    下面是一些示例。
    
    ```html
    <!-- 作者信息 -->
    <link rel="author" href="humans.txt">
    
    <!-- 版权信息 -->
    <link rel="license" href="copyright.html">
    
    <!-- 另一个语言的版本 -->
    <link rel="alternate" href="https://es.example.com/" hreflang="es">
    
    <!-- 联系方式 -->
    <link rel="me" href="https://google.com/profiles/someone" type="text/html">
    <link rel="me" href="mailto:name@example.com">
    <link rel="me" href="sms:+15035550125">
    
    <!-- 历史资料 -->
    <link rel="archives" href="http://example.com/archives/">
    
    <!-- 目录 -->
    <link rel="index" href="http://example.com/article/">
    
    <!-- 导航 -->
    <link rel="first" href="http://example.com/article/">
    <link rel="last" href="http://example.com/article/?page=42">
    <link rel="prev" href="http://example.com/article/?page=1">
    <link rel="next" href="http://example.com/article/?page=3">
    ```

  - #### `hreflang` 属性

    `hreflang`属性用来表示`href`属性链接资源的所用语言，通常指当前页面的其他语言版本。
    
    ```html
    <link href="https://example.com/de" rel="alternate" hreflang="de" />
    ```
    
    上面示例中，`hreflang`表示`href`属性所链接页面使用德语，即当前页面的德语版本。
    
    如果一个页面有多个语言的版本，`hreflang`属性可以设为`x-default`，表示哪一个页面是默认版本。
    
    ```html
    <link href="https://example.com" rel="alternate" hreflang="x-default" />
    <link href="https://example.com/de" rel="alternate" hreflang="de" />
    ```
    
    上面示例中，`hreflang`设为`x-default`表示该页面为默认版本。

  - #### 资源的预加载

    某些情况下，你需要浏览器预加载某些资源，也就是先把资源缓存下来，等到使用的时候，就不用再从网上下载了，立即就能使用。预处理指令可以做到这一点。
    
    预加载主要有下面五种类型。

    - ##### `<link rel="preload">`：

      `<link rel="preload">`告诉浏览器尽快下载并缓存资源（如脚本或样式表），该指令优先级较高，浏览器肯定会执行。当加载页面几秒钟后需要该资源时，它会很有用。**下载后，浏览器不会对资源执行任何操作，脚本未执行，样式表未应用。它只是缓存，当其他东西需要它时，它立即可用**。
      
      ```html
      <link rel="preload" href="image.png" as="image">
      ```
      
      `rel="preload"`除了优先级较高，还有两个优点：一是允许指定预加载资源的类型，二是允许`onload`事件的回调函数。下面是`rel="preload"`配合`as`属性，告诉浏览器预处理资源的类型，以便正确处理。
      
      ```html
      <link rel="preload" href="style.css" as="style">
      <link rel="preload" href="main.js" as="script">
      ```
      
      上面代码要求浏览器提前下载并缓存`style.css`和`main.js`。
      
      `as`属性指定加载资源的类型，常用的值有下面几种：
      
      - `script`
      
      - `style`
      
      - `image`
      
      - `media`
      
      - `document`
      
        ...
      
      **如果不指定`as`属性，或者它的值是浏览器不认识的，那么浏览器会以较低的优先级下载这个资源**。
      
      有时还需要`type`属性，进一步明确 MIME 类型。
      
      ```html
      <link rel="preload" href="sintel-short.mp4" as="video" type="video/mp4">
      ```
      
      上面代码要求浏览器提前下载视频文件，并且说明这是 MP4 编码。
      
      下面是预下载字体文件的例子。
      
      ```html
      <link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>
      ```
      
      注意，所有预下载的资源，只是下载到浏览器的缓存，并没有执行。如果希望资源预下载后立刻执行，可以参考下面的写法。
      
      ```html
      <link rel="preload" as="style" href="async_style.css" onload="this.rel='stylesheet'">
      ```
      
      上面代码中，`onload`指定的回调函数会在脚本下载完成后执行，立即插入页面。

    - ##### `<link rel="prefetch">`：

      `<link rel="prefetch">`的使用场合是，如果后续的页面需要某个资源，并且希望预加载该资源，以便加速页面渲染。该指令不是强制性的，优先级较低，浏览器不一定会执行。这意味着，浏览器可以不下载该资源，比如连接速度很慢时。
      
      ```html
      <link rel="prefetch" href="https://www.example.com/">
      ```

    - ##### `<link rel="preconnect">`：

      `<link rel="preconnect">`要求浏览器提前与某个域名建立 TCP 连接。当你知道，很快就会请求该域名时，这会很有帮助。
      
      ```html
      <link rel="preconnect" href="https://www.example.com/">
      ```

    - ##### `<link rel="dns-prefetch">`：

      `<link rel="dns-prefetch">`要求浏览器提前执行某个域名的 DNS 解析。
      
      ```html
      <link rel="dns-prefetch" href="//example.com/">
      ```

    - ##### `<link rel="prerender">`：

      `<link rel="prerender">`要求浏览器加载某个网页，并且提前渲染它。用户点击指向该网页的链接时，就会立即呈现该页面。如果确定用户下一步会访问该页面，这会很有帮助。
      
      ```html
      <link rel="prerender" href="http://example.com/">
      ```

  - #### `media` 属性

    `media`属性给出外部资源生效的媒介条件。
    
    ```html
    <link href="print.css" rel="stylesheet" media="print">
    <link href="mobile.css" rel="stylesheet" media="screen and (max-width: 600px)">
    ```
    
    上面代码中，打印时加载`print.css`，移动设备访问时（设备宽度小于600像素）加载`mobile.css`。
    
    下面是使用`media`属性实现条件加载的例子。
    
    ```html
    <link rel="preload" as="image" href="map.png" media="(max-width: 600px)">
    <link rel="preload" as="script" href="map.js" media="(min-width: 601px)">
    ```
    
    上面代码中，如果屏幕宽度在600像素以下，则只加载第一个资源，否则就加载第二个资源。

  - #### 其他属性

    `<link>`标签的其他属性如下：
    
    - `crossorigin`：加载外部资源的跨域设置。
    - `href`：外部资源的网址。
    - `referrerpolicy`：加载时`Referer`头信息字段的处理方法。
    - `as`：`rel="preload"`或`rel="prefetch"`时，设置外部资源的类型。
    - `type`：type属性指定链接资源的MIME类型，帮助浏览器**预先判断资源类型**。
    - `title`：加载样式表时，用来标识样式表的名称。
    - `sizes`：用来声明图标文件的尺寸，比如加载苹果手机的图标文件。

- ## `<script>`、`<noscript>`标签

  `<script>`标签用于在网页插入脚本，`<noscript>`标签用于指定浏览器不支持脚本时的显示内容。

  - ##### `<script>`：

    `<script>`用于加载脚本代码，目前主要是加载 JavaScript 代码。
    
    
    ```html
    <script>
    	console.log('hello world');
    </script>
    ```
    
    上面代码嵌入网页，会立即执行。（它的执行会阻塞下方HTML代码的加载）
    
    `<script>`也可以加载外部脚本，src属性给出外部脚本的地址。
    
    
    ```html
    <script src="javascript.js"></script>
    ```
    
    上面代码会加载`javascript.js`脚本文件，并执行。
    
    `type`属性给出脚本的类型，默认是 JavaScript 代码，所以可省略。完整的写法其实是下面这样。
    
    ```html
    <script type="text/javascript" src="javascript.js"></script>
    ```
    
    `type`属性也可以设成`module`，表示这是一个 ES6 模块，不是传统脚本。当旧版本浏览器遇到`type="module"`时，由于其无法识别"module"类型，因此会忽略这个脚本。
    
    ```html
    <script type="module" src="main.js"></script>
    ```
    
    对于那些不支持 ES6 模块的浏览器，可以为`<script>`标签设置`nomodule`属性。支持 ES6 模块的浏览器，会不加载指定的脚本。这个属性通常与`type="module"`配合使用，作为老式浏览器的回退方案。
    
    ```html
    <script type="module" src="main.js"></script><!-- 该脚本只在新版本浏览器中执行 -->
    <script nomodule src="fallback.js"></script><!-- 该脚本只在旧版本浏览器中执行 -->
    ```
    
    `<script>`还有下面一些其他属性，大部分跟 JavaScript 语言有关，可以参考相关的 JavaScript 教程。
    
    
    - `async`：该属性指定 JavaScript 代码为异步执行，不是造成阻塞效果，JavaScript 代码默认是同步执行。
    - `defer`：该属性指定 JavaScript 代码不是立即执行，而是页面解析完成后执行。
    - `crossorigin`：如果采用这个属性，就会采用跨域的方式加载外部脚本，即 HTTP 请求的头信息会加上`origin`字段。
    - `referrerpolicy`：HTTP 请求的`Referer`字段的处理方法。
    - `integrity`：给出外部脚本的哈希值，防止脚本被篡改。浏览器拿到脚本后，根据其内容计算出hash值，如果和`<script>`标签指定的hash值相同，才会执行。
    - `nonce`：一个密码随机数（每次加载脚本时这个数都不一样），由服务器在 HTTP 头信息里面给出（`Content-Security-Policy: script-src 'nonce-abc123xyz789...'`），浏览器**记住**这个策略：只有 `nonce="abc123xyz789..."` 的脚本才能执行。它相当于给出了内嵌脚本的白名单，只有在白名单内的脚本才能执行。因此`<script>`标签的`nonce`属性值必须随着响应头去动态变化。

  - ##### `<noscript>`：

    `<noscript>`标签用于浏览器不支持或关闭 JavaScript 时，所要显示的内容。用户关闭 JavaScript 可能是为了节省带宽，以延长手机电池寿命，或者为了防止追踪，保护隐私。
    
    ```html
    <noscript>
    	您的浏览器不能执行 JavaScript 语言，页面无法正常显示。
    </noscript>
    ```
    
    上面这段代码，只有浏览器不能执行 JavaScript 代码时才会显示，否则就不会显示。

- ## 多媒体标签

  除了图像，网页还可以放置视频和音频。

  - ##### `<video>`：

    `<video>`标签是一个块级元素，用于放置视频。如果浏览器支持加载的视频格式，就会显示一个播放器，否则就显示`<video>`内部的子元素。
    
    ```html
    <video src="example.mp4" controls>
    	<p>你的浏览器不支持 HTML5 视频，请下载<a href="example.mp4">视频文件</a>。</p>
    </video>
    ```
    
    上面代码中，如果浏览器不支持该种格式的视频，就会显示`<video>`内部的文字提示。
    
    `<video>`有以下属性：
    
    - `src`：视频文件的资源地址。
    - `controls`：播放器是否显示控制栏。该属性是布尔属性，如果存在该属性，浏览器会在视频底部提供一个控制面板，允许用户控制视频的播放，包括音量、拖动进度、暂停或恢复播放。如果不想使用浏览器默认的播放器，而想使用自定义播放器，就不要使用该属性，而是通过JS和CSS来控制。
    - `width`：视频播放器的宽度，单位像素。
    - `height`：视频播放器的高度，单位像素。
    - `autoplay`：视频是否自动播放，该属性是布尔属性。
    - `loop`：视频是否循环播放，该属性是布尔属性。
    - `muted`：是否默认静音，该属性是布尔属性。
    - `poster`：视频播放器的封面图片的 URL。
    - `preload`：视频播放之前，是否缓冲视频文件。这个属性仅适合没有设置`autoplay`的情况。它有三个值，分别是`none`（不缓冲）、`metadata`（仅缓冲视频文件的元数据）、`auto`（可以缓冲整个文件）。
    - `playsinline`：iPhone 的 Safari 浏览器播放视频时，会自动全屏，该属性可以禁止这种行为。该属性为布尔属性。
    - `crossorigin`：是否采用跨域的方式加载视频。它可以取两个值，分别是`anonymous`（跨域请求时，不发送用户凭证，主要是 Cookie），`use-credentials`（跨域时发送用户凭证）。
    - `currentTime`：指定当前播放位置（双精度浮点数，单位为秒）。如果尚未开始播放，则会从这个属性指定的位置开始播放。
    
    下面是一个例子。
    
    ```html
    <video width="400" height="400"
           autoplay loop muted
           poster="poster.png">
    </video>
    ```
    
    上面代码中，视频播放器的大小是 400 x 400，会自动播放和循环播放，并且静音，还带有封面图。这是网站首页背景视频的常见写法。
    
    HTML 标准没有规定浏览器需要支持哪些视频格式，完全由浏览器厂商自己决定。为了避免浏览器不支持视频格式，可以使用`<source>`标签，放置同一个视频的多种格式。
    
    ```html
    <video controls>
      <source src="example.mp4" type="video/mp4">
      <source src="example.webm" type="video/webm">
      <p>你的浏览器不支持 HTML5 视频，请下载<a href="example.mp4">视频文件</a>。</p>
    </video>
    ```
    
    上面代码中，`<source>`标签的`type`属性的值是视频文件的 MIME 类型，上例指定了两种格式的视频文件：MP4 和 WebM。如果浏览器支持 MP4，就加载 MP4 格式的视频，不再往下执行了。如果不支持 MP4，就检查是否支持 WebM，如果还是不支持，则显示提示。
    
  - ##### `<audio>`：
  
    `<audio>`标签是一个块级元素，用于放置音频，用法与`<video>`标签基本一致。
    
    ```html
    <audio controls>
      <source src="foo.mp3" type="audio/mp3">
      <source src="foo.ogg" type="audio/ogg">
      <p>你的浏览器不支持 HTML5 音频，请直接下载<a href="foo.mp3">音频文件</a>。</p>
    </audio>
    ```
    
    上面代码中，`<audio>`标签内部使用`<source>`标签，指定了两种音频格式：优先使用 MP3 格式，如果浏览器不支持则使用 Ogg 格式。如果浏览器不能播放音频，则提供下载链接。
    
    `<audio>`标签的属性与`<video>`标签类似，参见上一节。
    
    - `autoplay`：是否自动播放，布尔属性。
    - `controls`：是否显示播放工具栏，布尔属性。如果不设置，浏览器不显示播放界面，通常用于背景音乐。
    - `crossorigin`：是否使用跨域方式请求。
    - `loop`：是否循环播放，布尔属性。
    - `muted`：是否静音，布尔属性。
    - `preload`：音频文件的缓冲设置。
    - `src`：音频文件网址。
  
  - ##### `<track>`：
  
    `<track>`标签用于指定视频的字幕，格式是 WebVTT （`.vtt`文件），放置在`<video>`标签内部。它是一个单独使用的标签，没有结束标签。
    
    ```html
    <video controls src="sample.mp4">
      <track label="英文" kind="subtitles" src="subtitles_en.vtt" srclang="en">
      <track label="中文" kind="subtitles" src="subtitles_cn.vtt" srclang="cn" default>
    </video>
    ```
    
    上面代码指定视频文件的英文字幕和中文字幕。
    
    `<track>`标签有以下属性。
    
    - `label`：播放器显示的字幕名称，供用户选择。
    - `kind`：字幕的类型，默认是`subtitles`，表示将原始声音成翻译外国文字，比如英文视频提供中文字幕。另一个常见的值是`captions`，表示原始声音的文字描述，通常是视频原始使用的语言，比如英文视频提供英文字幕。
    - `src`：vtt 字幕文件的网址。
    - `srclang`：字幕的语言，必须是有效的语言代码。
    - `default`：是否默认打开，布尔属性。
  
  - ##### `<source>`：
  
    `<source>`标签用于`<picture>`、`<video>`、`<audio>`的内部，用于指定一项外部资源。该标签是单独使用的，没有结束标签。
    
    它有如下属性，具体示例请参见相应的容器标签。
    
    - `type`：指定外部资源的 MIME 类型。
    - `src`：指定源文件，用于`<video>`和`<audio>`。
    - `srcset`：指定不同条件下加载的图像文件，用于`<picture>`。
    - `media`：指定媒体查询表达式，用于`<picture>`。
    - `sizes`：指定不同设备的显示大小，用于`<picture>`，必须跟`srcset`搭配使用。
  
  - ##### `<embed>`：
  
    `<embed>`标签用于嵌入外部内容，这个外部内容通常由浏览器插件负责控制。由于浏览器的默认插件都不一致，很可能不是所有浏览器的用户都能访问这部分内容，建议谨慎使用。
    
    下面是嵌入视频播放器的例子。
    
    ```html
    <embed type="video/webm"
        src="/media/examples/flower.mp4"
        width="250"
        height="200">
    ```
    
    上面代码嵌入的视频，将由浏览器插件负责控制。如果浏览器没有安装 MP4 插件，视频就无法播放。
    
    `<embed>`标签具有如下的通用属性。
    
    - `height`：显示高度，单位为像素，不允许百分比。
    - `width`：显示宽度，单位为像素，不允许百分比。
    - `src`：嵌入的资源的 URL。
    - `type`：嵌入资源的 MIME 类型。
    
    浏览器通过`type`属性得到嵌入资源的 MIME 类型，一旦该种类型已经被某个插件注册了，就会启动该插件，负责处理嵌入的资源。
    
    下面是 QuickTime 插件播放 MOV 视频文件的例子。
    
    ```html
    <embed type="video/quicktime" src="movie.mov" width="640" height="480">
    ```
    
    下面是启动 Flash 插件的例子。
    
    ```html
    <embed src="whoosh.swf" quality="medium"
           bgcolor="#ffffff" width="550" height="400"
           name="whoosh" align="middle" allowScriptAccess="sameDomain"
           allowFullScreen="false" type="application/x-shockwave-flash"
           pluginspage="http://www.macromedia.com/go/getflashplayer">
    ```
    
    上面代码中，如果浏览器没有安装 Flash 插件，就会提示去`pluginspage`属性指定的网址下载。
  
  - ##### `<object>`，`<param>`：
  
    `<object>`标签作用跟`<embed>`相似，也是插入外部资源，由浏览器插件处理。它可以视为`<embed>`的替代品，有标准化行为，只限于插入少数几种通用资源，没有历史遗留问题，因此更推荐使用。
    
    下面是插入 PDF 文件的例子。
    
    ```html
    <object
    	type="application/pdf"
      data="/media/examples/In-CC0.pdf"
      width="250"
      height="200">
    </object>
    ```
    
    上面代码中，如果浏览器安装了 PDF 插件，就会在网页显示 PDF 浏览窗口。
    
    `<object>`具有如下的通用属性。
    
    - `data`：嵌入的资源的 URL。
    - `form`：当前网页中相关联表单的`id`属性（如果有的话）。
    - `height`：资源的显示高度，单位为像素，不能使用百分比。
    - `width`：资源的显示宽度，单位为像素，不能使用百分比。
    - `type`：资源的 MIME 类型。
    - `typemustmatch`：布尔属性，表示`data`属性与`type`属性是否必须匹配。
    
    下面是插入 Flash 影片的例子。
    
    ```html
    <object data="movie.swf" type="application/x-shockwave-flash"></object>
    ```
    
    `<object>`标签是一个容器元素，内部可以使用`<param>`标签，给出插件所需要的运行参数。
    
    ```html
    <object data="movie.swf" type="application/x-shockwave-flash">
    	<param name="foo" value="bar">
    </object>
    ```
  
- ## iframe

  `<iframe>`标签用于在网页里面嵌入其他网页。

  - #### 基本用法

    `<iframe>`标签生成一个指定区域，在该区域中嵌入其他网页。它是一个容器元素，如果浏览器不支持`<iframe>`，就会显示内部的子元素。
  
    ```html
    <iframe src="https://www.example.com"
         width="100%" height="500" frameborder="0"
         allowfullscreen sandbox>
    	<p><a href="https://www.example.com">点击打开嵌入页面</a></p>
    </iframe>
    ```
  
    上面的代码在当前网页嵌入`https://www.example.com`，显示区域的宽度是`100%`，高度是`500`像素。如果当前浏览器不支持`<iframe>`，则会显示一个链接，让用户点击。
  
    浏览器普遍支持`<iframe>`，所以内部的子元素可以不写。
  
    `<ifeame>`的属性如下：
  
    - `allowfullscreen`：允许嵌入的网页全屏显示，需要全屏 API 的支持，请参考相关的 JavaScript 教程。
    - `frameborder`：是否绘制边框，`0`为不绘制，`1`为绘制（默认值）。建议尽量少用这个属性，而是在 CSS 里面设置样式。
    - `src`：嵌入的网页的 URL。
    - `width`：显示区域的宽度。
    - `height`：显示区域的高度。
    - `importance`：浏览器下载嵌入的网页的优先级，可以设置三个值。`high`表示高优先级，`low`表示低优先级，`auto`表示由浏览器自行决定。
    - `name`：内嵌窗口的名称，可以用于`<a>`、`<form>`、`<base>`的`target`属性。
    - `referrerpolicy`：请求嵌入网页时，HTTP 请求的`Referer`字段的设置。参见`<a>`标签的介绍。
    - `sandbox`：设置嵌入的网页的权限，详见下文。
    - `loading`：详见下文。
  
  - #### `sandbox` 属性
  
    嵌入的网页默认具有正常权限，比如执行脚本、提交表单、弹出窗口等。如果嵌入的网页是其他网站的页面，你不了解对方会执行什么操作，因此就存在安全风险。为了限制`<iframe>`的风险，HTML 提供了`sandbox`属性，允许设置嵌入的网页的权限，等同于提供了一个隔离层，即“沙箱”。
    
    `sandbox`可以当作布尔属性使用，表示打开所有限制。
    
    ```html
    <iframe src="https://www.example.com" sandbox></iframe>
    ```
    
    `sandbox`属性可以设置具体的值，表示逐项打开限制。未设置某一项，就表示不具有该权限。（值之间用空格隔开）
    
    - `allow-forms`：允许提交表单。
    - `allow-modals`：允许提示框，即允许执行`window.alert()`等会产生弹出提示框的 JavaScript 方法。
    - `allow-popups`：允许嵌入的网页使用`window.open()`方法弹出窗口。
    - `allow-popups-to-escape-sandbox`：允许弹出窗口不受沙箱的限制。
    - `allow-orientation-lock`：允许嵌入的网页用脚本锁定屏幕的方向，即横屏或竖屏。
    - `allow-pointer-lock`：允许嵌入的网页使用 Pointer Lock API，锁定鼠标的移动。
    - `allow-presentation`：允许嵌入的网页使用 Presentation API。
    - `allow-same-origin`：不打开该项限制，将使得所有加载的网页都视为跨域。
    - `allow-scripts`：允许嵌入的网页运行脚本（但不创建弹出窗口）。
    - `allow-storage-access-by-user-activation`：`sandbox`属性同时设置了这个值和`allow-same-origin`的情况下，允许`<iframe>`嵌入的第三方网页通过用户发起`document.requestStorageAccess()`请求，经由 Storage Access API 访问父窗口的 Cookie。
    - `allow-top-navigation`：允许嵌入的网页对顶级窗口进行导航。
    - `allow-top-navigation-by-user-activation`：允许嵌入的网页对顶级窗口进行导航，但必须由用户激活。
    - `allow-downloads-without-user-activation`：允许在没有用户激活的情况下，嵌入的网页启动下载。
    
    注意：不要同时设置`allow-scripts`和`allow-same-origin`属性，这将使得嵌入的网页可以改变或删除`sandbox`属性。
    
  - #### `loading` 属性
  
    `<iframe>`指定的网页会立即加载，有时这不是希望的行为。`<iframe>`滚动进入视口以后再加载，这样会比较节省带宽。
    
    `loading`属性可以触发`<iframe>`网页的懒加载。该属性可以取以下三个值：
    
    - `auto`：浏览器的默认行为，与不使用`loading`属性效果相同。
    - `lazy`：`<iframe>`的懒加载，即将滚动进入视口时开始加载。
    - `eager`：立即加载资源，无论在页面上的位置如何。
    
    ```html
    <iframe src="https://example.com" loading="lazy"></iframe>
    ```
    
    上面代码会启用`<iframe>`的懒加载。
    
    有一点需要注意，**如果`<iframe>`是隐藏的，则`loading`属性无效，将会立即加载**。只要满足以下任一个条件，Chrome 浏览器就会认为`<iframe>`是隐藏的：
    
    - `<iframe>`的宽度和高度为4像素或更小。
    - 样式设为`display: none`或`visibility: hidden`。
    - 使用定位坐标为负`X`或负`Y`，将`<iframe`>放置在屏幕外。
  
- ## 表格

  表格（table）以行（row）和列（column）的形式展示数据。

  - ##### `<table>`，`<caption>`：

    `<table>`是一个块级容器标签，所有表格内容都要放在这个标签里面。
    
    ```html
    <table>
    	... ...
    </table>
    ```
    
    `<caption>`总是`<table>`里面的第一个子元素，表示表格的标题。该元素是可选的。
    
    ```html
    <table>
    	<caption>示例表格</caption>
    </table>
    ```

  - ##### `<thead>`、`<tbody>`、`<tfoot>`：

    `<thead>`、`<tbody>`、`<tfoot>`都是块级容器元素，且都是`<table>`的一级子元素，分别表示表头、表体和表尾。
    
    ```html
    <table>
     <thead>... ...</thead>
     <tbody>... ...</tbody>
     <tfoot>... ...</tfoot>
    </table>
    ```
    
    这三个元素都是可选的。如果使用了`<thead>`，那么`<tbody>`和`<tfoot>`一定在`<thead>`的后面。如果使用了`<tbody>`，那么`<tfoot>`一定在`<tbody>`后面。
    
    大型表格内部可以使用多个`<tbody>`，表示连续的多个部分。

  - ##### `<colgroup>`，`<col>`：

    `<colgroup>`是`<table>`的一级子元素，用来包含一组列的定义。`<col>`是`<colgroup>`的子元素，用来定义表格的一列。
    
    ```html
    <table>
      <colgroup>
        <col>
        <col>
        <col>
      </colgroup>
    </table>
    ```
    
    上面代码表明表格有3列。
    
    `<col>`不仅是一个单独使用的标签，没有结束标志，而且还是一个空元素，没有子元素。它的主要作用，除了申明表格结构，还可以为表格附加样式。
    
    ```html
    <table>
      <colgroup>
        <col class="c1">
        <col class="c2">
        <col class="c3">
      </colgroup>
      <tr>
        <td>1</td>
        <td>2</td>
        <td>3</td>
      </tr>
    </table>
    ```
    
    上面代码中，`<colgroup>`声明表格有三列，每一列有自己的 class，可以使用 CSS 针对每个 class 设定样式，会对整个表格生效。
    
    `<col>`有一个`span`属性，值为正整数，默认为`1`。如果大于1，就表示该列的宽度包含连续的多列。
    
    ```html
    <table>
      <colgroup>
        <col>
        <col span="2">
        <col>
      </colgroup>
    </table>
    ```
    
    上面代码中，表格的表头定义了3列，实际数据有4列。表头的第2列会连续跨2列。

  - ##### `<tr>`：

    `<tr>`标签表示表格的一行（table row）。如果表格有`<thead>`、`<tbody>`、`<tfoot>`，那么`<tr>`就放在这些容器元素之中，否则直接放在`<table>`的下一级。
    
    ```html
    <table>
      <tr>...</tr>
      <tr>...</tr>
      <tr>...</tr>
    </table>
    ```
    
    上面代码表示表格共有3行。

  - ##### `<th>`，`<td>`：

    `<th>`和`<td>`都用来定义表格的单元格。其中，`<th>`是标题单元格，`<td>`是数据单元格。
    
    ```html
    <table>
      <tr>
      	<th>学号</th><th>姓名</th>
      </tr>
      <tr>
      	<td>001</td><td>张三</td>
      </tr>
      <tr>
      	<td>002</td><td>李四</td>
      </tr>
    </table>
    ```
    
    上面代码中，表格一共有三行。第一行是标题行，所以使用`<th>`；第二行和第三行是数据行，所以使用`<td>`。
    
    **（1）`colspan`属性，`rowspan`属性**
    
    单元格会有跨越多行或多列的情况，这要通过`colspan`属性和`rowspan`属性设置，前者表示单元格跨越的栏数，后者表示单元格跨越的行数。它们的值都是一个非负整数，默认为1。
    
    ```html
    <table>
      <tr>
      	<td colspan="2">A</td><td>B</td>
      </tr>
      <tr>
      	<td>A</td><td>B</td><td>C</td>
      </tr>
    </table>
    ```
    
    上面代码中，第一行的第一个单元格会跨两列。
    
    **（2）`headers`属性**
    
    如果表格很大，单元格很多，源码里面会看不清，哪个单元格对应哪个表头，这时就可以使用`headers`属性。
    
    ```html
    <table>
      <tr>
      	<th id="no">学号</th><th id="names">姓名</th>
      </tr>
      <tr>
      	<td headers="no">001</td><td headers="names">张三</td>
      </tr>
      <tr>
      	<td headers="no">002</td><td headers="names">李四</td>
      </tr>
    </table>
    ```
    
    上面代码中，标题栏的`<th>`设置了`id`属性，后面的`<td>`单元格的`headers`属性就对应这些`id`属性的值，因此就能看出来这些单元格对应哪个标题栏。
    
    `headers`属性的值总是对应`<th>`标签的`id`属性的值。由于一个单元格可以对应多个标题栏（跨行的情况），所以`headers`属性可以是一个空格分隔的字符串，对应多个`id`属性的值。
    
    **（3）`scope`属性**
    
    `scope`属性只有`<th>`标签支持，一般不在`<td>`标签使用，表示该`<th>`单元格到底是栏的标题，还是列的标题。
    
    ```html
    <table>
     <tr>
        <th scope="col">姓名</th>
        <th scope="col">学号</th>
        <th scope="col">性别</th>
     </tr>
     <tr>
        <th scope="row">张三</th>
        <td>001</td>
        <td>男</td>
     </tr>
     <tr>
        <th scope="row">李四</th>
        <td>002</td>
        <td>男</td>
     </tr>
    </table>
    ```
    
    上面代码中，第一行的标题栏都是列标题，所以`<th>`的`scope`属性为`col`，第二行和第三行的第一列是行标题，所以`<th>`标签的`scope`属性为`row`。
    
    `scope`属性可以取下面这些值：
    
    - `row`：该行的所有单元格，都与该标题单元格相关。
    - `col`：该列的所有单元格，都与该标题单元格相关。
    - `rowgroup`：多行组成的一个行组的所有单元格，都与该标题单元格相关，可以与`rowspan`属性配合使用。
    - `colgroup`：多列组成的一个列组的所有单元格，都与该标题单元格相关，可以与`colspan`属性配合使用。
    - `auto`：默认值，表示由浏览器自行决定。
    
    下面是一个`colgroup`属性和`rowgroup`属性的例子。
    
    ```html
    <table>
      <thead>
        <tr>
          <th scope="col">海报名称</th>
          <th scope="col">颜色</th>
          <th colspan="3" scope="colgroup">尺寸</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th rowspan="3" scope="rowgroup">Zodiac</th>
          <th scope="row">Full color</th>
          <td>A2</td>
          <td>A3</td>
          <td>A4</td>
        </tr>
        <tr>
          <th scope="row">Black and white</th>
          <td>A1</td>
          <td>A2</td>
          <td>A3</td>
        </tr>
        <tr>
          <th scope="row">Sepia</th>
          <td>A3</td>
          <td>A4</td>
          <td>A5</td>
        </tr>
      </tbody>
    </table>
    ```
    
    上面的例子中，列标题“尺寸”的`scope`属性为`colgroup`，表示这个标题单元格对应多列（本例为3列）；行标题的`scope`属性为`rowgroup`，表示这个标题单元格对应多行（本例为3行）。
    
    渲染结果就是下面的样子：
    
    | 海报名称   | 颜色                | 尺寸 |      |      |
    | ---------- | ------------------- | ---- | ---- | ---- |
    | **Zodiac** | **Full color**      | A2   | A3   | A4   |
    |            | **Black and white** | A1   | A2   | A3   |
    |            | **Sepia**           | A3   | A4   | A5   |

- ## 其他标签

  > 本章介绍一些最新引入标准的标签。

  - #### `<dialog>`

    - ##### 基本用法：

      `<dialog>`标签表示一个可以关闭的对话框。
      
      ```html
      <dialog>
      	Hello world
      </dialog>
      ```
      
      上面就是一个最简单的对话框。
      
      默认情况下，对话框是隐藏的，不会在网页上显示。如果要让对话框显示，必须加上`open`属性。
      
      ```html
      <dialog open>
      	Hello world
      </dialog>
      ```
      
      上面代码会在网页显示一个方框，内容是`Hello world`。
      
      `<dialog>`元素里面，可以放入其他 HTML 元素。
      
      ```html
      <dialog open>
        <form method="dialog">
          <input type="text">
          <button type="submit" value="foo">提交</button>
        </form>
      </dialog>
      ```
      
      上面的对话框里面，有一个输入框和提交按钮。
      
      注意，上例中`<form>`的`method`属性设为`dialog`，这时点击提交按钮，对话框就会消失。但是，表单不会提交到服务器，浏览器会将表单元素的`returnValue`属性设为 Submit 按钮的`value`属性（上例是`foo`）。

    - ##### JavaScript API：

      `<dialog>`元素的 JavaScript API 提供`Dialog.showModal()`和`Dialog.close()`两个方法，用于打开/关闭对话框。
      
      ```js
      const modal = document.querySelector('dialog');
      
      // 对话框显示，相当于增加 open 属性
      modal.showModal();
      
      // 对话框关闭，相当于移除 open 属性
      modal.close();
      ```
      
      开发者可以提供关闭按钮，让其调用`Dialog.close()`方法，关闭对话框。
      
      `Dialog.close()`方法可以接受一个字符串作为参数，用于传递信息。`<dialog>`接口的`returnValue`属性可以读取这个字符串，否则`returnValue`属性等于提交按钮的`value`属性。
      
      ```js
      modal.close('Accepted');
      modal.returnValue // "Accepted"
      ```
      
      `Dialog.showModal()`方法唤起对话框时，会有一个透明层，阻止用户与对话框外部的内容互动。CSS 提供了一个 Dialog 元素的`::backdrop`伪类，用于选中这个透明层，因此可以编写样式让透明层变得可见。
      
      ```css
      dialog {
        padding: 0;
        border: 0;
        border-radius: 0.6rem;
        box-shadow: 0 0 1em black;
      }
      
      dialog::backdrop {
        /* make the backdrop a semi-transparent black */
        background-color: rgba(0, 0, 0, 0.4);
      }
      ```
      
      上面代码不仅为`<dialog>`指定了样式，还将对话框的透明层变成了灰色透明。
      
      `<dialog>`元素还有一个`Dialog.show()`方法，也能唤起对话框，但是没有透明层，用户可以与对话框外部的内容互动。

    - ##### 事件：

      `<dialog>`元素有两个事件，可以监听。
      
      - `close`：对话框关闭时触发
      - `cancel`：用户按下`esc`键关闭对话框时触发
      
      如果希望用户点击透明层，就关闭对话框，可以用下面的代码。
      
      ```js
      modal.addEventListener('click', (event) => {
        if (event.target === modal) {
        	modal.close('cancelled');
        }
      });
      ```

  - #### `<details>`，`<summary>`

    - ##### 基本用法：

      `<details>`标签用来折叠内容，浏览器会折叠显示该标签的内容。
      
      ```html
      <details>
      	这是一段解释文本。
      </details>
      ```
      
      上面的代码在浏览器里面，会折叠起来，显示`Details`，前面有一个三角形，就像下面这样。
      
      ```tex
      ▶ Details
      ```
      
      用户点击这段文本，折叠的文本就会展开，显示详细内容。
      
      ```tex
      ▼ Details
      这是一段解释文本。
      ```
      
      再点击一下，展开的文本又会重新折叠起来。
      
      `<details>`标签的open属性，用于默认打开折叠。
      
      ```html
      <details open>
      	这是一段解释文本。
      </details>
      ```
      
      上面代码默认打开折叠。
      
      `<summary>`标签用来定制折叠内容的标题。
      
      ```html
      <details>
        <summary>这是标题</summary>
        这是一段解释文本。
      </details>
      ```
      
      上面的代码显示结果如下。
      
      ```tex
      ▶ 这是标题
      ```
      
      点击后，展示的效果如下。
      
      ```tex
      ▼ 这是标题
      这是一段解释文本。
      ```
      
      通过 CSS 设置`summary::-webkit-details-marker`，可以改变标题前面的三角箭头。
      
      ```css
      summary::-webkit-details-marker {
        background: url(https://example.com/foo.svg);
        color: transparent;
      }
      ```
      
      下面的样式是另一种替换箭头的方法。
      
      ```css
      summary::-webkit-details-marker {
      	display: none;
      }
      summary:before {
        content: "\2714";
        color: #696f7c;
        margin-right: 5px;
      }
      ```

    - ##### JavaScript API：

      `Details`元素的`open`属性返回`<details>`当前是打开还是关闭。
      
      ```js
      const details = document.querySelector('details');
      
      if (detail.open === true) {
      	// 展开状态
      } else {
      	// 折叠状态
      }
      ```
      
      `Details`元素有一个`toggle`事件，打开或关闭折叠时，都会触发这个事件。
      
      ```js
      details.addEventListener('toggle', event => {
        if (details.open) {
        	/* 展开状况 */
        } else {
        	/* 折叠状态 */
        }
      });
      ```

------

