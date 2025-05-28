# HTTP（HyperText Transfer Protocol，超文本传输协议

HTTP 是基于 TCP/IP 协议的应用层协议，用于客户端和服务器之间的通信

## HTTP 报文组成部分

  - 请求报文
	- 请求行 `Request Method`,`Request URL`
	- 请求头 包含若干属性，key:value 值	
	- 空行
	- 请求体
- 响应报文
	- 状态行 `Request Version`,`Status Code`
	- 响应头 包含若干属性，key:value 值
	- 空行
	- 响应体

### 字段
- content
	- `Content-Encoding`
	- `Content-Type`
	- `Content-Length`
	- `Content-Language`
- 请求来源
	- `Connection`:keep-alive / close / upgrade
	- `Referer`请求来源域 保留完整URL
	- `Origin`请求来源域 仅保留协议域名端口
	- `Host`服务器域名
	- `Upgrade`:websocket
- 缓存相关
	- `Cache-Control`
	- `Pragma`
	- `Expires`实体首部
	- `Last-Modified`实体首部
	- `If-Modified-Since`
	- `ETag`
	- `If-None-Match`
- Cookie 相关
	- `Cookie`
	- `Set-Cookie`
- 跨域相关
	- `Origin`
	- `Access-Control-Request-Method`
	- `Access-Control-Allow-Origin`
	- `Access-Control-Allow-Methods`
	- `Access-Control-Max-Age`
	- `Access-Control-Allow-Credentials`

## HTTP 的响应状态码

> 消息

- 100 继续发送

- 101 协议升级

> 请求成功

- 200 OK

- 206 处理了部分请求

> 重定向

- 301 永久性重定向，资源已经分配了新的 URl

- 302 临时重定向

- 303 表示资源存在着另一个 URL，应使用 GET 方法获取资源

> 对于 301/302/303 响应，几乎所有浏览器都会删除报文主体并自动用 GET 重新请求

- 304 资源未修改

> 请求错误

- 400 Bad Request 请求报文语法错误

- 401 Unauthorized 需要认证

- 403 Forbidden 禁止访问

- 404 Not Found

- 405 请求方法非法

- 408 请求超时

> 服务器错误

- 500 服务器错误

- 503 服务器过载

- 505 不支持该版本 HTTP 协议

## HTTP 发展历程
### HTTP 0.9

- 没有头部（both in 请求和响应）
- 只能且直接返回 html
- 没有状态码
#### 请求方法
- `GET`
### HTTP1.0

- 加入了请求头和响应头
- 支持多媒体类型数据`Content-Type`
- 加入状态码

#### 请求方法
- `GET`
	1. 请求放在参数放在 URL 地址后
	2. 刷新无副作用
	3. 能保存书签，会被浏览器缓存，加入历史记录
	4. 编码 urlencoded
	5. 浏览器或者服务器会对 URL 长度做限制
- `POST`
	1. 请求参数放在 body 中
	2. 刷新提示再次提交？
	3. 不缓存不保存不记录
	4. 数据类型无限制
- `HEAD` 
	1. 只请求头部，获取其中“关于该资源的信息”(元信息或称元数据)
#### 短连接
每次通信完后断开 TCP 通道，每个新请求都需要建立新的连接。
队头阻塞 (Head-of-Line Blocking, HOLB)： 即使多个请求可以并行处理，也需要排队等待 TCP 连接的建立和关闭。
😭存在队头阻塞(Head-of-Line Blocking, HOLB)
### HTTP1.1

- 引入了更细致的缓存控制如 `Cache-Control`、`ETag` 等。
- 范围请求（Byte-Range Requests）：允许客户端请求资源的某个部分（例如，断点续传）。
#### 持久连接
也称为 **HTTP Keep-Alive**，是指在一个 TCP 连接上可以发送和接收**多个 HTTP 请求和响应**，而不需要为每个请求都建立和关闭新的 TCP 连接。
连接会在一段时间不活动后（由 `Keep-Alive` 超时时间控制）或在任一方明确关闭时才断开。
😭存在队头阻塞(Head-of-Line Blocking, HOLB)
```js
// headers
Connection: keep-alive
Keep-Alive: timeout=5, max=100
```

#### 管线化
**管线化**是建立在持久连接之上的优化。它允许客户端在**收到上一个请求的响应之前，就发送下一个请求**。客户端可以一次性发送多个请求到服务器，然后服务器按照请求的顺序依次返回响应。
只能用于`GET`,`HEAD`,`PUT`,`DELETE`请求。

##### **局限性（为什么管线化在实践中很少被广泛使用）：**

尽管管线化看起来很有前景，但它在实践中并未被广泛部署，原因如下：

- **服务器必须按序响应：** 服务器必须严格按照请求的顺序发送响应。如果请求 A 需要很长时间才能处理，那么即使请求 B 和 C 已经处理完毕，它们也必须等待 A 的响应发送出去后才能发送。这仍然会造成**服务器端的队头阻塞**，抵消了并行发送请求带来的好处。
- **网络中间设备支持问题：** 许多旧的代理服务器、负载均衡器和防火墙可能不支持 HTTP 管线化，或者在处理管线化请求时行为异常，导致不稳定的连接或错误。
- **客户端实现复杂性：** 浏览器在实现管线化时，需要处理各种复杂的边缘情况，如请求失败、响应顺序混乱等，增加了实现的复杂性。
- **幂等性问题：** 如果管线化发送的请求是非幂等的（例如，一个 `POST` 请求），并且连接在发送请求后、收到响应前断开，客户端可能无法确定服务器是否已经处理了该请求。如果简单地重试所有请求，可能会导致重复操作。
- **HTTP/2 的出现：** `HTTP/2` 提供了更强大、更彻底的解决方案——**多路复用（Multiplexing）**。在 `HTTP/2` 中，多个请求和响应可以在同一个 TCP 连接上**完全并行且乱序**地传输，彻底解决了队头阻塞问题，使得管线化变得不必要。

#### 请求方法
- `OPTIONS`询问获取当前资源所支持的方法
- `PUT`从客户端向服务器传送新文件
- `PATCH`是对 PUT 方法的补充，用来对已知资源进行局部更新 。
- `DELETE`请求服务器删除指定文件
- `TRACE`回显服务器收到的请求，主要用于测试或诊断。
- `CONNECT` HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。
### SPDY
2009 年推出，试验性。
位于 HTTP 与 SSl 之间，是对 HTTP 的增强。
1. 多路复用：通过一个 TCP 连接，可以无限制处理多个 HTTP 请求。
2. 赋予请求优先级：给请求逐个分配优先级顺序。可以解决在发送多个请求时，因带宽低而导致响应变慢的问题。
3. 压缩 HTTP 头部：压缩方式：DELEFT
4. 服务端推送

### HTTP2
2015 年发布，基于 SPDY 协议
#### 二进制分帧
`HTTP/2` 将所有通信（请求、响应、头部等）都拆分为更小的二进制帧，并以二进制格式传输，而非 `HTTP/1.1` 的文本格式。这使得解析更高效，也更适合计算机处理。
#### 多路复用(Multiplexing)
在单个 TCP 连接上同时并发地传输多个请求和响应。每个请求和响应都分帧传输，且帧可以乱序发送和接收，最终由接收方根据帧的 Stream ID 重组成完整的消息。这彻底解决了 HTTP/1.1 的队头阻塞问题。

#### 头部压缩（HPACK）
使用 HPACK 算法对 HTTP 头部进行压缩，利用静态表和动态表来避免重复发送相同的头部字段，大幅减少了请求和响应的头部大小。

#### 服务器推送（Server Push）
允许服务器在客户端需要之前（例如，客户端请求 HTML 页面时），主动推送页面所需的其他资源（如 CSS、JS、图片），从而减少客户端的往返时间。

#### 请求优先级
客户端可以为不同的请求设置优先级，服务器可以根据优先级来优化资源分配和发送顺序。
#### 通信加密（强烈推荐，并非强制）
`HTTP/2` 协议本身没有强制要求加密，但所有主流浏览器（如 Chrome, Firefox, Edge）都只支持在 TLS/SSL 之上运行的 `HTTP/2` (`h2`)。这使得 `HTTP/2` 实际上是基于 HTTPS 的。
### HTTP3
http3 使用新的 UDP 协议`QUIC (Quick UDP Internet Connections)`


- 解决 http2 时代丢包阻塞

- 切换网络时的连接保持（快速重启会话）

- 加密传输
