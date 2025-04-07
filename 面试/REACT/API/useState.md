## 核心概念
useState 是 React 提供的 **状态管理 Hook**，用于在函数组件中存储和更新状态。它的基本使用方式如下：
```js
const [count, setCount] = useState(0);
```
- count 是当前状态值
- setCount 是用于更新状态的函数
- useState(0) 传入 0 作为初始状态

## 内部存储
在 React 的 fiber 架构中，每个 hook 存储在 fiber.memoizedState [[链表]]中：
```js
// memorizedState :
{
  memoizedState: state, // 存储当前状态值
  queue: { // 存储steState产生的更新队列
  // ⭐️环状链表，首尾相连，pending指向当前更新
    pending: null
  },
  next: null // 指向下一个 hook
};
```

## 执行流程
```js
function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```
### 初始渲染
- useState(0) 生成 hook = { memoizedState: 0 }
- queue.pending = null
- **按钮显示 0**

### 点击按钮
- setCount(1)
- 生成 update = { action: `()=>1` ,next:null }
- update 加入 queue.pending
- 触发 scheduleRender()

### 更新渲染
- processQueue() 处理 queue.pending
- memoizedState = 1
- **按钮显示 1**


## 生命周期
|**生命周期**|useState **/** useReducer|
|---|---|
|**constructor**|初始化状态|
|**render**|读取状态并渲染|
|**shouldComponentUpdate**|触发组件更新|
|**componentDidMount**|组件首次渲染后，状态更新可能触发重渲染|
|**componentDidUpdate**|状态更新后组件重新渲染|
|**componentWillUnmount**|组件卸载时不影响状态（但状态变更不会影响已卸载组件）|