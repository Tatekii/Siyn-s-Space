# ConcurrentMode
react新架构引入的并发渲染模式，目标是不阻塞60hz(~16.7ms内执行完新一帧的渲染)的屏幕内容更新频率（不阻塞浏览器的渲染线程）。

包含三个部分：
1. 不阻塞渲染
2. 更新可区分优先级
3. 更新可中断与恢复

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