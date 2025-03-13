## 概念
useCallback 的作用是缓存函数的引用，避免因组件重新渲染而创建新的函数实例。基于 useMemo 实现，核心思路是**依赖项未变化时返回相同的函数引用**。
```tsx
export function useCallback<T extends Function>(callback: T, deps: DependencyList): T {
  return useMemo(() => callback, deps);
}
```

## 执行流程

### **初次渲染时**
- useMemo 计算 callback，将其存入 memoizedState，并存储 deps 依赖数组。
- 返回 callback 的引用。

### **组件重新渲染时**
- React 比较当前 deps 和上一次的 deps 是否相等：
- **相等**：直接返回缓存的 callback，避免创建新的函数实例。
- **不相等**：重新计算 callback，更新 memoizedState 并返回新函数。