# Fiber的协调

## 双缓存机制
内存中同时会有两棵Fiber树，一个为对应此时真实DOM的currentFiberTree，一个是协调过程中操作的workInProgressFiberTree。
### 协调的执行
fiber树中执行协调的顺序类似中序遍历（DFS），先walk child，然后walk sibling，再返回上层（return）。

## 协调过程
### **渲染（Render）阶段**
>可中断
#### beginWork()
- 深度优先遍历fiber链表/[生成新的fiberNode](#fiberNode的生成)
- 执行渲染函数
- hooks执行
- 记录副作用类型[Flags](#Flags)
- 将fiber链接到[副作用](副作用.md)
- 使用bitmask(位掩码)标记更新`Fiber.flag`
#### completeWork()
- 根据flag输出新的虚拟DOM`stateNode`
- 回溯过程中冒泡更新标记`flags`

### **提交（Commit）阶段**
>不可中断
- 执行`effectList`(副作用链表)
- DOM更新到实际页面上
#### beforeMutation()
- getSnapshotBeforeMutation
#### mutation() ⭐️DOM已更新⭐️
- DOM 操作，如 appendChild、removeChild
#### layout()
##### commitLayoutEffect()
- useLayoutEffect cleanup
- useLayoutEffect
- componentDidMount
- componentDidUpdate
- ref
#### Passive() ⚠️异步执行⚠️
##### commitPassiveEffect()
- [[API/useEffect]] cleanup
- [useEffect](API/useEffect.md)
- 触发 `setTimeout`、事件监听等


## fiberNode的生成
1. 浅拷贝currentFiberNode
	- 只需要进行DOM属性的更新或移动
2. 新建fiberNode(createFiberNodeFromXXX)
3. 完全复用currentFiberNode
	1. 组件的render没有执行，没有返回新的reactElement，则就直接复用
	2. 关联的API:`shouldComponentUpdate`,`React.memo()`

