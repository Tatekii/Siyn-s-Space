## React的架构演变
## <16.8 旧架构
- `Stack Reconciler`
	- 数据保存在递归的调用[[栈]] 中
	- 递归更新不可中断
- `Renderer`
### >16.8 新架构
- `Scheduler`
- `Fiber Reconciler`
	- 协调过程可中断与恢复
	- 时间切片
	- 更新任务可区分优先级
- `Renderer`
## 旧架构下的生命周期
![](../../assets/Pasted%20image%2020250226175957.png)

## Fiber架构下的生命周期
```text
【Update】
	⬇️
【Schduler】
	⬇️
【Render】
	⬇️
- {begin work} [向下生成/执行fiber链表，使用props,state执行渲染函数，hooks执行/状态计算， 打标flags]
	⬇️
- {complete work} [根据flags输出虚拟DOM=>stateNode，向上冒泡flags]
	⬇️
【Commit】
执行`effectList`,用来保存 fiber 节点需要执行副作用的单向链表，执行相应的 DOM 操作。
	⬇️
- {commitBeforeMutationEffects}
	- getSnapshotBeforeUpdate
	- flushPassiveEffects
	⬇️
- {commitMutationEffects}
	- 解绑`ref`
	- ⚠️ 同步执行`useLayoutEffect`的销毁函数
	- 根据不同的标记执行DOM操作
	- `componentWillUnmount`
【Current切换】
	⬇️
【Layout】
	⬇️
- {commitLayoutEffects}
	- 赋值新的`ref`
	- ⚠️ 同步执行`useLayoutEffect`的回调
	- ⚠️ 异步执行`useEffect`的销毁和回调
	- `componentDidMount`
	- `componentDidUpdate`
- {执行 render 函数的回调}
```

### 总结
#### Mount阶段
1. constructor()
2. static getDerivedStateFromProps()
3. render()
4. *UNSAFE_componentWillMount()*
5. componentDidMount()
#### Update阶段
 1. *UNSAFE_componentWillReceiveProps()*
1. static getDerivedStateFromProps()
2. shouldComponentUpdate()
3. render()
4. getSnapshotBeforeUpdate()
5. *UNSAFE_componentWillUpdate()*
6. componentDidUpdate()
#### UNMOUNT阶段
7. componentWillUnmount
#### 错误
1. static getDerivedStateFromError()
2. componentDidCatch()


### `FiberNode`
用于保存一个React组件的状态，输入及输出的对象。
```javascript
function FiberNode(
  this: $FlowFixMe,
  tag: WorkTag,
  pendingProps: mixed,
  key: null | string,
  mode: TypeOfMode,
) {
  // Instance
  this.tag = tag;
	/*
		0 Function 组件
		1 Class组件
		9 ContextConsumer
		10 ContextProvider
		11 ForwardRef
		...
	*/
  this.key = key; // diff用
  this.elementType = null;
  this.type = null;
  this.stateNode = null; // DOM节点

  // Fiber
  this.return = null;
  this.child = null;
  this.sibling = null;
  this.index = 0;

  this.ref = null;
  this.refCleanup = null;

	
  this.pendingProps = pendingProps;
  this.memoizedProps = null;
  this.updateQueue = null;
  this.memoizedState = null;
  this.dependencies = null;

  this.mode = mode;

	/** 更新标记
		本节点更新标记与子树中的更新标记
	*/
  this.flags = NoFlags;
  this.subtreeFlags = NoFlags;
  this.deletions = null;

	/** Lanes模型
		判断task优先级与从属关系		
	*/
  this.lanes = NoLanes;
  this.childLanes = NoLanes;

  /* diff匹配的fiber节点 */
  this.alternate = null;
}

```

## `Scheduler`
基于`requestIdleCalllback`封装的浏览器空闲回调执行函数，调度协调的进行。
```javascript
window.requestIdleCallback(callback:Function, options:{timeout:number})
// timeout 可设置运行的deadline
```

## `Fiber Reconciler`
[[FiberReconciliation]]
执行fiber树的协调，虚拟dom的diff，打上更新标记，并收集副作用。


## `Renderer`
根据不同的标记更新到真实DOM结构上。


