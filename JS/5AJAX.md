# AJAX

------

### 什么是AJAX?

> AJAX（Asynchronous Javascript And Xml）：异步js和xml的一个技术。简单来说就是使用浏览器的内置对象XMLHttpRequest对象与服务器进行异步通信的一个技术。

### 传统请求及缺点

> 传统请求有哪些方式？
> - 直接在浏览器地址栏上输入URL
> - 超链接
> - form表单
> - 使用img标签发送资源请求
> - ...
>
> 传统请求存在的问题：页面全部刷新导致了用户的体验极差。传统的请求导致用户的体验有空白期，体验不连贯。因为一点链接发送请求过去之后，页面内容就被清空了，空白了，只能等请求处理完毕之后响应回来全新的数据，如果网络不太好，那么对于用户来说体验很差。能不能只刷新网页中的部分区域呢？可以，通过AJAX。

### AJAX概述：

> - AJAX不能称为一种技术，属于JS代码发送http请求，它和普通a标签发送请求的方式不同，它默认发送的是异步请求，不会刷新整个页面。
> - 什么是异步，什么是同步？假设有t1和t2线程，t1和t2线程同时并发进行，谁也不影响谁，这就是异步。如果t1和t2排队执行，需要等待前面线程t1执行完毕t2才能开始执行，这就是同步，同步就是排队。
> - AJAX就可以发送异步http请求。也就是说，发送请求时不影响页面加载或下面代码的执行，等请求回来后，触发某事件，再执行数据的渲染，这样就完成了网页的局部刷新。
> - 我们之前通过浏览器地址栏上发送`get/post`请求就不是异步的，是同步请求。如果网络不好，页面要一直转圈直到S端响应
> - AJAX传输的数据格式可以是：xml、txt文本、JSON文本、HTML文本等。
> - AJAX可以做到在一个网页中同时启动多个请求，类似于网页中启动多个线程，一个线程对应一个请求。这些请求可以是同步的也可以是异步的。具体哪种可以设置。
> - 在js中发送http请求的其中一种方式就是使用“XMLHttpRequest对象”，我们这里就采用这种方式来发送异步请求。
> - AJAX请求也有缺点：
>   1. 没有浏览历史，不能像普通请求一样回退
>   2. 存在“AJAX跨域”的问题。跨域是指在网页中，一个域（站点）的页面通过AJAX访问另一个域的资源时，由于浏览器的同源策略（Same Origin Policy）的限制，导致请求被阻止的情况。跨域是由于浏览器的安全机制，为了防止恶意网站窃取用户信息或进行恶意操作而设立的。
>   3. 对SEO（搜索引擎优化）不友好。因为第一次请求网页时，这些内容并没有在源代码中，所以搜索引擎爬虫找不到这些内容所以无法进行优化。

### AJAX4步：

```js
document.querySelector("#btn").onclick = function(){
//AJAX4步
    //	1、创建ajax核心对象（通过XMLHttpRequest()构造函数）
    const xhr = new XMLHttpRequest()

    //	2、请求初始化，开启tcp链接通道
    //第1个参数设置请求类型（大写），第2个参数设置请求url，如果是get请求url中还有查询字符串，第3个参数true表示异步的（默认）
    xhr.open("GET","/module1?usercode=110&user=zhangsan",true)

    //	3、发送请求（请求的一些设置要在发送之前做）；如果是post请求，要将数据作为参数传进去；
    xhr.send();

    //	4、注册回调函数
    xhr.addEventListener('readystatechange', () => {//也可以用loadend事件，无论成功还是失败都会调用回调函数
        if (xhr.readyState === 4) {//readyState属性保存http请求的实时状态
            //status属性保存http协议的“响应状态码”；2开头的都是成功
            if (xhr.status >= 200 && xhr.status < 300) {
                console.log(xhr.getAllResponseHeaders());//获取所有响应头
                console.log(xhr.response);//response属性保存响应体
                console.log(responseText);//以文本的方式解析响应体
            }else{
                //响应失败打印状态码
                console.log(xhr.status);
                //响应状态描述
                console.log(xhr.statusText);
            }
        }
    })
    //第4步也可以这样
//    xhr.addEventListener('loadend', () => {
//        console.log(xhr.response)
//    }
}
```

### 关于XMLHttpRequest对象：

> `XMLHttpRequest`对象是AJAX的核心对象，发送ajax请求以及接收返回来的数据就全靠它了，xhr对象现在的浏览器都支持，都“内置”了该对象的构造函数，直接用就行。

- 属性：

  > - `readyState`：AJAX请求对象xhr的请求状态。它有5个值：0请求未初始化（xhr对象刚new出来的初值），1链接已建立（`open()`已执行），2请求已收到（`send()`已执行），3正在处理返会结果，4结构已处理完毕，请求结束。（所以`readystatechange`是一个事件，就是指，xhr的`readystate`属性发生了改变这个事件，也就是请求状态发生了改变）
  > - `status`：保存http协议的“响应状态码”
  > - `statusText`：保存http响应状态的描述信息
  > - `response`：响应体，它根据xhr对象的`responseType`属性来解析响应体。如果未设置`responseType`，默认根据S端设置的，响应头的`Content-Type`来解析响应体数据，通常不用设置`responseType`
  > - `responseText`：纯文本方式解析响应体
  > - `responseXML`：以XML文本的方式解析响应体数据
  > - `responseType`：设置响应体的内容类型（String），response中会根据该属性的设置来决定如何解析响应体数据。值可以是：'xml'、'json'、'script'、'html'、'text'等。默认是text文本数据。不设置会根据响应头的设置来解析。
  > - `timeout`：用于设置超时取消。值2000表示2000ms后还不响应就取消请求。它还对应一个事件`timeout`，用于超时处理，`error`表示断网事件

- 方法：

  > - `open('POST',url,async,user,psw)`：建立tcp链接，打开数据流通道。参数1是请求类型，参数2是url请求路径，参数3是true异步或false同步。user和psw是可选的，需不需要密码认证得看服务器端要求。
  > - `send()/send(string)`：发送请求；如果是post请求，数据要作为String型参数传进去。参数类型还可以是`ArrayBuffer、Blob、Document、FormData`等类型。
  > - `getAllResponseHeaders()`：获取所有响应头
  > - `getResponseHeader('响应头')`：获取特定的响应头
  > - `setRequestHeader()`：设置请求头。如果要发送post请求，发送之前需要通过此方法指定请求头：`xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded")`，指定请求体的内容类型
  > - `abort()`：取消当前请求

------

### 通过jQuery来发送AJAX请求：

```js
//还有`$.get()`和`$.post()`函数
$.ajax({
    type: 'GET/POST',
	//设置请求体的MIME类型，相当于xhr.setRequestHeader('Content-Type','application/json')
	contentType: 'application/json',
    url: '',
    data: {},//请求体提交的数据
    dataType: 'json',//以什么方式解析响应的数据，相当于responseType
    success: function(data){},
    timeout: 2000,//超时时间为2秒钟
    error: function(xhr, status, error){},
    complete: function(xhr){},//请求完成后，无论成功或失败都会调用该函数
    headers: {
        contentType: 'application/json',
//自定义请求头，此时后端应该：response.setHeader("Access-Control-Allow-Headers", "*");允许这个自定义头，否则前端会报错
        "自定义请求头": "值"
    }
})
```

------

### axios发送AJAX请求：

> - `axios`是目前前端最热门的AJAX工具包，使用频率很高，是Vue和React都推荐使用的工具。（底层封装的是XHR对象）
> - axios是一个基于Promise的HTTP客户端，可以在浏览器和NodeJS环境下使用。特点：
>   - 在浏览器端发送AJAX请求。
>   - 在服务器端发送HTTP请求。
>   - 支持Promise API。
>   - 请求响应拦截器。
>   - 转换请求和响应的数据。
>   - 取消请求。
>   - 自动将结果转为JSON格式。
>   - 保护客户端避免XSRF攻击。

> 我们这里用在线地址引入：`<script src="https://unpkg.com/axios/dist/axios.min.js"></script>`，使用：

```js
// axios函数接收一个配置对象并发送ajax请求（XHR的方式），返回值是一个promise对象
axios({
    method: 'POST', // 请求方式
    url: 'http://localhost:3000/posts/2',
    // params: {name: zs, age: 12}, // 设置url中携带的查询字符串参数
    data: { name: 'John', age: 30 }, // 设置请求体的数据。若data的值为对象，那么会自动转成JSON串发送
    headers: { // 设置请求头，可以包含自定义请求头
        'Content-Type': 'application/json', // 设置请求头中的Content-Type内容类型为JSON格式
    }
}).then(function (result) { // promise对象成功状态的回调
    console.log('Response data: ', result.data) // axios将成功的数据放在了结果值对象的data属性中了
}).catch(function (error) { // promise对象失败状态的回调
    console.error(error)
})
// 每个请求方式都有对应的`axios.xxx({})`函数，这些函数都是对axios()函数的进一步封装
```

> 还有一个`axios.request({})`函数（axios()函数底层就是该函数），它允许你以更加灵活的方式发送 HTTP 请求。你可以通过这个方法自定义请求的所有方面，包括请求方法、URL、数据、头部信息等等。不过大多数情况下 `axios` 函数足够用了，它已经包含了大部分常用的配置选项。

###### 关于axios返回的promise对象，它成功状态的结果值：

> 该promise对象成功状态的结果值是一个对象，对象中有如下属性：
>
> - config：这是我们传进去的配置对象，以及一些AJAX请求中默认的配置。
> - data：服务器响应的结果，也就是响应体的内容。如果服务器响应的是JSON串，那么axios会自动将其解析为JS对象。
> - headers：响应头信息。
> - request：它是原生的XMLHttpRequest对象，axios就是对它的封装。
> - status：响应状态码。
> - statusText：响应状态字符串。

- ##### 关于axios()函数接收的配置对象：

  > - method：设置请求方式（字符串）。默认为GET请求。
  > - url：设置请求的URL（字符串）。
  > - baseURL：设置请求URL的基路径（字符串）。如果请求的URL为相对路径，那么会用该基路径拼接形成绝对路径后再发送请求。
  > - headers：设置请求头信息（对象）。
  > - params：设置请求URL中的查询字符串参数（对象）。
  > - （不常用）paramsSerializer：用于将查询字符串序列化的函数。也就是用于自定义params对象转成的查询字符串的格式。
  > - data：设置请求体数据。可以是对象或字符串格式。如果是对象，axios会将其转成JSON串再发送；如果是查询字符串则直接发送。
  > - timeout：指定请求超时的毫秒数（Number）。超时请求会被取消。默认值0永不超时。
  > - responseType：响应体的内容类型（字符串），用于告诉浏览器如何解析响应体的。值包括`array\buffer\document\json\text\stream`，还有浏览器专属的`blob`。默认是`json`。
  > - maxContentLength：设置响应体所允许的最大字节数（Number）。

  > 不常用的配置项：
  >
  > - onUploadProgress/onDownloadProgress：指定上传和下载时的回调（浏览器专属）。回调函数可以接收到一个参数progressEvent。
  >
  > - auth：设置XHR请求的用户名和密码的，值是一个对象：`{username: '用户名', password: '密码'}`
  >
  > - adapter：设置请求的适配器，允许自定义处理请求。值是一个函数function(config)。（详细请看：/lib/adapters/README.md）
  >
  > - withCredentials：表示跨域请求时是否需要使用携带凭证Cookie，默认false不携带。true时可以在跨域请求中携带Cookie。
  >
  > - transformRequest：对请求的数据进行处理，再将处理后的结果发送给服务器。值为函数数组，这是为了支持多个数据转换步骤。数组中的函数按照它们在数组中的顺序依次执行，前一个函数的输出作为下一个函数的输入（类似管道操作）。
  >
  > - transformResponse：对响应的数据进行处理，再将处理后的结果放在data中。值同样是函数数组。
  >
  >   ....还有其他的，需要时去axios官网查即可。

- ##### axios的默认配置：

  > 默认配置的设置是项目中非常实用的技巧，它可以把一些重复性的设置配置到默认设置里，简化我们的代码。只需要在使用前，通过`axios.defaults.配置项=值`配置下即可。

- ##### 通过axios实例来发送AJAX请求：

  1. 创建axios实例：

     ```js
     const ais = axios.create({
         baseURL: 'https://api.apiopen.top',
         timeout: 3000
     })
     ```

     > 这里的ais是一个函数，它和axios函数的功能几乎是一样的。

  2. 通过创建的axios函数来发送请求：

     ```js
     ais({
     	url: '/getJoke'
     }).then(resp=>{
     	console.log(resp)
     })
     ```

     > 也可以：`ais.get('/getJoke').then(resp=>{ console.log(resp) })`

  > 用axios实例来发送AJAX的方式更灵活。

- ##### axios中的拦截器：

  > axios中的拦截器分为**请求拦截器**和**响应拦截器**（函数）。其中请求拦截器可以让我们在发送请求之前做拦截，满足条件后再发。当服务器响应了结果后，可以在响应拦截器中先对结果做预处理，这样拿到的响应数据就可以直接用了。拦截器就像请求过程中的一道道关卡，通过这些关卡可以更精细的控制请求和响应的过程。

  ###### 使用：

------

### 通过JS的fetch函数发送AJAX请求：

> 通过JS的全局函数`fetch()`也可以发送AJAX请求，它是`XMLHttpRequest`的升级版（在一些旧的浏览器上不能用）。用法：

```js
fetch('url?name=value..',{
    method: 'POST',
    headers: {(添加头文件)
        'Content-Type': 'application/json;charset=UTF-8'
    },
    body: '请求体数据'
}).then(function(response)){
    return response.json()
}.then(response){
    console.log(response)
}
```

> Node.js 18 之后也支持 `fetch()`，用法和浏览器端基本一致。它是基于 Node.js 的 **`undici`** （高性能 HTTP 客户端）实现的。

------

### AJAX的跨域访问：

- #### 关于浏览器的同源策略：

  > - **同源策略**：浏览器为了确保资源的安全，而遵循的一种策略。所谓同源是指：协议、域名、端口都相同就是同源。若所处源与目标源不一致，就是非同源（跨域）。所谓同源策略就是，浏览器对跨域的资源访问、资源操作会进行拦截。
  >
  > - 例如：源A和源B是非同源的，那么浏览器会有如下限制。
  >
  >   - 限制DOM访问。（源A的脚本不能操作源B的DOM）
  >   - 限制Cookie访问。（源A的不能访问源B的Cookie）
  >   - 限制AJAX获取数据。（源A给源B发送的AJAX请求所返回的数据会被浏览器拦截住）
  >
  >   > 在上述限制中，浏览器对AJAX获取数据的限制对我们来说是影响最大的一个，且实际开发中经常会遇到。因此我们才要解决AJAX跨域的问题。
  >
  > - `<link>、<img>、<a>、<script>...`这些标签发送的请求也可能跨域，只不过默认情况下浏览器对标签跨域不做严格限制，所以对开发无影响。

- #### 解决AJAX跨域：

  - ##### 方案1：CORS（Cross-Origin Resource Sharing）跨域资源共享

    > 这是官方的跨域解决方案，是用于控制浏览器校验跨域请求的一套规范，服务器按照CORS规范，添加特应响应头来控制浏览器的校验。它的特点是不需要在前端做任何操作，只在后端设置，且支持GET和POST（其他也支持）。

    - ###### CORS解决简单请求跨域：

      > 服务器设置响应头`Access-Control-Allow-Origin: http://localhost:8080`（值要和发送请求的URL一模一样），表示允许这个源http://localhost:8080/对我服务器的资源进行AJAX跨域访问。（响应头`*`表示该服务器的资源允许所有源跨域访问）

      > **简单请求和复杂请求：**
      >
      > CORS会把请求分为2类，分别是**简单请求**和**复杂请求**。其中简单请求要符合以下要求：
      >
      > - 请求方式为GET\POST\HEAD
      > - 请求头字段要符合《CORS安全规范》（一般不手动更改请求头的话都符合该规范）
      > - 请求头的`Content-Type`的值只能是这3种：`text/plain`、`multipart/form-data`、`application/x-www-form-urlencoded`
      >
      > 只要不符合以上要求的都是复杂请求。复杂请求会自动发送一次**预检请求**。关于预检请求：
      >
      > 1. 发送时机：预检请求在实际的跨域请求之前，由浏览器自动发出。
      > 2. 主要作用：用于向服务器确认是否允许接下来的跨域请求。
      > 3. 基本流程：先发起`OPTIONS`请求，如果通过预检，继续发起实际的跨域请求。
      > 4. 请求头内容：一个`OPTIONS`预检请求，通常会包含以下请求头：
      >    - Origin：请求源
      >    - Access-Control-Request-Method：实际请求的方式
      >    - Access-Control-Request-Headers：实际请求中所使用的自定义头（如果有的话）

    - ###### CORS解决复杂请求跨域：

      1. 需要先通过浏览器发送的预检请求，服务器需要返回如下响应头：（在Node中通常用`cors`库来做第1步）
         - Access-Control-Allow-Origin：允许的源
         - Access-Control-Allow-Methods：允许的方法
         - Access-Control-Allow-Headers：允许的自定义头
         - （可选）Access-Control-Max-Age：预检请求的响应结果的缓存时间。也就是在该时间内就不需要再发送预检请求了。（一般会设置这个）
      2. 然后就可以正常处理跨域请求了，与处理简单请求跨域方式相同。

  ------

  - ##### 方案2：JSONP（JSON with Padding）

    > JSONP是利用了`<script>`标签可以跨域加载脚本，且不受严格限制的特性来解决的跨域（只能处理GET请求跨域）。（它是非官方的跨域解决方案，野路子，可以说是程序员智慧的结晶，早期一些浏览器不支持CORS时，可以靠JSONP解决跨域）。
    
    ###### JSONP的原理是：（类似于Vue的自定义事件）
    
    > 前端通过`<script>`标签的`src`属性来发送GET请求，服务器响应的是一段函数调用的js：
    >
    > ```js
    > demo({name:'张三',age:18})
    > ```
    >
    > 此时如果恰好前端有一个`demo(v)`函数，就刚好触发了调用，通过函数的形参拿到了数据。
    
    ###### 注意：JSONP解决跨域有局限性，只能解决GET请求跨域。因为`<script>`标签只能发送GET请求。
    
    ###### jQuery封装的JSONP：
    
    ```js
    // 使用jquery封装的函数，发送伪ajax请求，本质上是jsonp乔装打扮的
    $.ajax({
      type : "GET",	// 注意：jsonp只支持get请求，所以这里必须是get，省略的话默认也是get
      // 注意：这里的url中没有跟callback去指定回调函数名，它默认用的callback。后端需要获取query中的callback关键字
      url : "http://localhost:8080/web01",
      // 一定要指定返回的数据类型为“jsonp”（关键）
      dataType : "jsonp",
      // 指定返回时的回调
      success : function (data){ //data通常是一个json：{"username":"张三"}
        $("#mydiv").html("欢迎你："+ data.username)
      }
    })
    /*如果想自定义返回时的回调，用这两个属性换掉success：
    		jsonp: "后端的函数名"
    		jsonpCallback: "前端的函数名"
    */
    ```

------

