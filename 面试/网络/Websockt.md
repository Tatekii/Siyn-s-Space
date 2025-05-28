# Websocket
WebSocket 是一种在单个 TCP 连接上进行**全双工、长连接通信**的协议，用于浏览器和服务器之间进行实时、低延迟的数据交换。

### websocket之前的实时通信
1. 客户端轮询
2. 长轮询（刮起请求不响应）
3. 流式HTTP (HTTP Streaming) / Server-Sent Events (SSE)

## 特点
- 全双工双向通信
	- 服务端推送
- 基于TCP长连接
- 低延迟，轻量头部


## 工作原理
### WS握手
1. 客户端请求

```http
GET /chat HTTP/1.1 
Host: server.example.com 
Upgrade: websocket 
Connection: Upgrade 
%% 握手过程的安全性验证。 %%
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ== 
%% 客户端支持的 WebSocket 协议版本 %%
Sec-WebSocket-Version: 13 
Origin: http://client.example.com
```

2. 服务端响应协议升级

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: xxxxxxxx==
```

### WSS
WebSocket 本身是基于 TCP 的协议，它并不自带加密机制。

当你使用 wss:// 开头时，实际做的就是：
在 WebSocket 握手之前，**用 [TLS/SSL](HTTPS.md#TLS/SSL) 建立一条加密通道（TCP over TLS）**，然后再在这个安全的通道中完成 WebSocket 握手。

```text
Client                        Server
   |----- TLS Handshake ----->|   ← 建立加密通道（和 HTTPS 一样）
   |----- HTTP Upgrade ------>|
   |<----- 101 Switching ---- |
   → WebSocket 通信（加密传输）
```
