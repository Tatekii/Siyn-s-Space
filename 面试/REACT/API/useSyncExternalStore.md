## 核心概念
useSyncExternalStore 是 **React 18** 引入的一个 Hook，专门用于订阅外部存储（如 Redux、Zustand、浏览器 API）时，确保组件状态在 **并发渲染模式**（Concurrent Mode）下保持一致。

**why**
In **React Concurrent Mode**, external state (such as global variables, event listeners, or external libraries like Redux, Zustand, or RxJS) can **cause inconsistencies** because React may **pause, resume, or restart rendering**. This means that external state updates **might not be in sync** with React’s rendering cycle.

## 实现解析
```js
// 伪代码
function useSyncExternalStore(subscribe, getSnapshot) {
  // 获取当前快照数据
  const [state, setState] = useState(getSnapshot());

  // 订阅外部存储的数据变化
  useEffect(() => {
    // 外部存储发生变化时触发的回调函数
    function handleStoreChange() {
      const newValue = getSnapshot();
      if (newValue !== state) {
        // 当新的值与当前状态不一致时，更新状态
        setState(newValue); // React 会将这个更新加入更新队列
      }
    }

    // 订阅外部数据源变化
    const unsubscribe = subscribe(handleStoreChange);

    // 清理订阅
    return () => unsubscribe();
  }, [subscribe, getSnapshot, state]); // 当订阅函数或 `getSnapshot` 改变时重新订阅

  return state;
}
```
### hook初始化
1. 在fiber的updateQueue中新建一个更新(useState);
2. 执行getSnapshot()获取外部最新数据置为该hook的memorizedState;
3. 订阅外部store的数据变更

### Render阶段
- 协调过程中，再执行一次getSnapshot();
- 如果getSnapshot() 返回值与旧值不一致，需要标记为需要re-render;
- 如果协调被中断了，不会更新snapShot，避免闪烁。
```javascript
const newSnapshot = getSnapshot();
	  
if (hook.memoizedState !== newSnapshot) {
	markWorkInProgressReceivedUpdate(); // Mark Fiber for re-render
}else{
	setState(newSnapshot)
}
```

### Commit阶段
- 更新到DOM中后，订阅一个回调
	- 再次执行getSnapShot()
	- 如果返回值不一致，再次标记为需要re-render。