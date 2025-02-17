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
- `GET`
- 只能且直接返回 html
### HTTP1.0
非持久连接：每次通信完后断开 TCP 通道，每个新请求都需要建立新的连接。
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
- `HEAD` 与 get 相同的请求，不过只请求头部，获取其中“关于该资源的信息”(元信息或称元数据)
### HTTP1.1
持久连接：一定时间内，只要不是主动断开则保持连接（keep-alive 设定这个时长），但是下一个请求必须等待上一个请求响应完成（线头阻塞）。
管线化持久连接：在同一条长连接上发出连续的请求，而不用等待应答返回,只能用于`GET`,`HEAD`,`PUT`,`DELETE`请求。
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
5. SPDY 位于 HTTP 下，TCP 和 SSL 之上，可以兼容老版本 http 同时使用可用的 ssl 功能
6. SPDY 强制使用 SSL
### HTTP2
2015 年发布，基于 SPDY 协议
http/1.x 是一个超文本协议，而 http2 是一个二进制协议。
1. 二进制分帧
	当客户端同时向服务端发起多个请求，那么这些请求会被分解成一一个的帧，每个帧都会在一个 TCP 链路中无序的传输，当帧到达服务端之后，就可以根据 `Stream Identifier` 来重新组合得到完整的请求。
2. 头部压缩（请求和响应首部压缩）
	`HPACK` 算法，客户端和服务器建立字典（首部表）重复头部不用再发送
3. 服务端推送`Server Push`
- 服务端能主动返回多的觉得客户端会用到的资源
1. 多路复用
	- 同域名的通信都在单个连接上完成(只占用一个 tcp 连接
	- 单连接可以承受任意数量的双向数据流
	- 服务端可按照自定义优先级返回资源
### HTTP3
- http3 使用新的 UDP 协议`QUIC`代替 TCP

- 解决 http2 时代丢包阻塞

- 切换网络时的连接保持（快速重启会话）

- 加密传输


## Websocket

  

- websocket 的出现解决了传统 http 请求只能由客户端发起的缺陷

- http 无法得知服务器连续的状态变化，只能靠轮询

- ws 构造函数就要传入`绝对URL`，且连接不能转发只能是直接连接

- 全双工，双向通信

- 服务端推送

- 持久连接

- 基于 TCP 传输，复用 HTTP 的握手通道

- 协议升级为 ws

7. 客户端请求

```javascript

Connection: Upgrade;

Upgrade: websocket;

```

8. 服务端响应协议升级

```javascript

HTTP/1.1 101 Switching Protocols

```