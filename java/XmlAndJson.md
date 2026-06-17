# XML

- ### xml简介

  > XML是可扩展标记语言（eXtensible Markup Language）的缩写，它是一种数据表示格式，可以描述非常复杂的数据结构，常用于传输和存储数据。
  >
  > 例如，一个描述书籍的XML文档可能如下：
  >
  > ```xml
  > <?xml version="1.0" encoding="UTF-8" ?>
  > <!DOCTYPE note SYSTEM "book.dtd">
  > <book id="1">
  >     <name>Java核心技术</name>
  >     <author>Cay S. Horstmann</author>
  >     <isbn lang="CN">1234567</isbn>
  >     <tags>
  >         <tag>Java</tag>
  >         <tag>Network</tag>
  >     </tags>
  >     <pubDate/>
  > </book>
  > ```
  >
  > 
  >
  > XML有几个特点：一是纯文本，默认使用UTF-8编码，二是可嵌套，适合表示结构化数据。如果把XML内容存为文件，那么它就是一个XML文件，例如`book.xml`。此外，XML内容经常通过网络作为消息传输。
  >
  > ### XML的结构
  >
  > XML有固定的结构，首行必定是`<?xml version="1.0"?>`，可以加上可选的编码。紧接着，如果以类似`<!DOCTYPE note SYSTEM "book.dtd">`声明的是文档定义类型（DTD：Document Type Definition），DTD是可选的。接下来是XML的文档内容，一个XML文档有且仅有一个根元素，根元素可以包含任意个子元素，元素可以包含属性，例如，`<isbn lang="CN">1234567</isbn>`包含一个属性`lang="CN"`，且元素必须正确嵌套。如果是空元素，可以用`<tag/>`表示。
  >
  > 由于使用了`<`、`>`以及引号等标识符，如果内容出现了特殊符号，需要使用`&???;`表示转义。例如，`Java<tm>`必须写成：
  >
  > ```xml
  > <name>Java&lt;tm&gt;</name>
  > ```
  >
  > 
  >
  > 常见的特殊字符如下：
  >
  > | 字符 | 表示   |
  > | ---- | ------ |
  > | <    | &lt;   |
  > | >    | &gt;   |
  > | &    | &amp;  |
  > | "    | &quot; |
  > | '    | &apos; |
  >
  > 格式正确的XML（Well Formed）是指XML的格式是正确的，可以被解析器正常读取。而合法的XML是指，不但XML格式正确，而且它的数据结构可以被DTD或者XSD验证。
  >
  > DTD文档可以指定一系列规则，例如：
  >
  > - 根元素必须是`book`
  > - `book`元素必须包含`name`，`author`等指定元素
  > - `isbn`元素必须包含属性`lang`
  > - ...
  >
  > 如何验证XML文件的正确性呢？最简单的方式是通过浏览器验证。可以直接把XML文件拖拽到浏览器窗口，如果格式错误，浏览器会报错。
  >
  > 和结构类似的HTML不同，浏览器对HTML有一定的“容错性”，缺少关闭标签也可以被解析，但XML要求严格的格式，任何没有正确嵌套的标签都会导致错误。
  >
  > XML是一个技术体系，除了我们经常用到的XML文档本身外，XML还支持：
  >
  > - DTD和XSD：验证XML结构和数据是否有效；
  > - Namespace：XML节点和属性的名字空间；
  > - XSLT：把XML转化为另一种文本；
  > - XPath：一种XML节点查询语言；
  > - ...
  >
  > 实际上，XML的这些相关技术实现起来非常复杂，在实际应用中很少用到，通常了解一下就可以了。
  >
  > ### 小结
  >
  > XML使用嵌套结构的数据表示方式，支持格式验证；
  >
  > XML常用于配置文件、网络消息传输等。

- ### 使用DOM

  > 因为XML是一种树形结构的文档，它有两种标准的解析API：
  >
  > - DOM：一次性读取XML，并在内存中表示为树形结构；
  > - SAX：以流的形式读取XML，使用事件回调。
  >
  > 我们先来看如何使用DOM来读取XML。
  >
  > DOM是Document Object Model的缩写，DOM模型就是把XML结构作为一个树形结构处理，从根节点开始，每个节点都可以包含任意个子节点。
  >
  > 我们以下面的XML为例：
  >
  > ```xml
  > <?xml version="1.0" encoding="UTF-8" ?>
  > <book id="1">
  >     <name>Java核心技术</name>
  >     <author>Cay S. Horstmann</author>
  >     <isbn lang="CN">1234567</isbn>
  >     <tags>
  >         <tag>Java</tag>
  >         <tag>Network</tag>
  >     </tags>
  >     <pubDate/>
  > </book>
  > ```
  >
  > 
  >
  > 如果解析为DOM结构，它大概长这样：
  >
  > ```
  >                       ┌─────────┐
  >                       │document │
  >                       └─────────┘
  >                            │
  >                            ▼
  >                       ┌─────────┐
  >                       │  book   │
  >                       └─────────┘
  >                            │
  >      ┌──────────┬──────────┼──────────┬──────────┐
  >      ▼          ▼          ▼          ▼          ▼
  > ┌─────────┐┌─────────┐┌─────────┐┌─────────┐┌─────────┐
  > │  name   ││ author  ││  isbn   ││  tags   ││ pubDate │
  > └─────────┘└─────────┘└─────────┘└─────────┘└─────────┘
  >                                       │
  >                                  ┌────┴────┐
  >                                  ▼         ▼
  >                              ┌───────┐ ┌───────┐
  >                              │  tag  │ │  tag  │
  >                              └───────┘ └───────┘
  > ```
  >
  > 注意到最顶层的document代表XML文档，它是真正的“根”，而`<book>`虽然是根元素，但它是`document`的一个子节点。
  >
  > Java提供了DOM API来解析XML，它使用下面的对象来表示XML的内容：
  >
  > - Document：代表整个XML文档；
  > - Element：代表一个XML元素；
  > - Attribute：代表一个元素的某个属性。
  >
  > 使用DOM API解析一个XML文档的代码如下：
  >
  > ```java
  > InputStream input = Main.class.getResourceAsStream("/book.xml");
  > DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
  > DocumentBuilder db = dbf.newDocumentBuilder();
  > Document doc = db.parse(input);
  > ```
  >
  > 
  >
  > `DocumentBuilder.parse()`用于解析一个XML，它可以接收InputStream，File或者URL，如果解析无误，我们将获得一个Document对象，这个对象代表了整个XML文档的树形结构，需要遍历以便读取指定元素的值：
  >
  > ```java
  > void printNode(Node n, int indent) {
  >     for (int i = 0; i < indent; i++) {
  >         System.out.print(' ');
  >     }
  >     switch (n.getNodeType()) {
  >     case Node.DOCUMENT_NODE: // Document节点
  >         System.out.println("Document: " + n.getNodeName());
  >         break;
  >     case Node.ELEMENT_NODE: // 元素节点
  >         System.out.println("Element: " + n.getNodeName());
  >         break;
  >     case Node.TEXT_NODE: // 文本
  >         System.out.println("Text: " + n.getNodeName() + " = " + n.getNodeValue());
  >         break;
  >     case Node.ATTRIBUTE_NODE: // 属性
  >         System.out.println("Attr: " + n.getNodeName() + " = " + n.getNodeValue());
  >         break;
  >     default: // 其他
  >         System.out.println("NodeType: " + n.getNodeType() + ", NodeName: " + n.getNodeName());
  >     }
  >     for (Node child = n.getFirstChild(); child != null; child = child.getNextSibling()) {
  >         printNode(child, indent + 1);
  >     }
  > }
  > ```
  >
  > 
  >
  > 解析结构如下：
  >
  > ```plain
  > Document: #document
  >  Element: book
  >   Text: #text = 
  >     
  >   Element: name
  >    Text: #text = Java核心技术
  >   Text: #text = 
  >     
  >   Element: author
  >    Text: #text = Cay S. Horstmann
  >   Text: #text = 
  >   ...
  > ```
  >
  > 
  >
  > 对于DOM API解析出来的结构，我们从根节点Document出发，可以遍历所有子节点，获取所有元素、属性、文本数据，还可以包括注释，这些节点被统称为Node，每个Node都有自己的Type，根据Type来区分一个Node到底是元素，还是属性，还是文本，等等。
  >
  > 使用DOM API时，如果要读取某个元素的文本，需要访问它的Text类型的子节点，所以使用起来还是比较繁琐的。
  >
  > ### 练习
  >
  > 使用DOM解析XML。
  >
  > [下载练习](https://liaoxuefeng.com/books/java/xml-json/dom/xml-dom.zip)
  >
  > ### 小结
  >
  > Java提供的DOM API可以将XML解析为DOM结构，以Document对象表示；
  >
  > DOM可在内存中完整表示XML数据结构；
  >
  > DOM解析速度慢，内存占用大。

- ### 使用SAX

  > 使用DOM解析XML的优点是用起来省事，但它的主要缺点是内存占用太大。
  >
  > 另一种解析XML的方式是SAX。SAX是Simple API for XML的缩写，它是一种基于流的解析方式，边读取XML边解析，并以事件回调的方式让调用者获取数据。因为是一边读一边解析，所以无论XML有多大，占用的内存都很小。
  >
  > SAX解析会触发一系列事件：
  >
  > - startDocument：开始读取XML文档；
  > - startElement：读取到了一个元素，例如`<book>`；
  > - characters：读取到了字符；
  > - endElement：读取到了一个结束的元素，例如`</book>`；
  > - endDocument：读取XML文档结束。
  >
  > 如果我们用SAX API解析XML，Java代码如下：
  >
  > ```java
  > InputStream input = Main.class.getResourceAsStream("/book.xml");
  > SAXParserFactory spf = SAXParserFactory.newInstance();
  > SAXParser saxParser = spf.newSAXParser();
  > saxParser.parse(input, new MyHandler());
  > ```
  >
  > 
  >
  > 关键代码`SAXParser.parse()`除了需要传入一个`InputStream`外，还需要传入一个回调对象，这个对象要继承自`DefaultHandler`：
  >
  > ```java
  > class MyHandler extends DefaultHandler {
  >     public void startDocument() throws SAXException {
  >         print("start document");
  >     }
  > 
  >     public void endDocument() throws SAXException {
  >         print("end document");
  >     }
  > 
  >     public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
  >         print("start element:", localName, qName);
  >     }
  > 
  >     public void endElement(String uri, String localName, String qName) throws SAXException {
  >         print("end element:", localName, qName);
  >     }
  > 
  >     public void characters(char[] ch, int start, int length) throws SAXException {
  >         print("characters:", new String(ch, start, length));
  >     }
  > 
  >     public void error(SAXParseException e) throws SAXException {
  >         print("error:", e);
  >     }
  > 
  >     void print(Object... objs) {
  >         for (Object obj : objs) {
  >             System.out.print(obj);
  >             System.out.print(" ");
  >         }
  >         System.out.println();
  >     }
  > }
  > ```
  >
  > 
  >
  > 运行SAX解析代码，可以打印出下面的结果：
  >
  > ```plain
  > start document
  > start element:  book
  > characters:
  >      
  > start element:  name
  > characters: Java核心技术
  > end element:  name
  > characters:
  >      
  > start element:  author
  > ...
  > ```
  >
  > 
  >
  > 如果要读取`<name>`节点的文本，我们就必须在解析过程中根据`startElement()`和`endElement()`定位当前正在读取的节点，可以使用栈结构保存，每遇到一个`startElement()`入栈，每遇到一个`endElement()`出栈，这样，读到`characters()`时我们才知道当前读取的文本是哪个节点的。可见，使用SAX API仍然比较麻烦。
  >
  > ### 练习
  >
  > 使用SAX解析XML。
  >
  > [下载练习](https://liaoxuefeng.com/books/java/xml-json/sax/xml-sax.zip)
  >
  > ### 小结
  >
  > SAX是一种流式解析XML的API；
  >
  > SAX通过事件触发，读取速度快，消耗内存少；
  >
  > 调用方必须通过回调方法获得解析过程中的数据。

- ### 使用Jackson

  > 前面我们介绍了DOM和SAX两种解析XML的标准接口。但是，无论是DOM还是SAX，使用起来都不直观。
  >
  > 观察XML文档的结构：
  >
  > ```xml
  > <?xml version="1.0" encoding="UTF-8" ?>
  > <book id="1">
  >     <name>Java核心技术</name>
  >     <author>Cay S. Horstmann</author>
  >     <isbn lang="CN">1234567</isbn>
  >     <tags>
  >         <tag>Java</tag>
  >         <tag>Network</tag>
  >     </tags>
  >     <pubDate/>
  > </book>
  > ```
  >
  > 
  >
  > 我们发现，它完全可以对应到一个定义好的JavaBean中：
  >
  > ```java
  > public class Book {
  >     public long id;
  >     public String name;
  >     public String author;
  >     public String isbn;
  >     public List<String> tags;
  >     public String pubDate;
  > }
  > ```
  >
  > 
  >
  > 如果能直接从XML文档解析成一个JavaBean，那比DOM或者SAX不知道容易到哪里去了。
  >
  > 幸运的是，一个名叫Jackson的开源的第三方库可以轻松做到XML到JavaBean的转换。我们要使用Jackson，先添加一个Maven的依赖：
  >
  > - com.fasterxml.jackson.dataformat:jackson-dataformat-xml:2.10.1
  >
  > 然后，定义好JavaBean，就可以用下面几行代码解析：
  >
  > ```java
  > InputStream input = Main.class.getResourceAsStream("/book.xml");
  > JacksonXmlModule module = new JacksonXmlModule();
  > XmlMapper mapper = new XmlMapper(module);
  > Book book = mapper.readValue(input, Book.class);
  > System.out.println(book.id);
  > System.out.println(book.name);
  > System.out.println(book.author);
  > System.out.println(book.isbn);
  > System.out.println(book.tags);
  > System.out.println(book.pubDate);
  > ```
  >
  > 
  >
  > 注意到`XmlMapper`就是我们需要创建的核心对象，可以用`readValue(InputStream, Class)`直接读取XML并返回一个JavaBean。运行上述代码，就可以直接从Book对象中拿到数据：
  >
  > ```plain
  > 1
  > Java核心技术
  > Cay S. Horstmann
  > 1234567
  > [Java, Network]
  > null
  > ```
  >
  > 
  >
  > 如果要解析的数据格式不是Jackson内置的标准格式，那么需要编写一点额外的扩展来告诉Jackson如何自定义解析。这里我们不做深入讨论，可以参考Jackson的[官方文档](https://github.com/FasterXML/jackson)。
  >
  > ### 练习
  >
  > 使用Jackson解析XML。
  >
  > [下载练习](https://liaoxuefeng.com/books/java/xml-json/jackson/xml-jackson.zip)
  >
  > ### 小结
  >
  > 使用Jackson解析XML，可以直接把XML解析为JavaBean，十分方便。

- ### 使用JSON

  > 前面我们讨论了XML这种数据格式。XML的特点是功能全面，但标签繁琐，格式复杂。在Web上使用XML现在越来越少，取而代之的是JSON这种数据结构。
  >
  > JSON是JavaScript Object Notation的缩写，它去除了所有JavaScript执行代码，只保留JavaScript的对象格式。一个典型的JSON如下：
  >
  > ```json
  > {
  >     "id": 1,
  >     "name": "Java核心技术",
  >     "author": {
  >         "firstName": "Abc",
  >         "lastName": "Xyz"
  >     },
  >     "isbn": "1234567",
  >     "tags": ["Java", "Network"]
  > }
  > ```
  >
  > 
  >
  > JSON作为数据传输的格式，有几个显著的优点：
  >
  > - JSON只允许使用UTF-8编码，不存在编码问题；
  > - JSON只允许使用双引号作为key，特殊字符用`\`转义，格式简单；
  > - 浏览器内置JSON支持，如果把数据用JSON发送给浏览器，可以用JavaScript直接处理。
  >
  > 因此，JSON适合表示层次结构，因为它格式简单，仅支持以下几种数据类型：
  >
  > - 键值对：`{"key": value}`
  > - 数组：`[1, 2, 3]`
  > - 字符串：`"abc"`
  > - 数值（整数和浮点数）：`12.34`
  > - 布尔值：`true`或`false`
  > - 空值：`null`
  >
  > 浏览器直接支持使用JavaScript对JSON进行读写：
  >
  > ```javascript
  > // JSON string to JavaScript object:
  > jsObj = JSON.parse(jsonStr);
  > 
  > // JavaScript object to JSON string:
  > jsonStr = JSON.stringify(jsObj);
  > ```
  >
  > 
  >
  > 所以，开发Web应用的时候，使用JSON作为数据传输，在浏览器端非常方便。因为JSON天生适合JavaScript处理，所以，绝大多数REST API都选择JSON作为数据传输格式。
  >
  > 现在问题来了：使用Java如何对JSON进行读写？
  >
  > 在Java中，针对JSON也有标准的JSR 353 API，但是我们在前面讲XML的时候发现，如果能直接在XML和JavaBean之间互相转换是最好的。类似的，如果能直接在JSON和JavaBean之间转换，那么用起来就简单多了。
  >
  > 常用的用于解析JSON的第三方库有：
  >
  > - Jackson
  > - GSON
  > - JSON-lib
  > - ...
  >
  > 注意到上一节提到的那个可以解析XML的浓眉大眼的Jackson也可以解析JSON！因此我们只需要引入以下Maven依赖：
  >
  > - com.fasterxml.jackson.core:jackson-databind:2.12.0
  >
  > 就可以使用下面的代码解析一个JSON文件：
  >
  > ```java
  > InputStream input = Main.class.getResourceAsStream("/book.json");
  > ObjectMapper mapper = new ObjectMapper();
  > // 反序列化时忽略不存在的JavaBean属性:
  > mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
  > Book book = mapper.readValue(input, Book.class);
  > ```
  >
  > 
  >
  > 核心代码是创建一个`ObjectMapper`对象。关闭`DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES`功能使得解析时如果JavaBean不存在该属性时解析不会报错。
  >
  > 把JSON解析为JavaBean的过程称为反序列化。如果把JavaBean变为JSON，那就是序列化。要实现JavaBean到JSON的序列化，只需要一行代码：
  >
  > ```java
  > String json = mapper.writeValueAsString(book);
  > ```
  >
  > 
  >
  > 要把JSON的某些值解析为特定的Java对象，例如`LocalDate`，也是完全可以的。例如：
  >
  > ```json
  > {
  >     "name": "Java核心技术",
  >     "pubDate": "2016-09-01"
  > }
  > ```
  >
  > 
  >
  > 要解析为：
  >
  > ```java
  > public class Book {
  >     public String name;
  >     public LocalDate pubDate;
  > }
  > ```
  >
  > 
  >
  > 只需要引入标准的JSR 310关于JavaTime的数据格式定义至Maven：
  >
  > - com.fasterxml.jackson.datatype:jackson-datatype-jsr310:2.12.0
  >
  > 然后，在创建`ObjectMapper`时，注册一个新的`JavaTimeModule`：
  >
  > ```java
  > ObjectMapper mapper = new ObjectMapper().registerModule(new JavaTimeModule());
  > ```
  >
  > 
  >
  > 有些时候，内置的解析规则和扩展的解析规则如果都不满足我们的需求，还可以自定义解析。
  >
  > 举个例子，假设`Book`类的`isbn`是一个`BigInteger`：
  >
  > ```java
  > public class Book {
  > 	public String name;
  > 	public BigInteger isbn;
  > }
  > ```
  >
  > 
  >
  > 但JSON数据并不是标准的整形格式：
  >
  > ```json
  > {
  >     "name": "Java核心技术",
  >     "isbn": "978-7-111-54742-6"
  > }
  > ```
  >
  > 
  >
  > 直接解析，肯定报错。这时，我们需要自定义一个`IsbnDeserializer`，用于解析含有非数字的字符串：
  >
  > ```java
  > public class IsbnDeserializer extends JsonDeserializer<BigInteger> {
  >     public BigInteger deserialize(JsonParser p, DeserializationContext ctxt) throws IOException, JsonProcessingException {
  >         // 读取原始的JSON字符串内容:
  >         String s = p.getValueAsString();
  >         if (s != null) {
  >             try {
  >                 return new BigInteger(s.replace("-", ""));
  >             } catch (NumberFormatException e) {
  >                 throw new JsonParseException(p, s, e);
  >             }
  >         }
  >         return null;
  >     }
  > }
  > ```
  >
  > 
  >
  > 然后，在`Book`类中使用注解标注：
  >
  > ```java
  > public class Book {
  >     public String name;
  >     // 表示反序列化isbn时使用自定义的IsbnDeserializer:
  >     @JsonDeserialize(using = IsbnDeserializer.class)
  >     public BigInteger isbn;
  > }
  > ```
  >
  > 
  >
  > 类似的，自定义序列化时我们需要自定义一个`IsbnSerializer`，然后在`Book`类中标注`@JsonSerialize(using = ...)`即可。
  >
  > ### 反序列化
  >
  > 在反序列化时，Jackson要求Java类需要一个默认的无参数构造方法，否则，无法直接实例化此类。存在带参数构造方法的类，如果要反序列化，注意再提供一个无参数构造方法。
  >
  > 对于`enum`字段，Jackson按String类型处理，即：
  >
  > ```java
  > class Book {
  >     public DayOfWeek start = MONDAY;
  > }
  > ```
  >
  > 
  >
  > 序列化为：
  >
  > ```json
  > {
  >     "start": "MONDAY"
  > }
  > ```
  >
  > 
  >
  > 对于`record`类型，Jackson会自动找出它的带参数构造方法，并根据JSON的key进行匹配，可直接反序列化。对`record`类型的支持需要版本`2.12.0`以上。
  >
  > ### 练习
  >
  > 使用Jackson解析JSON。
  >
  > [下载练习](https://liaoxuefeng.com/books/java/xml-json/json/json-jackson.zip)
  >
  > ### 小结
  >
  > JSON是轻量级的数据表示方式，常用于Web应用；
  >
  > Jackson可以实现JavaBean和JSON之间的转换；
  >
  > 可以通过Module扩展Jackson能处理的数据类型；
  >
  > 可以自定义`JsonSerializer`和`JsonDeserializer`来定制序列化和反序列化。

```tex
XML（由于现代开发过程中，不需要开发人员手动的解析XML文档了，因此主要了解xml即可）
==================================================================================================
# XML：可扩展标记语言（代码见IdeaProjects项目中），虽然可扩展可自定义，但是如果编写了约束规则，就不能随意写了；
	XML文档是用XML标记写的，最初的XML文档作为网页来使用，后来被淘汰了。目前在java使用xml文档来代替
	properties作为配置文件，因为它可以表示比较复杂的配置关系，不只是key=value这种一对一的形式。
	XML用于数据的传输或当作配置文件来使用，它是更严格的HTML版本；此外XML还有另一个版本XHTML，它是可扩展的HTML；
（在IDEA中使用xml文件：settings->editor->文件和代码模板-->
新建文件，名字可以叫：XML Template，extension扩展名可以为：xml）

# XML语法：
	- 开头必须顶行声明，上面连空格也不能有：<?xml version="1.0" encoding="utf-8" standalone='yes/no'?>，
		表示xml版本是1.0，字符编码utf-8，standalone说明这个文档是否是独立的，默认为no非独立；
		（注意：如果xml引入了外部的dtd约束文件，那么standalone不能设置为yes）

	- XML中，主题内容都是由元素（Element）组成的，元素一般由：开始标签、属性、元素内容、结束标签组成；
		如果一个元素中没有嵌套子元素，且没有元素内容，这样的元素叫“空元素”，结束标签可以用/代替；

	- XML区分大小写，且空格不会像HTML那样被自动删除；

	- 一个XML文档必须以一个【根目录标签】开始，其他标签作为【根目录标签】的“子标签”或者“间接子标签”出现的；html可以有多个根元素；

	- xml中，属性的内容，必须包含在单引号('')或双引号("")内；
		<?xml version="1.0" encoding="utf-8"?>
		<学校 name="某某大学" address="xxx">
			<班级 name="软工1班">
				<学生>张三</学生>
			</班级>
			<班级 name="软工2班">
				<学生>李四</学生>
			</班级>
			...
		</学校>
		（注意：只能有一个根，它就是学校元素）

	- 	“ <学校></学校> ”：根节点
		“ <班级></班级> ”：元素节点
		“ 张三 ” 		：文本节点/文本值
		“name="软工2班"”：属性节点

	- 实体符号：（在xml中一定要注意，你的这些特殊符号都要用实体符号转义才行）
		&lt;		<
		&gt;		>
		&amp;		&
		&apos;		'
		&quot;		"
		（或者使用转义标签：<![CDATA[]]>）

------------------------------------------------------------------------------------------------
由于xml语法松散，元素名字怎么写都行，为了规范化，出现了xml约束，作用是：
	* 设置可以出现的“元素名”
	* 设置可以在元素中出现的“属性名”
	* 设置元素之间的父子关系和兄弟关系
==================常见的xml约束有2种：===========================================================

# dtd约束：（是一种简单约束，上手快，但没schema细致）

	- DTD约束有3种方式：
		内部dtd：<!DOCTYPE 根元素名 [
					DTD语句...
				]>
		外部dtd：<!DOCTYPE 根元素名 SYSTEM "本地dtd文件地址">
		公共dtd（引入网络中的dtd）：<!DOCTYPE 根元素名 PUBLIC "dtd名" "网络中dtd的URL">

	- 语法：
（*是0到多次，+是至少一次，?是0或1次，不写就是必须且只能出现1次，逗号,表示必须按照指定的顺序，括号()给元素进行分组，竖线|表示或）
		<!ELEMENT 标签名>：可以在xml中出现的标签名；

		<!ELEMENT 标签名 EMPTY/ANY>：该标签是一个空元素/该标签中可以出现任何东西；

		<!ELEMENT 标签名 (name,age)>：标签里必须有两个属性name和age且得按顺序出现；

		<!ELEMENT 标签名 (#PCDATA) >：当前标签的里的内容是普通文本txt，缩写为：parsed character普通文本数据；

		<!ATTLIST 标签名 属性名 属性类型 类型说明>：给标签定义属性
			属性类型：
				CDATA：字符数据，类似于#PCDATA；
				Enumerated（枚举）：这样用，(老虎|兔子|大象) "大象"，表示有3个供选择的值，不写默认是大象；
				ID：将该属性值设置为唯一的，不能重复，类型说明只能设置为#REQUIRED必须的，或#IMPLIED可有可无的；
				IDREF/IDREFS：为属性值之间设置关系；
			类型说明：
				#REQUIRED：必须有；
				#FIXED "值"：属性默认有一个固定值，写不写都有
				#IMPLIED：可有可无；
				"值"：默认值


# xsd约束：（也叫Schema约束，是更高级的约束，是dtd约束的升级版，后缀名.xsd，开发中大多是这种约束方式）
	* 由于原来的约束方式中，xml文件中只能有一个约束，如果想要多个约束文件共同约束一个xml，或者在原来的基础上再加一些约束，
	dtd约束就不行了，此时就必须使用高级约束XSD；
	* XSD约束文件本身也是一种xml文件，其中内容遵循xml语法；dtd的验证需要用另一套机制，但XSD则采用与xml文档相同的合法性验证机制；
	* XSD对命名空间支持的非常好，而dtd不支持命名空间；XSD支持的数据类型也更多，可以是自定义类型；
	* 由于XSD对文档的约束更细致，所以它的语法也复杂的多；

===============================================================================================================

xsd文件内容：
-----------------------------------------------------------------------------------------------------------
<!--文档声明-->
<?xml version="1.0" encoding="UTF-8"?>
<!--
	# 以abc:schema表示模式定义的开始，后面的命名空间的声明表示，abc标签中用到的元素和数据类型来自该命名空间：
		"http://www.w3.org/2001/XMLSchema"；它还规定了使用来自该命名空间的元素和数据类型，
		应该使用“前缀abc：”去标记；
	# xmlns:abc="http://www.w3.org/2001/XMLSchema"表示，引入网络中这个命名空间来约束本文件（本文件也是约束文件）
	# abc:element表示element来自http://www.w3.org/2001/XMLSchema文档，它里面的约束来约束着element；
	# xmln="http://www.w3school.com.cn"表示默认的命名空间；
-->
<abc:schema xmlns:abc="http://www.w3.org/2001/XMLSchema">//给这个命名空间起别名为：abc；

<abc:element name="students">	//abc:element指定根元素名为students
    <abc:complexType>			//设置根标签元素内容为复杂类型
		<abc:sequence>			//指定内置标签的顺序

			<abc:element name="student" maxOccurs="2">//maxOccurs表示最多出现2次
				<abc:complexType>
					<abc:sequence>
						<abc:element name="name" type="abc:string"/>
						<abc:element name="age" type="abc:int"/>
						<abc:element name="sex" type="abc:string"/>
					</abc:sequence>

					//给student指定了一个id属性，可选的，固定值001
					<abc:attribute name="id" use="optional" fixed="001"></abc:attribute>
				</abc:complexType>
			</abc:element>

		</abc:sequence>
    </abc:complexType>
</abc:element>

<!--模式定义的结束-->
</abc:schema>
-----------------------------------------------------------------------------------------------------------
	* 怎么在xml中引入使用呢？
		1)开头加上：<?xml version="1.0" encoding="UTF-8"?>
				   <根标签名 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"//遵循标准命名空间，xsi:schemaLocation在这定义
				   xsi:schemaLocation="自定义的命名空间  xsd文件的URL地址">//相当于import

				   </跟标签名>
			引入多个约束文件：
					<?xml version="1.0" encoding="UTF-8"?>
					<根标签名 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
							 xmlns:demo="http://www.bjpowernode.com/demo/mydemo"//给命名空间起别名

					xsi:schemaLocation="http://www.node.com/bj/pw  http://www.bjpowernode.com/demo.xsd
	（另一个namespace的名和地址）	http://www.bjpowernode.com/demo/mydemo http://www.bjpowernode.com/demo.xsd">

					</跟标签名>
		2)直接引入同一目录下本地文件：
			<?xml version="1.0" encoding="UTF-8"?>
			<根标签名 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"//约束遵循xsd标准，xsi:noNamespaceSchemaLocation在这
			xsi:noNamespaceSchemaLocation="文件名.xsd">

			</根标签名>

==========XML解析============================================================================================
在Java中默认提供了两种解析xml的方式，分别是DOM和SAX方式解析：

	1、DOM(文档对象模型)：
		一次性将xml文档中所有的dom加载到内存中去，并转为树状模型，然后一个节点去访问或更改；这种方式解析的效率较高，
		但是比较消耗内存，适合小的xml文件；

	2、SAX解析：
		这种方式会逐行地去扫描XML文档，当遇到标签时会触发解析处理器，采用事件处理的方式解析XML，是基于事件模型
		完成的，所有的api都在org.xml中被封装好了，相对来说简单一些；根据开发人员的需要，一次只将若干个满足条件
		的标签加载到内存当中；优点是：在读取文档的同时即可对XML进行处理，不必等到文档加载结束，相对快捷；
		xml资源不会长久保留在内存中，因此不存在占用内存的问题，可以用来解析超大XML；缺点是只能读取xml，无法增删改；

（在实际开发中，我们一般采用DOM的方式解析；dom4j封装了dom很好用，我们一般用xpath+dom4j来解析）

什么是 XPath?
	# XPath是使用“路径表达式”来选取XML文档中的节点或节点集。这些路径表达式和我们在常规的电脑文件系统中看到的路径非常类似；
	# 语法：
		常用的：
			节点名		选取此节点的所有子节点
			/			从根节点开始进行选取，代表绝对路径
			//			从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置
			.			选取当前节点
			..			选取当前节点的父节点
			@			选取属性

				例如：
				bookstore 		选取 bookstore 元素的所有子节点
				/bookstore 		选取根元素bookstore
				bookstore/book 	选取bookstore下的所有book子元素
				//book			选取当前路径下的所有book子元素，而不管它们在文档中的位置
				bookstore//book 选择属于 bookstore 元素的后代的所有 book 元素，而不管它们位于 bookstore 之下的什么位置
				//@lang		 	选取名为 lang 的所有属性

		谓语：用来查找某个特定的节点或者包含某个指定的值的节点，谓语被嵌在方括号中
			例如：
				/bookstore/book[1] 				选取属于 bookstore 子元素的第一个 book 元素；
				/bookstore/book[last()] 		选取属于 bookstore 子元素的最后一个 book 元素；
				/bookstore/book[last()-1] 		选取属于 bookstore 子元素的倒数第二个 book 元素；
				/bookstore/book[position()<3] 	选取最前面的两个属于 bookstore 元素的子元素的 book 元素；
				//title[@lang] 					选取所有属性名为 lang 的 title 元素；
				//title[@lang='eng'] 			选取所有 title 元素，且这些元素拥有值为 eng 的 lang 属性；
				/bookstore/book[price>35.00] 	选取 bookstore 元素的所有 book 元素，且其中的 price 元素的值须大于 35.00；
```

