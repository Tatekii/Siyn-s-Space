# 调度器
在 React 16 及其之前，更新是**同步的、不可打断**的：

- **大任务（如大型组件树的渲染）会阻塞主线程**，导致 UI 卡顿，影响交互。
- **无法区分高低优先级**，例如：
	- 用户输入（高优先级）可能会因为动画或列表渲染（低优先级）而被阻塞。

React 16+ 引入 **Scheduler**，基于 **时间切片（Time Slicing）** 和 **任务优先级调度（Task Scheduling）**，让 React 能在适当的时候暂停、恢复或取消任务。

## 任务调度
Scheduler 使用**最小堆（Min Heap）** 维护任务队列`updateQueue`，每个任务根据**到期时间**排序：
- **高优先级任务** 先执行
- **过期任务** 立即执行
- **低优先级任务** 可延迟执行


## 时间切片
React 在每一帧中预留了时间给 js 任务执行(`5ms`)，react旧的stackReconcile架构，组件的递归更新不可中止，会超过这个时间导致渲染线程被占用页面卡顿。

切分时间片及重新请求新的切片：
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
### performance
和 JavaScript 中其他可用的时间类函数（比如[`Date.now`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date/now)）不同的是，`window.performance.now()`返回的时间戳没有被限制在一毫秒的精确度内，相反，它们以浮点数的形式表示时间，精度最高可达微秒级。

另外一个不同点是，`window.performance.now()`是以一个恒定的速率慢慢增加的，它不会受到系统时间的影响（系统时钟可能会被手动调整或被 NTP 等软件篡改）。另外，`performance.timing.navigationStart + performance.now()` 约等于 `Date.now()`。

### MessageChannel
MessageChannel 是基于异步消息传递的。当通过 MessageChannel 发送消息时，它会创建一个宏任务，所以可以模拟setTimeout(,0)，并且延迟比setTimeout更小。
