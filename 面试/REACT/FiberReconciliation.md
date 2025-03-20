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
- 将fiber链接到[effectList](effectList.md)
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
#### commitLayoutEffect()
- 更新ref
- 同步执行useLayoutEffect

### 清理（Cleanup）阶段
- 清空effectList


## fiberNode的生成
1. 浅拷贝currentFiberNode
	- 只需要进行DOM属性的更新或移动
2. 新建fiberNode(createFiberNodeFromXXX)
3. 完全复用currentFiberNode
	1. 组件的render没有执行，没有返回新的reactElement，则就直接复用
	2. 关联的API:`shouldComponentUpdate`,`React.memo()`

## Flags
React 使用 Flags（之前叫 EffectTag）来标记 Fiber 节点需要执行的操作。Flags 是一个 **位掩码（bitmask）**，可以使用 **按位或（|）** 合并多个操作。

协调结束过程中，会收集更新时产生的所有副作用(`effect`)，进入提交阶段会处理这些effect。

```tsx
export const NoFlags = /*                      */ 0b00000000000000000000000000; // 无操作
export const Placement = /*                    */ 0b00000000000000000000000010; // 新增（插入）
export const Update = /*                       */ 0b00000000000000000000000100; // 更新
export const Deletion = /*                     */ 0b00000000000000000000001000; // 删除
export const PlacementAndUpdate = /*           */ Placement | Update;           // 既是新增又是更新
export const ChildDeletion = /*                */ 0b00000000000000000000010000; // 子元素删除
export const ContentReset = /*                 */ 0b00000000000000000000100000; // 内容重置
export const Ref = /*                          */ 0b00000000000000000001000000; // ref 变更
export const Hydrating = /*                    */ 0b00000000000000000010000000; // 服务端渲染 hydration
export const Visibility = /*                   */ 0b00000000000000000100000000; // 组件可见性
```


### completeWork() 
向上冒泡组件的副作用，并在更新过程中使用位运算符存储，合并，删除每个节点的副作用记录。

### commitWork()
```tsx
function commitWork(fiber) {
  const flags = fiber.flags;

  if (flags & Placement) {
    // 处理插入操作
    commitPlacement(fiber);
  }
  
  if (flags & Update) {
    // 处理更新操作
    commitUpdate(fiber);
  }
  
  if (flags & Deletion) {
    // 处理删除操作
    commitDeletion(fiber);
  }
}
```
