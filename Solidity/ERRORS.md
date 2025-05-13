# Hardhat Ignition 的 `libraries` 链接为空
## 检查
Hardhat **编译器** 在生成 `Factory` 合约的 artifact 时，**没有记录下** `Factory` 合约需要链接一个名为 `CrowdfundingLib` 的库。Hardhat Ignition 是依赖于编译生成的 artifact 来确定链接需求的。

在这种情况下，问题可能出在以下更底层或更隐蔽的方面：

1. **库函数是否被实际需要链接？** 确保你在 `Factory` 合约中调用的 `CrowdfundingLib` 中的函数是需要通过 `using for` 方式调用的。例如，如果你只是导入了库，但只调用了库中的 `public` 或 `external` 函数（这很少见，因为库通常是 `internal` 或用于链接），或者你调用的方式编译器认为不需要链接，那么 artifact 中就不会出现链接信息。使用 `using for` 主要用于将 `internal` 库函数附加到类型上。
    
2. **检查 Artifact 文件:** 这是最直接的方法来验证编译器是否认为需要链接。
    
    - 找到你的 Hardhat 项目中的 `artifacts/contracts/Factory.sol/Factory.json` 文件（路径可能略有不同取决于你的目录结构）。
    - 打开这个 JSON 文件。
    - 查找一个名为 `linkReferences` 的顶层键。
    - 在 `linkReferences` 对象内部，你会看到一个以库文件路径为键的对象（例如 `"contracts/CrowdfundingLib.sol"`）。
    - 在这个文件路径对象内部，你会看到一个以库名称为键的对象（例如 `"CrowdfundingLib"`），里面包含字节码需要链接的位置信息。
    
    **如果 `linkReferences` 对象是空的，或者里面没有 `"CrowdfundingLib"` 这个键（在正确的库文件路径下），那么问题就出在编译阶段——编译器认为 Factory 不需要链接 CrowdfundingLib。** 你的 Ignition 配置是正确的，但它试图链接一个编译器认为不需要的库。
    
3. **Solidity 版本或编译器配置:** 检查你的 `hardhat.config.js` 中的 `solidity` 部分。确保 Solidity 版本是一个稳定的、不会有奇怪编译行为的版本。如果使用了复杂的编译器设置（如 `viaIR` 或特定的优化器配置），尝试暂时简化或禁用它们，看看是否影响编译结果。
    
4. **文件名与合约名/库名大小写和符号:** 尽管你可能觉得匹配，但有时候操作系统或工具对大小写敏感性、特殊符号处理可能导致细微差异。Double check 文件名 (`CrowdfundingLib.sol`)、Solidity 定义 (`library CrowdfundingLib`) 和 Ignition 引用 (`m.contract("CrowdfundingLib")` 的 artifact 名称以及 `libraries: { CrowdfundingLib: ... }` 的键) 是否完全一致，包括大小写。
## 解决
根据 Solidity 的规则：

1. **`internal` 库函数 + `using LibraryName for Type;`:** 这是最常见的库使用模式。库函数需要将应用的对象作为其**第一个**参数（通常命名为 `self` 或使用下划线），并且在调用时使用 `value.function(other_args)` 的语法。编译器会处理链接，通过 `DELEGATECALL` 或类似机制在运行时执行库代码。
2. **`public` 或 `external` 库函数:** 可以通过 `LibraryName.function(all_args)` 的语法直接调用。这也需要链接，但编译器处理的方式可能略有不同。
3. **`internal` 库函数 + 直接通过 `LibraryName.function(all_args)` 调用:** 这是**无效的 Solidity 语法**。你不能直接通过库名称调用其 `internal` 函数，除非是在库内部或从继承了库的合约中（库不能被继承，但可以包含在同一个文件中）。

你的 `CrowdfundingLib` 函数是 `internal` 的，但它们的签名（例如 `calculateTokenPrice(uint256 _sold, uint256 _amount)`）**并不是**按照 `using for` 的模式设计的（缺少隐式的 `self` 参数作为第一个参数）。同时，你在 `Factory.sol` 中使用的调用语法 `CrowdfundingLib.function(...)` 又是为 `public`/`external` 函数准备的。

这种矛盾导致 Hardhat **编译器** 在处理 `Factory.sol` 时非常困惑。它看到 `using for *;`，但实际的函数调用却不是通过 `value.function()` 语法进行的，而且调用的函数签名也与 `using for` 模式不符。因此，编译器**没有**在 `Factory` 合约的 artifact (`Factory.json`) 中生成一个明确的 `linkReferences` 条目来表明它需要链接 `CrowdfundingLib`。

当 Hardhat Ignition 运行时，它检查 `Factory` 的 artifact，发现没有 `linkReferences` 指向 `CrowdfundingLib`，但你的 Ignition 模块中却显式地提供了 `libraries: { CrowdfundingLib: ... }` 的配置。Ignition 认为你在尝试链接一个编译器认为不需要链接的库，从而抛出 `IGN719` 错误。