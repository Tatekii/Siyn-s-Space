In **React Concurrent Mode**, external state (such as global variables, event listeners, or external libraries like Redux, Zustand, or RxJS) can **cause inconsistencies** because React may **pause, resume, or restart rendering**. This means that external state updates **might not be in sync** with React’s rendering cycle.

## 解决方法
1. useRef
2. useSyncExternalStore

## useSyncExternalStore
订阅外部状态的更新
```javascript
useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?)
```
1. beginWork
	`getSnapShot()`获取store的最新状态
2. commit
	订阅外部store的回调`subscribe(callback)`
3. trigger（callback）
	订阅的回调触发协调
4. 协调被丢弃（更新被插队）
	重新执行`getSnapShot()`获取store的最新状态
