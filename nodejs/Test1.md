# NodeJS

## 入门

- ### 什么是Node.JS？

  > Node.JS是一个开源的、跨平台的，JavaScript运行时环境，它是基于Chrome V8引擎（底层c++）进行的封装。通俗的讲，Node.js就是一个应用程序，一个软件，它提供了在浏览器外运行js的环境。

- ### Node.JS可以干什么？

  1. 作为服务器端语言，编写服务器程序，web应用。
  2. 开发工具类应用。如Webpack、vite、Babel等工具。
  3. 开发桌面端应用。如VScode，Figma，Postman等软件，它们的开发都借助了`electron`框架，而该框架用了node.js语言。

- ### 下载与安装：

  > 直接去nodejs官网，选择LTS长期支持版下载即可，安装步骤一直下一步，不要勾选任何东西。安装完毕后环境变量自动配好了，cmd中输入`node -v`查看是否安装成功。

- ### Node.js和浏览器环境下的js的区别：

  - Node环境的API和浏览器中的API不通用。
  - 浏览器环境下的，WebAPI包括：DOM、BOM、AJAX、Stroage、alert、confirm、console、定时器..
  - nodejs环境下的API包括：fs、url、http、util、path、console、定时器..（定时器和console控制台模块通用，其他的不通用）
  - nodejs的顶级对象是`global`，也可以使用`globalThis`来访问顶级对象，一样的。
  - 

------

## Buffer对象

> Buffer中文叫缓冲区，是一个类似于Array的对象，用于表示固定长度的字节序列。换句话说，Buffer就是一段定长的内存空间，用于存储二进制数据。它的特点：
>
> 1. 大小固定无法调整
> 2. 性能较好，可以直接操作内存
> 3. Buffer中的每个单元为1B（字节）

1. ### Buffer的创建：

   ```javascript
   // 开辟10字节的内存缓冲区
   let buf1 = Buffer.alloc(10)
   // 这种方式不安全，但效率高点，内存中可能有脏数据
   let buf2 = Buffer.allocUnsafe(10)
   // 可以传进去字符串或数组，用该数据来初始化缓冲区，缓冲区大小根据内容来决定，用utf8编码
   let buf3 = Buffer.from('hello')
   ```

2. ### Buffer与字符串的转换：

   ```javascript
   let buf = Buffer.from([101,102,103])
   console.log(buf.toString()) // 默认根据UTF8来解码
   ```

3. ### Buffer的读写：（像数组一样，可以通过中括号[]来对缓冲区中的每个字节进行读写）

   ```javascript
   // 默认采用utf8来进行编码，大小是6B
   let buf = Buffer.from('你好')
   // 修改，如果数值超过了一个字节能够表示的范围，那么高位舍弃
   buf[0] = 95
   console.log(buf[0].toString(2)) // 转成二进制串输出
   ```

------

## fs（File System）模块

> fs模块可以实现与硬盘的交互，完成对文件相关的操作。使用前需要先用`require('fs')`函数导入该模块对应的JS文件（fs是模块标识符，模块的名字）
>
> （`__dirname`是CommonJS模块化环境中的一个“全局变量”（ESM中没有），里面保存的是当前源码所在目录的绝对路径串；`__filename`这个是当前源码文件的绝对路径）

- #### 文件读取：

  1. 普通读文件：用`fs.readFile(文件名[,可选配置项],回调函数)`来做。

     ```javascript
     const fs = require('fs')
     //用fs.readFile(文件名,可选的配置项,回调函数)函数完成异步读文件
     fs.readFile('./a.txt',(err,data) => {
         //读取完成后该函数自动调用。如果读取失败会传err对象，成功读取err为null。data是读到的数据，是Buffer对象
         if(err){
             console.log('读取失败')
         }else{
             console.log('读取成功')
             console.log(data.toString())
         }
     })
     ```
     
     > 默认异步读取，如果想要同步读取，用：`fs.readFileSync(文件名[,可选配置项])`，返回值为Buffer对象，没有回调函数。
     
  2. 流式读取：
  
     ```javascript
     const fs = require('fs')
     //开启通道，返回文件流对象
     const rs = fs.createReadStream('./a.txt')
     //给读取流rs绑定`data`事件，读取一块Buffer数据就执行一次回调函数，一块数据大小为64KB
     rs.on('data', chunk => {
     	console.log(chunk)
     })
     //（可选的）还可以给读取流，绑定end事件，读取完成自动调用函数
     rs.on('end', ()=> {
     	console.log('读取完成')
         //一定要在函数里面关闭流，不能在外面关
     	rs.close();
     })
     ```


- #### 文件写入：

  1. 普通写文件：用`fs.writeFile(文件名,数据[,可选配置项],错误回调函数)`来做

     ```javascript
     //首先，导入fs模块，就像Java中的导包
     const fs = require('fs')
     //用fs.writeFile(文件名,数据,可选的配置项,回调函数)函数完成异步写文件
     fs.writeFile('./a.txt','我是一个数据', err => {
         //该回调函数写入完成后自动调用。如果写入失败会传err对象，成功err为null
         if(err){
             console.log('写入失败')
         }else{
             console.log('写入成功')
         }
     })
     ```

     > 默认异步写入，如果想要同步写入，用：`fs.writeFileSync(文件名,数据[,可选的配置项])`，此时参数表中就不需要回调函数了
     >
     > 如果要追加写入：指定下配置项参数：`{ flag: 'a' }`，或者用追加写入的函数：
     >
     > - 追加写入：`fs.appendFile(文件名,数据[,可选配置项],错误回调函数)`
     >
     >
     > - 同步追加：`fs.appendFileSync(文件名,数据[,可选配置项])`
     >

  2. 流式写入：
  
     ```javascript
     //第一步永远是，导入fs模块
     const fs = require('fs')
     //创建写入流对象，开启通道
     const ws = fs.createWriteStream('./a.txt')
     //往里面写数据
     ws.write('hello')
     ws.write('world')
     //最后关闭通道，关闭流
     ws.close();//不加的话，代码执行完毕也会自动关掉，建议手动写上
     ```
  
     > 流式写入适合写入频繁的场景，要时不时进行写入的场景。而且适合大文件写入。


- #### 写一个文件复制：

  > 方式1：普通方式复制

  ```javascript
  const fs = require('fs')
  //方式1：
  let data = fs.readFileSync(${__dirname} +'/a.txt')//返回Buffer类型的数据
  fs.writeFileSync('./a1.txt', data)
  ```

  > 方式2：流式复制

  ```javascript
  const fs = require('fs')
  //方式2：
  let rs = fs.createReadStream('./a.txt')
  let ws = fs.createWriteStream('./a1.txt')
  rs.on('data', chunk => {
      ws.write(chunk)
  })
  rs.on('end', () => {
      console.log('复制完成')
      rs.close()
      ws.close()
  })
  //也可以直接这样做，不用data事件绑定的方式
  //rs.pipe(ws)
  ```
  
  > 第2种方式更好一些，占用内存少。
  
- #### 文件移动和重命名：用`rename(oldPath,newPath,callback)`和`renameSync(oldPath,newPath)`，移动和重命名本质是一样的，不涉及磁盘间移动时，就只是改了操作系统管理的文件信息而已。

  ```javascript
  const fs = require('fs')
  //重命名或文件移动
  fs.rename('./a.txt','./a1.txt', err => {
  	if(err){
          console.log('移动失败')
      }else{
          console.log('移动成功')
      }
  })
  ```

- #### 文件删除：用`rm(path,callback)`或`unlink(path,callback)`来删除文件

  ```javascript
  const fs = require('fs')
  //Node 14.4版本之后才可以用rm()来删除，它也有对应的rmSync()
  fs.rm('./a.txt', err => {
  	if(err){
          console.log('删除失败')
      }else{
          console.log('删除成功')
      }
  })
  ```
  
- #### 操作目录：创建目录用`mkdir(path,可选参数,callback)`，查看目录用`readdir(path,可选参数,callback)`，它们都有对应的Sync版本。

  ```javascript
  //递归创建目录
  fs.mkdir('./a/b', {recursive: true}, err => {
  	if(err){
          console.log('创建失败')
      }else{
          console.log('创建成功')
      }
  })
  ```

  ```javascript
  //查看目录中的所有文件名
  fs.readdir('./a', (err,data) => {
  	if(err){
          console.log('查看失败')
      }else{
          //字符串数组
          console.log(data)
      }
  })
  ```


- #### 查看文件状态：用`stat(path,callback)`

  ```javascript
  fs.stat('./a.txt', (err,data) => {
  	if(err){
  		console.log('失败')
  	}else{
  		console.log(data)
  		console.log(data.isFile())
  		console.log(data.isDirectory())
  	}
  })
  ```

------

## path模块

> path模块是为了方便操作计算机的本地路径的，它常用的实例属性和方法：（同样使用前要先导入该模块）

- `resolve('dir','file')`：它可以根据参数来返回文件的绝对路径，第1个参数通常是绝对路径，第2个参数是相对路径。如：`path.resolve(__dirname, 'index.html')`，返回字符串：`c:\Users\22737\Desktop\test\index.html`，是一个符合当前操作系统规范的路径字符串

- `join('/user','zc','index.js')`：它会根据系统的路径分隔符，来将参数中的字符串进行拼接形成完整路径，其中字符串中的`/`斜杠或反斜杠`\`都以平台标准分隔符来替换掉，而且`..`和`.`这些都会正确被解析。

- `sep`：保存当前操作系统的路径分隔符。windows中是：`\`，linux中是：`/`

- `parse('path')`：解析本地路径的字符串，返回路径的信息对象。

  ```js
  {
    root: 'C:\\',
    dir: 'C:\\Users\\22737\\Desktop\\test',
    base: 'test.txt',
    ext: '.txt',
    name: 'test'
  }
  ```

- `basename('url')`：快速获取文件名

- `dirname('url')`：快速获取文件所在路径

- `extname('url')`：快速获取文件的扩展名

------

## http模块

> 我们可以通过http模块，来创建一个处理请求的WEB服务，用来对前端发过来的http的请求做出响应。如下：

```javascript
const http = require('http')
//	1、创建WEB服务对象。也可以将第2步的匿名函数直接传到createServer方法中
const server = http.createServer()
//	2、监听request事件，当请求过来就执行该函数
server.on('request', (req,resp)=>{
    //设置响应头
    resp.setHeader('Content-Type', 'text/html;charset=utf-8')
    //响应数据并结束本次请求
	resp.end('欢迎访问nodejs服务器')    
})
//	3、给WEB服务分配监听的端口号，并启动WEB服务，服务启动成功后执行该匿名函数（就是在cmd中启动了一个进程）
server.listen(9000, () => {
    console.log('服务器启动...')
})
//此时在浏览器中输入http://localhost:9000/就可以访问该WEB服务了
```

> 注意事项：
>
> 1. 命令行使用`Ctrl + c`来停止WEB服务。并且代码更新需要重启服务
> 2. 端口号范围是`[0-65535]`，其中`[0-1023]`被分配过了不能使用，如http服务默认访问80端口。
> 3. 如果端口号被占用，就换一个端口。或者打开windows的`资源监视器`，找到`侦听端口`，记下`PID`然后`任务管理器`关掉该程序即可。
> 4. 默认会接收所有对该端口的请求。如果某些请求没有用`resp.end()`，也就是没有完成响应的话，那么该请求建立的`http链接`就会一直占用，要很长时间才会释放掉。

- ##### HTTP请求对象的API：

  > - `method`：请求方式
  >
  > - `url`：请求url（不带协议ip和端口）
  >
  > - `httpVersion`：http协议的版本号，如1.1
  >
  > - `headers`：请求头信息对象。（含有特殊字符的请求头，在对象中会用''引号括起来）并且对象中的请求头属性全是小写，属性值都是字符串。
  >
  > - `on('data', function(chunk){})`：通过读取网络数据流，来获取请求体中的数据。
  >
  > - `on('end', function(){})`：请求体读取完后就会执行该函数。

- ##### HTTP响应对象的API：

  > - `statusCode`：设置响应状态码。（不设置默认是200）
  >
  > - `statusMessage`：设置响应状态描述信息。（一般不用，自动会跟状态码对应）
  >
  > - `setHeader('响应头','值')`：设置响应头。如果要设置多个同名的响应头，此时值是字符串数组。（响应头也可以自定义）
  >
  > - `write('hi')`：向响应体写数据，数据可以是串或Buffer对象，它可以调用多次。并且一般`write()`中响应了信息后，`end()`就不设置响应体了。
  >
  > - `end()`：结束响应。也可以向请求体中追加结束数据，数据可以是串或Buffer对象。所有请求处理最后都必须由该方法来断开http链接，且该方法只能出现1次。除了这种，还可以用`resp.destory()`方法立即强制断开连接
  
- ##### url模块：

  > 我们通过`req.url`获取请求路径字符串，手动拆分字符串获取请求路径和查询字符串很不方便。我们可以借助Node中提供的`url模块`，它可以对url网络路径进行操作：

  ```javascript
  const url = require('url')
  //url.parse()方法可以解析url字符串，返回关于该url信息的普通对象（没有构造器）
  let urls = url.parse(req.url)
  console.log(urls)
  //这就是?前面和端口号后面的路径
  console.log(urls.pathname)
  //这是查询字符串，即“?和后面的数据”
  console.log(urls.search)
  //不带?的字符串
  console.log(urls.query)
  ```

  > `query`是字符串，获取路径中的key和value还是不方便。我们可以给`parse`方法的第2个参数传`true`，这样query就是对象了。
  >
  > 此时查询字符串中的key是什么，那么该对象中的属性就有什么。如：`query.username`

- ##### （推荐）还可以用http模块中的URL对象来封装请求url：

  ```javascript
  //这个字符串必须是“带协议的完整的url”，也可以分开传，第1个参数是不带协议的url，第2个参数是协议ip和端口
  //let url = new URL('http://www.xxx.com/user?username=zs&age=12')
  let url = new URL('/user?username=zs&age=12','http://127.0.0.1:9000')
  console.log(url)
  console.log(url.pathname)
  console.log(url.searchParams.get('username'))//获取不到返回null值
  ```

- ##### 写一个小练习：

  ```javascript
  const http = require('http')
  const fs = require('fs')
  
  const server = http.createServer((req,resp)=>{
      let {pathname} = new URL(request.url, 'http://127.0.0.1:9000')
      if(pathname==='/'){
          let html = fs.readFileSync('./index.html')
          resp.end(html)
      }else if(pathname==='/index.css'){
          let css = fs.readFileSync('./index.css')
          resp.end(css)
      }else if(pathname==='/index.js'){
          let js = fs.readFileSync('./index.js')
          resp.end(js)
      }else{
          resp.statusCode = 404
          resp.end('<h1>404 NOT FOUND</h1>')
      }	
  })
  
  server.listen(9000, () => {
      console.log('服务器启动...')
  })
  ```

> 我们发现，每用到一个静态资源，就需要写响应对应静态资源对应的代码，很麻烦，能不能写一个类似于tomcat中的`DefaultServlet`，让它专门去处理静态资源的请求，当静态资源的请求过来时，自动去服务器对应的目录中找对应的资源然后响应。这个当然可以自己手写，但开发效率较低，所以后面我们用`express框架`做WEB开发，这些功能都可以直接用框架做到。

------
## Nodejs的模块化

> - 将一个复杂的项目，按照一定的规范拆分成多个文件，然后再将这些文件关联到一起，这个过程称之为**模块化**。其中拆分出来的每个文件都是一个独立的模块。模块/文件内部的数据都是私有的，可以通过暴露的方式将模块（文件）内部的数据供其他模块来使用。模块化好处：
>
>    1. 独立作用域，防止命名冲突（避免出现全局污染）。
>
>    2. 高复用性，按需加载。
>
>    3. 高维护性。
>
> - Node默认用的是Common.js的模块化规范（也支持其他模块规范如ES6模块化）。我们先来看下默认的Common.js模块化：
>

- #### 导出（`module.exports`）：

  > 如何将一个模块文件中的私有数据暴露出去、供其他文件去使用呢？通过`module.exports/exports/this`。在m1.js中：

  ```javascript
  function hello(){
      console.log('hello')
  }
  module.exports = hello
  ```

  > **注意：**
  >
  > - `module.exports`可以导出任何类型的数据，指向什么就导出什么。它默认指向一个空对象`{}`，所以若不导出任何东西默认导出一个空对象。
  > - `exports`也指向默认的空对象`{}`。它常用来给该对象中添加数据，如：`exports.hello = function(){}`。但最终导出的是以`module.exports`为准。

- #### 导入（`require()`）：

  > 此时在外部的index.js中，就可以通过`require()`（global中的全局函数）函数导入m1模块，返回值是导出的数据：（没导出任何数据默认是一个`{}`）

  ```js
  const fun = require('./m1.js') // 如果导入的是JS文件可省略.js
  fun()
  ```

- #### 注意事项：

  > - 导入某个JS文件时，会将该文件中的全部代码立即执行一遍。
  >- 对于自己创建的模块（JS文件），导入时路径要写相对路径。因为要和内置模块、第三方模块区分开，所以不能省略路径开头的`./`或`../`
  > - 如果导入的是Node中的内置模块，或Node项目的依赖包node_modules下的第三方模块，直接写模块名导入，如：`require('http')`
  >- 也可以导入其他类型的文件，默认也会当作JS文件去处理。此时如果不符合JS语法则报错，一般要配合打包工具来处理。
  > - 也可以**导入文件夹**：
  > 
  >   - 如果导入的是一个文件夹（将该目录当作Node项目目录），会去该文件夹中找`package.json`文件，导入其中的`main`属性对应的文件。（如果项目使用ES6模块化，那么是根据`"module"` 字段找对应的文件， `"main"` 字段是用于CommonJS模块化规范下的Node项目的入口文件）
  >
  >   - 如果main属性或`package.json`文件不存在，则会去目录中找`index.js`和`index.json`文件进行导入。
  >
  >   - 以上两种方式都没有，则导入报错。
  >
  > - 服务器端Node环境下也可以启用ES6的模块化规范，需要在Node项目根目录的`package.json`文件中设置：`{ "type": "module" }`，默认值是`commonjs`。（另一种简单的方式是，直接将JS的文件后缀改为`.mjs`/`.cjs`）
  >
  > - CommonJS模块化是要在服务器端运行的，浏览器端要使用的话，需要先经过打包工具将代码进行打包，将打包后输出的文件放在浏览器上执行。
  >
  
- #### 模块化的底层原理：

  1. 将相对路径转成绝对路径，定位文件
  2. 缓存检测。（看之前有没有导入过，避免重复导入）
  3. 读取目标文件代码，加载进内存
  4. 将导入的数据包裹为一个自动执行函数。（可以通过`arguments.callee.toString()`来查看该函数的代码）
  5. 缓存模块的值
  6. 返回`module.exports`的值

  > 以下是伪代码，方便我们理解：

  ```javascript
  //定义require全局函数
  function require(filename){
      // 1、将相对路径转成绝对路径
      let abspath = path.resolve(__dirname, filename)
      // 2、缓存检测
      if( cache[abspath] ){
          return cache[abspath]
      }
      // 3、读取目标文件代码，加载进内存
      let code = fs.readFileSync(abspath).toString()
      // 4、将导入的数据包裹为一个自动执行函数
      	//这里的module、exports、module.exports肯定提前声明好了
      	let exports = module = module.exports = {}
      (function(){
          //文件中的所有代码，会通过code变量拿到并放在这里
          //---模块内的代码
          function hello(){
              console.log('hello')
          }
          module.exports = hello
  		//---模块内的代码
      })()
      // 5、缓存“模块的值”（其实内部缓存的是Module对象，这里是为了方便理解）
      cache[abspath] = module.exports
      // 6、返回`module.exports`的值
      return module.exports
  }
  
  //导入模块m1
  const m1 = require('./m1')
  ```

------

###### 包管理器npm：node自带的是npm包管理器，关于npm请查看后面npm笔记。

------

## express框架

> express是一个基于nodejs平台的极简的、灵活的web应用开发框架，官网：https://expressjs.com.cn/
>
> 简单来说，它就是一个封装好的用于web开发的工具包，便于我们做WEB开发。

- #### 初体验：

  > 先用npm初始化一个包目录`npm init`，然后局部安装`express`框架`npm i express`，然后新建一个文件编写代码：
  >
  > ```js
  > const express = require('express')
  > // 创建web应用对象
  > const app = express();
  > app.get('/home', (req, resp) => {
  > 	resp.end('hello express!')
  >   })
  > //监听端口，启动服务
  > app.listen(8080, () => {
  > 	console.log('服务器启动成功')
  >   })
  > ```
  > 
  >然后运行程序启动服务器，浏览器输入：http://localhost:8080/home，成功访问！
  
- #### 关于第一个程序：

  > `app.get('/path', callback)`是express框架封装好的函数，如果是`GET`请求，路径为`/path`，此时请求过来就会执行该函数中的回调函数，对应的还有`app.post()`、`app.all()匹配请求方式`，其中路径有3个特殊的：`/`表示默认路径，匹配所有没有映射上的路径；`*`表示任意单层路径；`/*`表示任意多层路径，不包括`/`；

- #### express的API：

  > 我们除了可以用之前原生http模块的API之外，express框架还提供了一套API方便我们获取报文中的数据。

  - ###### 请求API：

    - 获取请求路径：`req.path`

    - 获取查询字符串对象：`req.query`，返回一个对象

    - 获取ip：`req.ip`

    - 获取请求头：`req.get('host')`

    - 路径占位符：`req.params.key`，方法中：`get('/xx/:key.html')`，其中`:key`是占位符。此时任何`/xx/sdfds.html`这种路径都可以匹配上，并且通过`params`可以获取到路由参数的值。

  - ###### 响应API：

    - 设置响应状态码：`resp.status(500)`

    - 设置响应头：`resp.set('host','127.0.0.1')`

    - 设置响应体：`resp.send('你好')`，调用完该方法后，它会自动在响应头中设置`Content-Type: text/html;charset=utf-8`

      （这些3个方法都是可以链式操作的）

    - 重定向：`resp.redirect('url')`

    - 下载响应：`resp.download('本地绝对路径')`，下载响应其实就是将文件内容放在了响应体中，当浏览器收到后看到特定的响应头就会将响应体中自动下载到本地。

    - JSON响应：`resp.json({name: 'zs'})`，自动将该对象变成JSON串响应给用户。

    - 响应文件：`resp.sendFile('文件的绝对路径')`
    
      （这几个响应的方法都会调用`end()`方法，断开http连接，本次请求处理完毕）

------

- #### 中间件：

  > 中间件（中间件函数）是express中的一个功能，它本质是一个回调函数。我们可以在该函数中，对请求和响应做一个处理。中间件的作用就是封装公共操作，简化代码。中间件分为2大类：
  >
  > - 全局中间件：它最好放在所有的路由函数`app.get()`之前，以保持代码逻辑清晰且符合预期的执行顺序。
  > - 路由中间件：它是在部分路由函数之前执行，在全局中间件之后执行（在路由函数参数上加）
  >
  
  > 中间件的使用：
  
  - 全局中间件：
  
    > 1. 定义一个中间件函数（就是普通函数）：
    >
    >    ```js
    >    function remeber(req, resp, next){
    >    	//所有请求来之前要做的事..
    >        next()//放行代码
    >    }
    >    ```
    >
    > 2. 在所有路由函数前，将该函数注册为全局中间件：`app.use(remeber)`。此时`next()`方法执行，请求才会继续走`app.use()`下方的路由函数或其他中间件。
  
  - 路由中间件：
  
    > 1. 定义一个中间件函数：（中间件函数都是一样的）
    >
    >    ```js
    >    function doSome(req, resp, next){
    >    	//所有请求来之前要做的事..
    >        next()//放行代码
    >    }
    >    ```
    >
    > 2. 哪个路由函数要使用该中间件，就将中间件函数传到第2个参数上：`app.get('/path', doSome, callback)`。此时`doSome`函数中的`next()`方法执行，请求才会执行到`app.get()`方法中

------

- #### 静态资源中间件：

  > 前面我们说过，可以用express框架来完成对项目根目录下的静态资源的处理。其实处理静态资源我们是通过静态资源中间件来完成的。`express.static('url')`方法可以返回一个静态资源中间件函数，参数表示静态资源去服务器上哪个位置找。如下：
  >
  > ```js
  > //静态资源中间件
  > app.use(express.static(__dirname + '/public'))
  > ```
  >
  > 该中间件通常放在所有路由函数前，作为全局中间件使用。
  >
  > 此时如果public下有a.html，那么请求路径为`http://localhost:9000/a.html`

  静态资源中间件的注意点：

  > - 如果请求路径为`/`，那么默认访问的是静态资源目录中的`index.html`。静态资源中间件看到路径是`/`会自动会将`index.html`返回。（用来配置欢迎页）
  > - 一般路由函数负责响应动态资源，而静态资源由静态资源中间件来处理。

------

- #### express获取请求体数据：

  > Express之前是不会自动解析HTTP请求体的，但它有一个官方支持的中间件包`body-parser`可以解析HTTP请求体。而从Express的4.16.0版本后，Express将`body-parser`集成了，可以直接使用该中间件函数，去解析JSON请求体和url编码的请求体了。

  - `body-parser`的使用：

    > 之前版本需要npm先安装该软件包，然后才能使用`body-parser`的这3个中间件，分别用于解析URL编码、JSON串、二进制数据这3种请求体（当不是这3种请求体时，则不做任何处理）。使用：
    >
    > ```js
    > const bodyParser = require('body-parser')
    > // extend：true当 extended 被设置为 true 时，express.urlencoded 中间件会使用一个完整的查询字符串解析库（通常是 qs 库）来解析请求体。这种模式支持更复杂的对象结构，允许在表单数据中使用嵌套的对象和数组。
    > const urlParser = bodyParser.urlencoded({ extended: false })
    > const jsonParser = bodyParser.json()
    > // const binParser = bodyParser.raw() 解析二进制数据，将其添加到req.body上
    > app.get('/abc', urlParser, (req, res) => {
    > // 将url编码格式串，解析为JS对象，将该对象添加到req.body属性上
    > 	res.end(req.body)
    > })
    > app.post('/def', jsonParser, (req, res) => {
    > // 将json串解析为JS对象，将该JS对象添加到req.body属性上
    > 	res.end(req.body)
    > })
    > ```
    >
    > 该中间件可以放在所有路由函数前作为全局中间件使用，也可以作为路由中间件使用
  
  - JSON解析中间件的使用：
  
    > 之后Express集成了`body-parser`，所以无需下载和导包，直接可以使用这3个中间件函数，用法：
    >
    > ```js
    > app.use(express.urlencoded({ extended: false }))
    > app.use(express.json())
    > app.use(express.raw())
    > app.post('/abc', (req, res) => {
    > 	console.log(req.body)
    > 	res.end();
    > })
    > ```

------

- #### 路由的模块化：

  > 我们之前的代码中，每个路由都对应一个路由函数，如果路由很多，那么要写很多路由函数，很复杂且不好维护。可以通过模块化来改进路由函数：
  >
  > 1. 新建一个js文件，导入express模块，通过：`express.Router()`来创建一个路由对象`router`，是小型的web应用对象(app)
  > 2. 然后将一个类型的路由都放在该文件中，`router.get('/user', callback)`，也可以使用中间件`router.use()`。最后暴露该路由对象：`modules.exports = router`
  > 3. 在主文件中导入该路由模块，`const userRouter = require('./userRouter.js')`，并将该路由对象当作中间件进行设置：`app.use(userRouter)`。也可以：`app.use('/admin', userRouter)`，表示该路由模块中的所有路径加一个`/admin`的公共前缀。
  >
  > 这样就完成了路由的模块化，主程序中的大量路由写到不同的js文件中，方便维护。

------

- #### 模板引擎ejs：

  > 模板引擎是分离视图和数据的一种技术，在很多语言中都有，如Java中的Thymeleaf模板引擎。Node中我们常用的模板引擎有ejs、pug、twing等。ejs是一个高效的JS的模板引擎，官网：https://ejs.co/，中文站：https://ejs.bootcss.com/，安装导入就可以用ejs了。使用：

  1. 新建一个`ejs.html`：

     ```html
     <!DOCTYPE html>
     <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>ejs的使用</title>
     </head>
     <body>
       <h1>hello <%= username %></h1>
     </body>
     </html>
     ```

  2. js代码：通过ejs对象的`render(str, {})`函数，将字符串str中的ejs语法替换为动态数据。ejs语法类似于JSP

     ```js
     app.get('/hello', (req, resp) => {
       let str = fs.readFileSync('./ejs.html').toString()
       let result = ejs.render(str, {username: '张三'})
       resp.send(result)
     })
     ```

  > 此时访问http://localhost:8080/hello就会显示hello 张三

  我们也可以在express中进行设置，这样使用模板引擎更方便，不用每次去读取文件了。设置：

  ```js
  //设置模板视图引擎。告诉express我们用的是哪个模板引擎
  app.set('view engine', 'ejs')
  //设置模板文件存放位置。此时需要项目下新建templates目录，在该目录中放.ejs结尾的文件
  app.set('views', './templates')
  //使用
  app.get('/hello', (req, resp) => {
      //这次是调用的响应对象的render方法，它会自动调用end方法完成响应
      resp.render('index', {username: '张三'})//会找index.ejs文件
  })
  ```

------

- #### 关于express-generator工具：（以后就用该工具来初始化WEB项目）

  - 它是expres官方给我们提供的一个工具，通过该工具可以快速创建一个WEB应用的骨架。
  - 使用前先进行全局安装：`npm i -g express-generator`，然后通过该工具的命令`express -h`查看是否安装成功。
  - 使用该工具生成express框架的WEB项目：`express 项目名`，此时会在运行该命令的当前目录中生成项目名对应的目录，里面包含了express的WEB项目对应的文件代码（如果要用到ejs，运行时还可以加`-e`参数，表示添加ejs模板引擎的支持）
  - 生成后，我们运行该项目之前，先安装对应的依赖：`npm i`，然后`npm start`运行项目，浏览器访问3000端口，访问成功。


------

- #### 文件上传：

  > 接收请求体中上传的文件，需要用到其他的软件包，常用的有4个：`express-fileupload、busboy、multer、formidable`，都是中间件。特点是：
  >
  > 1. `express-fileupload`简洁，功能够用。
  > 2. `busboy`一堆的on消息判断，个人觉得不如异步简洁。
  > 3. `multer`包小文件少，功能也很够用，是基于busboy开发。
  > 4. `formidable`主要用于表单处理，功能丰富扩展强。
  >
  > 这里我们用`formidable`处理文件上传：
  >
  > 前端：
  >
  > ```html
  > <!DOCTYPE html>
  > <html lang="en">
  > <head>
  >   <meta charset="UTF-8">
  >   <meta name="viewport" content="width=device-width, initial-scale=1.0">
  >   <title>上传图片</title>
  > </head>
  > <body>
  >   <form action="/upload" method="POST" enctype="multipart/form-data">
  >     <input type="hidden" value="1" name="id">
  >     头像：<input type="file" multiple="multiple" name="img">
  >     <button type="submit">提交</button>
  >   </form>
  > </body>
  > </html>
  > ```
  >
  > 后端：
  >
  > ```js
  > //导包
  > const formidable = require('formidable')
  > const express = require('express')
  > 
  > app.post('/upload', (req,resp) => {
  >   //1、创建form表单对象
  >   const form = new formidable.IncomingForm()
  >   //console.log(form)
  >   //2、文件保存设置。文件保存到该imgs目录中，保留原文件扩展名，支持一次上传多个文件，每个文件最大5MB，
  >   form.uploadDir = __dirname +'\\static\\imgs'
  >   form.options.keepExtensions = true
  >   form.options.multiples = true
  >   form.options.maxFileSize = 50 * 1024 * 1024
  >   // console.log(form)
  >   //3、解析请求报文req。成功后自动根据上面的设置保存文件，fields和files保存解析到的数据的信息
  >   form.parse(req, (err, fields, files) => {
  >     //如果解析错误
  >     if (err) {
  >         console.log("Error parsing the files")
  >         return resp.status(400).json({
  >         	status: "Fail",
  >         	message: "There was an error parsing the files",
  >         	error: err
  >         })
  >     }
  >     //解析成功，结果会放在fields和files中
  >     console.log(fields)//fields是一个对象，只存表单中一般的字段
  >     console.log(files)//files是上传的文件
  >     //返回文件名给用户
  >     resp.send(`<h1>/imgs/${files.img[0].newFilename}</h1>`)
  >   })
  > })
  > ```

------

- #### 会话控制：

  > web开发离不开Session会话机制，Node中也对会话机制做了实现，只需要调用一些API即可完成B/S端的会话。用法：
  
  > **关于Cookie**：
  >
  > - 给用户端响应`Cookie`：`resp.Cookie('name', '张三')`，类似与Java中的`resp.addCookie()`，响应的cookie有默认的存活时间和关联的路径。
  > - 设置`Cookie`：`resp.Cookie('name', '张三', { maxAge: 60*1000 })`，第3个参数用来设置cookie，表示存活时间为60s，默认是浏览器关闭才销毁。
  > - 删除`Cookie`：`resp.clearCookie('name')`，此时响应头会有一个`Set-Cookie: name=/; Path=/; Expires=..1970..`，此时浏览器收到该域名响应的cookie，会将同名的cookie覆盖，然后浏览器发现该cookie时间已经过期了，于是就会删除。这样就完成了删除浏览器端同名cookie的效果。
  > - 获取请求中的`Cookie`：可以用手动获取请求头的API，不过比较麻烦，我们一般会使用一个工具包`cookie-parser`，用法：
  >   1. 导包（先用`npm i cookie-parser`安装这个包）：`const cookieParser = require('cookie-parser')`
  >   2. 注册中间件：`app.use(cookieParser())`，`cookieParser()`函数的执行会返回一个中间件函数
  >   3. 获取cookie：`req.cookies`，此时请求对象req中会新增一个`cookies`属性，是一个对象，里面是携带的所有cookie
  
  > **关于Session**：原生的Node中并没有像Java那样的`HttpSession`对象，所以我们通常用`express`框架的一个包`express-session`，但是该包默认没有包含在express包中，所以我们需要单独安装。
  >
  > 1. 导包：`const session = require('express-session')`
  >
  > 2. 通过`session()`函数返回的中间件，来，如：（如果是全局中间件，那么每个请求过来都创建对应的session对象）
  >
  >    ```js
  >    app.use( session({
  >    	name: 'session-name',//这是服务器创建好session对象后，响应给浏览器的cookie的name，默认是connect.sid
  >        secret: 'itheima',//这是生成cookie的加密用的密钥（加盐），没这个密钥解不出来真正的cookie内容
  >        saveUninitialized: false,//只有在session对象发生了变化，硬盘中持久化的session数据才会变
  >        resave: true,//是否在每次请求时重新更新session的过期时间
  >        cookie: { maxAge: 300 * 1000, httpOnly: true }//设置响应的cookie的特性。maxAge是设置cookie的保存时间以及session的存活时间，5min不操作就退出登录；httpOnly会在Set-Cookie中，cookie内容的末尾加一个“;HttpOnly”，表示前端JS不能通过document.cookie来访问这个cookie，更安全，只能传输时使用。
  >    }) )
  >    ```
  >
  > 3. 请求过来，创建session对象并添加数据：
  >
  >    ```js
  >    app.post('/login', (req, reqp)=>{
  >    	//登陆成功，在本次请求的session对象中保存数据
  >    	req.session.user = {name:'张三', age:13}//类似Java中的req.getHttpSession()
  >        //销毁内存中的session对象：req.session.destroy()
  >    })
  >    ```
  
  > **关于token**：`token`是服务器端生成，并返回给客户端的一串加密的字符串。`token`中保存着用户信息，用于实现会话控制，可以识别用户的身份，主要用于移动端。不同的是，服务器是在响应体中给用户返回的`token`，用户访问时需要手动提交`token`给服务器，一般放在请求头中提交。
  >
  > `token`的工作流程：`token`是将原先用户存在服务器`session`中的信息，存到了本地浏览器中。使用`token`使得服务器压力更小、相对`cookie`更安全、且服务器之间的用户数据可以通过`token`共享。
  >
  > **关于JWT**：JWT（JSON Web Token）是目前最流行的跨域认证解决方案，用于`token`的身份认证，实现会话控制。JWT使`token`的生成和校验更加规范。Node中使用JWT：通过`jsonwebtoken`包来操作`token`，使用步骤：
  >
  > 1. 导入jwt包：`const jwt = require('jsonwebtoken')`
  >
  > 2. 生成token：当用户登陆成功，生成`token`加密后返回给客户端浏览器保存。`let token = jwt.sign(用户数据,加密字符串,配置对象)`，如：
  >
  >    ```js
  >    let token = jwt.sign({
  >        name: '张三'
  >    },'itheima',{
  >        expiresIn: 60//设置token的到期时间，单位s秒
  >    })
  >    console.log(token)//eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoi5byg5LiJIiwiaWF0IjoxNzE2MjA5OTUyLCJleHAiOjE3MTYyMTAwMTJ9.us7jt7AH8KDQcbSnbtxXp-PyOY3tskRbzChcMV4Vo0s
  >    ```
  >
  > 3. 校验token：
  >
  >    ```js
  >    //第1个参数是前端携带的token串，第2个参数是加密token的密钥
  >    jwt.verify(tokenStr, 'itheima', (err,data)=>{
  >    	if(err){
  >    		console.log(err)
  >    		return
  >    	}
  >    	console.log(data)//获得token中的数据{ name: '张三', iat: 1716210248, exp: 1716210308 }，iat是创建时间，exp是过期时间。一旦过期，此时就会走err
  >    })
  >    ```

------

- #### lowdb的使用：（用的不多，了解即可）

  > 它是一个软件包，可以将数据以JSON文件的形式持久化保存在本地服务器上。使用前先安装，因为高版本用的是ES6的模块化，所以我们这里安装的低版本：`npm i lowdb@1.0.0`，使用：

  ```js
  //导入lowdb的2个包
  const low = require('lowdb')
  const FileSync = require('lowdb/adapters/FileSync')
  
  //将数据保存到该文件中
  const adapter = new FileSync('db.json')
  //获取db对象，也就是数据库对象（db.json文件对象）
  const db = low(adapter)
  
  // 初始化数据
  db.defaults({ posts: [], user: {} }).write()
  
  //获取posts，获取值最后要调用value()方法，更新数据最后要调用write()方法
  console.log( db.get('posts').value() )
  
  // 往posts数组中压入2条数据
  db.get('posts')
    .push({ id: 1, title: '我是数据1' })
    .write()
  
  db.get('posts')
    .push({ id: 2, title: '我是数据2' })
    .write()
  
  //删除id为3的数据，返回值就是删除掉的数据
  console.log( db.get('posts').remove({id:3}).write() )
  
  //获取单条数据
  console.log( db.get('posts').find({id:1}).value() )
  
  //更新数据
  db.get('posts').find({id:1}).assign({title:'今天下雨了'}).write()
  
  // 给user对象设置name属性为张三
  db.set('user.name', '张三').write()
  ```

  > 此时同目录下会出现db.json文件：

  ```json
  {
    "posts": [
      {
        "id": 1,
        "title": "今天下雨了"
      },
      {
        "id": 2,
        "title": "我是数据2"
      }
    ],
    "user": {
      "name": "张三"
    }
  }
  ```

  > 如果想给数据中添加唯一标识，可以用他推荐的`shortid`包，导入后返回`shortid`对象，调用对象的`generate()`方法返回id值

------

#### moment包的使用：（简单日期的格式化用Date对象的函数就可以完成）

> `npm i moment`安装包导入后，通过`moment('')`函数传进去一个字符串，返回`Moment`对象，调用该对象的`toDate()`方法，可以得到一个`Date`类型对象。也可以传进去一个`Date`对象，返回的同样是`Monment`对象，调用它的`format('YYYY-MM-DD')`方法即可将日期格式化为字符串。

> moment功能很强大，所以体积也大，如果只想用最简单的日期解析的功能，可以用`dayjs`。它是一个轻量级的处理时间和日期的JS库，和moment的用法完全一致。

------

#### json-server包的使用：

> `json-server`是一个js的全局工具包，可以在后端接口文档没有写好的情况下，先模拟一个`RESTful`风格的接口服务，方便前端写代码。使用：
>
> 1. 全局安装：`npm i -g json-server`
>
> 2. 创建数据库文件（db.json）：该文件更新后，服务器会自动重启
>
>    ```json
>    {
>    	"students": [
>    		{"name": "张三", "age": 81},
>    		{"name": "李四", "age": 11},
>    		{"name": "王五", "age": 41}
>    	]
>    }
>    ```
>
> 3. 在JSON文件所在的目录下执行命令：`json-server --watch db.json`，启动服务器，默认监听3000端口。浏览器地址栏输入http://localhost:3000/students，此时会返回students对应的数组数据（JSON串）

------

### md5加密：

> md5是一个单向加密算法，可以将密码字符串进行加密，不可逆，相同字符串经过加密后的内容永远是一样的。使用：
>
> 1. 安装md5包并导入：`const md5 = require('md5')`
> 2. 对密码加密：`console.log( md5('123456') )`

------

### 配置HTTPS证书：

> https是http + SSL（Secure Sockets Layer 安全套接层），https可以加密http报文，所以它可以理解为安全的http协议。
>
> 配置HTTPS证书，首先得获取证书，在服务器上安装该工具，官网：https://certbot.eff.org/
>
> 操作流程：（必须在服务器端）
>
> 1. 官网中下载certbot工具：https://dl.eff.org/certbot-beta-installer-win_amd64.exe
>
> 2. 安装该工具，全部下一步
>
> 3. 管理员身份执行命令：`certbot certonly --standalone`，然后输入邮箱，两次yes，然后输入该服务器的域名，回车等待生成的证书文件。
>
> 4. 然后改主程序代码：
>
>    ```js
>    //1、导入https模块
>    const https = require('https')
>    //2、创建服务对象的代码用https.createServer()去创建，第1个参数传配置对象
>    let server = https.createServer({
>        key: fs.readFileSync('证书文件privkey.pem的绝对路径'),
>        cert: fs.readFileSync('证书文件cert.pem的绝对路径'),
>        ca: fs.readFileSync('证书文件chain.pem的绝对路径')
>    }, app)//app是express的web应用对象
>    ```
>
> 5. 最后将代码中的服务端口改为https的默认端口443
>
> 此时80端口的http服务就不能访问了，如果希望都能访问，那么复制代码，创建两个服务对象即可。
>
> （证书的有效期是3个月，可以通过以下命令更新证书）
>
> ```shell
> ## 一般更新，剩余有效期小于1个月使用
> certbot renew
> ## 强制更新，剩余有效期大于1个月使用
> certbot --force-renewal
> ```

------

### NodeJS调试：

> 运行指令：`node --inspect-brk xxx.js`，即可开启NodeJS的调试功能。此时打开任意一个浏览器窗口，F12进入开发者工具，刷新页面或等一会，左上角会出现Node.js按钮，点击后即可进入Chrome提供的调试面板。
>
> ![debug](./assets/debug.png)

------

### Fetch API：

> Node18 版本后新增 `fetch()` 函数，用法和浏览器端基本一致。它是基于 Node.js 的**`undici`**（高性能 HTTP 客户端）实现的。

