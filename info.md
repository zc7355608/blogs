# 名词解释

- **Token（令牌）**：是一个广义概念。

  - 编程与人工智能（AI）领域，指的是**文本的最小单位**。大语言模型在理解文字时，并不是直接看单个字，而是先把句子切分成小块（**可以是词、字或子词**），这些小块就是 Token。
  - 在计算机安全与认证领域，它是一种经过加密或签名的数据载体、用来证明用户身份的凭证。Token通常是一个加密字符串。JWT就是Token的一种实现方式。Token 放在请求头中，不受 Cookie 的同源策略限制，便于实现单点登录（一次登录，访问多个系统）和移动端 App 认证。

- **JWT**：JWT是Json Web Token的缩写，它是目前Web开发中最流行的Token实现方案。一个典型的JWT字符串长这样：

  ```tex
  eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMzQ1LCJ1c2VybmFtZSI6InpoYW5nc2FuIiwicm9sZSI6ImFkbWluIiwiZXhwIjoxNzAwMDAwMDAwfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
  ```

  它由三部分组成，用点号`.`分隔：**Header(头部).Payload(负载).Signature(签名)**

  1. Header（头部）

     ```json
     {
       "alg": "HS256",    // 签名算法，常用HS256(对称加密)或RS256(非对称加密)
       "typ": "JWT"       // 令牌类型，固定为JWT
     }
     ```

  2. Payload（负载）- **核心数据区**

     ```json
     {
       // 注册声明（标准字段）
       "iss": "auth-server",     // 签发者
       "sub": "user-token",      // 主题
       "aud": "client-app",      // 受众
       "exp": 1700000000,        // 过期时间（时间戳）
       "iat": 1699996400,        // 签发时间
       "nbf": 1699996400,        // 生效时间
       
       // 公共/私有声明（自定义数据）
       "userId": 12345,
       "username": "zhangsan",
       "role": "admin",
       "email": "zhangsan@example.com"
     }
     ```

     - **重要**：Payload只是Base64编码，**不是加密的**！任何人都可以解码看到内容
     - **绝对不要**存放密码、信用卡号等敏感信息

  3. Signature（签名）- **防伪标识**

     ```tex
     HMACSHA256(
       base64UrlEncode(header) + "." + base64UrlEncode(payload),
       secret                    // 服务器保存的密钥
     )
     ```

     - 服务器用密钥对前两部分进行签名
     - 如果有人篡改了Payload，服务器验证签名时就会发现不匹配
     - 没有密钥的人无法伪造有效的签名

  JWT的原理：服务器响应给前端一段短期有效的JWT字符串，前端对其进行保存（通常保存在`LocalStorage/SessionStorage`）。每次请求时将其放在请求头中携带给服务器。服务器对Token验证通过后再响应用户数据。

- **CSRF（跨站请求伪造）**：Cross-Site Request Forgery，跨站请求伪造是一种：攻击者诱导用户访问恶意网站，利用用户在其他网站已登录的状态（保存的Cookie），通过一段恶意脚本**伪造请求**，执行非用户本意操作的攻击方式。

  CSRF防护措施：

  1. CSRF Token（最常用、最有效）：服务器生成随机Token，存在Session中，前端提交请求时必须携带这个Token验证。
  2. SameSite Cookie属性（现代浏览器防护）：设置Cookie的`SameSite`属性，限制跨站请求是否携带Cookie。
  3. 验证Referer/Origin头：检查请求来源，只接受可信域的请求。
  4. 使用自定义请求头：对于API请求，要求必须携带自定义头（如`X-Requested-With: XMLHttpRequest`）。
  5. 双重认证（敏感操作）：对敏感操作（转账、改密）进行二次验证。

- **XSS（跨站脚本攻击）**：XSS（Cross-Site Scripting，跨站脚本攻击）是一种**代码注入攻击**。攻击者通过在目标网站上**注入恶意脚本**，当其他用户访问时执行这些脚本，从而窃取信息或冒充用户操作。

  XSS防护措施：

  1. 输入过滤（Input Sanitization）：永远不要信任用户输入！

  2. 输出编码（Output Encoding）：根据数据将要出现的上下文，将其转换成该上下文中无意义的形式。

  3. 内容安全策略（CSP）- **最强防线**：通过HTTP头限制脚本执行。

     ```http
     # 只允许同源脚本
     Content-Security-Policy: script-src 'self'
     
     # 允许特定CDN
     Content-Security-Policy: script-src 'self' https://trusted-cdn.com
     
     # 禁止内联脚本（防止反射型XSS）
     Content-Security-Policy: script-src 'self' 'unsafe-inline'
     
     # 完整示例
     Content-Security-Policy: 
       default-src 'self';
       script-src 'self' https://apis.google.com;
       style-src 'self' https://fonts.googleapis.com;
       img-src 'self' data: https:;
       connect-src 'self' https://api.example.com;
     ```

  4. HttpOnly Cookie（防窃取）：

     ```js
     // 设置Cookie为HttpOnly，JavaScript无法读取
     res.cookie('sessionId', 'abc123', {
       httpOnly: true,   // 禁止JS访问
       secure: true,     // 仅HTTPS
       sameSite: 'strict'
     });
     
     // 现在即使有XSS漏洞，攻击者也读不到Cookie
     console.log(document.cookie);  // 空字符串或只显示非HttpOnly的Cookie
     ```

  5. 框架自带防护（推荐）：现代前端框架默认都有XSS防护

     ```jsx
     // React 自动转义
     const userInput = '<script>alert(1)</script>';
     return <div>{userInput}</div>;  // 显示文本，不执行脚本
     
     // Vue 自动转义
     <div>{{ userInput }}</div>  // 安全的
     
     // 如果确实需要HTML（危险操作）
     <div dangerouslySetInnerHTML={{__html: sanitizedHtml}} />
     ```

- **CDN**：CDN是”内容分发网络“（Content Delivery Network）的缩写，主要用来加速互联网内容的传输，让用户更快、更稳定地访问网站、视频、文件等内容。

  CDN的工作原理：

  1. 分布式节点。CDN会在多个地区部署服务器节点（也叫**边缘节点**），这些节点离用户更近。
  2. 内容缓存。当用户访问某个网站的内容时，CDN会把这些内容缓存到距离用户最近的节点上。
  3. 就近访问。下次再有用户访问同样内容时，会直接从最近的CDN节点获取，速度比跨越半个地球访问资源站快得多。

- **客户端组件和服务器组件**：在Next.js（特别是App Router模式）中，页面可以包含两种类型的组件，服务器组件和客户端组件。它们在渲染方式、执行环境、能力和限制方面有本质区别。

  - 服务器组件只在服务器运行，服务器只返回渲染后的HTML（不包含组件的逻辑代码）。它可以直接访问后端服务器的资源（数据库、API等）。但不能访问浏览器API、不能使用useState、useEffect等依赖客户端环境的Hooks。
  - 客户端组件就是运行在浏览器中的React、Vue组件。

- **CSR**：

- **SSR**：

- **SSG**：

- **TTFB**：TTFB是 **Time to First Byte** 的缩写，中文通常翻译为**首字节时间**。表示**从客户端发起请求，到接收到服务器返回的第一个字节数据所花费的时间**。这是衡量网站或应用响应速度的重要指标之一。它反映了请求在网络传输、服务器处理、生成响应等阶段所耗费的总时间。简单来说，它就是“从你开口问，到你听到第一个字之间的等待时间”。

- **布局组件**：是一种专门负责页面结构组织、不包含具体业务逻辑的 React 组件。它的主要职责是"**把内容放在哪里**"，而不是"**展示什么内容**"。

  ```jsx
  // ❌ 普通组件：既负责布局，又负责内容
  const UserCard = ({ user }) => (
    <div style={{ padding: '20px', border: '1px solid #ccc' }}>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </div>
  );
  
  // ✅ 布局组件：只负责结构
  const CardLayout = ({ children, padding = '20px' }) => (
    <div style={{ padding, border: '1px solid #ccc' }}>
      {children}
    </div>
  );
  
  // ✅ 内容组件：只负责内容
  const UserInfo = ({ user }) => (
    <>
      <h3>{user.name}</h3>
      <p>{user.email}</p>
    </>
  );
  ```

- **Serverless**：Serverless（无服务器）是一种云计算执行模型，在这种模式下，开发者无需关心底层服务器的部署和运维，只需专注于编写业务逻辑代码。应用运行所需的计算资源（服务器程序、数据库等）由云厂商动态提供和管理，按照实际使用量计费。尽管名为”无服务器“，实际上服务器依然存在，只是开发者不必直接配置和维护它们。

  Serverless的核心特点：自动弹性伸缩、按需计费、无需运维、快速开发部署。

  总之：**Serverless 就是让你专注于写代码，把服务器、扩容、运维这些脏活累活全部交给云平台。**

  它可以让开发者更专注业务逻辑、减少运维和管理负担的云计算模型，特别适合事件驱动、突发流量或需要快速上线的应用。

  常见的Serverless服务形式：

  - **函数即服务（FaaS）**：开发者编写函数，当HTTP请求时函数就会自动执行。例如：AWS Lambda、Azure Function、阿里云函数计算等..
  - **后端即服务（BaaS）**：提供认证、数据库、存储等后端服务，开发者直接调用接口即可，不需要自己搭建服务器。

- **CMS**：CMS是**内容管理系统（Content Management System）**，它是一个**让你不用懂代码，也能轻松搭建和管理网站内容的软件**。你可以把它理解为**网站的操作系统**，你只需要负责打字、传图片、排版，剩下的技术问题（比如怎么显示、怎么存）都由它搞定。

- **VuePress**：VuePress 是一个以 **Markdown** 为中心的**静态网站生成器（Static Site Generator, SSG）**。我们只需要写Markdown文档即可，它把这些文档“编译”成一堆最终的、无法再变动的 HTML 静态网页文件。

- **JAMstack系统**：JAMstack代表JavaScript、API和Markup（标记语言）。它是一种现代的网站架构思想，核心思想是**提前生成好所有的网页（静态文件），并通过通用的 API 来取代传统的数据库**。解决了传统 CMS 性能差、安全性低的问题。

- **AMP**：AMP（Accelerated Mobile Pages，加速移动页面）是由谷歌发起并主导的一个开源项目，旨在通过简化技术规范和利用缓存，**大幅提升移动端网页的加载速度**，为用户提供近乎瞬时的打开体验。你可以把它理解为一种：为移动端**“量身定制”并“预先优化”的精简版网页**。

------

