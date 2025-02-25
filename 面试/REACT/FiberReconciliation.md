# Fiber的协调
## 双缓存
内存中同时会有两棵Fiber树，一个为对应此时真实DOM的currentFiberTree，一个是协调过程中操作的workInProgressFiberTree。
### 协调的执行
fiber树中执行协调的顺序类似中序遍历，先walk child，然后walk sibling，再返回上层（return）。

## workInProgressFiberTree的构建
### 生成fiberNode的方式
1. 浅拷贝currentFiberNode
	- 只需要进行DOM属性的更新或移动
2. 新建fiberNode(createFiberNodeFromXXX)
3. 完全复用currentFiberNode
	1. 组件的render没有执行，没有返回新的reactElement，则就直接复用
	2. 关联的API:`shouldComponentUpdate`,`React.memo()`

## ⭐️diff计算
已匹配的直接子节点和currentFiberNode的直接子节点进行比较。不进行跨父节点比较。

除了组件的渲染压根没有执行，其余情况都需要根据新建节点，区别是新建的节点是否由currentFiberTree中克隆而来。

### 判断逻辑
> key：元素上的key = reactElement的key = fiber节点上的key
> type：div,input,component,fragemt等

- **reactElement.key === currentFiberNode.key && reactElement.type === currentFiberNode.type**, currentFiberNode 可以克隆；
    
- **reactElement.key !== currentFiberNode.key**, currentFiberNode 不可克隆；
    
- **reactElement.key === currentFiberNode.key && reactElement.type !== currentFiberNode.type**, currentFiberNode 不可克隆；

新旧子节点的比较会出现两种情况：
1. 单个新子节点 vs 旧子节点list
2. 新子节点list vs 旧子节点list
复杂的部分发生在2。

### 最长递增索引算法
lastPlacedIndex，定位未发生移动的currentFiberNode
```javascript
// currentFiberTree
A->B->C->D

// workingInProgressTree
C->B->A->D
```
1. 从直接子节点（DOM结构中的首个子节点）开始
2. C的newIndex为0,oldIndex为2,lastPlacedIndex初始化=2
3. B的newIndex为1，oldIndex为1，oldIndex<lastPlacedIndex,B发生了移动
4. A的newIndex为2，oldIndex为0，oldIndex<lastPlacedIndex,A发生了移动
5. D的newIndex为3，oldIndex为3，oldIndex>lastPlacedIndex,D没有移动，并且重新赋值lastPlacedIndex = 3

在fiber tree中的运用
![[Pasted image 20240712170330.png]][掘金/百应技术团队博客](https://juejin.cn/post/7012961682938920967#heading-9)


## 更新潜拷贝节点
diff结束后新建的节点存在完全新建和潜拷贝两种情况，潜拷贝的节点需要更进一步的更新逻辑去更新他的奇遇属性。
### props
通过对比**pendingProps** 和 memoizedProps就知道属性的改变；
- `pengindProps`
	jsx上的所有参数除了key都会直接作为react element的props，react element执行渲染函数后这些props将作为workingProgress fiber node 下`pendingProps`的值。
- `memoizedProps`
	current fiber node的`memorizedProps`也就是上一次更新时workingProgress fiber node的`pengingProps`。
### tag
- **ClassComponent**
- **FunctionComponent**
- **HostComponent**
- **Fragment**
- **SuspenseComponent**

## 副作用
协调结束过程中，会收集更新时产生的所有副作用(`effect`)，协调结束进入提交阶段会处理这些effect。

整颗 **fiber tree** 完成**协调**以后，所有被标记 **effect** 的 **fiber node** 都被收集到一起使用**单链表**结构存储，**firstEffect** 指向第一个标记 **effect** 的 **fiber node**，**lastEffect** 标记最后一个 **fiber node**，节点之间通过 **nextEffect** 指针连接。

由于协调的顺序为子->兄->父，副作用执行的顺序也为子->兄->父。

