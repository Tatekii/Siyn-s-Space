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


## 实现
```tsx
// ReactFiberHook
function mountMemo<T>(nextCreate: () => T, deps: DependencyList | null): T {
  const hook = mountWorkInProgressHook();
  const nextDeps = deps === null ? null : deps;
  const nextValue = nextCreate();
  hook.memoizedState = [nextValue, nextDeps];
  return nextValue;
}

function updateMemo<T>(nextCreate: () => T, deps: DependencyList | null): T {
  const hook = updateWorkInProgressHook();
  const prevState = hook.memoizedState;
  if (deps !== null) {
    const prevDeps = prevState[1];
    if (areHookInputsEqual(deps, prevDeps)) {
      return prevState[0]; // 直接返回缓存的值
    }
  }
  const nextValue = nextCreate();
  hook.memoizedState = [nextValue, deps];
  return nextValue;
}
```
### 实现解析
#### mountMemo：
- **初次渲染**时，存储 nextCreate() 以及 deps，并返回 nextValue。
#### updateMemo：
- **更新时**，比较 deps 依赖是否变化：
	- **没变**：直接返回之前缓存的 nextValue。
	- **变了**：重新执行 nextCreate()，生成新的 nextValue。

## 生命周期
| **生命周期**                      | useMemo       |
| ----------------------------- | ------------- |
| **componentWillReceiveProps** | 依赖项变化时重新计算    |
| **shouldComponentUpdate**     | 依赖项不变时阻止计算    |
| **render**                    | 在 render 期间执行 |
