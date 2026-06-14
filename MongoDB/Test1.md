# MongoDB

------

### 简介：

- Mongodb是一个基于分布式文件存储的数据库，官网：https://www.mongodb.com/，由于其语法与JS类似，容易上手，学习成本低，所以在前端被大量使用。是由C++编写的。
- Mongodb是一种文档型的数据库，一个数据库就相当于一个JSON文件。数据库里的数据以集合和文档形式存储，集合就是JSON文件中的一个JS数组（mysql的表），文档就是JS数组中的一个对象（mysql的一条记录）。有时也称对象中的属性为文档的字段，同一个集合中的文档的字段可以不同。（一般情况下一个项目就用一个数据库，数据库中每个集合中存储同一种类型的数据）
- Mongodb的存储结构是BSON，是一种类似于JSON的格式，是二进制的JSON，可以更加高效的存储和传输数据。
- Mongodb并不需要事先创建好数据库和集合（表）再插入数据，也不需要预先定义好集合中字段的类型，同一个集合中的数据也不需要有相同的结构，它更加灵活简单。

### Mongodb的下载与安装：

> zip方式安装稍微繁琐，具体百度即可。这里直接通过windows的msi方式安装(7.0.9)，将MongoDB安装为windows服务。由于mongodb5之后的版本没有自带数据库客户端的shell程序，所以安装时勾选mongodb的compass，是一个好用的数据库客户端程序，方便我们操作数据库。（也可以用navicat）

------

### MongoDB的命令：（所有会修改数据的操作都是以方法的形式带()括号，不修改数据的都是以属性的方式）

1. 数据库命令：
   - `show dbs`：显示所有数据库
   - `db`：显示当前所在的数据库
   - `use 数据库名`：切换到指定的数据库。（数据库名不存在会自动创建该数据库，所以不用提前创建数据库，直接use用即可）
   - `db.dropDatabase()`：删除当前的数据库。（必须先切换到当前数据库下）
2. 集合命令：
   - `show collections`：查看当前数据库下的所有集合
   - `db.createCollection('集合名')`：创建集合。（往文档中写数据也不用提前创建集合，文档有了数据库和集合都有了）
   - `db.集合名.drop()`：删除集合
   - `db.集合名.renameCollection('新名')`：重命名集合
3. 文档命令：（重要）
   - `db.集合名.insert({name:'zc'})`：新增文档（就是在数组/集合中新增一个对象）。它会自动生成一个主键属性`_id`，类型是`ObjectId`类型，不是普通的数字。
   - `db.集合名.find({name:'zc'})`：查询文档。不给条件或给一个空对象`{}`就是查全部。
   - `db.集合名.update({name:'zc'}, {name:'ls'})`：更新文档。第1个参数是条件，第2个参数是改成的内容（全部改）。如果不想将对象全改，第2个参数这样写：`{$set: {name:'ls'}}`，这样就只改对象的name属性。
   - `db.集合名.remove({name:'zc'})`：删除文档。

------

### MongoDB的条件查询：

> MongoDB的条件查询需要用以下条件运算符来做，我们来看下怎么用：
>
> - **关系运算符**：MongoDB中，不能用`> < = !=`等关系运算符，需要用替代符号
>
>   - `>`用`$gt`
>   - `<`用`$lt`
>   - `>=`用`$gte`
>   - `<=`用`$lte`
>   - `!=`用`$ne`
>
>   ```js
>   //例如：找价格price小于20的
>   db.books.find({ price: {$lt:20} })
>   ```
>
> - **逻辑运算符**：`$or`和`$and`，不能用`&&`和`||`
>
>   ```js
>   //例如：找曹雪芹或施耐庵的书
>   db.books.find({ $or: [{author:'曹雪芹'},{author:'施耐庵'}] })
>   ```
>
> - **正则匹配**：`$regex`，条件中可以用JS的正则语法，通过正则可以实现模糊查询，如：
>
>   ```js
>   //例如：找名字中带`雪`的
>   db.books.find({ name: {$regex:'雪'} })
>   //正则也可以这样
>   db.books.find({ name: /雪/ })
>   //还可以
>   db.books.find({ name: new RegExp('雪') })
>   ```

------

### Mongoose：

> mongoose是一个文档对象模型库，官网：http://www.mongoosejs.net/。简单来说，就是一个node连接mongodb数据库的工具包，可以让我们不再局限于原始的连接方式。

- ##### 原始方式连接数据库：

  > 1. 首先安装`mongodb`的驱动包：`npm i mongodb`
  > 2. 创建Mongo客户端对象`MongoClient`，连接mongo数据库并创建集合：
  >
  > ```js
  > let MongoClient = require('mongodb').MongoClient
  > let url = "mongodb://localhost:27017/test"
  > //与数据库程序建立连接。如果连接的数据库不存在，会自动创建
  > MongoClient.connect(url, function(err, db) {
  > 	//和MySQL不同的是，数据库和集合不需要提前定义
  > 	if (err) throw err
  > 	console.log("数据库已创建!")
  >     //获取数据库对象
  >     let dbtest = db.db("test")
  >     //通过数据库对象来创建集合students
  >     dbtest.createCollection('students', function (err, result) {
  >         if (err) throw err
  >         console.log("student集合已创建!")
  >         //断开连接
  >         db.close()
  >     })
  > })
  > ```
  >
  > 其实MongoDB会自动创建数据库和集合，所以上面的代码不需要写

  新增文档：

  ```js
  let MongoClient = require('mongodb').MongoClient
  let url = "mongodb://localhost:27017/"
  MongoClient.connect(url, function(err, db) {
      if (err) throw err
      //获取test数据库对象
      let dbtest = db.db("test")
      let data = { name: "菜鸟教程", url: "www.runoob" }
      dbtest.collection("students").insertOne(data, function(err, result) {
          if (err) throw err
          console.log("文档插入成功")
          //断开数据库连接
          db.close()
      })
  })
  //新增多条用insertMany
  dbtest.collection("students").insertMany([数组数据], function(err, result) {
      if (err) throw err
  	console.log("插入的文档数量为: " + result.insertedCount)
      db.close()
  })
  ```

  查文档：

  ```js
  dbtest.collection("students").find({}).toArray(function(err, result) {//返回集合中所有数据
  	if (err) throw err
  	console.log(result)
  	db.close()
  })
  ```

  更新文档：

  ```js
  //更新一条
  dbtest.collection("students").updateOne({条件对象}, {更新的对象}, function(err, result) {
  	if (err) throw err
  	console.log("文档更新成功")
  	db.close()
  })
  //更新多条用updateMany
  dbtest.collection("students").updateMany({条件对象}, {更新的对象}, function(err, result) {
      if (err) throw err
      console.log(result.result.nModified + " 条文档被更新")
      db.close()
  })
  ```

  删除：

  ```js
  //删除一条
  dbtest.collection("students").deleteOne({条件对象}, function(err, result) {
      if (err) throw err
      console.log("文档删除成功")
      db.close()
  })
  //删除多条用deleteMany
  dbtest.collection("students").deleteMany({条件对象}, function(err, result) {
      if (err) throw err
      console.log(result.result.n + " 条文档被删除")
      db.close()
  })
  ```

- ##### mongoose连接数据库：

  ```js
  const mongoose = require('mongoose')
  
  //1、连接mongodb服务
  mongoose.connect('mongodb://localhost:27017/test')
  //2、设置回调
      //连接失败时
  mongoose.connection.on('error', ()=>{console.log('连接失败')})
      //关闭数据库连接时
  mongoose.connection.on('close', ()=>{console.log('连接关闭')})
      //当连接成功时。数据库连接开启官方建议用once来绑定，因为如果数据库服务如果重启了，那么你的回调函数会执行2次
  mongoose.connection.once('open', ()=>{
      console.log('连接成功')
  
  //3、操作数据库
  	//3.1、设置文档模式。（文档模式就是，集合中文档对象各属性的类型约束）
      let BookSchema = new mongoose.Schema({
          name: String,
          author: String,
          price: Number
      })
      //3.2、获取集合（books）的模型对象，用来操作集合中的文档。将文档模式传进去
      let BookModel = mongoose.model('books', BookSchema)
  
      //增。mongoose会在每个文档对象中，自动帮我们添加属性`__v`，它是该对象的序列化版本号
      BookModel.create({name: '西游记',price: 19.3,author: '吴承恩'}).then((data)=>{
          console.log(data)
          //关闭连接。实际项目中这行代码不会加，一只连着数据库，方便随时操作数据
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      BookModel.insertMany([//批量插入用数组参数
          {name: '西游记', price: 19.3, author: '吴承恩'},
          {name: '西游记', price: 19.3, author: '吴承恩'},
          {name: '西游记', price: 19.3, author: '吴承恩'},
          {name: '西游记', price: 19.3, author: '吴承恩'}
      ]).then((data)=>{
          console.log(data)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      //删
      BookModel.deleteOne({//删一条
          _id: '66486325fadc0967e4dbdcc6'
      }).then((data)=>{
          console.log('影响记录:'+ data.deletedCount)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      BookModel.deleteMany({//删多条
          name: '西游记',
          author: '吴承恩'
      }).then((data)=>{
          console.log('影响记录:'+ data.deletedCount)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      //改
      BookModel.updateOne({ name: '西游记' },{ price: 199 })//更新一条，只更新同名属性，不需要加{$set:{}}
      .then((data)=>{
          console.log('影响记录:'+ data.modifiedCount)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      BookModel.updateMany({ price: 19.3 },{ price: 199 })//更新多条，只更新同名属性
      .then((data)=>{
          console.log('影响记录:'+ data.modifiedCount)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      //查
      BookModel.find({ price: 19.3 })//根据条件查多条，没条件就查全部
      .then((data)=>{
          console.log(data)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      BookModel.findOne({ price: 199 })//只查一条
      .then((data)=>{
          console.log(data)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
      BookModel.findById('6648942b45bf3c0c97c87119')//根据id只查一条
      .then((data)=>{
          console.log(data)
          mongoose.disconnect()
      }).catch(err => {
          console.log(err)
          throw err
      })
  })
  ```
  

------

### mongoose中，文档模式所支持的数据类型：

> String：字符串
>
> Number：数字型
>
> Boolean：布尔值
>
> Array：数组
>
> Date：Date日期对象
>
> Buffer：存二进制数据
>
> Mixed：类型任意。需要使用`mongoose.Schema.Types.Mixed`指定
>
> ObjectId：该属性值为主键id类型，值必须是文档id形式（123这种不行），需要使用`mongoose.Schema.Types.ObjectId`指定
>
> Decimal128：高精度数字类型。需要使用`mongoose.Schema.Types.Decimal128`指定。
>
> （如果新增文档的类型匹配不上，`create()`方法会抛异常；如果新增文档的属性没有在模式中定义，那么该属性会被忽略）

### mongoose的字段验证：（我们也可以自己做验证，不用mongoose提供的）

> 对文档属性值进行校验，校验通过再插入数据，否则就报错。mongoose中内置了一些**验证器**，可以完成对字段的验证。
>
> 1. 必填项：`required: true`
> 2. 默认值：`default: 默认值`
> 3. 枚举值：`enum: [枚举值]`
> 4. 唯一值：`unique: true`，（注意：如果集合已经存在，那么该设置无效，必须在集合新建时才能设置唯一性）
>
> 用法：将数据类型指定为一个对象，对象的type属性指定文档的属性类型，然后里面再加以上属性即可。
>
> ```js
> let BookSchema = new mongoose.Schema({
> 	name: {
>         type: String,
>         required: '无姓名'
>     },
> 	author: {
>         type: String,
>         default: '无作者'
>     },
> 	price: Number
> })
> ```

### mongoose的个性化读取：

> - 字段筛选：通过`select()`函数，可以只输出文档对象中的部分属性，而不是输出整个文档对象。
>
>   ```js
>   //只查name和author、主键_id这3个属性，如果_id不想看到，就：_id:0
>   BookModel.find().select({name:1,author:1})
>   .then((data)=>{
>       console.log(data)
>       mongoose.disconnect()
>   }).catch(err => {
>       console.log(err)
>       throw err
>   })
>   ```
>
> - 数据排序：通过`sort()`函数，将结果集（无序的对象）进行排序。
>
>   ```js
>   //结果集根据升序排，如果是-1就是降序排
>   BookModel.find().select({name:1,author:1}).sort({price:1})
>   .then((data)=>{
>       console.log(data)
>       mongoose.disconnect()
>   }).catch(err => {
>       console.log(err)
>       throw err
>   })
>   ```
>
> - 数据截取：通过`skip()`和`limit()`函数，可以只获取部分结果集。
>
>   ```js
>   //结果集从第4条开始，取2条输出
>   BookModel.find().select({name:1,author:1}).sort({price:1}).skip(3).limit(2)
>   .then((data)=>{
>       console.log(data)
>       mongoose.disconnect()
>   }).catch(err => {
>       console.log(err)
>       throw err
>   })
>   ```

------

### Mongoose代码模块化：

> 我们接下来将mongoose连接数据库的代码，封装成一个函数，该函数单独作为`db.js`工具。这样我们需要操作数据库时，只需要导入该文件，调用该函数传进去操作数据库的代码即可。如下：

1. db.js中：

   ```js
   module.exports = function(success, err){//success是连接成功的回调函数，err是连接失败的回调函数
       //如果将来不给第二个参数，默认发生错误时输出连接失败
       if(typeof err != 'function'){
           err = ()=>{
               console.log('连接失败')
           }
       }
       //导入mongoose包
       const mongoose = require('mongoose')
   
       //1、连接mongodb服务
       mongoose.connect('mongodb://localhost:27017/test')
       //2、设置回调
       mongoose.connection.on('error', ()=>{ err() })
       mongoose.connection.on('close', ()=>{console.log('连接关闭')})
       mongoose.connection.once('open', ()=>{
           console.log('连接成功')
           success()//通常success()函数中会启动http服务，因为连接上数据库才会启动服务器，没连上启动服务器也没用
       //3、操作数据库
           //要先设置集合模式，再获取集合的模型对象，通过模型对象来操作数据库，所以这部分公共代码我们也封装一下
       })
   }
   ```

2. BookModel.js中：（将来要操作其他集合，就加一个该文件即可）

   ```js
   const mongoose = require('mongoose')
   let BookSchema = new mongoose.Schema({
       name: String,
       price: Number
   })
   let BookModel = mongoose.model('books', BookSchema)
   //将BookModel导出，因为我们要用到这个对象来操作数据库
   module.exports = BookModel
   ```

3. 要连接数据库的主文件index.js中：

   ```js
   const mongoose = require('mongoose')
   //导入连接数据库的文件
   const db = require('./db/db.js')
   //导入操作数据库的文件
   const BookModel = require('./models/BookModel.js')
   
   //插入一条数据
   db(()=>{
   	//增
       BookModel.create({ name: '汤姆索亚历险记', price: 19.3 })
   	.then((data)=>{
           console.log(data)
       }).catch(err => {
           console.log(err)
           throw err
       })
   },(err)=>{//第2个参数也可以不传
       console.log(err)
   	throw err
   })
   ```

   > 将来连接数据库的端口、ip、数据库名可能会变化，所以我们再优化下，将这些信息写到配置文件`./config/config.js`中：

   ```js
   module.exports = {
       DBHOST: '127.0.0.1',
       DBPORT: '27017',
       DBNAME: 'test'
   }
   ```

   > 然后`db.js`中导入该配置文件：`const {DBHOST, DBPORT, DBNAME} = require('./config/config.js')`
   >
   > url地址变为：`mongodb://${DBHOST}:${DBPORT}/${DBNAME}`

------

