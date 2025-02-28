# ConcurrentMode
react新架构引入的并发渲染模式，目标是不阻塞60hz(~16.7ms内执行完新一帧的渲染)的屏幕内容更新频率（不阻塞浏览器的渲染线程）。

React 在每一帧中预留了时间给 js 任务执行(`5ms`)，react旧的stackReconcile架构，组件的递归更新不可中止，会超过这个时间导致渲染线程被占用页面卡顿。

## 时间切片
切分时间片及重新请求新的切片。
```javascript
const timeSlice = 5 // 一个切片的时长为5毫秒
const taskQueue = [] // task队列
const channel = new MessageChannel() 

function processTaskQueue () {
	const startTime = performance.now()
	while(true){
		const currentTime = preformance.now()
		if(currentTime - stratTime >= timeSlice){
			// 超出切片长度，结束执行
			break
		}
		processTask() // 未超时，继续执行
	}

	if(taskQueue.length > 0){
		channel.port2.postMessage(null) // 超时后，请求下一个切片
	}
}

channel.port1.onmessage = processTaskQueue
processTaskQueue()
```
- `performance`
	和 JavaScript 中其他可用的时间类函数（比如[`Date.now`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/now)）不同的是，`window.performance.now()`返回的时间戳没有被限制在一毫秒的精确度内，相反，它们以浮点数的形式表示时间，精度最高可达微秒级。

	另外一个不同点是，`window.performance.now()`是以一个恒定的速率慢慢增加的，它不会受到系统时间的影响（系统时钟可能会被手动调整或被 NTP 等软件篡改）。另外，`performance.timing.navigationStart + performance.now()` 约等于 `Date.now()`。
- `MessageChannel`
	MessageChannel 是基于异步消息传递的。当通过 MessageChannel 发送消息时，它会创建一个宏任务，所以可以模拟setTimeout(,0)，并且延迟比setTimeout更小。

## 更新优先级
- **ImmediatePriority**, **直接优先级**，对应用户的 **click**、**input**、**focus** 等操作；
	- 过期时间-1ms
- **UserBlockingPriority**，**用户阻塞优先级**，对应用户的 **mousemove**、**scroll** 等操作；
	- 过期时间250ms
- **NormalPriority**，**普通优先级**，对应**网络请求**、**useTransition** 等操作；
	- 过期时间5000ms
- **LowPriority**，**低优先级**；
	- 过期时间10000ms
- **IdlePriority**，**空闲优先级**，如 **OffScreen**;
	- 过期时间1073741823ms

### 判断优先级
使用最小[[堆]]识别出目前updateQueue中最高优先级的update。

# 协调调度的模式
## Legacy
Legacy模式下，协调为workLoopSync不可中断

触发：
- event
- setTimeout
- network
## Concurrent
Concurrent模式下，协调为workLoopConcurrent，可中断/插队
特性：
- 每次fiber的协调都会判断时间切片是否到期
- 在时间切片到期时，还要检查一遍下一个更新是否”过期“，如果超过过期时间会继续说执行。
触发：
- Suspense
- useTransition
- offScreen

### 中断与继续
1. 保存协调的进度
	```javascript
	let workInProgress = null // 指向协调的fiber
	let workInProgressRenderLane = NoLane // 指向协调中的优先级
	```
2. lane的记录
	```javascript
	FiberRootNode.pendingLane = NoLanes // 每次更新都会讲更新的lane值合并进根fiber的待处理lanes记录中
	```
3. 高优先级插队
	每次拿到新的**时间片**以后，**workLoopConcurrent** 都会判断本次**协调**对应的**优先级**和上一次**时间片**到期中断的**协调**的**优先级**是否一样。如果一样，说明没有**更高优先级**的更新产生，可以继续上次未完成的**协调**；
	如果不一样，说明有**更高优先级**的更新进来，会抛弃之前已开始的**协调**过程，从**根节点**开始重新**协调**。等**高优先级更新**处理完成以后，再次从**根节点**开始处理**低优先级更新**。

  