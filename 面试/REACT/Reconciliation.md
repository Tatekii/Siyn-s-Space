# Fiber的协调

## 双缓存机制
内存中同时会有两棵Fiber树，一个为对应此时真实DOM的currentFiberTree，一个是协调过程中操作的workInProgressFiberTree。
### 协调的执行
fiber树中执行协调的顺序类似中序遍历/深度优先搜索（DFS），先walk child，然后walk sibling，再返回上层（return）。

## 协调过程
### **渲染（Render）阶段**
>可中断
#### beginWork()
- 深度优先遍历fiber链表，[生成新的fiberNode](#fiberNode的生成)
- 执行渲染函数
- hooks执行
- 将fiber链接到[副作用](副作用.md)
- 记录副作用类型使用bitmask(位掩码)标记`Fiber.flag`
#### completeWork()
- 从最深节点向上回溯，收集所有带有effectTag的fiberNode到effectList
	- 子节点将自己的副作用冒泡给父节点
- 根据flag构建新的Virtual DOM结构存储到`stateNode`

### **提交（Commit）阶段**
>不可中断
#### commitBeforeMutationEffects
- getSnapshotBeforeMutation (类组件)
#### commitMutationEffects
- **解綁/清理 `ref`**：處理舊的 `ref`（如果需要）。
- **根據不同的 `flags` 執行 DOM 操作**：插入、更新屬性、刪除節點等。
- **執行 `componentWillUnmount`** (如果組件被卸載)。
- ⚠️ **同步執行 `useLayoutEffect.cleanup`**
#### commitLayoutEffects
- **賦值新的 `ref`**。
- **類組件的 `componentDidMount`** (對於新挂載的組件)。
- **類組件的 `componentDidUpdate`**。
- ⚠️ **同步執行 `useLayoutEffect`**。
##### flushPassiveEffects
> 异步执行
- [[useEffect]] cleanup
- [useEffect](API/useEffect.md)


## fiberNode的生成
1. 浅拷贝currentFiberNode
	- 只需要进行DOM属性的更新或移动
2. 新建fiberNode(createFiberNodeFromXXX)
3. 完全复用currentFiberNode
	1. 组件的render没有执行，没有返回新的reactElement，则就直接复用
	2. 关联的API:`shouldComponentUpdate`,`React.memo()`

