## TCP协议
## TCP握手
### 三次握手
1. 为了确保通信双方收发能力正常

2. 指定自己的初始化序列号(相当于随机数密钥)，为后面的可靠传送做准备

过程：

1. 第一次握手：
	客户端给服务端发一个 `SYN` 报文，并指明客户端的初始化序列号 `ISN(c)`。此时客户端处于 `SYN_Send` 状态。

2. 第二次握手：
	服务器收到客户端的 `SYN` 报文之后，会以自己的 `SYN` 报文作为应答，并且也是指定了自己的初始化序列号 `ISN(s)`，同时会把客户端的 `ISN(c) + 1` 作为 `ACK(确认字符)` 的值，表示自己已经收到了客户端的 `SYN`，此时服务器处于 `SYN_REVD` 的状态。

3. 第三次握手：
	客户端收到 `SYN` 报文之后，会发送一个 `ACK` 报文，当然，也是一样把服务器的 `ISN(s) + 1` 作为 `ACK` 的值，表示已经收到了服务端的 `SYN` 报文，此时客户端处于 `establised` 状态。

> 同时该 tcp [[栈]] 已经允许请求数据，例如可以同时带上一个 GET 请求

	服务器收到 ACK 报文之后，也处于 `establised` 状态，此时，双方以建立起了链接。

> `半连接队列`：在 TCP 协议三次握手的连接过程中，如果一个用户向服务器发送了 SYN 报文，服务器又发出 SYN+ACK 应答报文后未收到客户端的 ACK 报文回应，这种情况下服务器端会再次发送 SYN+ACK 给客户端，并等待一段时间后丢弃这个未完成的连接，这段时间的长度称为 SYN Timeout，一般来说这个时间是分钟级。

> `泛洪攻击`：SYN 泛洪攻击通过发送大量的伪造 TCP 连接报文而造成大量的 TCP 半连接,服务器端将为了维护这样一个庞大的半连接列表而消耗非常多的资源。这样服务器端将忙于处理攻击者伪造的 TCP 连接请求而无法处理正常连接请求,甚至会导致堆栈的溢出崩溃。

### 四次挥手
断开 tcp 连接

过程：

4. 第一次挥手：
	客户端发送`FIN` 报文，报文中会指定一个序列号`SYN(c)`。此时客户端处于 `FIN_WAIT1` 状态。

5. 第二次握手：
	服务端收到 `FIN` 之后，会把客户端的序列号值`SYN(c) + 1` 作为 `ACK` 报文的序列号值并发送给客户端，表明已经收到客户端的报文了，此时服务端处于 `CLOSE_WAIT`状态。

6. 第三次挥手：
	如果服务端也想断开连接了，和客户端的第一次挥手一样，发给 `FIN` 报文，且指定一个序列号`SYN(s)`。此时服务端处于 `LAST_ACK` 的状态。

7. 第四次挥手：
	客户端收到 FIN 之后，也把服务端的序列号值`SYN(s) + 1` 作为自己 `ACK` 报文的序列号值并发送给服务端,此时客户端处于 `TIME_WAIT` 状态。一段时间(👇`TIME_WAIT`)之后才会进入 `CLOSED` 状态

	服务端收到 `ACK` 报文之后，就处于关闭连接了，处于 `CLOSED` 状态。


> `TIME_WAIT` 状态:客户端发送 ACK 之后不直接关闭，而是要等一阵子才关闭,确保服务器是否已经收到了 ACK 报文，如果没有收到的话，服务器会重新发 FIN 报文给客户端，客户端再次收到 ACK 报文之后，就知道之前的 ACK 报文丢失了，然后再次发送 ACK 报文。

> 也可缩短为三次挥手，上述二三步骤合并，服务器同时回复 ACK 和 FIN

## 拥塞机制
根据网络拥堵情况，**动态调节发送速率**，以“试探+回退”方式寻求最优传输速率。
### **1️⃣ 慢启动（Slow Start）**
- 初始时，TCP 不知道网络能承载多少流量，**从一个小的窗口开始**（通常是 1 个 MSS）
- 每收到一个 ACK，就让拥塞窗口 `cwnd` **翻倍增长**（指数级）
	`1->2->4->8->16...`
### **2️⃣ 拥塞避免（Congestion Avoidance）**
- 当 cwnd 增长到一个阈值（ssthresh，慢启动门限），进入“拥塞避免”阶段
- 此时窗口变为 **线性增长**，每个 RTT 增加 1 个 MSS
	`16 → 17 → 18 → 19...`
### **3️⃣ 快速重传（Fast Retransmit）**
- 接收方如果**连续收到 3 个重复的 ACK**（表明中间某个包可能丢了）发送方立即**重传丢失的分段**，不等**超时重传**
### **4️⃣ 快速恢复（Fast Recovery）**
- 快速重传后不从慢启动从头开始
