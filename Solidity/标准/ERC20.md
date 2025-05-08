# ERC20
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



## 实现一个ERC20
```sol
pragma solidity >=0.8.2;

  

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol";

  

contract MyERC20 is IERC20 {

mapping(address => uint256) public override balanceOf;

mapping(address => mapping(address => uint256)) public override allowance;

  

uint256 public override totalSupply;

  

string public name;

string public symbol;

  

uint8 public decimal = 18;

  

// 初始化代币名，代号

constructor(string memory _name, string memory _symbol) {

name = _name;

symbol = _symbol;

}

  

// 账户之间转移代币

function transfer(address recipient, uint256 amount)

public

override

returns (bool)

{

balanceOf[msg.sender] -= amount;

balanceOf[recipient] += amount;

emit Transfer(msg.sender, recipient, amount);

return true;

}

  

// 被授权方spender可以支配调用者amount数量的代币

function approve(address spender, uint256 amount)

public

override

returns (bool)

{

allowance[msg.sender][spender] = amount;

emit Approval(msg.sender, spender, amount);

return true;

}

  

function transferFrom(

address sender,

address recipient,

uint256 amount

) public override returns (bool) {

allowance[sender][msg.sender] -= amount;

balanceOf[sender] -= amount;

balanceOf[recipient] += amount;

emit Transfer(sender, recipient, amount);

return true;

}

  

function mint(uint amount) external{

// require(msg.sender == ,'not owner');

balanceOf[msg.sender] += amount;

totalSupply += amount;

emit Transfer(address(0), msg.sender, amount);

}

}
```