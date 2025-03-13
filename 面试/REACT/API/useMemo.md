## 核心概念
- useMemo 用于 **缓存计算结果**，避免组件 **重复计算**。
- 仅当 **依赖项发生变化** 时，才会重新计算值。
- **返回值：** useMemo **返回缓存的值**，而不是函数。

## 内部存储
useMemo 和 [[useState]] 类似，它的状态也存储在 fiber.memoizedState[[链表]] 中:
```jsx
// memorizedState:
const memo = {
  memoizedState: cachedValue, // 存储计算结果
  deps: [dep1, dep2], // 依赖项
  next: null
};
```