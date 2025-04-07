# **React 如何追踪 Hook 的状态？Hooks 的原理是什么？**

React Hooks 的本质是 **通过一个内部的链表结构存储和管理组件状态**，并通过 **单一的调用顺序** 来确保 Hook 能正确地获取到对应的状态。React 追踪 Hook 状态的核心机制可以拆解为以下几个部分：

## **Hook 状态存储机制**

在 React **函数组件** 内，每次组件渲染时，React 需要知道：
- 组件内有哪些 Hook
- 每个 Hook 绑定了什么状态
- 下次渲染时如何找到正确的状态值

为了实现这一点，React **不把 Hook 状态绑定到变量上**，而是维护一个 **单独的 Hooks 链表**，并通过 **一个全局指针** 进行状态访问。

### **内部存储**

React 维护一个 **fiber 节点（fiber）**，这个 fiber 代表一个组件，并在 fiber.memoizedState 内部存储一个 **Hook 链表**：
```js
// Hook 数据结构
const hook = {
  memoizedState: null,   // Hook 存储的状态值
  next: null             // 指向下一个 Hook，形成链表结构
};
```

### **状态存储过程**

当组件执行时：
1. **React 通过 workInProgress Fiber 获取 memoizedState**（上一次渲染时存储的状态）。
2. **以链表的方式存储 Hook**，每次调用 Hook 时就创建或读取一个节点。
3. **利用全局指针 currentlyRenderingFiber 和 workInProgressHook** 来追踪 Hook 调用顺序，确保下次渲染能找到对应的状态。

## Hook执行顺序
React 依靠 **调用顺序** 来匹配 Hook 的状态，**Hooks 不能写在 if 或 for 语句中**，因为这会破坏调用顺序，导致状态不匹配。

**示例：多个 useState**
```jsx
function Counter() {
  const [count, setCount] = useState(0);   // 第一次调用 useState (index=0)
  const [text, setText] = useState("Hi");  // 第二次调用 useState (index=1)

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```
fiber内
```jsx
memoizedState : { state: 0, next -> { state: "Hi", next: null } }
```


# **React Hooks 在 Fiber 架构下的执行时机**
| **Hook**                  | **渲染阶段（Render Phase）** | **提交阶段（Commit Phase）**       |
| ------------------------- | ---------------------- | ---------------------------- |
| [[useState]] / useReducer | ✅ 计算状态，但不会触发 UI 更新     | —                            |
| [[useMemo]]               | ✅ 计算 memoized 值        | —                            |
| [[useCallback]]           | ✅ 计算 memoized 函数       | —                            |
| useContext                | ✅ 读取 Context           | —                            |
| [[useRef]]                | ✅ 创建 ref 对象            | ✅ ref.current 在 Commit 时同步更新 |
| [[useEffect]]             | —                      | ✅ **异步执行**（在 DOM 更新后运行）      |
| useLayoutEffect           | —                      | ✅ **同步执行**（在 DOM 更新后立即执行）    |
| useImperativeHandle       | —                      | ✅ 在 DOM 变更后执行                |
| useTransition             | ✅ 标记某些状态更新为并发          | —                            |
| useDeferredValue          | ✅标记某些状态更新为并发（低优先级      | —                            |
| [[useSyncExternalStore]]  | ✅ 读取外部存储的值             | ✅ 在 commit 阶段触发回调            |
