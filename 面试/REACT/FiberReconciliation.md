## Fiber的协调
## 双缓存机制
内存中同时会有两棵Fiber树，一个为对应此时真实DOM的currentFiberTree，一个是协调过程中操作的workInProgressFiberTree。
### 协调的执行
fiber树中执行协调的顺序类似中序遍历，先walk child，然后walk sibling，再返回上层（return）。


## fiberNode的生成
1. 浅拷贝currentFiberNode
	- 只需要进行DOM属性的更新或移动
2. 新建fiberNode(createFiberNodeFromXXX)
3. 完全复用currentFiberNode
	1. 组件的render没有执行，没有返回新的reactElement，则就直接复用
	2. 关联的API:`shouldComponentUpdate`,`React.memo()`

## ⭐️diff计算
### 同级比较
workinProgressFiberNode的直接子节点和currentFiberNode的直接子节点进行比较。不进行跨父节点比较。

### 节点复用
- ⭐️key：`元素上的key = reactElement的key = fiber节点上的key
- type：div,input,component,fragemt等
 
 克隆 currentFiberNode的条件：
`reactElement.key === currentFiberNode.key && reactElement.type === currentFiberNode.type`

### 最长递增索引K
`lastPlacedIndex`为新fiber节点在旧fiber节点中能匹配上的最大索引值，定位未发生移动的currentFiberNode。
```javascript
// currentFiberTree
A->B->C->D

// workingInProgressTree
C->B->A->D
// newChildren [C,B,A,D]
// K [2,1,0,3]
```
1. 从直接子节点（DOM结构中的首个子节点）开始
2. C的newIndex为0, oldIndex为2, lastPlacedIndex初始化=2
3. B的newIndex为1，oldIndex为1，oldIndex<lastPlacedIndex,B发生了移动
4. A的newIndex为2，oldIndex为0，oldIndex<lastPlacedIndex,A发生了移动
5. D的newIndex为3，oldIndex为3，oldIndex>lastPlacedIndex,D没有移动，并且重新赋值lastPlacedIndex = 3

![[Pasted image 20240712170330.png]]

[掘金/百应技术团队博客](https://juejin.cn/post/7012961682938920967#heading-9)
## 更新节点
- 新建的节点 -> 执行mount
- 克隆的节点-> 比较props -> 执行update
- 克隆的组件节点 -> 比较props -> 比较state -> 执行update
### props
通过对比**pendingProps** 和 memoizedProps就知道属性的改变；
#### `pengindProps`
jsx上的所有参数除了key都会直接作为react element的props，react element执行渲染函数后这些props将作为workingProgress fiber node 下`pendingProps`的值。
#### `memoizedProps`
节点更新完之后的props。
current fiber node的`memorizedProps`也就是上一次更新时workingProgress fiber node的`pengingProps`。

### Fiber.tag
标记不同的fiber节点类型
- **ClassComponent**类组件
- **FunctionComponent**函数组件
- **HostComponent**DOM元素
- **Fragment**
- **SuspenseComponent**
不同节点类型执行更新的操作步骤也不同。

### 组件状态
组件类型的tag相同时，还需要比较组件内部状态的不同。

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