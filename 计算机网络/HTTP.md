# HTTP

## 问题

### 基本概念

* HTTP 是什么？

* 常见的 HTTP 状态码有哪些？
* HTTP 常见字段？
* HTTP 请求报文和响应报文的结构？
* HTTP 首部字段？通用首部字段、请求首部字段、响应首部字段、实体首部字段都有什么？
* HTTP 的 BODY 是二进制还是文本？
* HTTP 如何解决粘包问题？content-length 字段和 chunk 字段了解吗？
* 谈谈 HTTP 代理、正向代理、反向代理



### 请求方法

* HTTP请求方法你知道多少？
* GET 和 POST 的区别，你知道哪些？
* GET与POST传递数据的最大长度能够达到多少呢？
* POST 方法会产生两个 TCP 数据包？你了解吗？
* POST 方法比 GET 方法安全？
* GET 方法的长度限制是怎么回事？
* GET 方法参数写法是固定的吗？



### HTTP 缓存机制

* HTTP 缓存有哪些实现方式？
	*  什么是强制缓存？
	* 什么是协商缓存？

* HTTP如何禁用缓存？如何确认缓存？
* HTTP中缓存的私有和共有字段？知道吗？



### HTTP 演化

* HTTP 1.0 的问题？
* HTTP /1.1版本比1.0版本多了哪些特性？
* HTTP /1.1优点？
* HTTP /1.1缺点？
* HTTP /1.1性能？
* HTTP /1.1如何优化？
* HTTP/1.1 性能问题？
* HTTP/2 做了什么优化？
* HTTP/2 如何兼容 HTTP/1.1？
* HTTP/2 有什么缺陷？
* QUIC 特点？
* HTTP/3 ？





### HTTP 过程



HTTP解析是怎么做的？

**一个TCP连接可以对应几个HTTP请求？**

**HTTP长连接和短连接的区别**

**说一下一次完整的HTTP请求过程包括哪些内容？**



### HTTP 与其他协议

* 有了 HTTP 为什么还要 RPC？
* 有了 HTTP 为什么还要 websocket？



### Cookie 、Session、Token

* 什么是 Session？
* 什么是 Cookie？
* 什么是 JSON WebToken？
* Cookie 和 Session 有什么区别？
* JWT 和 Session Cookie 有什么区别？





## 回答

### 基本概念

#### HTTP 是什么？

* 超文本传输协议
* HTTP 是一个在计算机世界里专门在「两点」之间「传输」文字、图片、音频、视频等「超文本」数据的「约定和规范」。

#### HTTP 常见的状态码？ 

![ 五大类 HTTP 状态码 ](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/6-%E4%BA%94%E5%A4%A7%E7%B1%BBHTTP%E7%8A%B6%E6%80%81%E7%A0%81.png)



 `1xx` 类状态码属于**提示信息**，是协议处理中的一种中间状态，实际用到的比较少。

`2xx` 类状态码表示服务器**成功**处理了客户端的请求，也是我们最愿意看到的状态。

- 「**200 OK**」是最常见的成功状态码，表示一切正常。如果是非 `HEAD` 请求，服务器返回的响应头都会有 body 数据。
- 「**204 No Content**」也是常见的成功状态码，与 200 OK 基本相同，但响应头没有 body 数据。
- 「**206 Partial Content**」是应用于 HTTP 分块下载或断点续传，表示响应返回的 body 数据并不是资源的全部，而是其中的一部分，也是服务器处理成功的状态。

`3xx` 类状态码表示客户端请求的资源发生了变动，需要客户端用新的 URL 重新发送请求获取资源，也就是**重定向**。

- 「**301 Moved Permanently**」表示永久重定向，说明请求的资源已经不存在了，需改用新的 URL 再次访问。
- 「**302 Found**」表示临时重定向，说明请求的资源还在，但暂时需要用另一个 URL 来访问。

301 和 302 都会在响应头里使用字段 `Location`，指明后续要跳转的 URL，浏览器会自动重定向新的 URL。

- 「**304 Not Modified**」不具有跳转的含义，表示资源未修改，重定向已存在的缓冲文件，也称缓存重定向，也就是告诉客户端可以继续使用缓存资源，用于缓存控制。

`4xx` 类状态码表示客户端发送的**报文有误**，服务器无法处理，也就是错误码的含义。

- 「**400 Bad Request**」表示客户端请求的报文有错误，但只是个笼统的错误。
- 「**403 Forbidden**」表示服务器禁止访问资源，并不是客户端的请求出错。
- 「**404 Not Found**」表示请求的资源在服务器上不存在或未找到，所以无法提供给客户端。

`5xx` 类状态码表示客户端请求报文正确，但是**服务器处理时内部发生了错误**，属于服务器端的错误码。

- 「**500 Internal Server Error**」与 400 类型，是个笼统通用的错误码，服务器发生了什么错误，我们并不知道。
- 「**501 Not Implemented**」表示客户端请求的功能还不支持，类似“即将开业，敬请期待”的意思。
- 「**502 Bad Gateway**」通常是服务器作为网关或代理时返回的错误码，表示服务器自身工作正常，访问后端服务器发生了错误。
- 「**503 Service Unavailable**」表示服务器当前很忙，暂时无法响应客户端，类似“网络服务正忙，请稍后重试”的意思。



#### HTTP 常见字段

***Host* 字段**

客户端发送请求时，用来指定服务器的域名。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/7-HOST%E5%AD%97%E6%AE%B5.png)

```text
Host: www.A.com
```

有了 `Host` 字段，就可以将请求发往「同一台」服务器上的不同网站。

*Content-Length 字段*

服务器在返回数据时，会有 `Content-Length` 字段，表明本次回应的数据长度。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/8-content-length%E5%AD%97%E6%AE%B5.png)

```text
Content-Length: 1000
```

如上面则是告诉浏览器，本次服务器回应的数据长度是 1000 个字节，后面的字节就属于下一个回应了。

***Connection 字段***

`Connection` 字段最常用于客户端要求服务器使用 TCP 持久连接，以便其他请求复用。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/9-connection%E5%AD%97%E6%AE%B5.png)

HTTP/1.1 版本的默认连接都是持久连接，但为了兼容老版本的 HTTP，需要指定 `Connection` 首部字段的值为 `Keep-Alive`。

```text
Connection: keep-alive
```

一个可以复用的 TCP 连接就建立了，直到客户端或服务器主动关闭连接。但是，这不是标准字段。

***Content-Type 字段***

`Content-Type` 字段用于服务器回应时，告诉客户端，本次数据是什么格式。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/10-content-type%E5%AD%97%E6%AE%B5.png)

```text
Content-Type: text/html; charset=utf-8
```

上面的类型表明，发送的是网页，而且编码是UTF-8。

客户端请求的时候，可以使用 `Accept` 字段声明自己可以接受哪些数据格式。

```text
Accept: */*
```

上面代码中，客户端声明自己可以接受任何格式的数据。

***Content-Encoding 字段***

`Content-Encoding` 字段说明数据的压缩方法。表示服务器返回的数据使用了什么压缩格式

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/11-content-encoding%E5%AD%97%E6%AE%B5.png)

```text
Content-Encoding: gzip
```

上面表示服务器返回的数据采用了 gzip 方式压缩，告知客户端需要用此方式解压。

客户端在请求时，用 `Accept-Encoding` 字段说明自己可以接受哪些压缩方法。

```text
Accept-Encoding: gzip, deflate
```



#### HTTP 请求报文和响应报文的结构？

**请求报文**

HTTP 请求报文由**请求行**、**请求头**、**空行**和**请求包体**(body)组成。如下图所示：

![img](https://pic2.zhimg.com/v2-bf990b661bcff759a17ce344416c7b15_b.jpg)

1.请求行

主要描述了客户端想要如何操作服务端的资源；请求行由三部分构成：

- 请求方法：表示对资源期望进行何种操作，常用的如 GET、POST
- 请求目标：通常是一个 URL ，表明了要操作的资源。
- 版本号：表示报文使用的 HTTP 协议版本。

这三个部分通常使用空格(space)来分隔，最后要用 CRLF 换行表示结束。

```text
GET / HTTP/1.1  
```

这个请求行，结合之前的描述，意思就是 “服务端妹子你好，我是客户端蛋蛋，现在我想获取网站根目录的默认信息，我这边用的协议版本是 1.1，麻烦你也要用这个版本回复我哦”

2.请求头

HTTP的报文头，报文头包含若干个属性，格式为“属性名:属性值”，服务端据此获取客户端的信息。与缓存相关的规则信息，均包含在 header 中，请求头可大致分为四种类型：通用首部字段、请求首部字段、响应首部字段、实体首部字段。这里先简单罗列，稍后做具体解释。

3.请求体

请求体就是 HTTP 要传输的内容，HTTP 可以承载很多类型的数字数据:图片、音频、视频、HTML 文档等。



**响应报文**

HTTP 响应报文由**状态行**、**响应头部**、**空行**和**响应包体**(body)组成。如下图所示：

![img](https://pic3.zhimg.com/v2-64cd282758794c3f77add4ffdaa4457e_b.jpg)

以请求 [http://www.baidu.com](https://link.zhihu.com/?target=http%3A//www.baidu.com)为例：

```text
HTTP/1.1 200 OK
Bdpagetype: 1
Bdqid: 0xfb0d743100040ad2
Cache-Control: private
Connection: keep-alive
Content-Encoding: gzip
Content-Type: text/html;charset=utf-8
Date: Fri, 24 Dec 2021 08:20:44 GMT
Expires: Fri, 24 Dec 2021 08:20:44 GMT
Server: BWS/1.1
Set-Cookie: BDSVRTM=17; path=/
Set-Cookie: BD_HOME=1; path=/
Set-Cookie: H_PS_PSSID=35635_34439_35104_35628_35488_35436_35456_34584_35491_35584_35586_34873_35317_26350_35610_35562; path=/; domain=.baidu.com
Strict-Transport-Security: max-age=172800
Traceid: 1640334044050133761018090243032019634898
X-Frame-Options: sameorigin
X-Ua-Compatible: IE=Edge,chrome=1
Transfer-Encoding: chunked
```

1.状态行

状态行包含了 **协议版本**、**状态码**以及**状态描述**。

- 协议版本：指明了报文使用的 HTTP 协议版本
- 状态码：状态码是一个三位数字，用来表示处理的结果，下面列出了状态码的类别：

![img](https://pic3.zhimg.com/v2-2514cf50fc93d982da605e29fd77e14a_b.jpg)



- 状态描述：这个是作为状态码的补充，是一段更详细的文字，帮助人们理解原因。

2.响应头部

和请求报文的请求头类似，响应头也由键值对组成，每行一对，键和值用英文冒号 : 分隔。响应头允许服务器传递不能放在状态行的附加信息，这些域主要描述服务器的信息和 Request-URI 进一步的信息

3.响应包体

服务器返回给浏览器的响应信息，响应数据的格式是根据服务器来的，常见的响应数据格式有：text/html、application/json 等。

常见的响应格式：

![img](https://pic2.zhimg.com/v2-1f206d9f42879e6650979010d45ce24d_b.jpg)



#### HTTP 首部字段？通用首部字段、请求首部字段、响应首部字段、实体首部字段都有什么？

在 HTTP 的请求头和响应头中都是由首部字段来表示的，首部内容可以为客户端和服务器分别处理请求和响应提供所需要的信息。

首部字段可以分为**通用首部字段**、**请求首部字段**、**响应首部字段**、**实体首部字段**。



**通用首部字段**是指请求报文和响应报文都会使用到的首部字段。

![img](https://pic4.zhimg.com/v2-0a5fb2d48967be2d7f98f2d8b385fe2f_b.jpg)



**请求首部字段**是从客户端往服务器端发送请求报文中所使用的字段，用于补充请求的附加信息、客户端信息、对响应内容相关的优先级等内容。

![img](https://pic1.zhimg.com/v2-6db0e0b4889703f9b6a0b7fdc8234720_b.jpg)

**响应首部字段**是由服务器端向客户端返回响应报文中所使用的字段，用于补充响应的附加信息、服务器信息，以及对客户端的附加要求等信息。

![img](https://pic4.zhimg.com/v2-d785432cd4c0220cff90a4cf8c63cfa3_b.jpg)

**实体首部字段**是包含在请求报文和响应报文中的实体部分所使用的首部，用于补充内容的更新时间等与实体相关的信息。

![img](https://pic4.zhimg.com/v2-998c0f77794271f5f002d07f4e87fb07_b.jpg)





#### HTTP 的 BODY 是二进制还是文本？

都有，看具体内容是啥格式的

**数据类型与编码**

在TCP/IP协议栈里，传输数据基本上都是"header+body"的格式，但是TCP,UDP因为是传输层的协议，它们并不关心body数据是什么，只要把数据送到对方就可以了。

而HTTP协议则不同，它是应用层的协议，数据到达之后工作只能说是完成了一半，还必须要告诉上层应用这是什么数据才行，否则上层应用就会不知所措。

那么这里简单列举一下在HTTP里经常遇到的几个类别:

* text:即文本格式的可读数据，我们最熟悉的应该就是text/html了，表示超文本文档，此外还有纯文本text/plain,样式表text/css等。
* image:即图像文件，有image/gif,image/jpeg,image/png等。
* audio/video:音频和视频数据，可能是文本也可能是二进制，必须由上层应用程序来解释。
* 常见的有application/json,application/javascript,application/pdf等，另外，如果实在是不知道数据是什么类型，就会是application/octet-stream,即不透明的二进制数据。



但仅有上面这些MIME type还不够，因为HTTP在传输时为了节约带宽，有时候还会压缩数据，还需要有一个"Encoding type"，告诉数据是用的什么编码格式，这样对方才能正确解压缩，还原出原始数据。

**通常Encoding type有下面三种类型:**

* gzip:GNU zip压缩格式，也是互联网上最流行的压缩格式
* deflate:zlib(deflate)压缩格式，流行程度仅次于gzip
* br:一种专门为HTTP优化的新压缩算法(Brotli)



**数据类型使用的头字段**

有了上面两种类型，无论是浏览器还是服务器就都可以轻松识别出body的类型，也就能正确处理数据了。

HTTP协议为此定义了两个Accept请求头子段和两个Content实体头字段，用于客户端和服务器进行内容协商。也就是说，客户端用Accept头告诉服务器希望接收什么样的数据，而服务器用Content头告诉客户端实际发送了什么样的数据。

Accept字段标记的是客户端可理解的MIME type，可用“，”做分隔符列出多个类型，让服务器有更多的选择余地，例如下面的这个头:

```
Accept: text/html,application/xml,image/webp,image/png这就是告诉服务器：我能够看懂HTML,XML的文本，还有webp和png的图片，请给我这四类格式的数据。
```

相应的，服务器会在响应报文里用头子段Content-Type告诉实体数据的真实类型:

```
Content-Type: text/htmlContent-Type: image/png
```


这样浏览器看到报文里的类型是"text/html"就知道是HTML文件，会调用排版引擎渲染出页面，看到image/png就知道是一个PNG文件，就会在页面上显示出图像。

Accept-Encoding字段标记的是客户端支持的压缩格式，例如上面说的gzip,deflate等，同样也可以用



#### HTTP 如何解决粘包问题？content-length 字段和 chunk 字段了解吗？

https://blog.csdn.net/u013378306/article/details/107742524

https://blog.csdn.net/weixin_42002747/article/details/124334361

**粘包问题存在争议：看 TCP 部分相关回答**



**在讲粘包问题之前，首先得明白这个包是应用层的数据包。**
当数据在传输层时，由于TCP是面向字节流的，所以它看到的数据是按照顺序一个个放在缓冲区中的，而对于应用层而言，看到的只是一连串的数据，那么应用层该从哪里读数据，读到哪合适呢？因此就有了粘包问题。

所以要避免粘包问题，就得明确两个包之间的边界：

* 对于定长的数据包，保证每次都按固定的大小读取即可；
* 对于变长的包，可在包头的位置，约定一个包总长度的字段，从而就知道了包的结束位置；
* 对于变长的包，还可以在包和包之间使用明确的分隔符（应用层协议，是我们自己写的，只要保证分隔符不和正文冲突即可）

若传输层是UDP协议，应用层会不会出现粘包问题？

对于UDP而言，报文长度是固定的，就算没有交付，长度依然在，同时，UDP是一个一个把数据交付给应用层的，就有很明显的边界
站在应用层的角度，每次收到的UDP报文，要么是一整个，要么不收，不会出现半个的情况



**HTTP 解决方案**

http请求报文格式

* 请求行：以\r\n结束；
* 请求头：以\r\n结束；
* \r\n；
* 数据；

http响应报文格式

* 响应行：以\r\n结束；
* 响应头：以\r\n结束；
* \r\n；
* 数据；

遇到第一个\r\n表示读取请求行或响应行结束；

遇到\r\n\r\n表示读取请求头或响应头结束；



怎么读取body数据呢？

* 根据请求头或响应头的Content-Length，单位是字节；
* chunked协议，取代Content-Length，如果请求头或者响应头有Transfer-Encoding: chunked，表示根据chunked协议协议读取数据



**2.1 背景**
   HTTP 协议通常使用 Content-Length 来标识 body 的长度，在服务器端，需要先申请对应长度的 buffer，然后再赋值。如果需要一边生产数据一边发送数据，就需要使用 "Transfer-Encoding: chunked" 来代替 Content-Length，也就是对数据进行分块传输。



**2.2 Content-Length 描述**
    1：http server 接收数据时，发现 header 中有 Content-Length 属性，则读取 Content-Length 的值，确定需要读取 body 的长度。

​    2：http server 发送数据时，根据需要发送 byte 的长度，在 header 中增加 Content-Length 项，其中 value 为 byte 的长度，然后将 byte 数据当做 body 发送到客户端。



**2.3 chunked 描述**

 1：http server 接收数据时，发现 header 中有 Transfer-Encoding: chunked，则会按照 truncked 协议分批读取数据。

 2：http server 发送数据时，如果需要分批发送到客户端，则需要在 header 中加上 Transfer-Encoding: chunked，然后按照 truncked 协议分批发送数据。



**2.4 truncked 协议**

　　

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWcyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTMwNDM2NC8yMDE5MDUvMTMwNDM2NC0yMDE5MDUyOTIxMTg1NzgwOC0yMDMxMzcxNjE1LmpwZw?x-oss-process=image/format,png)







​    1：主要包含三部分：chunk，last-chunk 和 trailer。如果分多次发送，则 chunk 有多份。

​    2：chunk 主要包含大小和数据，大小表示这个这个 trunck 包的大小，使用 16 进制标示。其中 trunk 之间的分隔符为 CRLF。

​    3：通过 last-chunk 来标识 chunk 发送完成。 一般读取到 last-chunk(内容为 0) 的时候，代表 chunk 发送完成。

​    4：trailer 表示增加 header 等额外信息，一般情况下 header 是空。通过 CRLF 来标识整个 chunked 数据发送完成。



 **2.5 优点**
   1：假如 body 的长度是 10K，对于 Content-Length 则需要申请 10K 连续的 buffer，而对于 Transfer-Encoding: chunked 可以申请 1k 的空间，然后循环使用 10 次。节省了内存空间的开销。

   2：如果内容的长度不可知，则可使用 trunked 方式能有效的解决 Content-Length 的问题

   3：http 服务器压缩可以采用分块压缩，而不是整个快压缩。分块压缩可以一边进行压缩，一般发送数据，来加快数据的传输时间。



 **2.6 缺点**
   1：truncked 协议解析比较复杂。

   2：在 http 转发的场景下 (比如 nginx) 难以处理，比如如何对分块数据进行转发。



### 请求方法

#### HTTP请求方法你知道多少？

客户端发送的 **请求报文** 第一行为请求行，包含了方法字段。

根据 HTTP 标准，HTTP 请求可以使用多种请求方法。

HTTP1.0 定义了三种请求方法： GET, POST 和 HEAD方法。

HTTP1.1 新增了六种请求方法：OPTIONS、PUT、PATCH、DELETE、TRACE 和 CONNECT 方法。

| 序 号 | 方法    | 描述                                                         |
| ----- | ------- | ------------------------------------------------------------ |
| 1     | GET     | 请求指定的页面信息，并返回实体主体。                         |
| 2     | HEAD    | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头 |
| 3     | POST    | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改。 |
| 4     | PUT     | 从客户端向服务器传送的数据取代指定的文档的内容。             |
| 5     | DELETE  | 请求服务器删除指定的页面。                                   |
| 6     | CONNECT | HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。    |
| 7     | OPTIONS | 允许客户端查看服务器的性能。                                 |
| 8     | TRACE   | 回显服务器收到的请求，主要用于测试或诊断。                   |
| 9     | PATCH   | 是对 PUT 方法的补充，用来对已知资源进行局部更新 。           |

 

#### GET 和 POST 的区别，你知道哪些？

- get是获取数据，post是修改数据
- get把请求的数据放在url上， 以?分割URL和传输数据，参数之间以&相连，所以get不太安全。而post把数据放在HTTP的包体内（requrest body）
- get提交的数据最大是2k（ 限制实际上取决于浏览器）， post理论上没有限制。
- GET产生一个TCP数据包，浏览器会把http header和data一并发送出去，服务器响应200(返回数据); POST产生两个TCP数据包，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok(返回数据)。(这个不是协议规定，是浏览器浏览器和框架的行为)
- GET请求会被浏览器主动缓存，而POST不会，除非手动设置。
- 本质区别：GET是幂等的，而POST不是幂等的

这里的幂等性：幂等性是指一次和多次请求某一个资源应该具有同样的副作用。简单来说意味着对同一URL的多个请求应该返回同样的结果。

正因为它们有这样的区别，所以不应该且**不能用get请求做数据的增删改这些有副作用的操作**。因为get请求是幂等的，**在网络不好的隧道中会尝试重试**。如果用get请求增数据，会有**重复操作**的风险，而这种重复操作可能会导致副作用（浏览器和操作系统并不知道你会用get请求去做增操作）。



#### GET与POST传递数据的最大长度能够达到多少呢？

 get 是通过URL提交数据，因此GET可提交的数据量就跟URL所能达到的最大长度有直接关系。

很多文章都说GET方式提交的数据最多只能是1024字节，而实际上，URL不存在参数上限的问题，HTTP协议规范也没有对URL长度进行限制。

**这个限制是特定的浏览器及服务器对它的限制，比如IE对URL长度的限制是2083字节(2K+35字节)。对于其他浏览器，如FireFox，Netscape等，则没有长度限制，这个时候其限制取决于服务器的操作系统；即如果url太长，服务器可能会因为安全方面的设置从而拒绝请求或者发生不完整的数据请求。**

**post 理论上讲是没有大小限制的，HTTP协议规范也没有进行大小限制，但实际上post所能传递的数据量大小取决于服务器的设置和内存大小。**

**因为我们一般post的数据量很少超过MB的，所以我们很少能感觉的到post的数据量限制，但实际中如果你上传文件的过程中可能会发现这样一个问题，即上传个头比较大的文件到服务器时候，可能上传不上去。**

以php语言来说，查原因的时候你也许会看到有说PHP上传文件涉及到的参数PHP默认的上传有限定，一般这个值是2MB，更改这个值需要更改php.conf的post_max_size这个值。这就很明白的说明了这个问题了。



#### POST 方法会产生两个 TCP 数据包？你了解吗？

有些文章中提到，POST 会将 header 和 body 分开发送，先发送 header，服务端返回 100 状态码再发送 body。

**HTTP 协议中没有明确说明 POST 会产生两个 TCP 数据包，而且实际测试(Chrome)发现，header 和 body 不会分开发送。**

**所以，header 和 body 分开发送是部分浏览器或框架的请求方法，不属于 post 必然行为。**



#### POST 方法比 GET 方法安全？

有人说POST 比 GET 安全，因为数据在地址栏上不可见。然而，从传输的角度来说，他们都是不安全的，因为 HTTP 在网络上是明文传输的，只要在网络节点上捉包，就能完整地获取数据报文。要想安全传输，就只有加密，也就是 HTTPS。



#### GET 方法的长度限制是怎么回事？

网络上都会提到浏览器地址栏输入的参数是有限的。首先说明一点，HTTP 协议没有 Body 和 URL 的长度限制，对 URL 限制的大多是浏览器和服务器的原因。

浏览器原因就不说了，服务器是因为处理长 URL 要消耗比较多的资源，为了性能和安全（防止恶意构造长 URL 来攻击）考虑，会给 URL 长度加限制。



#### GET 方法参数写法是固定的吗？

在约定中，我们的参数是写在 ? 后面，用 & 分割。我们知道，解析报文的过程是通过获取 TCP 数据，用正则等工具从数据中获取 Header 和 Body，从而提取参数。比如header请求头中添加token，来验证用户是否登录等权限问题。也就是说，我们可以自己约定参数的写法，只要服务端能够解释出来就行，万变不离其宗。

 

### 缓存机制

#### HTTP 缓存有哪些实现方式？

对于一些具有重复性的 HTTP 请求，比如每次请求得到的数据都一样的，我们可以把这对「请求-响应」的数据都**缓存在本地**，那么下次就直接读取本地的数据，不必在通过网络获取服务器的响应了，这样的话 HTTP/1.1 的性能肯定肉眼可见的提升。

所以，避免发送 HTTP 请求的方法就是通过**缓存技术**，HTTP 设计者早在之前就考虑到了这点，因此 HTTP 协议的头部有不少是针对缓存的字段。

HTTP 缓存有两种实现方式，分别是**强制缓存和协商缓存**。



**什么是强制缓存？**

强缓存指的是只要浏览器判断缓存没有过期，则直接使用浏览器的本地缓存，决定是否使用缓存的主动性在于浏览器这边。

如下图中，返回的是 200 状态码，但在 size 项中标识的是 from disk cache，就是使用了强制缓存。

![img](https://img-blog.csdnimg.cn/1cb6bc37597e4af8adfef412bfc57a42.png)

强缓存是利用下面这两个 HTTP 响应头部（Response Header）字段实现的，它们都用来表示资源在客户端缓存的有效期：

- `Cache-Control`， 是一个相对时间；
- `Expires`，是一个绝对时间；

如果 HTTP 响应头部同时有 Cache-Control 和 Expires 字段的话，**Cache-Control的优先级高于 Expires** 。

Cache-control 选项更多一些，设置更加精细，所以建议使用 Cache-Control 来实现强缓存。具体的实现流程如下：

- 当浏览器第一次请求访问服务器资源时，服务器会在返回这个资源的同时，在 Response 头部加上 Cache-Control，Cache-Control 中设置了过期时间大小；
- 浏览器再次请求访问服务器中的该资源时，会先**通过请求资源的时间与 Cache-Control 中设置的过期时间大小，来计算出该资源是否过期**，如果没有，则使用该缓存，否则重新请求服务器；
- 服务器再次收到请求后，会再次更新 Response 头部的 Cache-Control。



**什么是协商缓存？**

当我们在浏览器使用开发者工具的时候，你可能会看到过某些请求的响应码是 `304`，这个是告诉浏览器可以使用本地缓存的资源，通常这种通过服务端告知客户端是否可以使用缓存的方式被称为协商缓存。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http1.1%E4%BC%98%E5%8C%96/%E7%BC%93%E5%AD%98etag.png)

上图就是一个协商缓存的过程，所以**协商缓存就是与服务端协商之后，通过协商结果来判断是否使用本地缓存**。

协商缓存可以基于两种头部来实现。

第一种：请求头部中的 `If-Modified-Since` 字段与响应头部中的 `Last-Modified` 字段实现，这两个字段的意思是：

- 响应头部中的 `Last-Modified`：标示这个响应资源的最后修改时间；
- 请求头部中的 `If-Modified-Since`：当资源过期了，发现响应头中具有 Last-Modified 声明，则再次发起请求的时候带上 Last-Modified 的时间，服务器收到请求后发现有 If-Modified-Since 则与被请求资源的最后修改时间进行对比（Last-Modified），如果最后修改时间较新（大），说明资源又被改过，则返回最新资源，HTTP 200 OK；如果最后修改时间较旧（小），说明资源无新修改，响应 HTTP 304 走缓存。

第二种：请求头部中的 `If-None-Match` 字段与响应头部中的 `ETag` 字段，这两个字段的意思是：

- 响应头部中 `Etag`：唯一标识响应资源；
- 请求头部中的 `If-None-Match`：当资源过期时，浏览器发现响应头里有 Etag，则再次向服务器发起请求时，会将请求头If-None-Match 值设置为 Etag 的值。服务器收到请求后进行比对，如果资源没有变化返回 304，如果资源变化了返回 200。

第一种实现方式是基于时间实现的，第二种实现方式是基于一个唯一标识实现的，相对来说后者可以更加准确地判断文件内容是否被修改，避免由于时间篡改导致的不可靠问题。

如果在第一次请求资源的时候，服务端返回的 HTTP 响应头部同时有 Etag 和 Last-Modified 字段，那么客户端再下一次请求的时候，如果带上了 ETag 和 Last-Modified 字段信息给服务端，**这时 Etag 的优先级更高**，也就是服务端先会判断 Etag 是否变化了，如果 Etag 有变化就不用在判断 Last-Modified 了，如果 Etag 没有变化，然后再看 Last-Modified。

**为什么 ETag 的优先级更高？**这是因为 ETag 主要能解决 Last-Modified 几个比较难以解决的问题：

1. 在没有修改文件内容情况下文件的最后修改时间可能也会改变，这会导致客户端认为这文件被改动了，从而重新请求；
2. 可能有些文件是在秒级以内修改的，`If-Modified-Since` 能检查到的粒度是秒级的，使用 Etag就能够保证这种需求下客户端在 1 秒内能刷新多次；
3. 有些服务器不能精确获取文件的最后修改时间。

注意，**协商缓存这两个字段都需要配合强制缓存中 Cache-control 字段来使用，只有在未能命中强制缓存的时候，才能发起带有协商缓存字段的请求**。

下图是强制缓存和协商缓存的工作流程：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/network/http/http%E7%BC%93%E5%AD%98.png)

当使用 ETag 字段实现的协商缓存的过程：

- 当浏览器第一次请求访问服务器资源时，服务器会在返回这个资源的同时，在 Response 头部加上 ETag 唯一标识，这个唯一标识的值是根据当前请求的资源生成的；
- 当浏览器再次请求访问服务器中的该资源时，首先会先检查强制缓存是否过期：
	- 如果没有过期，则直接使用本地缓存；
	- 如果缓存过期了，会在 Request 头部加上 If-None-Match 字段，该字段的值就是 ETag 唯一标识；
- 服务器再次收到请求后，会根据请求中的 If-None-Match 值与当前请求的资源生成的唯一标识进行比较：
	- **如果值相等，则返回 304 Not Modified，不会返回资源**；
	- 如果不相等，则返回 200 状态码和返回资源，并在 Response 头部加上新的 ETag 唯一标识；
- 如果浏览器收到 304 的请求响应状态码，则会从本地缓存中加载资源，否则更新资源。





#### HTTP如何禁用缓存？如何确认缓存？

HTTP/1.1 通过 Cache-Control 首部字段来控制缓存。

**禁止进行缓存**

no-store 指令规定不能对请求或响应的任何一部分进行缓存。

```plaintext
Cache-Control: no-store
```

**强制确认缓存**

no-cache 指令规定缓存服务器需要先向源服务器验证缓存资源的有效性，只有当缓存资源有效时才能使用该缓存对客户端的请求进行响应。

```plaintext
Cache-Control: no-cache
```

 

#### HTTP中缓存的私有和共有字段？知道吗？

private 指令规定了将资源作为私有缓存，只能被单独用户使用，一般存储在用户浏览器中。

```plaintext
Cache-Control: private
```

public 指令规定了将资源作为公共缓存，可以被多个用户使用，一般存储在代理服务器中。

```plaintext
Cache-Control: public
```



### HTTP 演化

![HTTP 演化.png](https://s2.loli.net/2022/10/14/EAwVL38vkYMb7Qm.png)

#### HTTP/1.0 的问题？

- 无状态：服务器不跟踪不记录请求过的状态
- 无连接：浏览器每次请求都需要建立tcp连接

HTTP/1.0规定浏览器和服务器保持短暂的连接。浏览器的每次请求都需要与服务器建立一个TCP连接，服务器处理完成后立即断开TCP连接（**无连接**），服务器不跟踪每个客户端也不记录过去的请求（**无状态**）

无状态导致的问题可以借助[cookie/session](#cas)机制来做身份认证和状态记录解决。

然而，无连接特性将会导致以下性能缺陷：

1. **无法复用连接**。每次发送请求的时候，都需要进行一次[TCP连接](#tcp)，而TCP的连接释放过程又是比较费事的。这种无连接的特性会导致网络的利用率非常低。
2. **队头堵塞(head of line blocking)**。由于HTTP/1.0规定下一个请求必须在前一个请求响应到达之前才能发送。假设一个请求响应一直不到达，那么下一个请求就不发送，就到导致阻塞后面的请求。

#### HTTP/1.1版本比1.0版本多了哪些特性？

- **长连接。**HTTP/1.1增加了一个Connection字段，通过设置[Keep-alive](#keepAlive)（默认已设置）可以保持连接不断开，避免了每次客户端与服务器请求都要重复建立释放TCP连接，提高了网络的利用率。如果客户端想关闭HTTP连接，可以在请求头中携带`Connection:false`来告知服务器关闭请求
- **支持请求管道化（pipelining）。**基于HTTP/1.1的长连接，使得请求管线化成为可能。管线化使得请求能够“并行”传输。举个例子来说，假如响应的主体是一个html页面，页面中包含了很多img，这个时候keep-alive就起了很大的作用，能够进行“并行”发送多个请求。（注意这里的“并行”并不是真正意义上的并行传输，具体解释如下。）

需要注意的是，服务器必须按照客户端请求的先后顺序依次回送相应的结果，以保证客户端能够区分出每次请求的响应内容。

也就是说，HTTP管道化可以让我们把先进先出队列从客户端（请求队列）迁移到服务端（响应队列）。

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5583691fea2141b9aa62a8c9729b5a37~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

如图所示，客户端同时发了两个请求分别来获取html和css，假如说服务器的css资源先准备就绪，服务器也会先发送html再发送css。

换句话来说，只有等到html响应的资源完全传输完毕后，css响应的资源才能开始传输。也就是说，不允许同时存在两个并行的响应。

可见，HTTP/1.1还是无法解决队头阻塞（head of line blocking）的问题。同时“管道化”技术存在各种各样的问题，所以很多浏览器要么根本不支持它，要么就直接默认关闭，并且开启的条件很苛刻...而且实际上好像并没有什么用处。

那我们在谷歌控制台看到的并行请求又是怎么一回事呢？

![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/edd7694a16204e268a9ae7e7f3c715b2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

如图所示，绿色部分代表请求发起到服务器响应的一个等待时间，而蓝色部分表示资源的下载时间。按照理论来说，HTTP响应理应当是前一个响应的资源下载完了，下一个响应的资源才能开始下载。而这里却出现了响应资源下载并行的情况。这又是为什么呢？

虽然HTTP/1.1支持管道化，但是服务器也必须进行逐个响应的送回，这个是很大的一个缺陷。实际上，现阶段的浏览器厂商采取了另外一种做法，它允许我们打开多个TCP的会话。也就是说，上图我们看到的并行，其实是不同的TCP连接上的HTTP请求和响应。这也就是我们所熟悉的浏览器对同域下并行加载6~8个资源的限制。而这，才是真正的并行！

此外，**HTTP/1.1还加入了[缓存处理](#cache)，新的字段如cache-control，支持断点传输，以及增加了Host字段（使得一个服务器能够用来创建多个Web站点）**。



#### HTTP (1.1) 优点？

HTTP 最凸出的优点是「简单、灵活和易于扩展、应用广泛和跨平台」。

#### HTTP (1.1) 缺点？

HTTP 协议里有优缺点一体的**双刃剑**，分别是「无状态、明文传输」，同时还有一大缺点「不安全」。

***1. 无状态双刃剑***

无状态的**好处**，因为服务器不会去记忆 HTTP 的状态，所以不需要额外的资源来记录状态信息，这能减轻服务器的负担，能够把更多的 CPU 和内存用来对外提供服务。

无状态的**坏处**，既然服务器没有记忆能力，它在完成有关联性的操作时会非常麻烦。

例如登录->添加购物车->下单->结算->支付，这系列操作都要知道用户的身份才行。但服务器不知道这些请求是有关联的，每次都要问一遍身份信息。

这样每操作一次，都要验证信息，这样的购物体验还能愉快吗？别问，问就是**酸爽**！

对于无状态的问题，解法方案有很多种，其中比较简单的方式用 **Cookie** 技术。

`Cookie` 通过在请求和响应报文中写入 Cookie 信息来控制客户端的状态。

相当于，**在客户端第一次请求后，服务器会下发一个装有客户信息的「小贴纸」，后续客户端请求服务器的时候，带上「小贴纸」，服务器就能认得了了**，

![Cookie 技术](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/14-cookie%E6%8A%80%E6%9C%AF.png)

***2. 明文传输双刃剑***

明文意味着在传输过程中的信息，**是可方便阅读的**，比如 Wireshark 抓包都可以直接肉眼查看，**为我们调试工作带了极大的便利性。**

但是这正是这样，HTTP 的所有信息都暴露在了光天化日下，相当于**信息裸奔**。在传输的漫长的过程中，信息的内容都毫无隐私可言，很容易就能被窃取，如果里面有你的账号密码信息，那**你号没了**。

***3. 不安全***

HTTP 比较严重的缺点就是不安全：

- 通信使用明文（不加密），内容可能会被窃听。比如，**账号信息容易泄漏，那你号没了。**
- 不验证通信方的身份，因此有可能遭遇伪装。比如，**访问假的淘宝、拼多多，那你钱没了。**
- 无法证明报文的完整性，所以有可能已遭篡改。比如，**网页上植入垃圾广告，视觉污染，眼没了。**

HTTP 的安全问题，可以用 HTTPS 的方式解决，也就是通过引入 SSL/TLS 层，使得在安全上达到了极致。

#### HTTP/1.1 性能？

HTTP 协议是基于 **TCP/IP**，并且使用了「**请求 - 应答**」的通信模式，所以性能的关键就在这**两点**里。

*1. 长连接*

早期 HTTP/1.0 性能上的一个很大的问题，那就是每发起一个请求，都要新建一次 TCP 连接（三次握手），而且是串行请求，做了无谓的 TCP 连接建立和断开，增加了通信开销。

为了解决上述 TCP 连接问题，HTTP/1.1 提出了**长连接**的通信方式，也叫持久连接。这种方式的好处在于减少了 TCP 连接的重复建立和断开所造成的额外开销，减轻了服务器端的负载。

持久连接的特点是，只要任意一端没有明确提出断开连接，则保持 TCP 连接状态。

![短连接与长连接](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/16-%E7%9F%AD%E8%BF%9E%E6%8E%A5%E4%B8%8E%E9%95%BF%E8%BF%9E%E6%8E%A5.png)

当然，如果某个 HTTP 长连接超过一定时间没有任何数据交互，服务端就会主动断开这个连接。

*2. 管道网络传输*

HTTP/1.1 采用了长连接的方式，这使得管道（pipeline）网络传输成为了可能。

即可在同一个 TCP 连接里面，客户端可以发起多个请求，只要第一个请求发出去了，不必等其回来，就可以发第二个请求出去，可以**减少整体的响应时间。**

举例来说，客户端需要请求两个资源。以前的做法是，在同一个 TCP 连接里面，先发送 A 请求，然后等待服务器做出回应，收到后再发出 B 请求。那么，管道机制则是允许浏览器同时发出 A 请求和 B 请求，如下图：

![管道网络传输](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/17-%E7%AE%A1%E9%81%93%E7%BD%91%E7%BB%9C%E4%BC%A0%E8%BE%93.png)

但是**服务器必须按照接收请求的顺序发送对这些管道化请求的响应**。

如果服务端在处理 A 请求时耗时比较长，那么后续的请求的处理都会被阻塞住，这称为「队头堵塞」。

所以，**HTTP/1.1 管道解决了请求的队头阻塞，但是没有解决响应的队头阻塞**。

>  TIP 注意!
>
> 实际上 HTTP/1.1 管道化技术不是默认开启，而且浏览器基本都没有支持，所以**后面所有文章讨论HTTP/1.1 都是建立在没有使用管道化的前提**。大家知道有这个功能，但是没有被使用就行了。

*3. 队头阻塞*

「请求 - 应答」的模式加剧了 HTTP 的性能问题。

因为当顺序发送的请求序列中的一个请求因为某种原因被阻塞时，在后面排队的所有请求也一同被阻塞了，会招致客户端一直请求不到数据，这也就是「**队头阻塞**」，好比上班的路上塞车。

![队头阻塞](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/18-%E9%98%9F%E5%A4%B4%E9%98%BB%E5%A1%9E.png)

总之 HTTP/1.1 的性能一般般，后续的 HTTP/2 和 HTTP/3 就是在优化 HTTP 的性能。



HTTP/1.1 相比 HTTP/1.0 性能上的改进：

- **使用长连接**的方式改善了 HTTP/1.0 短连接造成的性能开销。
- **支持管道（pipeline）网络传输**，只要第一个请求发出去了，不必等其回来，就可以发第二个请求出去，可以减少整体的响应时间。(试图解决队头阻塞问题但基本没被支持)

但 HTTP/1.1 还是有性能瓶颈：

- 请求 / 响应头部（Header）未经压缩就发送，首部信息越多延迟越大。只能压缩 `Body` 的部分；
- 发送冗长的首部。每次互相发送相同的首部造成的浪费较多；
- 服务器是按请求的顺序响应的，如果服务器响应慢，会招致客户端一直请求不到数据，也就是队头阻塞；
- 没有请求优先级控制；
- 请求只能从客户端开始，服务器只能被动响应。



#### HTTP/1.1 如何优化？

**第一个思路是，通过缓存技术来避免发送 HTTP 请求**。客户端收到第一个请求的响应后，可以将其缓存在本地磁盘，下次请求的时候，如果缓存没过期，就直接读取本地缓存的响应数据。如果缓存过期，客户端发送请求的时候带上响应数据的摘要，服务器比对后发现资源没有变化，就发出不带包体的 304 响应，告诉客户端缓存的响应仍然有效。

**第二个思路是，减少 HTTP 请求的次数，有以下的方法：**

1. 将原本由客户端处理的重定向请求，交给代理服务器处理，这样可以减少重定向请求的次数；
2. 将多个小资源合并成一个大资源再传输，能够减少 HTTP 请求次数以及 头部的重复传输，再来减少 TCP 连接数量，进而省去 TCP 握手和慢启动的网络消耗；
3. 按需访问资源，只访问当前用户看得到/用得到的资源，当客户往下滑动，再访问接下来的资源，以此达到延迟请求，也就减少了同一时间的 HTTP 请求次数。

**第三思路是，通过压缩响应资源，降低传输资源的大小**，从而提高传输效率，所以应当选择更优秀的压缩算法。

**无损压缩**

无损压缩是指资源经过压缩后，信息不被破坏，还能完全恢复到压缩前的原样，适合用在文本文件、程序可执行文件、程序源代码。

首先，我们针对代码的语法规则进行压缩，因为通常代码文件都有很多换行符或者空格，这些是为了帮助程序员更好的阅读，但是机器执行时并不要这些符，把这些多余的符号给去除掉。

接下来，就是无损压缩了，需要对原始资源建立统计模型，利用这个统计模型，将常出现的数据用较短的二进制比特序列表示，将不常出现的数据用较长的二进制比特序列表示，生成二进制比特序列一般是「霍夫曼编码」算法。

**gzip 就是比较常见的无损压缩。**客户端支持的压缩算法，会在 HTTP 请求中通过头部中的 `Accept-Encoding` 字段告诉服务器：

```text
Accept-Encoding: gzip, deflate, br
```

服务器收到后，会从中选择一个服务器支持的或者合适的压缩算法，然后使用此压缩算法对响应资源进行压缩，最后通过响应头部中的 `content-encoding` 字段告诉客户端该资源使用的压缩算法。

```text
content-encoding: gzip
```

**gzip 的压缩效率相比 Google 推出的 Brotli 算法还是差点意思，也就是上文中的 br，所以如果可以，服务器应该选择压缩效率更高的 br 压缩算法。**

**有损压缩**

与无损压缩相对的就是有损压缩，经过此方法压缩，解压的数据会与原始数据不同但是非常接近。

有损压缩主要将次要的数据舍弃，牺牲一些质量来减少数据量、提高压缩比，这种方法经常用于压缩多媒体数据，比如音频、视频、图片。

**可以通过 HTTP 请求头部中的 `Accept` 字段里的「 q 质量因子」，告诉服务器期望的资源质量。**

```text
Accept: audio/*; q=0.2, audio/basic
```

关于图片的压缩，目前压缩比较高的是 Google 推出的 **WebP 格式**，它与常见的 Png 格式图片的压缩比例对比如下图：

![来源于：https://isparta.github.io/compare-webp/index.html](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http1.1%E4%BC%98%E5%8C%96/webp%E4%B8%8Epng.png)

可以发现，相同图片质量下，WebP 格式的图片大小都比 Png 格式的图片小，所以对于大量图片的网站，可以考虑使用 WebP 格式的图片，这将大幅度提升网络传输的性能。

关于音视频的压缩，音视频主要是动态的，每个帧都有时序的关系，通常时间连续的帧之间的变化是很小的。

比如，一个在看书的视频，画面通常只有人物的手和书桌上的书是会有变化的，而其他地方通常都是静态的，于是只需要在一个静态的关键帧，使用**增量数据**来表达后续的帧，这样便减少了很多数据，提高了网络传输的性能。对于视频常见的编码格式有 H264、H265 等，音频常见的编码格式有 AAC、AC3。





不管怎么优化 HTTP/1.1 协议都是有限的，不然也不会出现 HTTP/2 和 HTTP/3 协议，后续我们再来介绍 HTTP/2 和 HTTP/3 协议。



#### HTTP/1.1 协议的性能问题？

我们得先要了解下 HTTP/1.1 协议存在的性能问题，因为 HTTP/2 协议就是把这些性能问题逐个攻破了。

现在的站点相比以前变化太多了，比如：

- *消息的大小变大了*，从几 KB 大小的消息，到几 MB 大小的消息；
- *页面资源变多了*，从每个页面不到 10 个的资源，到每页超 100 多个资源；
- *内容形式变多样了*，从单纯到文本内容，到图片、视频、音频等内容；
- *实时性要求变高了*，对页面的实时性要求的应用越来越多；

这些变化带来的最大性能问题就是 **HTTP/1.1 的高延迟**，延迟高必然影响的就是用户体验。主要原因如下几个：

- ***延迟难以下降***，虽然现在网络的「带宽」相比以前变多了，但是延迟降到一定幅度后，就很难再下降了，说白了就是到达了延迟的下限；
- ***并发连接有限***，谷歌浏览器最大并发连接数是 6 个，而且每一个连接都要经过 TCP 和 TLS 握手耗时，以及 TCP 慢启动过程给流量带来的影响；
- ***队头阻塞问题***，同一连接只能在完成一个 HTTP 事务（请求和响应）后，才能处理下一个事务；
- ***HTTP 头部巨大且重复***，由于 HTTP 协议是无状态的，每一个请求都得携带 HTTP 头部，特别是对于有携带 cookie 的头部，而 cookie 的大小通常很大；
- ***不支持服务器推送消息***，因此当客户端需要获取通知时，只能通过定时器不断地拉取消息，这无疑浪费大量了带宽和服务器资源。

为了解决 HTTP/1.1 性能问题，具体的优化手段你可以看这篇文章「[HTTP/1.1如何优化？ (opens new window)](https://xiaolincoding.com/network/2_http/http_optimize.html)」，这里我举例几个常见的优化手段：

- 将多张小图合并成一张大图供浏览器 JavaScript 来切割使用，这样可以将多个请求合并成一个请求，但是带来了新的问题，当某张小图片更新了，那么需要重新请求大图片，浪费了大量的网络带宽；
- 将图片的二进制数据通过 base64 编码后，把编码数据嵌入到 HTML 或 CSS 文件中，以此来减少网络请求次数；
- 将多个体积较小的 JavaScript 文件使用 webpack 等工具打包成一个体积更大的 JavaScript 文件，以一个请求替代了很多个请求，但是带来的问题，当某个 js 文件变化了，需要重新请求同一个包里的所有 js 文件；
- 将同一个页面的资源分散到不同域名，提升并发连接上限，因为浏览器通常对同一域名的 HTTP 连接最大只能是 6 个；

尽管对 HTTP/1.1 协议的优化手段如此之多，但是效果还是不尽人意，因为这些手段都是对 HTTP/1.1 协议的“外部”做优化，**而一些关键的地方是没办法优化的，比如请求-响应模型、头部巨大且重复、并发连接耗时、服务器不能主动推送等，要改变这些必须重新设计 HTTP 协议，于是 HTTP/2 就出来了！

#### HTTP/2 做了什么优化？

**头部压缩**

HTTP 协议的报文是由「Header + Body」构成的，对于 Body 部分，HTTP/1.1 协议可以使用头字段 「Content-Encoding」指定 Body 的压缩方式，比如用 gzip 压缩，这样可以节约带宽，但报文中的另外一部分 Header，是没有针对它的优化手段。

HTTP/1.1 报文中 Header 部分存在的问题：

- 含很多固定的字段，比如Cookie、User Agent、Accept 等，这些字段加起来也高达几百字节甚至上千字节，所以有必要**压缩**；
- 大量的请求和响应的报文里有很多字段值都是重复的，这样会使得大量带宽被这些冗余的数据占用了，所以有必须要**避免重复性**；
- 字段是 ASCII 编码的，虽然易于人类观察，但效率低，所以有必要改成**二进制编码**；

HTTP/2 对 Header 部分做了大改造，把以上的问题都解决了。

HTTP/2 没使用常见的 gzip 压缩方式来压缩头部，而是开发了 **HPACK** 算法，HPACK 算法主要包含三个组成部分：

- 静态字典；
- 动态字典；
- Huffman 编码（压缩算法）；

**客户端和服务器两端都会建立和维护「字典」，用长度较小的索引号表示重复的字符串，再用 Huffman 编码压缩数据，可达到 50%~90% 的高压缩率。**



静态表编码

HTTP/2 为高频出现在头部的字符串和字段建立了一张**静态表**，它是写入到 HTTP/2 框架里的，不会变化的，静态表里共有 `61` 组，如下图：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E9%9D%99%E6%80%81%E8%A1%A8.png)

表中的 `Index` 表示索引（Key），`Header Value` 表示索引对应的 Value，`Header Name` 表示字段的名字，比如 Index 为 2 代表 GET，Index 为 8 代表状态码 200。

你可能注意到，表中有的 Index 没有对应的 Header Value，这是因为这些 Value 并不是固定的而是变化的，这些 Value 都会经过 Huffman 编码后，才会发送出去。

这么说有点抽象，我们来看个具体的例子，下面这个 `server` 头部字段，在 HTTP/1.1 的形式如下：

```text
server: nghttpx\r\n
```

算上冒号空格和末尾的\r\n，共占用了 17 字节，**而使用了静态表和 Huffman 编码，可以将它压缩成 8 字节，压缩率大概 47 %**。

我抓了个 HTTP/2 协议的网络包，你可以从下图看到，高亮部分就是 `server` 头部字段，只用了 8 个字节来表示 `server` 头部数据。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E9%9D%99%E6%80%81%E7%BC%96%E7%A0%81.png)

根据 RFC7541 规范，如果头部字段属于静态表范围，并且 Value 是变化，那么它的 HTTP/2 头部前 2 位固定为 `01`，所以整个头部格式如下图：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E9%9D%99%E6%80%81%E5%A4%B4%E9%83%A8.png)

HTTP/2 头部由于基于**二进制编码**，就不需要冒号空格和末尾的\r\n作为分隔符，于是改用表示字符串长度（Value Length）来分割 Index 和 Value。

接下来，根据这个头部格式来分析上面抓包的 `server` 头部的二进制数据。

首先，从静态表中能查到 `server` 头部字段的 Index 为 54，二进制为 110110，再加上固定 01，头部格式第 1 个字节就是 `01110110`，这正是上面抓包标注的红色部分的二进制数据。

然后，第二个字节的首个比特位表示 Value 是否经过 Huffman 编码，剩余的 7 位表示 Value 的长度，比如这次例子的第二个字节为 `10000110`，首位比特位为 1 就代表 Value 字符串是经过 Huffman 编码的，经过 Huffman 编码的 Value 长度为 6。

最后，字符串 `nghttpx` 经过 Huffman 编码后压缩成了 6 个字节，Huffman 编码的原理是将高频出现的信息用「较短」的编码表示，从而缩减字符串长度。

于是，在统计大量的 HTTP 头部后，HTTP/2 根据出现频率将 ASCII 码编码为了 Huffman 编码表，可以在 RFC7541 文档找到这张**静态 Huffman 表**，我就不把表的全部内容列出来了，我只列出字符串 `nghttpx` 中每个字符对应的 Huffman 编码，如下图：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/nghttpx.png)

通过查表后，字符串 `nghttpx` 的 Huffman 编码在下图看到，共 6 个字节，每一个字符的 Huffman 编码，我用相同的颜色将他们对应起来了，最后的 7 位是补位的。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/nghttpx2.png)

最终，`server` 头部的二进制数据对应的静态头部格式如下：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E9%9D%99%E6%80%81%E5%A4%B4%E9%83%A82.png)



动态表编码

静态表只包含了 61 种高频出现在头部的字符串，不在静态表范围内的头部字符串就要自行构建**动态表**，它的 Index 从 `62` 起步，会在编码解码的时候随时更新。

比如，第一次发送时头部中的「`user-agent` 」字段数据有上百个字节，经过 Huffman 编码发送出去后，客户端和服务器双方都会更新自己的动态表，添加一个新的 Index 号 62。**那么在下一次发送的时候，就不用重复发这个字段的数据了，只用发 1 个字节的 Index 号就好了，因为双方都可以根据自己的动态表获取到字段的数据**。

所以，使得动态表生效有一个前提：**必须同一个连接上，重复传输完全相同的 HTTP 头部**。如果消息字段在 1 个连接上只发送了 1 次，或者重复传输时，字段总是略有变化，动态表就无法被充分利用了。

因此，随着在同一 HTTP/2 连接上发送的报文越来越多，客户端和服务器双方的「字典」积累的越来越多，理论上最终每个头部字段都会变成 1 个字节的 Index，这样便避免了大量的冗余数据的传输，大大节约了带宽。

理想很美好，现实很骨感。动态表越大，占用的内存也就越大，如果占用了太多内存，是会影响服务器性能的，因此 Web 服务器都会提供类似 `http2_max_requests` 的配置，用于限制一个连接上能够传输的请求数量，避免动态表无限增大，请求数量到达上限后，就会关闭 HTTP/2 连接来释放内存。

综上，HTTP/2 头部的编码通过「静态表、动态表、Huffman 编码」共同完成的。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E5%A4%B4%E9%83%A8%E7%BC%96%E7%A0%81.png)



**二进制帧**

HTTP/2 厉害的地方在于将 HTTP/1 的文本格式改成二进制格式传输数据，极大提高了 HTTP 传输效率，而且二进制数据使用位运算能高效解析。

你可以从下图看到，HTTP/1.1 的响应 和 HTTP/2 的区别：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E4%BA%8C%E8%BF%9B%E5%88%B6%E5%B8%A7.png)

HTTP/2 把响应报文划分成了两类帧（Frame），图中的 HEADERS（首部）和 DATA（消息负载） 是帧的类型，也就是说一条 HTTP 响应，划分成了两类帧来传输，并且采用二进制来编码。

比如状态码 200 ，在 HTTP/1.1 是用 '2''0''0' 三个字符来表示（二进制：00110010 00110000 00110000），共用了 3 个字节，如下图

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/http1.png)

在 HTTP/2 对于状态码 200 的二进制编码是 10001000，只用了 1 字节就能表示，相比于 HTTP/1.1 节省了 2 个字节，如下图：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/h2c.png)

Header: :status: 200 OK 的编码内容为：1000 1000，那么表达的含义是什么呢？

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/network/http/index.png)

1. 最前面的 1 标识该 Header 是静态表中已经存在的 KV。
2. 我们再回顾一下之前的静态表内容，“:status: 200 ok”其静态表编码是8，即1000。

因此，整体加起来就是 1000 1000。

HTTP/2 **二进制帧**的结构如下图：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E5%B8%A7%E6%A0%BC%E5%BC%8F.png)

帧头（Frame Header）很小，只有 9 个字节，帧开头的前 3 个字节表示帧数据（Frame Playload）的**长度**。

帧长度后面的一个字节是表示**帧的类型**，HTTP/2 总共定义了 10 种类型的帧，一般分为**数据帧**和**控制帧**两类，如下表格：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/%E5%B8%A7%E7%B1%BB%E5%9E%8B.png)

帧类型后面的一个字节是**标志位**，可以保存 8 个标志位，用于携带简单的控制信息，比如：

- **END_HEADERS** 表示头数据结束标志，相当于 HTTP/1 里头后的空行（“\r\n”）；
- **END_Stream** 表示单方向数据发送结束，后续不会再有数据帧。
- **PRIORITY** 表示流的优先级；

帧头的最后 4 个字节是**流标识符**（Stream ID），但最高位被保留不用，只有 31 位可以使用，因此流标识符的最大值是 2^31，大约是 21 亿，它的作用是用来标识该 Frame 属于哪个 Stream，接收方可以根据这个信息从乱序的帧里找到相同 Stream ID 的帧，从而有序组装信息。

最后面就是**帧数据**了，它存放的是通过 **HPACK 算法**压缩过的 HTTP 头部和包体。



**并发传输**

知道了 HTTP/2 的帧结构后，我们再来看看它是如何实现**并发传输**的。

我们都知道 HTTP/1.1 的实现是基于请求-响应模型的。**同一个连接中，HTTP 完成一个事务（请求与响应），才能处理下一个事务，也就是说在发出请求等待响应的过程中**，是没办法做其他事情的，如果响应迟迟不来，那么后续的请求是无法发送的，也造成了**队头阻塞**的问题。

而 HTTP/2 就很牛逼了，通过 Stream 这个设计，**多个 Stream 复用一条 TCP 连接，达到并发的效果**，解决了 HTTP/1.1 队头阻塞的问题，提高了 HTTP 传输的吞吐量。

为了理解 HTTP/2 的并发是怎样实现的，我们先来理解 HTTP/2 中的 Stream、Message、Frame 这 3 个概念。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/stream.png)

你可以从上图中看到：

- 1 个 TCP 连接包含一个或者多个 Stream，Stream 是 HTTP/2 并发的关键技术；
- Stream 里可以包含 1 个或多个 Message，Message 对应 HTTP/1 中的请求或响应，由 HTTP 头部和包体构成；
- Message 里包含一条或者多个 Frame，Frame 是 HTTP/2 最小单位，以二进制压缩格式存放 HTTP/1 中的内容（头部和包体）；

因此，我们可以得出个结论：多个 Stream 跑在一条 TCP 连接，同一个 HTTP 请求与响应是跑在同一个 Stream 中，HTTP 消息可以由多个 Frame 构成， 一个 Frame 可以由多个 TCP 报文构成。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/stream2.png)

在 HTTP/2 连接上，**不同 Stream 的帧是可以乱序发送的（因此可以并发不同的 Stream ）**，因为每个帧的头部会携带 Stream ID 信息，所以接收端可以通过 Stream ID 有序组装成 HTTP 消息，而**同一 Stream 内部的帧必须是严格有序的**。

比如下图，服务端**并行交错地**发送了两个响应： Stream 1 和 Stream 3，这两个 Stream 都是跑在一个 TCP 连接上，客户端收到后，会根据相同的 Stream ID 有序组装成 HTTP 消息。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/network/http/http2%E5%A4%9A%E8%B7%AF%E5%A4%8D%E7%94%A8.jpeg)

客户端和服务器**双方都可以建立 Stream**，因为服务端可以主动推送资源给客户端， 客户端建立的 Stream 必须是奇数号，而服务器建立的 Stream 必须是偶数号。

比如下图，Stream 1 是客户端向服务端请求的资源，属于客户端建立的 Stream，所以该 Stream 的 ID 是奇数（数字1）；Stream 2 和 4 都是服务端主动向客户端推送的资源，属于服务端建立的 Stream，所以这两个 Stream 的 ID 是偶数（数字 2 和4）。

![img](https://img-blog.csdnimg.cn/83445581dafe409d8cfd2c573b2781ac.png)

同一个连接中的 Stream ID 是不能复用的，只能顺序递增，所以当 Stream ID 耗尽时，需要发一个控制帧 `GOAWAY`，用来关闭 TCP 连接。

在 Nginx 中，可以通过 `http2_max_concurrent_Streams` 配置来设置 Stream 的上限，默认是 128 个。

HTTP/2 通过 Stream 实现的并发，比 HTTP/1.1 通过 TCP 连接实现并发要牛逼的多，**因为当 HTTP/2 实现 100 个并发 Stream 时，只需要建立一次 TCP 连接，而 HTTP/1.1 需要建立 100 个 TCP 连接，每个 TCP 连接都要经过TCP 握手、慢启动以及 TLS 握手过程，这些都是很耗时的。**

HTTP/2 还可以对每个 Stream 设置不同**优先级**，帧头中的「标志位」可以设置优先级，比如客户端访问 HTML/CSS 和图片资源时，希望服务器先传递 HTML/CSS，再传图片，那么就可以通过设置 Stream 的优先级来实现，以此提高用户体验。



**服务器主动推送资源**

HTTP/1.1 不支持服务器主动推送资源给客户端，都是由客户端向服务器发起请求后，才能获取到服务器响应的资源。

比如，客户端通过 HTTP/1.1 请求从服务器那获取到了 HTML 文件，而 HTML 可能还需要依赖 CSS 来渲染页面，这时客户端还要再发起获取 CSS 文件的请求，需要两次消息往返，如下图左边部分：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/push.png)

如上图右边部分，在 HTTP/2 中，客户端在访问 HTML 时，服务器可以直接主动推送 CSS 文件，减少了消息传递的次数。

在 Nginx 中，如果你希望客户端访问 /test.html 时，服务器直接推送 /test.css，那么可以这么配置：

```nginx
location /test.html { 
  http2_push /test.css; 
}
```

那 HTTP/2 的推送是怎么实现的？

客户端发起的请求，必须使用的是奇数号 Stream，服务器主动的推送，使用的是偶数号 Stream。服务器在推送资源时，会通过 `PUSH_PROMISE` 帧传输 HTTP 头部，并通过帧中的 `Promised Stream ID` 字段告知客户端，接下来会在哪个偶数号 Stream 中发送包体。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http2/push2.png)

如上图，在 Stream 1 中通知客户端 CSS 资源即将到来，然后在 Stream 2 中发送 CSS 资源，注意 Stream 1 和 2 是可以**并发**的。



#### HTTP/2 如何兼容 HTTP/1.1？

HTTP/2 出来的目的是为了改善 HTTP 的性能。协议升级有一个很重要的地方，就是要**兼容**老版本的协议，否则新协议推广起来就相当困难，所幸 HTTP/2 做到了兼容 HTTP/1.1 。

那么，HTTP/2 是怎么做的呢？

第一点，HTTP/2 没有在 URI 里引入新的协议名，仍然用「http://」表示明文协议，用「https://」表示加密协议，于是只需要浏览器和服务器在背后自动升级协议，这样可以让用户意识不到协议的升级，很好的实现了协议的平滑升级。

第二点，只在应用层做了改变，还是基于 TCP 协议传输，应用层方面为了保持功能上的兼容，HTTP/2 把 HTTP 分解成了「语义」和「语法」两个部分，「语义」层不做改动，与 HTTP/1.1 完全一致，比如请求方法、状态码、头字段等规则保留不变。

但是，HTTP/2 在「语法」层面做了很多改造，基本改变了 HTTP 报文的传输格式。



#### HTTP/2 有什么缺陷？

HTTP/2 通过头部压缩、二进制编码、多路复用、服务器推送等新特性大幅度提升了 HTTP/1.1 的性能，而美中不足的是 HTTP/2 协议是基于 TCP 实现的，于是存在的缺陷有三个。

- 队头阻塞；
- TCP 与 TLS 的握手时延迟；
- 网络迁移需要重新连接；



**队头阻塞**

HTTP/2 多个请求是跑在一个 TCP 连接中的，那么当 TCP 丢包时，整个 TCP 都要等待重传，那么就会阻塞该 TCP 连接中的所有请求。

比如下图中，Stream 2 有一个 TCP 报文丢失了，那么即使收到了 Stream 3 和 Stream 4 的 TCP 报文，应用层也是无法读取读取的，相当于阻塞了 Stream 3 和 Stream 4 请求。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/network/quic/http2%E9%98%BB%E5%A1%9E.jpeg)

因为 TCP 是字节流协议，TCP 层必须保证收到的字节数据是完整且有序的，如果序列号较低的 TCP 段在网络传输中丢失了，即使序列号较高的 TCP 段已经被接收了，**应用层也无法从内核中读取到这部分数据，从 HTTP 视角看，就是请求被阻塞了。**

举个例子，如下图：

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http3/tcp%E9%98%9F%E5%A4%B4%E9%98%BB%E5%A1%9E.gif)

图中发送方发送了很多个 packet，每个 packet 都有自己的序号，你可以认为是 TCP 的序列号，其中 packet 3 在网络中丢失了，即使 packet 4-6 被接收方收到后，由于内核中的 TCP 数据不是连续的，于是接收方的应用层就无法从内核中读取到，只有等到 packet 3 重传后，接收方的应用层才可以从内核中读取到数据，**这就是 HTTP/2 的队头阻塞问题，是在 TCP 层面发生的。**



**TCP 与 TLS 的握手时延迟**

发起 HTTP 请求时，需要经过 TCP 三次握手和 TLS 四次握手（TLS 1.2）的过程，因此共需要 3 个 RTT 的时延才能发出请求数据。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http3/TCP%2BTLS.gif)

另外， TCP 由于具有「拥塞控制」的特性，所以刚建立连接的 TCP 会有个「慢启动」的过程，它会对 TCP 连接产生"减速"效果。



**网络迁移需要重新连接**

一个 TCP 连接是由四元组（源 IP 地址，源端口，目标 IP 地址，目标端口）确定的，这意味着如果 IP 地址或者端口变动了，就会导致需要 TCP 与 TLS 重新握手，这不利于移动设备切换网络的场景，比如 4G 网络环境切换成 WIFI。

这些问题都是 TCP 协议固有的问题，无论应用层的 HTTP/2 在怎么设计都无法逃脱。要解决这个问题，就必须把**传输层协议替换成 UDP**，这个大胆的决定，HTTP/3 做了！

![HTTP/1 ~ HTTP/3](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/27-HTTP3.png)





#### QUIC 协议特点 ？

HTTP/2 队头阻塞的问题是因为 TCP，所以 **HTTP/3 把 HTTP 下层的 TCP 协议改成了 UDP！**

![HTTP/1 ~ HTTP/3](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/27-HTTP3.png)

UDP 发送是不管顺序，也不管丢包的，所以不会出现像 HTTP/2 队头阻塞的问题。大家都知道 UDP 是不可靠传输的，但基于 UDP 的 **QUIC 协议** 可以实现类似 TCP 的可靠性传输。

QUIC 有以下 3 个特点。

- 无队头阻塞
- 更快的连接建立
- 连接迁移

*1、无队头阻塞*

QUIC 协议也有类似 HTTP/2 Stream 与多路复用的概念，也是可以在同一条连接上并发传输多个 Stream，Stream 可以认为就是一条 HTTP 请求。

QUIC 有自己的一套机制可以保证传输的可靠性的。**当某个流发生丢包时，只会阻塞这个流，其他流不会受到影响，因此不存在队头阻塞问题**。这与 HTTP/2 不同，HTTP/2 只要某个流中的数据包丢失了，其他流也会因此受影响。

所以，QUIC 连接上的多个 Stream 之间并没有依赖，都是独立的，某个流发生丢包了，只会影响该流，其他流不受影响。

![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/network/quic/quic%E6%97%A0%E9%98%BB%E5%A1%9E.jpeg)

*2、更快的连接建立*

对于 HTTP/1 和 HTTP/2 协议，TCP 和 TLS 是分层的，分别属于内核实现的传输层、openssl 库实现的表示层，因此它们难以合并在一起，需要分批次来握手，先 TCP 握手，再 TLS 握手。

HTTP/3 在传输数据前虽然需要 QUIC 协议握手，这个握手过程只需要 1 RTT，握手的目的是为确认双方的「连接 ID」，连接迁移就是基于连接 ID 实现的。

但是 HTTP/3 的 QUIC 协议并不是与 TLS 分层，而是QUIC 内部包含了 TLS，它在自己的帧会携带 TLS 里的“记录”，再加上 QUIC 使用的是 TLS/1.3，因此仅需 1 个 RTT 就可以「同时」完成建立连接与密钥协商，如下图：

![TCP HTTPS（TLS/1.3） 和 QUIC HTTPS ](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/HTTP/28-HTTP3%E4%BA%A4%E4%BA%92%E6%AC%A1%E6%95%B0.png)

甚至，在第二次连接的时候，应用数据包可以和 QUIC 握手信息（连接信息 + TLS 信息）一起发送，达到 0-RTT 的效果。

如下图右边部分，HTTP/3 当会话恢复时，有效负载数据与第一个数据包一起发送，可以做到 0-RTT（下图的右下角）：

![img](https://img-blog.csdnimg.cn/4cad213f5125432693e0e2a512c2d1a1.png)

*3、连接迁移*

基于 TCP 传输协议的 HTTP 协议，由于是通过四元组（源 IP、源端口、目的 IP、目的端口）确定一条 TCP 连接。

![TCP 四元组](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9jZG4uanNkZWxpdnIubmV0L2doL3hpYW9saW5jb2Rlci9JbWFnZUhvc3QyLyVFOCVBRSVBMSVFNyVBRSU5NyVFNiU5QyVCQSVFNyVCRCU5MSVFNyVCQiU5Qy9UQ1AtJUU0JUI4JTg5JUU2JUFDJUExJUU2JThGJUExJUU2JTg5JThCJUU1JTkyJThDJUU1JTlCJTlCJUU2JUFDJUExJUU2JThDJUE1JUU2JTg5JThCLzEwLmpwZw?x-oss-process=image/format,png)

那么**当移动设备的网络从 4G 切换到 WIFI 时，意味着 IP 地址变化了，那么就必须要断开连接，然后重新建立连接**。而建立连接的过程包含 TCP 三次握手和 TLS 四次握手的时延，以及 TCP 慢启动的减速过程，给用户的感觉就是网络突然卡顿了一下，因此连接的迁移成本是很高的。

而 QUIC 协议没有用四元组的方式来“绑定”连接，而是通过**连接 ID**来标记通信的两个端点，客户端和服务器可以各自选择一组 ID 来标记自己，因此即使移动设备的网络变化后，导致 IP 地址变化了，只要仍保有上下文信息（比如连接 ID、TLS 密钥等），就可以“无缝”地复用原连接，消除重连的成本，没有丝毫卡顿感，达到了**连接迁移**的功能。

所以， QUIC 是一个在 UDP 之上的**伪** TCP + TLS + HTTP/2 的多路复用的协议。

QUIC 是新协议，对于很多网络设备，根本不知道什么是 QUIC，只会当做 UDP，这样会出现新的问题，因为有的网络设备是会丢掉 UDP 包的，而 QUIC 是基于UDP 实现的，那么如果网络设备无法识别这个是 QUIC 包，那么就会当作 UDP包，然后被丢弃。



#### HTTP/3 ？

来看看 HTTP/3 协议在 HTTP 这一层做了什么变化。

HTTP/3 同 HTTP/2 一样采用二进制帧的结构，不同的地方在于 HTTP/2 的二进制帧里需要定义 Stream，而 HTTP/3 自身不需要再定义 Stream，直接使用 QUIC 里的 Stream**，于是 HTTP/3 的帧的结构也变简单了。** ![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/http3/http3frame.png)

从上图可以看到，HTTP/3 帧头只有两个字段：类型和长度。

根据帧类型的不同，大体上分为数据帧和控制帧两大类，HEADERS 帧（HTTP 头部）和 DATA 帧（HTTP 包体）属于数据帧。

HTTP/3 在头部压缩算法这一方面也做了升级，升级成了 **QPACK**。与 HTTP/2 中的 HPACK 编码方式相似，HTTP/3 中的 QPACK 也采用了静态表、动态表及 Huffman 编码。

对于静态表的变化，HTTP/2 中的 HPACK 的静态表只有 61 项，而 HTTP/3 中的 QPACK 的静态表扩大到 91 项。

HTTP/2 和 HTTP/3 的 Huffman 编码并没有多大不同，但是动态表编解码方式不同。

所谓的动态表，在首次请求-响应后，双方会将未包含在静态表中的 Header 项更新各自的动态表，接着后续传输时仅用 1 个数字表示，然后对方可以根据这 1 个数字从动态表查到对应的数据，就不必每次都传输长长的数据，大大提升了编码效率。

可以看到，**动态表是具有时序性的，如果首次出现的请求发生了丢包，后续的收到请求，对方就无法解码出 HPACK 头部，因为对方还没建立好动态表，因此后续的请求解码会阻塞到首次请求中丢失的数据包重传过来**。

HTTP/3 的 QPACK 解决了这一问题，那它是如何解决的呢？

QUIC 会有两个特殊的单向流，所谓的单向流只有一端可以发送消息，双向则指两端都可以发送消息，传输 HTTP 消息时用的是双向流，这两个单向流的用法：

- 一个叫 QPACK Encoder Stream， 用于将一个字典（key-value）传递给对方，比如面对不属于静态表的 HTTP 请求头部，客户端可以通过这个 Stream 发送字典；
- 一个叫 QPACK Decoder Stream，用于响应对方，告诉它刚发的字典已经更新到自己的本地动态表了，后续就可以使用这个字典来编码了。

这两个特殊的单向流是用来**同步双方的动态表**，编码方收到解码方更新确认的通知后，才使用动态表编码 HTTP 头部。



### HTTP 与其他协议

#### 有了 HTTP 为什么还要 RPC？



#### 有了 HTTP 为什么还要 websocket？



### Cookie、Session、Token

https://cloud.tencent.com/developer/article/1704064

HTTP 协议是一种`无状态协议`，即每次服务端接收到客户端的请求时，都是一个全新的请求，[服务器](https://cloud.tencent.com/product/cvm?from=10680)并不知道客户端的历史请求记录；Session 和 Cookie 的主要目的就是为了弥补 HTTP 的无状态特性。

![img](https://ask.qcloudimg.com/http-save/yehe-7022588/v546ndidb8.png?imageView2/2/w/1620)

#### 什么是 Session？

**Session 是什么**

客户端请求服务端，服务端会为这次请求开辟一块`内存空间`，这个对象便是 Session 对象，存储结构为 `ConcurrentHashMap`。Session 弥补了 HTTP 无状态特性，服务器可以利用 Session 存储客户端在同一个会话期间的一些操作记录。

**Session 如何判断是否是同一会话**

服务器第一次接收到请求时，开辟了一块 Session 空间（创建了Session对象），同时生成一个 sessionId ，并通过响应头的 **Set-Cookie：JSESSIONID=XXXXXXX **命令，向客户端发送要求设置 Cookie 的响应； 客户端收到响应后，在本机客户端设置了一个 **JSESSIONID=XXXXXXX **的 Cookie 信息，该 Cookie 的过期时间为浏览器会话结束；

![img](https://ask.qcloudimg.com/http-save/yehe-7022588/fd22y7mcf1.png?imageView2/2/w/1620)

接下来客户端每次向同一个网站发送请求时，请求头都会带上该 Cookie信息（包含 sessionId ）， 然后，服务器通过读取请求头中的 Cookie 信息，获取名称为 JSESSIONID 的值，得到此次请求的 sessionId。

**Session 的缺点**

Session 机制有个缺点，比如 A 服务器存储了 Session，就是做了负载均衡后，假如一段时间内 A 的访问量激增，会转发到 B 进行访问，但是 B 服务器并没有存储 A 的 Session，会导致 Session 的失效。

#### 什么是 Cookie？

HTTP 协议中的 Cookie 包括 `Web Cookie` 和`浏览器 Cookie`，它是服务器发送到 Web 浏览器的一小块数据。服务器发送到浏览器的 Cookie，浏览器会进行存储，并与下一个请求一起发送到服务器。通常，它用于判断两个请求是否来自于同一个浏览器，例如用户保持登录状态。

- HTTP Cookie 机制是 HTTP 协议无状态的一种补充和改良

Cookie 主要用于下面三个目的

- `会话管理`：登陆、购物车、游戏得分或者服务器应该记住的其他内容

- `个性化`：用户偏好、主题或者其他设置

- `追踪`：记录和分析用户行为

Cookie 曾经用于一般的客户端存储。虽然这是合法的，因为它们是在客户端上存储数据的唯一方法，但如今建议使用现代存储 API。Cookie 随每个请求一起发送，因此它们可能会降低性能（尤其是对于移动数据连接而言）。

**创建 Cookie**

当接收到客户端发出的 HTTP 请求时，服务器可以发送带有响应的 `Set-Cookie` 标头，Cookie 通常由浏览器存储，然后将 Cookie 与 HTTP 标头一同向服务器发出请求。

**Set-Cookie 和 Cookie 标头**

`Set-Cookie` HTTP 响应标头将 cookie 从服务器发送到用户代理。下面是一个发送 Cookie 的例子

![img](https://ask.qcloudimg.com/http-save/yehe-7022588/oap79bdexv.png)

此标头告诉客户端存储 Cookie

现在，随着对服务器的每个新请求，浏览器将使用 Cookie 头将所有以前存储的 Cookie 发送回服务器。

![img](https://ask.qcloudimg.com/http-save/yehe-7022588/qb7z1wrrgz.png)

有两种类型的 Cookies，一种是 Session Cookies，一种是 Persistent Cookies，如果 Cookie 不包含到期日期，则将其视为会话 Cookie。会话 Cookie 存储在内存中，永远不会写入磁盘，当浏览器关闭时，此后 Cookie 将永久丢失。如果 Cookie 包含`有效期` ，则将其视为持久性 Cookie。在到期指定的日期，Cookie 将从磁盘中删除。

还有一种是 `Cookie的 Secure 和 HttpOnly 标记`，下面依次来介绍一下

**会话 Cookies**（Session Cookie)

上面的示例创建的是会话 Cookie ，会话 Cookie 有个特征，客户端关闭时 Cookie 会删除，因为它没有指定`Expires`或 `Max-Age` 指令。

但是，Web 浏览器可能会使用会话还原，这会使大多数会话 Cookie 保持永久状态，就像从未关闭过浏览器一样。



**永久性 Cookies**

永久性 Cookie 不会在客户端关闭时过期，而是在`特定日期（Expires）`或`特定时间长度（Max-Age）`外过期。例如

```javascript
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

**Cookie 的 Secure 和 HttpOnly 标记**

安全的 Cookie 需要经过 HTTPS 协议通过加密的方式发送到服务器。即使是安全的，也不应该将敏感信息存储在cookie 中，因为它们本质上是不安全的，并且此标志不能提供真正的保护。

**HttpOnly 的作用**

-  会话 Cookie 中缺少 HttpOnly 属性会导致攻击者可以通过程序(JS脚本、Applet等)获取到用户的 Cookie 信息，造成用户 Cookie 信息泄露，增加攻击者的跨站脚本攻击威胁。 
-  HttpOnly 是微软对 Cookie 做的扩展，该值指定 Cookie 是否可通过客户端脚本访问。 
-  如果在 Cookie 中没有设置 HttpOnly 属性为 true，可能导致 Cookie 被窃取。窃取的 Cookie 可以包含标识站点用户的敏感信息，如 ASP.NET 会话 ID 或 Forms [身份验证](https://cloud.tencent.com/product/mfas?from=10680)票证，攻击者可以重播窃取的 Cookie，以便伪装成用户或获取敏感信息，进行跨站脚本攻击等。 

**Cookie 的作用域**

`Domain` 和 `Path` 标识定义了 Cookie 的作用域：即 Cookie 应该发送给哪些 URL。

`Domain` 标识指定了哪些主机可以接受 Cookie。如果不指定，默认为当前主机(不包含子域名）。如果指定了`Domain`，则一般包含子域名。

例如，如果设置 `Domain=mozilla.org`，则 Cookie 也包含在子域名中（如`developer.mozilla.org`）。

例如，设置 `Path=/docs`，则以下地址都会匹配：

- `/docs`
- `/docs/Web/`
- `/docs/Web/HTTP`

#### 什么是 JSON Web Token？

**token** 令牌，是用户身份的验证方式。 最简单的token组成:uid(用户唯一的身份标识)、time（当前时间的时间戳）、sign（签名）。 **对Token认证的五点认识**

- 一个Token就是一些信息的集合；

- 在Token中包含足够多的信息，以便在后续请求中减少查询[数据库](https://cloud.tencent.com/solution/database?from=10680)的几率；

- 服务端需要对cookie和HTTP Authrorization Header进行Token信息的检查；

- 基于上一点，你可以用一套token认证代码来面对浏览器类客户端和非浏览器类客户端；

- 因为token是被签名的，所以我们可以认为一个可以解码认证通过的token是由我们系统发放的，其中带的信息是合法有效的；

	

客户端A访问服务器，服务器给了客户端token，客户端A拿着token访问服务器，服务器验证token，返回数据。

![img](https://pic4.zhimg.com/v2-56ce64b6910836876d7476824f7bee0b_b.jpg)



Json Web Token 的简称就是 JWT，通常可以称为 `Json 令牌`。它是`RFC 7519` 中定义的用于`安全的`将信息作为 `Json 对象`进行传输的一种形式。JWT 中存储的信息是经过`数字签名`的，因此可以被信任和理解。可以使用 HMAC 算法或使用 RSA/ECDSA 的公用/专用密钥对 JWT 进行签名。

使用 JWT 主要用来下面两点

- `认证(Authorization)`：这是使用 JWT 最常见的一种情况，一旦用户登录，后面每个请求都会包含 JWT，从而允许用户访问该令牌所允许的路由、服务和资源。`单点登录`是当今广泛使用 JWT 的一项功能，因为它的开销很小。
- `信息交换(Information Exchange)`：JWT 是能够安全传输信息的一种方式。通过使用公钥/私钥对 JWT 进行签名认证。此外，由于签名是使用 `head` 和 `payload` 计算的，因此你还可以验证内容是否遭到篡改。

**JWT 的格式**

下面，我们会探讨一下 JWT 的组成和格式是什么

JWT 主要由三部分组成，每个部分用 `.` 进行分割，各个部分分别是

- `Header`
- `Payload`
- `Signature`

因此，一个非常简单的 JWT 组成会是下面这样

![img](https://ask.qcloudimg.com/http-save/yehe-7022588/ebdlw0iodk.png)

然后我们分别对不同的部分进行探讨。

**Header**

Header 是 JWT 的标头，它通常由两部分组成：`令牌的类型(即 JWT)`和使用的 `签名算法`，例如 HMAC SHA256 或 RSA。

例如

```javascript
{
  "alg": "HS256",
  "typ": "JWT"
}
```

指定类型和签名算法后，Json 块被 `Base64Url` 编码形成 JWT 的第一部分。

**Payload**

Token 的第二部分是 `Payload`，Payload 中包含一个声明。声明是有关实体（通常是用户）和其他数据的声明。共有三种类型的声明：**registered, public 和 private** 声明。

- `registered 声明`： 包含一组建议使用的预定义声明，主要包括

| ISS                   | 签发人   |
| :-------------------- | :------- |
| iss (issuer)          | 签发人   |
| exp (expiration time) | 过期时间 |
| sub (subject)         | 主题     |
| aud (audience)        | 受众     |
| nbf (Not Before)      | 生效时间 |
| iat (Issued At)       | 签发时间 |
| jti (JWT ID)          | 编号     |

- `public 声明`：公共的声明，可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息，但不建议添加敏感信息，因为该部分在客户端可解密。
- `private 声明`：自定义声明，旨在在同意使用它们的各方之间共享信息，既不是注册声明也不是公共声明。

例如

```javascript
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```

然后 payload Json 块会被`Base64Url` 编码形成 JWT 的第二部分。

**signature**

JWT 的第三部分是一个签证信息，这个签证信息由三部分组成

- header (base64后的)
- payload (base64后的)
- secret

比如我们需要 HMAC SHA256 算法进行签名

```javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

签名用于验证消息在此过程中没有更改，并且对于使用私钥进行签名的令牌，它还可以验证 JWT 的发送者的真实身份

**拼凑在一起**

现在我们把上面的三个由点分隔的 Base64-URL 字符串部分组成在一起，这个字符串可以在 HTML 和 HTTP 环境中轻松传递这些字符串。

下面是一个完整的 JWT 示例，它对 header 和 payload 进行编码，然后使用 signature 进行签名

```javascript
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

复制

![img](https://ask.qcloudimg.com/http-save/yehe-7022588/o401vy1pn.png)

如果想自己测试编写的话，可以访问 JWT [官网](https://jwt.io/#debugger-io) 



#### Cookie 和 Session 的区别？

- 数据存放位置不同：Session数据是存在服务器中的，cookie数据存放在浏览器当中。
- 安全程度不同：cookie放在服务器中不是很安全，session放在服务器中，相对安全。
- 性能使用程度不同：session放在服务器上，访问增多会占用服务器的性能；考虑到减轻服务器性能方面，应使用cookie。
- 数据存储大小不同：单个cookie保存的数据不能超过4K，session存储在服务端，根据服务器大小来定。

#### 

#### JWT 和 Session Cookies 的不同？

JWT 和 Session Cookies 都提供安全的用户身份验证，但是它们有以下几点不同

**密码签名**

JWT 具有加密签名，而 Session Cookies 则没有。

**JSON 是无状态的**

JWT 是`无状态`的，因为声明被存储在`客户端`，而不是服务端内存中。

身份验证可以在`本地`进行，而不是在请求必须通过服务器数据库或类似位置中进行。 这意味着可以对用户进行多次身份验证，而无需与站点或应用程序的数据库进行通信，也无需在此过程中消耗大量资源。

**可扩展性**

Session Cookies 是存储在服务器内存中，这就意味着如果网站或者应用很大的情况下会耗费大量的资源。由于 JWT 是无状态的，在许多情况下，它们可以节省服务器资源。因此 JWT 要比 Session Cookies 具有更强的`可扩展性`。

**JWT 支持跨域认证**

Session Cookies 只能用在`单个节点的域`或者它的`子域`中有效。如果它们尝试通过第三个节点访问，就会被禁止。如果你希望自己的网站和其他站点建立安全连接时，这是一个问题。

使用 JWT 可以解决这个问题，使用 JWT 能够通过`多个节点`进行用户认证，也就是我们常说的`跨域认证`。

**JWT 和 Session Cookies 的选型**

对于只需要登录用户并访问存储在站点数据库中的一些信息的中小型网站来说，Session Cookies 通常就能满足。

如果你有企业级站点，应用程序或附近的站点，并且需要处理大量的请求，尤其是第三方或很多第三方（包括位于不同域的API），则 JWT 显然更适合。
