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

注意⚠️：仅在没有更高优先级任务出现，而是时间切片到期时，更新可以中断并继续。

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
			pending: null, // 指向💡待处理的UpdateQueue链表头
		}
	},
	this.memorizedState = {} // 上次更新完的状态
	this.memorizedProps = {} // 上次更新完的参数
}
```
每个更新对象中lane的记录
```typescript
export interface Update<State> {
	action: Action<State> // 具体更新行为
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

## 更新插队
当一个更新被调度时，React 会给它分配一个优先级。

**当一个更高优先级的更新到来时，即使当前正在处理一个低优先级的更新，React 也会做以下事情：**

1. **中断当前低优先级工作：** 在 Render 阶段（也就是你提到的 `beginWork` 和 `completeWork` 阶段），React 会定期检查是否有更高优先级的任务。如果发现有，它会立即**中止**当前低优先级的 `workInProgress` 树的构建。
2. **放弃（或保留）已完成的低优先级工作：**
    - **通常情况下，已完成的部分会被放弃。** 因为低优先级更新的 `workInProgress` 树尚未完全构建，也没有进入 Commit 阶段，所以它只是内存中的一个草稿。当更高优先级的更新到来时，React 会创建一个新的 `workInProgress` 树，从头开始处理高优先级更新。
    - **在某些特定场景下，如果低优先级更新已经完成了某个子树的 `completeWork` 并且这个子树的副作用可以安全地被更高优先级更新“合并”或者“复用”，理论上是可能的，但这种情况比较复杂且不常见，通常认为 Render 阶段是被完全中断并重新开始的。** 核心思想是保证最终 UI 的一致性。
3. **开始处理高优先级更新：** React 会立即开始构建一个新的 `workInProgress` 树，用于处理这个高优先级的更新。这个高优先级更新会拥有更高的执行权。
4. **Commit 高优先级更新：** 一旦高优先级更新的 `workInProgress` 树构建完成，它会立即进入 Commit 阶段，将新的 UI 渲染到屏幕上。