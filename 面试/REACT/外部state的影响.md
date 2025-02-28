In **React Concurrent Mode**, external state (such as global variables, event listeners, or external libraries like Redux, Zustand, or RxJS) can **cause inconsistencies** because React may **pause, resume, or restart rendering**. This means that external state updates **might not be in sync** with React’s rendering cycle.

## 解决方法
1. useRef
2. useSyncExternalStore

## useSyncExternalStore
订阅外部状态的更新
```javascript
useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```
**🔹 Step 1: Hook Initialization**
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
