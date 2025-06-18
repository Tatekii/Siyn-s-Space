`staticCall`方法是属于[Contract](Contract.md)类的编写方法分析，同类的还有`populateTransaction`和`estimateGas`方法。

`contract.函数名.staticCall()` 方法来模拟执行一个可能会改变状态的函数，但不实际向区块链提交这个状态改变。这相当于调用以太坊节点的 `eth_call`。这通常用于模拟状态改变函数的结果。如果函数调用成功，它将返回函数本身的返回值；如果函数调用失败，它将抛出异常。

```js
const tx = await contract.函数名.staticCall( 参数, {override}) console.log(`交易会成功吗？：`, tx)
```

- 函数名：为模拟调用的函数名。
- 参数：调用函数的参数。
- {override}：选填，可包含以下参数：
    - `from`：执行时的`msg.sender`，也就是你可以模拟任何一个人的调用，比如Vitalik。
    - `value`：执行时的`msg.value`。
    - `blockTag`：执行时的区块高度。
    - `gasPrice`
    - `gasLimit`
    - `nonce`