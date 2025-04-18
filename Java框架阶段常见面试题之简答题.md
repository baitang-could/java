# 一、JavaWeb基础(7道)

## 第1题：Session和Cookie的机制和区别？

http是无状态的协议，也就是说不会记录多次请求之间的数据状态!! 所以需要使用session或者cookie技术进行状态记录!

**其他细节:**

存储位置：

- **Session**：Session 数据通常保存在服务器端。服务器会为每个用户生成一个唯一的会话 ID，并将该 ID 存储在客户端的 Cookie 中。通过这个 ID，服务器可以在会话期间查找并存储与该用户相关的信息（例如：用户登录状态、购物车信息等）。
- **Cookie**：Cookie 数据是存储在客户端的浏览器中，它存储的是一些键值对信息。Cookie 中的信息会随每个请求发送到服务器。

生命周期：

- **Session**：Session 的生命周期通常是与用户的浏览会话相同。当用户关闭浏览器或会话过期时，所谓的过期就是因为携带SessionID的Cookie失效,所以Session 数据无法没查找,就相当于过期和失效! 但是服务器会默认存储Session30分钟! 30分钟超时服务器会删除相关的会话信息。
- **Cookie**：Cookie 的生命周期由 `expires` 或 `max-age` 属性决定。它可以被设置为会话级别（浏览器关闭时过期）或持久级别（指定时间点过期）。即使关闭浏览器，持久化的 Cookie 仍会被存储并在下次访问时发送。

 安全性：

- **Session**：由于 Session 数据存储在服务器端，数据本身不会暴露给客户端，因此比 Cookie 更加安全。即使攻击者获取到了用户的 Cookie，无法直接获取用户的敏感信息。
- **Cookie**：Cookie 存储在客户端，容易受到如 XSS（跨站脚本攻击）等安全漏洞的威胁。如果用户的浏览器受到攻击，恶意代码可以访问 Cookie 中存储的信息。为了提高安全性，可以使用 `Secure` 和 `HttpOnly` 标志来限制 Cookie 的访问权限。

存储容量：

- **Session**：Session 存储的数据量通常不受浏览器限制，数据存储在服务器端，因此不受客户端存储容量的限制。
- **Cookie**：Cookie 存储空间有限，大多数浏览器对单个 Cookie 的存储大小限制大约为 4KB，且每个域名最多只能存储一定数量的 Cookie（通常是 20 个左右）。

数据传输：

- **Session**：每次请求时，浏览器通过 Cookie 发送 Session ID（如通过 HTTP 请求头的 `Cookie` 字段传递）。但实际的会话数据在服务器端，只有 Session ID 传递到服务器，服务器根据 ID 获取实际数据。
- **Cookie**：每次请求时，浏览器会将所有相关的 Cookie 信息自动包含在请求中（即通过 HTTP 请求头的 `Cookie` 字段传递），这会增加请求的大小，尤其是当 Cookie 数据较大时。

可扩展性：

- **Session**：Session 是基于服务器的，需要在服务器端维护会话信息。对于大规模应用（如分布式系统或多服务器环境），通常需要采用集中式存储（如 Redis）来管理 Session 数据。
- **Cookie**：Cookie 存储在客户端，客户端可以自行管理，因此在分布式环境中更易扩展，不需要服务器端维护每个用户的状态。

用途：

- **Session**：Session 主要用于存储用户的临时信息（如登录状态、购物车信息、用户偏好设置等），并且是为了在一个用户会话期间维持状态。用户关闭浏览器或会话过期时，Session 信息会被销毁。
- **Cookie**：Cookie 主要用于存储用户的持久性信息（如记住用户登录状态、跟踪用户行为、保存用户偏好设置等）。Cookie 可以跨会话存储，并且可以长期保存（直到过期）。

**总结:**

| **属性**     | **Session**                         | **Cookie**                                 |
| ------------ | ----------------------------------- | ------------------------------------------ |
| **存储位置** | 服务器端                            | 客户端（浏览器）                           |
| **生命周期** | 默认30分钟，最大不会超过一次会话    | 默认会话级别也可以持久化存储               |
| **安全性**   | 更安全，数据存储在服务器端          | 不如 Session 安全，容易受 XSS 攻击威胁     |
| **存储容量** | 服务器上存储，容量不受浏览器限制    | 4KB 限制，每个域名大约 20 个 Cookie        |
| **数据传输** | 每次请求发送 Session ID             | 每次请求都会发送所有相关的 Cookie 信息     |
| **可扩展性** | 需要在多台服务器间同步 Session 数据 | 自动管理，不依赖服务器，适合分布式环境     |
| **用途**     | （会话内）用户会话期间维持状态      | 存储持久信息，跨会话存储用户数据（跨会话） |

## 第2题：Servlet实现方案和生命周期方法!

Servlet 是 Java EE 规范(接口)的一部分，它运行在 Servlet 容器中，负责处理 HTTP 请求并生成响应数据。

Servlet的实现方案有三种:

方案1:  通过实现 `javax.servlet.Servlet` 接口来定义一个 Servlet 类。

方案2:  通过继承 `javax.servlet.GenericServlet ` 抽象类来定义一个 Servlet 类。

方案3:  通过继承 `javax.servlet.http.HttpServlet` 类来定义一个 Servlet 类。

总结对比:

| 特性          | Servlet                              | GenericServlet               | HttpServlet                                        |
| ------------- | ------------------------------------ | ---------------------------- | -------------------------------------------------- |
| 实现方式      | 实现 `Servlet` 接口                  | 继承 `GenericServlet`        | 继承 `HttpServlet`                                 |
| 主要方法      | `service()`                          | `service()`                  | `doGet()`, `doPost()`,`service()` 等 HTTP 处理方法 |
| 是否专注 HTTP | 否                                   | 否                           | 是                                                 |
| 简单性        | 较复杂，需要自己管理生命周期         | 简单，自动管理生命周期       | 更简单，专注于 HTTP 请求处理                       |
| 适用场景      | 通用请求处理，可以处理各种类型的请求 | 通用请求处理，适用于各种协议 | 适合 Web 应用，特别是 HTTP 请求                    |

Servlet 的生命周期由 Servlet 容器管理。Servlet 的生命周期包括从创建到销毁的各个阶段，主要通过以下方法控制：

1. 初始化阶段(`init()` 方法)

   Servlet 容器在首次请求到来之前创建 Servlet 实例，并调用 `init()` 方法进行初始化。

   `init()` 方法只会在 Servlet 实例创建时调用一次，用于执行一些初始化工作，如连接数据库、读取配置文件等。

   方法：

   ``` java
   public void init(ServletConfig config) throws ServletException;
   ```

   `ServletConfig` 对象提供了 Servlet 配置信息，包括初始化参数等。

2. 请求处理阶段(`service()` 方法)

   一旦 Servlet 被初始化，它会进入请求处理阶段，处理客户端的 HTTP 请求。每当一个请求到达时，Servlet 容器会调用 `service()` 方法。

   `service()` 方法会根据请求的类型（如 GET 或 POST）调用对应的处理方法，如 `doGet()`、`doPost()` 等。

   方法：

   ``` java
   public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;
   ```

   它是 Servlet 处理每个请求的核心方法。

3. 销毁阶段(`destroy()` 方法)

   当 Servlet 容器决定不再需要 Servlet 时，容器会调用 `destroy()` 方法进行销毁操作。通常发生在 Servlet 容器关闭或重新加载时。

   `destroy()` 方法用于清理资源，如关闭数据库连接、释放文件句柄等。

   方法签名：

   ``` java
   public void destroy();
   ```

4. **服务请求的多个线程处理**

   在 Servlet 容器中，通常每个 Servlet 请求会由不同的线程处理。因此，需要注意线程安全性问题。如果多个线程同时访问 Servlet 中的共享资源，需要采取措施（如同步）来保证线程安全,尽量避免全局变量的声明。

## 第3题：Http协议请求和响应的报文格式?

HTTP协议是HyperText Transfer Protocol（超文本传输协议）的缩写，它是Web浏览器与Web服务器之间通信的基础协议。HTTP协议请求和响应都由报文组成，报文格式的结构如下：

- **HTTP请求报文**：包括请求行、请求头部、空行、请求体。

  ![1736306387962](images/1736306387962.png)

- **HTTP响应报文**：包括状态行、响应头部、空行、响应体。

  ![1736306441398](images/1736306441398.png)

常见HTTP协议版本(了解):

1. **HTTP/1.0**
   - **发布时间**：1996年
   - **特征**：HTTP/1.0是HTTP协议的第一个正式版本。它基于请求/响应模型，每次客户端和服务器通信时都会建立一个新的连接，通信完成后立即关闭连接。
   - **缺点**：每个请求都需要建立一个新的TCP连接，造成了较高的延迟和额外的资源消耗。
2. **HTTP/1.1**
   - **发布时间**：1999年
   - 特征:
     - **持久连接**：HTTP/1.1默认使用持久连接（即连接在响应之后保持打开状态），允许多个请求/响应在同一连接上进行，减少了重复建立和关闭连接的开销。
     - **管道化**：支持管道化技术，即在一个TCP连接上，客户端可以发送多个请求，而无需等待每个请求的响应。这有助于减少延迟。
     - **分块传输编码**：支持分块传输编码，允许大文件分块传输，减少了内存压力。
   - **缺点**：尽管引入了持久连接和管道化，但仍然存在"头阻塞"问题，即在大量请求时可能会出现队头阻塞。
3. **HTTP/2**
   - **发布时间**：2015年
   - 特征:
     - **多路复用**：在单一TCP连接上，可以并行处理多个请求和响应，而无需等待响应完成后再发送请求，解决了HTTP/1.x中的头阻塞问题。
     - **二进制分帧**：HTTP/2将所有的通信数据分为更小的二进制帧，提升了传输效率和解析速度。
     - **头部压缩**：通过HPACK算法对HTTP头进行压缩，减少了传输的数据量。
     - **服务器推送**：服务器可以主动推送资源到客户端，而不需要客户端主动请求，优化了资源加载速度。
   - **缺点**：相对于HTTP/1.x，HTTP/2需要更多的计算资源来进行二进制帧的解析和压缩，因此对服务器和客户端的计算能力有一定要求。
4. **HTTP/3**
   - **发布时间**：2022年（仍在推广中）
   - 特征: 
     - 基于QUIC（Quick UDP Internet Connections）协议，而非传统的TCP协议，提供了更低的延迟和更好的网络表现。
     - 解决了TCP协议的拥塞控制和队头阻塞问题。
     - 更加稳定和高效的加密性能，因为QUIC自带TLS加密。
   - **缺点**：因为HTTP/3基于UDP而不是TCP，所以可能会遇到一些防火墙和路由器的兼容问题。

HTTP请求头包含了客户端请求的相关信息，例如客户端的浏览器类型、支持的文件格式、请求的目标地址等。常见的请求头如下(了解)：

1. **Host**：指定服务器的域名和端口，必须包含在每个HTTP请求中，尤其在同一IP下有多个虚拟主机的情况下。
   - 例如：`Host: www.example.com`
2. **User-Agent**：描述发出请求的客户端应用程序的类型及版本。
   - 例如：`User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36`
3. **Accept**：指定客户端可以处理的内容类型（MIME类型），用来告诉服务器客户端可以接收的数据格式。
   - 例如：`Accept: text/html, application/xhtml+xml, application/xml;q=0.9, */*;q=0.8`
4. **Accept-Encoding**：指定客户端支持的内容编码格式（如压缩格式）。
   - 例如：`Accept-Encoding: gzip, deflate, br`
5. **Authorization**：提供用于身份验证的信息，通常用于基于Token或Basic认证的情况。
   - 例如：`Authorization: Basic YWxhZGRpbjpvcGVuc2VzYW1l`
6. **Cookie**：发送与当前请求相关的cookie信息，用于实现会话管理。
   - 例如：`Cookie: user=1234; sessionid=abcd1234`
7. **Connection**：指定是否保持连接。通常使用`keep-alive`来保持连接在多次请求之间不关闭。
   - 例如：`Connection: keep-alive`
8. **Content-Type**：在POST请求中，表示请求体的数据类型。
   - 例如：`Content-Type: application/x-www-form-urlencoded`

HTTP响应头包含了关于响应内容的元数据，如服务器信息、响应的内容类型、缓存策略等。常见的响应头如下(了解)：

1. **Content-Type**：指示响应体的媒体类型（如HTML、JSON、图片等）。
   - 例如：`Content-Type: text/html; charset=UTF-8`
2. **Content-Length**：指示响应体的长度（字节数）。
   - 例如：`Content-Length: 1234`
3. **Date**：响应生成的日期和时间。
   - 例如：`Date: Wed, 21 Oct 2025 07:28:00 GMT`
4. **Server**：包含处理请求的服务器软件信息。
   - 例如：`Server: Apache/2.4.41 (Ubuntu)`
5. **Location**：在重定向响应中，指定客户端应当访问的新地址。
   - 例如：`Location: https://www.example.com/newpage`
6. **Cache-Control**：指定缓存策略，控制资源如何缓存。
   - 例如：`Cache-Control: no-cache, no-store, must-revalidate`
7. **Set-Cookie**：服务器用于向客户端发送cookie，以便在随后的请求中进行会话跟踪。
   - 例如：`Set-Cookie: user=1234; Expires=Wed, 21 Oct 2025 07:28:00 GMT; Path=/`
8. **X-Powered-By**：显示服务器使用的技术或框架。
   - 例如：`X-Powered-By: PHP/7.4.3`
9. **Access-Control-Allow-Origin**：在CORS（跨源资源共享）请求中，指定允许哪些源可以访问资源。
   - 例如：`Access-Control-Allow-Origin: *`

总结

- **HTTP协议版本**从1.0到1.1，再到更高效的2.0和3.0，不断优化网络传输的性能和用户体验。
- **请求头**携带了关于请求的元数据，如客户端类型、支持的格式、身份验证信息等。
- **响应头**则包含了服务器响应的相关信息，如返回的数据类型、缓存策略、服务器信息等。

## 第4题：Http协议GET和POST区别?

浏览器和表单的默认提交方式是GET，get请求效率比POST高

GET请求参数在url地址后拼接，所以有以下特点：

* 请求报文没有请求体

* 少了和请求体相关的请求头参数

* 参数在url地址中拼接，上传参数大小有限制，不能用来上传文件，相对POST请求不安全

POST请求参数在请求报文的请求体中携带，有以下特点：

* 请求报文有请求体，相对安全

* 请求头多了和请求体相关的参数

* 请求体数据没有大小限制可以用来上传文件

除了GET和POST外，还有其他常见的HTTP请求方法，各自有不同的用途：

**PUT**

- **用途**：PUT用于向服务器上传资源，通常用于更新已存在的资源。如果资源不存在，PUT请求也可以创建新资源。它通常用于更新一个完整的资源。
- **请求参数**：PUT请求的参数通常放在请求体中。
- **幂等性**：PUT请求是幂等的，即多次相同的PUT请求会产生相同的效果。

**DELETE**

- **用途**：DELETE请求用于请求删除服务器上的资源。例如，删除数据库中的一条记录或删除文件。
- **幂等性**：DELETE请求是幂等的。即使多次请求删除相同资源，结果不会改变（资源已经被删除）。

**PATCH**

- **用途**：PATCH请求用于对资源进行部分更新。与PUT不同，PATCH请求只传递需要更新的数据，而不是整个资源。
- **幂等性**：PATCH请求不一定是幂等的，具体取决于更新的方式和数据。

**HEAD**

- **用途**：HEAD请求类似于GET请求，但服务器只返回响应头部，而不返回消息体。这用于获取资源的元数据，如文件大小、类型、修改时间等，而不需要下载完整的资源。
- **缓存**：HEAD请求通常用于判断资源是否有变动，以便决定是否重新请求资源。

**OPTIONS**

- **用途**：OPTIONS请求用于询问服务器支持哪些HTTP方法。它通常用于跨域请求时确定允许的请求类型。通过发送OPTIONS请求，客户端可以了解服务器是否允许某种操作。
- **返回结果**：响应头中会包含`Allow`字段，列出服务器支持的HTTP方法。

**TRACE**

- **用途**：TRACE请求用于回显服务器收到的请求内容，主要用于调试。它可以帮助开发人员查看请求在经过代理服务器等中间件时的变化。
- **安全性**：由于TRACE可能会暴露敏感的请求信息，它在生产环境中通常被禁用。

**CONNECT**

- **用途**：CONNECT请求用于在客户端和服务器之间建立一个TCP连接，通常用于通过HTTP代理建立SSL/TLS连接。例如，在访问HTTPS网站时，客户端会使用CONNECT方法与代理服务器建立加密通道。

## 第5题：Http协议常见的响应状态码和含义!

HTTP 协议中的响应状态码用于表示服务器处理请求后的结果。状态码由三位数字组成，第一位数字代表响应的类型，后三位数字用于细化响应内容。常见的 HTTP 状态码如下：

**1xx 信息性状态码**

这些状态码表示请求已经接收，继续处理。

- **100 Continue**：客户端应继续发送请求的剩余部分（请求头已经发送，主体未发送，服务器需要客户端继续发送）。
- **101 Switching Protocols**：服务器根据客户端的请求，切换到另一协议。

**2xx 成功状态码**

这些状态码表示请求已成功处理。

- **200 OK**：请求成功，服务器已处理完请求并返回所请求的资源。
- **201 Created**：请求成功并且服务器创建了新的资源。常见于 POST 请求创建资源。
- **202 Accepted**：请求已接受，但尚未处理完成。通常表示请求已被接受，但操作可能还在后台进行。
- **204 No Content**：服务器成功处理了请求，但没有返回任何内容。
- **205 Reset Content**：服务器处理请求后，要求客户端重置文档视图。
- **206 Partial Content**：服务器已经成功处理了部分请求，常用于文件分块下载。

**3xx 重定向状态码**

这些状态码表示客户端需要进一步的操作才能完成请求，通常是重定向到另一个 URL。

- **301 Moved Permanently**：请求的资源已被永久移动到新的 URL。
- **302 Found (临时重定向)**：请求的资源临时被移动到另一个 URL。此时客户端仍应使用原 URL 进行后续请求。
- **303 See Other**：服务器指示客户端以 GET 方法请求另一个 URL。
- **304 Not Modified**：请求的资源自上次请求后未修改，客户端可以使用缓存的版本。
- **307 Temporary Redirect**：请求的资源临时被移动到其他位置，客户端应继续使用原 URL。
- **308 Permanent Redirect**：请求的资源被永久移动到新的 URL。

**4xx 客户端错误状态码**

这些状态码表示请求出现错误，客户端需要进行修改。

- **400 Bad Request**：请求无效，通常是由于客户端发送的请求格式不正确。
- **401 Unauthorized**：请求未经授权。客户端需要提供身份验证信息。
- **402 Payment Required**：暂时未使用，预留用于将来的支付要求。
- **403 Forbidden**：服务器理解请求，但拒绝执行。常见的情况是客户端没有访问权限。
- **404 Not Found**：请求的资源未找到。
- **405 Method Not Allowed**：请求使用了不被允许的 HTTP 方法（如使用 PUT 请求删除资源）。
- **406 Not Acceptable**：服务器无法根据客户端的请求头提供适当的响应内容类型。
- **407 Proxy Authentication Required**：客户端必须首先通过代理服务器进行身份验证。
- **408 Request Timeout**：服务器等待请求超时。
- **409 Conflict**：请求导致资源冲突，通常是由于客户端请求与当前资源状态不一致。
- **410 Gone**：请求的资源不再可用，且没有更合适的替代资源。
- **411 Length Required**：服务器要求请求包含 Content-Length 头信息。
- **412 Precondition Failed**：请求的前提条件失败。
- **413 Payload Too Large**：请求的实体过大，服务器无法处理。
- **414 URI Too Long**：请求的 URI 长度超过了服务器的处理能力。
- **415 Unsupported Media Type**：请求的媒体类型不受服务器支持。
- **416 Range Not Satisfiable**：请求的范围无效，服务器无法提供部分内容。
- **417 Expectation Failed**：请求头中的 Expect 头信息要求服务器处理，但服务器无法满足该期望。

**5xx 服务器错误状态码**

这些状态码表示服务器在处理请求时发生错误，导致无法完成请求。

- **500 Internal Server Error**：服务器内部错误，无法处理请求。
- **501 Not Implemented**：服务器不支持请求的功能，无法完成请求。
- **502 Bad Gateway**：服务器作为网关或代理时，从上游服务器收到无效响应。
- **503 Service Unavailable**：服务器当前无法处理请求，通常是因为过载或维护。
- **504 Gateway Timeout**：服务器作为网关或代理时，未能从上游服务器获得及时响应。
- **505 HTTP Version Not Supported**：服务器不支持客户端请求的 HTTP 协议版本。

**小结：**

- **1xx**：信息性状态码，告诉客户端请求已被接收，正在处理中。
- **2xx**：成功状态码，表示请求已经成功处理。
- **3xx**：重定向状态码，表示需要进一步的操作来完成请求。
- **4xx**：客户端错误状态码，表示客户端请求有问题。
- **5xx**：服务器错误状态码，表示服务器处理请求时发生错误。

理解这些状态码有助于分析和调试 Web 应用程序，确保用户与服务器之间的交互流畅。

## 第6题：转发和重定向的区别?

“转发”和“重定向”是在Web开发中常用来处理请求的传递，它们虽然都涉及到将请求从一个地方传送到另一个地方，但在行为、目的和实现方式上有所不同。下面是它们的区别：

1. **定义与基本概念**

- **转发（Forwarding）**：
  - 在服务器端，**转发**是指将一个请求从一个服务器或应用程序处理传递给另一个服务器或应用程序进行处理，通常是在同一个Web服务器或Web应用内部完成的。转发是服务器内部的操作，不会改变用户的浏览器地址栏中的URL。
  - 常见的转发方式是通过服务器端的**请求转发**机制，Java中使用`RequestDispatcher.forward()`方法。
- **重定向（Redirecting）**：
  - **重定向**是指服务器响应请求时，告知浏览器（客户端）去访问另一个URL。浏览器会收到一个新的URL，并自动发起对该URL的请求，用户的浏览器地址栏会发生变化，显示新的URL。
  - 重定向可以是临时重定向（HTTP状态码 302）或者永久重定向（HTTP状态码 301）。常见的重定向方式是通过服务器返回HTTP响应头`Location`。

2. **工作原理**

- **转发**：
  - **内部处理**：请求是由Web服务器内部进行转发的，浏览器不知道请求已被转发，地址栏不会改变。
  - **请求和响应对象共享**：因为是服务器内部处理，所以原请求和原响应对象可以被共享，通常可以传递数据到下一个处理的页面。
- **重定向**：
  - **客户端操作**：重定向是浏览器收到HTTP响应后触发的，浏览器会自动去新的URL请求资源，用户会看到新的URL在浏览器地址栏中。
  - **无请求和响应对象共享**：由于重定向是通过HTTP响应的`Location`头实现的，前一个请求的响应对象和后续请求没有任何共享。

3. **影响**

- **转发**：
  - **地址栏不变**：浏览器地址栏不会改变，用户看不到请求转发到的内部页面。
  - **服务器端处理**：转发发生在Web服务器内部，不需要额外的网络请求。
- **重定向**：
  - **地址栏变化**：用户浏览器的地址栏会显示新的URL，用户能够看到它跳转的目标地址。
  - **额外的网络请求**：重定向会导致浏览器发送新的请求到新的URL，通常会增加网络开销。

4. **使用场景**

- **转发**：
  - 用于请求在同一服务器上的多个处理环节之间传递。比如一个请求经过认证后，需要转发到一个主页面进行显示，但不希望用户看到不同的URL。
  - 常用于在服务器端处理请求的过程中，如处理表单数据后将结果传递给一个不同的视图页面。
- **重定向**：
  - 用于让客户端跳转到不同的URL，通常用来改变用户请求的目标位置。比如在用户登录成功后重定向到用户的个人主页，或者在处理完某个操作后，将用户引导到另一个页面。
  - 常见的应用场景有：URL重写、路径变化、权限控制失败时重定向到登录页、301或302的页面跳转等。

5. **HTTP状态码**

- **转发**：转发没有改变HTTP状态码，通常返回200（OK）或者其他处理结果。请求的响应依旧由目标页面或资源提供。
- **重定向**：重定向会返回状态码如 301（永久重定向）或 302（临时重定向），并通过`Location`头提供新的URL。

## 第7题：什么是AJAX,主要有什么作用?

AJAX = 异步 JavaScript 和(and) XML。(*Asynchronous JavaScript and XML*)

AJAX 是一种用于创建快速动态网页的技术。

通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

传统的网页（不使用 AJAX）如果需要更新内容，必需重载整个网页面。

AJAX的工作流程 :

1. **事件触发**（如点击按钮或页面加载）。
2. **AJAX 请求**：通过 JavaScript 创建一个 `XMLHttpRequest` 对象，向服务器发送请求。
3. **服务器处理请求**：服务器（通常使用 PHP、Node.js 等）接收请求，处理并返回响应数据（JSON、XML、HTML等）。
4. **AJAX 响应处理**：浏览器接收响应，使用 JavaScript 在页面上更新内容，而无需重新加载整个页面。

# 二、Java后端工程化(28道)

## 第1题：Maven中项目聚合和继承关系的概念和作用?

在Maven中，**项目聚合（Aggregation）**和**继承（Inheritance）**是两种不同的概念，但它们在管理多模块项目时起到重要的作用。它们主要帮助我们在构建大型、复杂项目时管理模块间的关系，提高开发效率和代码的可维护性。

**Maven中的继承（Inheritance）**

Maven中的继承关系指的是通过父POM（`pom.xml`）文件来为子模块提供统一的配置和约定。父模块为多个子模块提供了共享的构建信息、插件配置、依赖管理等。

作用：

- **共享配置**：通过父POM，多个子模块可以继承相同的插件配置、版本号等，避免在每个子模块中重复配置，减少冗余。
- **版本统一**：如果父POM中指定了插件或依赖的版本号，子模块会自动继承这些版本，确保整个项目使用统一的版本，避免版本冲突。

示例：

父模块的`pom.xml`：

```xml
xml<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- Dependencies, plugin versions, etc., go here -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>5.3.9</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

子模块的`pom.xml`：

```xml
xml<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath> <!-- 指定父POM的位置 -->
    </parent>

    <artifactId>child-module</artifactId>
</project>
```

在这个例子中，`child-module`会继承父项目`parent-project`中的所有配置，包括依赖、插件和版本号。



**Maven中的聚合（Aggregation）**

Maven中的聚合关系指的是在父模块中定义多个子模块，父模块作为一个“聚合器”来统一管理和构建这些子模块。聚合关系强调的是父模块对多个子模块的组织和管理，而不是配置的继承。

作用：

- **多模块管理**：父模块通过`<modules>`标签列出所有子模块，Maven会按照父模块定义的顺序构建这些子模块。这适用于多个模块的开发需要一起构建和发布的场景。
- **统一构建**：可以通过在父模块中执行`mvn clean install`来同时构建所有子模块，而不需要分别构建每个子模块。

示例：

父模块的`pom.xml`：

```xml
xml<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <!-- 聚合模块 -->
    <modules>
        <module>child-module-1</module>
        <module>child-module-2</module>
    </modules>
</project>
```

子模块的`pom.xml`：

```xml
xml<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath>
    </parent>

    <artifactId>child-module-1</artifactId>
</project>
```

在这个例子中，父模块`parent-project`定义了两个子模块：`child-module-1`和`child-module-2`，父模块通过`<modules>`标签来聚合这些子模块。执行`mvn clean install`时，Maven会按顺序构建这些子模块。

**区别与联系**

| 特性           | 继承（Inheritance）                            | 聚合（Aggregation）                        |
| -------------- | ---------------------------------------------- | ------------------------------------------ |
| 目的           | 共享配置和构建设置                             | 管理多个子模块的构建过程                   |
| 配置继承       | 子模块继承父模块的配置（插件、依赖等）         | 父模块不影响子模块的配置，只是管理构建顺序 |
| 子模块是否独立 | 子模块继承父模块配置后，配置的修改会影响子模块 | 聚合只管理模块之间的关系，不影响配置的继承 |
| 使用场景       | 需要统一版本、插件和依赖管理的大型项目         | 需要同时构建多个模块的项目                 |

**总结**

- **继承（Inheritance）**：帮助模块继承父模块的配置，确保一致性，减少重复配置。通常用于统一版本、插件和依赖的管理。
- **聚合（Aggregation）**：父模块管理多个子模块的构建过程，允许统一构建和管理多个子模块，确保子模块按顺序构建。

两者结合使用时，可以让你更加高效地管理大型的多模块项目。

## 第2题：Maven依赖导入报错如何解决?

情况1: *.lastUpdated情况：将*.lastUpdated文件删除，重新下载。

情况2: 内部损坏情况：删除损坏的jar包重新下载。

情况3: 依赖版本不存在（Version Not Found): 检查依赖的 **版本号** 是否正确。如果该版本不存在或拼写错误，会导致 Maven 无法从中央仓库下载依赖

## 第3题：Maven依赖的Scope的四个作用域？

当一个Maven工程添加了对某个jar包的依赖后，这个被依赖的jar包可以对应下面几个可选的范围，默认是compile。

| **作用域**   | **描述**                                                     | **生命周期阶段**                           | **是否传递性** | **是否打包到产物中** |
| ------------ | ------------------------------------------------------------ | ------------------------------------------ | -------------- | -------------------- |
| **compile**  | 默认作用域，适用于所有生命周期阶段。                         | 编译、测试、运行、打包等所有阶段。         | 是             | 是                   |
| **provided** | 依赖在编译和测试阶段有效，但不会被打包到最终产物中，通常由外部容器提供。 | 编译、测试阶段有效，但运行时不包含在内。   | 否             | 否                   |
| **runtime**  | 仅在运行时有效，编译时不需要，但运行时需要该依赖。           | 仅在运行时及之后的阶段（如测试、打包）有效 | 否             | 是                   |
| **test**     | 仅在测试阶段有效，编译、运行阶段不需要该依赖。               | 仅在测试阶段有效，不打包到最终产物中。     | 否             | 否                   |

关键点

- **传递性**：只有`compile`作用域的依赖是有传递性的，其他如`provided`、`runtime`、`test`都没有传递性。
- **是否打包**：`compile`、`runtime` 会被打包到产物中；`provided`、`test`不会。

## 第4题：解释Spring IoC和DI概念以及涉及的注解有哪些?



Spring IoC（控制反转）和 DI（依赖注入）是Spring框架的核心概念，它们用于实现对象的解耦合和自动化管理。它们通过不同的方式实现了对象间的依赖关系管理，使得开发者不需要显式地创建和管理对象实例，从而提高了代码的可维护性、可测试性和扩展性。

1. **Spring IoC (Inversion of Control) — 控制反转**

   **IoC（控制反转）** 是指将对象的控制权从传统的程序代码中反转到容器中，由容器负责创建和管理对象的生命周期。这意味着，在IoC中，开发者不需要自己手动创建对象或管理对象之间的关系，而是由Spring容器来控制对象的创建、配置和管理。

   在传统的编程中，通常是开发者在代码中直接使用 `new` 操作符来创建对象和设置依赖关系。而在Spring IoC中，这个控制权交给了容器，容器通过配置文件或注解来管理对象的创建和依赖关系。

2. **DI (Dependency Injection) — 依赖注入**

   **DI（依赖注入）** 是IoC的一种实现方式，它指的是将对象所依赖的其他对象通过外部方式注入到当前对象中，而不是由对象自己去创建依赖的对象。

   DI的主要目的是实现**解耦**。通过依赖注入，类不再主动创建依赖对象，而是由容器来负责将依赖对象注入给类。这样，类的依赖关系更加明确，且能够通过容器来管理。

3. 常见的DI方式有以下几种：

   构造器注入：通过类的构造函数来注入依赖。

   Setter注入：通过设置方法（setter）来注入依赖。

   字段注入：直接通过字段（成员变量）进行注入。

Spring框架中，IoC（控制反转）和DI（依赖注入）通过使用注解可以快速实现!以下是常见的与IoC/DI相关的注解及其含义的表格：

| 注解名称         | 含义                                                       | 作用说明                                                     |
| ---------------- | ---------------------------------------------------------- | ------------------------------------------------------------ |
| `@Component`     | 通常用于标识一个类是Spring的组件                           | 标识一个类为Spring管理的bean，默认会被自动扫描并注册到Spring容器中 |
| `@Service`       | 表示一个Service类，通常用于业务层                          | `@Service`是`@Component`的衍生注解，用于标识业务层的类       |
| `@Repository`    | 用于标识数据访问层的类                                     | `@Repository`是`@Component`的衍生注解，标识持久化层的类      |
| `@Controller`    | 用于标识Web层的控制器类                                    | `@Controller`是`@Component`的衍生注解，用于Spring MVC中标识控制器 |
| `@Autowired`     | 自动注入依赖                                               | 用于自动注入Spring容器中的bean。可用于构造方法、字段或setter方法注入 |
| `@Inject`        | 与`@Autowired`类似，用于自动注入                           | 由Java提供的注解，功能与`@Autowired`类似，但它是JSR-330标准的一部分 |
| `@Qualifier`     | 配合`@Autowired`或`@Inject`使用，指定注入的具体bean        | 用于解决自动注入时存在多个候选bean的情况，明确指定需要注入哪个bean |
| `@Value`         | 用于注入外部配置（如application.properties中的值）         | 用于注入配置文件中的属性值，通常是`String`类型的值           |
| `@Configuration` | 用于定义配置类，标识该类是一个配置类                       | 用于替代传统的XML配置文件，在类中配置bean                    |
| `@Bean`          | 用于方法上，表明该方法的返回值是一个bean，Spring会将其管理 | 用于配置一个bean，通常与`@Configuration`一起使用             |
| `@Scope`         | 用于定义bean的作用域                                       | 用于指定bean的生命周期和作用范围，如`singleton`、`prototype` |
| `@PostConstruct` | 用于定义bean初始化完成后的回调方法                         | 在bean的依赖注入完成之后执行一次方法，通常用于初始化任务     |
| `@PreDestroy`    | 用于定义bean销毁前的回调方法                               | 在bean销毁之前执行的清理方法，通常用于资源释放               |
| `@Primary`       | 用于多个bean之间的首选注入                                 | 用于标识多个bean的候选者中，优先选择该bean进行注入           |

## 第5题：BeanFactory和FactoryBean的区别?

`BeanFactory` 和 `FactoryBean` 是 Spring 框架中两个相关但用途不同的概念，它们都涉及到 Spring Bean 的管理和创建，但作用和使用方式有很大的不同。

**BeanFactory**

`BeanFactory` 是 Spring 的基础容器接口，负责管理 bean 的生命周期和依赖注入。Spring 的 `ApplicationContext` 是 `BeanFactory` 的扩展，提供了更多的功能，如国际化、事件传播等。

- **延迟加载**：`BeanFactory` 默认采用懒加载（lazy initialization），也就是说，它只会在你调用 `getBean()` 时才创建相应的 bean。
- **用途**：`BeanFactory` 是 Spring 容器的基础接口，用来获取和管理 bean，虽然它提供了完整的 Bean 配置和生命周期管理功能，但它的功能相对简单，适用于不需要复杂功能的场景。

**FactoryBean**

`FactoryBean` 是一个接口，它的实现类可以控制某个 bean 的创建过程。`FactoryBean` 使得 Spring 容器中的某些 bean 的创建过程变得更加灵活，可以在 `getObject()` 方法中执行复杂的逻辑，返回不同类型的 bean 实例。

- **用法**：当你需要自定义 Bean 的创建过程时，可以使用 `FactoryBean`。例如，生成一个代理对象，或者创建一个复杂的对象实例。
- 常见用途：
  - **动态代理**：你可以通过实现 `FactoryBean` 来返回一个动态代理对象。
  - **生成复杂的 Bean**：比如返回一个通过某些外部库生成的对象。
  - **提供不同的 Bean 类型**：通过 `FactoryBean`，你可以让容器在某个配置下创建多种不同类型的 Bean。

 **重要区别总结**

- **BeanFactory** 是 Spring 框架中的容器接口，负责管理和加载 bean，它是整个 Spring IoC 容器的核心。
- **FactoryBean** 是一个 Spring 提供的特殊接口，用于定制某个 Bean 的创建过程，它本身返回的是一个对象，而不是直接作为一个 Bean 存在。
- `BeanFactory` 负责控制容器中的 bean 的生命周期和管理，而 `FactoryBean` 则提供了一种方式让开发者通过定制的工厂方法来控制 bean 的创建。
- `BeanFactory` 是一个核心接口，`FactoryBean` 是 Spring 提供的一个支持动态创建 bean 的扩展接口。

总结：

- **BeanFactory** 是整个 Spring 容器的基础，负责创建和管理所有的 bean。
- **FactoryBean** 允许自定义 bean 的创建逻辑，通常用于创建复杂或特殊类型的对象。

## 第6题：Spring的组件实例化过程(Bean的生命周期)?

在Spring框架中，Bean的生命周期是指从Bean的创建到销毁的整个过程。Spring的容器负责管理Bean的生命周期，而在这个过程中，Bean的实例化、初始化、销毁等过程都有一定的顺序和生命周期回调。

**步骤一: 加载配置（Spring容器启动)**

Spring容器启动时，会加载配置文件（如`applicationContext.xml`，或基于Java配置的`@Configuration`类）。容器读取配置并解析Bean定义（`<bean>`标签或者通过`@Bean`注解等方式定义的Bean）。

**步骤二: 实例化Bean**

Spring容器通过反射机制根据Bean的定义创建一个Bean实例。Bean的实例化是在Spring容器启动时进行的，这个过程通过`BeanDefinition`来描述Bean的类、构造方法等。

- 无参构造函数：默认情况下，Spring使用Bean的无参构造函数来实例化Bean。
- 构造器注入：如果Bean配置了构造器注入，Spring会根据`<constructor-arg>`或`@Autowired`注解的参数来调用对应的构造函数。

**步骤三: 设置Bean的属性（依赖注入）**

实例化Bean后，Spring容器会进行依赖注入。Spring会通过**Setter方法注入**（基于`@Autowired`注解或者XML配置中的`<property>`标签）或者**构造器注入**（在实例化Bean时就传入所需的依赖）来注入Bean的依赖。

**步骤四: 调用Bean的BeanPostProcessor前置处理方法**

在Bean的初始化之前，Spring会调用所有注册的`BeanPostProcessor`的`postProcessBeforeInitialization`方法。`BeanPostProcessor`是一个扩展接口，可以在Bean的初始化过程中执行一些自定义操作。

- `postProcessBeforeInitialization(Object bean, String beanName)`：在Bean初始化之前调用，可以修改Bean实例的状态。

**步骤五: 初始化Bean**

- 通过InitializingBean接口：如果Bean实现了`InitializingBean`接口，Spring会调用它的`afterPropertiesSet()`方法。
- 通过@PostConstruct注解：如果Bean类上有`@PostConstruct`注解，Spring会在Bean实例化完成后、初始化之前调用该方法。
- 通过自定义的init-method：在XML配置中，`<bean>`标签中可以配置`init-method`属性，指定一个初始化方法，这个方法会在Bean完成依赖注入后、初始化之前调用。

**步骤六: 调用Bean的BeanPostProcessor后置处理方法**

在Bean初始化之后，Spring会再次调用所有注册的`BeanPostProcessor`的`postProcessAfterInitialization`方法。

- `postProcessAfterInitialization(Object bean, String beanName)`：此方法在Bean初始化之后调用，可以对Bean进行进一步的修改或返回修改后的Bean实例。

 **步骤七: 将Bean交给应用程序使用**

经过初始化过程后，Bean已经完全配置好，Spring容器可以将该Bean交给应用程序使用。在此阶段，Bean已准备好处理实际的业务逻辑。

**步骤八: 容器销毁（销毁Bean）**

当Spring容器关闭时，它会销毁Bean。销毁的时机通常是应用程序上下文关闭时，或者通过`@PreDestroy`注解或`destroy-method`指定的方法来触发销毁。

- **通过DisposableBean接口**：如果Bean实现了`DisposableBean`接口，Spring会调用`destroy()`方法。
- **通过@PreDestroy注解**：如果Bean类上有`@PreDestroy`注解，Spring会在容器销毁时调用该方法。
- **通过自定义的destroy-method**：如果在XML配置中定义了`destroy-method`属性，Spring会在容器销毁时调用该方法。

**Bean生命周期总结**

1. **容器加载**：加载配置文件或注解配置，读取Bean定义。
2. **实例化Bean**：通过反射机制创建Bean实例。
3. **依赖注入**：设置Bean的依赖关系（属性赋值）。
4. **调用BeanPostProcessor的前置方法**：处理初始化前的操作。
5. **初始化Bean**：调用`InitializingBean`、`@PostConstruct`或自定义的初始化方法。
6. **调用BeanPostProcessor的后置方法**：处理初始化后的操作。
7. **使用Bean**：Bean准备好供应用程序使用。
8. **销毁Bean**：当容器关闭时，调用销毁方法进行清理。

**注意事项**

1. **Bean的作用域**：Spring的Bean有多种作用域（如`singleton`、`prototype`、`request`、`session`等），不同作用域的Bean在生命周期管理上有所不同。例如，`prototype`作用域的Bean每次请求都会重新创建，而`singleton`作用域的Bean则只会被创建一次。
2. **延迟加载**：如果Bean设置为懒加载（`lazy-init="true"`），Spring容器不会在启动时立即实例化该Bean，而是等到真正使用该Bean时才实例化它。
3. **AOP代理**：如果Bean使用了AOP代理，Spring会在Bean的生命周期中插入AOP逻辑，影响Bean的方法执行顺序。

通过上述步骤，Spring实现了对Bean的生命周期进行细粒度的控制，确保在每个阶段都能够进行必要的配置、初始化、销毁等操作。

## 第7题：Spring的组件作用域有哪些?

Spring框架支持如下五种不同的作用域：

**singleton**：在Spring IOC容器中仅存在一个Bean实例，Bean以单实例的方式存在(默认)。

prototype：一个bean可以定义多个实例。

Web容器下

request：每次HTTP请求都会创建一个新的Bean。该作用域仅适用于WebApplicationContext环境。

session：一个HTTP Session定义一个Bean。该作用域仅适用于WebApplicationContext环境。

globalSession：同一个全局HTTP Session定义一个Bean。该作用域同样仅适用于WebApplicationContext环境。bean默认的scope属性是"singleton"。

## 第8题：Spring怎么解决循环依赖问题?（10分钟时间，什么样的循环依赖组件无法解决！！）

 **简单概括循环依赖问题:**

- A对象创建时需要注入B对象；
- B对象创建时需要注入A对象；

当Spring容器初始化这两个bean时，会出现一个问题：Spring容器不知道从哪里开始实例化。为了避免这种死锁，Spring提供了特定的机制来解决循环依赖。

**循环依赖问题解决机制:**

Spring 容器采用了**三级缓存机制**来解决循环依赖。

- **一级缓存（singletonObjects）**：存放已经完全实例化的bean。
- **二级缓存（earlySingletonObjects）**：存放提前暴露的bean，这些bean并未完全初始化完成，但为了避免循环依赖，Spring会将其提前暴露。
- **三级缓存（singletonFactories）**：存放Bean的创建工厂，这些工厂在需要时才会创建bean。

**使用案例：A 和 B 的循环依赖说明三级缓存步骤**

假设两个 Bean：`A` 和 `B` 互相依赖（通过字段注入），且 `A` 需要被 AOP 代理。

**流程步骤：**

1. ‌**创建 Bean A**‌：
   - 实例化 `A`（调用构造函数），此时 `A` 还未初始化。
   - 将 `A` 的工厂对象（`ObjectFactory`）放入 ‌**三级缓存**‌。
   - 开始填充 `A` 的依赖属性，发现需要注入 `B`。
2. ‌**创建 Bean B**‌：
   - 实例化 `B`（调用构造函数），此时 `B` 还未初始化。
   - 将 `B` 的工厂对象（`ObjectFactory`）放入 ‌**三级缓存**‌。
   - 开始填充 `B` 的依赖属性，发现需要注入 `A`。
3. ‌**解决 A 的依赖**‌：
   - 从 ‌**一级缓存**‌ 查找 `A`（未找到）。
   - 从 ‌**二级缓存**‌ 查找 `A`（未找到）。
   - 从 ‌**三级缓存**‌ 找到 `A` 的工厂对象，调用 `getObject()` 生成早期引用（可能是代理对象）。
   - 将 `A` 的早期引用放入 ‌**二级缓存**‌，并清除 ‌**三级缓存**‌ 中的 `A` 工厂。
   - 将 `A` 的早期引用注入到 `B` 中，完成 `B` 的初始化。
   - 将 `B` 放入 ‌**一级缓存**‌，并清除其三级缓存记录。
4. ‌**完成 A 的初始化**‌：
   - `A` 的 `B` 依赖已解决，继续初始化其他逻辑。
   - 完成 `A` 的初始化后，将 `A` 从 ‌**二级缓存**‌ 移到 ‌**一级缓存**‌。

关键点：

- ‌**三级缓存的作用**‌：通过工厂延迟生成代理对象（解决 AOP 的代理问题）。
- ‌**二级缓存的作用**‌：避免重复生成代理对象（确保多次依赖注入时引用一致）。
- ‌**一级缓存的作用**‌：最终可用的 Bean。

为什么需要三级缓存？

- 如果只有二级缓存，无法处理需要动态代理的场景（工厂对象是生成代理的关键）。
- ‌**示例**‌：若 `A` 需要被代理，直接在二级缓存中存储原始对象会导致后续流程中代理对象和原始对象不一致。

不适用场景：

- ‌**构造器注入的循环依赖**‌：无法通过三级缓存解决，因为 Bean 实例化前无法暴露引用。
- ‌**原型（Prototype）作用域的 Bean**‌：Spring 不管理原型 Bean 的生命周期，无法解决循环依赖。

通过三级缓存，Spring 在保证性能的同时，优雅地解决了单例 Bean 的循环依赖问题。

## 第9题：Spring中的单例Bean的线程安全问题？

Spring中的Bean默认是单例模式的，框架并没有对bean进行多线程的封装处理。

如果Bean是有状态的 那就需要开发人员自己来进行线程安全的保证，最简单的办法就是改变bean的作用域把 "singleton"改为"protopyte" 这样每次请求Bean就相当于是 new Bean() 这样就可以保证线程的安全了。

有状态就是有数据存储功能。无状态就是不会保存数据 controller、service和dao层本身并不是线程安全的，只是如果只是调用里面的方法，而且多线程调用一个实例的方法，会在内存中复制变量，这是自己的线程的工作内存，是安全的。

Dao会操作数据库Connection，Connection是带有状态的，比如说数据库事务，Spring的事务管理器使用Threadlocal为不同线程维护了一套独立的connection副本，保证线程之间不会互相影响（Spring是如何保证事务获取同一个Connection的）不要在bean中声明任何有状态的实例变量或类变量，如果必须如此，那么就使用ThreadLocal把变量变为线程私有的，如果bean的实例变量或类变量需要在多个线程之间共享，那么就只能使用synchronized、lock、CAS等这些实现线程同步的方法了。

## 第10题：谈谈你对AOP的理解,以及AOP的底层实现原理?

**AOP（Aspect-Oriented Programming）** 是一种编程范式，它通过关注横切关注点（cross-cutting concerns）来增强面向对象编程（OOP）的能力。它将程序中的一些关心点（比如日志记录、安全控制、事务管理等）从核心业务逻辑中抽离出来，从而使得核心业务逻辑更加简洁、易于维护，并且可以在不修改原有代码的情况下进行增强。

**AOP的核心概念**

1. **切面（Aspect）**： 切面是横切关注点的模块化，包含一个或多个“通知”和“连接点”。它是AOP的主要组成部分，主要用于封装切入逻辑。
2. **连接点（Joinpoint）**： 连接点是程序执行过程中一个特定的点，可以是方法的调用、方法的执行、构造函数的调用等。通常，连接点指的是一个方法的执行点。
3. **通知（Advice）**： 通知是切面要做的具体动作，它在连接点上执行，具体分为前置通知、后置通知、异常通知、环绕通知等。例如，在方法执行前后插入日志、权限检查等操作。
4. **切入点（Pointcut）**： 切入点是一个表达式，指定了切面应用到哪些连接点上。它通常用于选择哪些方法或哪些方法的特定行为需要被拦截。
5. **织入（Weaving）**： 织入是指将切面应用到目标对象的方法的过程。织入可以发生在编译期、类加载期、运行期等不同阶段，具体的织入机制取决于AOP框架的实现。
6. **目标对象（Target Object）**： 目标对象是指被切面增强的原始对象，也就是业务对象。通常，在面向对象的应用中，目标对象是业务逻辑的实现者。
7. **代理对象（Proxy Object）**： 代理对象是通过AOP机制生成的代理，代理对象会将方法调用委派给目标对象，同时在调用前后或环绕调用中插入增强逻辑。

**AOP的底层实现原理**

AOP的底层实现依赖于一种代理机制。AOP的核心思想是通过代理模式（Proxy Pattern）来在方法调用前、后，或者方法执行过程中插入特定的逻辑，从而实现横切关注点的“切入”。实现AOP的技术可以分为两种主要类型：**基于代理的实现**和**基于字节码操作的实现**。

**1. 基于代理的实现**

AOP框架通常会通过动态代理机制来实现，常见的代理机制有：

- **JDK动态代理**： JDK动态代理是通过Java的反射机制实现的，它可以对实现了接口的类进行代理。代理类会拦截对目标对象方法的调用，并根据切面逻辑进行增强。JDK动态代理只能针对接口进行代理。
- **CGLIB（Code Generation Library）代理**： CGLIB是一个字节码生成库，它通过继承目标类来生成代理对象，而不要求目标类实现接口。CGLIB代理是通过修改类的字节码来生成代理类。CGLIB适用于没有接口的类，但因为是继承，所以目标类的方法必须是非`final`的。
- **JDK + CGLIB混合**： 在实际的AOP实现中，很多框架（如Spring AOP）会根据目标对象的类型，选择使用JDK动态代理或者CGLIB代理。如果目标对象实现了接口，则使用JDK动态代理；如果没有实现接口，则使用CGLIB代理。

**2.基于字节码操作的实现**

在基于代理的AOP实现中，代理对象的创建是通过反射或字节码操作来实现的。在这种情况下，代理对象通常会在运行时动态生成。例如，Spring AOP在JDK动态代理和CGLIB代理之间选择时，通常会使用字节码生成技术来动态创建代理类。

- **ASM和Javassist**： 这些是常用的Java字节码操作库，它们允许我们在运行时修改类的字节码，从而实现动态增强。通过这种方式，可以直接修改目标类的字节码，在目标方法的执行过程中插入特定的通知逻辑。
- **Spring AOP**： Spring AOP基于JDK动态代理和CGLIB两种方式实现。在运行时，Spring会根据目标对象是否实现了接口来选择使用哪种代理方式。如果实现了接口，使用JDK动态代理；如果没有接口，使用CGLIB代理。

**Spring AOP的实现**

Spring AOP是基于JDK动态代理和CGLIB代理的实现。在Spring AOP中，织入发生在运行时。Spring通过代理方式来将切面增强逻辑插入到目标对象的方法执行前后。Spring AOP框架的实现基于以下几个步骤：

- **配置切面**：通过注解（如`@Aspect`）或XML配置来定义切面类和通知类型（如`@Before`、`@After`、`@Around`等）。
- **选择切入点**：通过切入点表达式（如`execution(* com.example.service.*.*(..))`）来指定哪些方法需要被切面增强。
- **织入和代理**：Spring AOP会创建代理对象，并将切面逻辑与目标对象结合。Spring根据目标对象是否实现接口来决定使用JDK动态代理还是CGLIB代理。

AOP的底层实现原理主要是通过代理模式来实现对方法调用的拦截，在目标方法执行的前后插入增强逻辑。具体实现可以基于JDK动态代理、CGLIB字节码代理或其他字节码操作技术，织入发生在编译期、类加载期或运行期。通过这些技术，AOP能够实现将横切关注点从核心业务逻辑中分离出来，提高代码的模块化和可维护性。

## 第11题：AOP涉及的具体注解有哪些?

AOP（面向切面编程，Aspect-Oriented Programming）是 Spring 框架中非常重要的概念之一，它允许通过关注横切关注点（如日志、事务管理、安全控制等）来增强应用程序的功能，而不修改核心业务逻辑。在 Spring AOP 中，常用的注解有以下几个。下面是这些注解的简要说明，并通过表格的形式进行整理：

| **注解**          | **描述**                                                     |
| ----------------- | ------------------------------------------------------------ |
| `@Aspect`         | 用于定义切面类（Aspect），表示该类是一个切面类，其中包含了切面逻辑。 |
| `@Before`         | 用于定义一个方法，该方法在目标方法执行之前执行（前置通知）。 |
| `@After`          | 用于定义一个方法，该方法在目标方法执行后执行（后置通知），无论目标方法执行是否异常都会执行该通知。 |
| `@AfterReturning` | 用于定义一个方法，该方法在目标方法成功执行（没有抛出异常）后执行。可以通过它获取目标方法的返回值。 |
| `@AfterThrowing`  | 用于定义一个方法，该方法在目标方法抛出异常时执行。可以通过它获取异常信息。 |
| `@Around`         | 用于定义一个方法，该方法在目标方法执行前后执行（环绕通知）。它可以控制目标方法是否执行，并可以在目标方法执行前后执行一些自定义逻辑。 |
| `@Pointcut`       | 用于定义切入点（Pointcut），即指定在哪些方法上应用切面。它可以结合其他注解如 `@Before`、`@After` 等来使用，表示在哪些方法执行时进行增强。 |

## 第12题：Spring 框架中用到了哪些设计模式？

**工厂设计模式**

由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类Spring中的BeanFactory就是简单工厂模式的体现，根据传入一个唯一的标识来获得Bean对象，但是否是在传入参数后创建还是传入参数前创建这个要根据具体情况来定。

实现了FactoryBean接口的bean是一类叫做factory的bean。其特点是，Spring会在使用getBean()调用获得该bean时，会自动调用该bean的getObject()方法，所以返回的不是factory这个bean，而是这个bean.getOjbect()方法的返回值。

**单例模式**

保证一个类仅有一个实例，并提供一个访问它的全局访问点。Spring对单例的实现： Spring中的单例模式完成了后半句话，即提供了全局的访问点BeanFactory。

但没有从构造器级别去控制单例，这是因为Spring管理的是任意的java对象。

**适配器模式**

Spring定义了一个适配接口，使得每一种Controller有一种对应的适配器实现类，让适配器代替Controller执行相应的方法。

这样在扩展Controller时，只需要增加一个适配器类就完成了SpringMVC的扩展了。

**装饰器模式**

动态地给一个对象添加一些额外的职责。就增加功能来说，Decorator模式相比生成子类更为灵活。

Spring中用到的装饰器模式在类名上有两种表现：一种是类名中含有Wrapper，另一种是类名中含有Decorator。

**代理模式**

切面在应用运行的时刻被织入。一般情况下，在织入切面时，AOP容器会为目标对象创建动态的创建一个代理对象。SpringAOP就是以这种方式织入切面的。

> 织入：把切面应用到目标对象并创建新的代理对象的过程。

**观察者模式**

Spring的事件驱动模型使用的是观察者模式 ，Spring中Observer模式常用的地方是listener的实现。

**策略模式**

Spring框架的资源访问Resource接口。该接口提供了更强的资源访问能力，Spring 框架本身大量使用了Resource 接口来访问底层资源。

**模板方法模式**

父类定义了骨架（调用哪些方法及顺序），某些特定方法由子类实现Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。

## 第13题：Spring 事务实现方式有哪些以及原理!

Spring 提供了灵活且易用的事务管理机制，支持编程式和声明式两种方式。推荐使用声明式事务管理，因为它简化了代码并减少了耦合性。通过 AOP 技术，Spring 能够透明地管理事务，而无需开发者手动控制事务的开始和结束。同时，事务传播行为、隔离级别和事务管理器的选择使得 Spring 能够在不同的应用场景下灵活处理事务。

**编程式事务管理**

这意味着你可以通过编程的方式管理事务，这种方式带来了很大的灵活性，但很难维护。

 **声明式事务管理** （spring-tx）

这种方式意味着你可以将事务管理和业务代码分离。你只需要通过注解或者XML配置管理事务。@Transactional注解就是声明式事务。

首先，事务这个概念是数据库层面的，Spring只是基于数据库中的事务进行了扩展，以及提供了一些能让程序员更加方便操作事务的方式。

比如我们可以通过在某个方法上增加@Transactional注解，就可以开启事务，这个方法中所有的SQL都会在一个事务中执行，统一成功或失败。

在一个方法上加了@Transactional注解后，Spring会基于这个类生成一个代理对象，会将这个代理对象作为bean，当在使用这个代理对象的方法时，如果这个方法上存在@Transactional注解，那么代理逻辑会先把事务的自动提交设置为false，然后再去执行原本的业务逻辑方法，如果执行业务逻辑方法没有出现异常，那么代理逻辑中就会将事务进行提交，如果执行业务逻辑方法出现了异常，那么则会将事务进行回滚。

当然，针对哪些异常回滚事务是可以配置的，可以利用@Transactional注解中的rollbackFor属性进行配置，默认情况下会对RuntimeException和Error进行回滚。

## 第14题：Spring事务定义的传播规则!

多个事务方法相互调用时,事务如何在这些方法间传播。

方法A是一个事务的方法，方法A执行过程中调用了方法B，那么方法B有无事务以及方法B对事务的要求不同都会对方法A的事务具体执行造成影响，同时方法A的事务对方法B的事务执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型所决定。

REQUIRED( Spring默认的事务传播类型)：如果当前没有事务，则自己新建一个事务，如果当前存在事务，则加入这个事务。

SUPPORTS：当前存在事务，则加入当前事务，如果当前没有事务，就以非事务方法执行。

MANDATORY：当前存在事务，则加入当前事务，如果当前事务不存在，则抛出异常。

REQUIRES_NEW：创建一个新事务，如果存在当前事务，则挂起该事务。

NOT_SUPPORTED：以非事务方式执行,如果当前存在事务，则挂起当前事务。

NEVER：不使用事务，如果当前事务存在，则抛出异常。

NESTED：如果当前事务存在，则在嵌套事务中执行，否则REQUIRED的操作一样（开启一个事务）。

NESTED和REQUIRES_NEW的区别

REQUIRES_NEW是新建一个事务并且新开启的这个事务与原有事务无关，而NESTED则是当前存在事务时（我们把当前事务称之为父事务）会开启一个嵌套事务（称之为一个子事务）。

在NESTED情况下父事务回滚时，子事务也会回滚，而在REQUIRES_NEW情况下，原有事务回滚，不会影响新开启的事务。

NESTED和REQUIRED的区别REQUIRED情况下，调用方存在事务时，则被调用方和调用方使用同一事务，那么被调用方出现异常时，由于共用一个事务，所以无论调用方是否catch其异常，事务都会回滚 而在NESTED情况下，被调用方发生异常时，调用方可以catch其异常，这样只有子事务回滚，父事务不受影响

## 第15题：Spring事务什么时候会失效?

Spring事务的原理是AOP，进行了切面增强，那么失效的根本原因是这个AOP不起作用了！常见情况有如 下几种

1、发生自调用，类里面使用this调用本类的方法（this通常省略），此时这个this对象不是代理类，而是该类的实例对象本身！解决方法很简单，让那个this变成该类的的代理类实例对象即可！

2、方法是private final修饰

3、数据库不支持事务

4、没有被Spring管理

5、异常被吃掉，事务不会回滚(或者抛出的异常没有被定义，默认为RuntimeException) rollbackfor = exception.class

## 第16题：SpringMVC常用的注解

以下是 SpringMVC 中常用的注解列表：

| 注解                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| `@Controller`        | 标记一个类为 Spring MVC 控制器，用于处理请求。               |
| `@RestController`    | 结合了 `@Controller` 和 `@ResponseBody`，用于创建 RESTful Web 服务的控制器。 |
| `@RequestMapping`    | 映射 HTTP 请求到方法上，支持多种 HTTP 方法（GET、POST、PUT 等）。 |
| `@GetMapping`        | 映射 HTTP GET 请求到方法上（是 `@RequestMapping` 的快捷方式）。 |
| `@PostMapping`       | 映射 HTTP POST 请求到方法上（是 `@RequestMapping` 的快捷方式）。 |
| `@PutMapping`        | 映射 HTTP PUT 请求到方法上（是 `@RequestMapping` 的快捷方式）。 |
| `@DeleteMapping`     | 映射 HTTP DELETE 请求到方法上（是 `@RequestMapping` 的快捷方式）。 |
| `@PatchMapping`      | 映射 HTTP PATCH 请求到方法上（是 `@RequestMapping` 的快捷方式）。 |
| `@RequestParam`      | 从请求参数中获取值，通常用于查询字符串或表单数据。           |
| `@PathVariable`      | 用于获取 URL 模板中的变量，通常与 RESTful 风格的 URL 一起使用。 |
| `@RequestBody`       | 将 HTTP 请求体中的内容绑定到方法的参数上，通常用于处理 JSON 或 XML 数据。 |
| `@ResponseBody`      | 将方法的返回值直接绑定到 HTTP 响应体，常用于返回 JSON 或 XML 数据。 |
| `@ModelAttribute`    | 用于将请求参数绑定到方法的参数对象上，常用于表单提交。       |
| `@SessionAttributes` | 将模型属性存储到 HTTP 会话中，可以在多个请求之间共享数据。   |
| `@InitBinder`        | 初始化数据绑定器，用于自定义请求参数与 JavaBean 属性的绑定方式。 |
| `@ExceptionHandler`  | 用于定义异常处理方法，捕获控制器中的异常。                   |
| `@CrossOrigin`       | 启用跨域请求，允许指定的来源进行访问。                       |
| `@Valid`             | 用于验证方法参数，通常与 `@RequestBody` 或 `@ModelAttribute` 一起使用，进行数据校验。 |
| `@Validated`         | 用于启用 JSR-303/JSR-380 验证机制，通常与 `@Valid` 配合使用。 |

这些注解在 SpringMVC 开发中非常常见，能够帮助开发者方便地进行请求映射、数据绑定、响应处理等。

## 第17题：说一说SpringMVC 工作原理!

**流程说明（重要）：**

1) 用户发送请求至前端控制器 DispatcherServlet。

2）DispatcherServlet 收到请求调用 HandlerMapping 处理器映射器。

3）处理器映射器找到具体的处理器(可以根据 xml 配置、注解进行查找)，生成处理器及处理器拦截器(如果有则生成)一并返回给 DispatcherServlet。

4）DispatcherServlet 调用 HandlerAdapter 处理器适配器。

5）HandlerAdapter 经过适配调用具体的处理器(Controller，也叫后端控制器)

6）Controller 执行完成返回 ModelAndView。

7）HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet。

8）DispatcherServlet 将 ModelAndView 传给 ViewReslover 视图解析器。

9）ViewReslover 解析后返回具体 View。

10）DispatcherServlet 根据 View 进行渲染视图（即将模型数据填充至视图中）。

11）DispatcherServlet 响应用户。

**流程图解:**

![1736324716815](images/1736324716815.png)

## 第18题：SpringMVC拦截器和JavaWebFilter的区别?

SpringMVC 拦截器（`HandlerInterceptor`）和 Java Web 过滤器（`Filter`）都是用来拦截和处理 HTTP 请求的机制，但它们在功能、使用场景和执行时机等方面有所不同。以下是两者的主要区别：

 **定义和所属框架**

- SpringMVC 拦截器：
  - 属于 SpringMVC 框架的一部分，用于在 SpringMVC 的请求处理流程中对请求进行预处理、后处理等。
  - 主要用于控制器方法调用前后进行一些操作，属于 Spring Web 模块。
- Java Web 过滤器：
  - 属于 Java EE 的 Servlet 规范，是 Web 应用中的一个标准机制。
  - 它在 Servlet 容器层面起作用，可以拦截和处理所有的 HTTP 请求和响应。过滤器不仅限于 Spring，任何基于 Servlet 的 Web 应用都可以使用过滤器。

**执行时机**

- SpringMVC 拦截器：
  - 执行顺序主要发生在 SpringMVC 的请求处理流程中。它可以在控制器方法调用之前（`preHandle`）、控制器方法执行之后（`postHandle`），以及请求处理完成后（`afterCompletion`）执行特定操作。
  - 执行时机具体如下：
    - `preHandle()`: 在处理请求之前调用。
    - `postHandle()`: 在处理请求之后调用，但在视图渲染之前。
    - `afterCompletion()`: 在请求处理完成之后调用，通常用于清理资源。
- Java Web 过滤器：
  - 过滤器是在请求到达 Servlet 前，或者响应离开 Servlet 后，进行处理。过滤器处理的生命周期更广泛，作用于所有的 HTTP 请求，不仅限于 SpringMVC 的控制器方法。
  - 执行顺序主要体现在过滤器链中：
    - `doFilter()`: 在请求到达 Servlet 之前或响应离开 Servlet 之后调用。

**作用范围**

- SpringMVC 拦截器：
  - 只作用于 SpringMVC 的请求处理流程，特别是控制器的调用（`@Controller` 或 `@RestController`）。
  - 只能拦截 SpringMVC 相关的请求，无法拦截静态资源或其他非 SpringMVC 处理的请求。
- Java Web 过滤器：
  - 过滤器作用于所有的 HTTP 请求和响应，包括 SpringMVC 和其他 Servlet（例如静态资源等）。
  - 可以跨越多个框架和技术栈，因此过滤器的作用范围更广。

**配置方式**

- SpringMVC 拦截器：
  - 配置在 Spring 的 `DispatcherServlet` 中，通常通过 Java 配置类或 `spring-mvc.xml` 中的 `<mvc:interceptors>` 标签进行配置。
- Java Web 过滤器：
  - 配置在 `web.xml` 中，或者通过 Java 配置（在 Servlet 3.0 及以后版本中支持）使用 `@WebFilter` 注解进行配置。

**总结对比表**

| 特性                 | SpringMVC 拦截器                                             | Java Web 过滤器                                       |
| -------------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| **所属框架**         | SpringMVC                                                    | Java EE / Servlet                                     |
| **功能范围**         | 仅限于 SpringMVC 请求和控制器                                | Web 应用的所有请求和响应（包括 SpringMVC 和静态资源） |
| **执行时机**         | 处理请求之前（preHandle）、处理后（postHandle）、完成后（afterCompletion） | 请求到达 Servlet 之前或响应离开 Servlet 后            |
| **配置方式**         | 通过 Spring 配置（Java 或 XML）                              | 通过 `web.xml` 或 `@WebFilter` 注解配置               |
| **访问 Spring 特性** | 可以直接访问 Spring 特性                                     | 无法直接访问，需要额外配置                            |
| **应用场景**         | 请求日志、权限验证、控制器级别的增强                         | 全局请求日志、跨域支持、字符编码、静态资源压缩等      |
| **适用范围**         | 只对 SpringMVC 控制器生效                                    | 全局有效，包含所有请求和响应                          |

## 第19题：Mybatis中#和$的区别？

在 MyBatis 中，`#` 和 `$` 都是用来传递参数到 SQL 语句中的占位符，但它们有很大的区别，主要体现在参数的处理方式上。下面是它们的详细区别：

1. `#`（安全占位符）

- **作用：** `#` 是 MyBatis 中的预编译占位符，用来防止 SQL 注入攻击。它会将传入的参数值作为一个字面量值，并且自动处理类型转换和参数的转义。
- **SQL 生成：** MyBatis 会将参数值转义后插入到 SQL 语句中，并且确保其类型是安全的。也就是说，如果传入的参数是字符串类型，MyBatis 会自动加上单引号，避免 SQL 注入问题。
- **应用场景：** 用于传递普通的参数值，如字符串、数字等。

2. `$`（直接替换）

- **作用：** `$` 是 MyBatis 中的直接替换占位符，它将直接将传入的参数值插入到 SQL 语句中，不做任何转义或预处理。因此，使用 `$` 时必须小心，以防止 SQL 注入问题。
- **SQL 生成：** MyBatis 会直接将参数值插入到 SQL 中，因此不会对字符串值进行任何转义。例如，如果参数是一个字段名或者表名时，才适合使用 `$`。
- **应用场景：** 用于动态 SQL 中需要替换的表名、列名等数据库对象，或者其他的无须处理转义的常量。

**总结：**

- **#**：用于参数化查询，自动处理参数的类型转换和转义，防止 SQL 注入，适用于大多数情况。
- **$**：用于直接替换 SQL 语句中的字面值，适用于表名、列名等数据库对象的动态替换，但需要非常小心，防止 SQL 注入。

通常，**推荐使用 #**，除非你非常明确知道传入的内容是安全的，并且在动态构建表名或列名时才考虑使用 `$`。

## 第20题：Mybatis中一级缓存与二级缓存？

在 MyBatis 中，一级缓存和二级缓存是两种不同级别的缓存机制，用于提高数据库操作的效率。它们之间的主要区别在于缓存的范围、生命周期和作用域。下面详细介绍这两种缓存机制：

1. **一级缓存（Per-Session Cache）**

   **定义：**

   一级缓存是 MyBatis 默认开启的缓存机制，它是基于 **SqlSession** 级别的缓存。每个 `SqlSession` 对象都有自己的一级缓存，存储在内存中。

   当同一个 `SqlSession` 执行相同的查询时，如果查询条件和结果没有发生变化（且多次查询之间没有DML语句发生），MyBatis 会直接从一级缓存中取出数据，而不需要再查询数据库。

   **生命周期：**

   一级缓存的生命周期与 `SqlSession` 的生命周期相同。当 `SqlSession` 被关闭时，一级缓存会被清空。

   **作用域：**

   只在当前 `SqlSession` 内有效。

   同一个 `SqlSession` 内，如果执行相同的查询语句，MyBatis 会自动缓存查询结果。

   如果是不同的 `SqlSession`，则不会共享缓存。

   **特点：**

   自动开启：MyBatis 默认开启一级缓存。

   同一会话内有效：同一 `SqlSession` 内的多次查询会使用缓存，但不同 `SqlSession` 之间的数据是独立的。

   线程安全：由于每个 `SqlSession` 只有一个线程，因此一级缓存本身是线程安全的。

   **使用场景：**

   一级缓存适用于同一 `SqlSession` 内频繁访问相同数据的场景，通过避免多次查询数据库来提高性能。

   **示例：**

   ```java
   javaSqlSession session = sqlSessionFactory.openSession();
   User user1 = session.selectOne("selectUserById", 1);
   // 第二次查询会直接从一级缓存中获取数据,不会再次查询数据库
   User user2 = session.selectOne("selectUserById", 1);  
   session.close();
   ```

2. 二级缓存（Global Cache）

   **定义：**

   二级缓存是 MyBatis 提供的跨 `SqlSession` 的缓存机制。二级缓存是 **跨会话** 的缓存，它的作用范围不仅限于一个 `SqlSession`，而是共享给所有使用相同 `SqlSessionFactory` 的 `SqlSession` 实例。

   **生命周期：**

   二级缓存的生命周期由配置文件中的设置决定，通常在 **SqlSessionFactory** 的生命周期内有效。每次关闭 `SqlSessionFactory` 或者清空二级缓存时，缓存数据都会被清除。

   **作用域：**

   共享给所有使用同一个 `SqlSessionFactory` 的 `SqlSession`。

   不同 `SqlSession` 之间可以共享二级缓存。

   **特点：**

   需要显式配置：二级缓存默认是关闭的，必须显式配置启用。

   跨 SqlSession 有效：一个 `SqlSession` 中的查询结果可以被其他 `SqlSession` 复用。

   线程安全问题：由于二级缓存是全局共享的，因此要特别注意并发问题，MyBatis 提供了相关的机制来保证线程安全。

   **配置二级缓存：**

   1. **在 mybatis-config.xml 配置二级缓存**：

      ```xml
      <configuration>
          <settings>
              <setting name="cacheEnabled" value="true"/>
          </settings>
      </configuration>
      ```

   2. **在映射文件中为特定的 Mapper 开启缓存**：

      ```xml
      <mapper namespace="com.example.mapper.UserMapper">
          <cache/>
          <!-- 映射语句 -->
      </mapper>
      ```

   3. **在 SqlSessionFactory 中配置 Cache 实现**，MyBatis 默认使用内存缓存实现（可以扩展为其他存储，比如 Redis、EhCache 等）。

   **使用场景：**

   二级缓存适用于跨会话缓存数据的场景。例如，一个用户在登录后修改了某个信息，之后其他会话可以从二级缓存中获取到最新的数据，从而减少对数据库的访问。

   **示例：**

   ``` java
   javaSqlSession session1 = sqlSessionFactory.openSession();
   User user1 = session1.selectOne("selectUserById", 1);
   session1.close();  // 一级缓存无效
   
   SqlSession session2 = sqlSessionFactory.openSession();
   User user2 = session2.selectOne("selectUserById", 1);  // 第二次查询从二级缓存中获取
   session2.close();
   ```

**总结：**

- **一级缓存**：是基于 `SqlSession` 的缓存，默认启用，适用于同一 `SqlSession` 内的多次查询。
- **二级缓存**：是跨 `SqlSession` 的缓存，默认关闭，需要显式配置，适用于跨会话的缓存共享。

在使用 MyBatis 时，合理地配置和使用一级缓存与二级缓存，可以显著提高数据库操作的性能，尤其是在高并发和频繁查询的场景下。

## 第21题：简述 Mybatis 的插件运行原理，如何编写一个插件!

MyBatis 插件的运行原理基于Java的动态代理机制。Mybatis 只支持针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor 这4 种接口的插件， Mybatis 使用 JDK 的动态代理， 为需要拦截的接口生成代理对象以实现接口方法拦截功能， 每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的invoke() 方法， 拦截那些你指定需要拦截的方法。

MyBatis自定义插件的实现方式主要包括以下几个步骤：

1. 创建插件类：首先，需要创建一个Java类来表示自定义插件。这个类需要实现MyBatis提供的 Interceptor 接口，并重写其中的 intercept() 方法。在这个方法中，可以编写自定义的拦截逻辑。
2. 配置插件：在MyBatis的全局配置文件（mybatis-config.xml）中，通过 <plugins> 标签定义和加载插件。在 <plugins> 标签内部，使用 <plugin> 标签指定自定义插件的全限定类名。
3. 注册插件：在MyBatis的 Configuration 配置对象中，将实现了 Interceptor 接口的插件进行注册。这样MyBatis就能够识别并使用这些插件。
4. 构建拦截器链：MyBatis基于责任链设计模式，将多个拦截器串联起来，形成一个拦截器链。当核心组件的方法被调用时，会依次经过每个拦截器的处理。
5. 动态代理机制：MyBatis利用Java的动态代理技术，对 ParameterHandler 、ResultSetHandler 、 StatementHandler 和 Executor 这四种核心接口创建代理对象。当这些组件的方法被调用时，会先执行拦截器链中的代码，再执行原始方法。
6. 插件执行：当MyBatis的核心组件执行方法时，会触发拦截器的 intercept() 方法。在这个方法中，可以实现对SQL语句的修改、日志打印、性能监控等功能。执行完拦截逻辑后，可以选择是否继续执行原始方法。
7. 完成插件功能：通过上述步骤，自定义插件完成了对MyBatis功能的增强或修改。开发者可以根据需要调整插件的行为，以实现特定的业务需求。

## 第22题：讲述下MyBatis多表映射中association与collection区别

在 MyBatis 的多表映射中，`association` 和 `collection` 都是用于处理一对一和一对多的关系映射的关键元素，我们详细讨论这两者的区别：

**1. `association` — 一对一关系映射**

`association` 用于表示 **一对一**（one-to-one）关系的映射。它通常用于在查询时将一个对象关联到另一个对象。例如，在一个 `User` 对象中，可能有一个 `Address` 对象来表示用户的地址信息。这个关系在数据库表中通常通过外键来实现。

**示例**： 假设有两个表：`users` 和 `addresses`，其中 `users` 表有一个字段 `address_id`，指向 `addresses` 表的 `id` 字段，表示每个用户对应一个地址。

```xml
<resultMap id="userResultMap" type="User">
    <id property="id" column="id"/>
    <result property="name" column="name"/>
    <!-- 一对一关联 -->
    <association property="address" column="address_id" javaType="Address" select="selectAddressById"/>
</resultMap>
```

在这个例子中，`association` 映射了 `User` 对象中的 `address` 属性与 `Address` 对象之间的一对一关系。`selectAddressById` 是一个查询语句，用来根据 `address_id` 获取用户的地址信息。

**2. `collection` — 一对多关系映射**

`collection` 用于表示 **一对多**（one-to-many）关系的映射。它通常用于将一个对象关联到多个对象。例如，一个 `User` 对象可能有多个 `Order` 对象，表示用户的多个订单。`collection` 用来映射这种一对多关系。

**示例**： 假设有两个表：`users` 和 `orders`，其中 `orders` 表有一个字段 `user_id`，指向 `users` 表的 `id` 字段，表示每个订单属于一个用户。

```
<resultMap id="userResultMap" type="User">
    <id property="id" column="id"/>
    <result property="name" column="name"/>
    <!-- 一对多关联 -->
    <collection property="orders" ofType="Order" column="id" select="selectOrdersByUserId"/>
</resultMap>
```

在这个例子中，`collection` 映射了 `User` 对象中的 `orders` 属性与多个 `Order` 对象之间的一对多关系。`selectOrdersByUserId` 是一个查询语句，用来获取指定 `user_id` 下的所有订单。

**总结**

- `association` 用于 **一对一关系**，它把关联的表中的一条记录映射到一个对象。
- `collection` 用于 **一对多关系**，它把关联的表中的多条记录映射到一个对象的集合。

这两者在实际应用中都非常重要，能够帮助我们灵活地映射多表数据，提升 MyBatis 的使用效率和灵活性。

## 第23题：请写出Mybatis中常用的动态标签，并简单介绍其作用？

**if 标签**

if 标签通常用于 WHERE 语句、UPDATE 语句、INSERT 语句中，通过判断参数值来决定是否使用某个查询条件、判断是否更新某一个字段、判断是否插入某个字段的值。

**foreach 标签**

foreach 标签主要用于构建 in 条件，可在 sql 中对集合进行迭代。也常用到批量删除、添加等操作中。

**choose 标签**

有时候我们并不想应用所有的条件，而只是想从多个选项中选择一个。MyBatis 提供了 choose 元素，按顺序判断 when 中的条件出否成立，如果有一个成立，则 choose 结束。当 choose 中所有 when的条件都不满则时，则执行 otherwise 中的 sql。类似于 Java 的 switch 语句，choose 为 switch，when 为 case，otherwise 则为 default。

**where 标签**

当 if 标签较多时,如果标签返回的内容是以 AND 或 OR 开头的，则它会剔除掉.

**set 标签**

使用 set 标签可以将动态的配置 set关键字，和剔除追加到条件末尾的任何不相关的逗号。

**trim 标签**

去除多余的 `AND` 或 `OR`，控制 SQL 语句的格式。

**sql 标签**

当多种类型的查询语句的查询字段或者查询条件相同时，可以将其定义为常量，方便调用。为求 <select> 结构清晰也可将 sql 语句分解。

## 第24题：SpringBoot框架的主要优势？

Spring Boot 是基于 Spring Framework 开发的一个开源框架，它简化了 Spring 应用程序的配置和部署。其主要优势包括：

**1. 简化配置**

Spring Boot 通过自动配置（Auto Configuration）机制，使得开发人员不再需要手动配置大量的 XML 或 Java 配置文件。它根据项目的类路径和其他配置自动推测并提供默认配置，极大地减少了配置的复杂性。

- **自动配置**：Spring Boot 自动配置提供了开发人员所需的很多常见配置，比如数据库连接池、JPA、Web 应用等，而无需手动配置。
- **约定大于配置**：Spring Boot 默认提供合理的约定，开发者只需要关注业务逻辑，而不需要过多关注框架的配置细节。

 **2. 快速开发**

- **Spring Initializr**：Spring Boot 提供了 [Spring Initializr](https://start.spring.io/) 工具，用户可以快速创建一个 Spring Boot 项目。只需要选择需要的依赖、项目结构、语言等，生成一个基本项目框架，省去了繁琐的项目初始化过程。
- **内嵌式服务器**：Spring Boot 内置了常用的 Web 服务器（如 Tomcat、Jetty 和 Undertow），无需额外部署和配置外部服务器，可以直接运行 Spring Boot 应用，进一步加速开发过程。

**3. 独立运行**

- Spring Boot 构建的应用是一个独立的可执行 JAR 或 WAR 文件，包含了内嵌的 Web 服务器（如 Tomcat）。因此，应用可以像桌面应用一样独立运行，不需要依赖外部的 Web 服务器。这种独立部署的方式提高了应用的可移植性。
- **One-click 启动**：通过命令行或者 IDE（如 IntelliJ IDEA、Eclipse）直接运行，Spring Boot 应用可以在几秒钟内启动并运行。

**4. 开箱即用的功能**

- Spring Boot 提供了一些预构建的功能和模块（如 Spring Data JPA、Spring Security、Spring Web 等），开发者可以很容易地集成和使用这些模块。
- 例如，Spring Boot 的数据访问模块可以自动配置数据库连接、JPA 和 Hibernate 等，只需要指定数据库的连接信息，其他配置会自动完成。

 **5. 微服务支持**

- Spring Boot 是 Spring Cloud 微服务架构的核心基础，支持分布式系统的快速构建。通过 Spring Cloud，Spring Boot 应用能够轻松实现服务注册、配置管理、负载均衡等微服务功能。
- **集成 Spring Cloud**：Spring Boot 与 Spring Cloud 的无缝集成简化了微服务架构的开发，减少了复杂度。

**6. 易于集成其他技术**

- Spring Boot 可以与许多常见的第三方技术、库和框架（如 Redis、RabbitMQ、Kafka、MongoDB、MySQL、Oracle、Elasticsearch 等）快速集成。
- **Spring Boot Starter**：Spring Boot 提供了大量的 starter 依赖，开发者可以通过添加这些依赖来集成所需的技术和工具。

**总结**

Spring Boot 通过自动配置、简化部署、开箱即用、内嵌式 Web 服务器和对微服务架构的强力支持，帮助开发者减少了大量的开发和配置工作，让开发更加高效、灵活，并且适应现代应用（特别是微服务架构和容器化）的需求。

## 第25题：SpringBoot的自动配置原理? 

**自动配置流程细节梳理：**

**1、**导入`starter-web`：导入了web开发场景

- 1、场景启动器导入了相关场景的所有依赖：`starter-json`、`starter-tomcat`、`springmvc`
- 2、每个场景启动器都引入了一个`spring-boot-starter`，核心场景启动器。
- 3、**核心场景启动器**引入了`spring-boot-autoconfigure`包。
- 4、`spring-boot-autoconfigure`里面囊括了所有场景的所有配置。
- 5、只要这个包下的所有类都能生效，那么相当于SpringBoot官方写好的整合功能就生效了。
- 6、SpringBoot默认却扫描不到 `spring-boot-autoconfigure`下写好的所有**配置类**。（这些**配置类**给我们做了整合操作），**默认只扫描主程序所在的包**。

**2、**主程序：`@SpringBootApplication`

- 1、`@SpringBootApplication`由三个注解组成`@SpringBootConfiguration`、`@EnableAutoConfiguratio`、`@ComponentScan`
- 2、SpringBoot默认只能扫描自己主程序所在的包及其下面的子包，扫描不到 `spring-boot-autoconfigure`包中官方写好的**配置类**
- 3、`@EnableAutoConfiguration`：SpringBoot **开启自动配置的核心**。
- - 1. 是由`@Import(AutoConfigurationImportSelector.class)`提供功能：批量给容器中导入组件。
  - 2. SpringBoot启动会默认加载 142个配置类。
  - 3. 这**142个配置类**来自于`spring-boot-autoconfigure`下 `META-INF/spring/**org.springframework.boot.autoconfigure.AutoConfiguration**.imports`文件指定的
  - 项目启动的时候利用 @Import 批量导入组件机制把 `autoconfigure` 包下的142 `xxxxAutoConfiguration`类导入进来（**自动配置类**）
  - 虽然导入了`142`个自动配置类
- 4、按需生效：
- - 并不是这`142`个自动配置类都能生效
  - 每一个自动配置类，都有条件注解`@ConditionalOnxxx`，只有条件成立，才能生效 

**3、**`xxxxAutoConfiguration**`**自动配置类**

- 1、给容器中使用@Bean 放一堆组件。
- 2、每个**自动配置类**都可能有这个注解`@EnableConfigurationProperties(**ServerProperties**.class)`，用来把配置文件中配的指定前缀的属性值封装到 `xxxProperties`**属性类**中
- 3、以DataSourceAutoConfiguration为例：所有配置都是以`spring.datasource`开头的，配置都封装到了属性类中。
- 4、给**容器**中放的所有**组件**的一些**核心参数**，都来自于`**xxxProperties**`**。**`**xxxProperties**`**都是和配置文件绑定。**
- **只需要改配置文件的值，核心组件的底层参数都能修改**

**4、**写业务，全程无需关心各种整合（底层这些整合写好了，而且也生效了）



**核心流程总结：**

1. 导入`starter`，就会导入`autoconfigure`包。

2. `autoconfigure` 包里面 有一个文件 `META-INF/spring/**org.springframework.boot.autoconfigure.AutoConfiguration**.imports`,里面指定的所有启动要加载的自动配置类

3. @EnableAutoConfiguration 会自动的把上面文件里面写的所有**自动配置类都导入进来。xxxAutoConfiguration 是有条件注解进行按需加载**

4. `xxxAutoConfiguration`给容器中导入一堆组件，组件都是从 `xxxProperties`中提取属性值

5. `xxxProperties`又是和**配置文件**进行了绑定

**效果：**导入`starter`、修改配置文件，就能修改底层行为。

## 第26题：Spring Boot中如何实现不同环境属性配置文件的支持？

Spring Boot支持不同环境的属性配置文件切换，通过创建application-{profile}.properties文件，其中{profile}是具体的环境标识名称，

例如：

application-dev.properties用于开发环境，application-test.properties用于测试环境，application-uat.properties用于uat环境。

如果要想使用application-dev.properties文件，则在application.properties文件中添加spring.profiles.active=dev。

如果要想使用application-test.properties文件，则在application.properties文件中添加spring.profiles.active=test。

## 第27题：Spring Boot 的启动类注解？由哪几个注解组成的？

Spring Boot 启动类的注解是由一组注解组合而成的，最常用的是 `@SpringBootApplication` 注解。这个注解本质上是一个复合注解，它包含了多个其他的注解。下面详细说明：

**`@SpringBootApplication` 由以下三个注解组成：**

1. **@EnableAutoConfiguration**
    该注解启用了 Spring Boot 的自动配置机制。Spring Boot 会根据项目中已包含的库和类路径（classpath）自动配置 Spring 应用程序的相关设置。简言之，Spring Boot 会根据应用程序的需求自动配置 Spring 环境中的 bean。

   例如，当你添加了 `spring-boot-starter-data-jpa` 依赖时，Spring Boot 会自动配置 JPA 相关的 bean（如 `EntityManagerFactory`、`DataSource` 等）。

2. **@ComponentScan**
    该注解启用组件扫描机制，它让 Spring 自动扫描当前包及其子包中的所有组件（包括 `@Component`、`@Service`、`@Repository`、`@Controller` 等注解标注的类）。`@SpringBootApplication` 会默认扫描启动类所在的包及其子包，这样你不需要手动配置扫描路径。

3. **@SpringBootConfiguration**
    该注解表明当前类是一个配置类，它是 Spring Java Config 的核心注解之一。配置类允许开发者使用 Java 代码来代替传统的 XML 配置文件。这是 Spring Boot 中 Java 配置的关键所在，允许你通过注解来定义和管理 Spring 的 bean。

**`@SpringBootApplication` 注解的组合意义：**

- **自动配置（Auto Configuration）**：基于应用的依赖和环境，自动配置 Spring 应用的设置，避免手动配置大量 Bean。
- **组件扫描（Component Scan）**：自动扫描指定包及其子包中的组件（如服务、控制器等），减少开发者手动配置包扫描路径的需求。
- **配置类（Configuration）**：标识该类是一个配置类，允许使用 Java 注解进行配置，替代 XML 配置。

**小结：**

`@SpringBootApplication` 注解是 Spring Boot 启动类的核心，它由 `@EnableAutoConfiguration`、`@ComponentScan` 和 `@Configuration` 三个注解组成。这使得开发者能够以最简单的方式启动和配置 Spring Boot 应用。

## 第28题：你如何理解 Spring Boot 中的 Starter以及工作原理？

Spring Boot Starter 通过封装某个方向需求的所有依赖库和自动化配置，极大地简化了应用的配置过程。开发者只需要关注应用的业务逻辑，而将繁琐的技术栈配置交给 Spring Boot 处理。这种机制降低了入门门槛，并提高了开发效率。通过合理地利用 `starter` 依赖，可以快速构建出功能丰富的应用，同时保持高度的可配置性。

Spring Boot 在启动的时候会干这几件事情：

- Spring Boot 在启动时会去依赖的 Starter 包中寻找 resources/META-INF/spring/**org.springframework.boot.autoconfigure.AutoConfiguration**.imports 文件
- 据文件中配置的 Jar 包去扫描项目所依赖的 Jar 包。
- 根据 imports 清单文件中的配置加载 AutoConfigure 类
- 根据 @Conditional 注解的条件，进行自动配置并将 Bean 注入 Spring Context

总结一下，其实就是 Spring Boot 在启动的时候，按照约定去读取 Spring Boot Starter 的配置信息，再根据配置信息对资源进行初始化，并注入到 Spring 容器中。这样 Spring Boot 启动完毕后，就已经准备好了一切资源，使用过程中直接注入对应 Bean 资源即可

# 三、Java分布式项目尚庭公寓(15道)

## 第1题: Redis的五大数据类型以及底层结构?

Redis 是一个开源的高性能键值数据库，它支持五种主要的数据类型。每种数据类型都有特定的应用场景，且底层实现结构不同。以下是 Redis 的五大数据类型、它们的应用场景以及底层结构的详细介绍。

**1. 字符串（String）**

- 应用场景：
  - 存储和管理简单的数据，例如缓存页面内容、用户会话、计数器等。
  - 使用 Redis 进行计数操作（例如网站访问量统计），通过 `INCR`、`DECR` 等命令操作数值。
- 底层结构：
  - Redis 将字符串值存储为原始数据（最多 512 MB），在内存中采用简单的动态字符串（SDS，Simple Dynamic String）来存储。
  - 当字符串较短时，Redis 采用简单的内存优化，节省空间。

**2. 哈希（Hash）**

- 应用场景：
  - 存储和管理对象数据，例如用户信息（如姓名、年龄、地址等）。
  - 比如可以将用户的多项属性（如 ID、姓名、年龄）存储为一个哈希表，而不是将其存储为一个单一的字符串。
- 底层结构：
  - Redis 使用哈希表来存储键值对，针对哈希表做了多种优化。
  - 哈希表的底层是一个链式哈希结构，使用数组和链表进行存储。对于存储少量字段时，它会使用一种压缩的编码方式以节省内存。

 **3. 列表（List）**

- 应用场景：
  - 存储和管理有序的数据，例如任务队列、消息队列等。
  - 用于发布/订阅模式、聊天记录、时间线等。
- 底层结构：
  - Redis 的列表底层使用双向链表实现，支持从两端快速插入和删除元素。
  - 对于元素较少的列表，Redis 会使用压缩列表（ziplist）来节省空间。对于较大的列表，使用链表结构。

**4.  集合（Set）**

- 应用场景：
  - 存储不重复的数据集，如用户关注的标签、独特的访客 IP 地址等。
  - 进行集合操作（如交集、并集、差集），如用户兴趣标签、社交网络的好友关系等。
- 底层结构：
  - Redis 使用哈希表或整数集合（IntSet）来实现集合。
  - 集合中的元素是无序的，但 Redis 可以高效地进行集合操作（如并集、交集等）。

**5.  有序集合（Sorted Set）**

- 应用场景：
  - 存储需要排序的数据，如排行榜、优先级队列等。
  - 每个元素关联一个分数（score），用于按照分数进行排序。
- 底层结构：
  - Redis 使用跳表（Skip List）和哈希表来实现有序集合，跳表用于保证高效的顺序查询，而哈希表用于高效的元素存取。
  - 跳表使得查询操作在 O(log N) 的时间复杂度下完成，适用于对分数排序的数据。

**总结表格**

|        数据类型        |             应用场景              |          底层结构          |
| :--------------------: | :-------------------------------: | :------------------------: |
|    字符串（String）    |  存储简单数据（如缓存、计数器）   |     动态字符串（SDS）      |
|      哈希（Hash）      |    存储对象数据（如用户信息）     |           哈希表           |
|      列表（List）      |        有序队列、消息队列         | 双向链表（压缩列表或链表） |
|      集合（Set）       | 存储不重复数据（如标签、IP 地址） |      哈希表（IntSet）      |
| 有序集合（Sorted Set） |         排行榜、优先队列          |       跳表 + 哈希表        |

## 第2题: Redis为什么这么快？

Redis之所以被认为是快速的，主要有以下几个原因：

1. 内存存储：Redis将数据存储在内存中，而不是磁盘上。相比于磁盘访问内存访问速度更快，可以实现很低的延迟和高吞吐量。
2. 单线程模型：Redis采单线程模型，避免了多线程间的竞争和上下文切换的开销。线程模型简化了并发控制，减少了锁的使用，提高了处理请求的效率。
3. 高效的数据结构：Redis提供了多种高效的数据结构，如字符串、哈希表、跳跃表、集合和有序集合等。这些数据结构在内部实现上都经过了优化，能够快速地进行插入、删除、查找和遍历操作。
4. 异步操作：Redis支持异步操作，可以在后台执行一些耗时的操作，如持久化、复制和集群的同步等。这样可以减少客户端的等待时间，提高系统的响应速度。
5. 高效的网络通信（核心）：Redis自定义的RESP协议进行网络通信，协议本身简单而高效。Redis的网络通信采用非阻塞I/O多路复用机制和事件驱动的方式，可以处理大量的并发连接，提高了系统的并发性能。
6. 优化的算法和数据结构：Redis在内部实现中使用了许多优化的算法和数据结构。例如，使用跳跃表（Skip List）来实现有序集合，使用压缩列表（ziplist）来存储小规模的列表和哈希表等。这些优化可以减少内存占用和提高数据操作的效率。

总的来说，Redis之所以快速，是因为它使用内存存储、采用单线程模型、提供高效的数据结构、支持异步操作、优化网络通信和使用优化的算法和数据结构等。这些特性使得能够在处理大量请求时保持低延迟和高吞吐量。

## 第3题: Redis的持久化方案和各自优缺点?

Redis提供了两种主要的持久化机制，分别是RDB（Redis Database File）和AOF（Append Only File）。这两种机制各有优缺点，适用于不同的场景。以下是对这两种持久化机制的详细介绍：

RDB（Redis Database File）持久化

1. **概述**：RDB是一种快照（Snapshot）形式的持久化方式。Redis会在指定的时间间隔内，将当前的内存数据快照保存为一个`.rdb`文件。这个文件可以用于Redis重启后的数据恢复。
2. 优点
   - **启动速度快**：由于RDB文件是二进制的快照文件，Redis加载RDB文件的速度非常快。
   - **适合冷备份**：RDB文件是一个压缩的二进制文件，适合将其复制到其他存储介质进行长期保存，尤其是灾难恢复的场景。
   - **占用空间小**：相比AOF日志，RDB文件体积小，适合定期存储。
3. 缺点
   - **数据丢失风险**：由于RDB是周期性保存快照的方式，如果Redis在快照之间发生宕机，最新的数据将会丢失。
   - **大数据集性能开销**：在生成快照时，Redis需要fork子进程来执行持久化操作，如果数据集较大，fork过程会消耗较多资源，可能会影响性能。
4. **配置**：RDB持久化的配置主要通过redis.conf文件中的`save`指令来设置。你可以根据需求设置保存快照的频率。

AOF（Append Only File）持久化

1. **概述**：AOF是一种日志记录的持久化方式。Redis通过将每一个写操作记录到日志文件中，重启时可以通过重放日志文件中的命令来恢复数据。AOF记录的文件名通常是`appendonly.aof`。

2. 优点

   - **数据丢失最少**：AOF可以设置成每次写操作后立即同步到磁盘，数据丢失的风险非常低。
   - **日志文件可读**：AOF文件以文本格式保存，记录了所有写操作，方便审计和排查问题。
   - **重写机制**：AOF支持日志文件重写，通过定期压缩日志文件，避免日志无限增长。

3. 缺点

   - **文件体积较大**：由于AOF记录了每一次写操作，文件体积往往比RDB文件大很多。
   - **恢复速度较慢**：AOF在重启时需要重放所有写操作，因此相较于RDB的快照恢复，速度较慢。
   - **性能开销大**：如果配置为每次写操作都同步到磁盘，AOF的性能开销较高。

4. 配置

   ：AOF持久化可以通过redis.conf中的以下配置项进行控制： 

   - `appendonly yes`：开启AOF持久化。
   - `appendfilename "appendonly.aof"`：设置AOF文件名。
   - `appendfsync`：控制数据同步到磁盘的频率，可选值为`always`、`everysec`、`no`。

**总结**

| 特性       | RDB                                    | AOF                                  |
| ---------- | -------------------------------------- | ------------------------------------ |
| 持久化方式 | 快照形式，定期保存内存数据的快照       | 日志形式，记录每个写操作命令         |
| 启动速度   | 快，因为只需加载二进制快照文件         | 慢，因为需要重放所有写操作命令       |
| 数据安全性 | 较低，存在数据丢失风险                 | 较高，数据丢失风险极低               |
| 文件大小   | 较小，因为是压缩的二进制文件           | 较大，因为记录了每个写操作命令       |
| 适用场景   | 适合冷备份和大规模数据恢复             | 适合数据敏感场景和实时性要求高的应用 |
| 性能开销   | fork子进程时有较大性能开销，但通常较快 | 如果每次写操作都同步，性能开销较大   |

总的来说，RDB和AOF各有其优缺点，具体选择哪种持久化机制取决于业务需求。如果业务允许短暂的数据丢失，可以仅使用RDB持久化以减少性能开销；如果需要更高的可靠性，可以选择AOF，或者结合使用RDB和AOF混合模式。

## 第4题: 什么是Redis主从模式以及集群模式?

**主从模式**

**Redis主从模式是一种经典的复制模式，用于提高数据的可用性和读取性能**。在Redis的主从模式下，一个Redis实例作为主节点（Master），负责处理所有的写操作和数据同步；而其他实例作为从节点（Slave），主要负责读操作，并实时同步主节点的数据。这种模式通过读写分离来优化性能，同时提供了一定程度的容错能力。

在配置主从结构时，可以通过三种方式实现：在配置文件中加入`slaveof {masterHost} {masterPort}`来保证持久性；在redis-server启动命令时加入`slaveof {masterHost} {masterPort}`命令；或者在Redis子节点的命令行中输入`slaveof {masterHost} {masterPort}`命令即可建立主从结构连接，输入`slaveof no one`即可断开主从结构。

主从复制的基本流程包括从节点调用`slaveof`命令开始配置主从同步关系，保存主节点的地址信息；主从节点建立TCP连接后，进行应用层的ping-pong测试以确保连接良好；如果主节点设置了密码，则需要进行密码校验；然后进行数据集的同步，首次连接会进行全量复制或部分复制；之后进入增量复制阶段，即主节点的实时复制。

为了确保连接正常，Redis提供了心跳包机制。主节点每隔10秒向从节点发送ping命令，若60秒内未收到pong响应，则认为连接异常。从节点也会每隔1秒向主节点发送特定请求，上报自身同步数据进度。

此外，当主节点故障时，需要人工干预来恢复服务。在某些情况下，如使用哨兵模式时，可以自动将从节点提升为主节点，以实现高可用性。

综上所述，Redis主从模式通过读写分离和数据同步提高了系统的可用性和性能，适用于读多写少的场景。然而，它也存在一定的局限性，如无法自动故障转移和扩展性有限等问题。因此，在选择是否使用Redis主从模式时，需要根据具体业务需求和系统环境进行综合考虑。

**Redis Cluster**

**Redis Cluster是一种分布式的Redis部署方案，旨在提高系统的可用性、扩展性和性能。它通过分片（sharding）的方式将数据分布在多个节点上，支持自动故障转移和动态扩展**。以下是对Redis Cluster的详细介绍：

1. 基本概念
   - **集群（Cluster）**：由多个Redis节点组成的集合，这些节点共同提供高可用性和高性能的数据存储服务。
   - **主节点（Master Node）**：负责处理客户端请求的节点，每个主节点只负责一部分数据。
   - **从节点（Slave Node）**：复制主节点的数据，用于故障转移和读取操作。
   - **槽（Slot）**：数据分布的基本单位，Redis Cluster将整个数据集划分为16384个槽，每个主节点负责一部分槽。
2. 特点与优势
   - **高可用性**：通过主从复制和自动故障转移机制，确保在部分节点失效时系统仍能正常工作。
   - **水平扩展**：可以动态添加或移除节点，实现无缝扩展。
   - **性能提升**：数据分布在多个节点上，可以实现更高的吞吐量和更低的延迟。
   - **去中心化**：没有单点故障，所有节点地位平等。
3. 工作原理
   - **数据分布**：Redis Cluster使用一致性哈希算法将数据分布在不同的槽中，每个槽对应一个主节点。客户端请求会根据键的哈希值路由到相应的主节点。
   - **故障检测与转移**：哨兵机制监控各个节点的健康状态，当检测到主节点故障时，会自动将从节点提升为新的主节点，并重新分配槽。
   - **客户端交互**：客户端通过与集群中的任一节点通信来获取集群的状态信息和进行数据操作。
4. 搭建步骤
   - **准备环境**：安装Redis，并确保所有节点之间的网络连接正常。
   - **配置节点**：在每个节点上配置redis.conf文件，指定集群模式和其他参数。
   - **启动节点**：分别启动各个节点，并使用`redis-cli --cluster create`命令创建集群。
   - **验证集群状态**：使用`redis-cli -c -h {node_ip} -p {node_port}`命令连接到集群中的任意节点，输入`cluster info`查看集群状态。
5. 注意事项
   - **节点数量**：至少需要6个主节点才能形成稳定的集群，其中3个主节点和3个从节点。
   - **版本要求**：确保使用支持集群功能的Redis版本（3.x及以上）。
   - **持久化设置**：建议开启AOF持久化，以确保数据安全。
6. 应用场景
   - **大规模数据存储**：适用于需要存储大量数据且访问频繁的场景。
   - **高并发应用**：适合需要高吞吐量和低延迟的应用，如电商网站、社交媒体等。
   - **弹性扩展需求**：对于需要动态扩展存储容量的应用，Redis Cluster提供了良好的解决方案。
7. 最佳实践
   - **合理规划节点数量**：根据业务需求合理规划节点数量，避免资源浪费或不足。
   - **定期备份数据**：虽然Redis Cluster具有高可用性，但仍需定期备份数据以防万一。
   - **监控与报警**：部署监控系统，实时监控集群状态，并设置报警机制，及时发现并处理异常情况。

综上所述，Redis Cluster通过分布式架构提供了高可用性、可扩展性和高性能的数据存储解决方案。它在大规模数据处理和高并发应用场景中表现出色，但也需要合理规划和管理以确保其稳定运行。

## 第5题: 请介绍一下Redis的过期策略!

Redis的过期管理策略主要包括**定时删除、惰性删除和混合策略**三种。以下是对这三种策略的具体介绍：

1. 定时删除（Fixed Interval Expiration）
   - **原理**：为每个设置了过期时间的键创建一个定时器，当键的过期时间到达时，定时器触发并立即删除该键。
   - **优点**：这种方式可以确保内存及时释放，因为过期键一旦到达设定时间就会立刻被移除，从而避免过期数据长时间占用内存资源。
   - **缺点**：创建和管理大量定时器会消耗CPU资源，尤其是当系统中存在大量带有过期时间的键时，CPU负载可能会显著增加。此外，如果定时器的精度不够高或者执行延迟，可能会导致键在预期时间之后才被删除。
2. 惰性删除（Lazy Expiration）
   - **原理**：只有在访问某个键时，Redis才会检查其是否已过期，如果已过期则删除该键。
   - **优点**：惰性删除不会占用额外的CPU资源进行检查，只在键被访问时才进行处理，因此对系统性能的影响较小。
   - **缺点**：如果键从未被访问，那么即使它已经过期，也会一直保留在内存中，导致内存浪费。此外，对于需要频繁访问的数据，惰性删除可能会导致短时间内大量的键被删除，从而影响系统性能。
3. 混合策略（Combined Policy）
   - **原理**：结合定时删除和惰性删除两种策略。Redis会定期随机抽取一部分带有过期时间的键进行检查，并删除其中已过期的键；同时，在访问键时也会检查其是否已过期，如果已过期则删除该键。
   - **优点**：这种组合方式既能保证过期键及时被清理，又能尽量减少对系统性能的影响。通过合理设置扫描频率和每次扫描的耗时，可以在不同情况下平衡CPU和内存资源的使用。
   - **缺点**：虽然混合策略在一定程度上缓解了定时删除和惰性删除的缺点，但它仍然需要在CPU资源和内存资源之间做出权衡。此外，混合策略的配置可能需要根据具体的应用场景进行调整以获得最佳效果。

综上所述，Redis的过期管理策略通过定时删除、惰性删除和混合策略三种方式来管理带有过期时间的键。这些策略各有优缺点，适用于不同的场景和需求。在实际使用中，可以根据具体情况选择合适的策略或调整相关参数以达到最佳效果。

## 第6题: 请介绍一下Redis的内存淘汰策略!

Redis的内存淘汰策略是**一种用于管理Redis实例中数据生命周期的机制，当Redis使用的内存达到预设的最大限制时，这些策略决定了哪些键值对应该被删除以释放空间**。以下是对Redis内存淘汰策略的具体介绍：

1. **noeviction**：这是Redis的默认策略（≥v3.0）。当内存使用达到最大限制时，Redis会拒绝新的写入操作，并返回错误，此时只响应读操作。这种策略适用于数据保留非常重要且不能丢失的场景，或者在内存充足的环境下使用。
2. **allkeys-lru**：在所有键中使用LRU（最近最少使用）算法进行淘汰。Redis会维护一个近似的LRU列表，虽然不完全精确，但对大多数使用场景来说是足够的。这种策略适用于缓存应用，其中需要保留最近被访问的数据以便快速响应后续的读取请求。
3. **allkeys-lfu**：在所有键中使用LFU（最不经常使用）算法进行淘汰。LFU算法根据键的访问频率来淘汰数据，访问次数最少的键优先被淘汰。这种策略适用于有明显热点数据的应用场景，可以确保热点数据不被轻易淘汰。
4. **volatile-lru**：仅在设置了过期时间的键中，基于LRU算法淘汰数据。这种策略适用于部分数据有时效性要求的场景，只针对设置了过期时间的键进行淘汰。
5. **volatile-lfu**：仅在设置了过期时间的键中，基于LFU算法淘汰数据。同样只针对设置了过期时间的键，但淘汰依据是访问频率。
6. **allkeys-random**：随机从所有key中淘汰数据。这种策略适用于对数据淘汰无特定要求的场景。
7. **volatile-random**：随机从设置了ttl key中淘汰数据。只针对设置了过期时间的键进行随机淘汰。
8. **volatile-lru**：根据键的剩余过期时间进行淘汰，越早过期的键越先被淘汰。这种策略适用于缓存数据时效性要求严格的场景。

总的来说，Redis的内存淘汰策略提供了多种方式来管理和优化内存使用，用户可以根据具体需求选择合适的策略。在实际使用中，还需要注意监控Redis的内存使用情况，并根据需要调整相关参数以优化性能和资源利用。第7题: 除了做缓存，Redis还能用来干什么？

## 第8题: 说出10个常用的Linux命令以及作用（设计）?

以下是20个常用的Linux命令及其作用，自行准备10个即可：

| 命令    | 作用                                     |
| ------- | ---------------------------------------- |
| `ls`    | 列出当前目录中的文件和文件夹             |
| `cd`    | 改变当前工作目录                         |
| `pwd`   | 显示当前工作目录的完整路径               |
| `cp`    | 复制文件或目录                           |
| `mv`    | 移动或重命名文件或目录                   |
| `rm`    | 删除文件或目录                           |
| `mkdir` | 创建新目录                               |
| `rmdir` | 删除空目录                               |
| `touch` | 创建空文件，或更新文件的访问和修改时间   |
| `cat`   | 查看文件内容，或将文件内容合并输出       |
| `man`   | 显示命令的帮助文档（手册页）             |
| `chmod` | 更改文件或目录的权限                     |
| `chown` | 更改文件或目录的所有者及所属组           |
| `ps`    | 显示当前正在运行的进程信息               |
| `top`   | 显示系统的资源占用情况，类似于任务管理器 |
| `kill`  | 结束指定的进程                           |
| `df`    | 显示文件系统的磁盘空间使用情况           |
| `du`    | 显示文件和目录的磁盘使用情况             |
| `grep`  | 搜索文件中符合条件的文本                 |
| `find`  | 在指定目录下查找符合条件的文件           |

## 第9题: 什么是Nginx? 主要有什么作用?

**Nginx**（发音为 "Engine-X"）是一个高性能的开源 **HTTP 服务器** 和 **反向代理服务器**，同时也可以作为 **负载均衡器** 和 **HTTP缓存**。最初由俄罗斯开发者 Igor Sysoev 于2002年开始编写，Nginx旨在解决传统Web服务器（如Apache）在高并发环境下的性能瓶颈。

**Nginx的主要作用：**

1. **Web服务器**：
   - Nginx作为Web服务器，负责接收HTTP请求，处理并返回网页或资源。它可以处理静态文件，如HTML、CSS、JavaScript、图片等，提供高效的文件传输能力。
2. **反向代理服务器**：
   - Nginx可以作为反向代理服务器，将客户端的请求转发到后端的应用服务器（如Tomcat、Node.js等），然后将处理结果返回给客户端。通过这种方式，可以隐藏后端应用服务器的真实地址，提高系统的安全性和扩展性。
3. **负载均衡**：
   - Nginx可以将用户的请求分配到多个后端服务器上，实现负载均衡。通过智能的算法（如轮询、IP哈希等），Nginx可以有效分配流量，提高系统的可用性和扩展性。
4. **HTTP缓存**：
   - Nginx可以缓存静态内容（如图片、网页等），减轻后端服务器的负担，提高响应速度，提升用户体验。
5. **SSL/TLS终止**：
   - Nginx可以处理SSL/TLS加密连接（即HTTPS），并将解密后的请求转发给后端服务器。这样可以减少后端服务器的负担，同时提高安全性。
6. **反向代理和API网关**：
   - 在微服务架构中，Nginx常作为反向代理服务器，处理不同微服务之间的请求转发。此外，它还可以作为API网关，集中管理微服务的路由、认证、日志等功能。
7. **高并发处理能力**：
   - Nginx通过事件驱动的异步架构，能够高效处理大量并发连接，能够在低内存消耗下提供高吞吐量。与传统的多线程模型的服务器（如Apache）相比，Nginx能够以更低的资源消耗处理更多的并发请求。

总结：

Nginx是一款高效、可靠的Web服务器，特别适合处理高并发请求。它除了作为Web服务器外，还具有反向代理、负载均衡、缓存等多种功能，因此被广泛应用于各种Web架构中，尤其是在需要高可用性和高性能的环境下。

## 第10题: 什么是正向和反向代理?

**正向代理**和**反向代理**是两种不同的代理模式，它们的作用和工作方式有所不同，主要体现在代理的对象和请求的方向上。

**1. 正向代理 (Forward Proxy)**

**正向代理**是客户端向代理服务器发起请求，然后由代理服务器代替客户端转发请求到目标服务器，获取响应后再返回给客户端。简单来说，**正向代理**隐藏的是**客户端**的身份。

工作流程：

- 客户端向代理服务器发送请求。
- 代理服务器接收到请求后，再向目标服务器发送请求。
- 目标服务器的响应返回给代理服务器，代理服务器再将响应转发给客户端。

主要用途：

- **绕过访问限制**：正向代理常用于绕过防火墙、IP限制等，使客户端能够访问被屏蔽的网站。
- **访问控制**：企业或学校等网络环境中，使用正向代理来限制或监控用户访问的内容。
- **匿名访问**：正向代理可以隐藏客户端的真实IP地址，保护用户隐私。

示例：

- **VPN**：通常作为正向代理工作，用户通过VPN连接后，所有请求都会先通过VPN服务器再访问外部网络。
- **Shadowsocks**：一种用于绕过审查和屏蔽的代理工具。

![1736477589380](images/1736477589380.png)

**2. 反向代理 (Reverse Proxy)**

**反向代理**是指代理服务器接受来自客户端的请求，然后将请求转发给后端的一个或多个服务器进行处理，最后将处理结果返回给客户端。**反向代理**隐藏的是**服务器**的身份，而客户端不需要知道具体是哪个服务器处理了请求。

工作流程：

- 客户端向反向代理服务器发送请求。
- 反向代理服务器根据策略（如负载均衡、路由等）将请求转发给相应的后端服务器。
- 后端服务器处理请求并将响应返回给反向代理，反向代理再将响应返回给客户端。

主要用途：

- **负载均衡**：反向代理可以将请求分发到多个后端服务器上，实现负载均衡，防止单一服务器过载。
- **提高安全性**：客户端无法直接访问后端服务器，反向代理可以屏蔽后端服务器的真实IP地址，防止攻击者直接攻击后端。
- **缓存静态内容**：反向代理可以缓存静态内容（如图片、HTML页面等），减少后端服务器的压力，提高响应速度。
- **SSL/TLS终止**：反向代理通常可以处理SSL加密解密任务，减轻后端服务器的负担。

示例：

- **Nginx**：作为反向代理，Nginx可以将请求分发给不同的后端应用服务器，常用于负载均衡、API网关等场景。
- **Cloudflare**：提供反向代理服务，可以代理多个后端网站，同时提供安全保护和流量加速。

![1736477622165](images/1736477622165.png)**3. 正向代理与反向代理的对比：**

| 特点           | 正向代理                               | 反向代理                                         |
| -------------- | -------------------------------------- | ------------------------------------------------ |
| 代理的对象     | 客户端                                 | 服务器                                           |
| 代理的工作方向 | 客户端 -> 代理 -> 目标服务器           | 客户端 -> 反向代理 -> 后端服务器                 |
| 客户端的身份   | 被隐藏，客户端不知道目标服务器的IP     | 被隐藏，客户端不知道实际处理请求的服务器         |
| 主要用途       | 绕过访问限制、隐藏客户端身份、访问控制 | 负载均衡、隐藏后端服务器、加速和缓存、提高安全性 |
| 示例           | VPN、Shadowsocks、浏览器代理设置       | Nginx、Cloudflare、负载均衡器、API网关           |

**总结：**

- **正向代理**主要作用是代理客户端的请求，隐藏客户端的真实身份，通常用于访问控制、突破网络封锁和匿名浏览等。
- **反向代理**主要作用是代理服务器的请求，隐藏真实的后端服务器，通常用于负载均衡、提高安全性和优化性能等。

## 第11题: 解释Nginx配置文件的结构?

以下是一个基本的 Nginx 配置文件示例，解释其作用。

```json
# Nginx 主配置文件示例
user nginx;                          # 设置 Nginx 工作进程的用户
worker_processes 1;                  # 设置 Nginx 启动的工作进程数
pid /var/run/nginx.pid;              # 指定 Nginx 进程的 PID 文件存储路径

events {
    worker_connections 1024;         # 每个工作进程的最大连接数
}

http {
    include       /etc/nginx/mime.types;  # 加载 mime 类型配置
    default_type  application/octet-stream;  # 默认 MIME 类型

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';  # 定义日志格式

    access_log  /var/log/nginx/access.log  main;  # 指定访问日志文件路径及格式

    sendfile on;                        # 启用高效的文件传输
    tcp_nopush on;                      # 禁用部分 TCP 包，提高响应性能
    tcp_nodelay on;                     # 启用 TCP 快速模式
    keepalive_timeout 65;                # 设置长连接的超时时间

    # 压缩相关设置
    gzip on;
    gzip_disable "msie6";  # 禁止 IE6 浏览器使用 gzip
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain application/xml text/css application/javascript application/json;  # 需要压缩的文件类型

    server {
        listen       80;  # 配置监听的端口号
        server_name  localhost;  # 配置服务器域名

        # 配置根目录
        root   /usr/share/nginx/html;
        index  index.html index.htm;  # 设置默认首页文件

        # 处理访问日志
        access_log  /var/log/nginx/localhost_access.log;

        # 处理请求
        location / {
            try_files $uri $uri/ =404;  # 访问时，如果文件不存在则返回 404 错误
        }

        # 配置某些特定路径的处理方式
        location /images/ {
            # 限制最大上传文件大小
            client_max_body_size 10M;
            # 处理特定路径下的文件
            root /var/www/images;
        }

        # 错误页面配置
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

    # 另一个虚拟主机配置示例
    server {
        listen       80;
        server_name  example.com;  # 配置另一个域名

        root   /var/www/example.com/html;  # 配置对应的站点根目录
        index  index.html index.htm;

        # 处理错误日志
        error_log  /var/log/nginx/example.com_error.log;

        location / {
            try_files $uri $uri/ =404;  # 同样处理 404 错误
        }
    }
}
```

**解释每部分作用**

1. **全局设置部分**
   - `user nginx;`：设置 Nginx 工作进程的运行用户。通常，Nginx 使用一个非特权用户运行，比如 `nginx` 或 `www-data`。
   - `worker_processes 1;`：配置启动的工作进程数。通常，设置为 CPU 核心数的值。
   - `pid /var/run/nginx.pid;`：指定存储 Nginx 进程 ID 的文件路径。
2. **events 块**
   - `worker_connections 1024;`：指定每个工作进程最大可以处理的连接数。这个值决定了 Nginx 的并发处理能力。
3. **http 块**
   - `include /etc/nginx/mime.types;`：包含 MIME 类型文件，用于指定请求头中 `Content-Type` 的类型。
   - `default_type application/octet-stream;`：设置默认的 MIME 类型为 `application/octet-stream`，如果没有匹配的类型时使用该类型。
   - `log_format`：定义访问日志的格式。这里定义的 `main` 格式包含了客户端 IP、请求时间、请求方法、请求路径、响应状态等信息。
   - `access_log /var/log/nginx/access.log main;`：指定访问日志的文件路径和使用的日志格式。
   - `sendfile on;`：启用高效的文件传输方式，减少内核与用户空间之间的数据复制。
   - `tcp_nopush`、`tcp_nodelay`：通过优化 TCP 层的行为来提升性能。
   - `keepalive_timeout 65;`：设置长连接的超时时间为 65 秒。
4. **Gzip 压缩设置**
   - 启用 `gzip` 压缩，用于减少传输的数据量。`gzip_types` 指定了哪些文件类型会被压缩，常见的是文本类型、CSS、JavaScript 等。
5. **server 块**
   - `listen 80;`：指定监听的端口。通常是 80（HTTP）或 443（HTTPS）。
   - `server_name localhost;`：配置虚拟主机的域名。可以为多个域名设置多个 `server` 块。
   - `root /usr/share/nginx/html;`：设置站点的根目录。
   - `index index.html index.htm;`：定义默认的主页文件名。
   - `access_log` 和 `error_log`：分别指定访问日志和错误日志的文件路径。
   - `location /`：定义 URI 匹配规则。`try_files` 用于尝试查找文件或目录，如果不存在则返回 404 错误。
6. **location 块**
   - `location /images/ {}`：在此 `location` 块中可以设置对特定路径的处理。比如，设置最大上传文件大小，指定根目录等。
   - `client_max_body_size 10M;`：限制上传的最大文件大小为 10MB。
7. **错误页面配置**
   - `error_page 500 502 503 504 /50x.html;`：为 HTTP 500 系列错误指定一个自定义的错误页面。
   - `location = /50x.html {}`：定义错误页面文件的位置。

**总结**

这个配置文件展示了如何设置 Nginx 以处理基本的静态文件服务，包括压缩、日志记录、错误处理以及文件上传限制。你可以根据自己的需求修改 `server_name`、`root` 和 `location` 等配置来处理不同的业务逻辑。

## 第12题: 如何在Nginx中一个虚拟主机配置细节？

在Nginx中配置虚拟主机，可以通过**创建配置文件、设置基本信息以及文档根目录等**步骤实现。以下是关于如何在Nginx中配置一个虚拟主机的具体分析：

1. **创建配置文件**：在`nginx/conf.d`目录下（默认路径，可以根据实际情况调整），新建一个`.conf`文件，例如`example.com.conf`。
2. **设置基本信息**：在配置文件中，添加以下基础信息，定义监听的端口和虚拟主机的名称：

```nginx
server {
    listen 80; # 或者443（如果启用HTTPS）
    server_name example.com;
}
```

1. **文档根目录**：指定站点的主目录，例如：

```nginx
root /var/www/example.com; # 这里替换为你实际的网站文件夹路径
```

1. **访问控制和SSL配置（如有必要）**：如果需要HTTPS，可以加入SSL证书和密钥：

```nginx
ssl_certificate /path/to/your.crt;
ssl_certificate_key /path/to/your.key;
```

1. **错误页面和日志设置**：设置错误页面和访问日志的位置：

```nginx
error_page 404 /404.html;
access_log /var/log/nginx/example.access.log main;
```

1. **启用虚拟主机**：最后，在`nginx.conf`的`http`块中包含你新创建的虚拟主机配置，如未包含则添加：

```nginx
include /etc/nginx/conf.d/*.conf
```

总的来说，通过上述步骤，可以在Nginx中成功配置一个虚拟主机。配置完成后，不要忘记重启Nginx服务以使更改生效。根据具体需求，还可以进一步优化配置，如设置负载均衡、反向代理等高级功能。

## 第13题：MyBatis和MyBatis-Plus的区别?

[MyBatis-Plus](https://github.com/baomidou/mybatis-plus) 是一个 [MyBatis](https://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 SQL 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

## 第14题：说一说项目用户认证的两种方案?以及JwtToken是什么?

有两种常见的认证方案，分别是基于**Session**的认证和基于**Token**的认证，下面逐一进行介绍

- **基于Session**

  基于Session的认证流程如下图所示

  <img src="images/%E7%99%BB%E5%BD%95%E6%B5%81%E7%A8%8B-%E5%9F%BA%E4%BA%8ESession.drawio.png" style="zoom:50%;" />

  该方案的特点

  - 登录用户信息保存在服务端内存中，若访问量增加，单台节点压力会较大
  - 随用户规模增大，若后台升级为集群，则需要解决集群中各服务器登录状态共享的问题。

- **基于Token（令牌）**

  基于Token的认证流程如下图所示

  <img src="../%E8%AF%BE%E4%BB%B6/%E6%8E%88%E8%AF%BE%E7%AC%94%E8%AE%B0/09_%E5%B0%9A%E5%BA%AD%E5%85%AC%E5%AF%93/%E6%95%99%E6%A1%88/%E7%AC%94%E8%AE%B0%E6%96%87%E6%A1%A3/images/%E7%99%BB%E5%BD%95%E6%B5%81%E7%A8%8B-%E5%9F%BA%E4%BA%8EToken.drawio.png" style="zoom:50%;" />

  该方案的特点

  - 登录状态保存在客户端，服务器没有存储开销

  - 客户端发起的每个请求自身均携带登录状态，所以即使后台为集群，也不会面临登录状态共享的问题。

    

(Token)**令牌**是一个代表用户身份和权限的字符串，用于在客户端和服务器之间进行身份验证和授权。令牌可以是任何形式的字符串，由服务器生成并在客户端存储。令牌可以包含有关用户身份、访问权限、过期时间等信息。

我们所说的Token，通常指**JWT**（JSON Web TOKEN）。JWT是一种轻量级的安全传输方式，用于在两个实体之间传递信息，通常用于身份验证和信息传递。

JWT是一个字符串，如下图所示，该字符串由三部分组成，三部分由`.`分隔。三个部分分别被称为

- `header`（头部）

  Header部分是由一个JSON对象经过`base64url`编码得到的，这个JSON对象用于保存JWT 的类型（`type`）、签名算法（`alg`）等元信息，例如

- `payload`（负载）： token是可以携带一些信息，账号 账号id..

- `signature`（签名）: 瓜瓜乐的保安全，负载经过header指定的算法和你的密钥生成一条秘文，和负载进行户型

## 第15题: 说出Git中常用的命令及其作用

下面是一个常用的 Git 命令及其作用的表格：

| **命令**                         | **作用**                                                     |
| -------------------------------- | ------------------------------------------------------------ |
| `git init`                       | 初始化一个新的 Git 仓库（在当前目录创建一个 `.git` 目录）。  |
| `git clone <repository>`         | 从远程仓库克隆一个完整的仓库到本地。                         |
| `git status`                     | 查看当前工作区和暂存区的状态，显示哪些文件有更改。           |
| `git add <file>`                 | 将文件添加到暂存区，准备提交。                               |
| `git commit -m "message"`        | 提交更改到本地仓库，并附上提交信息。                         |
| `git push`                       | 将本地仓库的提交推送到远程仓库。                             |
| `git pull`                       | 从远程仓库拉取并合并更改到本地仓库。                         |
| `git fetch`                      | 从远程仓库获取更新，但不自动合并。                           |
| `git merge <branch>`             | 将指定分支合并到当前分支。                                   |
| `git branch`                     | 查看所有本地分支或创建新的分支。                             |
| `git branch <branch-name>`       | 创建一个新的分支。                                           |
| `git checkout <branch>`          | 切换到指定的分支。                                           |
| `git checkout -b <branch-name>`  | 创建并切换到一个新的分支。                                   |
| `git log`                        | 查看提交历史记录。                                           |
| `git log --oneline`              | 查看简洁版的提交历史。                                       |
| `git diff`                       | 查看文件的更改内容，显示工作区与暂存区的差异。               |
| `git diff --staged`              | 查看暂存区与最新提交的差异。                                 |
| `git reset <file>`               | 从暂存区中移除指定的文件，保留工作区中的更改。               |
| `git reset --hard`               | 重置工作区和暂存区到最近一次提交的状态，所有未保存的更改将丢失。 |
| `git rm <file>`                  | 从工作区和暂存区中删除文件。                                 |
| `git log --graph`                | 以图形方式展示提交历史，显示分支的合并情况。                 |
| `git remote -v`                  | 查看当前远程仓库的 URL 地址。                                |
| `git remote add <name> <url>`    | 添加一个远程仓库。                                           |
| `git remote remove <name>`       | 删除指定的远程仓库。                                         |
| `git stash`                      | 暂时保存当前工作区的更改，以便在之后恢复。                   |
| `git stash pop`                  | 恢复最近一次 `stash` 保存的更改，并将其从栈中移除。          |
| `git stash list`                 | 查看所有的 `stash` 列表。                                    |
| `git tag <tag-name>`             | 给当前提交打标签。                                           |
| `git tag -d <tag-name>`          | 删除指定的标签。                                             |
| `git show <commit>`              | 显示某次提交的详细信息（包括差异和提交内容）。               |
| `git config --global user.name`  | 设置 Git 用户名（全局配置）。                                |
| `git config --global user.email` | 设置 Git 用户邮箱（全局配置）。                              |

这些是 Git 中最常用的一些命令，掌握这些基本命令可以让你高效地进行版本控制管理。



