## 定义
表示类的继承（继承另一个类/接口）。用于 **限制泛型的范围** 或 **条件类型判断**。

## 作用
- 继承接口`interface`，多个用逗号隔开
- 用作条件类型做约束

### 分布式条件类型
当条件类型的参数是 **联合类型** 时，TypeScript 会对联合类型的 **每个成员** 单独计算条件类型，并最终合并结果：
```ts
type Check<T> = T extends string ? "String" : "Other";

type Test = Check<"hello" | 42>;  // "String" | "Other"

// 等效于
Check<'hello'> | Check<42>
```

**如果要避免分布式行为**，可以使用 [] 包裹：
```ts
type NoDistribution<T> = [T] extends [string] ? "String" : "Other";
type Result = NoDistribution<"hello" | 42>; // "Other"
```
这里 [T] extends [string] 迫使 T 作为整体进行匹配，而不是对每个成员单独计算。
