在 React Fiber 架构中，副作用链表（effectList）的存储位置如下：

1. **每个 Fiber 节点** 都可能有副作用，副作用信息存储在 FiberNode 结构中：
	- fiber.flags：标记当前 Fiber 需要执行的副作用（如 Update、Placement、Deletion）
	- fiber.nextEffect：指向下一个需要执行副作用的 Fiber，形成 **单向链表**

2. **Root Fiber (root.current) 维护整个 effectList 链表**
	- root.finishedWork：指向已经完成的 Fiber 树
	- root.firstEffect：指向副作用链表的头部
	- root.lastEffect：指向副作用链表的尾部

其结构为：
```tsx
Root
 ├── firstEffect → Fiber(A) → Fiber(B) → Fiber(C) → null
 └── lastEffect  → Fiber(C)  (指向链表最后一个副作用)
```