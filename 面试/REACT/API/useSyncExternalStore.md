## 核心概念
useSyncExternalStore 是 **React 18** 引入的一个 Hook，专门用于订阅外部存储（如 Redux、Zustand、浏览器 API）时，确保组件状态在 **并发渲染模式**（Concurrent Mode）下保持一致。

**why**
In **React Concurrent Mode**, external state (such as global variables, event listeners, or external libraries like Redux, Zustand, or RxJS) can **cause inconsistencies** because React may **pause, resume, or restart rendering**. This means that external state updates **might not be in sync** with React’s rendering cycle.

## 实现解析
```js
function useSyncExternalStore(subscribe, getSnapshot) {

  // 使用useState
  const [state, setState] = useState(getSnapshot());

  useEffect(() => {
    function handleStoreChange() {
      setState(getSnapshot()); // 组件状态更新
    }

    const unsubscribe = subscribe(handleStoreChange);
    return unsubscribe;
  }, []);

  return state;
}
```
### hook初始化
1. 在fiber的updateQueue中新建一个更新;
2. 执行getSnapshot()获取外部最新数据置为memorizedState;
3. 
- useSyncExternalStore initializes an **update queue** inside the current Fiber.
- Calls **getSnapshot()** to get the current value of the external store.
- Stores the value inside a **hook state**.
- Registers a **subscription** with subscribe().
```javascript
function useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot) {
  const fiber = currentlyRenderingFiber;
  const hook = mountWorkInProgressHook(); // Creates a new hook in the Fiber tree
  
  let snapshot = getSnapshot();
  
  hook.memoizedState = snapshot;  // Stores the snapshot in hook state

  return snapshot;
}
```

**🔹 Step 2: Render Phase (Work Loop)**
- During reconciliation, React calls **getSnapshot()** inside the render phase.
- If getSnapshot() returns a different value from the last render, React marks this component as **needing a re-render**.
- If React **pauses the render**, it will **not update the snapshot**, preventing tearing.
```javascript
function updateSyncExternalStore(subscribe, getSnapshot) {
  const fiber = currentlyRenderingFiber;
  const hook = updateWorkInProgressHook();
  
  const newSnapshot = getSnapshot();
  
  if (hook.memoizedState !== newSnapshot) {
    markWorkInProgressReceivedUpdate(); // Mark Fiber for re-render
  }

  return newSnapshot;
}
```
**🔹 Step 3: Lane System (Tracking Priority)**
- React assigns an **update lane** based on the importance of the update:
- **User interactions (clicks, typing): High Priority**
-  **External state updates (from store): Low Priority**
- React ensures that the external store updates follow the correct **priority queue**.

**🔹 Step 4: Commit Phase**
- After React **commits the changes** to the DOM, it registers the **subscription callback**.
- The callback:
	- Calls **getSnapshot()** to get the latest value.
	- If the snapshot has changed, it triggers a **re-render**.
```javascript
function useSyncExternalStore(subscribe, getSnapshot) {
  const [state, setState] = useState(getSnapshot);

  useEffect(() => {
    function handleStoreChange() {
      const newSnapshot = getSnapshot();
      setState(newSnapshot);
    }

    const unsubscribe = subscribe(handleStoreChange);

    // Check if state changed during mount to avoid stale data
    handleStoreChange();

    return () => {
      unsubscribe();
    };
  }, [subscribe, getSnapshot]);

  return state;
}
```
