# FastMCP 2.0
官方地址：https://gofastmcp.com/getting-started/welcome

## FastMCP介绍

### 简介

##### FastMCP 2.0 具有一套全面的功能，远远超出了核心 MCP 规范，所有这些都是为了提供最简单的生产路径 。
  这些功能包括部署、身份验证、客户端、服务器代理和组合、从 REST API 生成服务器、动态工具重写、内置测试工具、集成等。(FastMCP 2.0 has a comprehensive set of features that go far beyond the core MCP specification, all in service of providing the simplest path to production. These include deployment, auth, clients, server proxying and composition, generating servers from REST APIs, dynamic tool rewriting, built-in testing tools, integrations, and more.)

##### MCP 服务器可以：
1. 通过 Resources 公开数据（可以把这些看作是 GET 端点;它们用于将信息加载到 LLM 的上下文中）（Expose data through Resources (think of these sort of like GET endpoints; they are used to load information into the LLM’s context)）
2. 通过工具提供功能（有点像 POST 端点;它们用于执行代码或以其他方式产生副作用）（Provide functionality through Tools (sort of like POST endpoints; they are used to execute code or otherwise produce a side effect))
3. 通过 Prompts（用于 LLM 交互的可重用模板）定义交互模式(Define interaction patterns through Prompts (reusable templates for LLM interactions))


## 知识补充

#### 1. REST API：

   REST API 提供了一种轻量级且灵活的方式来构建Web API，促进了应用程序、Web 服务和数据库之间的数据交换，并广泛应用于微服务架构中。1 它的设计原则使其具有高可扩展性、可靠性和易用性，成为现代Web开发中应用程序之间通信的标准方式。
   
##### 什么是API？
API（Application Programming Interface）是一组定义和协议，允许不同的应用程序或服务之间进行通信和数据交换。 简单来说，API 是一种机制，支持应用程序或服务访问另一个应用程序、服务或数据库中的资源。

##### 什么是REST？
REST（Representational State Transfer，表述性状态转移）是一种软件架构风格，由计算机科学家 Roy Fielding 于2000年提出。 它定义了一系列用于构建Web API 的规则和准则，旨在提高系统的可扩展性、可靠性和可维护性。

##### REST API 的特点：
1.  资源导向： REST API 将所有内容抽象为“资源”，每个资源都有一个唯一的标识符（URI，统一资源标识符）。 客户端通过URI来定位和操作这些资源。
2.  统一接口： REST API 具有一致的接口，简化了整体系统架构，并提高了交互的可见性。这意味着服务器以标准形式传输信息，并且资源表示包含足够的信息，以便客户端修改或删除资源的状态。
3.  客户端-服务器分离： 客户端和服务器是独立的，可以独立发展和更新，互不影响。 客户端负责用户界面和用户体验，而服务器处理数据存储和业务逻辑。
4.  无状态： 服务器不存储客户端的会话信息。每个请求都包含处理该请求所需的所有信息，服务器可以独立理解和处理每个请求，这减少了服务器负载并提高了性能。
5.  可缓存性： 服务器响应可以包含是否允许对已交付资源进行缓存的信息。客户端或中间方可以缓存响应，以减少服务器负载并提高数据传输速度。
6.  分层系统： 系统可以分为多层，每层只与相邻层通信，客户端不需要知道它们是直接连接到服务器还是连接到中间件（如反向代理或负载均衡器）。
7.  按需编码（可选）： 在某些情况下，响应可以包含可执行代码（例如Java小程序），这些代码只应按需运行，以扩展客户端功能。
8.  技术独立性： REST API 独立于开发相应应用程序的编程语言和框架。客户端和服务器应用程序不需要共享语言或框架。

##### REST API 的工作原理：
REST API 通常使用HTTP协议进行通信。客户端通过HTTP方法（如GET、POST、PUT、DELETE、PATCH）对资源执行操作。
1.  GET： 用于从服务器检索资源。
2.  POST： 用于在服务器上创建新资源。
3.  PUT： 用于更新现有资源（客户端提供改变后的完整资源）。
4.  PATCH： 用于更新资源的部分属性。
5.  DELETE： 用于从服务器删除资源。

服务器返回的数据格式通常是JSON，但也支持XML等其他格式。REST API 还使用HTTP状态码来表示请求结果，例如200表示成功，404表示未找到资源，500表示服务器内部错误等。
