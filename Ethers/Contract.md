# Contract Class
Contracts 类是部署在以太坊网络上的合约的抽象。它可以获取交易结果和事件，并与智能合约进行交互。

```js
const contract = new ethers.Contract(`address`, `abi`, `provider`);
```