# 定义
`ERC20`是以太坊上的代币标准，来自2015年11月V神参与的[`EIP20`](https://eips.ethereum.org/EIPS/eip-20)。它实现了代币转账的基本逻辑：

- 账户余额(balanceOf())
- 转账(transfer())
- 授权转账(transferFrom())
- 授权(approve())
- 代币总供给(totalSupply())
- 授权转账额度(allowance())
- 代币信息（可选）：名称(name())，代号(symbol())，小数位数(decimals())

# IERC20
`IERC20`是`ERC20`代币标准的接口合约，规定了`ERC20`代币需要实现的函数和事件。

## 事件

### Transfer
```sol
/** * @dev 释放条件：当 `value` 单位的货币从账户 (`from`) 转账到另一账户 (`to`)时. */ event Transfer(address indexed from, address indexed to, uint256 value);
```

### Approval
```sol
/** * @dev 释放条件：当 `value` 单位的货币从账户 (`owner`) 授权给另一账户 (`spender`)时. */ event Approval(address indexed owner, address indexed spender, uint256 value)
```
## 函数

### totalSupply()
返回代币总供给量
```sol
function totalSupply() external view returns (uint256);
```

### balanceoOf()
返回账户余额
```sol
function balanceOf(address account) external view returns (uint256);
```

### transfer()
从调用者账户转账到目标地址
```sol
function transfer(address to,uint256 amount) external returns (bool);
```

### allowance()
返回授权额度
```sol
/**
 * @dev 返回`owner`账户授权给`spender`账户的额度，默认为0。
 *
 * 当{approve} 或 {transferFrom} 被调用时，`allowance`会改变.
 */
function allowance(address owner, address spender) external view returns (uint256);

```

### approve()
授权
```sol
/**
 * @dev 调用者账户给`spender`账户授权 `amount`数量代币。
 *
 * 如果成功，返回 `true`.
 *
 * 释放 {Approval} 事件.
 */
function approve(address spender, uint256 amount) external returns (bool);

```

### transferFrom()
授权转账
```sol
/**
 * @dev 通过授权机制，从`from`账户向`to`账户转账`amount`数量代币。转账的部分会从调用者的`allowance`中扣除。
 *
 * 如果成功，返回 `true`.
 *
 * 释放 {Transfer} 事件.
 */
function transferFrom(
    address from,
    address to,
    uint256 amount
) external returns (bool);

```