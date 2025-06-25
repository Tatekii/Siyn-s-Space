## 核心概念
useEffect 主要用于在 **组件渲染后执行副作用**。

**执行时机：**
- **初次渲染时** 执行 effect。
- **依赖项变化时** 执行 effect，并执行 cleanup（如果提供了）。
- **组件卸载时** 执行 cleanup。

## 内部存储
React 通过 fiber.updateQueue 维护 effect 队列：
```jsx
// memorizedState:
const effectHook = { 
	memoizedState: null, // 对于 useEffect，这里通常是 effect 本身的链表 
	queue: null, // 对于 useEffect，通常不直接使用这个（更常用于 useState/useReducer） 
	next: null, // 指向组件中下一个 Hook 对象 // ... 其他 Hook 通用属性 
}; 
// memoizedState 内部可能指向的 Effect 链表中的一个 Effect 对象
const effect = { 
	tag: null, // 描述 effect 的类型，如 Passive | Layout | HookHasEffect 
	create: null, // useEffect 的回调函数（副作用函数，例如 () => { console.log(count); }） 
	destroy: null, // effect 的清理函数（如果 create 函数返回了一个函数） 
	deps: null, // 依赖数组（例如 [count]） 
	next: null, // 指向同一个 Hook 上可能存在的下一个 Effect（多个相同 effect 钩子时） 
	// ... 其他内部属性 
};
```

### Effect.next
理想状态下，一个useEffect只会有一个活跃的create和destroy。

协调过程中，中断/继续，deps的变化会使得同一个effect使用不同的上下文执行。在某个状态的effect还有活跃的状态（比如上个deps的destroy还没有执行时），新状态的effect对象会被链接到旧effect.next上，确保所有effect都执行完毕。

在commit阶段后，才会把旧的effect对象清除。

## 生命周期
| **生命周期**                 | useEffect   |
| ------------------------ | ----------- |
| **componentDidMount**    | 依赖为空 [] 时触发 |
| **componentDidUpdate**   | 依赖项变化时触发    |
| **componentWillUnmount** | 触发清理函数      |