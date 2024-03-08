# API 范式



![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/38d2cb92929e4d8b80b03d894b30e56c~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)



## RPC(Remote Procedure Call)

### 简介

RPC 出现的最初目的，就是**为了让计算机能够跟调用本地方法一样去调用远程方法**，我们可以简单理解为一个本地方法调用+网络通信



这边可以扯一下《凤凰架构》里的讨论

1. 单个进程调用方法（普通的方法调用）

    * 传递方法参数
    * 确定方法版本
    * 执行被调方法
    * 返回执行结果

2. 不同进程调用方法（进程间通信 IPC 来解决两个进程数据交换问题）

    * 经典进程间通信八股

3. 跨机器进程间通信

    * Socket

    * RPC 最开始设计也是类似的，**为了让计算机能够跟调用本地方法一样去调用远程方法，看起来透明的，当作 IPC 的特例**

        > 大家不妨来反思一下：开发一个分布式系统，是不是就一定要用 RPC 呢？RPC 的三大问题源自于对本地方法调用的类比模拟，如果我们把思维从“方法调用”的约束中挣脱，那参数与结果如何表示、方法如何表示、数据如何传递这些问题都会海阔天空，拥有焕然一新的视角。但是我们写程序，真的可能不面向方法来编程吗？这就是笔者下一节准备谈的话题了。《凤凰架构》

    

问题：看起来透明的远程调用通信方式容易让人误以为通信是无成本的假象。另外还有很多问题



1987 年，在“透明的 RPC 调用”一度成为主流范式的时候，Andrew Tanenbaum 教授曾发表了论文《[A Critique of The Remote Procedure Call Paradigm](https://www.cs.vu.nl/~ast/Publications/Papers/euteco-1988.pdf)》，对这种透明的 RPC 范式提出了一系列质问：

- 两个进程通信，谁作为服务端，谁作为客户端？
- 怎样进行异常处理？异常该如何让调用者获知？
- 服务端出现多线程竞争之后怎么办？
- 如何提高网络利用的效率，譬如连接是否可被多个请求复用以减少开销？是否支持多播？
- 参数、返回值如何表示？应该有怎样的字节序？
- 如何保证网络的可靠性？譬如调用期间某个链接忽然断开了怎么办？
- 发送的请求服务端收不到回复该怎么办？
- ……



论文的中心观点是：**本地调用与远程调用当做一样处理，这是犯了方向性的错误，把系统间的调用做成透明，反而会增加程序员工作的复杂度。**



### 三个基本问题

各种 RPC 基本都是在变着花样用各种手段来解决以下三个基本问题

* 如何表示数据
    * 数据包含：传递给方法的参数，返回值
    * 跨语言、硬件指令集、操作系统
    * 有效的做法是将交互双方所涉及的数据转换为某种事先约定好的中立数据流格式来进行传输，将数据流转换回不同语言中对应的数据类型来进行使用（序列化与反序列化）。
        * gRPC 的 Protocol Buffers
        * Web Service 的 XML Serialization
        * 众多轻量级 RPC 支持的 JSON Serialization
* 如何传递数据
    * 如何通过网络，在两个服务的 Endpoint 之间相互操作、交换数据
    * 交换数据一般指应用层协议，实际还是基于 TCP、UDP 之类的
    * 交互不光是序列化数据流表示参数和结果
        * 还要考虑异常、超时、安全、认证、授权、事务等等
        * 这种行为叫 Wire Protocol
    * 例如
        * Web Service 的 Simple Object Access Protocol（SOAP）
        * JSON-RPC（简单的话双方都 HTTP Endpoint，直接 HTTP）
* 如何确定方法
    * 要跨语言，不同语言方法签名不同
    * 方法签名唯一、不重复 ID，直接通过 ID 确定语言
        * UUID
    * 语言无关的接口描述语言 (Interface Description Language，IDL)
        * Web Service 的 Web Service Description Language（WSDL)
        * JOSON-RPC 的 JSON Web Service Protocol（JSON-WSP)



### RPC 统一与分裂

RPC 只是一个概念或者说调用方式，实际有很多规范和对应的实现，那些具体实现才是协议



#### 试图统一

DCE/RPC，ONC RPC 

* 浓厚的 Unix 痕迹，但没在 Unix 之外的系统流行
* 没面向对象

CORBA

* 支持跨进程、跨语言（面向异构语言）、面向对象
* 太啰嗦了，晦涩难懂，各语言厂商解读都不一样，没有很好地实现所宣称的面向异构语言

W3C 管理的 Web Service 

* 不再属于哪个公司
* 采用了 XML 作为远程过程调用的序列化、接口描述、服务发现等所有编码的载体
* 不需要程序员手工去编写对象的描述和服务代理
* 缺点
    * XML 作为一门描述性语言本身信息密度就相对低下，如果要严谨描述要存比原有字段多很多的空间，这导致
        * Web Service 要有专门客户端去调用和解析 SOAP 内容
        * 要有专门服务器去部署
        * 每一次数据交互都包含大量冗余信息，性能差
    * 太贪婪了，希望在一套协议上解决分布式计算中可能遇到的所有问题，衍生出一个家族的协议，学习负担重

#### 走向分裂

一直没有同时满足简单、普适、高性能三点要求的 完美 RPC 协议出现，所以开始百家争鸣了

或许不追求大而全的完美 RPC 才更适合现阶段的发展，每个 RPC 都有自己针对性的特点



* 面向对象方向发展（分布式对象）
    * 不满足于 RPC 将面向过程的编码方式带到分布式，希望在分布式系统中也能够进行跨进程的面向对象编程
    * 代表：RMI、.NET Remoting、DCOM
* 性能方向发展
    * RPC 性能的两个因素
        * 序列化效率：序列化输出结果的容量越小，速度越快，效率自然越高
        * 信息密度：取决于协议中有效荷载（Payload）所占总传输数据的比例大小，使用传输协议的层次越高，信息密度就越低，SOAP 使用 XML 拙劣的性能表现就是前车之鉴
    * 例如
        * Google 的 gRPC ：基于 HTTP/2 的，支持多路复用和 Header 压缩
        * FaceBook/Apache 的 Thrift：直接基于传输层的 TCP 协议来实现，省去很多应用层协议的开销
* 简化方向发展
    * JSON-RPC：牺牲了功能和效率，换来的是协议的简单轻便，接口与格式都更为通用，尤其适合用于 Web 浏览器这类一般不会有额外协议支持、额外客户端支持的应用场合。
* 更高的层次
    * 不仅仅负责调用远程服务，还管理远程服务，提供负载均衡、服务注册、可观察性等方面地支持
    * 插件化，不再赘有独立地解决 RPC 地全部三个问题，设计扩展点让用户自己选择
    * 例如 Dubbo、Thrift





### 工作过程

客户端调用远程过程，将参数和附加信息序列化为消息，并将消息发送到服务器。收到消息后，服务器反序列化其内容，执行请求的操作，并将结果发送回客户端。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b9bf93fdb41747a78146704369a37949~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

### 优点

> 实现 RPC 的可以传输协议可以直接建立在 TCP 之上，也可以建立在 HTTP 协议之上。**大部分 RPC 框架都是使用的 TCP 连接（gRPC使用了HTTP2）。**

- 调用简单，清晰，透明，不用像 rest 一样复杂，就像调用本地方法一样简单（同样也是缺点，就是后续提到的耦合度强）
- 高效低延迟，性能高
- **使用自定义 TCP 协议进行传输可以极大地减轻了传输数据的开销。** 这也就是为什么通常会采用自定义 TCP 协议的 RPC 来进行进行服务调用的真正原因。
- 成熟的 RPC 框架还提供好了“服务自动注册与发现”、"智能负载均衡"、“可视化的服务治理和运维”、“运行期流量调度”等等功能减轻开发者心智负担

### 缺点

- **与底层系统紧密耦合**：API 的抽象级别有助于其可重用性。它对底层系统越紧密，对其他系统的可重用性就越低。RPC 与底层系统的紧密耦合不允许在系统功能和外部 API 之间存在抽象层。这会引发安全问题，因为很容易将有关底层系统的实现细节泄露到 API 中。RPC 的紧耦合使得可扩展性需求和松耦合团队难以实现。因此，客户端要么担心调用特定端点的任何可能的副作用，要么尝试找出要调用的端点，因为它不了解服务器如何命名其功能。
- **各个函数可能复用率低**：创建新功能非常容易（这也可以算作优点之一）。因此，可能经常没有编辑现有的，而是创建了新的，最终得到了大量难以理解的重叠功能。



## REST(Representational state transfer)

### 概念介绍

REST – REpresentational State Transfer 表征状态转移

* 资源 Resource：信息内容
* 表征 Representation：表示资源的格式
* 状态 State：特定语境的上下文信息
    * 有状态 Stateful 和 无状态 Stateless，都是相对于服务端来说的
    * 服务端记住状态信息就是有状态
    * 客户端记住状态信息，在请求时告诉服务器就是无状态
* 转移 Transfer：实际的行为逻辑
    * 只能由服务端来提供，因为只有服务端拥有该资源及其表征形式

阅读文章的例子：

* 文章内容本身=>资源
* 需要这个资源的 HTML 格式=>表征
* 当你读完了这篇文章，想看后面是什么内容时，你向服务器发出请求“给我下一篇文章”
    * 依赖“当前你正在阅读的文章是哪一篇”才能正确回应，这个就是状态
    * 取下一篇文章这个行为逻辑，通过某种方式，把“用户当前阅读的文章”转变成“下一篇文章”，这就被称为“表征状态转移”。

### 面向资源编程：

REST本质上是面向资源编程，这也是与RPC面向过程编程最主要的区别，需要注意的是，REST只是一种风格，不遵循它编译器也不会报错，只是不能享受到对应的一些好处罢了，需要设计者灵活考虑。

既然是面向资源编程，所以我们可以这样理解一个符合RESTful的接口：

- 看URL就知道我们的目标是什么资源
- 看方法就知道我们需要对该资源进行什么样的操作
- 看返回码就知道操作的结果如何

比如我们要获取这个编号的咖啡信息

```sh
curl -X GET https://api.justin3go.com/coffees/1
```

### REST的指导原则

* **统一接口** Uniform Interface
    * 通过将 [通用性原则](https://link.juejin.cn?target=https%3A%2F%2Fwww.d.umn.edu%2F~gshute%2Fsofteng%2Fprinciples.html)应用于 组件接口，我们可以简化整体系统架构并提高交互的可见性。
    * 多个体系结构约束有助于获得统一的接口并指导组件的行为。以下四个约束可以实现统一的REST接口：
        - 资源标识：接口必须唯一标识客户端和服务器之间交互中涉及的每个资源。
            - URI 统一资源定位符
        - 通过表示操作资源 ：资源在服务器响应中应该有统一的表示。API 消费者应该使用这些表示来修改服务器中的资源状态。
            - HTTP 协议中已经提前约定好了一套“统一接口”，它包括：GET、HEAD、POST、PUT、DELETE、TRACE、OPTIONS 七种基本操作，任何一个支持 HTTP 协议的服务器都会遵守这套规定，对特定的 URI 采取这些操作，服务器就会触发相应的表征状态转移。
        - 自描述消息 ：每个资源表示都应该携带足够的信息来描述如何处理消息。它还应提供有关客户端可以对资源执行的其他操作的信息。
            - 一种被广泛采用的自描述方法是在名为“Content-Type”的 HTTP Header 中标识出[互联网媒体类型](https://en.wikipedia.org/wiki/Media_type)（MIME type），譬如“Content-Type : application/json; charset=utf-8”，则说明该资源会以 JSON 的格式来返回，请使用 UTF-8 字符集进行处理。
        - 超文本驱动：客户端应该只有应用程序的初始 URI。客户端应用程序应使用超链接动态驱动所有其他资源和交互。
    * 建议系统应能做到每次请求中都包含资源的 ID，所有操作均通过资源 ID 来进行；建议每个资源都应该是自描述的消息；建议通过超文本来驱动应用状态的转移。

- **服务端与客户端分离** Client-Server
    - 客户端-服务器设计模式强制 **关注点分离**，这有助于客户端和服务器组件独立发展。

    - 通过将用户界面问题（客户端）与数据存储问题（服务器）分开，我们提高了跨多个平台的用户界面的可移植性，并通过简化服务器组件提高了可扩展性。

    - 随着客户端和服务器的发展，我们必须确保客户端和服务器之间的接口/契约不会中断。

- **无状态 **Stateless
    - 要求从客户端到服务器的每个请求都必须包含理解和完成请求所需的所有信息。服务器无法利用服务器上任何先前存储的上下文信息。为此，客户端应用程序必须完全保持会话状态。
    - 比较难完全达到，很多大型系统服务器状态太复杂了，没法都交给客户端来做
- **可缓存** Cacheability
    - 无状态服务虽然提升了系统的可见性、可靠性和可伸缩性，但降低了系统的网络性。“降低网络性”的通俗解释是某个功能如果使用有状态的设计只需要一次（或少量）请求就能完成，使用无状态的设计则可能会需要多次请求，或者在请求中带有额外冗余的信息。希望通过缓存来缓解这个问题，提高性能
- **分层系统 **Layered System
    - 分层系统风格允许架构通过约束组件行为由分层层组成。
    - 例如，在分层系统中，每个组件都无法看到与其交互的直接层之外的信息。
    - 这里所指的并不是表示层、服务层、持久层这种意义上的分层。而是指客户端一般不需要知道是否直接连接到了最终的服务器，抑或连接到路径上的中间服务器。
- **按需代码**  Code-On-Demand（*可选*）
    - REST 还允许通过下载和执行小程序或脚本形式的代码来扩展客户端功能。
    - 下载的代码通过减少需要预先实现的功能数量来简化客户端。服务端可以将部分特性以代码的形式交付给客户端，客户端只需要执行代码即可。

### 优点

- **解耦客户端和服务器**：耦合性低，兼容性好，提高开发效率
- 不用关心接口实现细节，相对更规范，更标准，更通用，跨语言支持
- **缓存友好**：重用大量 HTTP 工具，REST 是唯一允许在 HTTP 级别缓存数据的样式。相比之下，任何其他 API 上的缓存实现都需要配置额外的缓存模块。
- **多种格式支持**：支持多种格式来存储和交换数据
- 降低服务接口的学习成本。统一接口（Uniform Interface）是 REST 的重要标志，将对资源的标准操作都映射到了标准的 HTTP 方法上去，这些方法对于每个资源的用法都是一致的，语义都是类似的，不需要刻意去学习，更不需要有什么 Interface Description Language 之类的协议存在。
- 资源天然具有集合与层次结构。以方法为中心抽象的接口，由于方法是动词，逻辑上决定了每个接口都是互相独立的；但以资源为中心抽象的接口，由于资源是名词，天然就可以产生集合与层次结构。

### 缺点/争议

- **没有统一的REST结构**：正如之前所说，只是一种风格，有一些指导原则，所以构建REST API没有完全正确的方法。如何建模资源以及建模哪些资源仍灵活多变，取决于业务场景。**这使得REST理论上很简单但实践中较为困难**。
- **高负载**：REST API会返回大量丰富的元数据，方便客户端仅从响应中就可以了解有关应用程序状态的所有必要信息，随之而来的就是一定的性能问题（高负载）。这个缺点和下面一个缺点也是后续GraphQL被提出的主要原因。
- **过度获取和获取不足**：无法预估后续业务场景会如何变化，这导致了最初设计的API很难根据业务场景不断变化并且不能影响到之前的业务。
- **REST 绑定于 HTTP 协议**，不适合应用于追求高性能传输的场景中。
    - 面向资源编程不是必须构筑在 HTTP 之上，但 REST 是，这是缺点，也是优点。
    - REST 和 RPC 尽管应用场景的确有所重合，但重合的范围有多大就是见仁见智的事情。

- 面向资源的编程思想可能只适合做 CRUD，面向过程、面向对象编程才能处理真正复杂的业务逻辑
    - 有时候把比较难把需求抽象成 CRUD，和 HTTP 方法做映射。不过 REST 也不是什么教条，用户可以自定义方法，比如 Google 推荐的 REST API 风格，[自定义方法](https://cloud.google.com/apis/design/custom_methods)应该放在资源路径末尾，嵌入冒号加自定义动词的后缀
    - 面向过程、面向对象、面向资源只是立场不同的选择问题，没有高下之分
        - 面向过程编程时，为什么要以算法和处理过程为中心，输入数据，输出结果？当然是为了符合计算机世界中主流的交互方式。
        - 面向对象编程时，为什么要将数据和行为统一起来、封装成对象？当然是为了符合现实世界的主流的交互方式。
        - 面向资源编程时，为什么要将数据（资源）作为抽象的主体，把行为看作是统一的接口？当然是为了符合网络世界的主流的交互方式。


下面三个没太懂？TODO 凤凰架构 

- 不利于事务支持
- 没有传输可靠性支持
- 缺乏对资源进行部分和批量处理的能力



### RMM 成熟度

如何评价服务是否 RESTful

* Level 0：The Swamp of [Plain Old XML](https://en.wikipedia.org/wiki/Plain_Old_XML)：完全不 REST。另外，关于 Plain Old XML 这说法，SOAP 表示[感觉有被冒犯到](https://baike.baidu.com/item/感觉有被冒犯到)。
    * 问题：需求固定的时候还想，如果不想获取为获取**其他信息**写别的方法或者改现有接口就不行了
* Level 1：Resources：开始引入资源的概念。
    * 引入资源的概念，在 API 中基本的体现是围绕着资源而不是过程来设计服务
        * 服务的 Endpoint 应该是一个名词而不是动词
        * 每次请求中都应包含资源的 ID，所有操作均通过资源 ID 来进行
    * 问题
        * 如果在信息的**操作**上还想有扩展，那还是要提供新的服务接口
        * 处理结果响应时，智能靠着结果的 code 和 message 字段做分支判断，比较难全面考虑，也不利用统一处理
        * 没考虑认证授权等方面的安全问题
* Level 2：HTTP Verbs：引入统一接口，映射到 HTTP 协议的方法上。（大多数系统的级别）
    * 把不同业务需求抽象为对资源的增加、修改、删除等操作来解决第一个问题
    * 使用 HTTP 协议的 Status Code，可以涵盖大多数资源操作可能出现的异常，而且 Status Code 可以自定义扩展，以此解决第二个问题
    * 依靠 HTTP Header 中携带的额外认证、授权信息来解决第三个问题
    * 问题：如何知道要访问的服务 Endpoint？
        * 这个东西不是说代码里就这么写的，应该期望达到超文本驱动（见 Level3）
* Level 3：Hypermedia Controls：超媒体控制在本文里面的说法是“超文本驱动”，在 Fielding 论文里的说法是“Hypertext As The Engine Of Application State，HATEOAS”，其实都是指同一件事情。
    * 希望的是除了第一个请求是由你在浏览器地址栏输入所驱动之外，其他的请求都应该能够自己描述清楚后续可能发生的状态转移，由超文本自身来驱动。
    * 做到了第 3 级 REST，那服务端的 API 和客户端也是完全解耦的，你要调整服务数量，或者同一个服务做 API 升级将会变得非常简单。



## GraphQL(Graph query language)

### 介绍

> 如果你熟悉REST，但不熟悉GraphQL，推荐阅读这篇文章--[GraphQL vs. REST](https://link.juejin.cn?target=https%3A%2F%2Fwww.apollographql.com%2Fblog%2Fgraphql%2Fbasics%2Fgraphql-vs-rest%2F)，里面有较为详细的对比与介绍

首先来说，它是一种查询语言，具有一定的语法规则（即学习成本--有编程基础的话较小），可以解决上述REST中的一些问题。

引用[官网](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2F)的介绍：

> GraphQL 是一种用于 API 的查询语言，也是使用现有数据完成这些查询的运行时。GraphQL 为您的 API 中的数据提供了完整且易于理解的描述，使客户能够准确地询问他们需要什么，仅此而已，随着时间的推移更容易发展 API，并启用强大的开发人员工具。

### Q&A

这里引用一下[官网的FAQ](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2Ffaq%2F%23how-does-graphql-affect-my-product-s-performance)

**ls GraphQL a database language like SQL?**

> No, but this is a common misconception.
>
> GraphQL is a specification typically used for remote client-server communications. Unlike SQL, GraphQL is agnostic to the data source(s) used to retrieve data and persist changes. Accessing and manipulating data is performed with arbitrary functions called [resolvers](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2Flearn%2Fexecution%2F). GraphQL coordinates and aggregates the data from these resolver functions, then returns the result to the client. Generally, these resolver functions should delegate to a [business logic layer](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2Flearn%2Fthinking-in-graphs%2F%23business-logic-layer) responsible for communicating with the various underlying data sources. These data sources could be remote APIs, databases, [local cache](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2Flearn%2Fcaching%2F), and nearly anything else your programming language can access.
>
> For more information on how to get GraphQL to interact with your database, check out our [documentation on resolvers](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2Flearn%2Fexecution%2F%23root-fields-resolvers).

**Does GraphQL replace REST?**

> No, not necessarily. They both handle APIs and can [serve similar purposes](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2Flearn%2Fthinking-in-graphs%2F%23business-logic-layer) from a business perspective. GraphQL is often considered an alternative to REST, but it’s not a definitive replacement.
>
> GraphQL and REST can actually co-exist in your stack. For example, you can abstract REST APIs behind a [GraphQL server](https://link.juejin.cn?target=https%3A%2F%2Fwww.howtographql.com%2Fadvanced%2F1-server%2F). This can be done by masking your REST endpoint into a GraphQL endpoint using [root resolvers](https://link.juejin.cn?target=https%3A%2F%2Fgraphql.org%2Flearn%2Fexecution%2F%23root-fields-resolvers).
>
> For an opinionated perspective on how GraphQL compares to REST, check out [How To GraphQL](https://link.juejin.cn?target=https%3A%2F%2Fwww.howtographql.com%2Fbasics%2F1-graphql-is-the-better-rest%2F).

看到上述两个FAQ我自己蹦出了这样的想法：

首先我想到的是一个比较荒谬的问题：既然GraphQL是一种查询语言，SQL也是一种查询语言，那为什么不直接前端传入sql直接拿数据呢？

> 显然这是有很多问题的，最主要的问题就是这相当于无后端，全部逻辑都集中在了客户端，这对于客户端的压力是非常大的，并且也是非常不安全的，就类似于破解单机游戏一样...
>
> 高耦合的话我理解前端程序也可以进行多层抽象来解耦，比如MVC这类。但这又要重新经历一次类似的web架构演变，对现有的生态也是极大的破坏...
>
> 上述只是一些胡乱猜想，不必当真，回到这里的话GraphQL就是对后端提供的GraphQL运行时查询的语言，官方语法为SDL。而这个运行时是应用程序业务逻辑外面的一层接口暴露，我们开发人员需要对每一个接口业务字段

然后与REST的区别我理解就是：二者本质都可以理解为面向资源编程

- GraphQL通过一个运行时，使用规定的语法可以更加精准灵活地操作资源（灵活度也是有一定限度的，只是相对来说）
- 而REST就能根据提前定义好地URL，通过不同的方法操作资源

这部分可能各有各的想法思考，欢迎友善讨论~

### 工作过程

> 在查询之前需要schema，客户端可以验证他们的查询以确保服务器能够响应它。在到达后端应用程序时，GraphQL 操作将针对整个schema进行解释，并使用前端应用程序的数据进行解析。向服务器发送大量查询后，API 会返回一个 JSON 响应，其中的数据形状与我们请求的数据完全相同。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8689d53eca784ee6b3f24d1095cf6947~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp)

参考：_[*Jonas Helfer*](https://link.juejin.cn?target=https%3A%2F%2Fwww.apollographql.com%2Fblog%2Fgraphql-explained-5844742f195e)

除了 RESTful CRUD 操作之外，GraphQL 还具有允许来自服务器的实时通知的[订阅](https://link.juejin.cn?target=https%3A%2F%2Fdocs.nestjs.com%2Fgraphql%2Fsubscriptions)。

GraphQL需要我们对暴露出去的每一个字段规定一个函数进行处理，比如一个简单的node搭建的应用程序如下：

```js
var express = require('express');
var graphqlHTTP = require('express-graphql');
var { buildSchema } = require('graph');
// 构建schema，这里定义查询的语句和类型
var schema = buildSchema(`
	typr Account {
		name: String
		age: Int
		sex: String
		department: String
	}
	type Query {
		hello: String
		accountName: String
		age: Int
		account: Account
	}
`)
// 定义查所对应的resolver，也就是查询对应的处理器
var root = {
	hello: () => 'Hello world',
	accountName: () => 'justin3go',
	age: () => 18,
	account: () => ({
		name: '',
		age: 18,
		sex: '',
		department: ''
	})
}

var app = express();
app.use('/graphql', graphqlHTTP({
	schema: schema,
	rootValue: root,
	graphiql:true
}));

app.listen(4000)
```

本篇文章不做其实战介绍，可直接参考[NestJS官网搭建GraphQL教程](https://link.juejin.cn?target=https%3A%2F%2Fdocs.nestjs.com%2Fgraphql%2Fquick-start)以及其仓库有非常丰富并值得参考的相关代码：

- [22-graphql-prisma](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fnestjs%2Fnest%2Ftree%2Fmaster%2Fsample%2F22-graphql-prisma)
- [23-graphql-code-first](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fnestjs%2Fnest%2Ftree%2Fmaster%2Fsample%2F23-graphql-code-first)

> Nest 提供了两种构建 GraphQL 应用程序的方法，**代码优先**和**模式优先**方法。您应该选择最适合您的。这个 GraphQL 部分的大部分章节分为两个主要部分：一个是如果你**先采用代码**，你应该遵循，另一个是如果你先采用**模式**，则应该使用。
>
> 在**代码优先**方法中，您使用装饰器和 TypeScript 类来生成相应的 GraphQL 模式。如果您更喜欢专门使用 TypeScript 并避免在语言语法之间切换上下文，则此方法很有用。
>
> 在**模式优先**方法中，事实来源是 GraphQL SDL（模式定义语言）文件。SDL 是一种在不同平台之间共享模式文件的与语言无关的方式。Nest 基于 GraphQL 模式自动生成您的 TypeScript 定义（使用类或接口），以减少编写冗余样板代码的需要。
>
> [docs.nestjs.com/graphql/qui…](https://link.juejin.cn?target=https%3A%2F%2Fdocs.nestjs.com%2Fgraphql%2Fquick-start%23overview)

### 优点

- **非常适合图形数据**：深入链接关系但不适合平面数据的数据
- 请求的数据不多不少，按需请求，非常灵活
- 获取多个资源，只用一个请求
- 描述所有可能类型的系统。便于维护，根据需求平滑演进，添加或者隐藏字段（无需版本控制）

### 缺点

- **性能问题**：GraphQL 以复杂性换取其强大功能。一个请求中包含太多嵌套字段会导致系统过载。因此，REST 仍然是复杂查询的更好选择。
- **缓存复杂性**：由于 GraphQL 没有重用 HTTP 缓存语义，因此它需要自定义缓存工作。
- **一定的学习成本**：没有足够的时间弄清楚 GraphQL生态和 SDL，许多项目决定遵循众所周知的 REST 路径。



## 各种关系和比较

### RPC 和 HTTP 关系

首先梳理一下时间顺序

1. TCP 70 年
2. RPC 80 年代
3. HTTP 90 年代





#### 区别

* 服务发现：找服务对应的 IP 和端口地址
    * HTTP：可以通过域名 DNS 解析 IP 地址，端口一般默认 80
    * RPC：一般有专门的中间服务去保存服务名和 IP 信息
        * Consul
        * Etcd
        * Reids
        * CoreDns（基于 DNS 做服务发现）
* 底层链接形式
    * HTTP：HTTP 1 和 2 默认基于 TCP 连接，然后 Keep Alive 保持，复用连接
    * RPC：没规定，基于可以 TCP 甚至也可以 HTTP
        *  如果基于 TCP 长连接的话可能还会再建立一个连接池来复用
* 传输的内容
    * 序列化的话， HTTP 一般基于 JSON 来做一些序列化(可能有冗余），RPC 定制化程度高一些。性能一般比传统 HTTP 1.1 好（后面 HTTP 也升级了，性能这块就不好说了，至于是否迁移，也有一部分历史沉淀原因在）



### 关系





### RPC 和 REST

都是为了访问过程服务

* RPC 的思路是模拟本地方法调用
* REST 的思路是面向资源编程





## 参考资料

[了解API相关范式(RPC、REST、GraphQL) - 掘金 (juejin.cn)](https://juejin.cn/post/7193379460962320442)

[访问远程服务 | 凤凰架构 (icyfenix.cn)](http://icyfenix.cn/architect-perspective/general-architecture/api-style/)

[What is REST - REST API Tutorial (restfulapi.net)](https://restfulapi.net/)