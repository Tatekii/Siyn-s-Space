# 定义
库（library）是一种特殊的合约。
库与合约类似，但主要用于重用代码。库包含其他合约可以调用的函数。

```sol
librbary CustomLib{}
```

## 注意
1. 库不能定义[`storage`](变量类型.md#`storage`)状态变量
2. 库不能发送接收以太币
3. 库不可以被销毁，因为它是无状态的
4. 库不能继承和被继承

# 用法
### 附加
将库合约中的函数附加到任何类型中。
```sol
//将 MathLibrary 库附加到 uint256 类型上
//这样所有的 uint256 类型的变量都可以直接使用 MathLibrary 库中的函数

using MathLibrary for uint256;

```

⚠️注意：
如果库函数有参数的话，变量 a.functionName()的方式会自动把变量作为函数的第一个参数传入。
```sol
library MathLibrary {
    function square(uint256 x) external pure returns (uint256) {
        return x * x;
    }
}

contract XXX{
    using MathLibrary for uint256;

    function calculateSquare(uint256 y) external pure returns (uint256) {
        // 调用库合约的函数，y 变量将默认作为第一个参数传入square函数。
        return y.square();
    }
}
```
