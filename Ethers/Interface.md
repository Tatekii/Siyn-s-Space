# Interface Class
接口类抽象了与以太坊网络上的合约交互所需的`ABI`编码和解码。
```js
// 利用abi生成
const interface = ethers.Interface(abi)
// 直接从contract中获取
const interface2 = contract.interface
```

## Interface.Method
### `getSighash()`
获取[函数选择器](../Solidity/函数.md#函数选择器)，参数为函数名或[函数签名](../Solidity/函数.md#函数签名)。    
```js
    interface.getSighash("balanceOf");
```
    
### `encodeDeploy()`
编码构造器的参数，然后可以附在合约字节码的后面。
    
```js
	interface.encodeDeploy("Wrapped ETH", "WETH");
```
    
### `encodeFunctionData()`
编码函数的[`calldata`](../Solidity/变量类型.md#`calldata`)。
    
```js
	interface.encodeFunctionData("balanceOf", ["0xc778417e063141139fce010982780140aa0cd5ab"]);
```
    
### `decodeFunctionResult()`
解码函数的返回值。
    
```js
	interface.decodeFunctionResult("balanceOf", resultData)
```