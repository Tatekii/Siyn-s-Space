# 定义
`interface`为一个合约的一种规范，它指定了合约应该提供哪些功能和行为，但并不涉及具体实现的细节。

```sol
pragma solidity ^0.8.0;

// 定义接口
interface MyInterface {
		//接口中的函数必须定义为external，因为设计接口的目的是提供给外部调用。
		//函数接口中，参数名可以省略，myFunction(uint256)的写法也是可以的。
    function myFunction(uint256 x) external returns (uint256);
}

// 实现接口的合约
contract MyContract {
    function myFunction(uint256 x) external returns (uint256) {
        // 实现函数的具体逻辑
        return x * 2;
    }
}

// 使用接口调用函数的合约
contract CallerContract {
    MyInterface public myContract;

		//传入MyContract的地址
    constructor(address contractAddress) {
        myContract = MyInterface(contractAddress);
    }
		//通过接口调用MyContract中的myFunction函数（接口调用我们会在下一节中详细讲解）
    function callInterface(uint256 value) public returns (uint256) {
        uint256 result = myContract.myFunction(value);
        return result;
    }
}
```

# 特性
1. 接口中的函数必须定义为`external`
2. 接口无法继承其它合约，但可以继承其它接口
3. 接口不能实现任何函数
4. 接口不能定义构造函数
5. 接口不能定义状态变量
6. 函数接口中，参数名可以省略
7. 继承接口的非抽象合约必须实现接口定义的所有功能

# 用法
## 接口调用
- 接口名
- 实现接口的合约地址
- 调用的函数
```sol
MyInterface(contractAddress).myFunction();
```
也可使用接口变量
```sol
//先定义一个接口变量otherContract
OtherContractInterface  otherContract = OtherContractInterface(otherContractAddress);
//随后使用interface.getValue()调用otherContract的getValue函数
otherContract.getValue();
```