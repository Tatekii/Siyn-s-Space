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