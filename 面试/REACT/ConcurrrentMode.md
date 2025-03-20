# ConcurrentMode
react新架构引入的并发渲染模式，目标是不阻塞60hz(~16.7ms内执行完新一帧的渲染)的屏幕内容更新频率（不阻塞浏览器的渲染线程）。

Concurrent模式下，协调为workLoopConcurrent，可中断/插队
ConcurrentMode = [Lane模型](#Lane模型) + [Scheduler](Scheduler.md)
⭐️工作流程：
- 更新存在优先级，高优先的更新会插队低优先级更新
- 每次fiber的协调都会判断时间切片是否到期
- 在时间切片到期时，还要检查一遍下一个更新是否”过期“，如果超过过期时间会继续说执行。

## Lane模型
Lane（车道） 代表不同优先级的更新任务，类似于马路上的不同车道，不同任务可以在不同车道上同时执行，而不是按队列依次阻塞执行。

React 18 中，Lane 共有 **31** 种，主要分为 **7 大类**：

| **Lane**                   | **数值（二进制）**                       | 过期时间   | **说明**                             |
| -------------------------- | --------------------------------- | ------ | ---------------------------------- |
| **SyncLane**               | 0b0000000000000000000000000000001 | -1ms   | **同步更新（最高优先级）**，如 onClick setState |
| **InputContinuousLane**    | 0b0000000000000000000000000000010 | 250ms  | **输入相关的更新**，如 onChange setState    |
| **DefaultLanes**           | 0b0000000000000000000000000011100 | 5000ms | **默认更新**，如 effect setState         |
| **TransitionLanes**        | 0b0000000000111111111111111100000 | ~      | **过渡更新**，如 useTransition 触发的 UI 更新 |
| **RetryLanes**             | 0b0000000111000000000000000000000 | ~      | **重试更新**，用于 Suspense 失败后重新尝试       |
| **SelectiveHydrationLane** | 0b0000001000000000000000000000000 | ~      | **水合过程的选择性更新**，用于 SSR（服务器端渲染）      |
| **IdleLane**               | 0b0100000000000000000000000000000 | ~      | **空闲更新（最低优先级）**，适用于后台任务            |

### Lane合并
使用二进制位（bitmask）来表示不同优先级的 Lane，不同 Lane 可以通过 **按位或（|）** 操作 合并。
```tsx
const handleClick = () => {
  setCount((prev) => prev + 1); // SyncLane（同步执行）
  startTransition(() => {
    setCount((prev) => prev + 1); // TransitionLanes（延迟执行）
  });
};

// SyncLane 和 TransitionLanes 的 bitmask
SyncLane          = 0b0000000000000000000000000000001
TransitionLanes   = 0b0000000000111111111111111100000

// 事件回调中触发了 SyncLane 和 TransitionLane
const mergedLanes = SyncLane | TransitionLanes;

mergedLanes = 0b0000000000111111111111111100001
```


## 中断与继续
### 全局保存协调的进度
```javascript
// 全局
let workInProgress = null // 指向协调的fiber
let workInProgressRenderLane = NoLane // 指向协调中的优先级
```
### fiber内保存协调的进度
```javascript
class Fiber {
	this.updateQueue = {
		shared: {
			pending: null, // 指向待处理的更新head
		}
	},
	this.memorizedState = {} // 上次更新完的状态
	this.memorizedProps = {} // 上次更新完的参数
}
```
每个更新对象中lane的记录
```typescript
export interface Update<State> {
	action: Action<State>
	next: Update<any> | null
	lane: Lane
}
```
根节点中lanes的纪录
```javascript
class FiberRootNode {
	this.pendingLane: Lane // 每次更新都会将更新的lane值合并进根fiber的待处理lanes记录中
}
```
高优先级插队
```javascript
function renderRoot(root: FiberRootNode, lane: Lane) {

	const nextLane = getHighestPriorityLane(root.pendingLanes)
	
	if (compare(lane,nextLane)) {
		// NOTE 其他比 SyncLane 低的优先级或 NoLane，重新调度
		ensureRootIsScheduled(root)
	return
	}
}
```