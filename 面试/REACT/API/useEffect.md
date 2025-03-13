## 核心概念
useEffect 主要用于在 **组件渲染后执行副作用**。

**执行时机：**
- **初次渲染时** 执行 effect。
- **依赖项变化时** 执行 effect，并清除旧的 cleanup（如果提供了）。
- **组件卸载时** 执行 cleanup。

## 内部存储
React 通过 fiber.updateQueue 维护 effect 队列：
```jsx
// updateQueue:[]
const effect = {
  create: effectFunction,  // 需要执行的副作用
  destroy: cleanupFunction, // 清理副作用的函数
  deps: [dep1, dep2], // 依赖项
  next: null // 链表结构，指向下一个 effect
};
```