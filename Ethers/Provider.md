# Provider Class
`Provider`类是一个为以太坊网络连接提供抽象的类，它提供对区块链及其状态的**只读**访问。

## `ethers.getDefaultProvider()`
```js
const provider = ethers.getDefaultProvider("network");
```
默认节点有速度限制。
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
对于像主网、Ropsten、Rinkeby 等公共以太坊网络，
可以找到提供免费访问或注册付费服务的公共节点提供者。

# Provider.method
### getBalance()
获取账户的余额，单位为 wei。

### getNetwork()
获取有关网络的信息，例如网络名称、链ID等。

### getBlockNumber()
获取当前区块高度。

### getTransactionCount()
返回特定地址的交易数量，包括收入和支出的交易。

### getFeeData()
获取与费用相关的数据，如当前的 gas 价格和估计的 gas 限制。

### getBlock()
检索有关特定区块的详细信息，包括哈希、时间戳、交易等。

### getCode()
获取部署智能合约的字节码。