# Contract Class
Contracts 类是部署在以太坊网络上的合约的抽象。它可以获取交易结果和事件，并与智能合约进行交互。

## 读写限制
### 只读Contract
```js
const contract = new ethers.Contract(`address`, `abi`, `provider`);
```
⚠️只能调用[`pure`](../Solidity/函数.md#`pure`)和[`view`](../Solidity/函数.md#`view`)函数
⚠️无法执行交易`transaction`

### 读写Contract
```js
const contract = new ethers.Contract(`address`, `abi`, `signer`);
```
其中[Singer](Singer.md)是交易签名。


## 合约事件
### 检索事件
```js
const transferEvents = await contract.queryFilter('事件名', 起始区块, 结束区块)

```

### 监听事件
```js
contract.on('事件名',callback)
contract.once('事件名',callback) // 只监听一次
```

#### 事件过滤器
`ethers.js`中的合约类提供了`contract.filters`来简化过滤器的创建
```js
const filter = contract.filters.EVENT_NAME( ...args )
```
![filterArgs](../assets/Pasted%20image%2020250618122428.png)

1. 过滤来自`myAddress`地址的`Transfer`事件

```js
contract.filters.Transfer(myAddress)
```

2. 过滤所有发给 `myAddress`地址的`Transfer`事件

```js
contract.filters.Transfer(null, myAddress)
```

3. 过滤所有从 `myAddress`发给`otherAddress`的`Transfer`事件

```js
contract.filters.Transfer(myAddress, otherAddress)
```

4. 过滤所有发给`myAddress`或`otherAddress`的`Transfer`事件

```js
contract.filters.Transfer(null, [ myAddress, otherAddress ])
```
