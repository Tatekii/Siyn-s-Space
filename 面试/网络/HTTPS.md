# HTTPS
**When you visit a website via HTTPS**, you’re actually using HTTP over a TLS-secured connection.
## TLS/SSL

- HTTPS（**HyperText Transfer Protocol Secure**）是 HTTP 的安全版本，依赖 **TLS（Transport Layer Security）** 或 **SSL（Secure Sockets Layer）** 协议对数据进行加密和保护。TLS 1.2 及 TLS 1.3 是目前常用的版本，而 SSL 已经被淘汰。

- TLS 协议的特点是与应用层协议（HTTP，FTP）无耦合，应用层协议能透明运行在 TLS 协议上层（HTTP 与 TCP 之间）
#### 特性
- 非对称加密
- 摘要算法
- 数字签名
#### SSL 证书
- 证书包含以下信息：申请者公钥、申请者的组织信息和个人信息、签发机构 CA 的信息、有效时间、证书序列号，摘要算法等信息的明文
- CA 使用 CA 私钥对证书明文摘要算法后得出的指纹进行加密得到签名
#### 验证证书合法性
1. 从操作系统中找到相应的`CA机构证书`
2. 使用`CA证书`的公钥解密`站点证书 `的`签名`得到校验码 1
3. 使用证书指定的摘要算法对`站点证书`明文计算得到校验码 2
4. 比较两个校验码相同则说明证书没被篡改
5. 后面就开始查看证书是否在有效期，是否对应当前域名，是否在吊销列表中等

#### TSL协议连接流程

1. 用户端发起握手`Client Hello`
	1. 客户端支持的TLS版本
	2. 客户端支持的加密算法
	3. 随机数`Random_C`

2. 服务器响应`Server Hello`
	1. 使用TSL版本
	2. 指定加密算法
	3. 随机数`Random_S`
	4. 服务器数字证书📄（包含公钥🔑）

3. 证书验证
	1. 客户端浏览器使用操作系统里的 **_CA 公钥_** 打开并验证证书的合法性得到证书中的 **_服务器公钥_**

4. 生成会话密钥
	1. 客户端生成生新的随机数`Pre-Master Secret`，并使用 **_服务器公钥_** 加密发送给服务器
	2. 服务器使用自己的 **_服务器私钥_** 解密，得到 `Pre-Master Secret`
	3. 双方使用同样的加密算法【**伪随机函数（PRF）**】对上述三个随机数进行计算得到对称的密钥`Session Key`

5. 确认会话密钥
	1. 客户端Finish消息
	2. 服务端Finish消息
	3. 之后的数据传输使用对称加密

#### TLS1.3
在 **TLS 1.3** 中，为了提高性能和安全性：

**握手次数减少**
• 取消 **RSA 密钥交换**，默认使用 **ECDHE（椭圆曲线 Diffie-Hellman）**。
• 默认开启 **前向安全性（Forward Secrecy）**，即使私钥泄露也无法解密过去的数据。

**移除不安全算法**
• 移除了 MD5、SHA-1、RC4、DES 等不安全的加密算法。

**更快的 TLS 握手**
• 支持 **0-RTT（Zero Round Trip Time）**，在第二次访问相同服务器时可跳过部分握手步骤，提高加载速度。

### 数据传输
TLS 通过 **HMAC（Hash-based Message Authentication Code）** 或 **AEAD（Authenticated Encryption with Associated Data）** 来验证数据完整性。
1. **HMAC（TLS 1.2 及更早版本）**
	1. 发送方使用密钥对发送数据进行HASH计算后得到摘要值
	2. 接收方同样对收到的数据进行HASH计算
	3. 比较两个摘要值是否相等
2. **AEAD（TLS 1.3）**
	1. 数据加密时自动计算认证标签，解密时自动验证，无需单独的 HMAC 计算。