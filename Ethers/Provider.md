## `ethers.getDefaultProvider()`
```js
const provider = ethers.getDefaultProvider("network");
```

### 支持的网络
- goerli 
- kovan 
- sepolia 
- classicKotti 
- optimism-goerli 
- arbitrum-goerli 
- matic-mumbai 
- bnbt
### 支持的服务
- alchemy 
- ankr 
- cloudflare 
- chainstack 
- etherscan 
- infura 
- publicPolygon 
- quicknode
## `ethers.JsonRpcProvider()`
它允许我们使用 JSON-RPC（远程过程调用）协议连接到以太坊节点。它主要设计用于通过 HTTP 使用。

```js
const provider = new ethers.JsonRpcProvider('YOUR PROVIDER ADDRESS');
```

### 获取URL
#### Infura
https://mainnet.infura.io/v3/YOUR_PROJECT_ID

#### Alchemy
https://eth-mainnet.alchemyapi.io/v2/YOUR_API_KEY

#### 本地节点
使用 Ganache 或 Geth 等工具运行本地节点。默认的 URL 如下所示： http://localhost:8545

#### 公共以太坊网络
对于像主网、Ropsten、Rinkeby 等公共以太坊网络，可以找到提供免费访问或注册付费服务的公共节点提供者。