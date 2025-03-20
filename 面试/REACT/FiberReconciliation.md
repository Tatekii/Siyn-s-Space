# Fiber的协调

## 双缓存机制
内存中同时会有两棵Fiber树，一个为对应此时真实DOM的currentFiberTree，一个是协调过程中操作的workInProgressFiberTree。
### 协调的执行
fiber树中执行协调的顺序类似中序遍历（DFS），先walk child，然后walk sibling，再返回上层（return）。

## 协调过程分为两个阶段
### **渲染（Render）阶段**
>可中断
#### beginWork()
- 深度优先遍历fiber链表/[生成新的fiberNode](#fiberNode的生成)
- 执行渲染函数
- hooks执行
	- 生成副作用链表
- 使用bitmask(位掩码)标记更新`Fiber.flag`
#### completeWork()
- 根据flag输出新的虚拟DOM`stateNode`
- 回溯过程中冒泡更新标记flags

### **提交（Commit）阶段**
>不可中断
- 执行`effectList`(副作用链表)
- DOM更新到实际页面上
#### beforeMutation()
- getSnapshotBeforeMutation
#### mutation()
- DOM 操作，如 appendChild、removeChild
#### layout()
- componentDidMount
- componentDidUpdate
- [useEffect](API/useEffect.md)

## 副作用
协调结束过程中，会收集更新时产生的所有副作用(`effect`)，协调结束进入提交阶段会处理这些effect。

每个fiber节点内使用使用`Fiber.flags`来存储副作用
```
export const NoFlags = 0b0000000;
export const PerformedWork = 0b0000001;
export const Placement = 0b0000010;
export const Update = 0b0000100;
export const ChildDeletion = 0b0001000;
```

`completeWork` 阶段会向上冒泡组件的副作用，并在更新过程中使用位运算符存储，合并，删除每个节点的副作用记录。


整颗 **fiber tree** 完成**协调**以后，所有被标记 **effect** 的 **fiber node** 都被收集到一起使用**单链表**结构存储，**firstEffect** 指向第一个标记 **effect** 的 **fiber node**，**lastEffect** 标记最后一个 **fiber node**，节点之间通过 **nextEffect** 指针连接。

由于协调的顺序为子->兄->父，副作用执行的顺序也为子->兄->父。